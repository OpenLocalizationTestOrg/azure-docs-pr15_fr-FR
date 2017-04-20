<properties
    pageTitle="Configurer une règle de pare-feu au niveau du serveur de base de données SQL | Microsoft Azure"
    description="Découvrez comment configurer le pare-feu pour les adresses IP qui accèdent Azure SQL server."
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
    ms.author="rickbyh;carlrab"/>


# <a name="configure-an-azure-sql-database-server-level-firewall-rule-using-the-azure-portal"></a>Configurer une règle de pare-feu au niveau du serveur de base de données SQL Azure à l’aide du portail Azure


> [AZURE.SELECTOR]
- [Vue d’ensemble](sql-database-firewall-configure.md)
- [Portail Azure](sql-database-configure-firewall-settings.md)
- [TSQL](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [API REST](sql-database-configure-firewall-settings-rest.md)

Azure SQL server utilise des règles de pare-feu pour autoriser les connexions à vos serveurs et les bases de données. Vous pouvez définir des paramètres du pare-feu au niveau du serveur et au niveau de la base de données pour la forme de base ou une base de données utilisateur de votre serveur Azure SQL server logique autoriser l’accès à la base de données de façon sélective. Cette rubrique décrit les règles de pare-feu au niveau du serveur.

> [AZURE.IMPORTANT] Pour autoriser les applications à partir d’Azure pour vous connecter à votre serveur SQL Azure, connexions Azure doivent être activées. Pour mieux comprendre comment le pare-feu règles de travail, voir [Comment faire pour configurer un pare-feu de serveur SQL Azure \- vue d’ensemble](sql-database-firewall-configure.md). Si vous apportez des connexions à l’intérieur de la limite Azure cloud, vous devrez peut-être ouvrir certains ports TCP supplémentaires. Pour plus d’informations, voir la **V12 de base de données de SQL : à l’extérieur de vs à l’intérieur de** section de [Ports au-delà 1433 pour ADO.NET 4.5 et V12 de base de données SQL](sql-database-develop-direct-route-ports-adonet-v12.md)

**Recommandation :** Utiliser des règles de pare-feu au niveau du serveur pour les administrateurs et lorsque vous avez plusieurs bases de données qui ont les mêmes conditions d’accès, et vous ne voulez pas de temps configuration individuellement chaque base de données. Microsoft recommande l’utilisation de règles de pare-feu au niveau de la base de données dès que possible, pour améliorer la sécurité et pour rendre votre base de données plus portable.

[AZURE.INCLUDE [Create SQL Database database](../../includes/sql-database-create-new-server-firewall-portal.md)]

## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>Gérer les règles de pare-feu au niveau du serveur existantes via le portail d’Azure

Répétez les étapes pour gérer les règles de pare-feu au niveau du serveur.

- Pour ajouter l’ordinateur actuel, cliquez sur Ajouter IP du client.
- Pour ajouter des adresses IP supplémentaires, tapez dans le nom de la règle, une adresse IP de début et une adresse IP de fin.
- Pour modifier une règle existante, cliquez sur un des champs de la règle et modifier.
- Pour supprimer une règle existante, placez le curseur sur la règle jusqu'à ce que le X apparaît à la fin de la ligne. Cliquez sur X pour supprimer la règle.

Cliquez sur **Enregistrer** pour enregistrer les modifications.

## <a name="next-steps"></a>Étapes suivantes

Pour un article sur l’utilisation de procédure Transact-SQL pour créer des règles de pare-feu au niveau du serveur et au niveau de la base de données, voir [règles de pare-feu de base de données au niveau du serveur et de base de données SQL Azure configurer à l’aide de T-SQL](sql-database-configure-firewall-settings-tsql.md). 

Pour faire vers des articles sur la création de règles de pare-feu au niveau du serveur à l’aide d’autres méthodes, voir : 

- [Configurer les règles de pare-feu au niveau du serveur de base de données SQL Azure à l’aide de PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [Configurer les règles de pare-feu au niveau du serveur de base de données SQL Azure à l’aide de l’API REST](sql-database-configure-firewall-settings-rest.md)

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

 
