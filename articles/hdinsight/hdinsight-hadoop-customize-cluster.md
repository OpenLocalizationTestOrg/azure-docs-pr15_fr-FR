<properties
    pageTitle="Personnaliser les HDInsight à l’aide des actions de script | Microsoft Azure"
    description="Apprenez à personnaliser les HDInsight à l’aide d’Action de Script."
    services="hdinsight"
    documentationCenter=""
    authors="nitinme"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/05/2016"
    ms.author="nitinme"/>

# <a name="customize-windows-based-hdinsight-clusters-using-script-action"></a>Personnaliser les HDInsight fonctionnant sous Windows à l’aide de Script Action

**Action de script** peut être utilisée pour appeler [des scripts personnalisés](hdinsight-hadoop-script-actions.md) pendant le processus de création de cluster pour l’installation d’un logiciel supplémentaire sur un cluster.

Les informations dans cet article sont spécifiques aux clusters HDInsight fonctionnant sous Windows. Pour les clusters basés sur Linux, voir [clusters HDInsight basé sur Linux personnaliser à l’aide de Script Action](hdinsight-hadoop-customize-cluster-linux.md). 

HDInsight clusters peuvent être personnalisées de plusieurs manières également, par exemple, y compris des comptes supplémentaires de stockage Azure, modifier le Hadoop (core site.xml, hive site.xml, etc.) des fichiers de configuration ou ajout de bibliothèques (par exemple, Hive, Oozie) partagées vers des emplacements courants dans le cluster. Ces personnalisations peuvent être effectuées via PowerShell Azure, le Kit de développement Azure HDInsight .NET ou le portail Azure. Pour plus d’informations, voir [Hadoop créer des groupes dans un HDInsight][hdinsight-provision-cluster].

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell-cli-and-dotnet-sdk.md)]

## <a name="script-action-in-the-cluster-creation-process"></a>Action de script dans le processus de création de cluster

Action de script est utilisée uniquement quand un clusters est en cours de création. Le diagramme suivant illustre lors de l’Action de Script est exécutée pendant le processus de création :

![Étapes de création d’un cluster et de personnalisation de cluster HDInsight][img-hdi-cluster-states]

Lorsque le script est en cours d’exécution, le cluster insère la scène **ClusterCustomization** . À ce stade, le script est exécuté sous le compte d’administrateur système, en parallèle sur tous les nœuds spécifiés dans le cluster et fournit des privilèges administrateur complets sur les nœuds.

> [AZURE.NOTE] Étant donné que vous avez des privilèges d’administrateur sur les nœuds de cluster lors de l’étape **ClusterCustomization** , vous pouvez utiliser le script pour effectuer des opérations, telles que l’arrêt et démarrage des services, y compris les services Hadoop liés. En tant que composant de script, vous devez vous assurer que les services Ambari et autres services Hadoop sont en cours d’exécution avant la fin du script en cours d’exécution. Ces services sont nécessaires pour vérifier correctement l’état et l’état du cluster pendant sa création. Si vous changez n’importe quelle configuration sur le cluster qui affecte ces services, vous devez utiliser les fonctions d’assistance qui sont proposées. Pour plus d’informations sur les fonctions d’assistance, consultez [scripts développer une Action de Script pour HDInsight][hdinsight-write-script].

La sortie et les journaux d’erreurs pour le script sont stockées dans le compte de stockage par défaut spécifié pour le cluster. Les journaux sont stockés dans une table avec le nom **u < \cluster-name-fragment >< \time-stamp > setuplog**. Il s’agit des journaux d’agrégation à partir du script s’exécuter sur tous les nœuds (nœud de tête et nœuds de travail) dans le cluster.

Chaque cluster peut accepter plusieurs actions de script qui sont appelées dans l’ordre dans lequel elles sont définies. Un script peut être exécuté sur le nœud de tête, les nœuds de travail ou les deux.

HDInsight fournit plusieurs scripts pour installer les composants suivants sur les clusters HDInsight :

