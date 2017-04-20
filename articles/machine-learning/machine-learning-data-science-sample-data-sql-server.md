<properties 
    pageTitle="Exemples de données dans SQL Server sur Azure | Microsoft Azure" 
    description="Exemples de données dans SQL Server sur Azure" 
    services="machine-learning" 
    documentationCenter="" 
    authors="bradsev" 
    manager="jhubbard" 
    editor="cgronlun" />

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/19/2016" 
    ms.author="fashah;garye;bradsev" /> 

#<a name="heading"></a>Exemples de données dans SQL Server sur Azure


Ce document montre comment des exemples de données stockées dans SQL Server sur Azure en utilisant SQL ou le langage de programmation Python. Il indique également comment déplacer des données échantillonnées dans apprentissage automatique Azure en enregistrant dans un fichier, téléchargeant dans un blob Azure, puis le lisez dans Azure Machine apprentissage Studio.

L’échantillonnage Python utilise la bibliothèque ODBC [pyodbc](https://code.google.com/p/pyodbc/) pour vous connecter à SQL Server sur Azure et la bibliothèque [Pandas](http://pandas.pydata.org/) pour effectuer l’échantillonnage.

>[AZURE.NOTE] L’exemple de code SQL dans ce document part du principe que les données se trouvent dans un serveur SQL Azure. Si elle n’est pas le cas, reportez-vous à la rubrique [déplacer des données vers SQL Server sur Azure](machine-learning-data-science-move-sql-server-virtual-machine.md) pour obtenir des instructions sur la façon de déplacer vos données vers SQL Server sur Azure.

**Pourquoi des exemples de vos données ?**
Si le jeu de données que vous souhaitez analyser est volumineux, il est généralement judicieux de sélectionner les données pour réduire la taille de plus petite taille, mais représentatives et plus facile à gérer échantillons vers le bas. Cela facilite la compréhension des données, exploration et ingénierie de fonctionnalité. Son rôle dans l' [Équipe données scientifique processus (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) consiste à activer prototypage rapide de la machine modèles d’apprentissage et de fonctions de traitement des données.

Le **menu** en dessous des liens vers des rubriques qui décrivent l’échantillon de données de différents environnements de stockage. 

[AZURE.INCLUDE [cap-sample-data-selector](../../includes/cap-sample-data-selector.md)]

Cette tâche d’échantillonnage est une étape de l' [Équipe données scientifique processus (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

##<a name="SQL"></a>À l’aide de SQL

Cette section décrit plusieurs méthodes pour effectuer un sondage simple contre les données dans la base de données à l’aide de SQL. Choisissez une méthode basée sur la taille de vos données et de sa distribution.

Les deux éléments ci-dessous montrent comment utiliser newid dans SQL Server pour effectuer l’échantillonnage. La méthode choisie dépend de comment aléatoires que vous voulez l’échantillon à être (pk_id dans l’exemple de code ci-dessous est supposé égal à une clé primaire générées automatiquement).

1. Sondage moins strict

        select  * from <table_name> where <primary_key> in 
        (select top 10 percent <primary_key> from <table_name> order by newid())

2. Échantillon plus aléatoire 

        SELECT * FROM <table_name>
        WHERE 0.1 >= CAST(CHECKSUM(NEWID(), <primary_key>) & 0x7fffffff AS float)/ CAST (0x7fffffff AS int)

TABLESAMPLE peut être utilisée pour l’échantillonnage ainsi qu’illustré ci-dessous. Cela peut être une meilleure approche si vos données sont volumineuses (en supposant que les données sur différentes pages ne sont pas en corrélation) et de la requête à effectuer dans les délais.

    SELECT *
    FROM <table_name> 
    TABLESAMPLE (10 PERCENT)

>[AZURE.NOTE] Vous pouvez Explorer et générer des fonctionnalités à partir de ces données échantillonnées en stockant dans une nouvelle table


###<a name="sql-aml"></a>Connexion à apprentissage automatique Azure

Vous pouvez utiliser directement les requêtes de l’exemple ci-dessus dans l' apprentissage automatique Azure [Importer des données] [ import-data] module pour les données à la volée échantillons vers le bas et importez-les dans une expérience d’apprentissage automatique Azure. Capture d’écran de l’utilisation du module de lecture pour lire les données échantillonnées est indiqué ci-dessous :
   
![lecteur sql][1]

##<a name="python"></a>À l’aide du langage de programmation Python 

Cette section illustre l’utilisation de la [bibliothèque pyodbc](https://code.google.com/p/pyodbc/) pour établir une application ODBC se connecter à une base de données SQL server dans Python. La chaîne de connexion se présente comme suit : (remplacez le nom du serveur, dbname, nom d’utilisateur et mot de passe avec votre configuration) :

    #Set up the SQL Azure connection
    import pyodbc   
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

La bibliothèque [Pandas](http://pandas.pydata.org/) dans Python fournit un large éventail de structures de données et outils d’analyse des données pour manipulation des données pour la programmation Python. Le code ci-dessous lit un échantillon de 0,1 % des données à partir d’une table de base de données SQL Azure dans un données Pandas :

    import pandas as pd

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select column1, cloumn2... from <table_name> tablesample (0.1 percent)''', conn)

Vous pouvez maintenant utiliser les données dans le cadre de données Pandas échantillonnées. 

###<a name="python-aml"></a>Connexion à apprentissage automatique Azure

Vous pouvez utiliser le code suivant pour enregistrer les données échantillonnée vers le bas vers un fichier et téléchargez-le sur un blob Azure. Les données dans le blob pouvant être directement lu dans une expérience de formation Machine Azure à l’aide de l' [Importation de données] [ import-data] module. La procédure est la suivante : 

1. Écrire la trame de données pandas dans un fichier local

        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)

2. Télécharger des fichiers local sur blob Azure

        from azure.storage import BlobService
        import tables

        STORAGEACCOUNTNAME= <storage_account_name>
        LOCALFILENAME= <local_file_name>
        STORAGEACCOUNTKEY= <storage_account_key>
        CONTAINERNAME= <container_name>
        BLOBNAME= <blob_name>

        output_blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)    
        localfileprocessed = os.path.join(os.getcwd(),LOCALFILENAME) #assuming file is in current working directory
        
        try:
       
        #perform upload
        output_blob_service.put_block_blob_from_path(CONTAINERNAME,BLOBNAME,localfileprocessed)
        
        except:         
            print ("Something went wrong with uploading blob:"+BLOBNAME)

3. Lire les données d’objets blob Azure à l’aide d’apprentissage automatique Azure [Importer des données] [ import-data] module comme le montre la capture d’écran ci-dessous :
 
![objets blob Reader][2]

## <a name="the-team-data-science-process-in-action-example"></a>Le processus d’équipe données scientifique dans l’exemple d’Action

Pour obtenir un exemple de procédure pas à pas de bout en bout du processus d’équipe données scientifique une en utilisant un jeu de données public, voir [équipe données scientifique processus en Action : à l’aide de SQL Server](machine-learning-data-science-process-sql-walkthrough.md).

[1]: ./media/machine-learning-data-science-sample-sql-server-virtual-machine/reader_database.png
[2]: ./media/machine-learning-data-science-sample-sql-server-virtual-machine/reader_blob.png

 [import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
