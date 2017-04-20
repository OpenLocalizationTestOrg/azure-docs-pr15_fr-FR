<properties 
    pageTitle="Analyser des données de retard aérienne avec Hive sur basé sur Linux HDInsight | Microsoft Azure" 
    description="Découvrez comment utiliser Hive pour analyser des données de vol sur basé sur Linux HDInsight, puis exporter les données vers SQL de base de données à l’aide de Sqoop." 
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

#<a name="analyze-flight-delay-data-by-using-hive-in-hdinsight"></a>Analyser des données de vol retard à l’aide Hive dans HDInsight

Apprenez à analyser des données de retard vol avec Hive sur basé sur Linux HDInsight puis exporter les données dans la base de données SQL Azure à l’aide à l’aide de Sqoop.

> [AZURE.NOTE] Si les éléments individuels de ce document peuvent être utilisés avec HDInsight fonctionnant sous Windows clusters (Python et Hive par exemple), plusieurs étapes sont spécifiques aux clusters basés sur Linux. Pour connaître les étapes qui fonctionnent avec un cluster Windows, voir [données analyse aérienne à l’aide de Hive dans HDInsight](hdinsight-analyze-flight-delay-data.md)

###<a name="prerequisites"></a>Conditions préalables

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- **Azure un abonnement**. Voir [Azure obtenir la version d’évaluation gratuite](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- __HDInsight un cluster__. Les étapes de création d’un nouveau cluster basé sur Linux HDInsight, voir [commencer à utiliser Hadoop avec Hive dans HDInsight sous Linux](hdinsight-hadoop-linux-tutorial-get-started.md) .

- __Base de données SQL azure__. Vous utilisez une base de données SQL Azure comme un magasin de données de destination. Si vous n’avez pas déjà une base de données SQL, voir [didacticiel de base de données SQL : créer une base de données SQL en minutes](../sql-database/sql-database-get-started.md).

- __Azure infrastructure du langage commun__. Si vous n’avez pas installé l’infrastructure du langage commun Azure, voir [installer et configurer l’infrastructure du langage commun Azure](../xplat-cli-install.md) pour plus de détails.

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]


##<a name="download-the-flight-data"></a>Télécharger les données de vol

1. Accédez à la [recherche et une technologie innovante Administration, Bureau du transport statistiques][rita-website].
2. Dans la page, sélectionnez les valeurs suivantes :

  	| Nom | Valeur |
  	| ---- | ---- |
  	| Filtre année | 2013 |
  	| Filtre période | Janvier |
  	| Champs | Année FlightDate, UniqueCarrier, opérateur, FlightNum, OriginAirportID, origine, OriginCityName, OriginState, DestAirportID, Dest, DestCityName, DestState, DepDelayMinutes, ArrDelay, ArrDelayMinutes, CarrierDelay, WeatherDelay, NASDelay, SecurityDelay, LateAircraftDelay. Effacer tous les autres champs |

3. Cliquez sur **Télécharger**. 

##<a name="upload-the-data"></a>Télécharger les données

1. Pour télécharger le fichier zip sur le nœud de tête cluster HDInsight, utilisez la commande suivante :

        scp FILENAME.csv USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:

    Remplacez __FILENAME__ par le nom du fichier zip. Remplacez __nom_utilisateur__ par la connexion SSH pour le cluster HDInsight. Remplacez NOMDUCLUSTER par le nom du cluster HDInsight.
    
    > [AZURE.NOTE] Si vous utilisez un mot de passe d’authentification de votre connexion SSH, le système vous demandera le mot de passe. Si vous avez utilisé une clé publique, vous devrez peut-être utiliser les `-i` paramètre et spécifiez le chemin d’accès à la clé privée correspondante. Par exemple `scp -i ~/.ssh/id_rsa FILENAME.csv USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:`.