Nom | Script
----- | -----
**Installer explosion** | https://hdiconfigactions.BLOB.Core.Windows.NET/sparkconfigactionv03/Spark-installer-v03.ps1. Voir [installer et utiliser au service sur clusters HDInsight][hdinsight-install-spark].
**Installer R** | https://hdiconfigactions.BLOB.Core.Windows.NET/rconfigactionv02/r-installer-v02.ps1. Voir [installer et utiliser R sur clusters HDInsight][hdinsight-install-r].
**Installer mode série sur LAN.r** | https://hdiconfigactions.BLOB.Core.Windows.NET/solrconfigactionv01/solr-installer-v01.ps1. Voir [installer et utiliser le mode série sur LAN.r sur HDInsight clusters](hdinsight-hadoop-solr-install.md).
- **Installer Giraph** | https://hdiconfigactions.BLOB.Core.Windows.NET/giraphconfigactionv01/giraph-installer-v01.ps1. Voir [installer et utiliser des clusters Giraph sur HDInsight](hdinsight-hadoop-giraph-install.md).
| **Chargez au préalable les bibliothèques Hive** | https://hdiconfigactions.BLOB.Core.Windows.NET/setupcustomhivelibsv01/Setup-customhivelibs-v01.ps1. Consultez [Ajouter ruche des bibliothèques sur des clusters HDInsight](hdinsight-hadoop-add-hive-libraries.md) |


## <a name="call-scripts-using-the-azure-portal"></a>Appeler des scripts à l’aide du portail Azure

**À partir du portail Azure**

1. Commencer à créer un cluster tels que décrits sur [Hadoop créer des groupes dans un HDInsight](hdinsight-provision-clusters.md#portal).
2. Sous Configuration facultatives, pour la carte de **Script Actions** , cliquez sur **Ajouter une action de script** pour fournir des détails sur l’action script, comme indiqué ci-dessous :

    ![Action de Script d’utilisation pour personnaliser un cluster] (./media/hdinsight-hadoop-customize-cluster/HDI.CreateCluster.8.png "Action de Script d’utilisation pour personnaliser un cluster")

    <table border='1'>
        <tr><th>Propriété</th><th>Valeur</th></tr>
        <tr><td>Nom</td>
            <td>Attribuez un nom à l’action de script.</td></tr>
        <tr><td>Script URI</td>
            <td>Spécifiez l’URI pour le script qui est appelé pour personnaliser le cluster. s</td></tr>
        <tr><td>Tête/collaborateur</td>
            <td>Spécifier les nœuds (**tête** ou **collaborateur**) sur lequel s’exécute le script de personnalisation. </b>.
        <tr><td>Paramètres</td>
            <td>Spécifier les paramètres, le cas échéant par le script.</td></tr>
    </table>

    Appuyez sur ENTRÉE pour ajouter plusieurs actions de script pour installer plusieurs composants sur le cluster.

3. Cliquez sur **Sélectionner** pour enregistrer la configuration de l’action script et poursuivre la création d’un cluster.

## <a name="call-scripts-using-azure-powershell"></a>Appeler scripts à l’aide de PowerShell Azure

Ce script PowerShell suivant montre comment installer explosion sur cluster HDInsight en fonction de Windows.  

    # Provide values for these variables
    $subscriptionID = "<Azure Suscription ID>" # After "Login-AzureRmAccount", use "Get-AzureRmSubscription" to list IDs.

    $nameToken = "<Enter A Name Token>"  # The token is use to create Azure service names.
    $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")
    
    $resourceGroupName = $namePrefix + "rg"
    $location = "EAST US 2" # used for creating resource group, storage account, and HDInsight cluster.
    
    $hdinsightClusterName = $namePrefix + "spark"
    $httpUserName = "admin"
    $httpPassword = "<Enter a Password>"
    
    $defaultStorageAccountName = "$namePrefix" + "store"
    $defaultBlobContainerName = $hdinsightClusterName
    
    #############################################################
    # Connect to Azure
    #############################################################
    
    Try{
        Get-AzureRmSubscription
    }
    Catch{
        Login-AzureRmAccount
    }
    Select-AzureRmSubscription -SubscriptionId $subscriptionID
    
    #############################################################
    # Prepare the dependent components
    #############################################################
    
    # Create resource group
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
    
    # Create storage account
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $defaultStorageAccountName `
        -Location $location `
        -Type Standard_GRS
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey `
                                    -ResourceGroupName $resourceGroupName `
                                    -Name $defaultStorageAccountName)[0].Value
    $defaultStorageAccountContext = New-AzureStorageContext `
                                    -StorageAccountName $defaultStorageAccountName `
                                    -StorageAccountKey $storageAccountKey  
    New-AzureStorageContainer `
        -Name $defaultBlobContainerName `
        -Context $defaultStorageAccountContext
    
    #############################################################
    # Create cluster with ApacheSpark
    #############################################################
    
    # Specify the configuration options
    $config = New-AzureRmHDInsightClusterConfig `
                -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
                -DefaultStorageAccountKey $defaultStorageAccountKey 
                
    
    # Add a script action to the cluster configuration
    $config = Add-AzureRmHDInsightScriptAction `
                -Config $config `
                -Name "Install Spark" `
                -NodeType HeadNode `
                -Uri https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1 `
    
    # Start creating a cluster with Spark installed
    New-AzureRmHDInsightCluster `
            -ResourceGroupName $resourceGroupName `
            -ClusterName $hdinsightClusterName `
            -Location $location `
            -ClusterSizeInNodes 2 `
            -ClusterType Hadoop `
            -OSType Windows `
            -DefaultStorageContainer $defaultBlobContainerName `
            -Config $config


