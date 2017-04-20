<properties
    pageTitle="Conseils pour le réglage des performances de base de données SQL | Microsoft Azure"
    description="Conseils pour les performances de base de données SQL Azure par le biais d’évaluation et d’amélioration du produit."
    services="sql-database"
    documentationCenter=""
    authors="v-shysun"
    manager="felixwu"
    editor=""
    keywords="SQL réglage des performances, base de données réglage des performances, conseils, d’optimisation des performances sql optimisation des performances de base de données sql"/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="v-shysun"/>

# <a name="sql-database-performance-tuning-tips"></a>Conseils de réglage de performances de base de données SQL
Vous pouvez modifier le [niveau de service](sql-database-service-tiers.md) d’une base de données unique ou augmenter l’eDTUs d’un pool de base de données élastique à tout moment pour améliorer les performances, mais vous voudrez peut-être identifier les opportunités pour améliorer et optimiser les performances des requêtes tout d’abord. Index manquants et requêtes mal optimisés quelques raisons courantes d’un problème de base de données. Cet article fournit des conseils pour les performances de base de données SQL.

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="steps-to-evaluate-and-tune-database-performance"></a>Étapes à suivre pour évaluer et optimiser les performances de base de données
1.  Dans le [Portail Azure](https://portal.azure.com), cliquez sur **bases de données SQL**et sélectionnez la base de données, puis utilisez le graphique d’analyse pour rechercher des ressources approche leur maximum. Consommation DTU est affichée par défaut. Cliquez sur **Modifier** pour modifier la plage horaire et les valeurs affichées.
2.  [Aperçu des performances de requête](sql-database-query-performance.md) permet d’évaluer les requêtes à l’aide de DTUs, puis utilisez [Le Conseiller de base de données SQL](sql-database-advisor.md) pour afficher les recommandations pour la création et suppression d’index, paramétrage de requêtes et résolution des problèmes de schéma.
3.  Vous pouvez utiliser des vues de gestion dynamiques (DMV), les événements étendue (Xevents) et la banque de requête dans SSMS pour obtenir les paramètres de performances en temps réel. Consultez la [rubrique de guide de performances](sql-database-performance-guidance.md) de surveillance et optimisation des conseils détaillées.


    > [AZURE.IMPORTANT] Il est recommandé d’utiliser toujours la dernière version de Management Studio doit pour rester synchronisé avec les mises à jour Microsoft Azure et base de données SQL. [Mettre à jour de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).


## <a name="steps-to-improve-database-performance-with-more-resources"></a>Étapes à suivre pour améliorer les performances de base de données avec davantage de ressources
1.  Pour les bases de données individuelles, vous pouvez [Modifier les niveaux de service](sql-database-scale-up.md) à la demande pour améliorer les performances de base de données.
2.  Pour plusieurs bases de données, envisagez d’utiliser [des pools élastique de base de données](sql-database-elastic-pool-guidance.md) à l’échelle automatiquement des ressources.
