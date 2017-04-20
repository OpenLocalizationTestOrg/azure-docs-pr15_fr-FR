<properties
   pageTitle="Mettre à niveau un cluster autonome Service tissu sur Windows Server | Microsoft Azure"
   description="Mettre à niveau le code de Service tissu et/ou la configuration qui s’exécute à un cluster de Service TISSU autonome, y compris la définition du mode de mise à jour de cluster"
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/10/2016"
   ms.author="chackdan"/>


# <a name="upgrade-your-standalone-service-fabric-cluster-on-windows-server"></a>Mettre à niveau votre cluster Service TISSU autonome sur Windows Server

> [AZURE.SELECTOR]
- [Cluster Azure](service-fabric-cluster-upgrade.md)
- [Cluster autonome](service-fabric-cluster-upgrade-windows-server.md)

Pour les systèmes moderne, conception de la mise à jour est crucial pour atteindre un résultat positif à long terme de votre produit. Un cluster de Service tissu est une ressource qui vous appartient. Cet article décrit comment vous pouvez vous assurer que le cluster s’exécute toujours versions prises en charge de la structure de service code et les configurations.

## <a name="controlling-the-fabric-version-that-runs-on-your-cluster"></a>Contrôle de la version de tissu qui s’exécute sur votre Cluster

Vous pouvez définir votre cluster afin de télécharger les mises à jour de service tissu, lorsque Microsoft publie une nouvelle version ou cliquez pour sélectionner une version de tissu pris en charge que vous voulez faire figurer sur votre cluster. 

Pour cela, en définissant la configuration du cluster « fabricClusterAutoupgradeEnabled » true ou false.