Pour installer d’autres logiciels, vous devrez remplacer le fichier de script dans le script :


Lorsque vous y êtes invité, entrez les informations d’identification pour le cluster. Il peut prendre plusieurs minutes avant que le cluster est créé.

## <a name="call-scripts-using-net-sdk"></a>Appeler scripts à l’aide du Kit de développement .NET 

L’exemple suivant montre comment installer explosion sur cluster HDInsight en fonction de Windows. Pour installer d’autres logiciels, vous devrez remplacer le fichier de script dans le code.

**Pour créer un cluster HDInsight avec explosion** 

1. Créer une application console c# dans Visual Studio.
2. À partir de la Console Gestionnaire de Package Nuget, exécutez la commande suivante.

        Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Pre
        Install-Package Microsoft.Azure.Management.ResourceManager -Pre
        Install-Package Microsoft.Azure.Management.HDInsight

2. Utilisez la commande suivante à l’aide d’instructions dans le fichier Program.cs :

        using System;
        using System.Security;
        using Microsoft.Azure;
        using Microsoft.Azure.Management.HDInsight;
        using Microsoft.Azure.Management.HDInsight.Models;
        using Microsoft.Azure.Management.ResourceManager;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Rest;
        using Microsoft.Rest.Azure.Authentication;

3. Placez le code de la classe avec les éléments suivants :

        private static HDInsightManagementClient _hdiManagementClient;

        // Replace with your AAD tenant ID if necessary
        private const string TenantId = UserTokenProvider.CommonTenantId; 
        private const string SubscriptionId = "<Your Azure Subscription ID>";
        // This is the GUID for the PowerShell client. Used for interactive logins in this example.
        private const string ClientId = "1950a258-227b-4e31-a9cf-717495945fc2";
        private const string ResourceGroupName = "<ExistingAzureResourceGroupName>";
        private const string NewClusterName = "<NewAzureHDInsightClusterName>";
        private const int NewClusterNumWorkerNodes = 2;
        private const string NewClusterLocation = "East US";
        private const string NewClusterVersion = "3.2";
        private const string ExistingStorageName = "<ExistingAzureStorageAccountName>";
        private const string ExistingStorageKey = "<ExistingAzureStorageAccountKey>";
        private const string ExistingContainer = "<ExistingAzureBlobStorageContainer>";
        private const string NewClusterType = "Hadoop";
        private const OSType NewClusterOSType = OSType.Windows;
        private const string NewClusterUsername = "<HttpUserName>";
        private const string NewClusterPassword = "<HttpUserPassword>";

        static void Main(string[] args)
        {
            System.Console.WriteLine("Running");

            // Authenticate and get a token
            var authToken = Authenticate(TenantId, ClientId, SubscriptionId);
            // Flag subscription for HDInsight, if it isn't already.
            EnableHDInsight(authToken);
            // Get an HDInsight management client
            _hdiManagementClient = new HDInsightManagementClient(authToken);

            CreateCluster();
        }

        private static void CreateCluster()
        {
            var parameters = new ClusterCreateParameters
            {
                ClusterSizeInNodes = NewClusterNumWorkerNodes,
                Location = NewClusterLocation,
                ClusterType = NewClusterType,
                OSType = NewClusterOSType,
                Version = NewClusterVersion,

                DefaultStorageAccountName = ExistingStorageName,
                DefaultStorageAccountKey = ExistingStorageKey,
                DefaultStorageContainer = ExistingContainer,

                UserName = NewClusterUsername,
                Password = NewClusterPassword,
            };

            ScriptAction sparkScriptAction = new ScriptAction("Install Spark",
                new Uri("https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1"), "");

            parameters.ScriptActions.Add(ClusterNodeType.HeadNode, new System.Collections.Generic.List<ScriptAction> { sparkScriptAction });
            parameters.ScriptActions.Add(ClusterNodeType.WorkerNode, new System.Collections.Generic.List<ScriptAction> { sparkScriptAction });

            _hdiManagementClient.Clusters.Create(ResourceGroupName, NewClusterName, parameters);
        }

        /// <summary>
        /// Authenticate to an Azure subscription and retrieve an authentication token
        /// </summary>
        /// <param name="TenantId">The AAD tenant ID</param>
        /// <param name="ClientId">The AAD client ID</param>
        /// <param name="SubscriptionId">The Azure subscription ID</param>
        /// <returns></returns>
        static TokenCloudCredentials Authenticate(string TenantId, string ClientId, string SubscriptionId)
        {
            var authContext = new AuthenticationContext("https://login.microsoftonline.com/" + TenantId);
            var tokenAuthResult = authContext.AcquireToken("https://management.core.windows.net/", 
                ClientId, 
                new Uri("urn:ietf:wg:oauth:2.0:oob"), 
                PromptBehavior.Always, 
                UserIdentifier.AnyUser);
            return new TokenCloudCredentials(SubscriptionId, tokenAuthResult.AccessToken);
        }
        /// <summary>
        /// Marks your subscription as one that can use HDInsight, if it has not already been marked as such.
        /// </summary>
        /// <remarks>This is essentially a one-time action; if you have already done something with HDInsight
        /// on your subscription, then this isn't needed at all and will do nothing.</remarks>
        /// <param name="authToken">An authentication token for your Azure subscription</param>
        static void EnableHDInsight(TokenCloudCredentials authToken)
        {
            // Create a client for the Resource manager and set the subscription ID
            var resourceManagementClient = new ResourceManagementClient(new TokenCredentials(authToken.Token));
            resourceManagementClient.SubscriptionId = SubscriptionId;
            // Register the HDInsight provider
            var rpResult = resourceManagementClient.Providers.Register("Microsoft.HDInsight");
        }


