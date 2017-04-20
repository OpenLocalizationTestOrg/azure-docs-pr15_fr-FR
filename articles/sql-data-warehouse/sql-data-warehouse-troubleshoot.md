<properties
   pageTitle="Résolution des problèmes de Data Warehouse SQL Azure | Microsoft Azure"
   description="Résolution des problèmes de Data Warehouse SQL Azure."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/30/2016"
   ms.author="sonyama;barbkess"/>

# <a name="troubleshooting-azure-sql-data-warehouse"></a>Résolution des problèmes de Data Warehouse SQL Azure

Cette rubrique dresse la liste des plus fréquemment posées dépannage que nous entendre de nos clients.

## <a name="connecting"></a>Connexion

| Problème                              | Résolution                                      |
| :----------------------------------| :---------------------------------------------- |
| Échec de la connexion pour l’utilisateur 'NT AUTHORITY\ANONYMOUS connexion'. (Microsoft SQL Server, erreur : 18456) | Cette erreur se produit lorsqu’un utilisateur AAD essaie de se connecter à la base de données principale, mais n’a pas un utilisateur dans le masque.  Pour corriger ce problème soit spécifier SQL Data Warehouse que vous souhaitez vous connecter au moment de la connexion ou ajouter l’utilisateur à la base de données principale.  Consultez l’article de [Présentation de la sécurité][] pour plus d’informations.|
|Le serveur principal « MyUserName » n’est pas en mesure d’accéder à la base de données « principale » dans le contexte de sécurité en cours. Impossible d’ouvrir de base de données utilisateur par défaut. Échec de la connexion. Échec de la connexion pour l’utilisateur 'MyUserName'. (Microsoft SQL Server, erreur : 916) | Cette erreur se produit lorsqu’un utilisateur AAD essaie de se connecter à la base de données principale, mais n’a pas un utilisateur dans le masque.  Pour corriger ce problème soit spécifier SQL Data Warehouse que vous souhaitez vous connecter au moment de la connexion ou ajouter l’utilisateur à la base de données principale.  Consultez l’article de [Présentation de la sécurité][] pour plus d’informations.|
| Erreur CTAIP                        | Cette erreur peut se produire quand une connexion a été créée dans la base de données SQL server maître, mais pas dans la base de données SQL Data Warehouse.  Si vous rencontrez cette erreur, consultez l’article [Présentation de la sécurité][] .  Cet article explique comment créer créer une connexion et un utilisateur sur Masque, puis sur la création d’un utilisateur dans la base de données SQL Data Warehouse.|
| Bloqué par le pare-feu                |Bases de données SQL Azure sont protégés par pare-feux au niveau serveur et base de données pour vous assurer seulement connus adresses IP ont accès à une base de données. Les pare-feu sont sécurisés par défaut, ce qui signifie que vous devez activer explicitement et adresse IP ou plage d’adresses avant de vous connecter.  Pour configurer votre pare-feu pour l’accès, suivez les étapes [configurer l’accès serveur pare-feu pour votre adresse IP client][] dans les [instructions Provisioning][].|
| Impossible de se connecter avec outil ou un pilote | Data Warehouse SQL recommande l’utilisation de [SSMS][], [SSDT Visual Studio 2015][]ou [sqlcmd][] pour vos données de la requête. Pour plus d’informations sur les pilotes et la connexion à Data Warehouse SQL, consultez les articles [pilotes pour Azure SQL Data Warehouse][] et [se connecter à SQL Azure Data Warehouse][] .|


## <a name="tools"></a>Outils

| Problème                              | Résolution                                      |
| :----------------------------------| :---------------------------------------------- |
| Explorateur d’objets Visual Studio il manque des utilisateurs AAD | Il s’agit d’un problème connu.  Pour résoudre ce problème, afficher les utilisateurs de [sys.database_principals][].  Consultez [l’authentification Azure SQL Data Warehouse][] pour en savoir plus sur l’utilisation de Azure Active Directory avec Data Warehouse SQL.|

## <a name="performance"></a>Performances

|  Problème                             | Résolution                                      |
| :----------------------------------| :---------------------------------------------- |
| Résolution des problèmes de performances de la requête  | Si vous tentez de résoudre les problèmes d’une requête spécifique, commencez à [apprendre à surveiller vos requêtes][].|
| Les offres et les performances des requêtes un problème est souvent un résultat de statistiques manquantes   | La cause la plus courante d’une baisse des performances est manque de statistiques sur vos tables.  Afficher les [statistiques de la table maintenance] [ Statistics] pour plus d’informations sur la création des statistiques et pourquoi ils sont essentielles à votre performance.|
| Accès concurrentiels au faible / requêtes en file d’attente   | Présentation de la [gestion de la charge de travail][] est important afin de comprendre comment équilibrer l’allocation de mémoire concurrence.|
| Comment mettre en œuvre les meilleures pratiques    | Le meilleur placez pour commencer à découvrir moyens d’améliorer les performances de la requête est l’article [SQL Data Warehouse meilleures pratiques][] .|
| Comment faire pour améliorer les performances de mise à l’échelle  | La solution à l’amélioration des performances est parfois simplement ajouter que plus rapide puissance à vos requêtes en [redimensionnant votre Data Warehouse SQL][]accrue.|
| Faibles performances de requête à la suite de la qualité de l’index un problème | Requêtes parfois peuvent ralenti en raison de [la qualité de l’index columnstore un problème][].  Consultez cet article pour plus d’informations et comment [reconstruire à partir d’index pour améliorer la qualité du segment][].|

## <a name="system-management"></a>Gestion du système

|  Problème                             | Résolution                                      |
| :----------------------------------| :---------------------------------------------- |
| Message 40847 : N’a pas pu effectuer l’opération, car server dépasse le quota d’unité de Transaction de base de données autorisé de 45000. | Réduisez la [DWU][] de la base de données que vous essayez de créer ou [demander une augmentation des quotas][].|
| Utilisation de l’espace chargées    | Voir les [tailles de tables][] de comprendre l’utilisation de l’espace de votre système.|
| Aide à la gestion des tables          | Reportez-vous à la [vue d’ensemble du tableau] [ Overview] article pour vous aider à gérer vos tableaux.  Cet article inclut également des liens dans les rubriques plus détaillées comme [types de données de tableau][Data types], [distribuer une table][Distribute], [indexation d’une table][Index], [partition d’une table][Partition], [statistiques de la table maintenance] [ Statistics] et [tables temporaires][Temporary].|

## <a name="polybase"></a>Polybase

|  Problème                             | Résolution                                      |
| :----------------------------------| :---------------------------------------------- |
| Erreur UTF-8                        |  Actuellement PolyBase ne prend en charge du chargement de fichiers de données qui ont été codé UTF-8.  Pour obtenir des instructions sur la façon de contourner cette limitation, voir [utilisation autour de la configuration minimale requise PolyBase UTF-8][] .|
| Chargement échoue en raison de lignes de grande taille   | Actuellement prise en charge de la ligne de grande taille n’est pas disponible pour Polybase.  Cela signifie que si votre tableau contient varchar (max), nvarchar (max) ou varbinary (max), tables externes ne peuvent pas être utilisés pour charger vos données.  Chargement de lignes de grande taille est actuellement pris en charge uniquement via Azure Data Factory (avec BCP), Azure flux Analytique, SSIS, BCP ou la classe SQLBulkCopy .NET. Prise en charge PolyBase des lignes de grande taille est ajouté dans une version ultérieure.|
| Échec de chargement de bcp de table avec le type de données MAX | Il existe un problème connu qui requiert que varchar (max), nvarchar (max) ou varbinary (max) soient placés à la fin de la table dans certains scénarios.  Essayez de déplacer votre nombre maximal de colonnes à la fin de la table.|

## <a name="differences-from-sql-database"></a>Différences de base de données SQL

