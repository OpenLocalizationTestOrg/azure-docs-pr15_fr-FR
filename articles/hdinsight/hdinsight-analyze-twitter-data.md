<properties
    pageTitle="Analyser des données Twitter avec Hadoop dans HDInsight | Microsoft Azure"
    description="Découvrez comment utiliser Hive pour analyser des données Twitter sur Hadoop dans HDInsight pour trouver la fréquence d’utilisation d’un mot spécifique."
    services="hdinsight"
    documentationCenter=""
    authors="mumian"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/19/2016"
    ms.author="jgao"/>

# <a name="analyze-twitter-data-using-hive-in-hdinsight"></a>Analyser des données Twitter avec Hive dans HDInsight

Sites Web sociaux sont parmi les forces positives principales d’adoption de données volumineuses. API publics fournis par sites semblables Twitter sont une source de données pour l’analyse et la compréhension des tendances populaires utile. Dans ce didacticiel, vous sera obtenir tweets à l’aide d’un Twitter API de diffusion en continu, puis utiliser Apache ruche sur Azure HDInsight pour obtenir une liste d’utilisateurs Twitter qui vous a envoyé les tweets la plupart des contenant un certain mot.

> [AZURE.NOTE] Les étapes décrites dans ce document nécessitent un cluster HDInsight fonctionnant sous Windows. Les procédures spécifiques d’un cluster Linux, voir [analyser Twitter des données à l’aide de Hive dans HDInsight (Linux)](hdinsight-analyze-twitter-data-linux.md).


##<a name="prerequisites"></a>Conditions préalables

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- **Un poste de travail** avec Azure PowerShell installé et configuré. 

    Pour exécuter des scripts Windows PowerShell, vous devez exécuter Azure PowerShell en tant qu’administrateur et définissez la stratégie d’exécution sur *RemoteSigned*. Voir [scripts exécuter Windows PowerShell][powershell-script].

    Avant l’exécution de scripts Windows PowerShell, vérifiez que vous êtes connecté à votre abonnement Azure à l’aide de l’applet de commande suivante :

        Login-AzureRmAccount

    Si vous avez plusieurs abonnements Azure, utilisez l’applet de commande suivante pour définir l’abonnement actif :

        Select-AzureRmSubscription -SubscriptionID <Azure Subscription ID>

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

- **Cluster un Azure HDInsight**. Pour obtenir des instructions sur la mise en service de cluster, voir [prise en main à l’aide de HDInsight] [ hdinsight-get-started] ou [groupes de mise en service HDInsight] [hdinsight-provision]. Vous devez le nom du cluster plus loin dans le didacticiel.

Le tableau suivant répertorie les fichiers utilisés dans ce didacticiel :

Fichiers|Description
---|---
/Tutorials/Twitter/Data/tweets.txt|Les données sources pour le travail Hive.
/Tutorials/Twitter/Output|Le dossier de sortie pour le travail Hive. Le nom de fichier par défaut Hive travail sortie est **000000_0**.
Tutorials/Twitter/Twitter.hql|Le fichier de script HiveQL.
/Tutorials/Twitter/JobStatus|L’état du travail Hadoop.


##<a name="get-twitter-feed"></a>Obtenir Twitter flux

Dans ce didacticiel, vous allez utiliser la [Twitter API de flux][twitter-streaming-api]. La spécifique Twitter API vous allez utiliser la diffusion est [statuts ou un filtre][twitter-statuses-filter].

>[AZURE.NOTE] Un fichier contenant les 10 000 tweets et le fichier de script Hive (décrites dans la section suivante) ont été téléchargés dans un conteneur Blob public. Vous pouvez ignorer cette section si vous voulez utiliser les fichiers téléchargés.

[Données tweets](https://dev.twitter.com/docs/platform-objects/tweets) sont stockées dans le format de la Notation JSON (JavaScript Object) qui contient une structure complexe imbriquée. Au lieu de la rédaction de nombreuses lignes de code en utilisant un langage de programmation classique, vous pouvez transformer cette structure imbriquée dans une table Hive, afin qu’il peut être interrogé par un langage SQL (Structured Query)-comme langage appelé HiveQL.

Twitter utilise OAuth pour fournir un accès autorisé à son API. OAuth est un protocole qui permet aux utilisateurs d’approuver les applications d’agir en leur nom sans partager son mot de passe. Plus d’informations sont accessibles au [oauth.net](http://oauth.net/) ou dans le [Guide du débutant OAuth](http://hueniverse.com/oauth/) excellente à partir de Hueniverse.

La première étape pour utiliser OAuth consiste à créer une nouvelle application sur le site des développeurs Twitter.

**Pour créer une application Twitter**

1. Se connecter à [https://apps.twitter.com/](https://apps.twitter.com/). Cliquez sur le lien **s’inscrire maintenant** si vous n’avez pas un compte Twitter.
2. Cliquez sur **créer une nouvelle application**.
3. Entrez le **nom**, **Description**, **site Web**. Vous pouvez rendre une URL pour le champ **site Web** . Le tableau suivant montre quelques exemples de valeurs à utiliser :

    Champ|Valeur
    ---|---
    Nom|MyHDInsightApp
    Description|MyHDInsightApp
    Site Web|http://www.myhdinsightapp.com

4. Consultez **Oui, j’accepte**et puis cliquez sur **créer votre application Twitter**.
5. Cliquez sur l’onglet **autorisations** . L’autorisation par défaut est **en lecture seule**. Ceci est suffisant pour ce didacticiel.
6. Cliquez sur l’onglet **clés et des jetons d’accès** .
7. Cliquez sur **créer mon jeton d’accès**.
8. Cliquez sur **Test OAuth** dans le coin supérieur droit de la page.
9. Prenez note de **clé de consommateur**, **consommateur secret**, **jeton d’accès**et **secret jeton d’accès**. Vous aurez besoin des valeurs plus loin dans le didacticiel.

Dans ce didacticiel, vous allez utiliser Windows PowerShell pour appeler le service web. Pour un échantillon .NET c#, voir [analyse en temps réel Twitter opinion avec HBase dans HDInsight][hdinsight-hbase-twitter-sentiment]. L’outil autres populaires pour émettre des appels de service web est [*coin*][curl]. Curl peut être téléchargé à partir [d’ici][curl-download].

>[AZURE.NOTE] Lorsque vous utilisez la commande curl dans Windows, utilisez des guillemets doubles au lieu de guillemets simples pour les valeurs d’option.

**Pour obtenir tweets**

1. Ouvrez le Windows PowerShell script environnement intégré (ISE). (Dans l’écran de démarrage de Windows 8, tapez **PowerShell_ISE** , puis cliquez sur **Windows PowerShell ISE**. Voir [démarrage de Windows PowerShell sur Windows 8 et Windows][powershell-start].)

2. Copiez le script suivant dans la fenêtre de script :

        #region - variables and constants
        $clusterName = "<HDInsightClusterName>" # Enter the HDInsight cluster name

        # Enter the OAuth information for your Twitter application
        $oauth_consumer_key = "<TwitterAppConsumerKey>";
        $oauth_consumer_secret = "<TwitterAppConsumerSecret>";
        $oauth_token = "<TwitterAppAccessToken>";
        $oauth_token_secret = "<TwitterAppAccessTokenSecret>";

        $destBlobName = "tutorials/twitter/data/tweets.txt" # This script saves the tweets into this blob.

        $trackString = "Azure, Cloud, HDInsight" # This script gets the tweets containing these keywords.
        $track = [System.Uri]::EscapeDataString($trackString);
        $lineMax = 10000  # The script will get this number of tweets. It is about 3 minutes every 100 lines.
        #endregion

        #region - Connect to Azure subscription
        Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
        Login-AzureRmAccount
        #endregion

        #region - Create a block blob object for writing tweets into Blob storage
        Write-Host "Get the default storage account name and Blob container name using the cluster name ..." -ForegroundColor Green
        $myCluster = Get-AzureRmHDInsightCluster -Name $clusterName
        $resourceGroupName = $myCluster.ResourceGroup
        $storageAccountName = $myCluster.DefaultStorageAccount.Replace(".blob.core.windows.net", "")
        $containerName = $myCluster.DefaultStorageContainer
        Write-Host "`tThe storage account name is $storageAccountName." -ForegroundColor Yellow
        Write-Host "`tThe blob container name is $containerName." -ForegroundColor Yellow

        Write-Host "Define the Azure storage connection string ..." -ForegroundColor Green
        $storageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName)[0].Value
        $storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$storageAccountName;AccountKey=$storageAccountKey"
        Write-Host "`tThe connection string is $storageConnectionString." -ForegroundColor Yellow

        Write-Host "Create block blob object ..." -ForegroundColor Green
        $storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
        $storageClient = $storageAccount.CreateCloudBlobClient();
        $storageContainer = $storageClient.GetContainerReference($containerName)
        $destBlob = $storageContainer.GetBlockBlobReference($destBlobName)
        #end region

        # region - Format OAuth strings
        Write-Host "Format oauth strings ..." -ForegroundColor Green
        $oauth_nonce = [System.Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes([System.DateTime]::Now.Ticks.ToString()));
        $ts = [System.DateTime]::UtcNow - [System.DateTime]::ParseExact("01/01/1970", "dd/MM/yyyy", $null)
        $oauth_timestamp = [System.Convert]::ToInt64($ts.TotalSeconds).ToString();

        $signature = "POST&";
        $signature += [System.Uri]::EscapeDataString("https://stream.twitter.com/1.1/statuses/filter.json") + "&";
        $signature += [System.Uri]::EscapeDataString("oauth_consumer_key=" + $oauth_consumer_key + "&");
        $signature += [System.Uri]::EscapeDataString("oauth_nonce=" + $oauth_nonce + "&");
        $signature += [System.Uri]::EscapeDataString("oauth_signature_method=HMAC-SHA1&");
        $signature += [System.Uri]::EscapeDataString("oauth_timestamp=" + $oauth_timestamp + "&");
        $signature += [System.Uri]::EscapeDataString("oauth_token=" + $oauth_token + "&");
        $signature += [System.Uri]::EscapeDataString("oauth_version=1.0&");
        $signature += [System.Uri]::EscapeDataString("track=" + $track);

        $signature_key = [System.Uri]::EscapeDataString($oauth_consumer_secret) + "&" + [System.Uri]::EscapeDataString($oauth_token_secret);

        $hmacsha1 = new-object System.Security.Cryptography.HMACSHA1;
        $hmacsha1.Key = [System.Text.Encoding]::ASCII.GetBytes($signature_key);
        $oauth_signature = [System.Convert]::ToBase64String($hmacsha1.ComputeHash([System.Text.Encoding]::ASCII.GetBytes($signature)));

        $oauth_authorization = 'OAuth ';
        $oauth_authorization += 'oauth_consumer_key="' + [System.Uri]::EscapeDataString($oauth_consumer_key) + '",';
        $oauth_authorization += 'oauth_nonce="' + [System.Uri]::EscapeDataString($oauth_nonce) + '",';
        $oauth_authorization += 'oauth_signature="' + [System.Uri]::EscapeDataString($oauth_signature) + '",';
        $oauth_authorization += 'oauth_signature_method="HMAC-SHA1",'
        $oauth_authorization += 'oauth_timestamp="' + [System.Uri]::EscapeDataString($oauth_timestamp) + '",'
        $oauth_authorization += 'oauth_token="' + [System.Uri]::EscapeDataString($oauth_token) + '",';
        $oauth_authorization += 'oauth_version="1.0"';

        $post_body = [System.Text.Encoding]::ASCII.GetBytes("track=" + $track);
        #endregion

        #region - Read tweets
        Write-Host "Create HTTP web request ..." -ForegroundColor Green
        [System.Net.HttpWebRequest] $request = [System.Net.WebRequest]::Create("https://stream.twitter.com/1.1/statuses/filter.json");
        $request.Method = "POST";
        $request.Headers.Add("Authorization", $oauth_authorization);
        $request.ContentType = "application/x-www-form-urlencoded";
        $body = $request.GetRequestStream();

        $body.write($post_body, 0, $post_body.length);
        $body.flush();
        $body.close();
        $response = $request.GetResponse() ;

        Write-Host "Start stream reading ..." -ForegroundColor Green

        Write-Host "Define a MemoryStream and a StreamWriter for writing ..." -ForegroundColor Green
        $memStream = New-Object System.IO.MemoryStream
        $writeStream = New-Object System.IO.StreamWriter $memStream

        $sReader = New-Object System.IO.StreamReader($response.GetResponseStream())

        $inrec = $sReader.ReadLine()
        $count = 0
        while (($inrec -ne $null) -and ($count -le $lineMax))
        {
            if ($inrec -ne "")
            {
                Write-Host "`n`t $count tweets received." -ForegroundColor Yellow

                $writeStream.WriteLine($inrec)
                $count ++
            }

            $inrec=$sReader.ReadLine()
        }
        #endregion

        #region - Write tweets to Blob storage
        Write-Host "Write to the destination blob ..." -ForegroundColor Green
        $writeStream.Flush()
        $memStream.Seek(0, "Begin")
        $destBlob.UploadFromStream($memStream)

        $sReader.close()
        #endregion

        Write-Host "Completed!" -ForegroundColor Green

3. Définir le premier cinq à huit variables dans le script :


    Variable|Description
    ---|---
    $clusterName|Il s’agit du nom du cluster HDInsight où vous souhaitez exécuter l’application.
    oauth_consumer_key $|Il s’agit de la d’application Twitter **clé de consommateur** que vous avez noté précédemment lorsque vous avez créé l’application Twitter.
    oauth_consumer_secret $|Il s’agit le d’application Twitter **secret consommateur** que vous avez noté précédemment.
    oauth_token $|Il s’agit d' application Twitter **jeton d’accès** que vous avez saisie précédemment.
    oauth_token_secret $|Il s’agit de la d’application Twitter **secret jeton d’accès** que vous avez saisie précédemment.
    $destBlobName|Il s’agit du nom du blob de sortie. La valeur par défaut est **tutorials/twitter/data/tweets.txt**. Si vous changez la valeur par défaut, vous devrez mettre à jour les scripts Windows PowerShell en conséquence.
    $trackString|Le service web renverra tweet associés à ces mots clés. La valeur par défaut est **Azure, Cloud, HDInsight**. Si vous changez la valeur par défaut, vous met à jour les scripts Windows PowerShell en conséquence.
    $lineMax|La valeur détermine combien tweet lira le script. Il permet d’accéder à lire 100 tweet environ trois minutes. Vous pouvez définir un plus grand nombre, mais il faudra plus de temps à télécharger.

5. Appuyez sur **F5** pour exécuter le script. Si vous rencontrez des problèmes, pour résoudre ce problème, sélectionnez toutes les lignes et appuyez sur la touche **F8**.
6. S’affiche pas « Terminé ! » à la fin de la sortie. Tous les messages d’erreur seront affichera en rouge.

Comme une procédure de validation, vous pouvez vérifier le fichier de copie, **/tutorials/twitter/data/tweets.txt**, sur le stockage Blob Azure à l’aide de stockage Azure explorer ou PowerShell Azure. Pour un exemple de script Windows PowerShell pour la liste des fichiers, voir [le stockage Blob utilisation avec HDInsight][hdinsight-storage-powershell].



##<a name="create-hiveql-script"></a>Créer un script HiveQL

À l’aide de PowerShell Azure, vous pouvez exécuter plusieurs instructions HiveQL une à la fois, ou l’instruction HiveQL dans un fichier de script du package. Dans ce didacticiel, vous allez créer un script HiveQL. Le fichier de script doit être téléchargé vers le stockage Blob Azure. Dans la section suivante, vous allez exécuter le fichier de script à l’aide de PowerShell Azure.

>[AZURE.NOTE] Le fichier de script Hive et un fichier contenant les 10 000 tweet ont été téléchargés dans un conteneur Blob public. Vous pouvez ignorer cette section si vous voulez utiliser les fichiers téléchargés.

Le script HiveQL effectue les éléments suivants :

1. **Supprimer la table tweets_raw** dans les cas, la table existe déjà.
2. **Créer une table Hive tweets_raw**. Ce tableau structuré Hive temporaire conserve les données pour extraire supplémentaire, de transformation et de chargement traitement (ETL). Pour plus d’informations sur les partitions, voir [didacticiel la ruche][apache-hive-tutorial].  
3. **Charger les données** à partir du dossier source, /tutorials/twitter/data. Le jeu de données volumineux tweet au format JSON imbriquée a maintenant transformée en une structure de table Hive temporaire.
3. **Supprimer la table tweet** cas, la table existe déjà.
4. **Créer la table tweets**. Avant de pouvoir interroger contre le dataset tweets à l’aide de Hive, vous devez exécuter un autre processus ETL. Ce processus ETL définit un schéma plus détaillé de la table pour les données stockées dans la table « twitter_raw ».  
5. **Insérer un tableau de remplacement**. Ce script Hive complexe sera démarrer un ensemble de tâches MapReduce long par le cluster Hadoop. En fonction de votre groupe de données et la taille de votre cluster, cela peut prendre environ 10 minutes.
6. **Insertion écraser directory**. Exécuter une requête et le jeu de données dans un fichier de sortie. Cette requête renvoie une liste des utilisateurs Twitter qui vous a envoyé la plupart des tweets contenant le mot « Azure ».

**Pour créer un script Hive et téléchargez-le sur Azure**

1. Ouvrez Windows PowerShell ISE.
2. Copiez le script suivant dans la fenêtre de script :

        #region - variables and constants
        $clusterName = "<Existing HDInsight Cluster Name>" # Enter your HDInsight cluster name
        $subscriptionID = "<Azure Subscription ID>"
        
        $sourceDataPath = "/tutorials/twitter/data"
        $outputPath = "/tutorials/twitter/output"
        $hqlScriptFile = "tutorials/twitter/twitter.hql"
        
        $hqlStatements = @"
        set hive.exec.dynamic.partition = true;
        set hive.exec.dynamic.partition.mode = nonstrict;
        
        DROP TABLE tweets_raw;
        CREATE EXTERNAL TABLE tweets_raw (
            json_response STRING
        )
        STORED AS TEXTFILE LOCATION '$sourceDataPath';
        
        DROP TABLE tweets;
        CREATE TABLE tweets
        (
            id BIGINT,
            created_at STRING,
            created_at_date STRING,
            created_at_year STRING,
            created_at_month STRING,
            created_at_day STRING,
            created_at_time STRING,
            in_reply_to_user_id_str STRING,
            text STRING,
            contributors STRING,
            retweeted STRING,
            truncated STRING,
            coordinates STRING,
            source STRING,
            retweet_count INT,
            url STRING,
            hashtags array<STRING>,
            user_mentions array<STRING>,
            first_hashtag STRING,
            first_user_mention STRING,
            screen_name STRING,
            name STRING,
            followers_count INT,
            listed_count INT,
            friends_count INT,
            lang STRING,
            user_location STRING,
            time_zone STRING,
            profile_image_url STRING,
            json_response STRING
        );
        
        FROM tweets_raw
        INSERT OVERWRITE TABLE tweets
        SELECT
            cast(get_json_object(json_response, '$.id_str') as BIGINT),
            get_json_object(json_response, '$.created_at'),
            concat(substr (get_json_object(json_response, '$.created_at'),1,10),' ',
            substr (get_json_object(json_response, '$.created_at'),27,4)),
            substr (get_json_object(json_response, '$.created_at'),27,4),
            case substr (get_json_object(json_response, '$.created_at'),5,3)
                when "Jan" then "01"
                when "Feb" then "02"
                when "Mar" then "03"
                when "Apr" then "04"
                when "May" then "05"
                when "Jun" then "06"
                when "Jul" then "07"
                when "Aug" then "08"
                when "Sep" then "09"
                when "Oct" then "10"
                when "Nov" then "11"
                when "Dec" then "12" end,
            substr (get_json_object(json_response, '$.created_at'),9,2),
            substr (get_json_object(json_response, '$.created_at'),12,8),
            get_json_object(json_response, '$.in_reply_to_user_id_str'),
            get_json_object(json_response, '$.text'),
            get_json_object(json_response, '$.contributors'),
            get_json_object(json_response, '$.retweeted'),
            get_json_object(json_response, '$.truncated'),
            get_json_object(json_response, '$.coordinates'),
            get_json_object(json_response, '$.source'),
            cast (get_json_object(json_response, '$.retweet_count') as INT),
            get_json_object(json_response, '$.entities.display_url'),
            array(
                trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
                trim(lower(get_json_object(json_response, '$.entities.hashtags[1].text'))),
                trim(lower(get_json_object(json_response, '$.entities.hashtags[2].text'))),
                trim(lower(get_json_object(json_response, '$.entities.hashtags[3].text'))),
                trim(lower(get_json_object(json_response, '$.entities.hashtags[4].text')))),
            array(
                trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
                trim(lower(get_json_object(json_response, '$.entities.user_mentions[1].screen_name'))),
                trim(lower(get_json_object(json_response, '$.entities.user_mentions[2].screen_name'))),
                trim(lower(get_json_object(json_response, '$.entities.user_mentions[3].screen_name'))),
                trim(lower(get_json_object(json_response, '$.entities.user_mentions[4].screen_name')))),
            trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
            trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
            get_json_object(json_response, '$.user.screen_name'),
            get_json_object(json_response, '$.user.name'),
            cast (get_json_object(json_response, '$.user.followers_count') as INT),
            cast (get_json_object(json_response, '$.user.listed_count') as INT),
            cast (get_json_object(json_response, '$.user.friends_count') as INT),
            get_json_object(json_response, '$.user.lang'),
            get_json_object(json_response, '$.user.location'),
            get_json_object(json_response, '$.user.time_zone'),
            get_json_object(json_response, '$.user.profile_image_url'),
            json_response
        WHERE (length(json_response) > 500);
        
        INSERT OVERWRITE DIRECTORY '$outputPath'
        SELECT name, screen_name, count(1) as cc
            FROM tweets
            WHERE text like "%Azure%"
            GROUP BY name,screen_name
            ORDER BY cc DESC LIMIT 10;
        "@
        #endregion
        
        #region - Connect to Azure subscription
        Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
        
        Try{
            Get-AzureRmSubscription
        }
        Catch{
            Login-AzureRmAccount
        }
        
        Select-AzureRmSubscription -SubscriptionId $subscriptionID
        
        #endregion
        
        #region - Create a block blob object for writing the Hive script file
        Write-Host "Get the default storage account name and container name based on the cluster name ..." -ForegroundColor Green
        $myCluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroupName = $myCluster.ResourceGroup
        $defaultStorageAccountName = $myCluster.DefaultStorageAccount.Replace(".blob.core.windows.net", "")
        $defaultBlobContainerName = $myCluster.DefaultStorageContainer
        Write-Host "`tThe storage account name is $defaultStorageAccountName." -ForegroundColor Yellow
        Write-Host "`tThe blob container name is $defaultBlobContainerName." -ForegroundColor Yellow
        
        Write-Host "Define the connection string ..." -ForegroundColor Green
        $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
        $storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$defaultStorageAccountName;AccountKey=$defaultStorageAccountKey"
        
        Write-Host "Create block blob objects referencing the hql script file" -ForegroundColor Green
        $storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
        $storageClient = $storageAccount.CreateCloudBlobClient();
        $storageContainer = $storageClient.GetContainerReference($defaultBlobContainerName)
        $hqlScriptBlob = $storageContainer.GetBlockBlobReference($hqlScriptFile)
        
        Write-Host "Define a MemoryStream and a StreamWriter for writing ... " -ForegroundColor Green
        $memStream = New-Object System.IO.MemoryStream
        $writeStream = New-Object System.IO.StreamWriter $memStream
        $writeStream.Writeline($hqlStatements)
        #endregion
        
        #region - Write the Hive script file to Blob storage
        Write-Host "Write to the destination blob ... " -ForegroundColor Green
        $writeStream.Flush()
        $memStream.Seek(0, "Begin")
        $hqlScriptBlob.UploadFromStream($memStream)
        #endregion
        
        Write-Host "Completed!" -ForegroundColor Green

        

4. Définissez les deux premières variables dans le script :

    Variable|Description
    ---|---
    $clusterName|Entrez le nom du cluster HDInsight où vous souhaitez exécuter l’application.
    $subscriptionID|Saisissez votre ID d’abonnement Azure.
    $sourceDataPath|L’emplacement de stockage d’objets Blob Azure où les requêtes Hive lira les données à partir de. Vous n’avez pas besoin de modifier cette variable.
    $outputPath|L’emplacement de stockage d’objets Blob Azure où les requêtes Hive affiche les résultats. Vous n’avez pas besoin de modifier cette variable.
    $hqlScriptFile|L’emplacement et le nom de fichier du fichier de script HiveQL. Vous n’avez pas besoin de modifier cette variable.

5. Appuyez sur **F5** pour exécuter le script. Si vous rencontrez des problèmes, pour résoudre ce problème, sélectionnez toutes les lignes et appuyez sur la touche **F8**.
6. S’affiche pas « Terminé ! » à la fin de la sortie. Tous les messages d’erreur seront affichera en rouge.

Comme une procédure de validation, vous pouvez vérifier le fichier de copie, **/tutorials/twitter/twitter.hql**, sur le stockage Blob Azure à l’aide de stockage Azure explorer ou PowerShell Azure. Pour un exemple de script Windows PowerShell pour la liste des fichiers, voir [le stockage Blob utilisation avec HDInsight][hdinsight-storage-powershell].  


##<a name="process-twitter-data-by-using-hive"></a>Traiter les données à l’aide de Hive Twitter

Vous avez terminé de tout le travail de préparation. À présent, vous pouvez appeler le script Hive et vérifier les résultats.

### <a name="submit-a-hive-job"></a>Soumettre une tâche Hive

Utiliser le script Windows PowerShell suivant pour exécuter le script Hive. Vous devez définir la première variable.

>[AZURE.NOTE] Pour utiliser les tweets et le script HiveQL que vous avez téléchargée dans les deux dernières sections, définissez $hqlScriptFile sur « / tutorials/twitter/twitter.hql ». Pour utiliser ceux qui ont été téléchargées sur un blob public pour vous, définissez $hqlScriptFile sur "wasbs://twittertrend@hditutorialdata.blob.core.windows.net/twitter.hql".

    #region variables and constants
    $clusterName = "<Existing Azure HDInsight Cluster Name>"
    $httpUserName = "admin"
    $httpUserPassword = "<HDInsight Cluster HTTP User Password>"
    
    #use one of the following
    $hqlScriptFile = "wasbs://twittertrend@hditutorialdata.blob.core.windows.net/twitter.hql"
    $hqlScriptFile = "/tutorials/twitter/twitter.hql"
    
    $statusFolder = "/tutorials/twitter/jobstatus"
    #endregion
    
    $myCluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroupName = $myCluster.ResourceGroup
    $defaultStorageAccountName = $myCluster.DefaultStorageAccount.Replace(".blob.core.windows.net", "")
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
    
    $defaultBlobContainerName = $myCluster.DefaultStorageContainer
    
    
    #region - Invoke Hive
    Write-Host "Invoke Hive ... " -ForegroundColor Green
    
    # Create the HDInsight cluster
    $pw = ConvertTo-SecureString -String $httpUserPassword -AsPlainText -Force
    $httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$pw)
    
    Use-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName -HttpCredential $httpCredential 
    $response = Invoke-AzureRmHDInsightHiveJob -DefaultStorageAccountName $defaultStorageAccountName -DefaultStorageAccountKey $defaultStorageAccountKey -DefaultContainer $defaultBlobContainerName -file $hqlScriptFile -StatusFolder $statusFolder #-OutVariable $outVariable
    
    Write-Host "Display the standard error log ... " -ForegroundColor Green
    $jobID = ($response | Select-String job_ | Select-Object -First 1) -replace ‘\s*$’ -replace ‘.*\s’
    Get-AzureRmHDInsightJobOutput -ClusterName $clusterName -JobId $jobID -DefaultContainer $defaultBlobContainerName -DefaultStorageAccountName $defaultStorageAccountName -DefaultStorageAccountKey $defaultStorageAccountKey -HttpCredential $httpCredential
    #endregion

### <a name="check-the-results"></a>Vérifier les résultats

Utilisez le script Windows PowerShell suivant pour vérifier la sortie des tâches Hive. Vous devez définir les deux premières variables.

    #region variables and constants
    $clusterName = "<Existing Azure HDInsight Cluster Name>"
    
    $blob = "tutorials/twitter/output/000000_0" # The name of the blob to be downloaded.
    #engregion
    
    #region - Create an Azure storage context object
    Write-Host "Get the default storage account name and container name based on the cluster name ..." -ForegroundColor Green
    $myCluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroupName = $myCluster.ResourceGroup
    $defaultStorageAccountName = $myCluster.DefaultStorageAccount.Replace(".blob.core.windows.net", "")
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
    $defaultBlobContainerName = $myCluster.DefaultStorageContainer
    
    Write-Host "`tThe storage account name is $defaultStorageAccountName." -ForegroundColor Yellow
    Write-Host "`tThe blob container name is $defaultBlobContainerName." -ForegroundColor Yellow
    
    Write-Host "Create a context object ... " -ForegroundColor Green
    $storageContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey  
    #endregion
    
    #region - Download blob and display blob
    Write-Host "Download the blob ..." -ForegroundColor Green
    cd $HOME
    Get-AzureStorageBlobContent -Container $defaultBlobContainerName -Blob $blob -Context $storageContext -Force
    
    Write-Host "Display the output ..." -ForegroundColor Green
    Write-Host "==================================" -ForegroundColor Green
    cat "./$blob"
    Write-Host "==================================" -ForegroundColor Green
    #end region

> [AZURE.NOTE] La table Hive utilise \001 comme le séparateur de champ. Le délimiteur n’est pas visible dans la sortie.

Une fois que les résultats d’analyse ont été placés dans le stockage Blob Azure, vous pouvez exporter les données vers un serveur de base de données/SQL Azure SQL, exporter les données vers Excel à l’aide de Power Query ou connecter votre application aux données en utilisant le pilote ODBC ruche. Pour plus d’informations, voir [Utiliser Sqoop avec HDInsight][hdinsight-use-sqoop], [analyse des données de retard aérienne à l’aide de HDInsight][hdinsight-analyze-flight-delay-data], [Connecter Excel à HDInsight avec Power Query][hdinsight-power-query]et [Connecter Excel à HDInsight avec le pilote ODBC de Microsoft ruche][hdinsight-hive-odbc].

##<a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, nous avons vu comment transformer un dataset JSON non structuré dans une table Hive structurée pour interroger, Explorer et analyser des données à partir de Twitter à l’aide de HDInsight dans Azure. Pour plus d’informations, voir :

- [Prise en main HDInsight][hdinsight-get-started]
- [Analyser opinion Twitter en temps réel avec HBase dans HDInsight][hdinsight-hbase-twitter-sentiment]
- [Analyser des données de retard aérienne à l’aide de HDInsight][hdinsight-analyze-flight-delay-data]
- [Se connecter Excel à HDInsight avec Power Query][hdinsight-power-query]
- [Se connecter Excel à HDInsight avec le pilote ODBC Microsoft Hive][hdinsight-hive-odbc]
- [Utiliser Sqoop avec HDInsight][hdinsight-use-sqoop]

[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[apache-hive-tutorial]: https://cwiki.apache.org/confluence/display/Hive/Tutorial

[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: powershell-install-configure.md
[powershell-script]: http://technet.microsoft.com/library/ee176961.aspx


[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-storage-powershell]: ../hdinsight-hadoop-use-blob-storage.md#powershell
[hdinsight-analyze-flight-delay-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md
[hdinsight-hive-odbc]: hdinsight-connect-excel-hive-odbc-driver.md
[hdinsight-hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
