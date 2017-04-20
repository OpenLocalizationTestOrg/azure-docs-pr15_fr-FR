<properties
   pageTitle="Service Proxy inverse TISSU | Microsoft Azure"
   description="Utiliser proxy inverse du Service tissu pour la communication à microservices à partir de l’intérieur et l’extérieur du cluster"
   services="service-fabric"
   documentationCenter=".net"
   authors="BharatNarasimman"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="10/04/2016"
   ms.author="vturecek"/>

# <a name="service-fabric-reverse-proxy"></a>Service TISSU Proxy inverse

Le serveur proxy inverse Service tissu est un tissu d’intégrés à service proxy inverse qui permet de définir les destinataires de microservices dans le cluster TISSU service qui exposent des points de terminaison HTTP.

## <a name="microservices-communication-model"></a>Modèle de communication Microservices

En règle générale, Microservices dans tissu de service s’exécutent sur un sous-ensemble des machines virtuelles du cluster et peut déplacer d’un ordinateur virtuel vers un autre pour diverses raisons. Les points de terminaison pour microservices peuvent de modifier dynamiquement. Le modèle par défaut pour communiquer avec le microservice est la boucle résoudre ci-dessous,

1. Résoudre l’emplacement du service initiale via le Service de dénomination.
2. Se connecter au service.
3. Déterminer l’origine d’échecs de connexion et de résoudre l’emplacement du service lorsque cela est nécessaire.

Ce processus implique généralement des bibliothèques de communication côté client dans une boucle de nouvelle tentative qui mettent en œuvre, les stratégies de résolution et réessayer de service d’habillage.
Pour plus d’informations sur cette rubrique, voir [communiquer avec les services](service-fabric-connect-and-communicate-with-services.md).

### <a name="communicating-via-sf-reverse-proxy"></a>Communication via un proxy inverse DF
TISSU de service proxy inverse s’exécute sur tous les nœuds du cluster. Elle effectue le processus de résolution d’ensemble du service pour un client et transfère ensuite la requête du client. Donc clients s’exécutant sur le cluster peuvent utiliser uniquement les bibliothèques de communication HTTP côté client pour communiquer avec le service cible via le serveur proxy inverse DF s’exécute sur le même nœud.

![Communications internes][1]

## <a name="reaching-microservices-from-outside-the-cluster"></a>Atteindre Microservices à partir de l’extérieur du cluster
Le modèle de communication externe par défaut pour microservices est **désactiver cette connexion** où chaque service par défaut ne sont pas accessibles directement à partir des clients externes. L' [Équilibrage de charge Azure](../load-balancer/load-balancer-overview.md) est une limite de réseau entre microservices et des clients externes, qui effectue la traduction d’adresses réseau et la transfère les requêtes externes à interne **IP : port** points de terminaison. Afin de pouvoir modifier le point de terminaison d’un microservice directement accessible aux clients externes, l’équilibrage de charge Azure doit d’abord être configuré pour acheminer le trafic vers chaque port utilisé par le service dans le cluster. En outre, la plupart des microservices (esp. dynamique microservices) ne live sur tous les nœuds du cluster et ils peuvent se déplacer entre les nœuds de basculement, afin que dans ce cas, l’équilibrage de charge Azure ne peut pas déterminer efficacement le nœud cible réplica se trouvent pour transférer le trafic vers.

### <a name="reaching-microservices-via-the-sf-reverse-proxy-from-outside-the-cluster"></a>Atteindre Microservices via le proxy inverse DF en dehors du cluster

Au lieu de la configuration des ports de service individuelles dans l’équilibrage de charge azure, simplement le port du proxy inverse DF peut être configuré dans l’équilibrage de charge Azure. Cela permet aux clients en dehors du cluster joindre les services à l’intérieur du cluster via le proxy inverse sans un configurations supplémentaires.

![Communications externes][0]

>[AZURE.WARNING] Configuration de port du serveur proxy inverse sur l’équilibrage de charge, rend tous les services micro dans le cluster qui exposent un point de terminaison http, soit addressible à partir de l’extérieur du cluster.


