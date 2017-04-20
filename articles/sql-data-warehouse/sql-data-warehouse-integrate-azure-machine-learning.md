<properties
   pageTitle="Utiliser l’apprentissage Machine Azure avec SQL Data Warehouse | Microsoft Azure"
   description="Didacticiel pour l’utilisation d’apprentissage automatique Azure avec Azure SQL Data Warehouse pour développer des solutions."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="kevinvngo"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/16/2016"
   ms.author="kevin;barbkess;sonyama"/>

# <a name="use-azure-machine-learning-with-sql-data-warehouse"></a>Utiliser l’apprentissage Machine Azure avec SQL Data Warehouse

Azure apprentissage automatique est un service entièrement géré analytique prédictive que vous pouvez utiliser pour créer des modèles prédictives par rapport à vos données SQL Data Warehouse et puis publier en tant que services web prêt à utiliser. Vous pouvez découvrir des prédictive analytique et d’apprentissage en lisant l’article [Introduction aux Machine d’apprentissage sur Azure][]automatique.  Vous pouvez puis apprenez à créer, former, score général et test d’un modèle d’apprentissage automatique à l’aide de la [créer expérimenter didacticiel][].

Dans cet article, vous allez apprendre à effectuer les opérations suivantes à l’aide de la [Azure Machine apprentissage Studio][]:

- Lire les données à partir de votre base de données pour créer, former et score général un modèle de prévision
- Écrire des données dans votre base de données


## <a name="read-data-from-sql-data-warehouse"></a>Lire les données de Data Warehouse SQL

Nous lira des données à partir de la table Product dans la base de données AdventureWorksDW.

### <a name="step-1"></a>Étape 1

Démarrer une nouvelle expérience en cliquant sur + Nouveau en bas de la fenêtre Machine apprentissage Studio, sélectionnez expérience, puis tester vide. Sélectionnez le nom d’expérience par défaut en haut de la zone de dessin, puis renommez-la un nom significatif, par exemple, les prédiction de prix de bicyclette.

### <a name="step-2"></a>Étape 2

Recherchez le module de lecteur dans la palette de jeux de données et les modules à gauche de la zone de dessin expérience. Faites glisser le module vers la zone de dessin expérience.
![][drag_reader]

### <a name="step-3"></a>Étape 3

Sélectionnez le module de lecteur et remplissez le volet de propriétés.

1. Sélectionnez la base de données SQL Azure comme Source de données.
2. Nom du serveur de base de données : tapez le nom du serveur. Vous pouvez utiliser le [portail Azure][] pour trouver cette.

![][server_name]

3. Nom de la base de données : tapez le nom d’une base de données sur le serveur que vous venez d’indiquer.
4. Nom du compte utilisateur Server : tapez le nom d’utilisateur d’un compte disposant des autorisations d’accès de la base de données.
5. Mot de passe utilisateur Server : fournir le mot de passe pour le compte d’utilisateur spécifié.
6. Accepter n’importe quel certificat du serveur : utilisez cette option (moins sécurisée) si vous souhaitez ignorer Examinez le certificat de site avant de lire vos données.
7. Requête de base de données : entrez une instruction SQL qui décrit les données que vous souhaitez lire. Dans ce cas, nous lira des données à partir de la table Product à l’aide de la requête suivante.


```SQL
SELECT ProductKey, EnglishProductName, StandardCost,
        ListPrice, Size, Weight, DaysToManufacture,
        Class, Style, Color
FROM dbo.DimProduct;
```

![][reader_properties]

### <a name="step-4"></a>Étape 4

1. Exécuter l’expérience en cliquant sur Exécuter dans la zone de dessin expérience.
2. Une fois l’expérience, le module de lecteur aura une coche verte pour indiquer qu’elle est terminée. Notez également le terminé état d’exécution dans le coin supérieur droit.

![][run]

3. Pour afficher les données importées, cliquez sur le port de sortie en bas de la voiture dataset et sélectionnez visualiser.


