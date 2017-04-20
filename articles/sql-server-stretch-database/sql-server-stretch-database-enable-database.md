<properties
    pageTitle="Activer étirement de base de données pour une base de données | Microsoft Azure"
    description="Découvrez comment configurer une base de données de base de données étirement."
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

# <a name="enable-stretch-database-for-a-database"></a>Activer étirement de base de données pour une base de données

Pour configurer une base de données existante pour étirement de base de données, sélectionnez tâches **| Étirer | Activer** pour une base de données dans SQL Server Management Studio pour ouvrir l’Assistant **Activer la base de données pour étirement** . Vous pouvez également utiliser Transact\-SQL pour activer étirement de base de données pour une base de données.

Si vous sélectionnez tâches **| Étirer | Activer** pour une table individuelle et que vous n'avez pas encore activé la base de données pour étirement de base de données, l’Assistant configure la base de données de base de données étirement et vous permet de sélectionner des tables dans le cadre du processus. Suivez les étapes décrites dans cette rubrique au lieu des étapes décrites dans [Activer étirement de base de données pour une table](sql-server-stretch-database-enable-database.md).

L’activation étirement de base de données dans une base de données ou un tableau nécessite db\_autorisations de propriétaire. Activation étirement de base de données sur une base de données requiert également des autorisations de contrôle de base de données.

 >   [AZURE.NOTE] Plus tard, si vous désactivez étirement de base de données, n’oubliez pas que la désactivation de la base de données étirement pour une table ou une base de données ne supprime pas l’objet distant. Si vous voulez supprimer la table distante ou la base de données distante, vous devez glisser à l’aide du portail de gestion Azure. Les objets distants continuent d’engager des coûts Azure jusqu'à ce que vous les supprimez manuellement.

## <a name="before-you-get-started"></a>Avant de commencer

-   Avant de configurer une base de données pour étirer, nous vous recommandons d’exécuter le Gestionnaire de base de données étirement pour identifier les bases de données et les tables qui sont éligibles pour étirement. Le Gestionnaire de base de données étirement identifie également les problèmes de blocage. Pour plus d’informations, voir [identifier les bases de données et des tables de base de données étirement](sql-server-stretch-database-identify-databases.md).

-   Passez en revue [Limitations pour étirement la base de données](sql-server-stretch-database-limitations.md).

