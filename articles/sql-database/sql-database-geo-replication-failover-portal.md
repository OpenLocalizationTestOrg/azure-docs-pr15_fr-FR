<properties 
    pageTitle="Démarrer une conversation de basculement planifié ou de base de données SQL Azure avec le portail Azure | Microsoft Azure" 
    description="Lancer un basculement planifié ou de base de données SQL Azure à l’aide du portail Azure" 
    services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="data-management" 
    ms.date="08/29/2016"
    ms.author="sstein"/>

# <a name="initiate-a-planned-or-unplanned-failover-for-azure-sql-database-with-the-azure-portal"></a>Démarrer une conversation de basculement planifié ou de base de données SQL Azure avec le portail Azure


> [AZURE.SELECTOR]
- [Portail Azure](sql-database-geo-replication-failover-portal.md)
- [PowerShell](sql-database-geo-replication-failover-powershell.md)
- [T-SQL](sql-database-geo-replication-failover-transact-sql.md)


Cet article vous explique comment initier basculement vers une base de données SQL secondaire grâce au [portail Azure](http://portal.azure.com). Pour configurer la réplication de Geo, voir [Configurer la Geo-réplication de base de données SQL Azure](sql-database-geo-replication-portal.md).


## <a name="initiate-a-failover"></a>Lancer un basculement

La base de données secondaire peut être changé pour devenir le serveur principal.  

1. Dans le [portail Azure](http://portal.azure.com) Parcourir du partenariat Geo réplication de base de données principale.
2. Sur la carte de base de données SQL, sélectionnez **tous les paramètres** > **Geo réplication**.
3. Dans la liste **secondaires** , sélectionnez la base de données que vous voulez deviennent la nouvelle primaire et cliquez sur **Basculer**.

    ![basculement][2]

4. Cliquez sur **Oui** pour commencer le basculement.

La commande bascule immédiatement la base de données secondaire dans le rôle principal. 

Il existe une courte période pendant laquelle les deux bases de données ne sont pas disponibles (l’ordre des secondes de 0 à 25) tandis que les rôles sont activées. Si la base de données principale comporte plusieurs bases de données secondaires, la commande reconfigurer automatiquement les autres secondaires pour vous connecter à la nouvelle primaire. L’opération entière doit prendre moins d’une minute pour effectuer dans des circonstances normales. 

>[AZURE.NOTE] Si le serveur principal est en ligne et la validation des transactions lorsque la commande est émise une perte de données peut se produire.


## <a name="next-steps"></a>Étapes suivantes   

- Après le basculement, vérifiez que la configuration requise d’authentification pour votre base de données et votre serveur est configurée sur la nouvelle primaire. Pour plus d’informations, consultez [sécurité de base de données SQL après sinistre](sql-database-geo-replication-security-config.md).
- Reprise après un sinistre à l’aide de Geo-réplication Active, y compris les versions antérieures et publier des étapes de récupération et effectuer une exploration de récupération après sinistre, Découvrez [Exercices de récupération d’urgence](sql-database-disaster-recovery.md)
- Pour un billet de blog Sasha Nosov sur Geo-réplication Active, voir [Actualités sur les nouvelles fonctionnalités Geo réplication](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication/)
- Pour plus d’informations sur la conception d’applications en nuage à utiliser la réplication Geo Active, consultez [conception d’applications cloud pour la continuité à l’aide de la réplication Geo](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- Pour plus d’informations sur l’utilisation de la réplication Geo Active avec des pools élastique de base de données, voir [stratégies de récupération Pool élastique](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).
- Pour une vue d’ensemble de l’entreprise continurity, voir [Vue d’ensemble de continuité d’entreprise](sql-database-business-continuity.md)




<!--Image references-->
[1]: ./media/sql-database-geo-replication-failover-portal/failover.png
[2]: ./media/sql-database-geo-replication-failover-portal/secondaries.png