2. Une fois le téléchargement terminé, connectez-vous au cluster à l’aide de SSH :

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
        
    Pour plus d’informations sur l’utilisation de SSH avec basé sur Linux HDInsight, voir les articles suivants :
    
    * [Utiliser le protocole SSH avec basé sur Linux Hadoop sur HDInsight de Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

    * [Utiliser le protocole SSH avec basé sur Linux Hadoop sur HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
    
3. Une fois connecté, utilisez les éléments suivants pour décompresser le fichier .zip :

        unzip FILENAME.zip
    
    Un fichier .csv qui est d’environ 60 Mo taille sera extrait.
    
4. Utilisez ce qui suit pour créer un nouveau répertoire sur WASB (la base de données distribué utilisée par HDInsight), puis copiez le fichier :

    Hadoop dfs - mkdir -p /tutorials/flightdelays/data Hadoop dfs-placer FILENAME.csv/didacticiels/flightdelays/données /
    
##<a name="create-and-run-the-hiveql"></a>Créer et exécuter la HiveQL

Utilisez les étapes suivantes pour importer des données à partir du fichier CSV dans une table Hive nommée __retards__.

1. Créer et modifier un nouveau fichier nommé __flightdelays.hql__, utilisez ce qui suit :

        nano flightdelays.hql
        
    Utilisez ce qui suit le contenu de ce fichier :
    
        DROP TABLE delays_raw;
        -- Creates an external table over the csv file
        CREATE EXTERNAL TABLE delays_raw (
            YEAR string,
            FL_DATE string,
            UNIQUE_CARRIER string,
            CARRIER string,
            FL_NUM string,
            ORIGIN_AIRPORT_ID string,
            ORIGIN string,
            ORIGIN_CITY_NAME string,
            ORIGIN_CITY_NAME_TEMP string,
            ORIGIN_STATE_ABR string,
            DEST_AIRPORT_ID string,
            DEST string,
            DEST_CITY_NAME string,
            DEST_CITY_NAME_TEMP string,
            DEST_STATE_ABR string,
            DEP_DELAY_NEW float,
            ARR_DELAY_NEW float,
            CARRIER_DELAY float,
            WEATHER_DELAY float,
            NAS_DELAY float,
            SECURITY_DELAY float,
            LATE_AIRCRAFT_DELAY float)
        -- The following lines describe the format and location of the file
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
        LINES TERMINATED BY '\n'
        STORED AS TEXTFILE
        LOCATION '/tutorials/flightdelays/data';
        
        -- Drop the delays table if it exists
        DROP TABLE delays;
        -- Create the delays table and populate it with data
        -- pulled in from the CSV file (via the external table defined previously)
        CREATE TABLE delays AS
        SELECT YEAR AS year,
            FL_DATE AS flight_date,
            substring(UNIQUE_CARRIER, 2, length(UNIQUE_CARRIER) -1) AS unique_carrier,
            substring(CARRIER, 2, length(CARRIER) -1) AS carrier,
            substring(FL_NUM, 2, length(FL_NUM) -1) AS flight_num,
            ORIGIN_AIRPORT_ID AS origin_airport_id,
            substring(ORIGIN, 2, length(ORIGIN) -1) AS origin_airport_code,
            substring(ORIGIN_CITY_NAME, 2) AS origin_city_name,
            substring(ORIGIN_STATE_ABR, 2, length(ORIGIN_STATE_ABR) -1)  AS origin_state_abr,
            DEST_AIRPORT_ID AS dest_airport_id,
            substring(DEST, 2, length(DEST) -1) AS dest_airport_code,
            substring(DEST_CITY_NAME,2) AS dest_city_name,
            substring(DEST_STATE_ABR, 2, length(DEST_STATE_ABR) -1) AS dest_state_abr,
            DEP_DELAY_NEW AS dep_delay_new,
            ARR_DELAY_NEW AS arr_delay_new,
            CARRIER_DELAY AS carrier_delay,
            WEATHER_DELAY AS weather_delay,
            NAS_DELAY AS nas_delay,
            SECURITY_DELAY AS security_delay,
            LATE_AIRCRAFT_DELAY AS late_aircraft_delay
        FROM delays_raw;
        
2. Utilisez __Ctrl + X__, puis __Y__ enregistrer le fichier.

3. Pour démarrer Hive et exécuter le fichier __flightdelays.hql__ , utilisez ce qui suit :

        beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin -f flightdelays.hql
        
    > [AZURE.NOTE] Dans cet exemple, `localhost` est utilisée dans la mesure où vous êtes connecté au nœud de tête du cluster HDInsight, ce qui correspond à l’endroit où HiveServer2 est en cours d’exécution.

4. Utilisez la commande suivante pour ouvrir une session de Beeline interactive :

        beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin

5. Lorsque vous recevez le `jdbc:hive2://localhost:10001/>` invite, utilisez ce qui suit pour récupérer des données à partir des données importées vol retard.

        INSERT OVERWRITE DIRECTORY '/tutorials/flightdelays/output'
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
        SELECT regexp_replace(origin_city_name, '''', ''),
            avg(weather_delay)
        FROM delays
        WHERE weather_delay IS NOT NULL
        GROUP BY origin_city_name;

    Cela récupérer une liste des villes qui rencontraient des retards météo, ainsi que le délai moyenne et enregistrez-le sur `/tutorials/flightdelays/output`. Plus tard, Sqoop lira les données à partir de cet emplacement et l’exporter vers une base de données SQL Azure.

6. Pour quitter Beeline, entrez `!quit` à l’invite.

## <a name="create-a-sql-database"></a>Créer une base de données SQL

Si vous disposez déjà d’une base de données SQL, vous devez obtenir le nom du serveur. Vous pouvez trouver cette dans le [Portail Azure](https://portal.azure.com) en sélectionnant les __Bases de données SQL__, puis en filtrant sur le nom de la base de données que vous souhaitez utiliser. Le nom du serveur est répertorié dans la colonne de __serveur__ .

Si vous n’avez pas déjà une base de données SQL, utilisez les informations contenues dans [didacticiel de base de données SQL : créer une base de données SQL en minutes](../sql-database/sql-database-get-started.md) en créer un. Vous devrez enregistrer le nom du serveur utilisé pour la base de données.

##<a name="create-a-sql-database-table"></a>Créer une table de base de données SQL

> [AZURE.NOTE] Il existe de nombreuses façons de se connecter à la base de données SQL pour créer une table. Les étapes suivantes utilisent [FreeTDS](http://www.freetds.org/) du cluster HDInsight.

1. Utiliser le protocole SSH pour vous connecter au cluster basé sur Linux HDInsight, puis exécutez les étapes suivantes à partir de la session SSH.

3. Utilisez la commande suivante pour installer FreeTDS :

        sudo apt-get --assume-yes install freetds-dev freetds-bin

4. Une fois que FreeTDS a été installé, utilisez la commande suivante pour vous connecter au serveur de base de données SQL. Remplacez le __nom du serveur__ par le nom du serveur de base de données SQL. Remplacer __adminLogin__ et __adminPassword__ par la connexion de base de données SQL. Remplacez __databaseName__ par le nom de la base de données.

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D <databaseName>

    Vous recevrez une sortie similaire à ce qui suit :

        locale is "en_US.UTF-8"
        locale charset is "UTF-8"
        using default charset "UTF-8"
        Default database being set to sqooptest
        1>

5. À la `1>` invite, entrez les lignes suivantes :

        CREATE TABLE [dbo].[delays](
        [origin_city_name] [nvarchar](50) NOT NULL,
        [weather_delay] float,
        CONSTRAINT [PK_delays] PRIMARY KEY CLUSTERED   
        ([origin_city_name] ASC))
        GO

    Lorsque la `GO` instruction est entrée, les instructions précédentes seront évaluées. Cela crée une nouvelle table nommée __retards__, avec un index cluster (requis par base de données SQL).

    Pour vérifier que la table a été créée, utilisez ce qui suit :

        SELECT * FROM information_schema.tables
        GO

    Vous devriez voir sortie similaire à ce qui suit :

        TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
        databaseName       dbo     delays      BASE TABLE

8. Entrez `exit` à la `1>` invite pour quitter l’utilitaire tsql.
    
##<a name="export-data-with-sqoop"></a>Exporter des données avec Sqoop

2. Utilisez la commande suivante pour vérifier que Sqoop peuvent voir votre base de données SQL :

        sqoop list-databases --connect jdbc:sqlserver://<serverName>.database.windows.net:1433 --username <adminLogin> --password <adminPassword>

    Cela doit renvoyer une liste des bases de données, y compris la base de données que vous avez créée la table retards dans.

3. Pour exporter des données à partir de hivesampletable à la table mobiledata, utilisez la commande suivante :

        sqoop export --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=<databaseName>' --username <adminLogin> --password <adminPassword> --table 'delays' --export-dir 'wasbs:///tutorials/flightdelays/output' --fields-terminated-by '\t' -m 1

    Cela indique Sqoop à se connecter à la base de données SQL, à la base de données contenant la table de retards et exporter des données à partir de la wasbs : / / / didacticiels/flightdelays/sortie (où nous enregistrées le résultat de la requête hive plus tôt,) à la table retards.

4. Une fois la commande terminée, utilisez ce qui suit pour vous connecter à la base de données à l’aide de TSQL :

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D sqooptest

    Une fois connecté, utilisez les instructions suivantes pour vérifier que les données ont été exportées vers la table mobiledata :
    
        SELECT * FROM delays
        GO

    Vous devriez voir une liste de données dans la table. Type de `exit` pour quitter l’utilitaire tsql.

##<a id="nextsteps"></a>Étapes suivantes

Maintenant que vous comprenez comment télécharger un fichier sur le stockage d’objets Blob Azure, comment faire pour remplir une table Hive en utilisant les données depuis le stockage Blob Azure, comment exécuter des requêtes Hive et l’utilisation de Sqoop pour exporter des données à partir de HDFS à une base de données SQL Azure. Pour plus d’informations, consultez les articles suivants :

* [Prise en main HDInsight][hdinsight-get-started]
* [Utiliser Hive avec HDInsight][hdinsight-use-hive]
* [Utiliser Oozie avec HDInsight][hdinsight-use-oozie]
* [Utiliser Sqoop avec HDInsight][hdinsight-use-sqoop]
* [Utiliser cochon avec HDInsight][hdinsight-use-pig]
* [Développer des programmes Java MapReduce pour HDInsight][hdinsight-develop-mapreduce]
* [Développer Hadoop Python diffusion en continu de programmes pour HDInsight][hdinsight-develop-streaming]



[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/


[rita-website]: http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time
[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[hdinsight-use-oozie]: hdinsight-use-oozie-linux-mac.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop-mac-linux.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-develop-streaming]: hdinsight-hadoop-streaming-python.md
[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce-linux.md

[hadoop-hiveql]: https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx


 
