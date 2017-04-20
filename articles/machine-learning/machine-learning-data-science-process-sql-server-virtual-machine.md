<properties 
    pageTitle="Traiter des données SQL Azure | Microsoft Azure" 
    description="Processus données SQL Azure" 
    services="machine-learning" 
    documentationCenter="" 
    authors="garyericson" 
    manager="jhubbard" 
    editor="" />

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/16/2016" 
    ms.author="fashah;garye;bradsev" /> 

#<a name="heading"></a>Données de processus dans SQL serveur Machine virtuelle sur Azure

Ce document explique comment Explorer des données et générer des fonctionnalités pour les données stockées dans un serveur machine virtuelle SQL sur Azure. Il est possible en ensuivirent de données à l’aide de SQL ou en utilisant un langage de programmation comme Python.


> [AZURE.NOTE] Les exemples d’instructions SQL dans ce document part du principe que les données se trouve dans SQL Server. Si elle n’est pas, reportez-vous à la carte de processus cloud données scientifique pour découvrir comment déplacer vos données vers SQL Server.

##<a name="SQL"></a>À l’aide de SQL

Nous décrire les tâches wrangling données suivantes dans cette section à l’aide de SQL :

1. [Exploration de données](#sql-dataexploration)
2. [Génération de fonctionnalité](#sql-featuregen)

###<a name="sql-dataexploration"></a>Exploration de données
Voici quelques exemples de scripts SQL qui peuvent servir à Explorer magasins de données dans SQL Server.


> [AZURE.NOTE] Pour obtenir un exemple pratique, vous pouvez utiliser le [jeu de données NYC Taxi](http://www.andresmh.com/nyctaxitrips/) et faire référence à la IPNB intitulé [NYC données ensuivirent à l’aide de bloc-notes IPython et SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) pour une étape par étape de bout en bout.

1. Obtenir le nombre d’observations par jour

    `SELECT CONVERT(date, <date_columnname>) as date, count(*) as c from <tablename> group by CONVERT(date, <date_columnname>)` 

2. Obtenir les niveaux dans une colonne par catégorie

    `select  distinct <column_name> from <databasename>`

3. Obtenir le nombre de niveaux dans la combinaison de deux colonnes par catégorie 

    `select <column_a>, <column_b>,count(*) from <tablename> group by <column_a>, <column_b>`

4. Obtenir la distribution pour des colonnes numériques

    `select <column_name>, count(*) from <tablename> group by <column_name>`


###<a name="sql-featuregen"></a>Génération de fonctionnalité

Dans cette section, nous décrire les méthodes de génération de fonctions à l’aide de SQL :  

1. [Compter en fonction de génération de fonctionnalité](#sql-countfeature)
2. [Groupement de génération de fonctionnalité](#sql-binningfeature)
3. [Découvrez les fonctionnalités des yeux à partir d’une seule colonne](#sql-featurerollout)


> [AZURE.NOTE] Une fois que vous générez des fonctionnalités supplémentaires, vous pouvez les ajouter en tant que colonnes à la table existante ou créer une table avec les autres fonctionnalités de clé primaire, qui peut être jointes à la table d’origine. 

###<a name="sql-countfeature"></a>Compter en fonction de génération de fonctionnalité

Ce document montre deux manières de générer des fonctionnalités de nombre. La première méthode utilise Somme conditionnelle et la deuxième méthode utilise la clause « where ». Ils peuvent être joints avec la table d’origine (à l’aide de colonnes de clé primaire) pour que les fonctions NB en parallèle avec les données d’origine.

    select <column_name1>,<column_name2>,<column_name3>, COUNT(*) as Count_Features from <tablename> group by <column_name1>,<column_name2>,<column_name3> 

    select <column_name1>,<column_name2> , sum(1) as Count_Features from <tablename> 
    where <column_name3> = '<some_value>' group by <column_name1>,<column_name2> 

###<a name="sql-binningfeature"></a>Groupement de génération de fonctionnalité

L’exemple suivant montre comment générer des fonctionnalités binned par groupement (à l’aide de 5 emplacements) une colonne numérique qui peut être utilisée comme une fonctionnalité à la place :

    `SELECT <column_name>, NTILE(5) OVER (ORDER BY <column_name>) AS BinNumber from <tablename>`


###<a name="sql-featurerollout"></a>Découvrez les fonctionnalités des yeux à partir d’une seule colonne

Dans cette section, nous vous montrer comment pellicule indépendante une seule colonne dans une table pour générer des fonctionnalités supplémentaires. L’exemple suppose qu’il existe une colonne latitude ou de longitude dans la table à partir de laquelle vous essayez de générer des fonctionnalités.

Voici une brève introduction sur les données de localisation latitude/longitude (allocation des ressources à partir de stackoverflow [comment mesurer la précision de latitude et longitude ?](http://gis.stackexchange.com/questions/8650/how-to-measure-the-accuracy-of-latitude-and-longitude)). Il est utile de connaître avant featurizing le champ emplacement :

- Le signe nous indique que nous sommes Nord ou sud, Moyen-Orient ou ouest sur le globe.
- Un centaines différente de zéro chiffre nous indique que nous utilisons longitude et latitude pas !
- Les dizaines chiffre donne une position à environ 1 000 kilomètres. Il nous donne des informations utiles sur quel continent ou océan nous situés.
- Le chiffre des unités (un degré décimal) donne une position 111 kilomètres (60 milles, environ 69 miles). Il peut donner à peu près état grand ou pays dans que nous sommes.
- La première décimale vaut jusqu'à 11.1 kilomètres : elle peut distinguer la position d’une grande ville à partir d’une grande ville voisine.
- La deuxième décimale vaut jusqu'à 1.1 kilomètres : il peut séparer un peuvent être omises du suivant.
- La troisième décimale vaut jusqu'à 110 m : il peut identifier un grand champ agricole ou un campus institutions.
- La quatrième décimale vaut m jusqu'à 11, il peut identifier une superficie de terre. Elle s’apparente à la précision d’une unité GPS corrigée classique sans avoir intervenir.
- La cinquième décimale est-il important de m jusqu'à 1.1 distinguer les uns des autres et les arborescences. Précision à ce niveau avec unités GPS professionnelle est possible uniquement avec correction différentielle.
- La sixième décimale vaut jusqu'à 0,11 m : vous pouvez l’utiliser pour disposer les structures de façon plus détaillée, pour la conception de paysages, créez des routes. Il doit être plus de suffisant pour le suivi des mouvements d’enneigées et terminaux. Pour cela, en prenant des mesures soignés avec GPS, par exemple GPS DIFFERENTIELLE corrigé.

Les informations d’emplacement peuvent être featurized comme suit, séparer les région, emplacement et d’informations sur la ville. Notez que vous pouvez également appeler un point de terminaison reste comme API Bing Maps disponibles en [trouver un emplacement par Point](https://msdn.microsoft.com/library/ff701710.aspx) afin d’obtenir les informations de région/secteur.

    select 
        <location_columnname>
        ,round(<location_columnname>,0) as l1       
        ,l2=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 1 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),1,1) else '0' end     
        ,l3=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 2 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),2,1) else '0' end     
        ,l4=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 3 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),3,1) else '0' end     
        ,l5=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 4 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),4,1) else '0' end     
        ,l6=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 5 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),5,1) else '0' end     
        ,l7=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 6 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),6,1) else '0' end     
    from <tablename>

