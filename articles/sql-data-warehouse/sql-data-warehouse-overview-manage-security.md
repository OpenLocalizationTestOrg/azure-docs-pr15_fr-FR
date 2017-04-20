<properties
   pageTitle="Sécuriser une base de données SQL Data Warehouse | Microsoft Azure"
   description="Conseils pour sécuriser une base de données SQL Azure Data Warehouse pour développer des solutions."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="ronortloff"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/24/2016"
   ms.author="rortloff;barbkess;sonyama"/>

# <a name="secure-a-database-in-sql-data-warehouse"></a>Sécuriser une base de données SQL Data Warehouse

> [AZURE.SELECTOR]
- [Présentation de la sécurité](sql-data-warehouse-overview-manage-security.md)
- [Authentification](sql-data-warehouse-authentication.md)
- [Chiffrement (portail)](sql-data-warehouse-encryption-tde.md)
- [Chiffrement (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)

Cet article décrit les notions de base de la sécurisation de votre base de données SQL Azure Data Warehouse. En particulier, cet article vous aideront à démarrer avec des ressources pour limiter l’accès, la protection des données et contrôler les activités dans une base de données.

## <a name="connection-security"></a>Sécurité de connexion

Sécurité de connexion fait référence à la restreindre et connexion sécurisée à votre base de données à l’aide de règles de pare-feu et de chiffrement de la connexion.

Règles de pare-feu sont utilisées par le serveur et la base de données pour rejeter les tentatives de connexion à partir des adresses IP qui n’ont pas été explicitement autorisés. Pour autoriser les connexions à partir de votre application ou l’adresse IP public d’ordinateur client, vous devez d’abord créer une règle de pare-feu au niveau du serveur à l’aide du portail Azure, API REST ou PowerShell. Pour obtenir les meilleurs résultats, vous devez restreindre les plages d’adresses IP autorisées via le pare-feu de votre serveur autant que possibles.  Pour accéder à SQL Azure Data Warehouse à partir de votre ordinateur local, vérifiez que le pare-feu sur votre réseau et de l’ordinateur local autorise les communications sortantes sur le port TCP 1433.  Pour plus d’informations, voir [pare-feu de base de données SQL Azure][], [sp_set_firewall_rule][]et [sp_set_database_firewall_rule][].

Connexions à votre Data Warehouse SQL sont chiffrées par défaut.  Modification des paramètres de connexion pour désactiver le chiffrement sont ignorées.

## <a name="authentication"></a>Authentification

Authentification fait référence à la manière dont vous prouver votre identité lorsque vous vous connectez à la base de données. SQL Data Warehouse prend actuellement en charge l’authentification SQL Server avec un nom d’utilisateur et mot de passe, ainsi qu’un Azure Active Directory. 

Lorsque vous avez créé le serveur logique pour votre base de données, vous avez spécifié une connexion « serveur d’administration » avec un nom d’utilisateur et mot de passe. À l’aide de ces informations d’identification, vous pouvez vous authentifier à une base de données sur ce serveur en tant que le propriétaire de base de données, ou « dbo » via l’authentification SQL Server.

Toutefois, comme il est conseillé aux utilisateurs de votre organisation doivent utiliser un autre compte s’authentifier. Ainsi, vous pouvez limiter les autorisations accordées à l’application et réduire les risques d’activité malveillante au cas où votre code de l’application est objet d’une attaque par injection SQL. 

Pour créer un utilisateur authentifié de SQL Server, vous connecter à la base de données **principale** sur votre serveur avec votre nom d’utilisateur d’administration serveur et créez une nouvelle connexion de serveur.  Par ailleurs, il est conseillé de créer un utilisateur dans la base de données principale pour les utilisateurs de magasin de données SQL Azure. Création d’un utilisateur principale permet à un utilisateur pour se connecter à l’aide d’outils tels que SSMS sans spécifier un nom de base de données.  Il leur permet également d’utiliser l’Explorateur d’objets pour afficher toutes les bases de données sur un serveur SQL server.

```sql
-- Connect to master database and create a login
CREATE LOGIN ApplicationLogin WITH PASSWORD = 'Str0ng_password';
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Ensuite, vous connecter à votre **base de données SQL Data Warehouse** avec votre nom d’utilisateur d’administration serveur et créez un utilisateur de base de données en fonction de la connexion de serveur que vous venez de créer.

```sql
-- Connect to SQL DW database and create a database user
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Si un utilisateur allez effectuer des opérations supplémentaires comme la création de connexions ou de création de nouvelles bases de données, elles devront également être affectées à la `Loginmanager` et `dbmanager` rôles dans la base de données principale. Pour plus d’informations sur ces rôles supplémentaires et d’authentification à une base de données SQL, voir [Gérer les bases de données et connexions dans la base de données SQL Azure][].  Pour plus d’informations sur Azure AD pour Data Warehouse SQL, consultez l’article [connexion à données entrepôt par à l’aide de Azure Active Directory l’authentification SQL][].


## <a name="authorization"></a>Autorisation

Autorisation fait référence à ce que vous pouvez faire au sein d’une base de données SQL Azure Data Warehouse, et cette opération est contrôlée par l’appartenance aux rôles de votre compte d’utilisateur et les autorisations. Pour obtenir les meilleurs résultats, vous devez accorder aux utilisateurs les privilèges minimum nécessaires. Data Warehouse SQL Azure c’est facile de gérer les rôles dans T-SQL :

```sql
EXEC sp_addrolemember 'db_datareader', 'ApplicationUser'; -- allows ApplicationUser to read data
EXEC sp_addrolemember 'db_datawriter', 'ApplicationUser'; -- allows ApplicationUser to write data
```

Le compte d’administrateur de serveur avec que vous vous connectez est membre db_owner, c'est-à-dire autorité faire tout ce que contient la base de données. Enregistrer ce compte pour le déploiement des mises à niveau de schéma et autres opérations de gestion. Utiliser le compte « ApplicationUser » avec des autorisations plus limitées pour vous connecter à partir de votre application de base de données avec les privilèges minimum requis par votre application.

Manières pour limiter qu’un utilisateur peut faire avec la base de données SQL Azure :

- Des [autorisations][] granulaires vous permettent de contrôler les opérations que vous pouvez effectuer dans des colonnes, tables, vues, procédures et d’autres objets dans la base de données. Utiliser des autorisations précises pour que le contrôle de la plupart des et accorder des autorisations nécessaires minimum. Le système d’autorisation granulaires est un peu compliqué et requiert l’étude de quelques pour utiliser efficacement.
- [Rôles de base de données][] différent db_datareader et db_datawriter peuvent être utilisés pour créer des comptes d’utilisateurs plus puissantes application ou moins puissants gestion des comptes. Les rôles de base de données fixe intégrée offrent un moyen simple pour accorder des autorisations, mais peuvent entraîner l’octroi d’autorisations plus que nécessaire.
- [Les procédures stockées][] peut être utilisé pour limiter les actions qui peuvent être prises sur la base de données.

Gestion des serveurs logiques et des bases de données à partir du portail classique Azure ou à l’aide de l’API du Gestionnaire de ressources Azure est contrôlée par attributions de rôle de votre compte d’utilisateur du portail. Pour plus d’informations sur ce sujet, consultez [contrôle d’accès basé sur un rôle dans portail Azure][].

## <a name="encryption"></a>Chiffrement

Azure SQL données Warehouse transparente données chiffrement (TDE) permet de protéger contre la menace d’activité malveillante en effectuant en temps réel chiffrement et déchiffrement de vos données au repos.  Lorsque vous chiffrez votre base de données, sauvegardes associés et les fichiers journaux des transactions sont chiffrés sans avoir besoin de toute modification apportée à vos applications. TDE chiffre le stockage d’une base de données entière en utilisant une clé symétrique appelée la clé de chiffrement de base de données. Dans la base de données SQL la clé de chiffrement de base de données est protégée par un certificat de serveur intégrés. Le certificat de serveur intégré est unique pour chaque serveur de base de données SQL. Microsoft fait automatiquement pivote ces certificats au moins tous les 90 jours. L’algorithme de chiffrement utilisé par SQL Data Warehouse est AES-256. Pour une description générale du TDE, voir [Chiffrement Transparent des données][].

Vous pouvez chiffrer votre base de données à l’aide du [Portail Azure] [ Encryption with Portal] ou [T-SQL][Encryption with TSQL].

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations et des exemples sur la connexion à votre SQL Data Warehouse avec différents protocoles, voir [se connecter à Data Warehouse SQL][].

<!--Image references-->

<!--Article references-->
[Se connecter à Data Warehouse SQL]: ./sql-data-warehouse-connect-overview.md
[Encryption with Portal]: ./sql-data-warehouse-encryption-tde.md
[Encryption with TSQL]: ./sql-data-warehouse-encryption-tde-tsql.md
[Connexion à Data Warehouse SQL en utilisant l’authentification Azure Active Directory]: ./sql-data-warehouse-authentication.md

<!--MSDN references-->
[Pare-feu de base de données SQL Azure]: https://msdn.microsoft.com/library/ee621782.aspx
[sp_set_firewall_rule]: https://msdn.microsoft.com/library/dn270017.aspx
[sp_set_database_firewall_rule]: https://msdn.microsoft.com/library/dn270010.aspx
[Rôles de base de données]: https://msdn.microsoft.com/library/ms189121.aspx
[Gestion des bases de données et connexions dans la base de données SQL Azure]: https://msdn.microsoft.com/library/ee336235.aspx
[Autorisations]: https://msdn.microsoft.com/library/ms191291.aspx
[Procédures stockées]: https://msdn.microsoft.com/library/ms190782.aspx
[Chiffrement de données transparent]: https://msdn.microsoft.com/library/bb934049.aspx
[Azure portal]: https://portal.azure.com/

<!--Other Web references-->
[Contrôle d’accès basé sur un rôle dans portail Azure]: https://azure.microsoft.com/documentation/articles/role-based-access-control-configure