>[AZURE.NOTE] Veillez à conserver votre cluster toujours exécutant une version prise en charge tissu de Service. Au fur et nous annoncer la sortie d’une nouvelle version de tissu de service, la version précédente est marquée après un minimum de 60 jours à partir de cette date de fin de prise en charge. Les nouvelles versions sont annoncé [sur le blog de l’équipe TISSU service](https://blogs.msdn.microsoft.com/azureservicefabric/ ). La nouvelle version n’est disponible pour choisir puis. 


Vous pouvez mettre à niveau votre cluster vers la nouvelle version uniquement si vous utilisez une configuration nœud de style de production, où chaque nœud Service TISSU alloué sur un physique distinct ou la machine virtuelle. Si vous disposez d’un cluster de développement, lorsqu’il existe plusieurs nœuds de tissu service sur un seul ordinateur physique ou virtuel, vous devez supprimer votre cluster et recréer avec la nouvelle version.


Il existe deux flux de travail distinctes pour la mise à niveau votre cluster plus tard ou une version de tissu service pris en charge. Une pour clusters dotés de connectivité à télécharger la version la plus récente automatiquement et l’autre pour les clusters aucune connectivité à télécharger la dernière version de tissu de Service.

### <a name="upgrade-the-clusters-with-connectivity-to-download-the-latest-code-and-configuration"></a>Mettre à niveau les groupes avec une connexion à télécharger les derniers code et configuration 

Suivez ces étapes pour mettre à niveau votre cluster vers une version prise en charge, si vos nœuds de cluster se connecter à internet à [http://download.microsoft.com](http://download.microsoft.com) 

Pour les clusters dotées de connectivité à [http://download.microsoft.com](http://download.microsoft.com), nous régulièrement vérifier la disponibilité de nouvelles versions de tissu de service.


Lorsqu’une nouvelle version de tissu de service est disponible, le package est téléchargé localement pour le cluster et sa mise en service de mise à niveau. En outre pour Informez le client de cette nouvelle version, le système place un avertissement d’intégrité explicite cluster semblable à ce qui suit :

« La cluster version actuelle (version #) prise en charge termine [Date]. », une fois que le cluster s’exécute la dernière version, l’avertissement disparaît.


#### <a name="cluster-upgrade-workflow"></a>Flux de travail cluster mise à niveau.
 
Une fois que vous obtenez l’avertissement de santé cluster, vous devez effectuer les opérations suivantes :

1. Connectez-vous au cluster à partir de n’importe quel ordinateur qui a accès administrateur à tous les ordinateurs qui sont répertoriés sous forme de nœuds dans le cluster. L’ordinateur sur lequel s’exécute ce script n’a pas à faire partie du cluster

    ```powershell

    ###### connect to the secure cluster using certs
    $ClusterName= "mysecurecluster.something.com:19000"
    $CertThumbprint= "70EF5E22ADB649799DA3C8B6A6BF7FG2D630F8F3" 
    Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
        -X509Credential `
        -ServerCertThumbprint $CertThumbprint  `
        -FindType FindByThumbprint `
        -FindValue $CertThumbprint `
        -StoreLocation CurrentUser `
        -StoreName My
    ```

2. Obtenir la liste des tissu de service que vous pouvez mettre à niveau vers les versions

    ```powershell

    ###### Get the list of available service fabric versions 
    Get-ServiceFabricRegisteredClusterCodeVersion
    ```

    Vous devez obtenir un résultat semblable à celui-ci :

    ![obtenir TISSU des versions][getfabversions]

3. Démarrer une mise à niveau de cluster à un des versions disponible à l’aide de [PowerShell démarrer ServiceFabricClusterUpgrade cmd](https://msdn.microsoft.com/library/mt125872.aspx)

    ```Powershell

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Here is a filled out example

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback
    
    ```
Vous pouvez suivre la progression de la mise à niveau dans l’Explorateur de tissu de Service ou en exécutant la commande shell power suivante

    ```powershell

    Get-ServiceFabricClusterUpgrade
    ```

    If the cluster health policies are not met, the upgrade is rolled back. You can specify custom health policies at the time for the Start-ServiceFabricClusterUpgrade command refer to [this document](https://msdn.microsoft.com/library/mt125872.aspx) for details. 

Une fois que vous avez corrigé les problèmes qui a donné lieu à l’annulation, vous devez démarrer la mise à niveau à nouveau, en suivant les mêmes étapes qu’avant.


### <a name="upgrade-the-clusters-with-uno-connectivityu-to-download-the-latest-code-and-configuration"></a>Mettre à niveau les groupes avec <U>aucune connectivité</u> pour télécharger les dernières code et configuration

Suivez ces étapes pour mettre à niveau votre cluster vers une version prise en charge, si votre cluster nœuds **n’ont pas** la connectivité internet à [http://download.microsoft.com](http://download.microsoft.com) 


>[AZURE.NOTE]Si vous exécutez un cluster qui n’est pas connectés à internet, vous devrez surveiller le blog de l’équipe TISSU service pour recevoir une notification d’une nouvelle version. Le système **ne** placez aucun avertissement santé cluster pour être averti de celle-ci.  

1. Modifier la configuration de votre cluster pour définir la propriété suivante sur false.

        "fabricClusterAutoupgradeEnabled": false,

et démarrer une mise à niveau de configuration. pour des détails d’utilisation, voir [démarrage ServiceFabricClusterUpgrade PS cmd](https://msdn.microsoft.com/library/mt125872.aspx) . La version manifeste cluster est la version que vous avez dans le clusterConfig.JSON. Veillez à mettre à jour avant coup la mise à niveau de configuration.

```powershell

    Start-ServiceFabricClusterUpgrade [-Config] [-ClusterConfigVersion] -FailureAction Rollback -Monitored 

```

#### <a name="cluster-upgrade-workflow"></a>Flux de travail cluster mise à niveau.
 


1. Téléchargez la dernière version du package à partir de document de [créer un cluster TISSU service pour windows server](service-fabric-cluster-creation-for-windows-server.md) 


1. Connectez-vous au cluster à partir de n’importe quel ordinateur qui a accès administrateur à tous les ordinateurs qui sont répertoriés sous forme de nœuds dans le cluster. L’ordinateur sur lequel s’exécute ce script n’a pas à faire partie du cluster 

    ```powershell

    ###### connect to the cluster
    $ClusterName= "mysecurecluster.something.com:19000"
    $CertThumbprint= "70EF5E22ADB649799DA3C8B6A6BF7FG2D630F8F3" 
    Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
        -X509Credential `
        -ServerCertThumbprint $CertThumbprint  `
        -FindType FindByThumbprint `
        -FindValue $CertThumbprint `
        -StoreLocation CurrentUser `
        -StoreName My
    ```

2. Copiez le package téléchargé dans le magasin d’image cluster.

    ```powershell

    ###### Get the list of available service fabric versions 
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file including the path to it> -ImageStoreConnectionString "fabric:ImageStore"

    ###### Here is a filled out example
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath .\MicrosoftAzureServiceFabric.5.3.301.9590.cab -ImageStoreConnectionString "fabric:ImageStore"


    ```

2. Enregistrer le package copié 

    ```powershell

    ###### Get the list of available service fabric versions 
    Register-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file> 

    ###### Here is a filled out example
    Register-ServiceFabricClusterPackage -Code -CodePackagePath MicrosoftAzureServiceFabric.5.3.301.9590.cab

     ```


3. Démarrer une mise à niveau de cluster à l’une des versions est disponible. 

    ```Powershell

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Here is a filled out example
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback
    
    ```
Vous pouvez suivre la progression de la mise à niveau dans l’Explorateur de tissu de Service ou en exécutant la commande shell power suivante

    ```powershell

    Get-ServiceFabricClusterUpgrade
    ```

    If the cluster health policies are not met, the upgrade is rolled back. You can specify custom health policies at the time for the start-serviceFabricClusterUpgrade command refer to [this document](https://msdn.microsoft.com/library/mt125872.aspx) for details. 

Une fois que vous avez corrigé les problèmes qui a donné lieu à l’annulation, vous devez démarrer la mise à niveau à nouveau, en suivant les mêmes étapes qu’avant.



## <a name="next-steps"></a>Étapes suivantes
- Découvrez comment personnaliser certains [paramètres du service TISSU cluster TISSU](service-fabric-cluster-fabric-settings.md)
- Découvrez comment faire [évoluer votre cluster et arrière](service-fabric-cluster-scale-up-down.md)
- En savoir plus sur [les mises à niveau de l’application](service-fabric-application-upgrade.md)

<!--Image references-->
[getfabversions]: ./media/service-fabric-cluster-upgrade-windows-server/getfabversions.PNG