Les fonctionnalités basée sur l’emplacement ci-dessus peuvent servir davantage pour générer des fonctionnalités supplémentaires count comme décrit plus haut. 


> [AZURE.TIP] Vous pouvez insérer par programmation les enregistrements à l’aide de la langue de votre choix. Vous devrez peut-être insérer les données dans des segments pour améliorer l’efficacité d’écriture (pour obtenir un exemple de la façon de procéder à l’aide de pyodbc, voir [exemple HelloWorld A pour accéder à SQL Server avec python](https://code.google.com/p/pypyodbc/wiki/A_HelloWorld_sample_to_access_mssql_with_python)). Une autre solution consiste à insérer des données dans la base de données à l’aide de l' [utilitaire BCP](https://msdn.microsoft.com/library/ms162802.aspx).

###<a name="sql-aml"></a>Connexion à apprentissage automatique Azure

La fonctionnalité nouvellement créée peut être ajoutée sous forme de colonne à une table existante ou stockée dans une nouvelle table et rejoint avec la table d’origine pour apprentissage automatique. Fonctionnalités pouvant être générées ou accessibles si vous avez déjà créé, à l’aide de l' [Importation de données] [ import-data] module dans l’apprentissage automatique Azure comme indiqué ci-dessous :

![lecteurs azureml][1] 

##<a name="python"></a>À l’aide d’un langage de programmation comme Python

À l’aide de Python pour explorer des données et générer des fonctionnalités lorsque les données sont dans SQL Server est similaire au traitement des données d’objets blob Azure à l’aide de Python comme indiqué dans les [données d’objets Blob Azure processus dans environnement scientifique données](machine-learning-data-science-process-data-blob.md). Les données devant être chargées à partir de la base de données dans une trame de données pandas et puis peuvent être poursuivies. Nous documenter le processus de connexion à la base de données et charger les données dans le cadre de données dans cette section.

Au format de la chaîne de connexion peut être utilisé pour vous connecter à une base de données SQL Server à partir de Python à l’aide de pyodbc (nom du serveur de remplacer, dbname, nom d’utilisateur et mot de passe avec vos valeurs spécifiques) :

    #Set up the SQL Azure connection
    import pyodbc   
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

La [bibliothèque Pandas](http://pandas.pydata.org/) dans Python fournit un large éventail de structures de données et outils d’analyse des données pour manipulation des données pour la programmation Python. Le code ci-dessous lit les résultats renvoyés à partir d’une base de données SQL Server dans un bloc de données Pandas :

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <cloumnname2>... from <tablename>''', conn)

Désormais, vous pouvez travailler avec la trame de données Pandas comme décrit dans l’article [processus Azure Blob des données dans un environnement scientifique données](machine-learning-data-science-process-data-blob.md).

## <a name="azure-data-science-in-action-example"></a>Données Azure scientifique dans l’exemple d’Action

Pour obtenir un exemple de procédure pas à pas de bout en bout du processus scientifique données Azure à l’aide d’un jeu de données public, voir [Azure données scientifique processus en Action](machine-learning-data-science-process-sql-walkthrough.md).

[1]: ./media/machine-learning-data-science-process-sql-server-virtual-machine/reader_db_featurizedinput.png


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
 