## <a name="create-train-and-score-a-model"></a>Créer, former et score un modèle

Vous pouvez désormais utiliser ce dataset pour :

- Créer un modèle : traiter des données et de définir des fonctionnalités
- Former le modèle : choisir et appliquer un algorithme d’apprentissage
- Score général et tester le modèle : prévoir nouveau prix de bicyclette


![][model]

Pour en savoir plus sur la création, former, score et tester une machine utilisation du modèle d’apprentissage [créer expérimenter didacticiel][].

## <a name="write-data-to-azure-sql-data-warehouse"></a>Écrire des données dans le magasin de données SQL Azure

Nous écrit les résultats au tableau ProductPriceForecast dans la base de données AdventureWorksDW.

### <a name="step-1"></a>Étape 1

Recherchez le module Writer dans la palette de jeux de données et les modules à gauche de la zone de dessin expérience. Faites glisser le module vers la zone de dessin expérience.

![][drag_writer]

### <a name="step-2"></a>Étape 2

Sélectionnez le module Writer et remplissez le volet de propriétés.

1. Sélectionnez la base de données SQL Azure comme Destination de données.
2. Nom du serveur de base de données : tapez le nom du serveur. Vous pouvez utiliser le [portail Azure][] pour trouver cette.
3. Nom de la base de données : tapez le nom d’une base de données sur le serveur que vous venez d’indiquer.
4. Nom du compte utilisateur Server : tapez le nom d’utilisateur d’un compte disposant des autorisations d’écriture pour la base de données.
5. Mot de passe utilisateur Server : fournir le mot de passe pour le compte d’utilisateur spécifié.
6. Accepter n’importe quel certificat de serveur (non sécurisé) : sélectionnez cette option si vous ne voulez pas afficher le certificat.
7. Liste séparées par des virgules des colonnes à être enregistrés : fournir une liste des colonnes dataset ou résultat que vous souhaitez copier.
8. Nom de table de données : indiquez le nom de la table de données.
9. Liste séparées par des virgules des colonnes de table de données : spécifier les noms de colonne à utiliser dans la nouvelle table. Les noms de colonne peuvent être différents de ceux présentés dans le groupe de données source, mais vous devez indiquer le même nombre de colonnes ici que vous définissez pour la table de sortie.
10. Nombre de lignes écrites par une opération SQL Azure : vous pouvez configurer le nombre de lignes qui sont écrits dans une base de données SQL en une seule opération.

![][writer_properties]

### <a name="step-3"></a>Étape 3

1. Exécuter l’expérience en cliquant sur Exécuter dans la zone de dessin expérience.
2. Une fois l’expérience, tous les modules aura une coche verte pour indiquer qu’elles sont terminées.

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir d’autres conseils de développement, voir [vue d’ensemble du développement Data Warehouse SQL][].

<!--Image references-->

[drag_reader]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-drag-reader.png
[server_name]: ./media/sql-data-warehouse-integrate-azure-machine-learning/dw-server-name.png
[reader_properties]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-reader-properties.png
[run]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-finished-running.png
[model]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-create-train-score-model.png
[drag_writer]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-drag-writer.png
[writer_properties]: ./media/sql-data-warehouse-integrate-azure-machine-learning/ml-writer-properties.png

<!--Article references-->

[Vue d’ensemble du développement Data Warehouse SQL]: ./sql-data-warehouse-overview-develop.md
[Créer expérience didacticiel]: https://azure.microsoft.com/documentation/articles/machine-learning-create-experiment/
[Introduction à la formation sur Azure de l’ordinateur]: https://azure.microsoft.com/documentation/articles/machine-learning-what-is-machine-learning/
[Machine Azure apprentissage Studio]: https://studio.azureml.net/Home
[Portail Azure]: https://portal.azure.com/

<!--MSDN references-->

<!--Other Web references-->

[Azure Machine Learning documentation]: http://azure.microsoft.com/documentation/services/machine-learning/
