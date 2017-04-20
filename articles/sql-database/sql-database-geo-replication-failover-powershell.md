<properties 
    pageTitle="Lancer un basculement planifié ou de base de données SQL Azure avec PowerShell | Microsoft Azure" 
    description="Lancer un basculement planifié ou de base de données SQL Azure à l’aide de PowerShell" 
    services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management" 
    ms.date="08/29/2016"
    ms.author="sstein"/>

# <a name="initiate-a-planned-or-unplanned-failover-for-azure-sql-database-with-powershell"></a>Lancer un basculement planifié ou de base de données SQL Azure avec PowerShell



> [AZURE.SELECTOR]
- [Portail Azure](sql-database-geo-replication-failover-portal.md)
- [PowerShell](sql-database-geo-replication-failover-powershell.md)
- [T-SQL](sql-database-geo-replication-failover-transact-sql.md)


Cet article vous explique comment initier un basculement planifié ou de base de données SQL avec PowerShell. Pour configurer la réplication de Geo, voir [Configurer la Geo-réplication de base de données SQL Azure](sql-database-geo-replication-powershell.md).



## <a name="initiate-a-planned-failover"></a>Lancer un basculement planifié

Utilisez l’applet de commande **Set-AzureRmSqlDatabaseSecondary** avec le paramètre **- Basculement** pour promouvoir une base de données secondaire pour devenir la nouvelle base de données principale, rétrograder primaire existant pour devenir secondaire. Cette fonctionnalité est conçue pour un basculement planifié, par exemple au cours des exercices de récupération d’urgence et requiert que la base de données principale soit disponible.

La commande exécute le flux de travail suivant :

1. Basculer temporairement la réplication vers le mode synchrone. Cela entraînera toutes les transactions en suspens vidage sur le site secondaire.

2. Changer les rôles des deux bases de données du partenariat Geo réplication.  

Cette séquence garantit que les deux bases de données sont synchronisés avant de changer les rôles et par conséquent se produira sans perte de données. Il existe une courte période pendant laquelle les deux bases de données ne sont pas disponibles (l’ordre des secondes de 0 à 25) tandis que les rôles sont activées. L’opération entière doit prendre moins d’une minute pour effectuer dans des circonstances normales. Pour plus d’informations, voir [Set-AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt619393.aspx).




Cette applet de commande renverra une fois le processus de passer de la base de données secondaire à primaire terminé.

La commande suivante bascule les rôles de la base de données nommée « mydb » sur le serveur « srv2 » sous le groupe de ressources « rg2 » principale. Le serveur principal d’origine à laquelle « db2 » a été connecté à bascule vers secondaire une fois que les deux bases de données sont complètement synchronisées.

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" –ResourceGroupName "rg2” –ServerName "srv2”
    $database | Set-AzureRmSqlDatabaseSecondary -Failover


> [AZURE.NOTE] Dans certains cas rares, il est possible que l’opération ne peut pas terminer et peut-être s’afficher ne répond pas. Dans ce cas, l’utilisateur peut appeler la commande de basculement force (basculement non planifié) et accepter des pertes de données.


## <a name="initiate-an-unplanned-failover-from-the-primary-database-to-the-secondary-database"></a>Lancer un basculement non planifié à partir de la base de données principale à la base de données secondaire


Vous pouvez utiliser l’applet de commande **Set-AzureRmSqlDatabaseSecondary** avec des paramètres **– Basculement** et **- AllowDataLoss** pour promouvoir une base de données secondaire pour devenir la nouvelle base de données principale de manière non planifiée, forcer la régression du primaire existante pour devenir secondaire à un moment lorsque la base de données principal n’est plus disponible.

Cette fonctionnalité est conçue pour la reprise lors de la restauration de disponibilité de la base de données est essentielle et des pertes de données soient acceptable. Lorsque basculement forcé est appelé, la base de données secondaire spécifiée devient la base de données principale et démarre l’acceptation des transactions d’écriture immédiatement. Dès que la base de données principale d’origine ne peut se reconnecter à cette nouvelle base de données principale après l’opération de basculement forcé, une sauvegarde incrémentielle est effectuée sur la base de données principale d’origine et l’ancienne base de données principale est effectué dans une base de données secondaire pour la nouvelle base de données principale ; par la suite, il est simplement une copie de la nouvelle primaire.

Mais étant donné que l’instantané dans heure restaurer n’est pas pris en charge sur les bases de données secondaires, si vous le souhaitez aux données de récupération validées pour la base de données principale ancienne qui n’a pas été répliquée sur la nouvelle base de données principale, vous devez s’engager CSS pour restaurer une base de données pour la sauvegarde du journal connus.

> [AZURE.NOTE] Si la commande est émise lorsque le sont principal et secondaire en ligne deviendra l’ancien serveur principal la nouvelle secondaire immédiatement sans la synchronisation des données. Si le serveur principal est transactions validation lorsque la commande est émise une perte de données peuvent se produire.


Si la base de données principale comporte plusieurs zones secondaires la commande partiellement réussit. Le moniteur secondaire sur lequel la commande a été exécutée devient principal. L’ancien serveur principal restent toutefois principal, c'est-à-dire les couleurs deux primaires seront retrouvent incohérente et connectés par un lien de réplication suspendu. L’utilisateur a réparer manuellement cette configuration à l’aide d’une API « supprimer secondaire » sur un de ces bases de données principales.


La commande suivante bascule les rôles de la base de données nommée « mydb « principale lorsque le serveur principal n’est pas disponible. Le serveur principal d’origine à laquelle « mydb » a été connecté à bascule vers secondaire lorsqu’elles sont en ligne. À ce stade, la synchronisation peut entraîner la perte de données.

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" –ResourceGroupName "rg2” –ServerName "srv2”
    $database | Set-AzureRmSqlDatabaseSecondary –Failover -AllowDataLoss




## <a name="next-steps"></a>Étapes suivantes   

- Après le basculement, vérifiez que la configuration requise d’authentification pour votre base de données et votre serveur est configurée sur la nouvelle primaire. Pour plus d’informations, consultez [sécurité de base de données SQL après sinistre](sql-database-geo-replication-security-config.md).
- Reprise après un sinistre à l’aide de Geo-réplication Active, y compris les versions antérieures et publier des étapes de récupération et effectuer une exploration de récupération après sinistre, Découvrez [Exercices de récupération d’urgence](sql-database-disaster-recovery.md)
- Pour un billet de blog Sasha Nosov sur Geo-réplication Active, voir [Actualités sur les nouvelles fonctionnalités Geo réplication](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication/)
- Pour plus d’informations sur la conception d’applications en nuage à utiliser la réplication Geo Active, consultez [conception d’applications cloud pour la continuité à l’aide de la réplication Geo](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- Pour plus d’informations sur l’utilisation de la réplication Geo Active avec des pools élastique de base de données, voir [stratégies de récupération Pool élastique](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).
- Pour une vue d’ensemble de l’entreprise continurity, voir [Vue d’ensemble de continuité d’entreprise](sql-database-business-continuity.md)
