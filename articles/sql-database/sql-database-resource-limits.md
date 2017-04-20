<properties
    pageTitle="Limites de ressources de base de données SQL Azure"
    description="Cette page décrit certaines limites communes ressource pour la base de données SQL Azure."
    services="sql-database"
    documentationCenter="na"
    authors="CarlRabeler"
    manager="jhubbard"
    editor="monicar" />


<tags
    ms.service="sql-database"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-management"
    ms.date="10/13/2016"
    ms.author="carlrab" />


# <a name="azure-sql-database-resource-limits"></a>Limites de ressources de base de données SQL Azure

## <a name="overview"></a>Vue d’ensemble

Base de données SQL Azure gère les ressources disponibles pour une base de données à l’aide de deux mécanismes différents : **Gouvernance de ressources** et de **L’application de limites**. Cette rubrique décrit ces deux zones principales de gestion des ressources.

## <a name="resource-governance"></a>Gouvernance de ressources
Un des objectifs des niveaux de service Basique, Standard et Premium est pour la base de données SQL Azure se comporte comme si la base de données est en cours d’exécution sur son propre ordinateur, complètement isolé à partir d’autres bases de données. La gouvernance de ressources émule ce comportement. Si l’utilisation des ressources agrégé atteint la maximale de l’UC disponible, la mémoire, e/s journal et ressources de données e/s affectées à la base de données, la gouvernance de ressources sera dans l’exécution des requêtes en file d’attente et affecter des ressources pour les requêtes en file d’attente qu’ils permettent de libérer.

En tant que sur un ordinateur dédié utilisant toutes les ressources disponibles entraîne une exécution plue d’en cours d’exécution des requêtes, ce qui peuvent entraîner des délais d’expiration de commande sur le client. Applications avec une logique de nouvelle tentative agressif et les applications exécuter des requêtes sur la base de données avec une fréquence élevée peuvent rencontrer des messages d’erreur lorsque vous essayez d’exécuter des requêtes nouveau lorsque la limite de demandes simultanées a été atteinte.

### <a name="recommendations"></a>Recommandations :
Surveiller l’utilisation des ressources, ainsi que les temps de réponse moyens de requêtes lorsque proche de l’utilisation maximale d’une base de données. Si vous rencontrez supérieur latence de requête que vous avez généralement trois options :

1.  Réduisez la quantité de demandes entrantes pour empêcher le délai et la pile la de requêtes de base de données.

2.  Attribuer un niveau de performances plus élevé à la base de données.

3.  Optimiser les requêtes pour réduire l’utilisation des ressources de chaque requête. Pour plus d’informations, voir la section requête réglage/Hinting dans l’article Conseils pour les performances de base de données SQL Azure.

## <a name="enforcement-of-limits"></a>Mise en œuvre des limites
Ressources différent du processeur, la mémoire, e/s journal et e/s de données sont appliquées en refusant les nouvelles demandes lorsque les limites sont atteintes. Les clients recevront un [message d’erreur](sql-database-develop-error-messages.md) en fonction de la limite a été atteinte.

Par exemple, le nombre de connexions à une base de données SQL, ainsi que le nombre de demandes simultanées qui peuvent être traitées est limité. Base de données SQL permet le nombre de connexions à la base de données est supérieur au nombre de demandes simultanées pour prendre en charge le regroupement de connexion. Lorsque le nombre de connexions disponibles facilement peut être contrôlé par l’application, le nombre de requêtes en parallèle est souvent parfois plus difficiles à évaluer et prendre le contrôle. En particulier durant les pointes lorsque l’application soit envoie trop de requêtes ou de la base de données atteint sa ressource limite et démarre accumuler threads de travail en raison de requêtes plus longues, erreurs peuvent être rencontrées.

## <a name="service-tiers-and-performance-levels"></a>Niveaux de service et de performances

Il existe des niveaux de service et de performances pour les deux pools autonome élastique et base de données.

### <a name="standalone-databases"></a>Bases de données autonome

Pour une base de données autonome, la limite d’une base de données est définie par le niveau de performances et de niveau de service de base de données. Le tableau suivant décrit les caractéristiques de base, Standard et Premium bases de données à différents niveaux de performances.

[AZURE.INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

### <a name="elastic-pools"></a>Pools élastiques

[Pools élastiques](sql-database-elastic-pool.md) partager les ressources entre les bases de données dans le pool. Le tableau suivant décrit les caractéristiques des pools d’une base de données élastique basique, Standard et Premium.

[AZURE.INCLUDE [SQL DB service tiers table for elastic databases](../../includes/sql-database-service-tiers-table-elastic-db-pools.md)]

Pour une définition développée de chaque ressource figurant dans les tableaux précédents, consultez les descriptions dans les [limites et les fonctionnalités de niveau de Service](sql-database-performance-guidance.md#service-tier-capabilities-and-limits). Pour une vue d’ensemble des niveaux de service, voir [niveaux de performances et de niveaux de Service de base de données SQL Azure](sql-database-service-tiers.md).

## <a name="other-sql-database-limits"></a>Autres limites de base de données SQL

| Zone | Limite | Description |
|---|---|---|
| Exporter des bases de données à l’aide d’automatisé par abonnement | 10 | Exportation automatisée permet de créer une planification personnalisée pour la sauvegarde de vos bases de données SQL. Pour plus d’informations, voir [bases de données SQL : prise en charge pour l’exportation de base de données SQL automatisé](http://weblogs.asp.net/scottgu/windows-azure-july-updates-sql-database-traffic-manager-autoscale-virtual-machines).|
| Base de données par serveur | Jusqu'à 5 000 | Jusqu'à 5000 bases de données sont autorisées par server sur des serveurs V12. |  
| DTUs par serveur | 45000 | 45000 DTUs sont disponibles par le serveur sur des serveurs V12 pour les bases de données de mise à disponibilité, pools élastiques et data warehouse. |



## <a name="resources"></a>Ressources

[Abonnement Azure et limites de Service, les Quotas et les contraintes](../azure-subscription-service-limits.md)

[Niveaux de Service de base de données SQL Azure et de performances](sql-database-service-tiers.md)

[Messages d’erreur pour les programmes clients de base de données SQL](sql-database-develop-error-messages.md)
