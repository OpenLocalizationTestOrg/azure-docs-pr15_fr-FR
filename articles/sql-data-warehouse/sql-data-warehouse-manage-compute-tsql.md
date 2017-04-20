<properties
   pageTitle="Gérer la puissance de calcul dans Azure SQL données Warehouse (REST) | Microsoft Azure"
   description="Tâches de Transact-SQL (T-SQL) horizontale les performances en ajustant DWUs. Réduire les coûts en redimensionnant précédent pendant les heures de pointe."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/08/2016"
   ms.author="barbkess;sonyama"/>

# <a name="manage-compute-power-in-azure-sql-data-warehouse-t-sql"></a>Gérer l’alimentation cluster dans SQL Azure Data Warehouse (T-SQL)

> [AZURE.SELECTOR]
- [Vue d’ensemble](sql-data-warehouse-manage-compute-overview.md)
- [Portail](sql-data-warehouse-manage-compute-portal.md)
- [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
- [RESTE](sql-data-warehouse-manage-compute-rest-api.md)
- [TSQL](sql-data-warehouse-manage-compute-tsql.md)


Adapter les performances en faisant évoluer calculent des ressources et la mémoire pour répondre à la demande de modification de votre charge de travail. Réduire les coûts par des ressources précédent mise à l’échelle au cours des heures non pointe ou cluster suspension complètement. 

Cet ensemble de tâches utilise T-SQL pour :

- Afficher les paramètres DWU en cours
- Modifier les ressources de cluster en ajustant DWUs

Pour interrompre ou reprendre une base de données, choisissez une des autres options de plateforme en haut de cet article.

Pour plus d’informations, voir [vue d’ensemble de la puissance de calcul gérer][].

<a name="current-dwu-bk"></a>

## <a name="view-current-dwu-settings"></a>Afficher les paramètres DWU en cours

Pour afficher les paramètres actuels DWU pour vos bases de données :

1. Ouvrez l’Explorateur d’objets SQL Server dans Visual Studio 2015.
2. Se connecter à la base de données maître associé au serveur de base de données SQL logique.
2. Sélectionnez à partir de la vue de gestion dynamique sys.database_service_objectives. Voici un exemple : 

```
SELECT
 db.name [Database],
 ds.edition [Edition],
 ds.service_objective [Service Objective]
FROM
 sys.database_service_objectives ds
 JOIN sys.databases db ON ds.database_id = db.database_id
```

<a name="scale-dwu-bk"></a>
<a name="scale-compute-bk"></a>

## <a name="scale-compute"></a>Échelle cluster

[AZURE.INCLUDE [SQL Data Warehouse scale DWUs description](../../includes/sql-data-warehouse-scale-dwus-description.md)]

Pour modifier les DWUs :


1. Se connecter à la base de données principale associé à votre serveur de base de données SQL logique.
2. Utilisez l’instruction TSQL [ALTER DATABASE][] . L’exemple suivant définit l’objectif de niveau de service à DW1000 pour la base de données MySQLDW. 

```Sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000')
;
```

<a name="next-steps-bk"></a>

## <a name="next-steps"></a>Étapes suivantes

Pour d’autres tâches de gestion, voir [Présentation de la gestion][].

<!--Image references-->

<!--Article references-->
[Service capacity limits]: ./sql-data-warehouse-service-capacity-limits.md
[Présentation de la gestion]: ./sql-data-warehouse-overview-manage.md
[Gérer la vue d’ensemble de cluster power]: ./sql-data-warehouse-manage-compute-overview.md

<!--MSDN references-->

[MODIFIER LA BASE DE DONNÉES]: https://msdn.microsoft.com/library/mt204042.aspx


<!--Other Web references-->

[Azure portal]: http://portal.azure.com/
