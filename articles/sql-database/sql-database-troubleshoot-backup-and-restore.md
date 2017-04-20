<properties
    pageTitle="Résoudre les problèmes de sauvegarde et de restauration avec la base de données SQL Azure"
    description="Découvrez comment récupérer une base de données cloud d’erreurs et défaillances à l’aide des sauvegardes et réplicas dans la base de données SQL Azure."
    services="sql-database"
    documentationCenter=""
    authors="dalechen"
    manager="felixwu"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/31/2016"
    ms.author="daleche"/>

# <a name="restore-a-database-to-a-previous-point-in-time-restore-a-deleted-database-or-recover-from-a-data-center-outage"></a>Restaurer une base de données à un point antérieur dans le temps, restaurer une base de données supprimé ou récupérer à partir de panne du centre de données

Base de données SQL effectue des copies de votre base de données afin que vous pouvez récupérer de défaillances et d’erreur utilisateur. Options disponibles varient selon le niveau de service de base de données et les options que vous choisissez. Consultez la [Vue d’ensemble de la continuité d’entreprise](sql-database-business-continuity.md) pour les détails et les éléments de conception.

## <a name="to-restore-a-database-to-a-previous-point-in-time"></a>Pour restaurer une base de données à un point antérieur dans le temps
1.  Dans le [Portail Azure](https://azure.microsoft.com/), cliquez sur **bases de données SQL**.
2.  Sélectionnez votre base de données dans la liste, puis sur **restaurer**.
3.  Tapez un nouveau nom pour la base de données, sélectionnez la date et l’heure à restaurer à partir de, puis cliquez sur **créer.**
4.  Application des ajustements que nécessaire pour faire référence à la nouvelle base de données. Voir [récupérer une base de données à un point dans le temps](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="to-restore-an-accidentally-deleted-database"></a>Pour restaurer une base de données accidentellement supprimé
1.  Dans le [Portail Azure](https://azure.microsoft.com/), cliquez sur **SQL Server**.
2.  Sélectionnez le serveur hébergeant la base de données à partir de la liste.
3.  Sur la carte serveur, faites défiler vers le bas, puis cliquez sur **bases de données supprimés**.
4.  Sélectionnez la base de données à restaurer, puis cliquez sur **créer**.
5.  Application des ajustements que nécessaire pour faire référence à la nouvelle base de données. Voir [récupérer une base de données supprimé](sql-database-recovery-using-backups.md#deleted-database-restore).

## <a name="to-recover-from-a-regional-datacenter-outage"></a>Pour récupérer une panne du centre de données régional
Les bases de données Standard et Premium, si vous avez configuré répliquées geo secondaires, vous pouvez les récupérer à l’aide de ces zones secondaires. Cela vous donne la possibilité de restaurer une base de données avec un moins risque de perte de données. Pour plus d’informations, voir [récupérer une base de données SQL Azure à l’aide des sauvegardes automatisées de base de données](sql-database-disaster-recovery.md) .

Azure permet également des sauvegardes de chaque base de données dans une zone différente (une sauvegarde geo redondants). Vous pouvez créer une nouvelle base de données à partir de ces sauvegardes, qui est appelé Geo restaurer, mais il est probable que vous allez perdre des données si vous avez recours à cette méthode uniquement.

**Pour restaurer une base de données à l’aide de geo restaurer :**

- Dans le [Portail Azure](https://azure.microsoft.com/), cliquez sur **Nouveau**et cliquez sur **données et stockage**, cliquez sur **Base de données SQL**, puis sélectionnez **sauvegarde** comme source de base de données. Pour plus d’informations, voir [récupérer une base de données SQL Azure en cas de panne](sql-database-disaster-recovery.md) .