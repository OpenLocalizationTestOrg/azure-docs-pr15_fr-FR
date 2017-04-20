<properties 
    pageTitle="Lancer un basculement planifié ou de base de données SQL Azure avec Transact-SQL | Microsoft Azure" 
    description="Lancer un basculement planifié ou de base de données SQL Azure à l’aide de Transact-SQL" 
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
    ms.date="08/29/2016"
    ms.author="carlrab"/>

# <a name="initiate-a-planned-or-unplanned-failover-for-azure-sql-database-with-transact-sql"></a>Lancer un basculement planifié ou de base de données SQL Azure avec Transact-SQL


> [AZURE.SELECTOR]
- [Portail Azure](sql-database-geo-replication-failover-portal.md)
- [PowerShell](sql-database-geo-replication-failover-powershell.md)
- [T-SQL](sql-database-geo-replication-failover-transact-sql.md)


Cet article vous explique comment initier basculement vers une base de données SQL secondaire à l’aide de Transact-SQL. Pour configurer la réplication de Geo, voir [Configurer la Geo-réplication de base de données SQL Azure](sql-database-geo-replication-transact-sql.md).



Pour déclencher le basculement, vous devez les éléments suivants :

- Une connexion qui est DBManager sur le serveur principal, ont db_ownership de la base de données locale que vous allez geo-réplication, et être DBManager sur les serveurs partenaire à laquelle vous allez configurer la réplication de Geo.
- SQL Server Management Studio (SSMS)


> [AZURE.IMPORTANT] Il est recommandé d’utiliser toujours la dernière version de Management Studio doit pour rester synchronisé avec les mises à jour Microsoft Azure et base de données SQL. [Mettre à jour de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).




## <a name="initiate-a-planned-failover-promoting-a-secondary-database-to-become-the-new-primary"></a>Lancer un basculement planifié promotion d’une base de données secondaire pour devenir la nouvelle primaire

Vous pouvez utiliser l’instruction **ALTER DATABASE** pour promouvoir une base de données secondaire pour devenir la nouvelle base de données principale de manière planifiée, rétrograder primaire existant pour devenir secondaire. Cette instruction est exécutée sur la base de données maître sur le serveur logique de base de données SQL Azure dans lequel se trouve le répliquées geo base de données secondaire qui est promu. Cette fonctionnalité est destinée à basculement planifié, tels que pendant les exercices DR et requiert que la base de données principale soit disponible.

La commande exécute le flux de travail suivant :

1. Bascule temporairement réplication en mode synchrone, à l’origine de toutes les transactions en suspens vidage sur le site secondaire et bloquer toutes les nouvelles transactions ;

2. Bascule les rôles des deux bases de données du partenariat Geo réplication.  