-   Base de données étirement migre des données vers Azure. Par conséquent, vous devez disposer d’un compte Azure et un abonnement pour facturation. Pour obtenir un compte Azure, [Cliquez ici](http://azure.microsoft.com/pricing/free-trial/).

-   Disposez les informations de connexion et de connexion que vous avez besoin pour créer un nouveau serveur Azure ou pour sélectionner un serveur Azure existant.

## <a name="EnableTSQLServer"></a>Au préalable : Activer étirement de base de données sur le serveur
Avant de pouvoir activer étirement de base de données dans une base de données ou un tableau, vous devez activer sur le serveur local. Cette opération nécessite des autorisations d’administrateur système ou serveradmin.

-   Si vous avez des autorisations d’administration, l’Assistant **Activer la base de données pour étirement** configure le serveur pour étirement.

-   Si vous n’avez pas les autorisations requises, un administrateur doit activer cette option manuellement en exécutant **sp\_configurer** avant d’exécuter l’Assistant ou dispose d’un administrateur exécuter l’Assistant.

Pour activer manuellement étirement de base de données sur le serveur, exécutez **sp\_configurer** et activez l’option **archive de données distante** . L’exemple suivant active l’option **archive de données distante** , définissez sa valeur sur 1.

```
EXEC sp_configure 'remote data archive' , '1';
GO

RECONFIGURE;
GO
```
Pour plus d’informations, voir [configurer les données distantes archiver Option Configuration du serveur](https://msdn.microsoft.com/library/mt143175.aspx) et [sp_configure (Transact-SQL)](https://msdn.microsoft.com/library/ms188787.aspx).

## <a name="Wizard"></a>Utiliser l’Assistant pour activer étirement de base de données sur une base de données
Pour plus d’informations sur la base de données activer étirement Assistant, y compris les informations que vous devrez entrer et les choix que vous devez apporter, voir [prise en main en exécutant la base de données pour étirement Assistant Activer](sql-server-stretch-database-wizard.md).

## <a name="EnableTSQLDatabase"></a>Utiliser Transact\-SQL pour activer étirement de base de données sur une base de données
Avant de pouvoir activer étirement de base de données dans des tables individuelles, vous devez activer la base de données.

L’activation étirement de base de données dans une base de données ou un tableau nécessite db\_autorisations de propriétaire. Activation étirement de base de données sur une base de données requiert également des autorisations de contrôle de base de données.

1.  Avant de commencer, choisissez un serveur Azure existant pour les données qui permet de migrer étirement de base de données, ou créer un nouveau serveur Azure.

2.  Sur le serveur Azure, créez une règle de pare-feu avec la plage d’adresses IP du serveur SQL qui vous permet de SQL Server communiquer avec le serveur distant.

    Vous pouvez facilement rechercher les valeurs que vous avez besoin et créez la règle de pare-feu lorsque vous tentez de vous connecter au serveur Azure à partir de l’Explorateur d’objets dans SQL Server Management Studio (SSMS). SSMS vous aide à créer la règle en ouvrant la boîte de dialogue qui déjà inclut les valeurs des adresses IP requis.

    ![Créer une règle de pare-feu dans SSMS][FirewallRule]

3.  Pour configurer une base de données SQL Server pour étirement de base de données, la base de données doit comporter une clé principale de base de données. La clé principale de base de données permet de sécuriser les informations d’identification étirement de base de données pour se connecter à la base de données distante. Voici un exemple qui crée une nouvelle clé principale de base de données.

    ```tsql
    USE <database>;
    GO

    CREATE MASTER KEY ENCRYPTION BY PASSWORD ='<password>';
    GO
    ```

    Pour plus d’informations sur la clé principale de base de données, voir [créer une clé de masque (Transact-SQL)](https://msdn.microsoft.com/library/ms174382.aspx) et [créer une clé principale de base de données](https://msdn.microsoft.com/library/aa337551.aspx).

4.  Lorsque vous configurez une base de données pour étirement de base de données, vous devez fournir les informations d’identification pour étirement de base de données à utiliser pour les communications entre les locaux sur SQL Server et le serveur Azure distant. Vous avez deux possibilités.

    -   Vous pouvez fournir des informations d’identification administrateur.

        -   Si vous activez étirement de base de données en exécutant l’Assistant, vous pouvez créer les informations d’identification à ce moment.

        -   Si vous prévoyez d’activer étirement de base de données en exécutant **l’Instruction ALTER DATABASE**, vous devez créer les informations d’identification manuellement avant d’exécuter **ALTER DATABASE** pour activer étirement de base de données.

        Voici un exemple qui crée une nouvelle information d’identification.

        ```tsql
        CREATE DATABASE SCOPED CREDENTIAL <db_scoped_credential_name>
            WITH IDENTITY = '<identity>' , SECRET = '<secret>';
        GO
        ```

        Pour plus d’informations sur les informations d’identification, voir [Créer de base de données inclus dans l’étendue d’informations d’identification (Transact-SQL)](https://msdn.microsoft.com/library/mt270260.aspx). Création de l’information d’identification nécessite des autorisations de modifier les informations d’identification tout.

    -   Vous pouvez utiliser un compte de service fédéré pour SQL Server pour communiquer avec le serveur distant Azure lorsque les conditions suivantes sont vraies.

        -   Le compte de service sous lequel s’exécute l’instance de SQL Server est un compte de domaine.

        -   Le compte de domaine appartient à un domaine dont Active Directory fédérée avec Azure Active Directory.

        -   Le serveur Azure distant est configuré pour prendre en charge l’authentification Azure Active Directory.

        -   Le compte de service sous lequel l’instance de SQL Server est exécuté doit être configuré comme compte dbmanager ou sysadmin sur le serveur distant Azure.

5.  Pour configurer une base de données de base de données étirer, exécutez la commande modifier la base de données.

    1.  Pour l’argument serveur, indiquez le nom du serveur Azure existant, y compris la `.database.windows.net` partie du nom du \- par exemple, `MyStretchDatabaseServer.database.windows.net`.

    2.  Fournir des informations d’identification d’administrateur existant avec l’argument d’informations d’identification, ou spécifier fédéré\_SERVICE\_compte = ON. L’exemple suivant fournit des informations d’identification existante.

    ```tsql
    ALTER DATABASE <database name>
        SET REMOTE_DATA_ARCHIVE = ON
            (
                SERVER = '<server_name>',
                CREDENTIAL = <db_scoped_credential_name>
            ) ;
    GO
    ```

## <a name="next-steps"></a>Étapes suivantes
-   [Activer la base de données étirement pour une table](sql-server-stretch-database-enable-table.md) activer des tables supplémentaires.

-   [Base de données moniteur étirement](sql-server-stretch-database-monitor.md) afficher l’état de migration des données.

-   [Interrompre et reprendre étirement de base de données](sql-server-stretch-database-pause.md)

-   [Gérer et résoudre les problèmes de base de données étirement](sql-server-stretch-database-manage.md)

-   [Bases de données compatibles avec étirement de sauvegarde](sql-server-stretch-database-backup.md)

## <a name="see-also"></a>Voir aussi

[Identifier les bases de données et des tables de base de données étirement](sql-server-stretch-database-identify-databases.md)

[Base de données ALTER définir les Options (Transact-SQL)](https://msdn.microsoft.com/library/bb522682.aspx)

[FirewallRule]: ./media/sql-server-stretch-database-enable-database/firewall.png
