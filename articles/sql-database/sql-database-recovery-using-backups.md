<properties
   pageTitle="Continuité des activités en nuage - restaurer une base de données supprimé - base de données SQL | Microsoft Azure"
   description="En savoir plus sur la restauration Point-à-temps, qui permet de restaurer une base de données SQL Azure à un point antérieur dans le temps (35 jours)."
   services="sql-database"
   documentationCenter=""
   authors="stevestein"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/01/2016"
   ms.author="sstein"/>

# <a name="recover-an-azure-sql-database-using-automated-database-backups"></a>Restaurer une base de données SQL Azure à l’aide des sauvegardes automatisées de base de données

Base de données SQL propose trois options pour la récupération de base de données à l’aide de la [que base de données SQL des sauvegardes automatisées](sql-database-automated-backups.md). Vous pouvez restaurer une base de données à partir des sauvegardes exécutée par le service pendant sa [période de rétention](sql-database-service-tiers.md) pour :

- Une nouvelle base de données du même serveur logique récupérés à un point spécifié dans un délai de la période de rétention. 
- Une base de données du même serveur logique restaurée jusqu’au moment de la suppression d’une base de données supprimé.
- Une nouvelle base de données sur un serveur logique dans n’importe quelle région restaurée vers les sauvegardes quotidiennes plus récentes dans le stockage blob répliquées geo (RA GRS).

Vous pouvez également utiliser des [sauvegardes automatisées de base de données SQL](sql-database-automated-backups.md) pour créer une [copie de la base de données](sql-database-copy.md) sur un serveur logique dans n’importe quelle région est cohérente avec la base de données SQL en cours. Vous pouvez utiliser copie de la base de données et [d’exportation vers un à DOS](sql-database-export.md) pour archiver une copie d’une base de données pour le stockage à long terme au-delà de la période de rétention cohérente, ou pour transférer une copie de votre base de données vers un local ou machine virtuelle Azure instance de SQL Server.

## <a name="recovery-time"></a>Temps de restauration

Le temps de récupération pour restaurer une base de données à l’aide des sauvegardes automatisées affectée par un certain nombre de facteurs : 
 - La taille de la base de données
 - Le niveau de performance de la base de données
 - Le nombre de journaux des transactions impliquées
 - Le volume d’activité devant être relire pour récupérer au point de restauration
 - La bande passante réseau si la restauration consiste à une zone différente 
 - Le nombre de demandes simultanées restaurer traitées dans la région cible. 
 
 Pour une base de données très volumineuses et/ou active la restauration peut prendre quelques heures. S’il existe une panne prolongée dans une région, il est possible qu’il y ait un grand nombre de requêtes Geo restaurer traitées par d’autres régions. S’il existe un grand nombre de requêtes cela peut augmenter le temps de récupération des bases de données dans cette zone. La majorité de base de données restaure complète dans les 12 heures.

 Il n’existe pas de fonctionnalités intégrées à restaurer la majeure. La [base de données SQL Azure : récupération de serveur complète](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666) script est un exemple d’une façon d’exécuter cette tâche.

> [AZURE.IMPORTANT] Pour récupérer à l’aide des sauvegardes automatisées, vous devez être membre du rôle SQL Server collaborateur dans l’abonnement ou être le propriétaire de l’abonnement. Vous pouvez les récupérer à l’aide du portail Azure, PowerShell ou l’API REST. Vous ne pouvez pas utiliser Transact-SQL. 

## <a name="point-in-time-restore"></a>Restauration de point dans le temps

Point-à-temps restaurer permet de restaurer une base de données existante comme une base de données à un point antérieur dans le temps sur le même serveur logique à l’aide de la [que base de données SQL des sauvegardes automatisées](sql-database-automated-backups.md). Vous ne pouvez pas remplacer la base de données existante. Vous pouvez restaurer à un point antérieur dans le temps à l’aide du [portail Azure](sql-database-point-in-time-restore-portal.md), [PowerShell](sql-database-point-in-time-restore-powershell.md) ou l' [API REST](https://msdn.microsoft.com/library/azure/mt163685.aspx).

> [AZURE.SELECTOR]
- [Restaurer point-à-temps : Portail Azure](sql-database-point-in-time-restore-portal.md)
- [Restauration de point-à-temps : PowerShell](sql-database-point-in-time-restore-powershell.md)

