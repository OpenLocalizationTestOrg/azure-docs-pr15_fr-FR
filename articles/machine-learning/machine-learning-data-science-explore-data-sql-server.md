<properties 
    pageTitle="Explorer des données dans SQL Server virtuelle Machine sur Azure | Microsoft Azure" 
    description="Comment Explorer des données qui sont stockées dans un serveur machine virtuelle SQL sur Azure." 
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
    ms.date="09/13/2016" 
    ms.author="bradsev" /> 

#<a name="explore-data-in-sql-server-virtual-machine-on-azure"></a>Explorer des données dans SQL Server virtuelle Machine sur Azure


Ce document explique comment Explorer des données qui sont stockées dans un serveur machine virtuelle SQL sur Azure. Il est possible en ensuivirent de données à l’aide de SQL ou en utilisant un langage de programmation comme Python.

Les liens de **menu** suivants vers des rubriques qui décrivent comment utiliser les outils pour explorer des données à partir de différents environnements de stockage. Cette tâche est une étape dans le processus d’Analytique Cortana (Lettrine).

[AZURE.INCLUDE [cap-explore-data-selector](../../includes/cap-explore-data-selector.md)]


> [AZURE.NOTE] Les exemples d’instructions SQL dans ce document part du principe que les données se trouve dans SQL Server. Si elle n’est pas, consultez le mappage de processus cloud données scientifique pour découvrir comment déplacer vos données vers SQL Server.



## <a name="sql-dataexploration"></a>Explorer des données SQL avec des scripts SQL

Voici quelques exemples de scripts SQL qui peuvent servir à Explorer magasins de données dans SQL Server.

1. Obtenir le nombre d’observations par jour

    `SELECT CONVERT(date, <date_columnname>) as date, count(*) as c from <tablename> group by CONVERT(date, <date_columnname>)` 

2. Obtenir les niveaux dans une colonne par catégorie

    `select  distinct <column_name> from <databasename>`

3. Obtenir le nombre de niveaux dans la combinaison de deux colonnes par catégorie 

    `select <column_a>, <column_b>,count(*) from <tablename> group by <column_a>, <column_b>`

4. Obtenir la distribution pour des colonnes numériques

    `select <column_name>, count(*) from <tablename> group by <column_name>`

> [AZURE.NOTE] Pour obtenir un exemple pratique, vous pouvez utiliser le [jeu de données NYC Taxi](http://www.andresmh.com/nyctaxitrips/) et faire référence à la IPNB intitulé [NYC données ensuivirent à l’aide de bloc-notes IPython et SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) pour une étape par étape de bout en bout.

##<a name="python"></a>Explorer des données SQL avec Python

À l’aide de Python pour explorer des données et générer des fonctionnalités lorsque les données sont dans SQL Server est semblable au traitement des données d’objets blob Azure à l’aide de Python, comme indiqué dans les [données d’objets Blob Azure processus dans votre environnement scientifique de données](machine-learning-data-science-process-data-blob.md). Les données devant être chargées à partir de la base de données dans un pandas DataFrame et puis peuvent être poursuivies. Nous documenter le processus de connexion à la base de données et charger les données dans la DataFrame dans cette section.

Au format de la chaîne de connexion peut être utilisé pour vous connecter à une base de données SQL Server à partir de Python à l’aide de pyodbc (nom du serveur de remplacer, dbname, nom d’utilisateur et mot de passe avec vos valeurs spécifiques) :

    #Set up the SQL Azure connection
    import pyodbc   
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

La [bibliothèque Pandas](http://pandas.pydata.org/) dans Python fournit un large éventail de structures de données et outils d’analyse des données pour manipulation des données pour la programmation Python. Le code suivant lit les résultats renvoyés à partir d’une base de données SQL Server dans un bloc de données Pandas :

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <cloumnname2>... from <tablename>''', conn)

Maintenant, vous pouvez travailler avec les DataFrame Pandas comme décrit dans la rubrique [processus Azure Blob des données dans votre environnement scientifique de données](machine-learning-data-science-process-data-blob.md).

## <a name="cortana-analytics-process-in-action-example"></a>Processus Analytique Cortana dans l’exemple d’Action

Pour obtenir un exemple de procédure pas à pas de bout en bout du processus Cortana Analytique à l’aide d’un jeu de données public, voir [le processus scientifique des données d’équipe en action : à l’aide de SQL Server](machine-learning-data-science-process-sql-walkthrough.md).

 
