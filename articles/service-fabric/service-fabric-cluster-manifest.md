<properties
   pageTitle="Configurer votre cluster autonome | Microsoft Azure"
   description="Cet article décrit comment configurer votre autonome ou un cluster de Service TISSU privé."
   services="service-fabric"
   documentationCenter=".net"
   authors="dsk-2015"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/07/2016"
   ms.author="dkshir"/>


# <a name="configuration-settings-for-standalone-windows-cluster"></a>Paramètres de configuration pour le cluster Windows autonome

Cet article décrit comment configurer un cluster de Service TISSU autonome à l’aide du fichier _**ClusterConfig.JSON**_ . Vous pouvez utiliser ce fichier pour spécifier des informations telles que les nœuds tissu de Service et leurs adresses IP et les différents types de nœuds sur le cluster, les configurations de sécurité, ainsi que la topologie de réseau en termes de panne/mise à niveau les domaines pour votre cluster autonome.

Lorsque vous [Téléchargez le package de Service TISSU autonome](service-fabric-cluster-creation-for-windows-server.md#downloadpackage), quelques exemples du fichier ClusterConfig.JSON sont téléchargés vers votre poste de travail. Les exemples ayant *DevCluster* dans leurs noms vous permettra de créer un cluster avec tous les trois nœuds sur le même ordinateur, comme les nœuds logiques. Se déconnecter de ceux-ci, au moins un nœud doit être marqué comme un nœud principal. Ce cluster est utile pour un environnement de développement ou de test et n’est pas pris en charge comme un cluster de production. Les exemples ayant *MultiMachine* dans leurs noms, vous permet de créer un cluster de qualité production, avec chaque nœud sur un ordinateur distinct. Le nombre de nœuds principaux pour ces cluster dépendra le [niveau de fiabilité](#reliability).

1. *ClusterConfig.Unsecure.DevCluster.JSON* et *ClusterConfig.Unsecure.MultiMachine.JSON* montrent comment créer un cluster de test ou de production non sécurisé respectivement. 
    
2. *ClusterConfig.Windows.DevCluster.JSON* et *ClusterConfig.Windows.MultiMachine.JSON* montrent comment créer cluster test ou de production, sécurisée à l’aide de [la sécurité de Windows](service-fabric-windows-cluster-windows-security.md).

3. *ClusterConfig.X509.DevCluster.JSON* et *ClusterConfig.X509.MultiMachine.JSON* expliquent comment créer cluster test ou de production, sécurisée à l’aide [X509 sécurité basée sur le certificat](service-fabric-windows-cluster-x509-security.md). 


Maintenant, nous allons examiner les différentes sections d’un fichier _**ClusterConfig.JSON**_ comme ci-dessous.

## <a name="general-cluster-configurations"></a>Configurations de cluster général
Ceci couvre les configurations spécifiques cluster large, comme le montre l’extrait de JSON ci-dessous.

    "name": "SampleCluster",
    "clusterConfigurationVersion": "1.0.0",
    "apiVersion": "2015-01-01-alpha",

Vous pouvez donner un nom convivial à votre cluster Service tissu en affectant à la variable de **nom** . La **clusterConfigurationVersion** est le numéro de version de votre cluster ; Vous devez l’augmenter chaque fois que vous mettez à niveau votre cluster tissu de Service. Vous devez toutefois laisser **apiVersion** la valeur par défaut.


<a id="clusternodes"></a>
## <a name="nodes-on-the-cluster"></a>Nœuds sur le cluster
Vous pouvez configurer les nœuds dans votre cluster tissu de Service à l’aide de la section de **nœuds** , comme le montre l’extrait de code suivantes.

    "nodes": [{
        "nodeName": "vm0",
        "iPAddress": "localhost",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r0",
        "upgradeDomain": "UD0"
    }, {
        "nodeName": "vm1",
        "iPAddress": "localhost",
        "nodeTypeRef": "NodeType1",
        "faultDomain": "fd:/dc1/r1",
        "upgradeDomain": "UD1"
    }, {
        "nodeName": "vm2",
        "iPAddress": "localhost",
        "nodeTypeRef": "NodeType2",
        "faultDomain": "fd:/dc1/r2",
        "upgradeDomain": "UD2"
    }],

Un cluster de tissu de Service doit contenir au moins 3 nœuds. Vous pouvez ajouter plus de nœuds à cette section en fonction de votre configuration. Le tableau suivant répertorie les paramètres de configuration pour chaque nœud.

|**Configuration du nœud**|**Description**|
|-----------------------|--------------------------|
|Nom_nœud|Vous pouvez donner un nom convivial pour le nœud.|
|adresse IP|Obtenir l’adresse IP de votre nœud en ouvrant une fenêtre de commande et en tapant `ipconfig`. Notez l’adresse IPV4 et affectez-le à la variable **d’adresse IP** .|
|nodeTypeRef|Chaque nœud peut être affectée à un type de nœud différents. Les [types de nœuds](#nodetypes) sont définis dans la section ci-dessous.|
|faultDomain|Domaines d’erreur permettent aux administrateurs de cluster définir les nœuds physiques peuvent échouer en même temps en raison des dépendances physiques partagés.|
|upgradeDomain|Mise à niveau domaines décrivent des ensembles de nœuds qui ne sont pas arrêtés mises à jour de tissu de Service en même temps. Vous pouvez choisir les nœuds à affecter à quels domaines mise à niveau, car ils ne sont pas limités par les exigences physiques.| 


## <a name="cluster-properties"></a>Propriétés du cluster

La section **Propriétés** de la ClusterConfig.JSON est utilisée pour configurer le cluster comme suit.

<a id="reliability"></a>
### <a name="reliability"></a>Fiabilité 
La section **reliabilityLevel** définit le nombre de copies des services système qui peuvent s’exécuter sur les nœuds du cluster principales. Cela permet d’augmenter la fiabilité de ces services et donc le cluster. Vous pouvez définir cette variable *Bronze*, *Silver*, *Gold* ou *Platinum* pour 3, 5, 7 ou 9 copies de ces services respectivement. Consultez un exemple ci-dessous.

    "reliabilityLevel": "Bronze",
    
Notez que dans la mesure où un nœud principal exécute une seule copie des services système, vous devez au moins 3 nœuds principales pour *Bronze*, 5 *Silver*, 7 pour *or* et 9 pour les niveaux de fiabilité *Platinum* .


### <a name="diagnostics"></a>Diagnostics
La section **diagnosticsStore** vous permet de configurer des paramètres pour permettre de diagnostics et de dépannage des problèmes de nœud ou cluster, comme le montre l’extrait de code suivante. 

    "diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "FileShare",
        "IsEncrypted": "false",
        "connectionstring": "c:\\ProgramData\\SF\\DiagnosticsStore"
    }

Les **métadonnées** sont une description de votre diagnostics cluster et peuvent être définie en fonction de votre configuration. Ces variables vous aider lors de la collecte ETW les journaux de suivi, vidage du blocage ainsi que des compteurs de performance. Lisez [Tracelog](https://msdn.microsoft.com/library/windows/hardware/ff552994.aspx) et [Suivi ETW](https://msdn.microsoft.com/library/ms751538.aspx) pour plus d’informations sur les journaux de suivi ETW. Tous les journaux, y compris des [compteurs de performance](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx) et de [vidage sur incident](https://blogs.technet.microsoft.com/askperf/2008/01/08/understanding-crash-dump-files/) peuvent être dirigés vers le dossier **connectionString** sur votre ordinateur. Vous pouvez également utiliser *AzureStorage* pour le stockage des diagnostics. Consultez un extrait de l’exemple ci-dessous.

    "diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "AzureStorage",
        "IsEncrypted": "false",
        "connectionstring": "xstore:DefaultEndpointsProtocol=https;AccountName=[AzureAccountName];AccountKey=[AzureAccountKey]"
    }

### <a name="security"></a>Sécurité 
La section **sécurité** est nécessaire pour un cluster de Service TISSU autonome sécurisé. L’extrait suivant montre une partie de cette section.

    "security": {
        "metadata": "This cluster is secured using X509 certificates.",
        "ClusterCredentialType": "X509",
        "ServerCredentialType": "X509",
        . . .
    }

Les **métadonnées** sont une description de votre cluster sécurisé et peuvent être définie en fonction de votre configuration. La **ClusterCredentialType** et **ServerCredentialType** déterminent le type de sécurité le cluster et les nœuds transpose. Ils peuvent être définies pour l' *X509* pour une sécurité basée sur le certificat, ou *Windows* pour une sécurité basée sur les Azure Active Directory. Le reste de la section **sécurité** dépendra du type de la sécurité. Pour plus d’informations sur la façon de remplir le reste de la section **sécurité** , lisez [la sécurité basée sur les certificats dans un cluster autonome](service-fabric-windows-cluster-x509-security.md) ou la [sécurité de Windows dans un cluster autonome](service-fabric-windows-cluster-windows-security.md) .


<a id="nodetypes"></a>
### <a name="node-types"></a>Types de nœuds
La section **nodeTypes** décrit le type des nœuds qui comporte votre cluster. Au moins un nœud type doit être spécifié pour un cluster, comme le montre l’extrait de code ci-dessous. 

    "nodeTypes": [{
        "name": "NodeType0",
        "clientConnectionEndpointPort": "19000",
        "clusterConnectionEndpointPort": "19001",
        "leaseDriverEndpointPort": "19002"
        "serviceConnectionEndpointPort": "19003",
        "httpGatewayEndpointPort": "19080",
        "applicationPorts": {
            "startPort": "20575",
            "endPort": "20605"
        },
        "ephemeralPorts": {
            "startPort": "20606",
            "endPort": "20861"
        },
        "isPrimary": true
    }]

Le **nom** est le nom convivial pour ce type de nœud particulier. Pour créer un nœud de ce type de nœud, attribuer son nom convivial dans la variable **nodeTypeRef** pour ce nœud, comme mentionné [ci-dessus](#clusternodes). Pour chaque type de nœud, définissez les points de terminaison de connexion qui seront utilisées. Vous pouvez choisir n’importe quel numéro de port pour ces points de terminaison de connexion, dans la mesure où ils ne sont pas en conflit avec les autres points de terminaison de ce groupe. Dans un cluster de nœuds multiples, il y a un ou plusieurs des nœuds principaux (c'est-à-dire **isPrimary** défini sur *true*), en fonction de la [**reliabilityLevel**](#reliability). Lisez [Considérations relatives au planning de Service TISSU cluster capacité](service-fabric-cluster-capacity.md) de d’informations sur les valeurs **nodeTypes** et **reliabilityLevel** et de savoir ce que sont principal et les types de nœuds secondaires. 

#### <a name="endpoints-used-to-configure-the-node-types"></a>Points de terminaison permettant de configurer les types de nœuds

- *clientConnectionEndpointPort* est le port utilisé par le client pour vous connecter au cluster, lors de l’utilisation de l’API clientes. 
- *clusterConnectionEndpointPort* est le port à partir duquel les nœuds de communiquent avec eux.
- *leaseDriverEndpointPort* est le port utilisé par le pilote de location cluster pour déterminer si les nœuds sont toujours actifs. 
- *serviceConnectionEndpointPort* est le port utilisé par les applications et services déployés sur un nœud, pour communiquer avec le client de Service tissu sur ce nœud particulier.
- *httpGatewayEndpointPort* est le port utilisé par l’Explorateur de tissu de Service pour vous connecter au cluster.
- *ephemeralPorts* sont les [ports dynamiques utilisés par le système d’exploitation](https://support.microsoft.com/kb/929851). Service TISSU utilisera une partie de ces ports d’application et le reste sera disponible pour le système d’exploitation. Il correspondra également cette plage de cellules est remplacé présents dans le système d’exploitation, afin d’utiliser les plages qui figurent dans les fichiers d’exemples JSON pour tous les rôles. Vous devez vous assurer que la différence entre le début et les ports de fin est au moins de 255. 
- *applicationPorts* sont les ports qui seront utilisés par les applications de Service tissu. Elles doivent être un sous-ensemble de *ephemeralPorts*, suffisant pour couvrir le besoin de point de terminaison de vos applications. Service TISSU utilisera ces chaque fois que les nouveaux ports requis, ainsient que de l’ouverture du pare-feu pour ces ports intégrées. 
- *reverseProxyEndpointPort* est un point de terminaison proxy inverse facultatif. Pour plus d’informations, voir [Service TISSU inverser Proxy](service-fabric-reverseproxy.md) . 


### <a name="other-settings"></a>Autres paramètres
La section **fabricSettings** vous permet de définir les répertoires racine pour les données de Service tissu et les journaux. Vous pouvez personnaliser ces uniquement pendant la création d’un cluster initial. Voir ci-dessous pour un extrait de l’exemple de cette section.

    "fabricSettings": [{
        "name": "Setup",
        "parameters": [{
            "name": "FabricDataRoot",
            "value": "C:\\ProgramData\\SF"
        }, {
            "name": "FabricLogRoot",
            "value": "C:\\ProgramData\\SF\\Log"
    }]

Il est recommandé à l’aide d’un lecteur du système d’exploitation non comme FabricDataRoot et FabricLogRoot car il offre que plus fiabilité en fonction du système d’exploitation se bloque. Notez que si vous personnalisez uniquement la racine de données, puis la racine de journal sera placée un niveau au-dessous de la racine de données.


## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez un fichier ClusterConfig.JSON complet configuré en fonction de votre installation sur un cluster autonome, vous pouvez déployer votre cluster en suivant l’article [créer un cluster Azure Service TISSU locale ou dans le cloud](service-fabric-cluster-creation-for-windows-server.md) , puis passez à [visualiser votre cluster avec l’Explorateur de tissu de Service](service-fabric-visualizing-your-cluster.md).


