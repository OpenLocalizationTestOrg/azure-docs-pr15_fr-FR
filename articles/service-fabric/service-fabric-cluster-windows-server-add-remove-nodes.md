<properties
   pageTitle="Ajouter ou supprimer des nœuds à un cluster de Service TISSU autonome | Microsoft Azure"
   description="Découvrez comment ajouter ou supprimer des nœuds à un cluster Azure Service tissu sur un ordinateur physique ou virtuel exécutant Windows Server, qui pourrait être locaux ou dans n’importe quel cloud."
   services="service-fabric"
   documentationCenter=".net"
   authors="dsk-2015"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/20/2016"
   ms.author="dkshir;chackdan"/>


# <a name="add-or-remove-nodes-to-a-standalone-service-fabric-cluster-running-on-windows-server"></a>Ajouter ou supprimer des nœuds à un cluster de Service TISSU autonome s’exécutant sur Windows Server

Une fois que vous avez [créé votre cluster Service TISSU autonome sur les ordinateurs Windows Server](service-fabric-cluster-creation-for-windows-server.md), besoins de votre entreprise peuvent modifier de sorte que vous devrez peut-être ajouter ou supprimer plusieurs nœuds pour votre cluster. Cet article fournit la procédure détaillée pour atteindre cet objectif.


## <a name="add-nodes-to-your-cluster"></a>Ajouter des nœuds à votre cluster

1. Préparer la machine virtuelle/machine que vous voulez ajouter à votre cluster en suivant les étapes mentionnées dans la section [préparer les ordinateurs afin de respecter les conditions préalables pour le déploiement de cluster](service-fabric-cluster-creation-for-windows-server.md#preparemachines) .
2. Planifier les domaines défaillance et mise à niveau vous vous apprêtez à ajouter cette machine virtuelle/machine pour.
3. Bureau à distance (RDP) à la machine virtuelle/machine que vous voulez ajouter au cluster.
4. Copie ou [Télécharger le package autonome pour tissu de Service de Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690) sur cet ordinateur/machine virtuelle et décompressez le package.
5. Exécuter Powershell en tant qu’administrateur, puis accédez à l’emplacement du package décompressé dans.
6. Exécutez *AddNode.ps1* Powershell avec les paramètres décrivant le nouveau nœud à ajouter. L’exemple suivant ajoute un nouveau nœud appelé VM5, avec le type NodeType0, adresse IP 182.17.34.52 dans UD1 et FD1. *ExistingClusterConnectionEndPoint* est un point de terminaison de connexion pour un nœud déjà dans le cluster existant. Pour ce point de terminaison, vous pouvez choisir l’adresse IP de *n’importe quel* nœud dans le cluster.

```
.\AddNode.ps1 -NodeName VM5 -NodeType NodeType0 -NodeIPAddressorFQDN 182.17.34.52 -ExistingClientConnectionEndpoint 182.17.34.50:19000 -UpgradeDomain UD1 -FaultDomain FD1 -AcceptEULA

```

## <a name="remove-nodes-from-your-cluster"></a>Supprimer des nœuds de votre cluster

1. En fonction du niveau de fiabilité choisi pour le cluster, vous ne pouvez pas supprimer les premiers nœuds (3/5/7/9) n du type de nœud principal
2. Commande RemoveNode en cours d’exécution sur un cluster de développement n’est pas pris en charge.
2. Bureau à distance (RDP) à la machine virtuelle/machine que vous souhaitez supprimer du cluster.
2. Copie ou [Télécharger le package autonome pour tissu de Service pour Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690) et décompressez le package sur cet ordinateur/machine virtuelle.
3. Exécuter Powershell en tant qu’administrateur, puis accédez à l’emplacement du package décompressé dans.
4. Exécutez *RemoveNode.ps1* dans PowerShell. L’exemple ci-dessous supprime le nœud actuel du cluster. *ExistingClientConnectionEndpoint* est un point de terminaison de connexion de client pour n’importe quel nœud reste dans le cluster. Sélectionnez l’adresse IP et le port de point de terminaison de *n’importe quel* **autre nœud** dans le cluster. Cet **autre nœud** actualise à son tour la configuration du cluster pour le nœud supprimé. 

```
.\RemoveNode.ps1 -ExistingClientConnectionEndpoint 182.17.34.50:19000
```

Veuillez noter que même après la suppression d’un nœud, il peut afficher sous la forme en cours vers le bas dans les requêtes et SFX. Ceci est un défaut connu et sera corrigé dans une version à venir. 


## <a name="next-steps"></a>Étapes suivantes
- [Paramètres de configuration pour le cluster Windows autonome](service-fabric-cluster-manifest.md)
- [Banque d’informations sécurisé un cluster autonome sur Windows à l’aide de la sécurité de Windows](service-fabric-windows-cluster-windows-security.md)
- [Banque d’informations sécurisé un cluster autonome sur Windows à l’aide de X509 certificats](service-fabric-windows-cluster-x509-security.md)
- [Créer un cluster de Service TISSU autonome avec Azure machines virtuelles exécutant Windows](service-fabric-cluster-creation-with-windows-azure-vms.md)
