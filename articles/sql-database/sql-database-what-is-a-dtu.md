<properties
    pageTitle="Base de données SQL : Qu’est un DTU ? | Microsoft Azure"
    description="Unité de transaction comprendre quels une Azure SQL base de données est."
    keywords="options de base de données, les performances de base de données"
    services="sql-database"
    documentationCenter=""
    authors="CarlRabeler"
    manager="jhubbard"
    editor="CarlRabeler"/>

<tags
    ms.service="sql-database"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="NA"
    ms.date="09/06/2016"
    ms.author="carlrab"/>

# <a name="explaining-database-transaction-units-dtus-and-elastic-database-transaction-units-edtus"></a>Expliquant élastique unités de Transaction de base de données (eDTUs) et les unités de Transaction de base de données (DTUs)

Cet article explique les unités de Transaction de base de données (DTUs) et élastique unités de Transaction de base de données (eDTUs) et que se passe-t-il lorsque vous avez atteint le maximum DTUs ou eDTUs.  

## <a name="what-are-database-transaction-units-dtus"></a>Que sont les unités de Transaction de base de données (DTUs)

Un DTU est une unité de mesure des ressources qui sont garanties soit disponible pour une base de données SQL Azure autonome à un niveau de performance spécifiques au sein d’un [niveau de service de base de données autonome](sql-database-service-tiers.md#standalone-database-service-tiers-and-performance-levels). Un DTU est une mesure combinée du processeur, la mémoire et données e/s et e/s de journal des transactions avec un rapport de déterminées par une charge de travail de référence OLTP conçu pour être typique de charges de travail OLTP réels. Doubler le DTUs en augmentant le niveau de performance d’une base de données est égale à doubler l’ensemble des ressources disponibles pour cette base de données. Par exemple, une base de données P11 Premium avec DTUs 1750 fournit 350 x DTU plus grande puissance à une base de données simple avec 5 DTUs de traitement. Pour mieux comprendre la méthodologie de la charge de travail de référence OLTP utilisée pour déterminer la fusion DTU, voir [vue d’ensemble du test d’évaluation de base de données SQL](sql-database-benchmark-overview.md).

![Introduction à la base de données SQL : unique DTUs de base de données par niveau et niveau](./media/sql-database-what-is-a-dtu/single_db_dtus.png)

Vous pouvez [Modifier les niveaux de service](sql-database-scale-up.md) à tout moment très peu de temps à votre application (généralement moyenne moins de quatre secondes). Pour de nombreux d’entreprises et d’applications, la possibilité de créer des bases de données et de numérotation de performances de base de données unique vers le haut ou vers le bas à la demande est suffisant, surtout si des modèles d’utilisation sont relativement prévisibles. Mais si vous avez des modèles d’utilisation inattendus, il est difficile à gérer les coûts et votre modèle d’entreprise. Dans ce scénario, vous utilisez un pool élastique avec un certain nombre d’eDTUs.

## <a name="what-are-elastic-database-transaction-units-edtus"></a>Que sont les unités de Transaction de base de données élastique (eDTUs)

Un eDTU est une unité de mesure de l’ensemble des ressources (DTUs) qui peut être partagé entre un ensemble de bases de données sur un serveur SQL Azure - appelé un [pool élastique](sql-database-elastic-pool.png). Pools élastiques fournissent une solution rentable simple pour gérer les objectifs de performance pour plusieurs bases de données les plus diverses et modèles d’utilisation inattendus. Voir [pools élastiques et les niveaux de service](sql-database-service-tiers.md#elastic-pool-service-tiers-and-performance-in-edtus) pour plus d’informations.

![Introduction à la base de données SQL : eDTUs par niveau et niveau](./media/sql-database-what-is-a-dtu/sqldb_elastic_pools.png)

Un pool est remplacé par un nombre défini d’eDTUs, pour un prix défini. Au sein du pool, bases de données individuelles figurent la flexibilité à l’échelle automatique dans définir les paramètres. Surchargé, une base de données peut utiliser plusieurs eDTUs pour répondre à la demande. Bases de données quand les charges légère consomment inférieure et bases de données sans charge ne consomment aucune eDTUs. Mise en service des ressources pour l’ensemble du pool plutôt que des bases de données unique simplifie les tâches d’administration. En outre, vous disposez d’un budget prévisible pour le pool.

EDTUs supplémentaires peuvent être ajoutés à un pool existant et sans interruption de service de base de données sans aucun impact sur les bases de données dans le pool élastique. De même, si eDTUs supplémentaires ne sont plus utiles qu’ils peuvent être supprimés à partir d’un pool existant à tout moment dans le temps. Vous pouvez ajouter ou soustraire des bases de données vers le pool. Si une base de données est mis sous-utilisation des ressources, déplacez-la.

## <a name="how-can-i-determine-the-number-of-dtus-needed-by-my-workload"></a>Comment puis-je déterminer le nombre de DTUs requis par mon travail ?

Si vous cherchez à migrer un existant en local ou SQL Server machine virtuelle la charge de travail à une base de données SQL Azure, vous pouvez utiliser la [Calculatrice DTU](http://dtucalculator.azurewebsites.net/) pour rapprocher le nombre de DTUs nécessité. Pour une charge de travail de base de données SQL Azure existante, vous pouvez utiliser [Aperçu de performances de requête de base de données SQL](sql-database-query-performance.md) pour comprendre votre utilisation des ressources de base de données (DTUs) pour obtenir plus claires comment optimiser votre charge de travail. Vous pouvez également utiliser la vue [sys.dm_db_ resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) pour récupérer les informations de consommation de ressources pour la dernière heure. Par ailleurs, la vue de catalogue [sys.resource_stats](http://msdn.microsoft.com/library/dn269979.aspx) peut également être interrogé pour obtenir les mêmes données pour les 14 derniers jours, bien qu’à une fidélité inférieure des moyennes de cinq minutes.

## <a name="how-do-i-know-if-i-could-benefit-from-an-elastic-pool-of-resources"></a>Comment savoir si je peux bénéficier d’un pool de ressources élastique ?

Pools sont adaptées à un grand nombre de bases de données avec les modèles d’utilisation spécifique. Dans une base de données donné, ce modèle est caractérisé par faible taux d’utilisation avec utilisation relativement peu fréquentes. Base de données SQL évalue l’utilisation des ressources historiques des bases de données dans un serveur de base de données SQL existant automatiquement et recommande la configuration du pool approprié dans le portail Azure. Pour plus d’informations, voir [quand un pool élastique de base de données doit être utilisé ?](sql-database-elastic-pool-guidance.md)

## <a name="what-happens-when-i-hit-my-maximum-dtus"></a>Que se passe-t-il lorsque j’atteint mon DTUs maximales

Niveaux de performances sont étalonnés et régis pour fournir les ressources nécessaires pour exécuter votre charge de travail de base de données jusqu'à atteindre les limites max. autorisées pour votre niveau de performances/de niveau de service sélectionné. Si votre charge de travail est en appuyant sur les limites d’une des limites de données de l’UC/IO/journal IO, vous continuez de recevoir les ressources à la valeur maximale autorisée niveau, mais il est probable que voir latence accrue pour vos requêtes. Ces limites n’entraînent pas des erreurs, mais plutôt une baisse de la charge de travail, à moins d’avoir ralenti devient donc difficiles que requêtes Commençons minutage. Si vous atteignez les limites de sessions/demandes maximale utilisateur simultanées autorisés (threads de travail), vous obtiendrez erreurs explicites. Voir [les limites des ressources de base de données SQL Azure](sql-database-resource-limits.md) pour plus d’informations sur la limite sur les ressources différent du processeur, la mémoire, données e/s et e/s de journal des transactions.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les DTUs et eDTUs disponible pour les bases de données autonome et pour des pools élastiques, voir [niveau de Service](sql-database-service-tiers.md) .
- Voir [les limites des ressources de base de données SQL Azure](sql-database-resource-limits.md) pour plus d’informations sur la limite sur les ressources différent du processeur, la mémoire, données e/s et e/s de journal des transactions.
- Afficher un [Aperçu de performances de requête de base de données SQL](sql-database-query-performance.md) pour comprendre votre consommation (DTUs).
- Voir [vue d’ensemble du test d’évaluation de base de données SQL](sql-database-benchmark-overview.md) pour mieux comprendre la méthodologie de la charge de travail de référence OLTP utilisée pour déterminer la fusion DTU.