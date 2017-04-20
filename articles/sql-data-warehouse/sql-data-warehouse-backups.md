<properties
   pageTitle="Des sauvegardes SQL Data Warehouse | Microsoft Azure"
   description="Obtenir des informations à propos des sauvegardes intégrés de la base de données SQL Data Warehouse qui vous permettent de restaurer une SQL Azure Data Warehouse sur un point de restauration ou une région géographique différente."
   services="sql-data-warehouse"
   documentationCenter=""
   authors="lakshmi1812"
   manager="barbkess"
   editor="monicar"/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/06/2016"
   ms.author="lakshmir;barbkess"/>

# <a name="sql-data-warehouse-backups"></a>Sauvegardes SQL Data Warehouse

SQL Data Warehouse propose des sauvegardes locales et géographiques dans le cadre de ses capacités de sauvegarde de magasin de données. Ceux-ci incluent des instantanés Azure stockage Blob et stockage geo redondants. Utiliser les données entrepôt sauvegardes pour restaurer votre data warehouse à un point de restauration dans la région principale ou restaurer dans une autre région géographique. Cet article explique les détails des sauvegardes dans Data Warehouse SQL.

## <a name="what-is-a-data-warehouse-backup"></a>Qu’est une sauvegarde de magasin de données ?

Une sauvegarde de magasin de données est les données que vous pouvez utiliser pour restaurer un data warehouse à un moment spécifique.  Dans la mesure où SQL Data Warehouse est un système distribué, une sauvegarde de magasin de données se compose de nombreux fichiers qui sont stockés dans des objets BLOB Azure. 

Sauvegardes de base de données sont une partie essentielle de toute stratégie récupération continuité et de reprise d’entreprise, car ils protègent vos données contre toute corruption accidentelle ou la suppression. Pour plus d’informations, voir [vue d’ensemble de la continuité de gestion](../sql-database/sql-database-business-continuity.md).

## <a name="data-redundancy"></a>Redondance des données

Data Warehouse SQL protège vos données en stockant vos données dans le stockage de Premium Azure localement redondantes (LRS). Cette fonctionnalité de stockage Azure stocke plusieurs copies synchrones des données dans le centre de données locales afin de garantir la protection des données transparente s’il existe des échecs localisés. Redondance des données garantit que vos données peuvent survie problèmes infrastructure tels que des problèmes de disque. Redondance des données garantit la continuité des activités avec une tolérance de panne et infrastructure hautement disponible.

En savoir plus sur :

- Stockage Premium Azure, voir [Introduction au stockage de Premium Azure](../storage/storage-premium-storage.md).
- Stockage localement redondants, voir [réplication de stockage Azure](../storage/storage-redundancy.md#locally-redundant-storage).


## <a name="azure-storage-blob-snapshots"></a>Instantanés de stockage Blob Azure

Comme un avantage de l’utilisation du stockage de Premium Azure, SQL Data Warehouse utilise des instantanés Azure stockage Blob de sauvegarde du data warehouse localement. Vous pouvez restaurer un data warehouse sur un point de restauration instantané. Instantanés démarrer un minimum de toutes les huit heures et sont disponibles pour les sept jours.  

En savoir plus sur :

- Des instantanés blob Azure, voir [créer une capture instantanée blob](../storage/storage-blob-snapshots.md).


## <a name="geo-redundant-backups"></a>Sauvegardes geo redondantes

Toutes les 24 heures, SQL Data Warehouse stocke le data warehouse complet dans le stockage Standard. Le data warehouse complet est créé pour correspondre à l’heure de la dernière capture instantanée. Le stockage standard appartient à un compte de stockage geo redondants avec accès en lecture (RA GRS). La fonctionnalité de stockage de Azure RA-GRS réplique les fichiers de sauvegarde sur un [Centre de données appariés](../best-practices-availability-paired-regions.md). Cette réplication geo garantit que vous pouvez restaurer entrepôt de données au cas où vous ne peut pas accéder à des instantanés dans votre région principale. 

Cette fonctionnalité est activée par défaut. Si vous ne voulez pas utiliser les sauvegardes geo redondantes, vous pouvez désactiver cette fonctionnalité. 

>[AZURE.NOTE] Dans le stockage Azure, le terme *réplication* fait référence à la copie des fichiers d’un emplacement vers un autre. De SQL *réplication de base de données* fait référence à tout en conservant vers plusieurs bases de données secondaires synchronisées avec une base de données principale. 

En savoir plus sur :
- Stockage geo redondantes, voir [réplication de stockage Azure](../storage/storage-redundancy.md).
- Stockage RA GRS, voir [le stockage geo redondants accès en lecture](../storage/storage-redundancy.md#read-access-geo-redundant-storage).

## <a name="data-warehouse-backup-schedule-and-retention-period"></a>Planification de la sauvegarde et de la période de rétention du Data warehouse

Data Warehouse SQL crée des instantanés sur vos magasins de données en ligne toutes les quatre à huit heures et conserve chaque instantané pendant sept jours. Vous pouvez restaurer votre base de données en ligne à un des points de restauration dans les sept derniers jours. 

Pour afficher au démarrage de la dernière capture instantanée, exécutez cette requête sur votre SQL Data Warehouse en ligne. 

```sql
select top 1 *
from sys.pdw_loader_backup_runs 
order by run_id desc;
```

Si vous avez besoin de conserver un instantané pendant plus de sept jours, vous pouvez restaurer un point de restauration vers un nouveau data warehouse. Une fois la restauration terminée, SQL Data Warehouse commence la création d’instantanés sur le nouveau data warehouse. Si vous n’apportez des modifications pour le nouveau data warehouse, les instantanés restent vides et par conséquent le coût instantané est minime. Vous pouvez également suspendre la base de données pour empêcher la création d’instantanés SQL Data Warehouse.


### <a name="what-happens-to-my-backup-retention-while-my-data-warehouse-is-paused"></a>Qu’arrive-t-il à mes rétention sauvegarde lorsque mon magasin de données est en pause ?

SQL Data Warehouse ne crée pas des instantanés et n’expire pas instantanés pendant qu’un data warehouse est suspendu. L’âge instantané ne change pas lorsque le data warehouse est en pause. Rétention instantané est basée sur le nombre de jours que du data warehouse est en ligne, pas les jours de calendrier.

Par exemple, si un instantané démarre octobre 1 à 4 heures et le data warehouse est suspendu octobre 3 à 4 heures, l’instantané est deux derniers jours. Chaque fois que le data warehouse revient en ligne l’instantané est deux derniers jours. Si le data warehouse est en ligne 5 octobre à 4 heures, l’instantané deux jours et reste 5 jours plus.

Lorsque le data warehouse revient en ligne, Data Warehouse SQL reprend les nouveaux clichés et arrive à expiration instantanés lorsqu’ils ont plus de sept jours de données.

### <a name="how-long-is-the-retention-period-for-a-dropped-data-warehouse"></a>Combien de temps est la période de rétention pour un ignorés data warehouse ?
Lorsqu’un data warehouse est supprimé, le data warehouse et les instantanés sont enregistrés pour les sept jours et puis supprimés. Vous pouvez restaurer le data warehouse à un des points de restauration enregistrés.

> [AZURE.IMPORTANT] Si vous supprimez une instance SQL server logique, toutes les bases de données qui appartiennent à l’instance sont également supprimés et ne peuvent pas être récupérés. Vous ne pouvez pas restaurer un serveur supprimé.

## <a name="data-warehouse-backup-costs"></a>Coûts de sauvegarde données entrepôt

Le coût total de votre entrepôt de données principale et sept jours d’instantanés Blob Azure est arrondi à la plus proche to. Par exemple, si votre data warehouse est 1,5 To et les instantanés utilisent 100 Go, vous êtes facturé pour 2 To de données à des taux de stockage Premium Azure. 

>[AZURE.NOTE] Chaque instantané est vide initialement et augmente à mesure que vous apportez des modifications au magasin de données principale. Tous les instantanés augmentent la taille en tant que les modifications de magasin de données. Par conséquent, les frais de stockage pour des instantanés agrandir en fonction de la fréquence de modification.

Si vous utilisez le stockage geo redondantes, vous payez des frais stockage distinct. Le stockage geo redondants est facturé au taux standard accès en lecture géographiquement stockage redondants (RA GRS).

Pour plus d’informations sur les tarifs Data Warehouse SQL, voir [Tarifs de magasin de données SQL](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

## <a name="using-database-backups"></a>Utilisation de sauvegardes de base de données

L’utilisation des sauvegardes de magasin de données SQL principale consiste à restaurer le data warehouse à un des points de restauration au sein de la période de rétention.  

- Pour restaurer un data warehouse SQL, voir [restaurer un data warehouse SQL](sql-data-warehouse-restore-database-overview.md).


## <a name="related-topics"></a>Rubriques connexes

### <a name="scenarios"></a>Scénarios

- Pour une vue d’ensemble de la continuité d’entreprise, voir [vue d’ensemble de la continuité de l’activité entreprise](../sql-database/sql-database-business-continuity.md)


<!-- ### Tasks -->

- Pour restaurer un data warehouse, voir [restaurer un data warehouse SQL](sql-data-warehouse-restore-database-overview.md)

<!-- ### Tutorials -->