Cette séquence garantit que les deux bases de données sont synchronisés avant de changer les rôles et par conséquent se produira sans perte de données. Il existe une courte période pendant laquelle les deux bases de données ne sont pas disponibles (l’ordre des secondes de 0 à 25) tandis que les rôles sont activées. Si la base de données principale comporte plusieurs bases de données secondaires, la commande reconfigurer automatiquement les autres secondaires pour vous connecter à la nouvelle primaire.  L’opération entière doit prendre moins d’une minute pour effectuer dans des circonstances normales. Pour plus d’informations, voir [Modifier une base de données (Transact-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) et les [Niveaux de Service](sql-database-service-tiers.md).


Procédez comme suit pour initier un basculement planifié.

1. Dans Management Studio, connectez-vous au serveur de base de données SQL Azure logique dans lequel se trouve une base de données secondaire répliquée geo.

2. Ouvrez le dossier de bases de données, développez le dossier **Bases de données système** , avec le bouton droit sur **maître**, puis cliquez sur **Nouvelle requête**.

3. L’instruction **ALTER DATABASE** suivante permet de basculer la base de données secondaire vers le rôle principal.

        ALTER DATABASE <MyDB> FAILOVER;

4. Cliquez sur **exécuter** pour exécuter la requête.

>[AZURE.NOTE] Rarement, il est possible que l’opération ne peut pas terminer et peut-être s’afficher bloquée. Dans ce cas, l’utilisateur peut exécuter la commande de basculement force et accepter des pertes de données.


## <a name="initiate-an-unplanned-failover-from-the-primary-database-to-the-secondary-database"></a>Lancer un basculement non planifié à partir de la base de données principale à la base de données secondaire

Vous pouvez utiliser l’instruction **ALTER DATABASE** pour promouvoir une base de données secondaire pour devenir la nouvelle base de données principale de manière non planifiée, forcer la régression du primaire existante pour devenir secondaire à un moment lorsque la base de données principal n’est plus disponible. Cette instruction est exécutée sur la base de données maître sur le serveur logique de base de données SQL Azure dans lequel se trouve le répliquées geo base de données secondaire qui est promu.

Cette fonctionnalité est conçue pour la reprise lors de la restauration de disponibilité de la base de données est essentielle et des pertes de données soient acceptable. Lorsque basculement forcé est appelé, la base de données secondaire spécifiée devient la base de données principale et démarre l’acceptation des transactions d’écriture immédiatement. Dès que la base de données principale d’origine ne peut se reconnecter à cette nouvelle base de données principale, une sauvegarde incrémentielle est effectuée sur la base de données principale d’origine et l’ancienne base de données principale est effectué dans une base de données secondaire pour la nouvelle base de données principale ; par la suite, il s’agit simplement un synchronisation réplica de la nouvelle primaire.

Toutefois, étant donné que l’instantané dans heure restaurer n’est pas pris en charge dans les bases de données secondaires, si l’utilisateur souhaite récupérer les données validées pour l’ancienne base de données principale n'a pas été répliquée vers la nouvelle base de données principale avant le basculement forcé s’est produite, l’utilisateur devra s’engager prise en charge pour récupérer cette perte de données.

Si la base de données principale comporte plusieurs bases de données secondaires, la commande reconfigurer automatiquement les autres secondaires pour vous connecter à la nouvelle primaire.

Procédez comme suit pour initier un basculement non planifié.

1. Dans Management Studio, connectez-vous au serveur de base de données SQL Azure logique dans lequel se trouve une base de données secondaire répliquée geo.

2. Ouvrez le dossier de bases de données, développez le dossier **Bases de données système** , avec le bouton droit sur **maître**, puis cliquez sur **Nouvelle requête**.

3. L’instruction **ALTER DATABASE** suivante permet de basculer la base de données secondaire vers le rôle principal.

        ALTER DATABASE <MyDB>   FORCE_FAILOVER_ALLOW_DATA_LOSS;

4. Cliquez sur **exécuter** pour exécuter la requête.

>[AZURE.NOTE] Si la commande est émise lorsque le sont principal et secondaire en ligne deviendra l’ancien serveur principal la nouvelle secondaire immédiatement sans la synchronisation des données. Si le serveur principal est transactions validation lorsque la commande est émise une perte de données peuvent se produire.



## <a name="next-steps"></a>Étapes suivantes   

- Après le basculement, vérifiez que la configuration requise d’authentification pour votre base de données et votre serveur est configurée sur la nouvelle primaire. Pour plus d’informations, consultez [sécurité de base de données SQL après sinistre](sql-database-geo-replication-security-config.md).
- Reprise après un sinistre à l’aide de Geo-réplication Active, y compris les versions antérieures et publier des étapes de récupération et effectuer une exploration de récupération après sinistre, Découvrez [Sinistre](sql-database-disaster-recovery.md)
- Pour un billet de blog Sasha Nosov sur Geo-réplication Active, voir [Actualités sur les nouvelles fonctionnalités Geo réplication](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication/)
- Pour plus d’informations sur la conception d’applications en nuage à utiliser la réplication Geo Active, consultez [conception d’applications cloud pour la continuité à l’aide de la réplication Geo](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- Pour plus d’informations sur l’utilisation de la réplication Geo Active avec des pools élastique de base de données, voir [stratégies de récupération Pool élastique](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).
- Pour une vue d’ensemble de l’entreprise continurity, voir [Vue d’ensemble de continuité d’entreprise](sql-database-business-continuity.md)
