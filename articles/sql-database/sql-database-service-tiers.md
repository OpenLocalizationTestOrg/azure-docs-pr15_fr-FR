<properties
    pageTitle="Options et des performances de base de données SQL : niveaux de Service | Microsoft Azure"
    description="Comparez les fonctionnalités de la continuité des performances et entreprise base de données SQL des niveaux de service pour équilibrer les coûts et les capacités mise à l’échelle."
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
    ms.workload="data-management"
    ms.date="08/10/2016"
    ms.author="carlrab"/>

# <a name="sql-database-options-and-performance-understand-whats-available-in-each-service-tier"></a>Options de base de données SQL et de performances : comprendre ce qui est disponible dans chaque niveau de service

[Base de données SQL Azure](sql-database-technical-overview.md) propose trois niveaux de service avec plusieurs niveaux de performance à gérer différentes charges de travail. Chaque niveau de performance fournit un ensemble croissant de ressources conçus pour offrir débit toujours plus rapidement. Vous pouvez gérer chaque base de données dans sa propre [niveau de service](sql-database-service-tiers.md#standalone-database-service-tiers-and-performance-levels) avec son propre niveau de performance. Vous pouvez également gérer plusieurs bases de données dans un [pool élastique](sql-database-service-tiers.md#elastic-pool-service-tiers-and-performance-in-edtus) avec un ensemble de ressources partagées. Les ressources disponibles pour les bases de données autonome sont exprimés en termes d’unités de Transaction de base de données (DTUs) et pour des pools élastiques en termes d’élastique DTUs ou eDTUs. Pour plus d’informations sur DTUs et eDTUs, voir [qu’est un DTU](sql-database-what-is-a-dtu.md). 

Dans les deux cas, les niveaux de service incluent **simple**, **Standard**et **Premium**. Options de base de données dans les niveaux sont similaires pour les bases de données autonome et pools élastiques, mais pour des pools élastiques d’autres aspects. Cet article fournit des détails des niveaux de service pour les bases de données autonome et pools élastiques.

## <a name="service-tiers-and-database-options"></a>Niveaux de service et les options de base de données
Base, Standard, et niveaux de service Premium tous ont une disponibilité SLA de 99,99 % et offre des performances prévisibles, options de continuité flexibles, les fonctionnalités de sécurité et de facturation horaire. Le tableau suivant fournit des exemples des niveaux meilleures adaptés aux charges de travail d’application.

| Niveau de service | Charges de travail cibles |
|---|---|
| **Base** | Mieux adaptée à une base de données petite, généralement une seule opération active à un moment donné de prise en charge. Exemples de bases de données utilisées pour le développement ou du test ou petite applications peu utilisées. |
| **Standard** | L’option accéder à la plupart des applications cloud, prise en charge de plusieurs requêtes simultanées. Exemples d’applications web ou de groupe de travail. |
| **Premium** | Conçu pour important volume de transactions, prise en charge de nombreux utilisateurs simultanées et exigeant le niveau de continuité d’activité. Bases de données prenant en charge les applications critiques sont des exemples. |

>[AZURE.NOTE] Éditions Web et entreprise sont annulées. Lisez le [FAQ coucher de soleil](https://azure.microsoft.com/pricing/details/sql-database/web-business/) si vous envisagez de continuer à utiliser les éditions Web et de l’entreprise.

## <a name="standalone-database-service-tiers-and-performance-levels"></a>Niveaux de service de base de données autonome et performances
Pour les bases de données autonome, il existe plusieurs niveaux de performance au sein de chaque niveau de service. Vous avez la possibilité de choisir le niveau qui correspond le mieux à des demandes de votre charge de travail. Si vous avez besoin de mettre à l’échelle vers le haut ou vers le bas, vous pouvez facilement modifier les niveaux de votre base de données. Pour plus d’informations, voir [modifier des niveaux de Service de base de données et de performances](sql-database-scale-up.md) .

Caractéristiques de performances répertoriés ici s’appliquent aux bases de données créés à l’aide de [V12 de base de données SQL](sql-database-v12-whats-new.md). Quel que soit le nombre de bases de données hébergées, votre base de données obtenait un ensemble de garantie de ressources et les caractéristiques de performances de votre base de données ne sont pas affectés.

[AZURE.INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

>[AZURE.NOTE] Pour une explication détaillée de toutes les autres lignes dans ce tableau de niveaux de service, voir [fonctionnalités de niveau de Service et les limites](sql-database-performance-guidance.md#service-tier-capabilities-and-limits).

## <a name="elastic-pool-service-tiers-and-performance-in-edtus"></a>Niveaux de service pool élastique et les performances dans eDTUs
Outre la création et la mise à l’échelle d’une base de données autonome, vous avez également la possibilité de gérer plusieurs bases de données au sein d’un [pool élastique](sql-database-elastic-pool.md). Toutes les bases de données dans un pool élastique partagent un ensemble commun de ressources. Les caractéristiques de performance sont mesurées en *Élastique unités de Transaction de base de données* (eDTUs). Comme les bases de données autonome, pools se présentent sous trois niveaux de service : **simple**, **Standard**et **Premium**. Pour des pools, ces niveaux de trois service toujours définir les limites de performances et des fonctionnalités.

Pools permettent de bases de données à partager et consomme des ressources DTU sans avoir besoin d’attribuer un niveau de performance spécifiques à chaque base de données dans le pool. Par exemple, une base de données autonome dans un pool Standard peuvent-ils d’utiliser 0 eDTUs à l’eDTU maximale de la base de données que vous configurez lorsque vous configurez le pool. Pools autoriser plusieurs bases de données avec différentes charges de travail à utiliser efficacement eDTU ressources disponibles pour l’ensemble du pool. Pour plus d’informations, voir [Considérations prix et de performances d’un pool élastique](sql-database-elastic-pool-guidance.md) .

Le tableau suivant décrit les caractéristiques de niveaux de service pool.

[AZURE.INCLUDE [SQL DB service tiers table for elastic pools](../../includes/sql-database-service-tiers-table-elastic-db-pools.md)]

Chaque base de données au sein d’un pool respecte également les caractéristiques de base de données autonome pour ce niveau. Par exemple, le pool de base est limité pour les sessions max par pool de 4800-28800, mais une base de données individuel au sein d’un pool base a une limite de base de données de 300 sessions.

## <a name="choosing-a-service-tier"></a>Choix d’un niveau de service

Pour choisir un niveau de service, commencez par déterminer si la base de données doit être une base de données autonome ou faire partie d’un pool élastique. 

### <a name="choosing-a-service-tier-for-a-standalone-database"></a>Choix d’un niveau de service pour une base de données autonome

Pour choisir un niveau de service pour une base de données autonome, commencez par déterminer les fonctionnalités de base de données dont vous avez besoin pour choisir l’édition de base de données SQL :

- Taille de la base de données (2 Go maximum de base, 250 Go maximum pour Standard et 500 à 1 To maximum Premium - selon le niveau de performance)
- Période de rétention de sauvegarde de base de données (7 jours pour Basic, 35 jours pour Standard et 35 jours pour Premium)

Une fois que vous avez déterminé l’édition de base de données SQL, vous êtes prêt à déterminer le niveau de performance pour la base de données (nombre de DTUs). Vous pouvez deviner, puis [mettre à l’échelle vers le haut ou vers le bas dynamiquement](sql-database-scale-up.md) selon l’expérience réel. Vous pouvez également utiliser la [Calculatrice DTU](http://dtucalculator.azurewebsites.net/) estimer le nombre de DTUs nécessité. 

### <a name="choosing-a-service-tier-for-an-elastic-database-pool"></a>Choix d’un niveau de service pour un pool élastique de base de données.

Pour choisir le niveau de service pour un pool de base de données élastique, commencez par déterminer les fonctionnalités de base de données dont vous avez besoin pour choisir le niveau de service pour votre pool.

- Taille de la base de données (2 Go pour Basic, 250 Go pour Standard et 500 Go pour Premium)
- Période de rétention de sauvegarde de base de données (7 jours pour Basic, 35 jours pour Standard et 35 jours pour Premium)
- Nombre de bases de données par pool (400 Basic, 400 pour Standard et 50 Premium)
- Stockage maximum par pool (117 Go pour Basic, 1200 pour Standard et 750 Premium)

Une fois que vous avez déterminé le niveau de service pour votre pool, vous êtes prêt à déterminer le niveau de performance pour le pool (eDTUs). Vous pouvez deviner et puis [évoluer ou ajuster dynamiquement vers le bas](sql-database-elastic-pool-manage-portal.md#change-performance-settings-of-a-pool) selon expérience réel. Vous pouvez utiliser la [Calculatrice DTU](http://dtucalculator.azurewebsites.net/) pour rapprocher le nombre de DTUs nécessaire pour chaque base de données dans un pool pour aider à déterminer la limite supérieure pour le pool.

## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur les tarifs à ces niveaux de [Prix de base de données SQL](https://azure.microsoft.com/pricing/details/sql-database/).
- Découvrez les détails des [pools élastiques](sql-database-elastic-pool-guidance.md) et [Considérations prix et des performances pour des pools élastiques](sql-database-elastic-pool-guidance.md).
- Découvrez comment [moniteur, gérer et redimensionner des pools élastiques](sql-database-elastic-pool-manage-portal.md) et [surveiller les performances des bases de données autonome](sql-database-single-database-monitor.md).
- Maintenant que vous savez sur les niveaux de base de données SQL, les utiliser avec un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/) et apprenez [à créer votre première base de données SQL](sql-database-get-started.md).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Modèles de conception pour les applications de SaaS clients multiples à l’aide de la base de données SQL Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md)
- [Cours vidéo Microsoft Virtual Academy sur les fonctionnalités de base de données élastique dans la base de données SQL Azure](https://mva.microsoft.com/en-US/training-courses/elastic-database-capabilities-with-azure-sql-db-16554)
