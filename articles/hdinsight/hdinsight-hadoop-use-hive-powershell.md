<properties
   pageTitle="Utiliser Hadoop Hive avec PowerShell dans HDInsight | Microsoft Azure"
   description="Utiliser PowerShell pour exécuter des requêtes Hive dans Hadoop sur HDInsight."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"
    tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/07/2016"
   ms.author="larryfr"/>

#<a name="run-hive-queries-using-powershell"></a>Exécuter des requêtes Hive à l’aide de PowerShell

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

Ce document fournit un exemple de l’utilisation de PowerShell Azure dans le mode de groupe de ressources Azure pour exécuter des requêtes Hive dans un Hadoop sur cluster HDInsight.

> [AZURE.NOTE] Ce document ne fournit pas une description détaillée de ce que font les instructions HiveQL qui sont utilisées dans les exemples. Pour plus d’informations sur la HiveQL est utilisée dans cet exemple, voir [Utiliser la ruche avec Hadoop sur HDInsight](hdinsight-use-hive.md).


**Conditions préalables**

Pour effectuer les étapes décrites dans cet article, vous devez les éléments suivants.

- **Un cluster Azure HDInsight (Hadoop sur HDInsight) (Windows ou Linux)**
- **Un poste de travail avec Azure PowerShell**.

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

##<a name="run-hive-queries-using-azure-powershell"></a>Exécuter des requêtes Hive à l’aide de PowerShell Azure