4. Appuyez sur **F5** pour exécuter l’application.


## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>Prise en charge des logiciels open source utilisés sur clusters HDInsight
Le service Microsoft Azure HDInsight est une plateforme flexible qui vous permet de créer des applications de données volumineuses dans le cloud à l’aide d’un réseau de technologies open source formé autour d’Hadoop. Microsoft Azure fournit un niveau général de prise en charge pour les technologies open source, comme indiqué dans la section de **Prise en charge étendue** du <a href="http://azure.microsoft.com/support/faq/" target="_blank">site Web du Forum aux questions sur Azure prend en charge</a>. Le service HDInsight fournit un niveau supplémentaire de prise en charge pour certains composants, comme décrit ci-dessous.

Il existe deux types de composants open source qui sont disponibles dans le service HDInsight :

- **Composants intégrés** - ces composants sont préinstallés sur clusters HDInsight et fournir des fonctionnalités de base du cluster. Par exemple, fils ResourceManager, le langage de requête Hive (HiveQL) et la bibliothèque Mahout appartiennent à cette catégorie. Une liste complète des composants de cluster n’est disponible dans [Quelles sont les nouveautés dans les versions de cluster Hadoop fournies par HDInsight ?](hdinsight-component-versioning.md) </a>.
- **Composants personnalisés** - vous, en tant qu’utilisateur du cluster, installez ou utiliser dans votre charge de travail n’importe quel composant disponible dans la Communauté ou créé par vous.

