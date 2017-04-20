<properties
    pageTitle="Analyser des données Twitter avec Apache ruche sur HDInsight | Microsoft Azure"
    description="Découvrez comment utiliser Python pour stocker Tweets qui contiennent des mots clés spécifiques, puis utilisent Hive et Hadoop sur HDInsight pour transformer les données brutes TWitter une table Hive disponible pour la recherche."
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
    ms.date="09/27/2016"
    ms.author="larryfr"/>

# <a name="analyze-twitter-data-using-hive-in-hdinsight"></a>Analyser des données Twitter avec Hive dans HDInsight

Dans ce document, vous obtiendrez tweet en utilisant un Twitter API de diffusion en continu et puis utiliser Apache ruche sur un cluster HDInsight basé sur Linux au processus le JSON mis en forme les données. Le résultat sera une liste des utilisateurs Twitter qui vous a envoyé les tweets la plupart des contenant un certain mot.

> [AZURE.NOTE] Si les éléments individuels de ce document peuvent être utilisés avec clusters HDInsight fonctionnant sous Windows (Python par exemple), plusieurs étapes sont basés sur en utilisant un cluster basé sur Linux HDInsight. Pour obtenir la procédure spécifique vers un cluster de Windows, voir [Twitter analyser des données à l’aide de Hive dans HDInsight](hdinsight-analyze-twitter-data.md).

###<a name="prerequisites"></a>Conditions préalables

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- Un __cluster basé sur Linux Azure HDInsight__. Pour plus d’informations sur la création d’un cluster, voir [Prise en main basé sur Linux HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md) les étapes de création d’un cluster.