Azure PowerShell fournit des *applets de commande* qui vous permettent d’effectuer à distance Hive requêtes sur HDInsight. En interne, cela revient à l’aide d’appels reste à [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (anciennement appelé Templeton) en cours d’exécution sur le cluster HDInsight.

Les applets de commande suivantes sont utilisées lors de l’exécution des requêtes Hive dans un cluster HDInsight distant :

* **Ajouter AzureRmAccount**: authentifie Azure PowerShell à votre abonnement Azure

* **Nouveau AzureRmHDInsightHiveJobDefinition**: crée une nouvelle *définition de travail* en utilisant les instructions HiveQL spécifiées

* **Démarrer AzureRmHDInsightJob**: envoie la définition de travail à HDInsight, commence la tâche et renvoie un objet de *travail* peut être utilisé pour vérifier l’état de la tâche

* **Attente AzureRmHDInsightJob**: utilise l’objet de travail pour vérifier l’état de la tâche. Il va attendre la fin de la tâche se termine ou dépasse de délai d’attente.

* **Get-AzureRmHDInsightJobOutput**: permet de récupérer le résultat de la tâche

* **Appeler AzureRmHDInsightHiveJob**: utilisés pour exécuter les instructions HiveQL. Cela se bloque la requête est terminée, puis renvoie le résultat

* **Utiliser AzureRmHDInsightCluster**: définit le cluster à utiliser pour la commande **Appeler AzureRmHDInsightHiveJob** en cours

Les étapes suivantes expliquent comment utiliser ces applets de commande pour exécuter une tâche dans votre cluster HDInsight :

1. À l’aide d’un éditeur, enregistrez le code suivant en tant que **hivejob.ps1**. Vous devez remplacer **NOMDUCLUSTER** par le nom de votre cluster HDInsight.

        #Specify the values
        $clusterName = "CLUSTERNAME"
        $creds=Get-Credential

        # Login to your Azure subscription
        # Is there an active Azure subscription?
        $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
        if(-not($sub))
        {
            Add-AzureRmAccount
        }

        #HiveQL
        #Note: set hive.execution.engine=tez; is not required for
        #      Linux-based HDInsight
        $queryString = "set hive.execution.engine=tez;" +
                    "DROP TABLE log4jLogs;" +
                    "CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';" +
                    "SELECT * FROM log4jLogs WHERE t4 = '[ERROR]';"

        #Create an HDInsight Hive job definition
        $hiveJobDefinition = New-AzureRmHDInsightHiveJobDefinition -Query $queryString 

        #Submit the job to the cluster
        Write-Host "Start the Hive job..." -ForegroundColor Green

        $hiveJob = Start-AzureRmHDInsightJob -ClusterName $clusterName -JobDefinition $hiveJobDefinition -ClusterCredential $creds

        #Wait for the Hive job to complete
        Write-Host "Wait for the job to complete..." -ForegroundColor Green
        Wait-AzureRmHDInsightJob -ClusterName $clusterName -JobId $hiveJob.JobId -ClusterCredential $creds

        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=(Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
        # Print the output
        Write-Host "Display the standard output..." -ForegroundColor Green
        Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $hiveJob.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds
            
2. Ouvrez une nouvelle invite **PowerShell Azure** . Accédez à l’emplacement du fichier **hivejob.ps1** , puis utilisez la commande suivante pour exécuter le script :

        .\hivejob.ps1

    Lorsque le script s’exécute, vous devrez entrer les informations d’identification du compte HTTPS / d’administration pour votre cluster. Vous pouvez également être invité à vous connecter à votre abonnement Azure.
    
7. Lorsque la tâche est terminée, elle doit renvoyer des informations similaires à ce qui suit :

        Display the standard output...
        2012-02-03      18:35:34        SampleClass0    [ERROR] incorrect       id
        2012-02-03      18:55:54        SampleClass1    [ERROR] incorrect       id
        2012-02-03      19:25:27        SampleClass4    [ERROR] incorrect       id

4. Comme mentionné précédemment, **Appeler-Hive** peut servir à exécuter une requête et attendre la réponse. Utilisez les commandes suivantes et remplacez **NOMDUCLUSTER** par le nom de votre cluster :

        Use-AzureRmHDInsightCluster -ClusterName $clusterName -HttpCredential $creds
        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=(Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
        $queryString = "set hive.execution.engine=tez;" +
                    "DROP TABLE log4jLogs;" +
                    "CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';" +
                    "SELECT * FROM log4jLogs WHERE t4 = '[ERROR]';"
        Invoke-AzureRmHDInsightHiveJob `
            -StatusFolder "statusout" `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -Query $queryString

    Le résultat se présente comme suit :

        2012-02-03  18:35:34    SampleClass0    [ERROR] incorrect   id
        2012-02-03  18:55:54    SampleClass1    [ERROR] incorrect   id
        2012-02-03  19:25:27    SampleClass4    [ERROR] incorrect   id

    > [AZURE.NOTE] Pour les requêtes HiveQL plus de temps, vous pouvez utiliser l’applet de commande PowerShell Azure **Ici chaînes** ou les fichiers de script HiveQL. L’extrait de code suivant montre comment utiliser l’applet de commande **Appeler-Hive** pour exécuter un fichier de script HiveQL. Le fichier de script HiveQL doit être téléchargé vers wasbs : / /.
    >
    > `Invoke-AzureRmHDInsightHiveJob -File "wasbs://<ContainerName>@<StorageAccountName>/<Path>/query.hql"`
    >
    > Pour plus d’informations sur les **Chaînes de cet emplacement**, reportez-vous <a href="http://technet.microsoft.com/library/ee692792.aspx" target="_blank">à l’aide de Windows PowerShell ici-chaînes</a>.

##<a name="troubleshooting"></a>Résolution des problèmes

Si aucune information n’est renvoyée lorsque la tâche se termine, une erreur peut se sont produites pendant le traitement. Pour afficher les informations d’erreur pour cette tâche, ajoutez le code suivant à la fin du fichier **hivejob.ps1** et enregistrez-le puis exécutez-le à nouveau.

    # Print the output of the Hive job.
    Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $job.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

Renvoie les informations écrites dans STDERR sur le serveur lorsque vous avez exécuté le travail, et elle peut aider à déterminer pourquoi le travail échoue.

##<a name="summary"></a>Résumé

Comme vous pouvez le voir, Azure PowerShell fournit un moyen facile pour exécuter des requêtes Hive dans un cluster de HDInsight, surveiller l’état du travail et récupérer la sortie.

##<a name="next-steps"></a>Étapes suivantes

Pour obtenir des informations générales sur Hive dans HDInsight :

* [Utiliser Hive avec Hadoop sur HDInsight](hdinsight-use-hive.md)

Pour plus d’informations sur les autres méthodes, vous pouvez travailler avec Hadoop sur HDInsight :

* [Utiliser cochon avec Hadoop sur HDInsight](hdinsight-use-pig.md)

* [Utiliser MapReduce avec Hadoop sur HDInsight](hdinsight-use-mapreduce.md)
