<properties
    pageTitle="Comment effectuer des tâches d’administration, par exemple, réinitialiser votre mot de passe administrateur | Microsoft Azure"
    description="Décrit comment effectuer des tâches administratives dans la base de données SQL. Par exemple, la réinitialisation de mot de passe administrateur, octroi et suppression de l’accès."
    services="sql-database"
    documentationCenter=""
    authors="v-shysun"
    manager="felixwu"
    editor=""
    keywords="Réinitialiser le mot de passe administrateur"/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="v-shysun"/>

# <a name="how-to-perform-common-administrative-tasks-such-as-resetting-admin-password-in-azure-sql-database"></a>Comment effectuer des tâches d’administration courantes telles que la réinitialisation de mot de passe administrateur dans la base de données SQL Azure
Utilisez cette rubrique pour obtenir la procédure rapide pour accorder ou supprimer l’accès à une base de données SQL Azure. Pour plus d’informations, voir :

- [Gestion des bases de données et connexions dans la base de données SQL Azure](sql-database-manage-logins.md)
- [Protéger votre base de données SQL](sql-database-security.md)
- [Centre de sécurité pour le moteur de base de données SQL Server et base de données SQL Azure](https://msdn.microsoft.com/library/bb510589)


[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="to-reset-admin-password-for-a-logical-server"></a>Réinitialiser le mot de passe administrateur pour un serveur logique

- Dans le [Portail Azure](https://portal.azure.com) cliquez sur **SQL Servers**, sélectionnez le serveur dans la liste, puis cliquez sur **Réinitialiser le mot de passe**.

## <a name="to-help-make-sure-only-authorized-ip-addresses-are-allowed-to-access-the-server"></a>Améliorer que seul IP autorisée adresses sont autorisées à accéder au serveur
- Voir [Comment : configurer les paramètres de pare-feu sur base de données SQL](sql-database-configure-firewall-settings.md).

## <a name="to-create-contained-database-users-in-the-user-database"></a>Pour créer des utilisateurs de base de données contenues dans la base de données utilisateur
- Utilisez l’instruction [CREATE USER](https://msdn.microsoft.com/library/ms173463.aspx) et voir [Contenus de base de données utilisateurs - rendre votre base de données Portable](https://msdn.microsoft.com/library/ff929188.aspx).

## <a name="to-authenticate-contained-database-users-by-using-your-azure-active-directory"></a>Pour authentifier les utilisateurs de base de données qu’il contient en utilisant votre Azure Active Directory
- Voir [qui se connecte à la base de données SQL à l’aide de l’authentification Azure Active Directory](sql-database-aad-authentication.md).

## <a name="to-create-additional-logins-for-high-privileged-users-in-the-virtual-master-database"></a>Pour créer des connexions supplémentaires pour les utilisateurs avec privilèges élevés dans la base de données maître virtuelle
- Utilisez l’instruction [CREATE LOGIN](https://msdn.microsoft.com/library/ms189751.aspx) et consultez la section Gestion des connexions de [bases de données de gestion et les connexions de base de données SQL Azure](sql-database-manage-logins.md) pour plus de détails.
