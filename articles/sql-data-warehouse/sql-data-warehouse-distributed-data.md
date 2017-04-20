<properties
   pageTitle="Distribué des données et options de table pour les systèmes hautement parallèle traitement (MPP) de Data Warehouse SQL et Parallel Data Warehouse | Microsoft Azure"
   description="Découvrez comment les données sont réparties pour considérablement en parallèle traitement (MPP) et les options pour la distribution de tables dans SQL Azure Data Warehouse et Parallel Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="10/10/2016"
   ms.author="barbkess"/>


# <a name="distributed-data-and-distributed-tables-for-massively-parallel-processing-mpp"></a>Données distribuées et des tables distribués pour considérablement en parallèle traitement (MPP)

Découvrez comment les données utilisateur étant distribuées au magasin de données SQL Azure et Parallel Data Warehouse, qui sont des systèmes hautement parallèle traitement (MPP) de Microsoft. Conception votre data warehouse pour utiliser efficacement les données distribuées vous aide à atteindre les avantages de l’architecture MPP de traitement de requête. Plusieurs choix de conception de base de données peut avoir un impact sur l’amélioration des performances des requêtes.  

>[AZURE.NOTE] Azure SQL Data Warehouse et Parallel Data Warehouse utilisent la même conception considérablement en parallèle traitement (MPP), mais ils ont quelques différences en raison de la plateforme sous-jacente. SQL Data Warehouse est une plateforme en tant que Service (PaaS) qui s’exécute sur Azure. Parallel Data Warehouse s’exécute sur Analytique plateforme système (PA) qui est un appareil local qui s’exécute sur Windows Server.

## <a name="what-is-distributed-data"></a>Que sont les données distribuées ?

Dans SQL Data Warehouse et Parallel Data Warehouse, données distribuées fait référence à des données utilisateur qui sont stockées dans plusieurs emplacements dans le système MPP. Chacun de ces emplacements fonctionne comme un stockage indépendantes et l’unité de traitement qui s’exécute des requêtes sur sa partie des données. Données distribuées sont fondamentales pour exécuter des requêtes en parallèle pour obtenir des performances élevées pour les requêtes.

Pour distribuer des données, data warehouse attribue chaque ligne d’une table utilisateur dans un emplacement spécifique distribué.  Vous pouvez distribuer des tables avec une méthode de distribution de hachage ou d’une méthode cyclique. La méthode de distribution spécifiée dans l’instruction CREATE TABLE. 

## <a name="hash-distributed-tables"></a>Tables distribué hachage
  
Le diagramme suivant illustre comment complet (table non distribuées) est stocké sous forme de tableau distribué hachage. Une fonction déterministe affecte chaque ligne doit appartenir à une distribution. Dans la définition de la table, une des colonnes est désignée comme étant la colonne distribution. La fonction de hachage utilise la valeur dans la colonne de distribution pour affecter chaque ligne pour une distribution.

Il existe considérations relatives aux performances pour la sélection d’une colonne de distribution, telles que distinction, données inclinaison et les types de requêtes s’exécutent sur le système.
  
![Table distribué] (media/sql-data-warehouse-distributed-data/hash-distributed-table.png "Table distribué")  
  
-   Chaque ligne appartient à une distribution.  
  
-   Un algorithme de hachage déterministe affecte chaque ligne pour une distribution.  
  
-   Le nombre de lignes de tableau par distribution varie telle qu’indiquée par les différentes tailles de tables.

## <a name="round-robin-distributed-tables"></a>Tables distribués alternées

Une table distribuée cyclique distribue les lignes en assignant chaque ligne pour une distribution de manière séquentielle. Il est rapide charger les données dans une table cyclique, mais les performances de la requête peuvent être plus lente.  Participation à une table cyclique généralement nécessite remaniement les lignes pour permettre à la requête produire un résultat précis, ce qui prend du temps.

## <a name="distributed-storage-locations-are-called-distributions"></a>Emplacements de stockage distribué sont appelés répartitions

Chaque emplacement distribué est appelé une distribution. Lorsqu’une requête s’exécute en parallèle, chaque distribution exécute une requête SQL sur sa partie des données. SQL Data Warehouse utilise la base de données SQL pour exécuter la requête. Parallel Data Warehouse utilise SQL Server pour exécuter la requête. Cette conception architecture sans partage est essentielle pour la réalisation informatique parallèle horizontale.

### <a name="can-i-view-the-distributions"></a>Puis-je afficher les répartitions ?

Chaque distribution possède un ID de distribution et est visible dans les vues système appartenant aux Data Warehouse SQL et Parallel Data Warehouse. Vous pouvez utiliser l’ID de distribution pour résoudre les problèmes de performances des requêtes et autres problèmes. Pour obtenir la liste des vues du système, voir la [vue système MPP](sql-data-warehouse-reference-tsql-statements.md).

## <a name="difference-between-a-distribution-and-a-compute-node"></a>Différence entre une distribution et un nœud de calcul

Une distribution est l’unité de base pour le stockage de données distribuées et le traitement des requêtes en parallèle. Répartitions sont regroupées en nœuds de calcul. Une ou plusieurs répartitions effectue le suivi de chaque nœud de calcul.  

-   Système de plateforme Analytique utilise des nœuds de calcul comme un composant central des capacités matériel architecture et horizontale. Il utilise toujours huit répartitions par nœud de calcul, comme indiqué dans le diagramme pour les tableaux distribué hachage. Le nombre de nœuds de calcul et par conséquent, le nombre de distribution, sont déterminés par le nombre de nœuds de calcul que vous achetez pour l’application. Par exemple, si vous achetez huit nœuds de calcul, vous obtenez 64 répartitions (8 nœuds x 8 répartitions/nœud de calcul). 

-   SQL Data Warehouse contient un nombre constant de 60 répartitions et un numéro flexible des nœuds de calcul. Les nœuds de calcul sont implémentées avec les ressources de calcul et de stockage Azure. Le nombre de nœuds de calcul peut varier selon la charge de travail principal service et la capacité informatique (DWUs) que vous spécifiez pour le data warehouse. Lorsque le nombre de nœuds de calcul est modifiée, le nombre de répartitions par nœud de calcul change également. 

### <a name="can-i-view-the-compute-nodes"></a>Puis-je afficher les nœuds de calcul ?

Chaque nœud de calcul a un ID de nœud et est visible dans les vues système appartenant aux Data Warehouse SQL et Parallel Data Warehouse.  Vous pouvez voir le nœud de calcul en recherchant la colonne node_id : dans les vues système dont le nom commence par sys.pdw_nodes. Pour obtenir la liste des vues du système, voir la [vue système MPP](sql-data-warehouse-reference-tsql-statements.md).

## <a name="Replicated"></a>Répliquées Tables Parallel Data Warehouse 
  
S’applique à : Parallel Data Warehouse

Outre l’utilisation des tables distribués, Parallel Data Warehouse inclut une option pour dupliquer des tables. Une *table répliquée* est une table qui est stockée dans son intégralité sur chaque nœud de calcul. Réplication d’une table évite d’avoir à transférer ses lignes de table entre les nœuds de calcul avant d’utiliser la table dans une jointure ou une agrégation. Les tables répliquées ne sont réalisables avec les tables de petite taille en raison de l’espace de stockage supplémentaire requis pour stocker la table complète sur chaque nœud de calcul.  
  
L’illustration ci-dessous présente une table répliquée qui se trouve sur chaque nœud de calcul. La table répliquée est stockée sur tous les disques affectés au nœud de calcul. Cette stratégie disque est activée à l’aide de groupes de fichiers SQL Server.  
  
![Table répliqué] (media/sql-data-warehouse-distributed-data/replicated-table.png "Table répliqué") 
  
## <a name="next-steps"></a>Étapes suivantes
  
Pour utiliser efficacement les tables distribués, voir [distribution tables SQL Data Warehouse](sql-data-warehouse-tables-distribute.md)  
  



  
