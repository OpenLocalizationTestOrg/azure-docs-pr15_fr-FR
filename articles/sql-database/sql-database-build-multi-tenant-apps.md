<properties
   pageTitle="Base de données SQL Azure crée des applications clients multiples avec isolement et l’efficacité"
   description="Découvrez comment base de données SQL crée des applications clients multiples"
   keywords=""
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="10/13/2016"
   ms.author="carlrab"/>

# <a name="builds-multi-tenant-apps-with-azure-sql-database-with-isolation-and-efficiency"></a>Crée des applications clients à plusieurs avec la base de données SQL Azure avec isolement et l’efficacité

## <a name="leverage-elastic-pools-and-build-more-efficient-multi-tenant-apps"></a>Tirer parti des pools élastiques et créer des applications clients multiples plus efficaces

Si vous êtes un développement SaaS écrire une application cliente multiples et la gestion de nombreux clients, que vous apportez souvent compromis dans client performances, gestion et sécurité. Avec Azure SQL de base de données élastique de base de données des Pools, vous devez n’est plus effectuer ce compromis. Ces pools vous aident à gérer et surveiller des applications clients multiples et bénéficier d’avantages d’isolement d’un client-par-bases de données. Voir les [modèles de conception pour les Applications SaaS client à plusieurs avec la base de données SQL Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md).

![génération-multi-client-apps](./media/sql-database-build-multi-tenant-apps/sql-database-build-multi-tenant-apps.png)

## <a name="auto-scaling-you-control"></a>Mise à l’échelle contrôler

Pools d’ajuster automatiquement les performances et la capacité de stockage des bases de données élastiques à la volée. Vous pouvez contrôler les performances affectés à un pool, ajouter ou supprimer élastiques bases de données à la demande et définir les performances des bases de données élastiques sans affecter le coût total du pool. Cela signifie que vous n’avez pas à vous soucier de gérer l’utilisation des bases de données.

[Consultez la documentation](sql-database-elastic-pool.md)

## <a name="intelligent-management-of-your-environment"></a>Gestion intelligente de votre environnement

Recommandations de dimensionnement intégrés identifient le fait bases de données susceptible de bénéficier des pools. Ces recommandations permettent une analyse « what-if » pour l’optimisation rapide atteindre vos objectifs de performances. Enrichi suivi des performances et résolution des problèmes de tableaux de bord vous aident à visualiser l’utilisation du pool historiques.

[Consultez la documentation](sql-database-elastic-pool-guidance.md)

## <a name="performance-and-price-to-meet-your-needs"></a>Performances et prix à vos besoins

Base, Standard, et des pools Premium fournissent un large éventail de performances, le stockage et options de tarification. Pools peuvent contenir jusqu'à 400 élastiques bases de données. Bases de données élastiques peuvent échelle automatique jusqu'à unités de transaction de base de données élastique 1000 (eDTU).

[Consultez la documentation](https://azure.microsoft.com/pricing/details/sql-database/?b=16.50)

## <a name="elastic-tools"></a>Outils élastiques

En plus des pools élastiques, il existe des fonctionnalités de base de données SQL pour faciliter la gestion des activités opérationnelles dans plusieurs bases de données :

**Exécuter des requêtes de bases de données croisées et création de rapports.**  
[Requête de base de données élastique](sql-database-elastic-query-overview.md) permet d’exécuter des requêtes ou rapports pour plusieurs bases de données dans votre pool élastique et accéder aux données distantes stockées dans plusieurs bases de données de votre pool en même temps.

**Exécuter les transactions de base de données externe.**  
[Les transactions de base de données élastique](sql-database-elastic-transactions-overview.md) permettent vous permet d’exécuter des transactions qui s’étalent sur plusieurs bases de données dans les bases de données SQL et exécuter des opérations (c'est-à-dire lors du traitement des transactions financières entre bases de données ou lors de la mise à jour d’inventaire dans une base de données et les commandes).

**Exécuter les mêmes opérations sur plusieurs bases de données.**  
[Tâches de base de données élastique](sql-database-elastic-jobs-overview.md) exécuter des opérations d’administration tels que la reconstruction d’index ou mise à jour des schémas dans chaque base de données dans votre pool élastique.

Accédez à la page d’accueil pour voir quelle autre base de données SQL a à offrir.
[Regarde](https://azure.microsoft.com/services/sql-database/) 

## <a name="next-steps"></a>Étapes suivantes

Obtenir un [abonnement Azure gratuit](https://azure.microsoft.com/get-started/) et [créer votre première base de données SQL Azure](sql-database-get-started.md).

## <a name="additional-resources"></a>Ressources supplémentaires

Explorez toutes les [fonctionnalités de base de données SQL](https://azure.microsoft.com/services/sql-database/).
 
Consultez [présentation technique de base de données SQL](sql-database-technical-overview.md).  
