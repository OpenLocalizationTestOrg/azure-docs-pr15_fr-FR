<properties
   pageTitle="Créer un cluster autonome avec Azure machines virtuelles exécutant Windows | Microsoft Azure"
   description="Découvrez comment créer et gérer un cluster Azure Service tissu sur Azure machines virtuelles exécutant Windows Server."
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
   ms.date="08/05/2016"
   ms.author="dkshir;chackdan"/>



# <a name="create-a-three-node-standalone-service-fabric-cluster-with-azure-virtual-machines-running-windows-server"></a>Créer un cluster de Service tissu de trois nœud autonome avec Azure machines virtuelles exécutant Windows Server

Cet article décrit comment créer un cluster sur Windows Azure machines virtuelles (machines virtuelles), en utilisant le programme d’installation du Service TISSU autonome pour Windows Server. Il s’agit d’un cas spécial de [créer et gérer un cluster s’exécutant sur Windows Server](service-fabric-cluster-creation-for-windows-server.md) où les ordinateurs virtuels sont [Machines virtuelles Azure exécutant Windows Server](../virtual-machines/virtual-machines-windows-hero-tutorial.md), mais vous ne créez pas [un cluster de tissu de Service de nuage Azure](service-fabric-cluster-creation-via-portal.md). La différence est que le cluster Service TISSU autonome créé par la procédure suivante est entièrement géré par vous, tout en la Azure sur le cloud Service TISSU clusters sont gérés et mis à niveau par le fournisseur de ressources tissu de Service.


## <a name="steps-to-create-the-standalone-cluster"></a>Étapes pour créer le cluster autonome

1. Connectez-vous au portail Azure et créez une nouvelle Windows Server 2012 R2 centre de données machine virtuelle dans un groupe de ressources. Consultez l’article [créer une machine virtuelle Windows dans le portail Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md) pour plus d’informations.
2. Ajouter quelques davantage Windows Server 2012 R2 centre de données d’ordinateurs virtuels au même groupe de ressources. Assurez-vous que chacun des ordinateurs virtuels a le même nom d’utilisateur administrateur et mot de passe lors de la création. Une fois créé, vous devriez voir tous les ordinateurs trois virtuels dans le même réseau virtuel.
3. Connectez-vous à chacun des ordinateurs virtuels et désactiver le pare-feu Windows à l’aide [Gestionnaire de serveur, tableau de bord Server Local](https://technet.microsoft.com/library/jj134147.aspx). Cela garantit que le trafic réseau peut communiquer entre les ordinateurs. Lorsque vous êtes connecté à chaque ordinateur, obtenir l’adresse IP en ouvrant une invite de commandes et en tapant `ipconfig`. Vous pouvez également voir l’adresse IP de chaque ordinateur en sélectionnant la ressource réseau virtuel du groupe de ressources dans le portail Azure.
4. Se connecter à un des ordinateurs virtuels et testez que vous pouvez tester les ordinateurs deux virtuels avec succès.
5. Se connecter à un des machines virtuelles et [Télécharger le package de Service TISSU autonome pour Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690) dans un nouveau dossier sur l’ordinateur et extrayez le package.
6. Ouvrez le fichier *ClusterConfig.Unsecure.MultiMachine.json* dans le bloc-notes et modifier chaque nœud avec les trois adresses IP des ordinateurs. Modifier le nom du cluster dans la partie supérieure et enregistrer le fichier.  Voici un exemple partiel du manifeste cluster.

    ```
    {
        "name": "TestCluster",
        "clusterManifestVersion": "1.0.0",
        "apiVersion": "2015-01-01-alpha",
        "nodes": [
        {
            "nodeName": "vm0",
            "metadata": "Replace the localhost with valid IP address or FQDN below",
            "iPAddress": "10.7.0.5",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc1/r0",
            "upgradeDomain": "UD0"
        },
        {
            "nodeName": "vm1",
            "metadata": "Replace the localhost with valid IP address or FQDN below",
            "iPAddress": "10.7.0.4",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc2/r0",
            "upgradeDomain": "UD1"
        },
        {
            "nodeName": "vm2",
            "metadata": "Replace the localhost with valid IP address or FQDN below",
            "iPAddress": "10.7.0.6",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc3/r0",
            "upgradeDomain": "UD2"
        }
    ],
    ```

7. Ouvrez une [fenêtre PowerShell ISE](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/introducing-the-windows-powershell-ise). Accédez au dossier où vous extrait le package d’installation autonome téléchargé et enregistré le fichier manifeste cluster. Exécutez la commande PowerShell suivante.

    ```
    .\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json -MicrosoftServiceFabricCabFilePath .\MicrosoftAzureServiceFabric.cab
    ```

8. Vous devriez voir la session PowerShell exécuter, connectez-vous à chaque ordinateur et créer un cluster. Après environ une minute, vous pouvez vérifier si le cluster est opérationnel en vous connectant à l’Explorateur de tissu de Service sur l’un des adresse IP de l’ordinateur, par exemple à l’aide de `http://10.7.0.5:19080/Explorer/index.html`. Dans la mesure où il s’agit d’un cluster autonome à l’aide de machines virtuelles Azure, afin de pouvoir sécurisé, vous devez [déployer des certificats pour les ordinateurs virtuels Azure](service-fabric-windows-cluster-x509-security.md) ou configurer un des ordinateurs en tant que [contrôleur de Windows Server Active Directory (AD) pour l’authentification Windows](service-fabric-windows-cluster-windows-security.md), comme vous le feriez en local.


## <a name="next-steps"></a>Étapes suivantes
- [Créer des clusters de Service TISSU autonome sur Windows Server ou Linux](service-fabric-deploy-anywhere.md)
- [Ajouter ou supprimer des nœuds à un cluster de Service TISSU autonome](service-fabric-cluster-windows-server-add-remove-nodes.md)
- [Paramètres de configuration pour le cluster Windows autonome](service-fabric-cluster-manifest.md)
- [Banque d’informations sécurisé un cluster autonome sur Windows à l’aide de la sécurité de Windows](service-fabric-windows-cluster-windows-security.md)
- [Banque d’informations sécurisé un cluster autonome sur Windows à l’aide de X509 certificats](service-fabric-windows-cluster-x509-security.md)
