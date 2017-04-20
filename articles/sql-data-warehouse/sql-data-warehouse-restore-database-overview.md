<properties
   pageTitle="Restaurer SQL Data Warehouse | Microsoft Azure"
   description="Vue d’ensemble des options de restauration de base de données pour restaurer une base de données SQL Azure Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="Lakshmi1812"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/29/2016"
   ms.author="lakshmir;barbkess;sonyama"/>


# <a name="sql-data-warehouse-restore"></a>Restaurer Data Warehouse SQL

> [AZURE.SELECTOR]
- [Vue d’ensemble][]
- [Portail][]
- [PowerShell][]
- [RESTE][]

Data Warehouse SQL offre des fonctionnalités locales et géographique restaure dans le cadre de ses sinistre entrepôt données récupération. Utiliser les données warehouse sauvegardes pour restaurer votre data warehouse à un point de restauration dans la région principale, ou utilisez des sauvegardes geo redondantes pour restaurer une région géographique différente. Cet article explique les caractéristiques de la restauration d’un data warehouse.

## <a name="what-is-a-data-warehouse-restore"></a>Qu’est une restauration de magasin de données ?

Une restauration de magasin de données est un nouveau data warehouse créé à partir d’une sauvegarde de configuration existant ou un supprimés data warehouse. Restauré data warehouse recrée sauvegardée data warehouse à un moment donné. Dans la mesure où SQL Data Warehouse est un système distribué, une restauration de magasin de données est créée à partir de nombreux fichiers de sauvegarde qui sont stockés dans des objets BLOB Azure. 

Restauration de la base de données est une partie essentielle de toute stratégie récupération continuité et de reprise d’entreprise, car il crée à nouveau vos données après toute corruption accidentelle ou la suppression.

Pour plus d’informations, voir :

-  [Sauvegardes SQL Data Warehouse](sql-data-warehouse-backups.md)
-  [Vue d’ensemble de la continuité de gestion](../sql-database/sql-database-business-continuity.md)

## <a name="data-warehouse-restore-points"></a>Points de restauration de magasin de données

Comme un avantage de l’utilisation du stockage de Premium Azure, SQL Data Warehouse utilise des instantanés Azure stockage Blob pour sauvegarder le magasin de données principale. Chaque instantané a un point de restauration qui représente l’heure de début de la capture instantanée. Pour restaurer un data warehouse, vous choisissez un point de restauration et exécutez une commande restaurer.  

Data Warehouse SQL restaure toujours la sauvegarde vers un nouveau data warehouse. Vous pouvez conserver le restauré data warehouse et celui en cours, ou supprimer un d’eux. Si vous souhaitez remplacer le data warehouse actuel et restaurés data warehouse, vous pouvez la renommer.

Si vous avez besoin restaurer un supprimés ou suspendus data warehouse, vous pouvez [créer une demande d’assistance](sql-data-warehouse-get-started-create-support-ticket.md). 

<!-- 
### Can I restore a deleted data warehouse?

Yes, you can restore the last available restore point.

Yes, for the next seven calendar days. When you delete a data warehouse, SQL Data Warehouse actually keeps the data warehouse and its snapshots for seven days just in case you need the data. After seven days, you won't be able to restore to any of the restore points. -->

## <a name="geo-redundant-restore"></a>Restaurer geo redondants

Si vous utilisez le stockage geo redondantes, vous pouvez restaurer le data warehouse à votre [Centre de données pour](../best-practices-availability-paired-regions.md) dans une région géographique différente. Le data warehouse est restauré à partir de la dernière sauvegarde quotidienne. 

## <a name="restore-timeline"></a>Restaurer la barre de planning

Vous pouvez restaurer une base de données à un point de restauration disponibles depuis les sept derniers jours. Instantanés toutes les heures quatre à huit et sont disponibles pour les sept jours. Lorsqu’un instantané est plu de sept jours, il arrive à expiration et son point de restauration n’est plus disponible.

## <a name="restore-costs"></a>Restaurer les coûts

Les frais de stockage pour le magasin de données restaurées sont facturée au taux le stockage Azure Premium. 

Si vous interrompez un restauré data warehouse, vous êtes chargé de stockage au taux d’espace de stockage Azure Premium. L’avantage de la pause, que vous ne seront pas imputés pour les ressources informatiques DWU.

Pour plus d’informations sur les tarifs Data Warehouse SQL, voir [Tarifs de magasin de données SQL](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

## <a name="uses-for-restore"></a>Utilisations pour restaurer

La principale utilisation de données warehouse restaurer consiste à récupérer les données après la perte de données accidentelle ou corruption.

Vous pouvez également utiliser une restauration des données entrepôt pour conserver une sauvegarde pendant plus de sept jours. Une fois que la sauvegarde est restaurée, vous disposez du data warehouse en ligne et pouvez suspendre indéfiniment pour calculer les coûts. La base de données en pause entraîne des frais de stockage à la vitesse de stockage Premium Azure. 

## <a name="related-topics"></a>Rubriques connexes

### <a name="scenarios"></a>Scénarios

- Pour une vue d’ensemble de la continuité d’entreprise, voir [vue d’ensemble de la continuité de l’activité entreprise](../sql-database/sql-database-business-continuity.md)


<!-- ### Tasks -->

Pour effectuer une restauration de magasin de données, restaurer à l’aide de :

- Portail Azure, voir [restaurer un data warehouse à l’aide du portail Azure](sql-data-warehouse-restore-database-portal.md)
- Applets de commande PowerShell, voir [restaurer un data warehouse à l’aide des applets de commande PowerShell](sql-data-warehouse-restore-database-powershell.md)
- POSITIONNEZ API, voir [restaurer un data warehouse à l’aide de l’API REST](sql-data-warehouse-restore-database-rest-api.md)

<!-- ### Tutorials -->

<!--Image references-->

<!--Article references-->
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Vue d’ensemble]: ./sql-data-warehouse-restore-database-overview.md
[Portail]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[RESTE]: ./sql-data-warehouse-restore-database-rest-api.md

<!--MSDN references-->


<!--Other Web references-->
