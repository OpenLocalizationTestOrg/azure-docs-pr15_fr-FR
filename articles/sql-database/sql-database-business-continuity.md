<properties
   pageTitle="Continuité des activités en nuage - récupération - base de données SQL de base de données | Microsoft Azure"
   description="Découvrez comment prend en charge la base de données SQL Azure cloud continuité des activités et récupération de base de données et vous aide à vos applications cloud critiques en cours d’exécution."
   keywords="continuité des activités, continuité des activités cloud, sinistre de base de données, récupération de base de données"
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
   ms.workload="NA"
   ms.date="10/13/2016"
   ms.author="carlrab"/>

# <a name="overview-of-business-continuity-with-azure-sql-database"></a>Vue d’ensemble de la continuité avec la base de données SQL Azure

Cette présentation décrit les fonctionnalités de base de données SQL Azure fournit pour continuité des activités et récupération d’urgence. Il propose des options, recommandations et des didacticiels de récupération à partir des interruptions qui peuvent provoquer perte de données ou des votre base de données et l’application ne sont plus disponibles. Inclut la discussion que faire lorsqu’un utilisateur ou erreur d’application affecte l’intégrité des données, une zone Azure a une panne ou votre application requiert maintenance. 

## <a name="sql-database-features-that-you-can-use-to-provide-business-continuity"></a>Fonctionnalités de base de données SQL que vous pouvez utiliser pour assurer la continuité

Base de données SQL offre plusieurs fonctionnalités de continuité commerciale, notamment des sauvegardes automatisées et la réplication de base de données facultatif. Chacun a des caractéristiques différentes pour les temps de reprise estimé (Insér) et perte de données potentielle pour les transactions récentes. Après avoir évalué ces options, vous pouvez choisir parmi les -, dans la plupart des scénarios, redimensionnez-les et associez-les ensemble pour différents scénarios. Lorsque vous développez votre plan continuité de service, vous devez comprendre la durée maximale acceptable avant que l’application récupère entièrement après l’événement sans interruption - il s’agit de votre récupération heure objectifs de. Vous devez également connaître la quantité maximale de données mises à jour récentes (intervalle de temps) l’application peut tolérer perdre lors de la récupération après l’événement sans interruption de service : la récupération point l’objectif. 

Le tableau suivant compare les Insér et RPO pour les trois scénarios courants.

| Fonction |  Niveau de base | Couche standard  | Niveau Premium |
|---|---|---|---|
| Pointez sur dans la zone heure restaurer à partir de sauvegarde | Un point de restauration sous 7 jours   | Un point de restauration au sein de 35 jours  | Un point de restauration au sein de 35 jours |
Geo-restaurer à partir de sauvegardes répliquées geo | Insér < 12h, RPO < 1h   | Insér < 12h, RPO < 1h   | Insér < 12h, RPO < 1h |
|Geo-réplication Active | Insér < 30 s, RPO < 5 s   | Insér < 30 s, RPO < 5 s | Insér < 30 s, RPO < 5 s |


### <a name="use-database-backups-to-recover-a-database"></a>Utiliser les sauvegardes de base de données pour restaurer une base de données

Base de données SQL effectue automatiquement une combinaison de sauvegardes complètes hebdomadaires, différentielles de base de données des sauvegardes toutes les heures et sauvegardes de journal des transactions toutes les cinq minutes pour protéger votre entreprise contre la perte de données. Ces sauvegardes sont stockées dans le stockage localement redondant 35 jours pour les bases de données dans les niveaux de service Standard et Premium et sept jours pour les bases de données dans le niveau de service de base : consultez les [niveaux de service](sql-database-service-tiers.md) pour plus d’informations sur les niveaux de service. Si la période de rétention pour votre niveau de service ne répond pas aux besoins de votre entreprise, vous pouvez augmenter la période de rétention en [modifiant le niveau de service](sql-database-scale-up.md). La base de données complète et différentielle des sauvegardes sont également répliquées vers un [Centre de données appariés](../best-practices-availability-paired-regions.md) de protection contre les panne du centre de données - voir [les sauvegardes automatiques de la base de données](sql-database-automated-backups.md) pour plus d’informations.

Vous pouvez utiliser ces sauvegardes automatiques de la base de données à récupérer une base de données à partir de différents événements d’interruption de service, au sein de votre centre de données et à un autre centre de données. À l’aide des sauvegardes automatiques de la base de données, la durée estimée de récupération dépend de plusieurs facteurs tels que le nombre total de bases de données de récupération dans la même région en même temps, la taille de la base de données, la taille du journal des transactions et la bande passante réseau. Dans la plupart des cas, le temps de récupération est inférieure à 12 heures. Lorsque vous récupérez à une autre zone de données, la perte de données potentielle est limitée à 1 heure par le stockage geo redondants de sauvegardes différentielles toutes les heures. 