La base de données pouvant être restauré sur n’importe quel niveau de performance ou élastique pool. Vous devez vous assurer que vous disposez d’un quota DTU suffisant sur le serveur logique ou le pool élastique. N’oubliez pas que la restauration crée une nouvelle base de données et que le niveau de performances et de niveau de service de la base de données restaurée peut être différent de l’état actuel de la base de données active. Une fois terminé, la base de données restaurée est normal entièrement accessible en ligne facturée au taux normales en fonction de son niveau de performances et de niveau de service. Vous n’entraînez pas frais jusqu'à ce que la restauration de la base de données est terminée.

En règle générale, vous restaurez une base de données à un point d’antérieures à des fins de récupération. Lorsque vous procédez ainsi, vous pouvez considérer la base de données restaurée en guise de remplacement pour la base de données d’origine ou l’utiliser pour extraire des données et puis mettre à jour la base de données d’origine. 

- ***Remplacement de base de données :*** Si la base de données restaurée est destiné à remplacer la base de données d’origine, vous devez vérifier le niveau de performance et/ou niveau de service sont appropriée et une échelle de la base de données si nécessaire. Vous pouvez renommer la base de données d’origine et puis donnez à la base de données restaurée le nom d’origine à l’aide de la commande ALTER DATABASE dans T-SQL. 
- ***Récupération des données :*** Si vous envisagez d’extraire des données à partir de la base de données restaurée de récupérer une erreur utilisateur ou l’application, vous devrez séparément écrire et exécuter quelque scripts de récupération des données que vous avez besoin pour extraire des données à partir de la base de données restaurée à la base de données d’origine. Bien que l’opération de restauration peut prendre beaucoup de temps, la restauration de la base de données est visibles dans la liste de base de données dans l’ensemble. Si vous supprimez la base de données lors de la restauration, cela permet d’annuler l’opération et vous n’êtes pas facturée pour la base de données la restauration ne s’est pas terminée. 