- Un __client SSH__. Pour plus d’informations sur l’utilisation de SSH avec basé sur Linux HDInsight, voir les articles suivants :

    * [Utiliser le protocole SSH avec basé sur Linux Hadoop sur HDInsight de Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

    * [Utiliser le protocole SSH avec basé sur Linux Hadoop sur HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

- __Python__ et [pip](https://pypi.python.org/pypi/pip)

##<a name="get-a-twitter-feed"></a>Obtenir un Twitter flux

Twitter vous permet d’extraire les [données pour chaque tweet](https://dev.twitter.com/docs/platform-objects/tweets) comme document de Notation JSON (JavaScript Object) via une API REST. [OAuth](http://oauth.net) est requis pour l’authentification à l’API. Vous devez également créer une _Application Twitter_ qui contient les paramètres utilisés pour accéder à l’API.

###<a name="create-a-twitter-application"></a>Créer une application Twitter

1. À partir d’un navigateur web, connectez-vous à [https://apps.twitter.com/](https://apps.twitter.com/). Cliquez sur le lien **s’inscrire maintenant** si vous n’avez pas un compte Twitter.
2. Cliquez sur **créer une nouvelle application**.
3. Entrez le **nom**, **Description**, **site Web**. Vous pouvez rendre une URL pour le champ **site Web** . Le tableau suivant montre quelques exemples de valeurs à utiliser :

  	| Champ | Valeur |
  	|:----- |:----- |
  	| Nom  | MyHDInsightApp |
  	| Description | MyHDInsightApp |
  	| Site Web | http://www.myhdinsightapp.com |
    
4. Consultez **Oui, j’accepte**et puis cliquez sur **créer votre application Twitter**.
5. Cliquez sur l’onglet **autorisations** . L’autorisation par défaut est **en lecture seule**. Ceci est suffisant pour ce didacticiel.
6. Cliquez sur l’onglet **clés et des jetons d’accès** .
7. Cliquez sur **créer mon jeton d’accès**.
8. Cliquez sur **Test OAuth** dans le coin supérieur droit de la page.
9. Prenez note de **clé de consommateur**, **consommateur secret**, **jeton d’accès**et **secret jeton d’accès**. Vous aurez besoin des valeurs plus tard.

>[AZURE.NOTE] Lorsque vous utilisez la commande curl dans Windows, utilisez des guillemets doubles au lieu de guillemets simples pour les valeurs d’option.

###<a name="download-tweets"></a>Télécharger tweets

Le code suivant Python sera Téléchargez 10 000 tweet Twitter et les enregistrer dans un fichier nommé __tweets.txt__.

> [AZURE.NOTE] Les étapes suivantes sont exécutées sur le cluster HDInsight, étant donné que Python est déjà installée.

1. Connectez-vous au cluster HDInsight à l’aide de SSH :

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
        
    Si vous avez utilisé un mot de passe pour votre compte d’utilisateur SSH, vous devrez entrer. Si vous avez utilisé une clé publique, vous devrez peut-être utiliser les `-i` paramètre pour spécifier la clé privée correspondante. Par exemple, `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`.
        
    Pour plus d’informations sur l’utilisation de SSH avec basé sur Linux HDInsight, voir les articles suivants :
    
    * [Utiliser le protocole SSH avec basé sur Linux Hadoop sur HDInsight de Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

    * [Utiliser le protocole SSH avec basé sur Linux Hadoop sur HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows)
    
2. Par défaut, l’utilitaire __pip__ n’est pas installé sur le nœud de tête HDInsight. Utilisez les éléments suivants pour installer et puis mettez à jour cet utilitaire :

        sudo apt-get install python-pip
        sudo pip install --upgrade pip

3. Le code à télécharger tweet repose sur [Tweepy](http://www.tweepy.org/) et [Progressbar](https://pypi.python.org/pypi/progressbar/2.2). Pour installer les fichiers, utilisez la commande suivante :

        sudo apt-get install python-dev libffi-dev libssl-dev
        sudo apt-get remove python-openssl
        sudo pip install tweepy progressbar pyOpenSSL requests[security]
        
    > [AZURE.NOTE] Les bits sur la suppression d’openssl python, l’installation de développement de python, libffi développement, libssl développement, pyOpenSSL et demandes [sécurité] consiste à éviter un avertissement InsecurePlatform lors de la connexion à Twitter via SSL à partir de Python.
    >
    > Tweepy v3.2.0 est utilisé pour éviter [un message d’erreur](https://github.com/tweepy/tweepy/issues/576) qui peuvent se produire lors du traitement de tweets.

4. Utilisez la commande suivante pour créer un nouveau fichier nommé __gettweets.py__:

        nano gettweets.py

5. Utilisez ce qui suit en tant que le contenu du fichier __gettweets.py__ . Remplacer les informations d’espace réservé pour __consommateur\_secret__, __consommateur\_clé__, __access /\_jeton__, et __access\_jeton\_secret__ avec les informations à partir de votre application Twitter.

        #!/usr/bin/python

        from tweepy import Stream, OAuthHandler
        from tweepy.streaming import StreamListener
        from progressbar import ProgressBar, Percentage, Bar
        import json
        import sys

        #Twitter app information
        consumer_secret='Your consumer secret'
        consumer_key='Your consumer key'
        access_token='Your access token'
        access_token_secret='Your access token secret'

        #The number of tweets we want to get
        max_tweets=10000

        #Create the listener class that will receive and save tweets
        class listener(StreamListener):
            #On init, set the counter to zero and create a progress bar
            def __init__(self, api=None):
                self.num_tweets = 0
                self.pbar = ProgressBar(widgets=[Percentage(), Bar()], maxval=max_tweets).start()

            #When data is received, do this
            def on_data(self, data):
                #Append the tweet to the 'tweets.txt' file
                with open('tweets.txt', 'a') as tweet_file:
                    tweet_file.write(data)
                    #Increment the number of tweets
                    self.num_tweets += 1
                    #Check to see if we have hit max_tweets and exit if so
                    if self.num_tweets >= max_tweets:
                        self.pbar.finish()
                        sys.exit(0)
                    else:
                        #increment the progress bar
                        self.pbar.update(self.num_tweets)
                return True

            #Handle any errors that may occur
            def on_error(self, status):
                print status

        #Get the OAuth token
        auth = OAuthHandler(consumer_key, consumer_secret)
        auth.set_access_token(access_token, access_token_secret)
        #Use the listener class for stream processing
        twitterStream = Stream(auth, listener())
        #Filter for these topics
        twitterStream.filter(track=["azure","cloud","hdinsight"])

6. Utilisez __Ctrl + X__, puis __Y__ enregistrer le fichier.

7. Pour exécuter le fichier et télécharger tweets, utilisez la commande suivante :

        python gettweets.py

    Un indicateur de progression devant apparaître et compter jusqu'à 100 % comme la tweet est téléchargés et enregistrées dans le fichier.

    > [AZURE.NOTE] Si elle prend beaucoup de temps pour la barre de progression avancer, vous devez modifier le filtre pour effectuer le suivi des sujets actuels ; Lorsqu’il y a un grand nombre de tweets à propos de la rubrique vous filtrez, vous pouvez obtenir très rapidement la 10000 tweet nécessaire.

###<a name="upload-the-data"></a>Télécharger les données

Pour télécharger les données vers WASB (le système de fichiers DFS utilisé par HDInsight), utilisez les commandes suivantes :

    hdfs dfs -mkdir -p /tutorials/twitter/data
    hdfs dfs -put tweets.txt /tutorials/twitter/data/tweets.txt

Cette option stocke les données dans un emplacement accessible à tous les nœuds du cluster.

##<a name="run-the-hiveql-job"></a>Exécuter la tâche HiveQL


1. Utilisez la commande suivante pour créer un fichier contenant les instructions de HiveQL :

        nano twitter.hql
    
    En tant que le contenu du fichier, utilisez ce qui suit :

        set hive.exec.dynamic.partition = true;
        set hive.exec.dynamic.partition.mode = nonstrict;
        -- Drop table, if it exists
        DROP TABLE tweets_raw;
        -- Create it, pointing toward the tweets logged from Twitter
        CREATE EXTERNAL TABLE tweets_raw (
            json_response STRING
        )
        STORED AS TEXTFILE LOCATION '/tutorials/twitter/data';
        -- Drop and recreate the destination table
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
        -- Select tweets from the imported data, parse the JSON,
        -- and insert into the tweets table
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
        
        
3. Appuyez sur __Ctrl + X__, puis appuyez sur __o__ pour enregistrer le fichier.

4. Utilisez la commande suivante pour exécuter le HiveQL contenue dans le fichier :

        beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin -i twitter.hql
        
    Cela charger un shell Hive, exécutez la HiveQL dans le fichier __twitter.hql__ et enfin de renvoyer un `jdbc:hive2//localhost:10001/>` invite de commandes.
    
5. À l’invite beeline, utilisez les éléments suivants pour vérifier que vous pouvez sélectionner les données de la table __tweets__ créée par le HiveQL dans le fichier __twitter.hql__ :
        
        SELECT name, screen_name, count(1) as cc
            FROM tweets
            WHERE text like "%Azure%"
            GROUP BY name,screen_name
            ORDER BY cc DESC LIMIT 10;

    Ceci renvoie un maximum de 10 tweets qui contiennent le mot __Azure__ dans le texte du message.

##<a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, nous avons vu comment transformer un dataset JSON non structuré dans une table Hive structurée pour interroger, Explorer et analyser des données à partir de Twitter à l’aide de HDInsight dans Azure. Pour plus d’informations, voir :

- [Prise en main HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md)
- [Analyser des données de retard aérienne à l’aide de HDInsight](hdinsight-analyze-flight-delay-data-linux.md)

[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[apache-hive-tutorial]: https://cwiki.apache.org/confluence/display/Hive/Tutorial

[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter
