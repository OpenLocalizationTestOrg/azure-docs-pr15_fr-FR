<properties
    pageTitle="Analyse des performances de base de données dans la base de données SQL Azure | Microsoft Azure"
    description="Découvrez les options pour l’analyse de votre base de données avec des outils Azure et vues de gestion dynamiques."
    keywords="base de données analyse, des performances de base de données du cloud"
    services="sql-database"
    documentationCenter=""
    authors="CarlRabeler"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="data-management"
    ms.date="09/27/2016"
    ms.author="carlrab"/>

# <a name="monitoring-database-performance-in-azure-sql-database"></a>Analyse des performances de base de données dans la base de données SQL Azure
Surveiller les performances d’une base de données SQL Azure commence par l’utilisation des ressources par rapport au niveau de performance de base de données que vous choisissez de surveillance. Surveillance vous aide à que déterminer si votre base de données a capacités excédentaires ou rencontre des problèmes, car les ressources sont atteint sa limite et puis décider s’il est temps pour ajuster le niveau de performance et un [niveau de service](sql-database-service-tiers.md) de votre base de données. Vous pouvez surveiller votre base de données à l’aide d’outils graphiques dans le [portail Azure](https://portal.azure.com) ou SQL [vues de gestion dynamiques](https://msdn.microsoft.com/library/ms188754.aspx).

## <a name="monitor-databases-using-the-azure-portal"></a>Surveiller les bases de données à l’aide du portail Azure

Dans le [portail Azure](https://portal.azure.com/), vous pouvez surveiller l’utilisation d’une base de données unique en sélectionnant votre base de données et en cliquant sur le graphique de **surveillance** . Cela affiche une fenêtre **métrique** que vous pouvez modifier en cliquant sur le bouton **Modifier le graphique** . Ajoutez les mesures suivantes :

- Pourcentage processeur
- Pourcentage DTU
- Pourcentage IO de données
- Pourcentage de taille de base de données

Une fois que vous avez ajouté les mesures, vous pouvez continuer à les afficher dans le graphique de **surveillance** avec plus d’informations sur la fenêtre **métrique** . Toutes les quatre mesures affichent le pourcentage d’utilisation moyenne par rapport à la **DTU** de votre base de données. Consultez l’article de [niveaux de service](sql-database-service-tiers.md) pour plus d’informations sur DTUs.

![Analyse de niveau de performance de base de données du service.](./media/sql-database-service-tiers/sqldb_service_tier_monitoring.png)

Vous pouvez également configurer des alertes sur les mesures de performances. Cliquez sur le bouton **Ajouter une alerte** dans la fenêtre **métrique** . Suivez l’Assistant pour configurer votre alerte. Vous avez la possibilité pour l’alerte si les mesures dépassent un certain seuil ou si la mesure est inférieur à un certain seuil.

Par exemple, si vous prévoyez la charge de travail sur la croissance de votre base de données, vous pouvez choisir configurer une alerte de messagerie chaque fois que votre base de données atteint 80 % sur n’importe lequel des mesures de performances. Vous pouvez utiliser ceci comme un avertissement au plus tôt pour déterminer quand vous devrez peut-être basculer vers le niveau de performance immédiatement supérieur.

Les indicateurs de performance peut également vous aider à déterminer si vous êtes en mesure de mettre à niveau vers un niveau de performances inférieur. Supposons que vous utilisez une base de données S2 Standard et tous les indicateurs de performance indiquent que la base de données sur moyenne n’utilise pas plus de 10 % à un moment donné. Il est probable que la base de données fonctionnent correctement dans S1 Standard. Toutefois, n’oubliez pas de charges de travail qui PIC ou varient avant de prendre une décision pour atteindre un niveau de performances inférieur.

## <a name="monitor-databases-using-dmvs"></a>Surveiller les bases de données à l’aide de DMV

Les mesures mêmes qui sont présentés dans le portail sont également disponibles via des vues système : [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) dans la base de données logique **maître** de votre serveur et [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) dans la base de données utilisateur. Utilisez **sys.resource_stats** si vous avez besoin analyser les données moins précises sur une période de temps plus longue. Utilisez **sys.dm_db_resource_stats** si vous avez besoin analyser les données plus granulaires dans un intervalle de temps plus petite. Pour plus d’informations, voir [Conseils pour les performances de base de données SQL Azure](sql-database-performance-guidance.md#monitoring-resource-use-with-sysresourcestats).

>[AZURE.NOTE] **Sys.dm_db_resource_stats** renvoie un résultat vide définir lorsqu’il est utilisé dans Web et entreprise edition bases de données sont annulées.

Pour des pools élastique de base de données, vous pouvez surveiller les bases de données individuelles dans le pool avec les techniques décrites dans cette section. Mais vous pouvez également contrôler le pool entier. Pour plus d’informations, voir [moniteur et gérer un pool de base de données élastique](sql-database-elastic-pool-manage-portal.md).