> [AZURE.IMPORTANT] Pour récupérer à l’aide des sauvegardes automatisées, vous devez être membre du rôle de SQL Server collaborateur ou le propriétaire de l’abonnement - voir [RBAC : rôles intégrés](../active-directory/role-based-access-built-in-roles.md). Vous pouvez les récupérer à l’aide du portail Azure, PowerShell ou l’API REST. Vous ne pouvez pas utiliser Transact-SQL.

Utilisez des sauvegardes automatisées comme mécanisme de continuité et de récupération entreprise si votre application :

- Ne fait pas mission critique.
- N’a pas un SLA liaison donc le temps d’arrêt de 24 heures ou plus n’entraînera pas de responsabilité financière.
- A un faible taux de modification des données (faibles transactions par heure) et perdre jusqu'à une heure de modification est une perte de données acceptable. 
- Coût est sensible. 

Si vous devez accélérer la restauration, utilisez [La réplication Geo Active](sql-database-geo-replication-overview.md) (étudié ci-après). Si vous devez être en mesure de récupérer les données d’une période antérieure à 35 jours, il est préférable d’archivage de votre base de données régulièrement un fichier à DOS (un fichier compressé contenant votre schéma de base de données et les données associées) stocké dans le stockage blob Azure ou dans un autre emplacement de votre choix. Pour plus d’informations sur la création d’une archive de base de données cohérentes, voir [créer une copie de la base de données](sql-database-copy.md) et [exportation de la copie de la base de données](sql-database-export.md). 

### <a name="use-active-geo-replication-to-reduce-recovery-time-and-limit-data-loss-associated-with-a-recovery"></a>Réplication de Geo Active permet de réduire le temps de récupération et de limiter la perte de données associé à une récupération

Outre l’utilisation des sauvegardes de base de données pour la récupération de base de données en cas d’interruption d’entreprise, vous pouvez utiliser [La réplication Geo Active](sql-database-geo-replication-overview.md) pour configurer une base de données pour que les bases de données secondaires lisibles jusqu'à quatre dans les zones de votre choix. Ces bases de données secondaires sont synchronisés avec la base de données principale à l’aide d’un mécanisme de réplication asynchrone. Cette fonctionnalité est utilisée pour vous protéger contre l’interruption de service en cas de panne du centre de données ou une mise à niveau de l’application. Réplication de Geo active permet également de fournir les meilleures performances de requête pour les requêtes en lecture seule aux utilisateurs géographiquement.

Si la base de données principale déconnecte de manière inattendue ou si vous devez déconnecter pour des activités de maintenance, vous pouvez rapidement promouvoir secondaire pour devenir principal (également appelé un basculement) et configurer les applications pour vous connecter à la principale récemment promue. Avec un basculement planifié, il n’existe aucune perte de données. Avec un basculement non planifié, il peut y avoir une certaine de perte de données pour les transactions très récentes en raison de la nature de la réplication asynchrone. Après un basculement, vous pouvez ultérieure retour arrière - selon un plan ou lorsque le centre de données revient en ligne. Dans tous les cas, les utilisateurs rencontrer un peu de temps d’arrêt et obligé de vous reconnecter. 

> [AZURE.IMPORTANT] Pour utiliser la réplication Geo Active, vous devez être le propriétaire de l’abonnement ou disposer des autorisations d’administration dans SQL Server. Vous pouvez configurer et basculement à l’aide du portail Azure, PowerShell ou l’API REST en utilisant des autorisations sur l’abonnement ou Transact-SQL à l’aide des autorisations dans SQL Server.

Utilisez la réplication Geo Active si votre application répond à chacun de ces critères :

- Est mission critique.
- A un niveau accord de service () qui ne permet pas de 24 heures ou plus de temps d’arrêt.
- Temps d’arrêt générera de responsabilité financière.
- A un taux élevé de données modification est élevée et perdre une heure de données n’est pas acceptable.
- Le coût supplémentaire de la réplication geo active est inférieur à la responsabilité financière potentielle et entraîne une perte d’entreprise.

## <a name="recover-a-database-after-a-user-or-application-error"></a>Restaurer une base de données après une erreur d’utilisateur ou d’application

* Aucune est idéale ! Un utilisateur peut accidentellement supprimé des données, par inadvertance supprimer une table importantes ou même abandonner une base de données entière. Ou bien, une application peut écraser accidentellement les bonnes données avec des données incorrectes en raison d’un défaut dans l’application. 

