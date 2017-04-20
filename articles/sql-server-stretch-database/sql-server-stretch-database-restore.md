<properties
    pageTitle="Restaurer des bases de données compatibles avec étirement | Microsoft Azure"
    description="Découvrez comment restaurer étirement\-activé les bases de données."
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
    ms.date="08/01/2016"
    ms.author="douglasl"/>

# <a name="restore-stretch-enabled-databases"></a>Restaurer des bases de données compatibles avec étirement

Restaurer une sauvegarde la base de données lorsque cela est nécessaire pour récupérer de nombreux types d’échecs, des erreurs et des incidents.

Pour plus d’informations sur la sauvegarde, voir [bases de données compatibles avec étirement de sauvegarde](sql-server-stretch-database-backup.md).

>   [AZURE.NOTE] Sauvegarde est uniquement une partie d’une disponibilité complète et la solution de continuité. Pour plus d’informations sur la disponibilité, voir [Haute disponibilité des Solutions](https://msdn.microsoft.com/library/ms190202.aspx).

## <a name="restore-your-sql-server-data"></a>Restaurer vos données SQL Server
Pour récupérer de panne matérielle ou corruption, restaurer l’étirement\-activé base de données SQL Server à partir d’une sauvegarde. Vous pouvez continuer à utiliser les méthodes de restauration de SQL Server que vous utilisez actuellement. Pour plus d’informations, consultez [Présentation de la récupération et de restauration](https://msdn.microsoft.com/library/ms191253.aspx).

Une fois que vous restaurez la base de données SQL Server, vous devez exécuter la procédure stockée **sys.sp_rda_reauthorize_db** pour rétablir la connexion entre l’étirement\-activé la base de données SQL Server et la base de données Azure à distance. Pour plus d’informations, voir [restaurer la connexion entre la base de données SQL Server et la base de données Azure à distance](#restore-the-connection-between-the-sql-server-database-and-the-remote-azure-database).

## <a name="restore-your-remote-azure-data"></a>Restaurer vos données Azure à distance

### <a name="recover-a-live-azure-database"></a>Récupérer une base de données Azure Active
La base de données SQL Server étirement du service sur Azure instantanés toutes les données actives au moins toutes les 8 heures à l’aide d’instantanés de stockage Azure. Ces instantanés sont conservées pendant 7 jours. Cela vous permet de restaurer les données à l’un des points d’au moins 21 dans un délai de 7 derniers jours jusqu'à l’heure lorsque l’instantané dernière.

Pour restaurer une base de données Azure active à un point antérieur dans le temps à l’aide du portail Azure, effectuer les opérations suivantes.

1. Connectez-vous au portail Azure.
2. Sur le côté gauche de l’écran Sélectionnez **Parcourir** , puis sélectionnez **Bases de données SQL**.
3. Accédez à votre base de données et le sélectionner.
4. Dans la partie supérieure de la cuillère de base de données, cliquez sur **restaurer**.
5. Spécifiez un nouveau **nom de la base de données**, sélectionnez un **Point de restauration** , puis sur **créer**.
6. Le processus de restauration de base de données commence et peut être contrôlé à l’aide de **NOTIFICATIONS**.

### <a name="recover-a-deleted-azure-database"></a>Restaurer une base de données Azure supprimé
Le service de base de données SQL Server étirer sur Azure prend un instantané de base de données avant d’une base de données est supprimée et conserve pendant 7 jours. Une fois que cela se produit, elle conserve n’est plus instantanés à partir de la base de données active. Cela vous permet de restaurer une base de données supprimé au point lorsqu’il a été supprimé.

Pour restaurer une base de données Azure supprimé du point lorsqu’il a été supprimé à l’aide du portail Azure, effectuer les opérations suivantes.

1. Connectez-vous au portail Azure.
2. Sur le côté gauche de l’écran Sélectionnez **Parcourir** , puis sélectionnez **Serveurs SQL Server**.
3. Accédez à votre serveur et sélectionnez-le.
4. Faites défiler vers le bas jusqu'à opérations sur carte de votre serveur, cliquez sur la vignette de **Bases de données supprimées** .
5. Sélectionnez la base de données supprimé que vous souhaitez restaurer.
5. Spécifiez un nouveau **nom de la base de données** et cliquez sur **créer**.
6. Le processus de restauration de base de données commence et peut être contrôlé à l’aide de **NOTIFICATIONS**.

## <a name="restore-the-connection-between-the-sql-server-database-and-the-remote-azure-database"></a>Restaurer la connexion entre la base de données SQL Server et la base de données Azure à distance

1.  Si vous comptez pour vous connecter à une base de données Azure restaurée avec un autre nom ou dans une zone différente, exécutez la procédure stockée [sys.sp_rda_deauthorize_db](https://msdn.microsoft.com/library/mt703716.aspx) pour vous déconnecter de la base de données Azure précédent.  

2.  Exécuter la procédure stockée [sys.sp_rda_reauthorize_db](https://msdn.microsoft.com/library/mt131016.aspx) pour vous reconnecter l’étirement local\-activé la base de données à la base de données Azure.  

    -   Fournir les informations d’identification inclus dans l’étendue de la base de données existante comme un sysname ou un varchar\(128\) valeur. \(N’utilisez pas varchar\(max\).\) Vous pouvez rechercher le nom d’informations d’identification dans la vue **sys.database\_portée\_informations d’identification**.  

    -   Spécifiez si vous voulez faire une copie des données à distance et se connecter à la copie (recommandée).  

    ```tsql  
    USE <Stretch-enabled database name>;
    GO
    EXEC sp_rda_reauthorize_db
        @credential = N'<existing_database_scoped_credential_name>',
        @with_copy = 1 ;  
    GO
    ```  

## <a name="see-also"></a>Voir aussi

[Gérer et résoudre les problèmes de base de données étirement](sql-server-stretch-database-manage.md)

[Sys.sp_rda_reauthorize_db (Transact-SQL)](https://msdn.microsoft.com/library/mt131016.aspx)

[Sauvegarder et restaurer des bases de données SQL Server](https://msdn.microsoft.com/library/ms187048.aspx)