## <a name="uri-format-for-addressing-services-via-the-reverse-proxy"></a>Format d’URI pour l’adressage des services via le serveur proxy inverse

Le serveur proxy inverse utilise un format URI spécifique pour identifier la demande entrante doit être transférée vers la partition dans laquelle service :

```
http(s)://<Cluster FQDN | internal IP>:Port/<ServiceInstanceName>/<Suffix path>?PartitionKey=<key>&PartitionKind=<partitionkind>&Timeout=<timeout_in_seconds>
```

 - **http :** Le serveur proxy inverse peut être configuré pour accepter le trafic HTTP ou HTTPS. En cas de trafic HTTPS, frais de résiliation SSL se produit au proxy inverse. Les requêtes qui sont transférés par le serveur proxy inverse services dans le cluster sont sur http.
 - **Nom de domaine complet cluster | adresse IP interne :** Pour les clients externes, le proxy inverse peut être configuré pour qu’il soit accessible par le biais du domaine cluster (par exemple, mycluster.eastus.cloudapp.azure.com). Par défaut que le proxy inverse s’exécute sur chaque nœud, donc pour le trafic interne il peut être contacté sur hôte local ou sur n’importe quel nœud interne IP (par exemple, 10.0.0.1).
 - **Port :** Le port qui a été spécifié pour le serveur proxy inverse. Par exemple : 19008.
 - **ServiceInstanceName :** Il s’agit du nom de l’instance service déployé complet du service que vous essayez d’atteindre la « TISSU : / « jeu. Par exemple, pour atteindre service *TISSU : / myapp/myservice/*, vous utiliseriez *myapp/myservice*.
 - **Chemin d’accès suffixe :** Il s’agit du chemin d’URL réel pour le service que vous voulez vous connecter. Par exemple, *myapi/valeurs/ajouter/3*
 - **PartitionKey :** Pour un service partitionné, il s’agit de la clé de partition calculées de la partition que vous souhaitez joindre. Notez qu’il s’agit *pas* la partition ID GUID. Ce paramètre n’est pas obligatoire pour les services en utilisant le schéma de partition singleton.
 - **PartitionKind :** Le schéma de partition de service. Cela peut être « Int64Range » ou « Nommé ». Ce paramètre n’est pas obligatoire pour les services en utilisant le schéma de partition singleton.
 - **Délai d’expiration :**  Ce paramètre spécifie le délai d’expiration de la requête http créée par le serveur proxy inverse au service au nom de la requête du client. La valeur par défaut est 60 secondes. Il s’agit d’un paramètre facultatif.

### <a name="example-usage"></a>Exemple d’utilisation

Par exemple, prenons le service **TISSU : / MyApp/MyService** qui ouvre un récepteur HTTP sur l’URL suivante :

```
http://10.0.05:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/
```

Avec les ressources suivantes :

 - `/index.html`
 - `/api/users/<userId>`

Si le service utilise le schéma de partition singleton, les paramètres de chaîne de requête *PartitionKey* et *PartitionKind* ne sont pas obligatoires, et le service est accessible via la passerelle en tant que :

 - En externe :`http://mycluster.eastus.cloudapp.azure.com:19008/MyApp/MyService`
 - En interne :`http://localhost:19008/MyApp/MyService`

Si le service utilise le schéma de partition Int64 uniforme, les paramètres de chaîne de requête *PartitionKey* et *PartitionKind* doivent servir à atteindre une partition du service :

 - En externe :`http://mycluster.eastus.cloudapp.azure.com:19008/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`
 - En interne :`http://localhost:19008/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`

Pour atteindre les ressources exposées par le service, placez simplement le chemin d’accès de la ressource après le nom du service dans l’URL :

 - En externe :`http://mycluster.eastus.cloudapp.azure.com:19008/MyApp/MyService/index.html?PartitionKey=3&PartitionKind=Int64Range`
 - En interne :`http://localhost:19008/MyApp/MyService/api/users/6?PartitionKey=3&PartitionKind=Int64Range`

La passerelle transférera puis ces requêtes à l’URL du service :

 - `http://10.0.05:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/index.html`
 - `http://10.0.05:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/api/users/6`

## <a name="special-handling-for-port-sharing-services"></a>Un traitement spécial pour le partage de port services

La passerelle d’Application tente de résoudre une adresse de service et réessayez la demande quand un service ne peuvent pas être atteintes. C’est un des principaux avantages de la passerelle, car le code client n’a pas besoin d’implémenter son propre résolution de service et de résoudre boucle.

Lorsqu’un service ne peuvent pas être atteintes il signifie généralement que l’instance de service ou réplica a déplacée vers un autre nœud dans le cadre de son cycle de vie normal. Dans ce cas, la passerelle peut recevoir une erreur de connexion réseau indiquant qu'un point de terminaison n’est plus ouvert sur l’adresse résolue à l’origine.

Toutefois, réplicas ou instances de service peuvent partager un processus hôte et peut également partager un port lorsqu’il est hébergé par un serveur web basées sur http.sys, y compris :

 - [System.Net.HttpListener](https://msdn.microsoft.com/library/system.net.httplistener%28v=vs.110%29.aspx)
 - [WebListener Core ASP.NET](https://docs.asp.net/latest/fundamentals/servers.html#weblistener)
 - [Interconnexions](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.OwinSelfHost/)

Dans ce cas, il est probable que le serveur web est disponible dans le processus hôte et répondre aux demandes mais l’instance de service résolu ou réplica n’est plus disponible sur l’hôte. Dans ce cas, la passerelle recevront une réponse HTTP 404 à partir du serveur web. Par conséquent, un HTTP 404 contient deux sens :

 1. L’adresse du service est correcte, mais la ressource demandée par l’utilisateur n’existe pas.
 2. L’adresse du service est incorrecte, et la ressource demandée par l’utilisateur ne peut exister réellement sur un nœud différent.

Dans le premier cas, il s’agit d’une erreur 404 HTTP normal, est considérée comme une erreur de l’utilisateur. Toutefois, dans le deuxième cas, l’utilisateur a demandé une ressource qui existe, mais la passerelle n’a pas pu localiser, car le service a été déplacé, auquel cas la passerelle doit résoudre l’adresse, puis réessayez.

La passerelle doit donc un moyen de faire la distinction entre les deux cas. Afin de pouvoir modifier cette distinction, un indicateur à partir du serveur est requis.

 - Par défaut, la passerelle d’Application suppose que cas #2 et tente de résoudre et ré-émettre la demande.
 - Pour indiquer la casse #1 à la passerelle d’Application, le service doit renvoyer l’en-tête de réponse HTTP suivant :

`X-ServiceFabric : ResourceNotFound`

Cet en-tête de réponse HTTP indique une situation HTTP 404 normale dans laquelle la ressource demandée n’existe pas, et la passerelle ne tente de résoudre l’adresse du service.

## <a name="setup-and-configuration"></a>Installation et configuration
Le proxy inverse TISSU du service peut être activé pour le cluster via le [Gestionnaire de ressources Azure modèle](./service-fabric-cluster-creation-via-arm.md).

Une fois que le modèle pour le cluster que vous voulez déployer (dans les exemples de modèles ou en créant un modèle de gestionnaire de ressource personnalisés) le proxy inverse peut être activée dans le modèle par les étapes suivantes.

1. Définir un port pour le serveur proxy inverse dans la [section Paramètres](../resource-group-authoring-templates.md) du modèle.

    ```json
    "SFReverseProxyPort": {
        "type": "int",
        "defaultValue": 19008,
        "metadata": {
            "description": "Endpoint for Service Fabric Reverse proxy"
        }
    },
    ```
2. Spécifiez le port pour chacun des objets nodetype dans la [section type de ressource](../resource-group-authoring-templates.md) de **Cluster**

    Pour apiVersion avant ' 2016-09-01' le port est identifié par le paramètre nom ***httpApplicationGatewayEndpointPort***

    ```json
    {
        "apiVersion": "2016-03-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
       "nodeTypes": [
          {
           ...
           "httpApplicationGatewayEndpointPort": "[parameters('SFReverseProxyPort')]",
           ...
          },
        ...
        ],
        ...
    }
    ```

    Pour apiVersion sur ou après ' 2016-09-01' le port est identifié par le paramètre nom ***reverseProxyEndpointPort***

    ```json
    {
        "apiVersion": "2016-09-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
       "nodeTypes": [
          {
           ...
           "reverseProxyEndpointPort": "[parameters('SFReverseProxyPort')]",
           ...
          },
        ...
        ],
        ...
    }
    ```

3. Pour résoudre le proxy inverse à partir de l’extérieur du cluster azure, configurer les **règles d’équilibrage de charge azure** pour le port spécifié à l’étape 1.

    ```json
    {
        "apiVersion": "[variables('lbApiVersion')]",
        "type": "Microsoft.Network/loadBalancers",
        ...
        ...
        "loadBalancingRules": [
            ...
            {
                "name": "LBSFReverseProxyRule",
                "properties": {
                    "backendAddressPool": {
                        "id": "[variables('lbPoolID0')]"
                    },
                    "backendPort": "[parameters('SFReverseProxyPort')]",
                    "enableFloatingIP": "false",
                    "frontendIPConfiguration": {
                        "id": "[variables('lbIPConfig0')]"
                    },
                    "frontendPort": "[parameters('SFReverseProxyPort')]",
                    "idleTimeoutInMinutes": "5",
                    "probe": {
                        "id": "[concat(variables('lbID0'),'/probes/SFReverseProxyProbe')]"
                    },
                    "protocol": "tcp"
                }
            }
        ],
        "probes": [
            ...
            {
                "name": "SFReverseProxyProbe",
                "properties": {
                    "intervalInSeconds": 5,
                    "numberOfProbes": 2,
                    "port":     "[parameters('SFReverseProxyPort')]",
                    "protocol": "tcp"
                }
            }  
        ]
    }
    ```
4. Pour configurer des certificats SSL sur le port pour le serveur proxy inverse, ajoutez le certificat à la propriété httpApplicationGatewayCertificate dans la [section type de ressource](../resource-group-authoring-templates.md) de **Cluster**

    Pour apiVersion avant ' 2016-09-01' le certificat est identifié par le paramètre nom ***httpApplicationGatewayCertificate***

    ```json
    {
        "apiVersion": "2016-03-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            "[concat('Microsoft.Storage/storageAccounts/', parameters('supportLogStorageAccountName'))]"
        ],
        "properties": {
            ...
            "httpApplicationGatewayCertificate": {
                "thumbprint": "[parameters('sfReverseProxyCertificateThumbprint')]",
                "x509StoreName": "[parameters('sfReverseProxyCertificateStoreName')]"
            },
            ...
            "clusterState": "Default",
        }
    }
    ```
    Pour apiVersion sur ou après ' 2016-09-01' le certificat est identifié par le paramètre nom ***reverseProxyCertificate***
    
    ```json
    {
        "apiVersion": "2016-09-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            "[concat('Microsoft.Storage/storageAccounts/', parameters('supportLogStorageAccountName'))]"
        ],
        "properties": {
            ...
            "reverseProxyCertificate": {
                "thumbprint": "[parameters('sfReverseProxyCertificateThumbprint')]",
                "x509StoreName": "[parameters('sfReverseProxyCertificateStoreName')]"
            },
            ...
            "clusterState": "Default",
        }
    }
    ```

## <a name="next-steps"></a>Étapes suivantes
 - Consultez un exemple de communication HTTP entre les services dans un [exemple de projet sur GitHUb](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/master/Services/WordCount).

 - [Appels de procédure distante avec l’accès distant des Services fiables](service-fabric-reliable-services-communication-remoting.md)

 - [API qui utilise OWIN dans fiable Services Web](service-fabric-reliable-services-communication-webapi.md)

 - [Communication WCF à l’aide des Services fiables](service-fabric-reliable-services-communication-wcf.md)


[0]: ./media/service-fabric-reverseproxy/external-communication.png
[1]: ./media/service-fabric-reverseproxy/internal-communication.png
