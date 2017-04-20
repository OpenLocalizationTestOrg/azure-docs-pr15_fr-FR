<properties
    pageTitle="Désactiver la base de données étirement et ramener données distantes | Microsoft Azure"
    description="Découvrez comment désactiver l’étirement de base de données pour une table et vous pouvez également faire réapparaître données distantes."
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
    ms.date="08/05/2016"
    ms.author="douglasl"/>

# <a name="disable-stretch-database-and-bring-back-remote-data"></a>Désactiver la base de données étirement et ramener des données distantes

Pour désactiver l’étirement de base de données pour une table, sélectionnez **étirement** pour une table dans SQL Server Management Studio. Sélectionnez ensuite une des options suivantes.

-   Désactiver **| Restaurer les données à partir d’Azure**. Copier les données distantes pour la table à partir d’Azure retour à SQL Server, puis désactivez étirement de base de données pour la table. Cette opération entraîne des coûts de transfert de données, et elle ne peut pas être annulée.

-   Désactiver **| Laisser les données dans Azure**. Désactiver étirement de base de données pour la table.  Abandonner les données distantes pour la table dans Azure.

Vous pouvez également utiliser Transact\-SQL pour désactiver étirement de base de données pour une table ou une base de données.

Après avoir désactivé étirement de base de données pour une table, cesse de migration de données et les résultats de la requête n’incluront plus de résultats de la table à distance.

Si vous souhaitez simplement en pause la migration de données, voir [Suspendre et reprendre étirement de base de données](sql-server-stretch-database-pause.md).

>   [AZURE.NOTE] Désactivation de la base de données étirement pour une table ou une base de données ne supprime pas l’objet distant. Si vous voulez supprimer la table distante ou la base de données distante, vous devez glisser à l’aide du portail de gestion Azure. Les objets distants continuent d’engager des coûts Azure jusqu'à ce que vous les supprimez. Pour plus d’informations, voir [Tarifs de base de données SQL Server étirement](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).

## <a name="disable-stretch-database-for-a-table"></a>Désactiver l’étirement de base de données pour un tableau

### <a name="use-sql-server-management-studio-to-disable-stretch-database-for-a-table"></a>Permet de désactiver étirement de base de données pour une table SQL Server Management Studio

1.  Dans SQL Server Management Studio, dans l’Explorateur d’objets, sélectionnez la table pour laquelle vous voulez désactiver étirement de base de données.

2.  Droite\-cliquez sur et sélectionnez **étirement**puis sélectionnez une des options suivantes.

    -   Désactiver **| Restaurer les données à partir d’Azure**. Copier les données distantes pour la table à partir d’Azure retour à SQL Server, puis désactivez étirement de base de données pour la table. Cette commande ne peut pas être annulée.

        >   [AZURE.NOTE] Copier les données distantes pour la table à partir d’Azure précédent pour SQL Server entraîne des frais de transfert de données. Pour plus d’informations, voir [Les détails de tarification transferts de données](https://azure.microsoft.com/pricing/details/data-transfers/).

        Une fois toutes les données distantes a été copiées à partir d’Azure sauvegardez sur SQL Server, étirement est désactivé pour la table.

    -   Désactiver **| Laisser les données dans Azure**. Désactiver étirement de base de données pour la table.  Abandonner les données distantes pour la table dans Azure.

    >   [AZURE.NOTE] Désactivation de la base de données étirement pour une table ne supprime pas les données distantes ou la table distante. Si vous souhaitez supprimer le tableau à distance, vous devez glisser à l’aide du portail de gestion Azure. La table distante continue à engager coûts Azure jusqu'à ce que vous supprimiez. Pour plus d’informations, voir [Tarifs de base de données SQL Server étirement](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).

### <a name="use-transact-sql-to-disable-stretch-database-for-a-table"></a>Utiliser Transact\-SQL pour désactiver étirement de base de données pour une table

-   Pour désactiver l’étirement pour une table et une copie des données à distance pour la table à partir d’Azure à SQL Server, exécutez la commande suivante. Une fois toutes les données distantes a été copiées à partir d’Azure sauvegardez sur SQL Server, étirement est désactivé pour la table.

    Cette commande ne peut pas être annulée.

    ```tsql
    USE <Stretch-enabled database name>;
    GO
    ALTER TABLE <Stretch-enabled table name>  
       SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = INBOUND ) ) ;
    GO
    ```
    >   [AZURE.NOTE] Copier les données distantes pour la table à partir d’Azure précédent pour SQL Server entraîne des frais de transfert de données. Pour plus d’informations, voir [Les détails de tarification transferts de données](https://azure.microsoft.com/pricing/details/data-transfers/).

-   Pour désactiver l’étirement pour une table et abandonner les données distantes, exécutez la commande suivante.

    ```tsql
    ALTER TABLE <table_name>
       SET ( REMOTE_DATA_ARCHIVE = OFF_WITHOUT_DATA_RECOVERY ( MIGRATION_STATE = PAUSED ) ) ;
    ```

>   [AZURE.NOTE] Désactivation de la base de données étirement pour une table ne supprime pas les données distantes ou la table distante. Si vous souhaitez supprimer le tableau à distance, vous devez glisser à l’aide du portail de gestion Azure. La table distante continue à engager coûts Azure jusqu'à ce que vous supprimiez. Pour plus d’informations, voir [Tarifs de base de données SQL Server étirement](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).

## <a name="disable-stretch-database-for-a-database"></a>Désactiver l’étirement de base de données pour une base de données
Vous pouvez désactiver l’étirement de base de données pour une base de données, vous devez désactiver étirement de base de données sur l’étirement individuel\-activé les tables dans la base de données.

### <a name="use-sql-server-management-studio-to-disable-stretch-database-for-a-database"></a>Permet de désactiver l’étirement de base de données pour une base de données SQL Server Management Studio

1.  Dans SQL Server Management Studio, dans l’Explorateur d’objets, sélectionnez la base de données pour laquelle vous voulez désactiver étirement de base de données.

2.  Droite\-cliquez sur **tâches**et puis sélectionnez **étirement**et sélectionnez puis sélectionnez **désactiver**.

>   [AZURE.NOTE] Désactivation de l’étirement de base de données pour une base de données ne supprime pas la base de données distante. Si vous souhaitez supprimer la base de données distante, vous devez glisser à l’aide du portail de gestion Azure. La base de données distante continue à engager coûts Azure jusqu'à ce que vous supprimiez. Pour plus d’informations, voir [Tarifs de base de données SQL Server étirement](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).

### <a name="use-transact-sql-to-disable-stretch-database-for-a-database"></a>Utiliser Transact\-SQL pour désactiver étirement de base de données pour une base de données
Exécutez la commande suivante.

```tsql
ALTER DATABASE <database name>
    SET REMOTE_DATA_ARCHIVE = OFF ;
```

>   [AZURE.NOTE] Désactivation de l’étirement de base de données pour une base de données ne supprime pas la base de données distante. Si vous souhaitez supprimer la base de données distante, vous devez glisser à l’aide du portail de gestion Azure. La base de données distante continue à engager coûts Azure jusqu'à ce que vous supprimiez. Pour plus d’informations, voir [Tarifs de base de données SQL Server étirement](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).

## <a name="see-also"></a>Voir aussi

[Base de données ALTER définir les Options (Transact-SQL)](https://msdn.microsoft.com/library/bb522682.aspx)

[Interrompre et reprendre étirement de base de données](sql-server-stretch-database-pause.md)
