   <properties
   pageTitle="Charger les données dans SQL Azure Data Warehouse | Microsoft Azure"
   description="Découvrez les scénarios courants pour les données de chargement dans SQL Data Warehouse. Celles-ci incluent l’utilisation PolyBase, stockage d’objets blob Azure, fichiers plats et envoi de disque. Vous pouvez également utiliser les outils tiers."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="07/12/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# <a name="load-data-into-azure-sql-data-warehouse"></a>Charger les données dans le magasin de données SQL Azure

Un résumé des options de scénario et recommandations pour charger les données SQL Data Warehouse.

La partie plus difficile de chargement des données est généralement préparer les données pour la charge. Azure simplifie le chargement en utilisant stockage blob Azure comme base de données courantes pour la plupart des services et Azure Data Factory pour harmoniser communications et déplacement des données entre les services Azure. Ces processus sont intégrés avec la technologie PolyBase qui utilise un traitement hautement parallèle (MPP) pour charger les données en parallèle à partir du stockage blob Azure dans Data Warehouse SQL. 

Pour des didacticiels qui chargent des bases de données exemples, voir [charger les bases de données exemples][].

## <a name="load-from-azure-blob-storage"></a>Charger depuis le stockage blob Azure
Le moyen le plus rapide pour importer des données dans SQL Data Warehouse consiste à utiliser PolyBase pour charger les données depuis le stockage blob Azure. PolyBase utilise de Data Warehouse SQL traitement hautement parallèle (MPP) pour charger les données en parallèle à partir du stockage blob Azure. Pour utiliser PolyBase, vous pouvez utiliser les commandes T-SQL ou un pipeline Azure Data Factory.

### <a name="1-use-polybase-and-t-sql"></a>1. Utilisez PolyBase et T-SQL

Résumé du processus de chargement :

2. Mettre en forme vos données en tant que UTF-8 dans la mesure où PolyBase ne prend pas en charge UTF-16.
2. Déplacer vos données dans le stockage blob Azure et stockez-le dans des fichiers texte.
3. Configurer des objets externe dans SQL Data Warehouse pour définir l’emplacement et le format des données
4. Exécuter une commande T-SQL pour charger les données en parallèle dans une nouvelle table de base de données.

<!-- 5. Schedule and run a loading job. --> 

Pour un didacticiel, voir [charger les données à partir du stockage blob Azure SQL Data Warehouse (PolyBase)][].

### <a name="2-use-azure-data-factory"></a>2. Utilisation des données Azure usine

Un moyen simple d’utiliser PolyBase, vous pouvez créer un pipeline Azure Data Factory qui utilise PolyBase pour charger les données depuis le stockage blob Azure dans Data Warehouse SQL. Il s’agit rapide pour configurer étant donné que vous n’avez pas besoin définir les objets T-SQL. Si vous avez besoin interroger les données externes sans l’importer, utilisez T-SQL. 

Résumé du processus de chargement :

2. Mettre en forme vos données en tant que UTF-8 dans la mesure où PolyBase ne prend pas en charge UTF-16.
2. Déplacer vos données dans le stockage blob Azure et stockez-le dans des fichiers texte.
3. Créer un pipeline Azure Data Factory pour les données d’acquisition. Utilisez l’option PolyBase.
4. Planifier et organiser le pipeline.

Pour un didacticiel, voir [charger les données à partir du stockage blob Azure SQL Data Warehouse (Azure Data Factory)][].


## <a name="load-from-sql-server"></a>Charger à partir de SQL Server
Pour charger les données à partir de SQL Server vers SQL Data Warehouse, vous pouvez utiliser Integration Services (SSIS), transférer des fichiers plat ou disques d’expédition à Microsoft. En lecture à voir un résumé des différentes chargement des processus et des liens vers des didacticiels.

Pour planifier une migration complète des données à partir de SQL Server vers Data Warehouse SQL, consultez la [vue d’ensemble de la Migration][]. 

### <a name="use-integration-services-ssis"></a>Utiliser Integration Services (SSIS)
Si vous utilisez déjà des packages Integration Services (SSIS) pour charger dans SQL Server, vous pouvez mettre à jour vos packages afin d’utiliser SQL Server comme source et SQL Data Warehouse comme destination. Il s’agit rapide et facile à faire, et est un bon choix si vous ne voulez pas migrer votre processus de chargement pour utiliser les données déjà dans le cloud. Le compromis est que la charge seront plus lente qu’avec PolyBase car ce SSIS n’effectue pas la charge en parallèle.

Résumé du processus de chargement :

1. Réviser votre package Integration Services pour qu’il pointe vers l’instance SQL Server pour la source et la base de données SQL Data Warehouse pour la destination.
2. Migrer votre schéma vers SQL Data Warehouse, si ce n’est pas fait déjà.
3. Modifier le mappage dans vos packages utiliser uniquement les types de données prises en charge par SQL Data Warehouse.
3. Planifier et exécuter le package.

Pour un didacticiel, voir [charger les données à partir de SQL Server à Azure SQL données Warehouse (SSIS)][].

### <a name="use-azcopy-recommended-for--10-tb-data"></a>Utiliser AZCopy (recommandé pour les données < 10 To)
Si le format de données est < 10 To, vous pouvez exporter les données à partir de SQL Server vers des fichiers plats, copier les fichiers à Azure blob storage et ensuite utiliser PolyBase pour charger les données dans Data Warehouse SQL

