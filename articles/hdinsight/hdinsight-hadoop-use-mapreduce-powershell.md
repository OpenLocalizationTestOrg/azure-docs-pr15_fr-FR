<properties
   pageTitle="Utiliser MapReduce et PowerShell avec Hadoop | Microsoft Azure"
   description="Découvrez comment utiliser PowerShell pour effectuer à distance des travaux MapReduce avec Hadoop sur HDInsight."
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
   ms.date="08/29/2016"
   ms.author="larryfr"/>

# <a name="run-mapreduce-jobs-with-hadoop-on-hdinsight-using-powershell"></a>Exécution des tâches MapReduce avec Hadoop sur HDInsight à l’aide de PowerShell

[AZURE.INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

Ce document fournit un exemple d’utilisation de PowerShell Azure pour exécuter une tâche MapReduce dans un Hadoop sur cluster HDInsight.

##<a id="prereq"></a>Conditions préalables

Pour effectuer les étapes décrites dans cet article, vous devez les éléments suivants :

- **Un cluster Azure HDInsight (Hadoop sur HDInsight) (Windows ou Linux)**

- **Un poste de travail avec Azure PowerShell**.

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

##<a id="powershell"></a>Exécuter une tâche MapReduce à l’aide de PowerShell Azure

Azure PowerShell fournit des *applets de commande* qui vous permettent d’effectuer à distance des travaux MapReduce sur HDInsight. En interne, cela revient à l’aide d’appels reste à [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (anciennement appelé Templeton) en cours d’exécution sur le cluster HDInsight.

Les applets de commande suivantes sont utilisées lors de l’exécution des travaux MapReduce dans un cluster HDInsight à distance.

* **Connexion AzureRmAccount**: authentifie Azure PowerShell à votre abonnement Azure

* **Nouveau AzureRmHDInsightMapReduceJobDefinition**: crée une nouvelle *définition de travail* en utilisant les informations MapReduce spécifiées

* **Démarrer AzureRmHDInsightJob**: envoie la définition de travail à HDInsight, commence la tâche et renvoie un objet de *travail* peut être utilisé pour vérifier l’état de la tâche

* **Attente AzureRmHDInsightJob**: utilise l’objet de travail pour vérifier l’état de la tâche. Il attend jusqu'à ce que la tâche se termine ou dépasse de délai d’attente.

* **Get-AzureRmHDInsightJobOutput**: permet de récupérer le résultat de la tâche

Les étapes suivantes expliquent comment utiliser ces applets de commande pour exécuter une tâche dans votre cluster HDInsight.

1. À l’aide d’un éditeur, enregistrez le code suivant en tant que **mapreducejob.ps1**. Vous devez remplacer **NOMDUCLUSTER** par le nom de votre cluster HDInsight.

        #Specify the values
        $clusterName = "CLUSTERNAME"
                
        # Login to your Azure subscription
        # Is there an active Azure subscription?
        $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
        if(-not($sub))
        {
            Login-AzureRmAccount
        }

        #Get HTTPS/Admin credentials for submitting the job later
        $creds = Get-Credential
        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=(Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value

        #Create a storage content and upload the file
        $context = New-AzureStorageContext `
            -StorageAccountName $storageAccountName `
            -StorageAccountKey $storageAccountKey
            
        #Define the MapReduce job
        #NOTE: If using an HDInsight 2.0 cluster, use hadoop-examples.jar instead.
        # -JarFile = the JAR containing the MapReduce application
        # -ClassName = the class of the application
        # -Arguments = The input file, and the output directory
        $wordCountJobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
            -JarFile "wasbs:///example/jars/hadoop-mapreduce-examples.jar" `
            -ClassName "wordcount" `
            -Arguments `
                "wasbs:///example/data/gutenberg/davinci.txt", `
                "wasbs:///example/data/WordCountOutput"

        #Submit the job to the cluster
        Write-Host "Start the MapReduce job..." -ForegroundColor Green
        $wordCountJob = Start-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobDefinition $wordCountJobDefinition `
            -HttpCredential $creds

        #Wait for the job to complete
        Write-Host "Wait for the job to complete..." -ForegroundColor Green
        Wait-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobId $wordCountJob.JobId `
            -HttpCredential $creds
        # Download the output
        Get-AzureStorageBlobContent `
            -Blob 'example/data/WordCountOutput/part-r-00000' `
            -Container $container `
            -Destination output.txt `
            -Context $context
        # Print the output
        Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $wordCountJob.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds
            
2. Ouvrez une nouvelle invite **PowerShell Azure** . Accédez à l’emplacement du fichier **mapreducejob.ps1** , puis utilisez la commande suivante pour exécuter le script :

        .\mapreducejob.ps1
    
    Lorsque vous exécutez le script, vous pouvez être invité à s’authentifier à votre abonnement Azure. Vous devrez également fournir le nom de compte HTTPS/administrateur et le mot de passe pour le cluster HDInsight.

3. Lorsque la tâche est terminée, vous devez recevoir sortie similaire à ce qui suit :

        Cluster         : CLUSTERNAME
        ExitCode        : 0
        Name            : wordcount
        PercentComplete : map 100% reduce 100%
        Query           :
        State           : Completed
        StatusDirectory : f1ed2028-afe8-402f-a24b-13cc17858097
        SubmissionTime  : 12/5/2014 8:34:09 PM
        JobId           : job_1415949758166_0071

    Cette sortie indique que le travail a réussi.

    > [AZURE.NOTE] Si **code de sortie** est une valeur différente de 0, consultez [résolution des problèmes](#troubleshooting).

    Cet exemple stocke également les fichiers téléchargés vers un fichier **output.txt** dans le répertoire que vous exécutez le script à partir de.

###<a name="view-output"></a>Sortie d’affichage

Ouvrez le fichier **output.txt** dans un éditeur de texte pour afficher les mots et les nombres obtenus par la tâche.

> [AZURE.NOTE] Les fichiers de sortie d’une tâche MapReduce sont immuables. Donc si vous exécutez à nouveau cet exemple, vous devez modifier le nom du fichier de sortie.

##<a id="troubleshooting"></a>Résolution des problèmes

Si aucune information n’est renvoyée lorsque la tâche se termine, une erreur peut se sont produites pendant le traitement. Pour afficher les informations d’erreur pour cette tâche, ajoutez la commande suivante à la fin du fichier **mapreducejob.ps1** et enregistrez-le puis exécutez-le à nouveau.

    # Print the output of the WordCount job.
    Write-Host "Display the standard output ..." -ForegroundColor Green
    Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $wordCountJob.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

Renvoie les informations écrite dans STDERR sur le serveur lorsque vous avez exécuté le travail, et elle peut aider à déterminer pourquoi le travail échoue.

##<a id="summary"></a>Résumé

Comme vous pouvez le voir, Azure PowerShell fournit un moyen facile pour exécuter des travaux MapReduce sur un cluster de HDInsight, surveiller l’état du travail et récupérer la sortie.

##<a id="nextsteps"></a>Étapes suivantes

Pour obtenir des informations générales sur les tâches MapReduce dans HDInsight :

* [Utiliser MapReduce sur HDInsight Hadoop](hdinsight-use-mapreduce.md)

Pour plus d’informations sur les autres méthodes, vous pouvez travailler avec Hadoop sur HDInsight :

* [Utiliser Hive avec Hadoop sur HDInsight](hdinsight-use-hive.md)

* [Utiliser cochon avec Hadoop sur HDInsight](hdinsight-use-pig.md)
