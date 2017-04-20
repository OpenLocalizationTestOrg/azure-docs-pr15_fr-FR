<properties
   pageTitle="Banque d’informations sécurisé un cluster s’exécutant sur Windows à l’aide de la sécurité de Windows | Microsoft Azure"
   description="Découvrez comment configurer la sécurité nœud à nœud et client-à-nœud sur un cluster autonome fonctionnant sous Windows à l’aide de la sécurité de Windows."
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/25/2016"
   ms.author="ryanwi"/>


# <a name="secure-a-standalone-cluster-on-windows-using-windows-security"></a>Banque d’informations sécurisé un cluster autonome sur Windows à l’aide de la sécurité de Windows

Pour empêcher tout accès non autorisé à un cluster de Service tissu que vous devez sécuriser, en particulier lorsqu’il a charges de travail en cours d’exécution dessus. Cet article explique comment configurer la sécurité nœud à nœud et client-à-nœud lié à l’aide de la sécurité de Windows dans le fichier *ClusterConfig.JSON* et correspond à l’étape de sécurité configurer de [créer un cluster autonome fonctionnant sous Windows](service-fabric-cluster-creation-for-windows-server.md). Pour plus d’informations sur la façon tissu de Service utilise la sécurité de Windows, voir [scénarios de sécurité Cluster](service-fabric-cluster-security.md).

>[AZURE.NOTE]
Vous devez prendre en compte votre sélection de sécurité pour la sécurité de nœud-à-nœud avec soin, car il n’existe aucune mise à niveau de cluster à partir de choix de sécurité à l’autre. Modification de la sélection de sécurité de verser une reconstruction cluster complète.

## <a name="configure-windows-security"></a>Configurer la sécurité de Windows
L’exemple de fichier de configuration *ClusterConfig.Windows.JSON* téléchargés avec la [Microsoft.Azure.ServiceFabric.WindowsServer.<version>. ZIP](http://go.microsoft.com/fwlink/?LinkId=730690) autonome cluster contient un modèle de configuration de la sécurité de Windows.  Sécurité de Windows est configurée dans la section **Propriétés** :

```
"security": {
            "ClusterCredentialType": "Windows",
            "ServerCredentialType": "Windows",
            "WindowsIdentities": {
        "ClusterIdentity" : "[domain\machinegroup]",
                "ClientIdentities": [{
                    "Identity": "[domain\username]",
                    "IsAdmin": true
                }]
            }
        }
```

|**Paramètre de configuration**|**Description**|
|-----------------------|--------------------------|
|ClusterCredentialType|Sécurité de Windows est activée en attribuant au paramètre **ClusterCredentialType** pour *Windows*.|
|ServerCredentialType|Sécurité de Windows pour les clients est activée en attribuant au paramètre **ServerCredentialType** pour *Windows*. Cela signifie que les clients du cluster et le cluster lui-même, fonctionnent au sein d’un domaine Active Directory.|
|WindowsIdentities|Contient les identités cluster et du client.|
|ClusterIdentity|Configure la sécurité nœud-à-nœud. Une liste séparée par des virgules du groupe gèrent les comptes de service ou les noms d’ordinateur.|
|ClientIdentities|Configure la sécurité du client-à-nœud. Tableau client des comptes d’utilisateur.|
|Identité|L’identité du client, un utilisateur de domaine.|
|IsAdmin|La valeur True indique que l’utilisateur de domaine a l’accès administrateur client, false pour l’accès utilisateur client.|

[Nœud à la sécurité de nœud](service-fabric-cluster-security.md#node-to-node-security) est configuré par paramètre à l’aide de **ClusterIdentity**. Pour créer des relations d’approbation entre les nœuds, ils doivent être effectuées prenant en charge de l’autre. Il est possible de deux façons différentes : indiquez le compte Service géré groupe qui inclut tous les nœuds du cluster ou spécifier les identités de nœud de domaine de tous les nœuds du cluster. Nous vous recommandons vivement à l’aide de l’approche [Groupe gérées Service compte (gMSA)](https://technet.microsoft.com/library/hh831782.aspx) , en particulier pour clusters de grande taille (plus de 10 nœuds) ou pour les clusters qui sont susceptibles d’agrandir ou réduire.
Cette approche permet aux nœuds être ajouté ou supprimé de la gMSA, sans avoir à modifier au manifeste cluster. Cette approche ne nécessite pas la création d’un groupe de domaine pour lequel les administrateurs cluster ont reçu des droits d’accès à ajouter et supprimer des membres. Pour plus d’informations, voir [Prise en main groupe les comptes de Service](http://technet.microsoft.com/library/jj128431.aspx).

[Client à la sécurité de nœud](service-fabric-cluster-security.md#client-to-node-security) est configuré à l’aide de **ClientIdentities**. Pour établir une approbation entre un client et le cluster, vous devez configurer le cluster pour connaître le client identités il fiables. Cela peut être exécuté de deux façons différentes : spécifiez les utilisateurs du groupe de domaine que vous peuvent vous connecter ou spécifier les utilisateurs de nœud de domaine qui peuvent se connecter. Service tissu prend en charge les deux types de contrôle d’accès différents pour les clients qui sont connectés à un cluster de tissu de Service : administrateur et utilisateur. Contrôle d’accès offre la possibilité pour l’administrateur de cluster limiter l’accès à certains types d’opérations de cluster pour différents groupes d’utilisateurs, ce qui rend le cluster plus sécurisée.  Les administrateurs ont un accès intégral aux fonctionnalités de gestion des (y compris les fonctionnalités en lecture/écriture). Les utilisateurs, par défaut, ont uniquement accès en lecture aux fonctionnalités de gestion des (par exemple, des fonctions de requête) et la possibilité pour résoudre les applications et services.

La section **sécurité** suivante configure la sécurité de Windows et spécifie que les ordinateurs de *ServiceFabric/clusterA.contoso.com* font partie du cluster et que *CONTOSO\usera* a accès au client administrateur :

```
"security": {
    "ClusterCredentialType": "Windows",
    "ServerCredentialType": "Windows",
    "WindowsIdentities": {
        "ClusterIdentity" : "ServiceFabric/clusterA.contoso.com",
        "ClientIdentities": [{
            "Identity": "CONTOSO\\usera",
        "IsAdmin": true
        }]
    }
},
```

## <a name="next-steps"></a>Étapes suivantes

Après avoir configuré la sécurité de Windows dans le fichier *ClusterConfig.JSON* , reprendre le processus de création de cluster dans [créer un cluster autonome fonctionnant sous Windows](service-fabric-cluster-creation-for-windows-server.md).

Pour plus d’informations sur la façon de sécurité nœud-à-nœud, client-à-nœud sécurité et contrôle d’accès basé sur un rôle, voir [scénarios de sécurité Cluster](service-fabric-cluster-security.md).

Voir [se connecter à un cluster sécurisée](service-fabric-connect-to-secure-cluster.md) pour obtenir des exemples de connexion à l’aide de PowerShell ou FabricClient.