Composants intégrés entièrement prises en charge et Support Microsoft aidera à identifier et résoudre les problèmes liés à ces composants.

> [AZURE.WARNING] Composants fournis avec le cluster HDInsight entièrement prises en charge et Support Microsoft aidera à identifier et résoudre les problèmes liés à ces composants.
>
> Composants personnalisés recevront RCSI prise en charge pour vous aider à résoudre le problème. Cela peut provoquer de résoudre le problème ou vous demandant de s’engager canaux disponibles pour les technologies d’ouvrir la source où se trouve une grande expertise pour que la technologie. Par exemple, de nombreux sites communautaires qui peuvent être utilisés, comme : [forum MSDN pour HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com). Projets Apache contient des sites de projet sur [http://apache.org](http://apache.org), par exemple : [Hadoop](http://hadoop.apache.org/), [commandé](http://spark.apache.org/).

Le service HDInsight fournit plusieurs méthodes pour utiliser des composants personnalisés. Quelle que soit comment un composant est utilisé ou installé sur le cluster, le même niveau de prise en charge s’applique. Voici une liste des façons plus courantes que les composants personnalisés peuvent être utilisés sur les clusters HDInsight :

1. Soumission de la tâche - Hadoop ou autres types de travaux qui exécuter ou utilise des composants personnalisés peut être soumise au cluster.
2. Personnalisation de cluster - lors de la création de cluster, vous pouvez spécifier des paramètres supplémentaires et des composants personnalisés qui seront installés sur les nœuds du cluster.
3. Exemples - pour les composants personnalisés, Microsoft et d’autres personnes peuvent fournir des exemples de la façon dont ces composants peuvent être utilisés dans les groupes HDInsight. Ces exemples sont fournis sans prise en charge.

## <a name="develop-script-action-scripts"></a>Développer des scripts d’Action de Script

Voir [Action de Script développer des scripts pour HDInsight][hdinsight-write-script].


## <a name="see-also"></a>Voir aussi

- [Créer des clusters Hadoop dans HDInsight] [ hdinsight-provision-cluster] fournit des instructions sur la création d’un cluster HDInsight à l’aide d’autres options personnalisées.
- [Développer des scripts d’Action de Script pour HDInsight][hdinsight-write-script]
- [Installer et utiliser explosion sur clusters HDInsight][hdinsight-install-spark]
- [Installer et utiliser R sur clusters HDInsight][hdinsight-install-r]
- [Installer et utiliser le mode série sur LAN.r sur HDInsight clusters](hdinsight-hadoop-solr-install.md).
- [Installer et utiliser les clusters Giraph sur HDInsight](hdinsight-hadoop-giraph-install.md).

[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-write-script]: hdinsight-hadoop-script-actions.md
[hdinsight-provision-cluster]: hdinsight-provision-clusters.md
[powershell-install-configure]: powershell-install-configure.md


[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster/HDI-Cluster-state.png "Étapes de création d’un cluster"