Résumé du processus de chargement :

1. Utiliser l’utilitaire de ligne de commande bcp pour exporter des données à partir de SQL Server aux fichiers plats.
2. Utiliser l’utilitaire de ligne de commande AZCopy pour copier des données à partir de fichiers plats à Azure blob storage.
3. Utilisez PolyBase pour charger dans SQL Data Warehouse.

Pour un didacticiel, voir [charger les données à partir du stockage blob Azure SQL Data Warehouse (PolyBase)][].

### <a name="use-bcp"></a>Utilisez le programme bcp
Si vous avez une petite quantité de données, vous pouvez utiliser bcp pour charger directement dans le magasin de données SQL Azure.

Résumé du processus de chargement :
1. Utiliser l’utilitaire de ligne de commande bcp pour exporter des données à partir de SQL Server aux fichiers plats.
2. Utiliser bcp pour charger les données à partir de fichiers plats directement vers SQL Data Warehouse.

Pour un didacticiel, voir [charger les données à partir de SQL Server dans SQL Azure Data Warehouse (bcp)][].


### <a name="use-importexport-recommended-for--10-tb-data"></a>Utilisez importer/exporter (recommandé pour les données > 10 To)
Si la taille de vos données est > 10 To et que vous voulez déplacer vers Azure, nous vous recommandons d’utiliser notre disque livraison service [Importer/exporter][]. 

Résumé des processus de chargement
2. Utiliser l’utilitaire de ligne de commande bcp pour exporter des données à partir de SQL Server à plats fichiers sur des disques transférables.
3. Envoyer les disques à Microsoft.
4. Microsoft charge les données de Data Warehouse SQL

## <a name="load-from-hdinsight"></a>Charger à partir de HDInsight
Data Warehouse SQL prend en charge le chargement des données à partir de HDInsight via PolyBase. Le processus est différente de celle du chargement des données depuis le stockage Blob Azure : à l’aide de PolyBase pour vous connecter à HDInsight pour charger les données. 

### <a name="1-use-polybase-and-t-sql"></a>1. Utilisez PolyBase et T-SQL

Résumé du processus de chargement :

2. Mettre en forme vos données en tant que UTF-8 dans la mesure où PolyBase ne prend pas en charge UTF-16.
2. Déplacer vos données dans HDInsight et stockez-le dans des fichiers texte format ORC ou Parquet.
3. Configurer des objets externe dans SQL Data Warehouse pour définir l’emplacement et le format des données.
4. Exécuter une commande T-SQL pour charger les données en parallèle dans une nouvelle table de base de données.

Pour un didacticiel, voir [charger les données à partir du stockage blob Azure SQL Data Warehouse (PolyBase)][].

## <a name="recommendations"></a>Recommandations

La plupart des partenaires ont chargement de solutions. Pour en savoir plus, voir une liste de nos [partenaires de solutions][]. 

Si vos données proviennent d’une source non relationnelles et que vous souhaitez charger dans SQL Data Warehouse vous devrez transformez-les en lignes et colonnes avant de le charger. Les données transformées ne doivent pas être stockés dans une base de données, il peut être stocké dans des fichiers texte.

Créer des statistiques sur les données que vous venez chargées. Azure SQL Data Warehouse ne prend pas encore prise en charge automatique créer ou mettre à jour les statistiques automatiquement chaque année.  Afin d’obtenir les meilleures performances de vos requêtes, il est important de créer des statistiques sur toutes les colonnes de toutes les tables après le premier chargement ou des modifications substantielles apparaissent dans les données.  Pour plus d’informations, voir [statistiques][].


## <a name="next-steps"></a>Étapes suivantes
Pour obtenir d’autres conseils de développement, consultez la [vue d’ensemble du développement][].

<!--Image references-->

<!--Article references-->
[Charger des données depuis le stockage blob Azure SQL Data Warehouse (PolyBase)]: ./sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md
[Charger des données depuis le stockage blob Azure SQL Data Warehouse (Azure Data Factory)]: ./sql-data-warehouse-load-from-azure-blob-storage-with-data-factory.md
[Charger les données à partir de SQL Server à Azure SQL données Warehouse (SSIS)]: ./sql-data-warehouse-load-from-sql-server-with-integration-services.md
[Charger les données à partir de SQL Server dans SQL Azure Data Warehouse (bcp)]: ./sql-data-warehouse-load-from-sql-server-with-bcp.md
[Load data from SQL Server to Azure SQL Data Warehouse (AZCopy)]: ./sql-data-warehouse-load-from-sql-server-with-azcopy.md

[Charger des bases de données exemple]: ./sql-data-warehouse-load-sample-databases.md
[Vue d’ensemble de la migration]: ./sql-data-warehouse-overview-migrate.md
[partenaires de solutions]: ./sql-data-warehouse-partner-business-intelligence.md
[vue d’ensemble de développement]: ./sql-data-warehouse-overview-develop.md
[Statistiques]: ./sql-data-warehouse-tables-statistics.md

<!--MSDN references-->

<!--Other Web references-->
[Importer/exporter]: https://azure.microsoft.com/documentation/articles/storage-import-export-service/
