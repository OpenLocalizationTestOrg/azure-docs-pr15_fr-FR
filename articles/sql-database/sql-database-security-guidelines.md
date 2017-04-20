<properties
   pageTitle="Instructions de sécurité de base de données SQL Azure et Limitations | Microsoft Azure"
   description="Découvrez les instructions de la base de données SQL Microsoft Azure et limitations liées à la sécurité."
   services="sql-database"
   documentationCenter=""
   authors="BYHAM"
   manager="jhubbard"
   editor=""
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="10/18/2016"
   ms.author="rickbyh"/>

# <a name="azure-sql-database-security-guidelines-and-limitations"></a>Limitations et conseils de sécurité de base de données SQL azure

Cette rubrique décrit les instructions de la base de données SQL Microsoft Azure et les limites relatives à la sécurité. Tenez compte des points suivants lors de la gestion de la sécurité de vos bases de données SQL Azure.

## <a name="access-to-the-virtual-master-database"></a>Accès à la base de données maître virtuelle

En règle générale, seuls les administrateurs ont besoin d’accéder à la base de données principale. Accès routine pour chaque base de données utilisateur doit être via les utilisateurs non administrateur de base de données contenues dans chaque base de données. Lorsque vous utilisez les utilisateurs de base de données qu’il contient, vous n’avez pas besoin de créer des connexions dans la base de données principale. Pour plus d’informations, voir [Contenus de base de données utilisateurs - rendre votre base de données Portable](https://msdn.microsoft.com/library/ff929188.aspx).


## <a name="firewall"></a>Pare-feu

Le pare-feu de SQL Server, ce qui a une étendue pour l’ensemble du serveur SQL Azure est généralement configuré via le portail et doit autoriser uniquement les adresses IP utilisées par les administrateurs. Pour créer une règle de pare-feu à portée de base de données qui s’ouvre les adresses IP nécessaire pour chaque base de données, vous connecter à une base de données utilisateur, puis utilisez la [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) instruction Transact-SQL.

Le service de base de données SQL Azure est uniquement disponible via le port TCP 1433. Pour accéder à une base de données SQL à partir de votre ordinateur, vous assurer que le pare-feu de votre ordinateur client permet aux communications sortantes TCP sur le port TCP 1433. Si ne nécessaire pas pour les autres applications, les connexions entrantes sur le port TCP 1433. 

Dans le cadre du processus de connexion, les connexions à partir d’Azure machines virtuelles sont redirigées vers une autre adresse IP et le port, unique pour chaque rôle de travail. Le numéro de port est dans la plage comprise entre 11000 11999. Pour plus d’informations sur les ports TCP, voir [Ports au-delà 1433 pour ADO.NET 4.5 et V12 de base de données SQL](sql-database-develop-direct-route-ports-adonet-v12.md).


## <a name="authentication"></a>Authentification

Utiliser l’authentification Active Directory (sécurité intégrée) dès que possible. Pour plus d’informations sur la configuration de l’authentification Active Directory, voir [connexion à une base de données par à l’aide de Azure Active Directory l’authentification SQL](sql-database-aad-authentication.md)et [en choisissant un Mode d’authentification](https://msdn.microsoft.com/library/ms144284.aspx) dans la documentation en ligne de SQL Server. 

Utiliser les utilisateurs de base de données contenues pour améliorer extensibilité élevées. Pour plus d’informations, voir [Contenus de base de données utilisateurs - rendre votre base de données Portable](https://msdn.microsoft.com/library/ff929188.aspx) [CREATE USER (Transact-SQL)](https://technet.microsoft.com/library/ms173463.aspx)et les [Bases de données de contenu](https://technet.microsoft.com/library/ff929071.aspx).

Le moteur de base de données se ferme connexions resteront inactives pendant plus de 30 minutes. La connexion doit se connecter à nouveau avant de pouvoir être utilisée.

En continu les connexions actives à la base de données SQL nécessitent une nouvelle autorisation (effectuée par le moteur de base de données) au moins toutes les heures 10. Le moteur de base de données essaie de renouvellement d’autorisation à l’aide du mot de passe à l’origine soumis et aucune entrée utilisateur n’est requise. Pour des raisons de performances, lors de la réinitialisation d’un mot de passe de base de données SQL, la connexion n’est pas être authentifiés, même si la connexion est réinitialisée en raison de regroupement de connexions. Ceci est différent du comportement de local SQL Server. Si le mot de passe a été changé depuis la connexion initiale a été autorisée, la connexion doit se terminer et une nouvelle connexion établie à l’aide du nouveau mot de passe. Un utilisateur ayant l’autorisation de supprimer la connexion de base de données peut explicitement mettre fin à une connexion à la base de données SQL à l’aide de la commande [Supprimer](https://msdn.microsoft.com/library/ms173730.aspx) .

## <a name="logins-and-users"></a>Connexions et les utilisateurs

Lors de la gestion des connexions et les utilisateurs dans la base de données SQL, il existe des restrictions.


- Vous devez être connecté à la base de données **principale** lors de l’exécution du ``CREATE/ALTER/DROP DATABASE`` instructions. -L’utilisateur de base de données dans la base de données maître correspondant à la connexion d’accès au niveau du serveur principal ne peut pas être modifié ou supprimé. 
- États-Unis-anglais est la langue par défaut de la connexion d’accès au niveau du serveur principale.
- Seuls les administrateurs (connexion principale au niveau du serveur ou administrateur Azure AD) et les membres du rôle de base de données **dbmanager** dans la base de données **principale** ont l’autorisation d’exécuter le ``CREATE DATABASE`` et ``DROP DATABASE`` instructions.
- Vous devez être connecté à la base de données principale lors de l’exécution du ``CREATE/ALTER/DROP LOGIN`` instructions. Toutefois l’utilisation de connexions est déconseillée. Utilisez les utilisateurs de base de données qu’il contient.
- Pour vous connecter à une base de données utilisateur, vous devez fournir le nom de la base de données dans la chaîne de connexion.
- Seuls le nom de connexion au niveau du serveur principal et les membres du rôle de base de données **loginmanager** dans la base de données **principale** ont l’autorisation d’exécuter le ``CREATE LOGIN``, ``ALTER LOGIN``, et ``DROP LOGIN`` instructions.
- Lorsque vous exécutez le ``CREATE/ALTER/DROP LOGIN`` et ``CREATE/ALTER/DROP DATABASE`` instructions dans une application ADO.NET, à l’aide des commandes paramétrées non autorisé. Pour plus d’informations, voir [commandes et paramètres](https://msdn.microsoft.com/library/ms254953.aspx).
- Lorsque vous exécutez le ``CREATE/ALTER/DROP DATABASE`` et ``CREATE/ALTER/DROP LOGIN`` instructions, chacune de ces instructions doivent être la seule instruction dans un lot Transact-SQL. Sinon, une erreur se produit. Par exemple, Transact-SQL suivant vérifie l’existence de la base de données. S’il existe, un ``DROP DATABASE`` instruction est appelée pour supprimer la base de données. Étant donné que la ``DROP DATABASE`` instruction n’est pas la seule instruction dans le lot, exécutant le code suivant instruction Transact-SQL génère une erreur.

```
IF EXISTS (SELECT [name]
           FROM   [sys].[databases]
           WHERE  [name] = N'database_name')
     DROP DATABASE [database_name];
GO
```

- Lorsque vous exécutez le ``CREATE USER`` instruction avec la ``FOR/FROM LOGIN`` option, elle doit être la seule instruction dans un lot Transact-SQL.
- Lorsque vous exécutez le ``ALTER USER`` instruction avec la ``WITH LOGIN`` option, elle doit être la seule instruction dans un lot Transact-SQL.
- Pour ``CREATE/ALTER/DROP`` un utilisateur nécessite la ``ALTER ANY USER`` autorisation sur la base de données.
- Lorsque le propriétaire d’un rôle de base de données tente d’ajouter ou supprimer un autre utilisateur de base de données vers ou à partir de ce rôle de base de données, l’erreur suivante peut se produire : **utilisateur ou le rôle « Nom » n’existe pas dans cette base de données.** Cette erreur se produit car l’utilisateur n’est pas visible par le propriétaire. Pour résoudre ce problème, accordez le propriétaire du rôle du ``VIEW DEFINITION`` autorisation sur l’utilisateur. 

Pour plus d’informations sur les connexions et les utilisateurs, voir [Gérer les bases de données et les connexions de base de données SQL Azure](sql-database-manage-logins.md).


## <a name="see-also"></a>Voir aussi

[Pare-feu de base de données SQL Azure](sql-database-firewall-configure.md)

[Comment : configurer les paramètres de pare-feu (base de données SQL Azure)](sql-database-configure-firewall-settings.md)

[Gestion des bases de données et connexions dans la base de données SQL Azure](sql-database-manage-logins.md)

[Centre de sécurité pour le moteur de base de données SQL Server et base de données SQL Azure](https://msdn.microsoft.com/library/bb510589)
