<properties
   pageTitle="Authentification SQL Azure Data Warehouse | Microsoft Azure"
   description="Authentification Azure Active Directory (DAS) et SQL Server Azure SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter=""
   authors="byham"
   manager="barbkess"
   editor=""
   tags=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="09/24/2016"
   ms.author="rickbyh;barbkess;sonyama"/>

# <a name="authentication-to-azure-sql-data-warehouse"></a>Authentification SQL Azure Data Warehouse

> [AZURE.SELECTOR]
- [Présentation de la sécurité](sql-data-warehouse-overview-manage-security.md)
- [Authentification](sql-data-warehouse-authentication.md)
- [Chiffrement (portail)](sql-data-warehouse-encryption-tde.md)
- [Chiffrement (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)

Pour vous connecter à SQL Data Warehouse, vous devez passer informations d’identification de sécurité à des fins d’authentification. Lors de l’établissement d’une connexion, certains paramètres de connexion sont configurés dans le cadre d’établir votre session de requête.  

Pour plus d’informations sur la sécurité et comment activer les connexions à votre data warehouse, voir [sécuriser une base de données SQL Data Warehouse][].

## <a name="sql-authentication"></a>Authentification SQL
Pour vous connecter à SQL Data Warehouse, vous devez fournir les informations suivantes :

- Nom complet du serveur
- Spécifier l’authentification SQL
- Nom d’utilisateur
- Mot de passe
- Base de données par défaut (facultatif)

Par défaut votre connexion se connecte à la base de données *principale* et non votre base de données de l’utilisateur. Pour vous connecter à votre base de données utilisateur, vous pouvez choisir une des deux actions suivantes :

- Spécifiez la base de données par défaut lors de l’enregistrement de votre serveur avec l’Explorateur d’objets SQL Server dans SSDT, SSMS, ou dans la chaîne de connexion d’application. Par exemple, incluez le paramètre InitialCatalog pour une connexion ODBC.
- Mettez en surbrillance la base de données utilisateur avant de créer une session dans SSDT.

> [AZURE.NOTE] L’instruction Transact-SQL **Utilisation MyDatabase ;** n’est pas prise en charge pour la modification de la base de données d’une connexion. Pour des instructions de connexion à SQL Data Warehouse avec SSDT, reportez-vous à l’article de la [requête avec Visual Studio][] .

## <a name="azure-active-directory-aad-authentication"></a>Authentification Azure Active Directory (DAS)

[Azure Active Directory] [ What is Azure Active Directory] authentification est un mécanisme de la connexion à Microsoft Azure SQL Data Warehouse à l’aide des identités dans Azure Active Directory (AD Azure). Avec l’authentification Azure Active Directory, vous pouvez gestion centralisée l’identité des utilisateurs de base de données et autres services Microsoft dans un emplacement central. Gestion des ID centrale propose un emplacement unique pour gérer les utilisateurs SQL Data Warehouse et simplifie la gestion des autorisations. 

### <a name="benefits"></a>Avantages

Azure Active Directory les avantages suivants :

- Fournit une alternative à l’authentification SQL Server.
- Permet de bloquer la prolifération des identités utilisateur sur les serveurs de base de données.
- Permet de rotation de mot de passe dans un emplacement unique
- Gérer les autorisations de base de données à l’aide de groupes (DAS) externes.
- Supprime les stocker les mots de passe en activant l’authentification Windows intégrée et autres formes d’authentification pris en charge par Azure Active Directory.
- Utilisateurs de base de données utilise contenus pour authentifier les identités au niveau de la base de données.
- Prend en charge l’authentification basée sur un jeton pour les applications qui se connectent à SQL Data Warehouse.
- Prend en charge l’authentification multifacteur via l’authentification universel Active Directory pour SQL Server Management Studio. Pour obtenir une description de l’authentification multifacteur, voir [SSMS prend en charge pour l’authentification Multifacteur d’Azure AD avec la base de données SQL et SQL Data Warehouse](../sql-database/sql-database-ssms-mfa-authentication.md).

> [AZURE.NOTE] Azure Active Directory est toujours relativement nouveau et présente certaines limitations. Pour vous assurer qu’Azure Active Directory est une bonne solution pour votre environnement, voir [fonctionnalités Azure AD et limites][], en particulier les remarques supplémentaires.

### <a name="configuration-steps"></a>Étapes de configuration

Suivez ces étapes pour configurer l’authentification Azure Active Directory.

1. Créer et remplir un Azure Active Directory
2. Facultatif : Associer ou modifier l’annuaire active directory qui est associé à votre abonnement Azure
3. Créer un administrateur de magasin de données SQL Azure Azure Active Directory.
4. Configurer vos ordinateurs clients
5. Créer des utilisateurs de base de données contenues dans votre base de données mappé avec les identités Azure AD
6. Se connecter à votre data warehouse à l’aide des identités Azure AD

Actuellement les utilisateurs Azure Active Directory ne sont pas affichés dans l’Explorateur d’objets SSDT. Pour résoudre ce problème, afficher les utilisateurs de [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx).
  
### <a name="find-the-details"></a>Découvrez les détails
- Suivez les étapes détaillées. Les étapes détaillées pour configurer et utiliser l’authentification Azure Active Directory sont presque identiques pour la base de données SQL Azure et Azure SQL Data Warehouse. Suivez les étapes détaillées dans la rubrique [connexion à la base de données SQL ou données entrepôt par à l’aide de Azure Active Directory l’authentification SQL](../sql-database/sql-database-aad-authentication.md).
- Créer des rôles de base de données personnalisée et ajouter des utilisateurs aux rôles. Puis accorder des autorisations précises pour les rôles. Pour plus d’informations, voir [Prise en main avec des autorisations de moteur de base de données](https://msdn.microsoft.com/library/mt667986.aspx).

## <a name="next-steps"></a>Étapes suivantes

Pour commencer à interroger votre data warehouse avec Visual Studio et d’autres applications, voir [requête avec Visual Studio][].

<!-- Article references -->
[Sécuriser une base de données SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md
[Requête avec Visual Studio]: ./sql-data-warehouse-query-visual-studio.md
[What is Azure Active Directory]: ../active-directory/active-directory-whatis.md
[Limitations et azure AD fonctionnalités]: ../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations
