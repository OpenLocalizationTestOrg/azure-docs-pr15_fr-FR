<properties
    pageTitle="Didacticiel de base de données SQL : prise en main sécurité"
    description="Apprenez à créer des comptes d’utilisateur pour accéder et gérer une base de données."
    keywords=""
    services="sql-database"
    documentationCenter=""
    authors="CarlRabeler"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="08/17/2016"
    ms.author="carlrab"/>

# <a name="sql-database-tutorial-create-sql-database-user-accounts-to-access-and-manage-a-database"></a>Didacticiel de base de données SQL : créer des comptes d’utilisateurs à accéder et gérer une base de données de base de données SQL


> [AZURE.SELECTOR]
- [Didacticiel mise en route Get](sql-database-get-started-security.md)
- [Accorder l’accès](sql-database-manage-logins.md)

Dans ce didacticiel, vous découvrez comment utiliser SQL Server Management Studio (SSMS) pour :

- Connectez-vous à la base de données SQL à l’aide d’une connexion d’accès au niveau du serveur principale.
- Créer un compte d’utilisateur de base de données SQL.
- Accorder à un utilisateur de base de données SQL [autorisations db_owner](https://msdn.microsoft.com/library/ms189121.aspx#Anchor_0).
- Se connecter à une base de données SQL avec un compte d’utilisateur qui n’est pas un utilisateur au niveau du serveur principal.

[AZURE.INCLUDE [Login](../../includes/azure-getting-started-portal-login.md)]


[AZURE.INCLUDE [Create SQL Database logical server](../../includes/sql-database-sql-server-management-studio-connect-server-principal.md)]


[AZURE.INCLUDE [Create SQL Database database](../../includes/sql-database-create-new-database-user.md)]


[AZURE.INCLUDE [Create SQL Database database](../../includes/sql-database-grant-database-user-dbo-permissions.md)]


[AZURE.INCLUDE [Create SQL Database database](../../includes/sql-database-sql-server-management-studio-connect-user.md)]


## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez terminé ce didacticiel de base de données SQL et créé un compte d’utilisateur et accordé des autorisations de l’utilisateur compte dbo, vous êtes prêt à en savoir plus sur la [sécurité de base de données SQL](sql-database-manage-logins.md).


