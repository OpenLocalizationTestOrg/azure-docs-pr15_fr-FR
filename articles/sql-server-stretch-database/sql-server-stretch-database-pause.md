<properties
    pageTitle="Interrompre et reprendre la migration de données (base de données étirement) | Microsoft Azure"
    description="Découvrez comment interrompre ou reprendre la migration des données vers Azure."
    services="sql-server-stretch-database"
    documentationCenter=""
    authors="douglaslMS"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-server-stretch-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/14/2016"
    ms.author="douglasl"/>

# <a name="pause-and-resume-data-migration-stretch-database"></a>Interrompre et reprendre la migration de données (base de données étirement)

Pour interrompre ou reprendre la migration des données vers Azure, sélectionnez **étirement** pour une table dans SQL Server Management Studio, puis **faites glisser le pointeur** en pause la migration des données ou **reprendre** pour reprendre la migration de données. Vous pouvez également utiliser Transact\-SQL pour interrompre ou reprendre la migration des données.

Migration des données pause sur des tables individuelles lorsque vous voulez pour résoudre les problèmes sur le serveur local ou afin d’optimiser la bande passante réseau disponible.

## <a name="pause-data-migration"></a>Migration des données pause

### <a name="use-sql-server-management-studio-to-pause-data-migration"></a>Utiliser SQL Server Management Studio en pause la migration des données

1.  Dans SQL Server Management Studio, dans l’Explorateur d’objets, sélectionnez l’étirement\-activé table pour laquelle vous voulez suspendre la migration des données.

2.  Droite\-cliquez sur et sélectionnez **Étirer**, puis **Pause**.

### <a name="use-transact-sql-to-pause-data-migration"></a>Utiliser Transact\-SQL pour mettre en pause la migration des données
Exécutez la commande suivante.

```tsql
USE <Stretch-enabled database name>;
GO
ALTER TABLE <Stretch-enabled table name>  
    SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = PAUSED ) ) ;  
GO
```

## <a name="resume-data-migration"></a>Reprendre la migration de données

### <a name="use-sql-server-management-studio-to-resume-data-migration"></a>Utiliser SQL Server Management Studio pour reprendre la migration de données

1.  Dans SQL Server Management Studio, dans l’Explorateur d’objets, sélectionnez l’étirement\-activé table pour laquelle vous souhaitez reprendre la migration de données.

2.  Droite\-cliquez sur et sélectionnez **Étirer**, puis **CV**.

### <a name="use-transact-sql-to-resume-data-migration"></a>Utiliser Transact\-SQL pour reprendre la migration de données
Exécutez la commande suivante.

```tsql
USE <Stretch-enabled database name>;
GO
ALTER TABLE <Stretch-enabled table name>   
    SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = OUTBOUND ) ) ;  
 GO
```

## <a name="check-whether-migration-is-active-or-paused"></a>Vérifier si la migration est actif ou en pause

### <a name="use-sql-server-management-studio-to-check-whether-migration-is-active-or-paused"></a>SQL Server Management Studio permet de vérifier si la migration est actif ou en pause
Dans SQL Server Management Studio, ouvrez **l’Analyseur de base de données étirement** et vérifiez la valeur de la colonne **État de la Migration** . Pour plus d’informations, voir [moniteur et résoudre les problèmes de migration des données](sql-server-stretch-database-monitor.md).

### <a name="use-transact-sql-to-check-whether-migration-is-active-or-paused"></a>Utiliser Transact-SQL pour vérifier si la migration est actif ou en pause
Interroger la vue de catalogue **sys.remote_data_archive_tables** et vérifiez la valeur de la colonne **is_migration_paused** . Pour plus d’informations, voir [sys.remote_data_archive_tables](https://msdn.microsoft.com/library/dn935003.aspx).

## <a name="see-also"></a>Voir aussi

[ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx)
[moniteur et résoudre les problèmes de migration des données](sql-server-stretch-database-monitor.md)
