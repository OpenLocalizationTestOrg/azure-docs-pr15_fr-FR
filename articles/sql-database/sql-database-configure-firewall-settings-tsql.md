<properties
    pageTitle="Règles de pare-feu au niveau du serveur et au niveau de la base de données de base de données SQL Azure à l’aide de T-SQL | Microsoft Azure"
    description="Découvrez comment configurer le pare-feu pour les adresses IP qui accèdent aux bases de données SQL Azure."
    services="sql-database"
    documentationCenter=""
    authors="BYHAM"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article" 
    ms.date="08/30/2016"
    ms.author="rickbyh"/>


# <a name="configure-azure-sql-database-server-level-and-database-level-firewall-rules-using-t-sql"></a>Configurer les règles de pare-feu au niveau du serveur et au niveau de la base de données de base de données SQL Azure à l’aide de T-SQL


> [AZURE.SELECTOR]
- [Vue d’ensemble](sql-database-firewall-configure.md)
- [Portail Azure](sql-database-configure-firewall-settings.md)
- [TSQL](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [API REST](sql-database-configure-firewall-settings-rest.md)


Base de données SQL Microsoft Azure utilise des règles de pare-feu pour autoriser les connexions à vos serveurs et les bases de données. Vous pouvez définir des paramètres du pare-feu au niveau du serveur et au niveau de la base de données pour la forme de base ou une base de données utilisateur de votre serveur de base de données SQL Azure autoriser l’accès à la base de données de façon sélective.

> [AZURE.IMPORTANT] Pour autoriser les applications à partir d’Azure pour vous connecter à votre serveur de base de données, les connexions Azure doivent être activées. Pour plus d’informations sur les règles de pare-feu et l’activation des connexions à partir d’Azure, voir [Pare-feu de base de données SQL Azure](sql-database-firewall-configure.md). Si vous apportez des connexions à l’intérieur de la limite Azure cloud, vous devrez peut-être ouvrir certains ports TCP supplémentaires. Pour plus d’informations, voir la **V12 de base de données de SQL : à l’extérieur de vs à l’intérieur de** section de [Ports au-delà 1433 pour ADO.NET 4.5 et V12 de base de données SQL](sql-database-develop-direct-route-ports-adonet-v12.md)


## <a name="server-level-firewall-rules"></a>Règles de pare-feu au niveau du serveur

Uniquement la connexion principale au niveau du serveur ou l’administrateur Azure Active Directory peut créer une règle de pare-feu au niveau du serveur à l’aide de Transact-SQL.

1. Démarrez une fenêtre de requête, puis se connecter à la base de données maître virtuel à l’aide de SQL Server Management Studio.
2. Règles de pare-feu au niveau du serveur peuvent être sélectionnés, créés, mis à jour ou supprimés à partir de la fenêtre de requête.
3. Pour créer ou mettre à jour les règles de pare-feu au niveau du serveur, exécutez la `sp_set_firewall_rule` procédure stockée. L’exemple suivant active une plage d’adresses IP sur le serveur Contoso.<br/>Commencez par s’affichent les règles existent déjà.

        SELECT * FROM sys.firewall_rules ORDER BY name;

    Ensuite, ajoutez une règle de pare-feu.

        EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
            @start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'

    Pour supprimer une règle de pare-feu au niveau du serveur, exécutez la procédure sp_delete_firewall_rule stockées. L’exemple suivant supprime la règle nommée ContosoFirewallRule.
 
        EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
 
 Pour plus d’informations sur ces procédures stockées, voir [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx) et [sp_delete_firewall_rule](https://msdn.microsoft.com/library/dn270024.aspx).

## <a name="database-level-firewall-rules"></a>Règles de pare-feu au niveau de la base de données

Seul un utilisateur de base de données avec l’autorisation de **contrôle** sur la base de données (par exemple, le propriétaire de la base de données) peut créer une règle de pare-feu au niveau de la base de données.

1. Après avoir créé un pare-feu au niveau du serveur pour votre adresse IP, lancez une fenêtre de requête par le biais du portail classique ou SQL Server Management Studio.
2. Se connecter à la base de données pour laquelle vous voulez créer une règle de pare-feu au niveau de la base de données.

    Pour créer un nouveau ou mettre à jour une règle de pare-feu au niveau de la base de données existante, exécutez la `sp_set_database_firewall_rule` procédure stockée. L’exemple suivant crée une nouvelle règle de pare-feu nommée ContosoFirewallRule.
 
        EXEC sp_set_database_firewall_rule @name = N'ContosoFirewallRule', 
            @start_ip_address = '192.168.1.11', @end_ip_address = '192.168.1.11'
 
    Pour supprimer une règle de pare-feu au niveau de la base de données existante, exécutez la `sp_delete_database_firewall_rule` procédure stockée. L’exemple suivant supprime la règle nommée ContosoFirewallRule.
`
   EXÉCUTION sp_delete_database_firewall_rule @name = N'ContosoFirewallRule'

Pour plus d’informations sur ces procédures stockées, voir [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) et [sp_delete_database_firewall_rule](https://msdn.microsoft.com/library/dn270030.aspx).

## <a name="next-steps"></a>Étapes suivantes

Pour faire vers des articles sur la création de règles de pare-feu au niveau du serveur à l’aide d’autres méthodes, voir : 

- [Configurer les règles de pare-feu au niveau du serveur de base de données SQL Azure à l’aide du portail Azure](sql-database-configure-firewall-settings.md)
- [Configurer les règles de pare-feu au niveau du serveur de base de données SQL Azure à l’aide de PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [Configurer les règles de pare-feu au niveau du serveur de base de données SQL Azure à l’aide de l’API REST](sql-database-configure-firewall-settings-rest.md)

Pour un didacticiel sur la création d’une base de données, voir [créer une base de données SQL en minutes à l’aide du portail Azure](sql-database-get-started.md).
Pour vous connecter à une base de données SQL Azure d’ouvrir la source ou des applications tierces, voir [démarrage rapide Client exemples de code pour la base de données SQL](https://msdn.microsoft.com/library/azure/ee336282.aspx).
Pour mieux comprendre comment accéder aux bases de données, voir [Gérer la sécurité access et de la connexion de base de données](https://msdn.microsoft.com/library/azure/ee336235.aspx).


## <a name="additional-resources"></a>Ressources supplémentaires

- [Protéger votre base de données](sql-database-security.md)
- [Centre de sécurité pour le moteur de base de données SQL Server et base de données SQL Azure](https://msdn.microsoft.com/library/bb510589)
