<properties
   pageTitle="Utiliser Hadoop cochon avec PowerShell dans HDInsight | Microsoft Azure"
   description="Apprenez à soumettre des travaux cochon à un cluster Hadoop sur HDInsight à l’aide de PowerShell Azure."
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
   ms.date="10/11/2016"
   ms.author="larryfr"/>

#<a name="run-pig-jobs-using-powershell"></a>Exécuter des travaux cochon à l’aide de PowerShell

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

Ce document fournit un exemple d’utilisation de PowerShell Azure à soumettre des tâches cochon à un Hadoop sur cluster HDInsight. Cochon permet d’écrire MapReduce travaux en utilisant une langue (Latin cochon), les modèles des transformations de données, au lieu de mapper et réduire les fonctions.

> [AZURE.NOTE] Ce document ne fournit pas une description détaillée de ce que font les instructions cochon Latin utilisées dans les exemples. Pour plus d’informations sur le Latin cochon utilisés dans cet exemple, voir [Utiliser cochon avec Hadoop sur HDInsight](hdinsight-use-pig.md).

##<a id="prereq"></a>Conditions préalables

Pour effectuer les étapes décrites dans cet article, vous devez les éléments suivants.

- **Azure un abonnement**. Voir [Azure obtenir la version d’évaluation gratuite](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **Un poste de travail avec Azure PowerShell**.

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]


##<a id="powershell"></a>Exécuter des travaux cochon à l’aide de PowerShell

Azure PowerShell fournit des *applets de commande* qui vous permettent d’effectuer à distance des travaux cochon sur HDInsight. En interne, cela revient à l’aide d’appels reste à [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (anciennement appelé Templeton) en cours d’exécution sur le cluster HDInsight.

Les applets de commande suivantes sont utilisées lors de l’exécution des travaux cochon sur un cluster HDInsight distant :

* **Connexion AzureRmAccount**: authentifie Azure PowerShell pour votre abonnement Azure

* **Nouveau AzureRmHDInsightPigJobDefinition**: crée une nouvelle *définition de travail* en utilisant les instructions cochon Latin spécifiées

* **Démarrer AzureRmHDInsightJob**: envoie la définition de travail à HDInsight, commence la tâche et renvoie un objet de *travail* peut être utilisé pour vérifier l’état de la tâche

* **Attente AzureRmHDInsightJob**: utilise l’objet de travail pour vérifier l’état de la tâche. Il va attendre la fin de la tâche est terminée, ou le délai d’attente a été dépassé.

* **Get-AzureRmHDInsightJobOutput**: permet de récupérer le résultat de la tâche

Les étapes suivantes expliquent comment utiliser ces applets de commande pour exécuter une tâche sur votre cluster HDInsight.

1. À l’aide d’un éditeur, enregistrez le code suivant en tant que **pigjob.ps1**. Vous devez remplacer **NOMDUCLUSTER** par le nom de votre cluster HDInsight.

        #Login to your Azure subscription
        Login-AzureRmAccount
        #Get credentials for the admin/HTTPs account
        $creds = Get-Credential

        #Specify the cluster name
        $clusterName = "CLUSTERNAME"
        
        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName = $clusterInfo.DefaultStorageAccount.split('.')[0]
        $container = $clusterInfo.DefaultStorageContainer
        $storageAccountKey = (Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value

        #Store the Pig Latin into $QueryString
        $QueryString =  "LOGS = LOAD 'wasbs:///example/data/sample.log';" +
        "LEVELS = foreach LOGS generate REGEX_EXTRACT(`$0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;" +
        "FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;" +
        "GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;" +
        "FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;" +
        "RESULT = order FREQUENCIES by COUNT desc;" +
        "DUMP RESULT;"


        #Create a new HDInsight Pig Job definition
        $pigJobDefinition = New-AzureRmHDInsightPigJobDefinition `
            -Query $QueryString `
            -Arguments "-w"

        # Start the Pig job on the HDInsight cluster
        Write-Host "Start the Pig job ..." -ForegroundColor Green
        $pigJob = Start-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobDefinition $pigJobDefinition `
            -HttpCredential $creds

        # Wait for the Pig job to complete
        Write-Host "Wait for the Pig job to complete ..." -ForegroundColor Green
        Wait-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobId $pigJob.JobId `
            -HttpCredential $creds

        # Display the output of the Pig job.
        Write-Host "Display the standard output ..." -ForegroundColor Green
        Get-AzureRmHDInsightJobOutput `
            -ClusterName $clusterName `
            -JobId $pigJob.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds

2. Ouvrez une nouvelle invite de commande Windows PowerShell. Accédez à l’emplacement du fichier **pigjob.ps1** , puis utilisez la commande suivante pour exécuter le script :

        .\pigjob.ps1
        
    Vous devrez tout d’abord vous connecter à votre abonnement Azure. Ensuite, vous êtes invité pour le nom de compte HTTPs/administrateur et le mot de passe pour le cluster HDInsight.

7. Lorsque la tâche est terminée, elle doit renvoyer des informations similaires à ce qui suit :

        Start the Pig job ...
        Wait for the Pig job to complete ...
        Display the standard output ...
        (TRACE,816)
        (DEBUG,434)
        (INFO,96)
        (WARN,11)
        (ERROR,6)
        (FATAL,2)

##<a id="troubleshooting"></a>Résolution des problèmes

Si aucune information n’est renvoyée lorsque la tâche se termine, une erreur peut se sont produites pendant le traitement. Pour afficher les informations d’erreur pour cette tâche, ajoutez la commande suivante à la fin du fichier **pigjob.ps1** et enregistrez-le puis exécutez-le à nouveau.

    # Print the output of the Pig job.
    Write-Host "Display the standard error output ..." -ForegroundColor Green
    Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $pigJob.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

Ceci renverra les informations écrite dans STDERR sur le serveur lorsque vous avez exécuté le travail, et elle peut aider à déterminer pourquoi le travail échoue.

##<a id="summary"></a>Résumé

Comme vous pouvez le voir, Azure PowerShell fournit un moyen facile pour exécuter des travaux cochon sur un cluster de HDInsight, surveiller l’état du travail et récupérer la sortie.

##<a id="nextsteps"></a>Étapes suivantes

Pour obtenir des informations générales sur cochon dans HDInsight :

* [Utiliser cochon avec Hadoop sur HDInsight](hdinsight-use-pig.md)

Pour plus d’informations sur les autres méthodes, vous pouvez travailler avec Hadoop sur HDInsight :

* [Utiliser Hive avec Hadoop sur HDInsight](hdinsight-use-hive.md)

* [Utiliser MapReduce avec Hadoop sur HDInsight](hdinsight-use-mapreduce.md)
