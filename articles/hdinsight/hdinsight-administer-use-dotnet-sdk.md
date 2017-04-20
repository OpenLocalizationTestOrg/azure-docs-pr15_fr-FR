<properties
    pageTitle="Gérer les groupes de Hadoop dans un HDInsight avec .NET SDK | Microsoft Azure"
    description="Découvrez comment effectuer des tâches d’administration pour les groupes Hadoop dans HDInsight à l’aide du Kit de développement .NET HDInsight."
    services="hdinsight"
    editor="cgronlun"
    manager="jhubbard"
    tags="azure-portal"
    authors="mumian"
    documentationCenter=""/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/02/2016"
    ms.author="jgao"/>

# <a name="manage-hadoop-clusters-in-hdinsight-by-using-net-sdk"></a>Gérer les groupes Hadoop dans un HDInsight à l’aide du Kit de développement .NET

[AZURE.INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Découvrez comment gérer les HDInsight à l’aide [HDInsight.NET SDK](https://msdn.microsoft.com/library/mt271028.aspx).


**Conditions préalables**

Avant de commencer cet article, vous devez disposer des éléments suivants :

- **Azure un abonnement**. Voir [Azure obtenir la version d’évaluation gratuite](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).


##<a name="connect-to-azure-hdinsight"></a>Se connecter à Azure HDInsight

Vous devez les packages Nuget suivants :

    Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Pre
    Install-Package Microsoft.Azure.Management.ResourceManager -Pre
    Install-Package Microsoft.Azure.Management.HDInsight

L’exemple de code suivant vous montre comment se connecter à Azure avant de pouvoir administrer HDInsight clusters sous votre abonnement Azure.

    using System;
    using Microsoft.Azure;
    using Microsoft.Azure.Management.HDInsight;
    using Microsoft.Azure.Management.HDInsight.Models;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;
    using Microsoft.Rest.Azure.Authentication;

    namespace HDInsightManagement
    {
        class Program
        {
            private static HDInsightManagementClient _hdiManagementClient;
            // Replace with your AAD tenant ID if necessary
            private const string TenantId = UserTokenProvider.CommonTenantId; 
            private const string SubscriptionId = "<Your Azure Subscription ID>";
            // This is the GUID for the PowerShell client. Used for interactive logins in this example.
            private const string ClientId = "1950a258-227b-4e31-a9cf-717495945fc2";

            static void Main(string[] args)
            {
                // Authenticate and get a token
                var authToken = Authenticate(TenantId, ClientId, SubscriptionId);
                // Flag subscription for HDInsight, if it isn't already.
                EnableHDInsight(authToken);
                // Get an HDInsight management client
                _hdiManagementClient = new HDInsightManagementClient(authToken);

                // insert code here

                System.Console.WriteLine("Press ENTER to continue");
                System.Console.ReadLine();
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
        }
    }

Est qu’une invite s’affiche lorsque vous exécutez ce programme.  Si vous ne voulez pas l’invite s’affiche, consultez [créer l’authentification non interactive HDInsight .NET applications](hdinsight-create-non-interactive-authentication-dotnet-applications.md).

##<a name="create-clusters"></a>Créer des clusters

Voir [basé sur Linux créer des groupes dans un HDInsight à l’aide du Kit de développement .NET](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md)

##<a name="list-clusters"></a>Liste clusters

L’extrait de code suivant répertorie les clusters et certaines propriétés :

    var results = _hdiManagementClient.Clusters.List();
    foreach (var name in results.Clusters) {
        Console.WriteLine("Cluster Name: " + name.Name);
        Console.WriteLine("\t Cluster type: " + name.Properties.ClusterDefinition.ClusterType);
        Console.WriteLine("\t Cluster location: " + name.Location);
        Console.WriteLine("\t Cluster version: " + name.Properties.ClusterVersion);
    }

##<a name="delete-clusters"></a>Supprimer les clusters

Utilisez l’extrait de code suivantes pour supprimer un cluster synchrone ou asynchrone : 

    _hdiManagementClient.Clusters.Delete("<Resource Group Name>", "<Cluster Name>");
    _hdiManagementClient.Clusters.DeleteAsync("<Resource Group Name>", "<Cluster Name>");
            
##<a name="scale-clusters"></a>Enverguez
Le cluster mise à l’échelle fonctionnalité permet de modifier le nombre de nœuds de travail utilisés par un cluster qui s’exécute dans Azure HDInsight sans avoir à recréer le cluster.

>[AZURE.NOTE] Clusters uniquement avec HDInsight version 3.1.3 ou version ultérieure sont prises en charge. Si vous n’êtes pas sûr de la version de votre cluster, vous pouvez vérifier la page Propriétés.  Voir la [liste et afficher clusters](hdinsight-administer-use-portal-linux.md#list-and-show-clusters).

L’impact de la modification du nombre de nœuds de données pour chaque type de cluster pris en charge par HDInsight :

- Hadoop

    Vous pouvez en toute transparence augmenter le nombre de nœuds de travail dans un cluster Hadoop qui s’exécute sans ayant un impact sur toutes les tâches en attente ou en cours d’exécution. Nouvelles tâches peuvent également être soumises lorsque l’opération est en cours. Échecs dans une opération de mise à l’échelle sont gérées correctement afin que le cluster est toujours vers la gauche dans un état de fonctionnalité.

    Lorsqu’un cluster Hadoop est mise à l’échelle en réduisant le nombre de nœuds de données vers le bas, certains des services dans le cluster redémarrage. Ainsi, en cours d’exécution et les tâches en attente échec à la fin de l’opération de mise à l’échelle. Vous pouvez, cependant, renvoyez les tâches une fois l’opération terminée.

- HBase

    Vous pouvez en toute transparence ajouter ou supprimer des nœuds à votre cluster HBase en cours d’exécution. Les serveurs régionaux sont réparties automatiquement après quelques minutes de fin de l’opération de mise à l’échelle. Toutefois, vous pouvez également manuellement équilibrer les serveurs régionaux en vous connectant à la headnode de cluster et exécutant les commandes suivantes dans une fenêtre d’invite de commandes :

        >pushd %HBASE_HOME%\bin
        >hbase shell
        >balancer

- Vague d'

    Vous pouvez en toute transparence ajouter ou supprimer des nœuds de données à votre cluster vague en cours d’exécution. Mais après la réussite de l’opération de mise à l’échelle, vous devrez rééquilibrer la topologie.

    Il est possible de rééquilibrage de deux façons :

    * Interface utilisateur du web vague
    * Outil de l’interface de ligne (commande)

    Reportez-vous à la [documentation de vague d’Apache](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) pour plus d’informations.

    L’interface utilisateur du web vague est disponible sur le cluster HDInsight :

    ![hdinsight vague échelle rééquilibrage](./media/hdinsight-administer-use-management-portal/hdinsight.portal.scale.cluster.storm.rebalance.png)

    Voici un exemple de l’utilisation de la commande de l’infrastructure du langage commun rééquilibrer la topologie vague :

        ## Reconfigure the topology "mytopology" to use 5 worker processes,
        ## the spout "blue-spout" to use 3 executors, and
        ## the bolt "yellow-bolt" to use 10 executors

        $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10

L’extrait de code suivant montre comment redimensionner un cluster synchrone ou asynchrone :

    _hdiManagementClient.Clusters.Resize("<Resource Group Name>", "<Cluster Name>", <New Size>);   
    _hdiManagementClient.Clusters.ResizeAsync("<Resource Group Name>", "<Cluster Name>", <New Size>);   
    

##<a name="grantrevoke-access"></a>Accès grant/revoke

HDInsight clusters comprennent les services web HTTP suivants (tous ces services ont des extrémités RESTful) :

- ODBC
- JDBC
- Ambari
- Oozie
- Templeton


Par défaut, ces services sont accordées pour l’accès. Vous pouvez révoquer/accorder l’accès. Pour révoquer :

    var httpParams = new HttpSettingsParameters
    {
        HttpUserEnabled = false,
        HttpUsername = "admin",
        HttpPassword = "*******",
    };
    _hdiManagementClient.Clusters.ConfigureHttpSettings("<Resource Group Name>, <Cluster Name>, httpParams);

Pour accorder :

    var httpParams = new HttpSettingsParameters
    {
        HttpUserEnabled = enable,
        HttpUsername = "admin",
        HttpPassword = "*******",
    };
    _hdiManagementClient.Clusters.ConfigureHttpSettings("<Resource Group Name>, <Cluster Name>, httpParams);


>[AZURE.NOTE] Par l’octroi/révoquer l’accès, vous allez réinitialiser le cluster nom utilisateur et mot de passe.

Il est également possible via le portail. Voir [Administrer HDInsight à l’aide du portail Azure][hdinsight-admin-portal].

##<a name="update-http-user-credentials"></a>Mettre à jour les informations d’identification utilisateur HTTP

Il s’agit de la même procédure que [Grant/revoke HTTP access](#grant/revoke-access). Si le cluster a été accordé l’accès par HTTP, vous devez tout d’abord annuler il.  Puis accorder le niveau d’accès avec les nouvelles informations d’identification utilisateur HTTP.


##<a name="find-the-default-storage-account"></a>Trouver le compte de stockage par défaut

L’extrait de code suivant montre comment obtenir le nom de compte de stockage par défaut et la clé de compte de stockage par défaut pour un cluster.

    var results = _hdiManagementClient.Clusters.GetClusterConfigurations(<Resource Group Name>, <Cluster Name>, "core-site");
    foreach (var key in results.Configuration.Keys)
    {
        Console.WriteLine(String.Format("{0} => {1}", key, results.Configuration[key]));
    }


##<a name="submit-jobs"></a>Soumettre des tâches

**Soumettre des travaux MapReduce**

Consultez [des exemples d’exécuter Hadoop MapReduce dans HDInsight](hdinsight-hadoop-run-samples-linux.md).

**Soumettre des travaux Hive** 

Voir [la ruche exécuter des requêtes à l’aide du Kit de développement .NET](hdinsight-hadoop-use-hive-dotnet-sdk.md).

**Soumettre des travaux cochon**

Voir [cochon exécuter des travaux à l’aide du Kit de développement .NET](hdinsight-hadoop-use-pig-dotnet-sdk.md).

**Soumettre des travaux Sqoop**

Voir [utiliser Sqoop avec HDInsight](hdinsight-hadoop-use-sqoop-dotnet-sdk.md).

**Soumettre des travaux Oozie**

Voir [Utiliser Oozie avec Hadoop pour définir et exécuter un flux de travail dans HDInsight](hdinsight-use-oozie-linux-mac.md).

##<a name="upload-data-to-azure-blob-storage"></a>Télécharger des données à Azure Blob storage
Voir [télécharger des données à HDInsight][hdinsight-upload-data].


## <a name="see-also"></a>Voir aussi
* [Documentation de référence HDInsight .NET SDK](https://msdn.microsoft.com/library/mt271028.aspx)
* [Administrer HDInsight à l’aide du portail Azure][hdinsight-admin-portal]
* [Administrer HDInsight à l’aide d’une interface de ligne de commande][hdinsight-admin-cli]
* [Créer des clusters HDInsight][hdinsight-provision]
* [Télécharger des données à HDInsight][hdinsight-upload-data]
* [Prise en main Azure HDInsight][hdinsight-get-started]


[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-provision-custom-options]: hdinsight-provision-clusters.md#configuration
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md

[hdinsight-admin-cli]: hdinsight-administer-use-command-line.md
[hdinsight-admin-portal]: hdinsight-administer-use-portal-linux.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-flight]: hdinsight-analyze-flight-delay-data.md


