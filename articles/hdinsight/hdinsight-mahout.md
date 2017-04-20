<properties
    pageTitle="Générer des recommandations à l’aide de Mahout et fonctionnant sous WIndows HDInsight | Microsoft Azure"
    description="Découvrez comment utiliser l’ordinateur Apache Mahout bibliothèque d’apprentissage pour générer des recommandations vidéo avec fonctionnant sous Windows HDInsight (Hadoop)."
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/11/2016"
    ms.author="larryfr"/>

#<a name="generate-movie-recommendations-by-using-apache-mahout-with-hadoop-in-hdinsight"></a>Générer des recommandations de vidéo à l’aide de Apache Mahout avec Hadoop dans HDInsight

[AZURE.INCLUDE [mahout-selector](../../includes/hdinsight-selector-mahout.md)]

Apprenez à utiliser la machine [Mahout Apache](http://mahout.apache.org) bibliothèque avec Azure HDInsight d’apprentissage pour générer des recommandations vidéo.

> [AZURE.NOTE] Les étapes décrites dans ce document nécessitent un client Windows et un cluster HDInsight fonctionnant sous Windows. Pour plus d’informations sur l’utilisation de Mahout à partir d’un Linux, OS X ou client Unix, avec un cluster basé sur Linux HDInsight, voir [recommandations de film générer à l’aide de Mahout Apache avec Hadoop basé sur Linux dans HDInsight](hdinsight-hadoop-mahout-linux-mac.md)


##<a name="learn"></a>Enseignements de cet article

Mahout est une [machine d’apprentissage] [ ml] bibliothèque pour Apache Hadoop. Mahout contient des algorithmes pour le traitement des données, telles que le filtrage, classement et cluster. Dans cet article, vous allez utiliser un moteur de recommandation pour générer des recommandations vidéo basés sur films que vos amis ont vu. Vous allez également apprendre à effectuer les classifications avec une forêt décision. Cela sera vous apprend les éléments suivants :

* Comment faire pour exécuter des travaux Mahout à l’aide de Windows PowerShell

* L’exécution des travaux Mahout à partir de la ligne de commande Hadoop

* Comment installer Mahout sur clusters HDInsight 3.0 et HDInsight 2.0

    > [AZURE.NOTE] Mahout est fourni avec la version 3.1 HDInsight des groupes. Si vous utilisez une version antérieure de HDInsight, voir [Installer Mahout](#install) avant de poursuivre.

##<a name="prerequisites"></a>conditions préalables

- **Hadoop basé sur un Windows cluster dans HDInsight**. Pour plus d’informations sur la création d’une, voir [commencer à utiliser Hadoop dans HDInsight][getstarted]
- **Un poste de travail avec Azure PowerShell**.

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]


##<a name="recommendations"></a>Générer des recommandations à l’aide de Windows PowerShell

> [AZURE.NOTE] Bien que le travail utilisés dans cette section fonctionne à l’aide de Windows PowerShell, la plupart des classes fournies avec Mahout ne fonctionnent pas avec Windows PowerShell, et ils doivent être exécutés à l’aide de la ligne de commande Hadoop. Pour une liste des classes qui ne fonctionnent pas avec Windows PowerShell, consultez la section [résolution des problèmes](#troubleshooting) .
>
> Pour obtenir un exemple de l’utilisation de la ligne de commande Hadoop pour exécuter des travaux Mahout, voir [/classify des données à l’aide de la ligne de commande Hadoop](#classify).

Un des fonctions qui est fourni par Mahout est un moteur de recommandation. Ce moteur accepte les données dans le format de `userID`, `itemId`, et `prefValue` (la préférence sur les utilisateurs de l’élément). Mahout pouvez puis effectuer une analyse de co-création occurrence pour déterminer : _les utilisateurs qui ont une préférence pour un élément disposez également d’une préférence pour ces autres éléments_. Mahout détermine ensuite les utilisateurs grâce aux préférences élément semblable, qui peuvent servir à faire des recommandations.

Voici un exemple très simple qui utilise des vidéos :

* __Co-création occurrence__: Jean, Alice et Bob aime _étoile de fichiers_, _L’Empire avertissements précédent_et _revenir de la Jedi_. Mahout détermine que les utilisateurs qui aiment également l’une de ces vidéos comme les deux autres.

* __Co-création occurrence__: Bob et Alice aime également _La Menace fantôme_, _attaque des Clones_et _Vengeance de la Sith_. Mahout détermine que les utilisateurs qui ont aimé les trois films précédents également comme ces trois.

* __Recommandation similitudes entre__: Jean, car aime les trois premières films, Mahout examine que d’autres personnes grâce aux préférences similaires aime films, mais Jean n’a pas observé (aime/nominal). Dans ce cas, Mahout recommande _Le Menace fantôme_, _attaque des Clones_et _Vengeance de la Sith_.

###<a name="understanding-the-data"></a>Comprendre les données

Facilement, [Recherche GroupLens] [ movielens] fournit des données de l’évaluation des vidéos dans un format compatible avec Mahout. Ces données sont disponibles sur le stockage par défaut de votre cluster en `/HdiSamples/MahoutMovieData`.

Il existe deux fichiers, `moviedb.txt` (informations sur les films) et `user-ratings.txt`. Le fichier utilisateur ratings.txt est utilisé lors de l’analyse, tandis que moviedb.txt est utilisée pour fournir des informations de texte convivial lorsque vous affichez les résultats de l’analyse.

Les données contenues dans utilisateur ratings.txt possèdent une structure de `userID`, `movieID`, `userRating`, et `timestamp`, qui nous indique comment recommandé de chaque utilisateur avec une vidéo. Voici un exemple de données :


    196 242 3   881250949
    186 302 3   891717742
    22  377 1   878887116
    244 51  2   880606923
    166 346 1   886397596

###<a name="run-the-job"></a>Exécuter la tâche

Utiliser le script Windows PowerShell suivant pour exécuter une tâche qui utilise le moteur de recommandation Mahout avec les données de la vidéo :

    # The HDInsight cluster name.
    $clusterName = "the cluster name"
    
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
            
    # NOTE: The version number in the file path
    # may change in future versions of HDInsight.
    $jarFile =  "file:///C:/apps/dist/mahout-0.9.0.2.2.9.1-8/examples/target/mahout-examples-0.9.0.2.2.9.1-8-job.jar"
    #
    # If you are using an earlier version of HDInsight,
    # set $jarFile to the jar file you
    # uploaded.
    # For example,
    # $jarFile = "wasbs:///example/jars/mahout-core-0.9-job.jar"

    # The arguments for this job
    # * input - the path to the data uploaded to HDInsight
    # * output - the path to store output data
    # * tempDir - the directory for temp files
    $jobArguments = "--similarityClassname", "recommenditembased", `
                    "-s", "SIMILARITY_COOCCURRENCE", `
                    "--input", "wasbs:///HdiSamples/MahoutMovieData/user-ratings.txt",
                    "--output", "wasbs:///example/out",
                    "--tempDir", "wasbs:///example/temp"

    # Create the job definition
    $jobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
      -JarFile $jarFile `
      -ClassName "org.apache.mahout.cf.taste.hadoop.item.RecommenderJob" `
      -Arguments $jobArguments

    # Start the job
    $job = Start-AzureRmHDInsightJob `
        -ClusterName $clusterName `
        -JobDefinition $jobDefinition `
        -HttpCredential $creds

    # Wait on the job to complete
    Write-Host "Wait for the job to complete ..." -ForegroundColor Green
    Wait-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobId $job.JobId `
            -HttpCredential $creds
    # Download the output
    Get-AzureStorageBlobContent `
            -Blob example/out/part-r-00000 `
            -Container $container `
            -Destination output.txt `
            -Context $context
            
    # Write out any error information
    Write-Host "STDERR"
    Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $job.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

> [AZURE.NOTE] Tâches mahout ne suppriment pas de données temporaires sont créé pendant le traitement de la tâche. La `--tempDir` paramètre est spécifié dans le travail d’exemple pour isoler les fichiers temporaires dans un répertoire spécifique.

La tâche Mahout ne renvoie pas le résultat dans STDOUT. Au lieu de cela, il stocke dans le répertoire de sortie spécifié en tant que __composant-r-00000__. Le script télécharge ce fichier à __output.txt__ dans le répertoire actif sur votre poste de travail.

Voici un exemple du contenu de ce fichier :

    1   [234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
    2   [282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
    3   [284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
    4   [690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]

La première colonne contient la `userID`. Les valeurs contenues dans ' [ » et «] » sont `movieId`:`recommendationScore`.

###<a name="view-the-output"></a>Afficher les résultats

Bien que la sortie générée OK à utiliser dans une application, il n’est pas très lisible. Le `moviedb.txt` à partir du serveur peut être utilisée pour résoudre le `movieId` à un film nom, mais vous devez d’abord télécharger le fichier évaluations à partir du serveur en utilisant le script suivant :

    # The HDInsight cluster name.
    $clusterName = "the cluster name"
    
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
    #Download the files
    Get-AzureStorageBlobContent -blob "HdiSamples/MahoutMovieData/moviedb.txt" `
    -Container $container `
    -Destination moviedb.txt `
    -Context $context
    Get-AzureStorageBlobContent -blob "HdiSamples/MahoutMovieData/user-ratings.txt" `
    -Container $container `
    -Destination user-ratings.txt `
    -Context $context

Une fois que vous avez téléchargé les fichiers, utilisez le script PowerShell suivant pour afficher des recommandations dont le nom de la vidéo :

    <#
    .SYNOPSIS
        Displays recommendations for movies.
    .DESCRIPTION
        Displays recommendations generated by Mahout
        with HDInsight example in a human readable format.
    .EXAMPLE
        .\Show-Recommendation -userId 4
            -userDataFile "user-ratings.txt"
            -movieFile "moviedb.txt"
            -recommendationFile "output.txt"
    #>

    [CmdletBinding(SupportsShouldProcess = $true)]
    param(
        #The user ID
        [Parameter(Mandatory = $true)]
        [String]$userId,

        [Parameter(Mandatory = $true)]
        [String]$userDataFile,

        [Parameter(Mandatory = $true)]
        [String]$movieFile,

        [Parameter(Mandatory = $true)]
        [String]$recommendationFile
    )
    # Read movie ID & description into hash table
    Write-Host "Reading movies descriptions" -ForegroundColor Green
    $movieById = @{}
    foreach($line in Get-Content $movieFile)
    {
        $tokens = $line.Split("|")
        $movieById[$tokens[0]] = $tokens[1]
    }
    # Load movies user has already seen (rated)
    # into a hash table
    Write-Host "Reading rated movies" -ForegroundColor Green
    $ratedMovieIds = @{}
    foreach($line in Get-Content $userDataFile)
    {
        $tokens = $line.Split("`t")
        if($tokens[0] -eq $userId)
        {
            # Resolve the ID to the movie name
            $ratedMovieIds[$movieById[$tokens[1]]] = $tokens[2]
        }
    }
    # Read recommendations generated by Mahout
    Write-Host "Reading recommendations" -ForegroundColor Green
    $recommendations = @{}
    foreach($line in get-content $recommendationFile)
    {
        $tokens = $line.Split("`t")
        if($tokens[0] -eq $userId)
        {
            #Trim leading/treailing [] and split at ,
            $movieIdAndScores = $tokens[1].TrimStart("[").TrimEnd("]").Split(",")
            foreach($movieIdAndScore in $movieIdAndScores)
            {
                #Split at : and store title and score in a hash table
                $idAndScore = $movieIdAndScore.Split(":")
                $recommendations[$movieById[$idAndScore[0]]] = $idAndScore[1]
            }
            break
        }
    }

    Write-Host "Rated movies" -ForegroundColor Green
    Write-Host "---------------------------" -ForegroundColor Green
    $ratedFormat = @{Expression={$_.Name};Label="Movie";Width=40}, `
                   @{Expression={$_.Value};Label="Rating"}
    $ratedMovieIds | format-table $ratedFormat
    Write-Host "---------------------------" -ForegroundColor Green

    write-host "Recommended movies" -ForegroundColor Green
    Write-Host "---------------------------" -ForegroundColor Green
    $recommendationFormat = @{Expression={$_.Name};Label="Movie";Width=40}, `
                            @{Expression={$_.Value};Label="Score"}
    $recommendations | format-table $recommendationFormat

Voici un exemple d’exécuter le script :

    PS C:\> show-recommendation.ps1 -userId 4 -userDataFile .\user-ratings.txt -movieFile .\moviedb.txt -recommendationFile .\output.txt

La sortie doit ressembler à ce qui suit :

    Reading movies descriptions
    Reading rated movies
    Reading recommendations
    Rated movies
    ---------------------------
    Movie                                    Rating
    -----                                    ------
    Devil's Own, The (1997)                  1
    Alien: Resurrection (1997)               3
    187 (1997)                               2
    (lines ommitted)

    ---------------------------
    Recommended movies
    ---------------------------

    Movie                                    Score
    -----                                    -----
    Good Will Hunting (1997)                 4.6504064
    Swingers (1996)                          4.6862745
    Wings of the Dove, The (1997)            4.6666665
    People vs. Larry Flynt, The (1996)       4.834559
    Everyone Says I Love You (1996)          4.707071
    Secrets & Lies (1996)                    4.818182
    That Thing You Do! (1996)                4.75
    Grosse Pointe Blank (1997)               4.8235292
    Donnie Brasco (1997)                     4.6792455
    Lone Star (1996)                         4.7099237  

##<a name="classify"></a>Classer les données à l’aide de la ligne de commande Hadoop

Une des méthodes de classification disponibles avec Mahout consiste à créer une [forêt aléatoire][forest]. Il s’agit d’un processus en plusieurs étapes qui consiste à utiliser des données d’apprentissage pour générer arborescences de décision, qui sont ensuite utilisés pour classer les données. Cet exemple utilise la classe __org.apache.mahout.classifier.df.tools.Describe__ fournie par Mahout. Il doit être actuellement exécuté à l’aide de la ligne de commande Hadoop.

###<a name="load-the-data"></a>Charger les données

1. Téléchargez les fichiers suivants dans [le jeu de données NSL KDD](http://nsl.cs.unb.ca/NSL-KDD/).

  * [KDDTrain +. ARFF](http://nsl.cs.unb.ca/NSL-KDD/KDDTrain+.arff): le fichier de formation

  * [KDDTest +. ARFF](http://nsl.cs.unb.ca/NSL-KDD/KDDTest+.arff): les données de test

2. Ouvrez chaque fichier et supprimer les lignes dans la partie supérieure qui commencent par '@', , puis enregistrez les fichiers. Si celles-ci ne sont pas supprimées, vous recevrez des messages d’erreur lors de l’utilisation de ces données avec Mahout.

2. Téléchargez les fichiers de __données d’exemple__. Vous pouvez le faire en utilisant le script suivant. Remplacez __NOMDUCLUSTER__ par le nom du cluster HDInsight. Remplacez le nom de fichier par le nom fo le fichier à télécharger.

        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterName="CLUSTERNAME"
        $fileToUpload="FILENAME"
        $blobPath="example/data/FILENAME"
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
            
        Set-AzureStorageBlobContent `
            -File $fileToUpload `
            -Blob $blobPath `
            -Container $container `
            -Context $context

###<a name="run-the-job"></a>Exécuter la tâche

1. Cette tâche nécessite de la ligne de commande Hadoop. Activer le Bureau à distance pour le cluster HDInsight, puis vous y connecter en suivant les instructions à [se connecter à clusters HDInsight à l’aide de RDP](hdinsight-administer-use-management-portal.md#rdp).

3. Une fois connecté, utilisez l’icône de __ligne de commande Hadoop__ pour ouvrir la ligne de commande Hadoop :

    ![infrastructure du langage commun Hadoop][hadoopcli]

3. Utilisez la commande suivante pour générer le descripteur de fichier (__KDDTrain + .info__), qui utilise Mahout.

        hadoop jar "c:/apps/dist/mahout-0.9.0.2.2.9.1-8/examples/target/mahout-examples-0.9.0.2.2.9.1-8-job.jar" org.apache.mahout.classifier.df.tools.Describe -p "wasbs:///example/data/KDDTrain+.arff" -f "wasbs:///example/data/KDDTrain+.info" -d N 3 C 2 N C 4 N C 8 N 2 C 19 N L

    La `N 3 C 2 N C 4 N C 8 N 2 C 19 N L` décrit les attributs des données dans le fichier. Par exemple, L indique une étiquette.

4. Créer une forêt d’arborescences de décision à l’aide de la commande suivante :

        hadoop jar c:/apps/dist/mahout-0.9.0.2.2.9.1-8/examples/target/mahout-examples-0.9.0.2.2.9.1-8-job.jar org.apache.mahout.classifier.df.mapreduce.BuildForest -Dmapred.max.split.size=1874231 -d wasbs:///example/data/KDDTrain+.arff -ds wasbs:///example/data/KDDTrain+.info -sl 5 -p -t 100 -o nsl-forest

    Le résultat de cette opération est stocké dans le répertoire __nsl forêt__ , qui se trouve dans l’espace de stockage pour votre cluster HDInsight à __ wasbs://user/&lt;nom d’utilisateur > / nsl-forêt/nsl-forest.seq. La &lt;nom d’utilisateur > est le nom d’utilisateur que vous avez utilisé pour votre session Bureau à distance. Ce fichier n’est pas lisible par les utilisateurs.

5. Testez la forêt par classer le dataset __KDDTest + .arff__ . Utilisez la commande suivante :

        hadoop jar c:/apps/dist/mahout-0.9.0.2.2.9.1-8/examples/target/mahout-examples-0.9.0.2.2.9.1-8-job.jar org.apache.mahout.classifier.df.mapreduce.TestForest -i wasbs:///example/data/KDDTest+.arff -ds wasbs:///example/data/KDDTrain+.info -m nsl-forest -a -mr -o wasbs:///example/data/predictions

    Cette commande renvoie des informations récapitulatives sur le processus de classification semblable au suivant :

        14/07/02 14:29:28 INFO mapreduce.TestForest:

        =======================================================
        Summary
        -------------------------------------------------------
        Correctly Classified Instances          :      17560       77.8921%
        Incorrectly Classified Instances        :       4984       22.1079%
        Total Classified Instances              :      22544

        =======================================================
        Confusion Matrix
        -------------------------------------------------------
        a       b       <--Classified as
        9437    274      |  9711        a     = normal
        4710    8123     |  12833       b     = anomaly

        =======================================================
        Statistics
        -------------------------------------------------------
        Kappa                                       0.5728
        Accuracy                                   77.8921%
        Reliability                                53.4921%
        Reliability (standard deviation)            0.4933

  Cette tâche génère également un fichier situé dans __wasbs:///example/data/predictions/KDDTest+.arff.out__. Toutefois, ce fichier n’est pas lisible par les utilisateurs.

> [AZURE.NOTE] Tâches mahout ne pas remplacent les fichiers. Si vous voulez exécuter à nouveau ces tâches, vous devez supprimer les fichiers qui ont été créés par les tâches précédentes.

##<a name="troubleshooting"></a>Résolution des problèmes

###<a name="install"></a>Installer Mahout

Mahout est installé sur des clusters 3.1 HDInsight, et elle peut être installée manuellement sur clusters HDInsight 3.0 ou HDInsight 2.1 en procédant comme suit :

1. La version de Mahout à utiliser dépend de la version HDInsight de votre cluster. Vous pouvez trouver la version cluster en affichant les propriétés pour le cluster dans le portail Azure.

  * __Pour HDInsight 2.1__, vous pouvez télécharger un fichier JAR (Java Archive) qui contient [Mahout 0,9](http://repo2.maven.org/maven2/org/apache/mahout/mahout-core/0.9/mahout-core-0.9-job.jar).

  * __Pour HDInsight 3.0__, vous devez [créer Mahout à partir de la source de] [ build] et spécifier la version Hadoop fournie par HDInsight. Installer les conditions préalables répertoriées dans la page Créer, téléchargez le code source et puis utilisez la commande suivante pour créer les fichiers jar Mahout :

            mvn -Dhadoop2.version=2.2.0 -DskipTests clean package

        After the build completes, you can find the JAR file at __mahout\mrlegacy\target\mahout-mrlegacy-1.0-SNAPSHOT-job.jar__.

        > [AZURE.NOTE] Lorsque vous relâchez Mahout 1.0, vous devez être en mesure d’utiliser les packages prédéfinis avec HDInsight 3.0.

2. Téléchargez le fichier jar sur __exemple/bocaux__ dans l’espace de stockage par défaut pour votre cluster. Remplacez NOMDUCLUSTER dans le script suivant avec le nom de votre cluster HDInsight et nom de fichier par le chemin d’accès au fichier __mahout-cours-0,9-job.jar__ ...

        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterName = "CLUSTERNAME"
        $fileToUpload = "FILENAME"
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
            
        Set-AzureStorageBlobContent `
            -File $fileToUpload `
            -Blob "example/jars/mahout-core-0.9-job.jar" `
            -Container $container `
            -Context $context

###<a name="cannot-overwrite-files"></a>Ne peut pas remplacer les fichiers

Mahout travaux ne pas nettoyer les fichiers temporaires qui ont été créés pendant le traitement. En outre, les tâches remplacera pas un fichier de sortie existant.

Pour éviter les erreurs lors de l’exécution des travaux Mahout, supprimez les fichiers temporaires et de sortie entre les séries ou utiliser des noms uniques répertoire temporaire et de sortie.

###<a name="cannot-find-the-jar-file"></a>Impossible de trouver le fichier JAR

3.1 HDInsight clusters incluent Mahout. Le chemin d’accès et le nom inclure le numéro de version de Mahout est installé sur le cluster. L’exemple de script Windows PowerShell dans ce didacticiel utilise un chemin d’accès est valide à partir de novembre 2015, mais le numéro de version passera à l’avenir mises à jour à HDInsight. Pour déterminer le chemin d’accès actuel vers le fichier JAR Mahout pour votre cluster, utilisez la commande Windows PowerShell suivante et modifiez le script pour faire référence au chemin d’accès de fichier est renvoyée :

    Use-AzureRmHDInsightCluster -ClusterName $clusterName
    #Get the cluster info so we can get the resource group, storage, etc.
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=(Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
    Invoke-AzureRmHDInsightHiveJob `
            -StatusFolder "wasbs:///example/statusout" `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -Query '!${env:COMSPEC} /c dir /b /s ${env:MAHOUT_HOME}\examples\target\*-job.jar'

###<a name="nopowershell"></a>Classes qui ne fonctionnent pas avec Windows PowerShell

Tâches de mahout qui utilisent les classes suivantes retournent une variété de messages d’erreur s’ils sont utilisés à partir de Windows PowerShell :

* org.apache.mahout.utils.clustering.ClusterDumper
* org.apache.mahout.utils.SequenceFileDumper
* org.apache.mahout.utils.vectors.lucene.Driver
* org.apache.mahout.utils.vectors.arff.Driver
* org.apache.mahout.text.WikipediaToSequenceFile
* org.apache.mahout.clustering.streaming.tools.ResplitSequenceFiles
* org.apache.mahout.clustering.streaming.tools.ClusterQualitySummarizer
* org.apache.mahout.classifier.sgd.TrainLogistic
* org.apache.mahout.classifier.sgd.RunLogistic
* org.apache.mahout.classifier.sgd.TrainAdaptiveLogistic
* org.apache.mahout.classifier.sgd.ValidateAdaptiveLogistic
* org.apache.mahout.classifier.sgd.RunAdaptiveLogistic
* org.apache.mahout.classifier.sequencelearning.hmm.BaumWelchTrainer
* org.apache.mahout.classifier.sequencelearning.hmm.ViterbiEvaluator
* org.apache.mahout.classifier.sequencelearning.hmm.RandomSequenceGenerator
* org.apache.mahout.classifier.df.tools.Describe

Pour exécuter les travaux qui utilisent ces classes, connectez-vous au cluster HDInsight, puis exécutez les tâches à l’aide de la ligne de commande Hadoop. Pour obtenir un exemple, voir [données /classify à l’aide de la ligne de commande Hadoop](#classify) .

##<a name="next-steps"></a>Étapes suivantes

À présent que vous avez appris à utiliser Mahout, découvrez d’autres façons de travailler avec des données sur HDInsight :

* [Ruche avec HDInsight](hdinsight-use-hive.md)
* [Cochon avec HDInsight](hdinsight-use-pig.md)
* [MapReduce avec HDInsight](hdinsight-use-mapreduce.md)

[build]: http://mahout.apache.org/developers/buildingmahout.html
[aps]: ../powershell-install-configure.md
[movielens]: http://grouplens.org/datasets/movielens/
[100k]: http://files.grouplens.org/datasets/movielens/ml-100k.zip
[getstarted]: hdinsight-hadoop-linux-tutorial-get-started.md
[upload]: hdinsight-upload-data.md
[ml]: http://en.wikipedia.org/wiki/Machine_learning
[forest]: http://en.wikipedia.org/wiki/Random_forest
[management]: https://manage.windowsazure.com/
[enableremote]: ./media/hdinsight-mahout/enableremote.png
[connect]: ./media/hdinsight-mahout/connect.png
[hadoopcli]: ./media/hdinsight-mahout/hadoopcli.png
[tools]: https://github.com/Blackmist/hdinsight-tools
 