Dans ce scénario, il s’agit de vos options de récupération.

### <a name="perform-a-point-in-time-restore"></a>Restaurez le point-à-temps

Vous pouvez utiliser les sauvegardes automatisées pour récupérer une copie de votre base de données pour un bon point connu dans le temps, autant que heure ne dépasse pas la période de rétention de base de données. Une fois que la base de données est restauré, vous pouvez remplacer la base de données d’origine avec la base de données restaurée ou copiez les données nécessaires les données restaurées dans la base de données d’origine. Si la base de données utilise la réplication Geo Active, nous vous recommandons de copier les données nécessaires à partir de la copie restaurée dans la base de données d’origine. Si vous remplacez la base de données d’origine avec la base de données restaurée, vous devrez reconfigurer et resynchroniser Geo-réplication Active (qui peut prendre un certain temps pour une base de données volumineux). 

Pour plus d’informations et pour obtenir la procédure détaillée pour restaurer une base de données à un point dans le temps à l’aide du portail Azure ou à l’aide de PowerShell, voir [restaurer point dans le temps](sql-database-recovery-using-backups.md#point-in-time-restore). Vous ne pouvez pas récupérer à l’aide de Transact-SQL.

### <a name="restore-a-deleted-database"></a>Restaurer une base de données supprimé

Si la base de données est supprimé, mais le serveur logique n’a pas été supprimé, vous pouvez restaurer la base de données supprimé du point à partir duquel il a été supprimé. Cela permet de restaurer une sauvegarde de base de données sur le même serveur logique à partir duquel il a été supprimé. Vous pouvez le restaurer en utilisant le nom d’origine ou fournir un nouveau nom ou la base de données restaurée.

Pour plus d’informations et pour obtenir la procédure détaillée pour restaurer une base de données supprimé à l’aide du portail Azure ou à l’aide de PowerShell, voir [restaurer une base de données supprimé](sql-database-recovery-using-backups.md#deleted-database-restore). Vous ne pouvez pas restaurer à l’aide de Transact-SQL.

> [AZURE.IMPORTANT] Si le serveur logique est supprimé, vous ne pouvez pas récupérer une base de données supprimé. 

### <a name="import-from-a-database-archive"></a>Importer à partir d’une archive de base de données

Si la perte de données s’est produite en dehors de la période de rétention en cours de sauvegardes automatiques et que vous avez été d’archiver la base de données, vous pouvez [Importer un fichier à DOS archivé](sql-database-import.md) vers une nouvelle base de données. À ce stade, vous pouvez remplacer la base de données d’origine avec la base de données importée ou copiez les données nécessaires à partir des données importées dans la base de données d’origine. 

## <a name="recover-a-database-to-another-region-from-an-azure-regional-data-center-outage"></a>Récupérer une base de données dans une autre région à partir d’une panne du centre de données régionales Azure

<!-- Explain this scenario -->

Bien que rares, un centre de données Azure peut avoir une panne. En cas de panne, il entraîne une interruption de service qui peut-être ne durent que quelques minutes ou peut durer des heures. 

- Une option consiste à attendre reconnectez lorsque la panne du centre de données se trouve sur votre base de données. Cela fonctionne pour les applications qui peuvent investir dans la base de données en mode hors connexion. Par exemple, un projet de développement ou une version d’évaluation gratuite vous n’avez pas besoin de travailler sur en permanence. Lorsqu’un centre de données a une panne, vous ne savez pas combien de temps l’interruption prendra, pour que cette option fonctionne uniquement si vous n’avez pas besoin votre base de données depuis un certain temps.
- Une autre option consiste à soit basculer vers une autre zone de données si vous utilisez la réplication Geo Active ou le récupérer à l’aide de sauvegardes de base de données geo redondantes (Geo-restaurer). Basculement prend uniquement quelques secondes, alors que la récupération à partir de sauvegardes prend heures.

Lorsque vous agir, combien de temps il vous permet d’accéder à récupérer et quantité perte de données vous engager en cas de panne du centre de données dépend de la façon dont vous décidez d’utiliser les fonctionnalités de continuité de l’activité entreprise abordées dans votre application. En effet, vous pouvez choisir d’utiliser une combinaison de sauvegardes de base de données et Geo-réplication Active en fonction des besoins de votre application. Pour une description des éléments de conception d’application de bases de données autonomes et des pools élastiques à l’aide de ces fonctionnalités de continuité d’entreprise, voir [Création d’une application de reprise cloud](sql-database-designing-cloud-solutions-for-disaster-recovery.md) et [stratégies de récupération Pool élastique](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).

Les sections suivantes fournissent une vue d’ensemble des étapes pour récupérer à l’aide des sauvegardes de base de données ou la réplication Geo Active. Pour plus d’informations, y compris la planification de configuration requise, les étapes de récupération billet et savoir comment simuler une panne pour effectuer une exploration de récupération d’urgence, voir [récupérer une base de données SQL en cas de panne](sql-database-disaster-recovery.md).

### <a name="prepare-for-an-outage"></a>Préparer une panne

Quelle que soit la fonctionnalité de continuité professionnel que vous utilisez, vous devez :

- Identifier et préparer le serveur cible, y compris les règles de pare-feu au niveau du serveur, les connexions et les autorisations au niveau de la base de données principale.
- Déterminer comment rediriger les clients et les applications clientes vers le nouveau serveur
- Document autres dépendances, telles que la vérification des paramètres et des alertes 
 
Si vous ne pas planifier et préparer correctement, en bénéficiant de vos applications en ligne après qu’un basculement ou une récupération prenne du temps et susceptibles de nécessitent également le dépannage à la fois de contrainte - une combinaison incorrecte.

### <a name="failover-to-a-geo-replicated-secondary-database"></a>Basculer vers une base de données secondaire répliquée geo 

Si vous utilisez la réplication Geo Active comme mécanisme de récupération, [forcez le basculement vers un secondaire répliquées geo](sql-database-disaster-recovery.md#failover-to-geo-replicated-secondary-database). Quelques secondes, le moniteur secondaire est promu la nouvelle primaire et est prêt à enregistrer les nouvelles transactions et répondre à des requêtes - avec seulement quelques secondes de perte de données pour les données qui n’ont pas encore été répliquées. Pour plus d’informations sur l’automatisation le processus de basculement, voir [Création d’une application de reprise cloud](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

> [AZURE.NOTE] Lorsque le centre de données revient en ligne, vous pouvez retour arrière vers le serveur principal d’origine (ou non).

### <a name="perform-a-geo-restore"></a>Effectuer une restauration Geo 

Si vous utilisez automatisée des sauvegardes avec la réplication de stockage geo redondants comme mécanisme de récupération, [initier une récupération de base de données à l’aide de Geo restaurer](sql-database-disaster-recovery.md#recover-using-geo-restore). Récupération est généralement effectuée dans les 12 heures - sans perte de données d’une heure déterminée par quand la sauvegarde horaire dernière avec prise et répliquées. Jusqu'à ce que la récupération est terminée, la base de données ne peut pas enregistrer toutes les transactions ou répondre à des requêtes. 

> [AZURE.NOTE] Si le centre de données revient en ligne avant de transférer votre application la base de données récupéré, vous pouvez annuler simplement la récupération.  

### <a name="perform-post-failover--recovery-tasks"></a>Effectuez publier basculement / tâches de récupération 

Après récupération d’un mécanisme de récupération, vous devez effectuer les tâches supplémentaires suivantes avant que les utilisateurs et applications sont revenir en cours d’exécution :

- Rediriger les clients et les applications clientes au nouveau serveur et base de données restaurée
- Vérifiez les règles de pare-feu au niveau du serveur appropriés sont en place pour les utilisateurs de se connecter (ou utilisez le [pare-feu au niveau de base de données](sql-database-firewall-configure.md#creating-database-level-firewall-rules))
- Vérifiez que connexions appropriées et les autorisations au niveau de la base de données principale sont en place (ou utilisez [contenait utilisateurs](https://msdn.microsoft.com/library/ff929188.aspx))
- Configurer l’audit, le cas échéant
- Configurer des alertes, le cas échéant

## <a name="upgrade-an-application-with-minimal-downtime"></a>Mise à niveau une application très peu de temps

Parfois une application doit être déconnecté en raison de la maintenance planifiée comme une mise à niveau de l’application. [Mises à niveau de gérer application](sql-database-manage-application-rolling-upgrade.md) décrit comment Geo-réplication Active permet d’activer les mises à niveau de votre application cloud pour réduire les temps d’arrêt pendant les mises à niveau et fournir un chemin de récupération au cas où un dysfonctionnement. Cet article examine deux façons d’organiser le processus de mise à niveau et présente les avantages et inconvénients de chaque option.

## <a name="next-steps"></a>Étapes suivantes

Pour une description des éléments de conception d’application de bases de données autonomes et des pools élastiques, voir [Création d’une application de reprise cloud](sql-database-designing-cloud-solutions-for-disaster-recovery.md) et [stratégies de récupération Pool élastique](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).






