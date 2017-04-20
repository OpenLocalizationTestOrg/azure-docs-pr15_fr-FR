<properties
    pageTitle="Configurer des règles de pare-feu au niveau du serveur de base de données SQL Azure à l’aide de PowerShell | Microsoft Azure"
    description="Découvrez comment configurer le pare-feu pour les adresses IP qui accèdent aux bases de données SQL Azure."
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="08/09/2016"
    ms.author="sstein"/>


# <a name="configure-azure-sql-database-server-level-firewall-rules-by-using-powershell"></a>Configurer des règles de pare-feu au niveau du serveur de base de données SQL Azure à l’aide de PowerShell


> [AZURE.SELECTOR]
- [Vue d’ensemble](sql-database-firewall-configure.md)
- [Portail Azure](sql-database-configure-firewall-settings.md)
- [TSQL](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [API REST](sql-database-configure-firewall-settings-rest.md)


Base de données SQL Azure utilise des règles de pare-feu pour autoriser les connexions à vos serveurs et les bases de données. Vous pouvez définir des paramètres du pare-feu au niveau du serveur et au niveau de la base de données pour la base de données principale ou une base de données utilisateur de votre serveur de base de données SQL pour autoriser l’accès à la base de données de façon sélective.

> [AZURE.IMPORTANT] Pour autoriser les applications à partir d’Azure pour vous connecter à votre serveur de base de données, les connexions Azure doivent être activées. Pour plus d’informations sur les règles de pare-feu et l’activation des connexions à partir d’Azure, voir [Pare-feu de base de données SQL Azure](sql-database-firewall-configure.md). Si vous apportez des connexions à l’intérieur de la limite Azure cloud, vous devrez peut-être ouvrir certains ports TCP supplémentaires. Pour plus d’informations, voir la « V12 de base de données SQL : à l’extérieur de vs à l’intérieur » section de [Ports au-delà 1433 pour ADO.NET 4.5 et V12 de base de données SQL](sql-database-develop-direct-route-ports-adonet-v12.md).


[AZURE.INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="create-server-firewall-rules"></a>Créer des règles de pare-feu de serveur

Pare-feu au niveau du serveur règles peuvent être créées, mise à jour et supprimés à l’aide de PowerShell Azure.

Pour créer une nouvelle règle de pare-feu au niveau du serveur, exécutez la [New-AzureRmSqlServerFirewallRule] (applet de commande https://msdn.microsoft.com/library/azure/mt603860(v=azure.300\).aspx). L’exemple suivant active une plage d’adresses IP sur le serveur Contoso.

    New-AzureRmSqlServerFirewallRule -ResourceGroupName 'resourcegroup1' -ServerName 'Contoso' -FirewallRuleName "ContosoFirewallRule" -StartIpAddress '192.168.1.1' -EndIpAddress '192.168.1.10'       

Pour modifier une règle de pare-feu au niveau du serveur existante, exécutez la [Set-AzureRmSqlServerFirewallRule] (applet de commande https://msdn.microsoft.com/library/azure/mt603789(v=azure.300\).aspx). L’exemple suivant modifie la plage d’adresses IP acceptables pour la règle nommé ContosoFirewallRule.

    Set-AzureRmSqlServerFirewallRule -ResourceGroupName 'resourcegroup1' –StartIPAddress 192.168.1.4 –EndIPAddress 192.168.1.10 –RuleName 'ContosoFirewallRule' –ServerName 'Contoso'

Pour supprimer une règle de pare-feu au niveau du serveur existante, exécutez la [Supprimer-AzureRmSqlServerFirewallRule] (applet de commande https://msdn.microsoft.com/library/azure/mt603588(v=azure.300\).aspx). L’exemple suivant supprime la règle nommée ContosoFirewallRule.

    Remove-AzureRmSqlServerFirewallRule –RuleName 'ContosoFirewallRule' –ServerName 'Contoso'


## <a name="manage-firewall-rules-by-using-powershell"></a>Gérer les règles de pare-feu à l’aide de PowerShell

Vous pouvez également utiliser PowerShell pour gérer les règles de pare-feu. Pour plus d’informations, voir les rubriques suivantes :

* [Nouvelle AzureRmSqlServerFirewallRule] (https://msdn.microsoft.com/library/azure/mt603860(v=azure.300\).aspx)
* [Supprimer AzureRmSqlServerFirewallRule] (https://msdn.microsoft.com/library/azure/mt603588(v=azure.300\).aspx)
* [Set-AzureRmSqlServerFirewallRule] (https://msdn.microsoft.com/library/azure/mt603789(v=azure.300\).aspx)
* [Get-AzureRmSqlServerFirewallRule] (https://msdn.microsoft.com/library/azure/mt603586(v=azure.300\).aspx)


## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’utilisation Transact-SQL pour créer des règles de pare-feu au niveau du serveur et au niveau de la base de données, voir [règles de pare-feu de base de données au niveau du serveur et de base de données SQL Azure configurer à l’aide de T-SQL](sql-database-configure-firewall-settings-tsql.md).

Pour plus d’informations sur la façon de créer des règles de pare-feu au niveau du serveur à l’aide d’autres méthodes, voir :

- [Configurer les règles de pare-feu au niveau du serveur de base de données SQL Azure à l’aide du portail Azure](sql-database-configure-firewall-settings.md)
- [Configurer les règles de pare-feu au niveau du serveur de base de données SQL Azure à l’aide de l’API REST](sql-database-configure-firewall-settings-rest.md)

Pour un didacticiel sur la création d’une base de données, voir [créer une base de données SQL en minutes à l’aide du portail Azure](sql-database-get-started.md).
Pour se connecter à une base de données SQL Azure d’ouvrir la source ou des applications tierces, voir [démarrage rapide Client exemples de code pour la base de données SQL](https://msdn.microsoft.com/library/azure/ee336282.aspx).
Pour mieux comprendre comment accéder aux bases de données, voir [Gérer la sécurité access et de la connexion de base de données](https://msdn.microsoft.com/library/azure/ee336235.aspx).


## <a name="additional-resources"></a>Ressources supplémentaires

- [Protéger votre base de données](sql-database-security.md)
- [Centre de sécurité pour le moteur de base de données SQL Server et base de données SQL Azure](https://msdn.microsoft.com/library/bb510589)


<!--Image references-->
[1]: ./media/sql-database-configure-firewall-settings/AzurePortalBrowseForFirewall.png
[2]: ./media/sql-database-configure-firewall-settings/AzurePortalFirewallSettings.png
<!--anchors-->
