<properties
    pageTitle="Explorer des données dans les tables Hive avec requêtes Hive | Microsoft Azure"
    description="Explorer des données dans les tables Hive à l’aide de requêtes Hive."
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun"  />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="bradsev" />

# <a name="explore-data-in-hive-tables-with-hive-queries"></a>Explorer des données dans les tables Hive avec Hive requêtes

Ce document fournit des exemples de scripts Hive qui sont utilisés pour explorer des données dans des tableaux Hive dans un cluster HDInsight Hadoop.

Les liens de **menu** suivants vers des rubriques qui décrivent comment utiliser les outils pour explorer des données à partir de différents environnements de stockage.

[AZURE.INCLUDE [cap-explore-data-selector](../../includes/cap-explore-data-selector.md)]

## <a name="prerequisites"></a>Conditions préalables
Cet article suppose que vous avez :

* Créer un compte de stockage Azure. Si vous avez besoin d’instructions, voir [créer un compte de stockage Azure](../storage/storage-create-storage-account.md#create-a-storage-account)
* Mise en service un cluster Hadoop personnalisée avec le service HDInsight. Si vous avez besoin d’instructions, voir [Personnaliser Azure HDInsight Hadoop Clusters pour Analytique avancée](machine-learning-data-science-customize-hadoop-cluster.md).
* Les données ont été téléchargées vers tables Hive dans Azure HDInsight Hadoop clusters. Si ce n’est pas le cas, suivez les instructions dans [créer et charger les données aux tables Hive](machine-learning-data-science-move-hive-tables.md) pour télécharger des données aux tables Hive commencer.
* Accès à distance activés pour le cluster. Si vous avez besoin d’instructions, voir [le nœud de tête de Hadoop Cluster l’accès](machine-learning-data-science-customize-hadoop-cluster.md#headnode).
* Si vous avez besoin d’obtenir des instructions sur l’envoi des requêtes Hive, Découvrez [comment envoyer les requêtes ruche](machine-learning-data-science-move-hive-tables.md#submit)

## <a name="example-hive-query-scripts-for-data-exploration"></a>Exemples de scripts Hive requête d’exploration de données

1. Obtenir le nombre d’observations par partition `SELECT <partitionfieldname>, count(*) from <databasename>.<tablename> group by <partitionfieldname>;`

2. Obtenir le nombre d’observations par jour `SELECT to_date(<date_columnname>), count(*) from <databasename>.<tablename> group by to_date(<date_columnname>);`

3. Obtenir les niveaux dans une colonne par catégorie  
    `SELECT  distinct <column_name> from <databasename>.<tablename>`

4. Obtenir le nombre de niveaux dans la combinaison de deux colonnes par catégorie `SELECT <column_a>, <column_b>, count(*) from <databasename>.<tablename> group by <column_a>, <column_b>`

5. Obtenir la distribution pour des colonnes numériques  
    `SELECT <column_name>, count(*) from <databasename>.<tablename> group by <column_name>`

6. Extraire des enregistrements de jointure de deux tables

        SELECT
            a.<common_columnname1> as <new_name1>,
            a.<common_columnname2> as <new_name2>,
            a.<a_column_name1> as <new_name3>,
            a.<a_column_name2> as <new_name4>,
            b.<b_column_name1> as <new_name5>,
            b.<b_column_name2> as <new_name6>
        FROM
            (
            SELECT <common_columnname1>,
                <common_columnname2>,
                <a_column_name1>,
                <a_column_name2>,
            FROM <databasename>.<tablename1>
            ) a
            join
            (
            SELECT <common_columnname1>,
                <common_columnname2>,
                <b_column_name1>,
                <b_column_name2>,
            FROM <databasename>.<tablename2>
            ) b
            ON a.<common_columnname1>=b.<common_columnname1> and a.<common_columnname2>=b.<common_columnname2>

## <a name="additional-query-scripts-for-taxi-trip-data-scenarios"></a>Scripts supplémentaires de la requête pour les scénarios de données de voyage taxi

Exemples de requêtes qui sont spécifiques aux [Données de voyage NYC Taxi](http://chriswhong.com/open-data/foil_nyc_taxi/) scénarios sont également fournis dans [référentiel Github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts). Ces requêtes déjà ont schéma de données spécifiée et êtes prêts à être soumis à exécuter.