Pour plus d’informations sur l’utilisation de la restauration de Point dans le temps pour récupérer les erreurs d’utilisateurs et des applications, voir restaurer [Point-à-temps](sql-database-recovery-using-backups.md#point-in-time-restore)

## <a name="deleted-database-restore"></a>Restaurer des bases de données supprimées

Restaurer des bases de données supprimées permet de restaurer une base de données supprimé à l’heure de suppression d’une base de données supprimé du même serveur logique à l’aide de la [que base de données SQL des sauvegardes automatisées](sql-database-automated-backups.md). 

> [AZURE.IMPORTANT] Si vous supprimez une instance du serveur de base de données SQL Azure, toutes ses bases de données sont également supprimés et ne peuvent pas être récupérés. Il n’existe aucune prise en charge pour restaurer un serveur supprimé pour l’instant.

Vous pouvez utiliser la même ou un nouveau nom de base de données pour la base de données restaurée. Vous pouvez utiliser le [portail Azure](sql-database-restore-deleted-database-portal.md), [PowerShell](sql-database-restore-deleted-database-powershell.md) ou la [reste (mode de création = restaurer)](https://msdn.microsoft.com/library/azure/mt163685.aspx). 

> [AZURE.SELECTOR]
- [Supprimés restauration de base de données : portail Azure](sql-database-restore-deleted-database-portal.md)
- [Supprimés restauration de base de données : PowerShell](sql-database-restore-deleted-database-powershell.md)

## <a name="geo-restore"></a>Geo-restaurer

Geo-restaurer permet de restaurer une base de données SQL sur n’importe quel serveur dans n’importe quelle région Azure à partir de la plus récente répliquées geo [effectuer des sauvegardes automatiques quotidiennes](sql-database-automated-backups.md). Geo-restaurer utilise une sauvegarde geo redondants comme source et peut servir à récupérer une base de données, même si la base de données ou le centre de données n’est pas accessible en raison d’une panne. Vous pouvez utiliser le [portail Azure](sql-database-geo-restore-portal.md), [PowerShell](sql-database-geo-restore-powershell.md), ou la [reste (mode de création = récupération)](https://msdn.microsoft.com/library/azure/mt163685.aspx) 

> [AZURE.SELECTOR]
- [Geo-restaurer : Portail Azure](sql-database-geo-restore-portal.md)
- [Geo-restaurer : PowerShell](sql-database-geo-restore-powershell.md)

Geo-restaurer est l’option de récupération par défaut lorsque votre base de données n’est pas disponible en raison d’un incident dans la région dans lequel la base de données est hébergé. Si un incident de grande échelle dans un entraîne région indisponibilité de votre application de base de données, vous pouvez utiliser Geo restaurer pour restaurer une base de données à partir de la sauvegarde la plus récente à un serveur dans n’importe quelle autre région. Toutes les sauvegardes sont répliquées geo et peuvent avoir un délai entre lorsque la sauvegarde est prise et geo répliquées vers la Azure blob dans une zone différente. Ce délai peut être jusqu'à une heure en cas de sinistre on peut observer la perte de données 1 heure, c'est-à-dire RPO jusqu'à 1 heure. La figure suivante montre la restauration de la base de données à partir de la dernière sauvegarde quotidienne.

![geo-restaurer](./media/sql-database-geo-restore/geo-restore-2.png)

Pour plus d’informations sur l’utilisation de Geo restaurer pour récupérer en cas de panne, voir [récupérer en cas de panne](sql-database-disaster-recovery.md)

> [AZURE.IMPORTANT] Geo-restaurer est disponible avec tous les niveaux de service, il est le plus simple des solutions de reprise disponibles dans la base de données SQL avec les points et les temps de récupération estimation (Insér) plus longue. Pour les bases de données de base avec une taille maximale de 2 Go Geo-restaurer fournit une solution DR raisonnablement avec un Insér de 12 heures. Plus grande Standard ou Premium bases de données, si beaucoup plus courte et récupérations sont souhaitées, ou pour réduire les risques de perdre des données vous envisagez d’utiliser la réplication Geo Active. Geo-réplication Active offre une RPO et Insér bien inférieur, car elle ne requiert que vous initier un basculement vers un secondaire répliqué en permanence. Pour plus d’informations, voir [Réplication de Geo Active](sql-database-geo-replication-overview.md).

## <a name="programmatically-performing-recovery-using-automated-backups"></a>Programme en effectuant récupération à l’aide des sauvegardes automatisées

Comme indiqué plus haut, addiition au portail Azure, récupération de base de données peut être effectuée programmically à l’aide de PowerShell Azure et l’API REST. Les tableaux ci-dessous décrivent l’ensemble des commandes disponibles.

### <a name="powershell"></a>PowerShell

|Applet de commande|Description|
|------|-----------|
|[Get-AzureRmSqlDatabase](https://msdn.microsoft.com/en-us/library/azure/mt603648.aspx)|Obtient un ou plusieurs bases de données.|
|[Get-AzureRMSqlDeletedDatabaseBackup](https://msdn.microsoft.com/en-us/library/azure/mt693387.aspx)|Obtient une base de données supprimé que vous pouvez restaurer.|
|[Get-AzureRmSqlDatabaseGeoBackup](https://msdn.microsoft.com/library/azure/mt693388.aspx)|Obtient une sauvegarde geo redondantes d’une base de données.|
|[Restaurer AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390.aspx)|Restaure une base de données SQL.|
||||

### <a name="rest-api"></a>API REST

|API|Description|
|---|-----------|
|[RESTE (mode de création = récupération)](https://msdn.microsoft.com/library/azure/mt163685.aspx)|Restaure une base de données|
|[Obtenir créer ou mettre à jour d’état de base de données](https://msdn.microsoft.com/library/azure/mt643934.aspx)|Renvoie l’état pendant une opération de restauration|
||||



## <a name="summary"></a>Résumé

Les sauvegardes automatiques protègent vos bases de données d’utilisateur et erreurs d’application, suppression accidentelle de la base de données et de pannes prolongées. Cette solution sans frais zéro-admin est disponible avec toutes les bases de données SQL. 

## <a name="next-steps"></a>Étapes suivantes

- Pour une vue d’ensemble de la continuité de gestion et scénarios, voir [vue d’ensemble de la continuité de gestion](sql-database-business-continuity.md)
- Pour en savoir plus sur les sauvegardes de base de données SQL Azure automatisé, voir [sauvegardes automatisées de base de données SQL](sql-database-automated-backups.md)
- Pour en savoir plus sur les options de récupération plus rapides, voir [Actif-Geo-réplication](sql-database-geo-replication-overview.md)  
- Pour en savoir plus sur l’utilisation des sauvegardes automatisées d’archivage, voir [Copier de base de données](sql-database-copy.md)
