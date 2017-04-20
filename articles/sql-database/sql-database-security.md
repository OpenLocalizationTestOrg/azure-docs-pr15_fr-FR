<properties
   pageTitle="Présentation de la sécurité de base de données SQL"
   description="En savoir plus sur la sécurité de base de données SQL Azure et SQL Server, notamment les différences entre le cloud et SQL Server en local lorsqu’il s’agit de l’authentification, l’autorisation, la sécurité de connexion, le chiffrement et la conformité."
   services="sql-database"
   documentationCenter=""
   authors="tmullaney"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="06/09/2016"
   ms.author="thmullan;jackr"/>


# <a name="securing-your-sql-database"></a>Protéger votre base de données SQL

## <a name="overview"></a>Vue d’ensemble

Cet article décrit les notions de base de la sécurisation de la couche de données d’une application à l’aide de la base de données SQL Azure. En particulier, cet article vous aideront à main de ressources pour limiter l’accès, la protection des données, et contrôler les activités dans une base de données créée dans la [prise en main didacticiel de base de données SQL](sql-database-get-started.md). Pour une vue d’ensemble complet de fonctionnalités de sécurité disponibles sur toutes les versions de SQL, consultez le [Centre de sécurité pour le moteur de base de données SQL Server et base de données SQL Azure](https://msdn.microsoft.com/library/bb510589). Informations supplémentaires sont également disponibles dans le [livre blanc technique de sécurité et base de données SQL Azure](https://download.microsoft.com/download/A/C/3/AC305059-2B3F-4B08-9952-34CDCA8115A9/Security_and_Azure_SQL_Database_White_paper.pdf) (PDF).

## <a name="connection-security"></a>Sécurité de connexion

Sécurité de connexion fait référence à la restreindre et connexion sécurisée à votre base de données à l’aide de règles de pare-feu et de chiffrement de la connexion.

Règles de pare-feu sont utilisées par le serveur et la base de données pour rejeter les tentatives de connexion à partir des adresses IP qui n’ont pas été explicitement autorisés. Pour permettre à votre application ou l’adresse IP public d’ordinateur client tente de se connecter à une base de données, vous devez d’abord créer une règle de pare-feu au niveau du serveur à l’aide du portail classique Azure, API REST ou PowerShell. Pour obtenir les meilleurs résultats, vous devez restreindre les plages d’adresses IP autorisées via le pare-feu de votre serveur autant que possibles. Pour plus d’informations, voir [Pare-feu de base de données SQL Azure](https://msdn.microsoft.com/library/ee621782).

Toutes les connexions à la base de données SQL Azure nécessitent chiffrement (SSL/TLS) à tout moment lors données « transfert » vers et depuis la base de données. Dans la chaîne de connexion de votre application, vous devez spécifier des paramètres pour chiffrer la connexion et *pas* faire confiance au certificat de serveur (Cela vous si vous copiez la chaîne de connexion se déconnecter le portail classique Azure), sinon la connexion ne vérifie pas l’identité du serveur et sera sensible aux attaques « homme dans le milieu ». Pour le pilote ADO.NET, par exemple, ces paramètres de chaîne de connexion sont **chiffrer = True** et **TrustServerCertificate = False**. Pour plus d’informations, voir [Azure chiffrement de la connexion de base de données SQL et la Validation de certificat](https://msdn.microsoft.com/library/azure/ff394108#encryption).


## <a name="authentication"></a>Authentification

Authentification fait référence à la manière dont vous prouver votre identité lorsque vous vous connectez à la base de données. Base de données SQL prend en charge les deux types d’authentification :

 - **Authentification SQL**, qui utilise un nom d’utilisateur et mot de passe
 - **Authentification azure Active Directory**, qui utilise des identités gérées par Azure Active Directory et est pris en charge pour les domaines gérées et intégrées

Lorsque vous avez créé le serveur logique pour votre base de données, vous avez spécifié une connexion « serveur d’administration » avec un nom d’utilisateur et mot de passe. À l’aide de ces informations d’identification, vous pouvez authentifier à une base de données sur ce serveur en tant que le propriétaire de base de données, ou « dbo. » Si vous souhaitez utiliser l’authentification Azure Active Directory, vous devez créer un autre serveur d’administration appelée « administrateur Azure AD, « qui est autorisé à gérer les groupes et les utilisateurs Azure AD. Cet administrateur peut également effectuer toutes les opérations pouvant être un administrateur de serveur ordinaire. Consultez l’article [connexion à la base de données par à l’aide de Azure Active Directory l’authentification SQL](sql-database-aad-authentication.md) pour une procédure pas à pas de la création d’un administrateur Azure AD pour activer l’authentification Azure Active Directory.

Meilleurs résultats votre application doit utiliser un autre compte s’authentifier--ainsi, vous pouvez limiter les autorisations accordées à l’application et réduire les risques liés à l’activité malveillante au cas où votre code de l’application est objet d’une attaque par injection SQL. L’approche recommandée consiste à créer un [utilisateur de base de données de contenu](https://msdn.microsoft.com/library/ff929188), qui permet à votre application de s’authentifier directement à une seule base de données. Vous pouvez créer un utilisateur de base de données qu’il contient qui utilise l’authentification SQL en exécutant la commande T-SQL suivante lorsque vous êtes connecté à votre base de données utilisateur en tant que la connexion d’administration server :

```
CREATE USER ApplicationUser WITH PASSWORD = 'strong_password'; -- SQL Authentication
```

Si vous avez créé un administrateur Azure AD, vous pouvez créer un utilisateur de base de données qu’il contient qui utilise l’authentification Azure Active Directory en exécutant la commande T-SQL suivante lorsque vous êtes connecté à votre base de données utilisateur en tant que l’administrateur Azure AD :

```
CREATE USER [Azure_AD_principal_name | Azure_AD_group_display_name] FROM EXTERNAL PROVIDER; -- Azure Active Directory Authentication
```

Dans les deux cas, chaîne de connexion de votre application doit spécifier ces informations d’identification utilisateur, plutôt que la connexion d’administration server, pour vous connecter à la base de données.

Pour plus d’informations sur l’authentification à une base de données SQL, voir [Gérer les bases de données et les connexions de base de données SQL Azure](sql-database-manage-logins.md).


## <a name="authorization"></a>Autorisation
Autorisation fait référence à ce que vous pouvez faire au sein d’une base de données SQL Azure, et cette opération est contrôlée par l’appartenance aux rôles de votre compte d’utilisateur et les autorisations. Pour obtenir les meilleurs résultats, vous devez accorder aux utilisateurs les privilèges minimum nécessaires. Base de données SQL Azure c’est facile de gérer les rôles dans T-SQL :

```
ALTER ROLE db_datareader ADD MEMBER ApplicationUser; -- allows ApplicationUser to read data
ALTER ROLE db_datawriter ADD MEMBER ApplicationUser; -- allows ApplicationUser to write data
```

Le compte d’administrateur de serveur avec que vous vous connectez est membre db_owner, c'est-à-dire autorité faire tout ce que contient la base de données. Enregistrer ce compte pour le déploiement des mises à niveau de schéma et autres opérations de gestion. Utiliser le compte « ApplicationUser » avec des autorisations plus limitées pour vous connecter à partir de votre application de base de données avec les privilèges minimum requis par votre application.

Manières pour limiter qu’un utilisateur peut faire avec la base de données SQL Azure :

* [Rôles de base de données](https://msdn.microsoft.com/library/ms189121) différent db_datareader et db_datawriter peuvent être utilisés pour créer des comptes d’utilisateurs plus puissantes application ou moins puissants gestion des comptes.
* Des [autorisations](https://msdn.microsoft.com/library/ms191291) granulaires vous permettent de contrôler les opérations que vous pouvez effectuer dans des colonnes, tables, vues, procédures et d’autres objets dans la base de données.
* [L’emprunt d’identité](https://msdn.microsoft.com/library/vstudio/bb669087) et la [signature de module](https://msdn.microsoft.com/library/bb669102) peuvent servir en toute sécurité élever temporairement les autorisations.
* [Sécurité au niveau de la ligne](https://msdn.microsoft.com/library/dn765131) peut être utilisé limite que les lignes d’un utilisateur peut accéder.
* [Masquage des données](sql-database-dynamic-data-masking-get-started.md) peut servir à limiter l’exposition des données sensibles.
* [Les procédures stockées](https://msdn.microsoft.com/library/ms190782) peut être utilisé pour limiter les actions qui peuvent être prises sur la base de données.

Gestion des serveurs logiques et des bases de données à partir du portail classique Azure ou à l’aide de l’API du Gestionnaire de ressources Azure est contrôlée par attributions de rôle de votre compte d’utilisateur du portail. Pour plus d’informations sur ce sujet, consultez [contrôle d’accès basé sur un rôle dans portail Azure](../active-directory./role-based-access-control-configure.md).


## <a name="encryption"></a>Chiffrement

Base de données SQL Azure peut contribuer à protéger vos données en chiffrant vos données lorsqu’elle est « au repos » ou stockés dans des sauvegardes, à l’aide de [Chiffrement Transparent des données](http://go.microsoft.com/fwlink/?LinkId=526242)et fichiers de base de données. Pour chiffrer votre base de données, connectez-vous en tant que propriétaire de base de données, puis exécutez :

```
ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

Pour les autres méthodes chiffrer votre secrets de données, vous pouvez :

* [Chiffrement de niveau cellule](https://msdn.microsoft.com/library/ms179331.aspx) pour chiffrer des colonnes spécifiques ou même les cellules de données avec des clés de chiffrement différentes.
* Si vous avez besoin d’un Module de sécurité matériel ou de la gestion centralisée de votre hiérarchie de clé de chiffrement, envisagez d’utiliser [L’archivage sécurisé de clé Azure avec SQL Server dans une machine virtuelle Azure](http://blogs.technet.com/b/kv/archive/2015/01/12/using-the-key-vault-for-sql-server-encryption.aspx).
* [Toujours chiffrées](https://msdn.microsoft.com/library/mt163865.aspx) (en mode Aperçu avant) rend le chiffrement transparent pour les applications et permet aux clients chiffrer des données sensibles à l’intérieur des applications clientes sans partager les clés de chiffrement avec la base de données SQL.

## <a name="auditing"></a>L’audit

Audit et suivi des événements de base de données peuvent vous aider à assurer la conformité réglementaire et d’identifier l’activité suspecte. L’audit de base de données SQL permet de que vous permettent de consigner les événements dans votre base de données à un audit connectez à votre compte de stockage Azure. Audit de base de données SQL s’intègre également avec Microsoft Power BI pour faciliter les analyses et rapports d’exploration des niveaux inférieurs. Pour plus d’informations, voir [prise en main d’audit de base de données SQL](sql-database-auditing-get-started.md).

Détection des menaces de base de données SQL fournit un niveau supplémentaire de sécurité en haut d’audit. Il vous permet de répondre aux menaces qu’ils se produisent en fournissant des alertes de sécurité sur activités anormales. Pour plus d’informations, voir [prise en main de détection de base de données SQL](sql-database-threat-detection-get-started.md).  

## <a name="compliance"></a>Conformité

Outre les fonctionnalités et les fonctionnalités qui peuvent vous aider à votre application de répondre aux exigences de conformité différentes, base de données SQL Azure également ci-dessus participe à des audits réguliers et ont été certifiés par rapport à un certain nombre de normes de conformité. Pour plus d’informations, voir le [Centre de gestion de la confidentialité de Microsoft Azure](https://azure.microsoft.com/support/trust-center/), où vous pouvez trouver liste la plus récente de [certification de conformité de base de données SQL](https://azure.microsoft.com/support/trust-center/services/).
