<properties
   pageTitle="Instructions et Limitations de général de la base de données SQL Azure"
   description="Cette page décrit certaines limitations générales pour la base de données SQL Azure, ainsi que des zones de l’interopérabilité et la prise en charge."
   services="sql-database"
   documentationCenter="na"
   authors="CarlRabeler"
   manager="jhubbard"
   editor="monicar" />
<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="09/06/2016"
   ms.author="carlrab" />

# <a name="azure-sql-database-general-limitations-and-guidelines"></a>Instructions et Limitations de général de la base de données SQL Azure

Cette rubrique fournit des limites d’ordre général et des instructions pour la base de données SQL Azure. Pour une présentation complète des quotas, gestion des ressources et prise en charge, consultez les [ressources supplémentaires](#additional-guidelines) à la fin de cette rubrique.

## <a name="connectivity-and-authentication"></a>Connectivité et l’authentification

  - L’authentification Windows n’est pas pris en charge. Reportez-vous à [gestion de bases de données et les connexions de base de données SQL Azure](sql-database-manage-logins.md). Toutefois, l’authentification Azure Active Directory est prise en charge avec certaines limitations. Voir [se connecter à la base de données SQL avec l’authentification Azure Active Directory](sql-database-aad-authentication.md).

  - Base de données SQL Microsoft Azure prend en charge les données tabulaires flux (TDS) protocole client 7.3 ou version ultérieure.

  - Seules les connexions TCP/IP sont autorisées.

  - Le navigateur de SQL Server 2008 SQL Server n’est pas pris en charge, car la base de données Microsoft Azure SQL n’a pas de ports dynamiques, uniquement ports 1433.

## <a name="sql-server-agentjobs"></a>SQL Server/travaux de l’Agent

Base de données Microsoft Azure SQL n’autorise pas l’Agent de SQL Server, mais vous pouvez utiliser les travaux élastique à l’exécution des tâches sur un à plusieurs bases de données. Pour plus d’informations sur les tâches élastiques, voir [travaux élastique](sql-database-elastic-jobs-overview.md).

## <a name="sql-server-collation-support"></a>Prise en charge du classement SQL Server

Le classement de base de données par défaut utilisé par base de données SQL Microsoft Azure est **SQL_LATIN1_GENERAL_CP1_CI_AS**, où **LATIN1_GENERAL** est l’anglais (États-Unis), **CP1** est la page de code 1252, **CI** respecte la casse, et **en tant** qu’est accents. Il n’est pas possible de modifier le classement des bases de données V12. Pour plus d’informations sur la façon de définir le classement, voir [assemblées (Transact-SQL)](https://msdn.microsoft.com/library/ms184391.aspx).

## <a name="naming-requirements"></a>Exigences concernant l'

Certains noms d’utilisateur ne sont pas autorisés pour des raisons de sécurité. Vous ne pouvez pas utiliser les noms suivants :

 - **administrateur**
 - **administrateur**
 - **invité**
 - **racine**
 - **association de sécurité**

Noms pour tous les nouveaux objets doivent respecter les règles de SQL Server pour les identificateurs. Pour plus d’informations, voir [identificateurs](https://msdn.microsoft.com/library/ms175874.aspx).

En outre, les noms de connexion et de l’utilisateur ne peut pas contenir la \ caractère (l’authentification Windows n’est pas pris en charge).

## <a name="additional-guidelines"></a>Instructions supplémentaires

- Outre les limites d’ordre général décrites dans cet article, base de données SQL contient les quotas de ressources spécifique et limitations en fonction de votre **niveau de service**. Pour une vue d’ensemble des niveaux de service, voir [niveaux de service de base de données SQL](sql-database-service-tiers.md).

- Autres limites de la base de données SQL, voir [Limites de ressources de base de données SQL Azure](sql-database-resource-limits.md).

- Pour la sécurité directives apparentées, consultez [les instructions de sécurité de base de données SQL Azure et Limitations](sql-database-security-guidelines.md).

- Une autre zone connexe entoure la compatibilité de base de données SQL Azure contient avec des versions de SQL Server, telles que SQL Server 2014 et SQL Server 2016 en local. La dernière version V12 de base de données SQL Azure a apporté de nombreuses améliorations dans cette zone. Pour plus d’informations, voir [Nouveautés de V12 de base de données SQL](sql-database-v12-whats-new.md).

- Pour plus d’informations sur la disponibilité des pilotes et prise en charge de base de données SQL, consultez [Les bibliothèques de connexions de base de données SQL et SQL Server](sql-database-libraries.md).
