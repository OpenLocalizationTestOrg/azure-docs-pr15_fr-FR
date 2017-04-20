<properties
    pageTitle="Utiliser Hadoop Sqoop dans HDInsight | Microsoft Azure"
    description="Découvrez comment utiliser HDInsight .NET SDK pour exécuter Sqoop d’importation et exportation entre un cluster Hadoop et une base de données SQL Azure."
    editor="cgronlun"
    manager="jhubbard"
    services="hdinsight"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
   ms.date="09/14/2016"
    ms.author="jgao"/>

#<a name="run-sqoop-jobs-using-net-sdk-for-hadoop-in-hdinsight"></a>Exécution de tâches Sqoop à l’aide du Kit de développement .NET pour Hadoop dans HDInsight

[AZURE.INCLUDE [sqoop-selector](../../includes/hdinsight-selector-use-sqoop.md)]

Apprenez à utiliser HDInsight .NET SDK pour exécuter des travaux de Sqoop dans HDInsight pour importer et exporter entre HDInsight cluster et base de données SQL Azure ou base de données SQL Server.

> [AZURE.NOTE] Les étapes décrites dans cet article peuvent être utilisés avec soit un cluster de HDInsight Windows ou Linux. Toutefois, ces instructions s’appliquent uniquement à partir d’un client Windows. Utilisez le sélecteur d’onglet en haut de cet article pour choisir d’autres méthodes.

###<a name="prerequisites"></a>Conditions préalables

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- **Hadoop A cluster dans HDInsight**. Voir [créer un cluster et base de données SQL](hdinsight-use-sqoop.md#create-cluster-and-sql-database).

## <a name="run-sqoop-using-net-sdk"></a>Exécuter Sqoop à l’aide du Kit de développement .NET

Le Kit de développement .NET HDInsight fournit des bibliothèques de client .NET, ce qui facilite la collaboration avec les clusters HDInsight à partir de .NET. Dans cette section, vous allez créer une application console c# pour exporter le hivesampletable à la table de base de données SQL que vous avez créée précédemment dans cette didacticiels.

**Soumettre un travail Sqoop**

1. Créer une application console c# dans Visual Studio.
2. À partir de la Console du Gestionnaire de Package Visual Studio, exécutez la commande Nuget suivante pour importer le package.

        Install-Package Microsoft.Azure.Management.HDInsight.Job
        
3. Utilisez le code suivant dans le fichier Program.cs :

        using System.Collections.Generic;
        using Microsoft.Azure.Management.HDInsight.Job;
        using Microsoft.Azure.Management.HDInsight.Job.Models;
        using Hyak.Common;
        
        namespace SubmitHDInsightJobDotNet
        {
            class Program
            {
                private static HDInsightJobManagementClient _hdiJobManagementClient;
        
                private const string ExistingClusterName = "<Your HDInsight Cluster Name>";
                private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
                private const string ExistingClusterUsername = "<Cluster Username>";
                private const string ExistingClusterPassword = "<Cluster User Password>";
        
                static void Main(string[] args)
                {
                    System.Console.WriteLine("The application is running ...");
        
                    var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
                    _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);
        
                    SubmitSqoopJob();
        
                    System.Console.WriteLine("Press ENTER to continue ...");
                    System.Console.ReadLine();
                }
        
                private static void SubmitSqoopJob()
                {
                    var sqlDatabaseServerName = "<SQLDatabaseServerName>";
                    var sqlDatabaseLogin = "<SQLDatabaseLogin>";
                    var sqlDatabaseLoginPassword = "<SQLDatabaseLoginPassword>";
                    var sqlDatabaseDatabaseName = "<DatabaseName>";
        
                    var tableName = "<TableName>";
                    var exportDir = "/tutorials/usesqoop/data";
        
                    // Connection string for using Azure SQL Database.
                    // Comment if using SQL Server
                    var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ".database.windows.net;user=" + sqlDatabaseLogin + "@" + sqlDatabaseServerName + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
                    // Connection string for using SQL Server.
                    // Uncomment if using SQL Server
                    //var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ";user=" + sqlDatabaseLogin + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
        
                    var parameters = new SqoopJobSubmissionParameters
                    {
                        Files = new List<string> { "/user/oozie/share/lib/sqoop/sqljdbc41.jar" }, // This line is required for Linux-based cluster.
                        Command = "export --connect " + connectionString + " --table " + tableName + "_mobile --export-dir " + exportDir + "_mobile --fields-terminated-by \\t -m 1"
                    };
        
                    System.Console.WriteLine("Submitting the Sqoop job to the cluster...");
                    var response = _hdiJobManagementClient.JobManagement.SubmitSqoopJob(parameters);
                    System.Console.WriteLine("Validating that the response is as expected...");
                    System.Console.WriteLine("Response status code is " + response.StatusCode);
                    System.Console.WriteLine("Validating the response object...");
                    System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
                }
            }
        }
        
4. Appuyez sur **F5** pour exécuter le programme. 

##<a name="limitations"></a>Limitations

* Exportation en bloc - basé sur Linux avec HDInsight, le connecteur Sqoop permet d’exporter des données vers Microsoft SQL Server ou de base de données SQL Azure ne prend pas en charge les insertions en bloc.

* Le traitement par lots - avec basé sur Linux HDInsight, lorsque vous utilisez la `-batch` commutateur lors de l’exécution d’insertions, Sqoop effectue plusieurs insertions au lieu du traitement par lots les opérations d’insertion.

##<a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à utiliser Sqoop. Pour plus d’informations, voir :

- [Utiliser Oozie avec HDInsight](hdinsight-use-oozie.md): action Sqoop utilisation dans un flux de travail Oozie.
- [Analyse des données de retard aérienne à l’aide de HDInsight](hdinsight-analyze-flight-delay-data.md): utiliser la ruche pour analyser aérienne retarder des données et ensuite utiliser Sqoop pour exporter des données dans une base de données SQL Azure.
- [Télécharger des données à HDInsight](hdinsight-upload-data.md): trouver d’autres méthodes pour le téléchargement des données à HDInsight/Azure Blob storage.


