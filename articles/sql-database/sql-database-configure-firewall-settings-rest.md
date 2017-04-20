<properties
    pageTitle="Règles de pare-feu au niveau du serveur de base de données SQL Azure à l’aide de l’API REST | Microsoft Azure"
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


#  <a name="configure-azure-sql-database-server-level-firewall-rules-using-the-rest-api"></a>Configurer les règles de pare-feu au niveau du serveur de base de données SQL Azure à l’aide de l’API REST


> [AZURE.SELECTOR]
- [Vue d’ensemble](sql-database-firewall-configure.md)
- [Portail Azure](sql-database-configure-firewall-settings.md)
- [TSQL](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [API REST](sql-database-configure-firewall-settings-rest.md)


Base de données SQL Microsoft Azure utilise des règles de pare-feu pour autoriser les connexions à vos serveurs et les bases de données. Vous pouvez définir des paramètres du pare-feu au niveau du serveur et au niveau de la base de données pour la forme de base ou une base de données utilisateur de votre serveur de base de données SQL Azure autoriser l’accès à la base de données de façon sélective.

> [AZURE.IMPORTANT] Pour autoriser les applications à partir d’Azure pour vous connecter à votre serveur de base de données, les connexions Azure doivent être activées. Pour plus d’informations sur les règles de pare-feu et l’activation des connexions à partir d’Azure, voir [Pare-feu de base de données SQL Azure](sql-database-firewall-configure.md). Si vous apportez des connexions à l’intérieur de la limite Azure cloud, vous devrez peut-être ouvrir certains ports TCP supplémentaires. Pour plus d’informations, voir la **V12 de base de données de SQL : à l’extérieur de vs à l’intérieur de** section de [Ports au-delà 1433 pour ADO.NET 4.5 et V12 de base de données SQL](sql-database-develop-direct-route-ports-adonet-v12.md)


## <a name="manage-server-level-firewall-rules-through-rest-api"></a>Gérer les règles de pare-feu au niveau du serveur via l’API REST
1. Gestion des règles de pare-feu via l’API REST doit être authentifié. Pour plus d’informations, consultez le [guide du développeur autorisation avec l’API du Gestionnaire de ressources Azure](../resource-manager-api-authentication.md).
2. Règles au niveau du serveur peuvent être créés, mis à jour ou supprimés à l’aide de l’API REST

    Pour créer ou mettre à jour une règle de pare-feu au niveau du serveur, exécutez la méthode place en utilisant les éléments suivants :
 
        https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/firewallRules/{rule-name}?api-version={api-version}
    
    Corps de la requête

        {
         "properties": { 
            "startIpAddress": "{start-ip-address}", 
            "endIpAddress": "{end-ip-address}
            }
        } 
 

    Pour supprimer une règle de pare-feu au niveau du serveur existante, exécutez la méthode de suppression en utilisant les éléments suivants :
     
        https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/firewallRules/{rule-name}?api-version={api-version}


## <a name="manage-firewall-rules-using-the-rest-api"></a>Gérer les règles de pare-feu à l’aide de l’API REST

* [Créer ou mettre à jour de la règle de pare-feu](https://msdn.microsoft.com/library/azure/mt445501.aspx)
* [Supprimer la règle de pare-feu](https://msdn.microsoft.com/library/azure/mt445502.aspx)
* [Obtenir des règles de pare-feu](https://msdn.microsoft.com/library/azure/mt445503.aspx)
* [Liste toutes les règles de pare-feu](https://msdn.microsoft.com/library/azure/mt604478.aspx)
 
## <a name="next-steps"></a>Étapes suivantes

Pour un article sur l’utilisation de procédure Transact-SQL pour créer des règles de pare-feu au niveau du serveur et au niveau de la base de données, voir [règles de pare-feu de base de données au niveau du serveur et de base de données SQL Azure configurer à l’aide de T-SQL](sql-database-configure-firewall-settings-tsql.md). 

Pour faire vers des articles sur la création de règles de pare-feu au niveau du serveur à l’aide d’autres méthodes, voir : 

- [Configurer les règles de pare-feu au niveau du serveur de base de données SQL Azure à l’aide du portail Azure](sql-database-configure-firewall-settings.md)
- [Configurer les règles de pare-feu au niveau du serveur de base de données SQL Azure à l’aide de PowerShell](sql-database-configure-firewall-settings-powershell.md)

Pour un didacticiel sur la création d’une base de données, voir [créer une base de données SQL en minutes à l’aide du portail Azure](sql-database-get-started.md).
Pour vous connecter à une base de données SQL Azure d’ouvrir la source ou des applications tierces, voir [démarrage rapide Client exemples de code pour la base de données SQL](https://msdn.microsoft.com/library/azure/ee336282.aspx).
Pour mieux comprendre comment accéder aux bases de données, voir [Gérer la sécurité access et de la connexion de base de données](https://msdn.microsoft.com/library/azure/ee336235.aspx).


## <a name="additional-resources"></a>Ressources supplémentaires

- [Protéger votre base de données](sql-database-security.md)
- [Centre de sécurité pour le moteur de base de données SQL Server et base de données SQL Azure](https://msdn.microsoft.com/library/bb510589)

<!--Image references-->
[1]: ./media/sql-database-configure-firewall-settings/AzurePortalBrowseForFirewall.png
[2]: ./media/sql-database-configure-firewall-settings/AzurePortalFirewallSettings.png
<!--anchors-->

 