|  Problème                             | Résolution                                      |
| :----------------------------------| :---------------------------------------------- |
| Fonctionnalités de base de données SQL non prises en charge  | Voir les [fonctionnalités de tableau non prises en charge][].|
| Types de données non prises en charge de la base de données SQL  | Voir les [types de données non prises en charge][].|
| SUPPRIMER et limitations de la mise à jour      | Voir [solutions de contournement mise à jour][], [Supprimer les solutions de contournement][] et [SACT à l’aide de contourner la syntaxe de la mise à jour et supprimer non prises en charge][].  |
| Déclaration de fusion n’est pas pris en charge   | Voir [Fusionner des solutions de contournement][].|
| Limitations de la procédure stockée       | Voir [limitations de procédure stockée][] pour comprendre certaines des limitations des procédures stockées.|
| UDF ne prennent pas en charge les instructions SELECT | Il s’agit d’une limite actuelle de notre UDF.  Voir [Créer une fonction][] pour la syntaxe de la que nous prenons en charge.   |

## <a name="next-steps"></a>Étapes suivantes

Si vous n’êtes ont été impossible de trouver une solution à votre problème ci-dessus, voici quelques autres ressources que vous pouvez essayer.

- [Blogs]
- [Demandes de fonctionnalités]
- [Vidéos]
- [Blogs de l’équipe Chat]
- [Créer des tickets de support]
- [Forum MSDN]
- [Forum de dépassement de capacité de pile]
- [Twitter]

<!--Image references-->

<!--Article references-->
[Présentation de la sécurité]: ./sql-data-warehouse-overview-manage-security.md
[SSMS]: https://msdn.microsoft.com/library/mt238290.aspx
[SSDT pour Visual Studio 2015]: ./sql-data-warehouse-install-visual-studio.md
[Pilotes pour Data Warehouse SQL Azure]: ./sql-data-warehouse-connection-strings.md
[Se connecter à Data Warehouse SQL Azure]: ./sql-data-warehouse-connect-overview.md
[Créer des tickets de support]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Mise à l’échelle votre Data Warehouse SQL]: ./sql-data-warehouse-manage-compute-overview.md
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[demander une augmentation des quotas]: ./sql-data-warehouse-get-started-create-support-ticket.md#request-quota-change 
[Apprendre comment contrôler vos requêtes]: ./sql-data-warehouse-manage-monitor.md
[Mise en service des instructions]: ./sql-data-warehouse-get-started-provision.md
[Configurer l’accès au serveur pare-feu pour votre IP du client]: ./sql-data-warehouse-get-started-provision.md#create-a-new-azure-sql-server-level-firewall
[Meilleures pratiques SQL Data Warehouse]: ./sql-data-warehouse-best-practices.md
[Taille de tableau]: ./sql-data-warehouse-tables-overview.md#table-size-queries
[Fonctionnalités de tableau non prises en charge]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[Types de données non prises en charge]: ./sql-data-warehouse-tables-data-types.md#unsupported-data-types
[Overview]: ./sql-data-warehouse-tables-overview.md
[Data types]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[Qualité de l’index columnstore un problème]: ./sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality
[Reconstruire des index pour améliorer la qualité du segment]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Gestion de la charge de travail]: ./sql-data-warehouse-develop-concurrency.md
[À l’aide de SACT contourner la syntaxe de la mise à jour et supprimer non prises en charge]: ./sql-data-warehouse-develop-ctas.md#using-ctas-to-work-around-unsupported-features
[Mettre à jour les solutions de contournement]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-update-statements
[SUPPRIMER des solutions de contournement]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-delete-statements
[FUSIONNER des solutions de contournement]: ./sql-data-warehouse-develop-ctas.md#replace-merge-statements
[Limitations de la procédure stockée]: ./sql-data-warehouse-develop-stored-procedures.md#limitations
[Authentification SQL Azure Data Warehouse]: ./sql-data-warehouse-authentication.md
[Travail autour de la configuration minimale requise PolyBase UTF-8]: ./sql-data-warehouse-load-polybase-guide.md#working-around-the-polybase-utf-8-requirement

<!--MSDN references-->
[Sys.database_principals]: https://msdn.microsoft.com/library/ms187328.aspx
[CRÉER DE FONCTION]: https://msdn.microsoft.com/library/mt203952.aspx
[Sqlcmd]: https://azure.microsoft.com/en-us/documentation/articles/sql-data-warehouse-get-started-connect-sqlcmd/

<!--Other Web references-->
[Blogs]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Blogs de l’équipe Chat]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Demandes de fonctionnalités]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Forum MSDN]: https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse
[Forum de dépassement de capacité de pile]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Vidéos]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse

