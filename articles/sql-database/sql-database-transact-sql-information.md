<properties
   pageTitle="Non prises en charge dans T-SQL de base de données SQL Azure | Microsoft Azure"
   description="Instructions Transact-SQL moins entièrement pris en charge dans la base de données SQL Azure"
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
   ms.date="08/30/2016"
   ms.author="rick.byham@microsoft.com"/>

# <a name="azure-sql-database-transact-sql-differences"></a>Différences Transact-SQL de base de données SQL Azure


La plupart des fonctionnalités Transact-SQL qui dépendent des applications prises en charge dans Microsoft SQL Server et base de données SQL Azure. Une liste partielle des fonctionnalités prises en charge pour les applications suit :

- Types de données.
- Opérateurs.
- Fonctions de chaîne, arithmétiques, logiques et le curseur.

Toutefois, base de données SQL Azure est conçu pour isoler des fonctions à partir de n’importe quel dépendance par rapport à la base de données **principale** . Par conséquent beaucoup d’activités au niveau du serveur est inappropriés pour la base de données SQL et non prises en charge. Fonctionnalités déconseillées dans SQL Server ne sont généralement pas pris en charge dans la base de données SQL.

> [AZURE.NOTE]
> Cette rubrique décrit les fonctionnalités qui sont disponibles avec la base de données SQL lors de la mise à niveau vers la version actuelle ; V12 de base de données SQL. Pour plus d’informations sur V12, voir [du SQL de base de données V12 que nouveau](sql-database-v12-whats-new.md).

Les sections suivantes répertorient les fonctionnalités qui sont partiellement prises en charge et les fonctionnalités qui sont complètement non prises en charge.


## <a name="features-partially-supported-in-sql-database-v12"></a>Fonctionnalités partiellement prises en charge dans V12 de base de données SQL

V12 de base de données SQL prend en charge certains mais pas tous les arguments qui existent dans les instructions SQL Server 2016 Transact-SQL correspondantes. Par exemple, l’instruction CREATE PROCEDURE est disponible alors que toutes les options de CREATE PROCEDURE ne sont pas disponibles. Consultez les rubriques de syntaxe liées pour plus d’informations sur les fonctionnalités prises en charge de chaque instruction.

- Bases de données : [créer](https://msdn.microsoft.com/library/dn268335.aspx )/[ALTER](https://msdn.microsoft.com/library/ms174269.aspx)
- DMV est généralement disponibles pour les fonctionnalités qui sont disponibles.
- Fonctions : [créer](https://msdn.microsoft.com/library/ms186755.aspx)/[ALTER fonction](https://msdn.microsoft.com/library/ms186967.aspx)
- [ARRÊT](https://msdn.microsoft.com/library/ms173730.aspx) 
- Connexions : [créer](https://msdn.microsoft.com/library/ms189751.aspx)/[ALTER LOGIN](https://msdn.microsoft.com/library/ms189828.aspx)
- Procédures stockées : [créer](https://msdn.microsoft.com/library/ms187926.aspx)/[ALTER PROCEDURE](https://msdn.microsoft.com/library/ms189762.aspx)
- Tables : [créer](https://msdn.microsoft.com/library/dn305849.aspx)/[ALTER](https://msdn.microsoft.com/library/ms190273.aspx)
- Types (personnalisé) : [Créer un TYPE](https://msdn.microsoft.com/library/ms175007.aspx)
- Utilisateurs : [créer](https://msdn.microsoft.com/library/ms173463.aspx)/[ALTER utilisateur](https://msdn.microsoft.com/library/ms176060.aspx)
- Affichages : [créer](https://msdn.microsoft.com/library/ms187956.aspx)/[Modifier vue](https://msdn.microsoft.com/library/ms173846.aspx)

## <a name="features-not-supported-in-sql-database"></a>Fonctionnalités non prises en charge dans la base de données SQL

- Classement des objets système
- Connexion liée : instructions de point de terminaison, ORIGINAL_DB_NAME. Base de données SQL ne prend pas en charge l’authentification Windows, mais ne prend pas en charge l’authentification Azure Active Directory similaire. Certains types d’authentification nécessitent la dernière version de SSMS. Pour plus d’informations, consultez l’article [connexion à base de données SQL ou données entrepôt par à l’aide de Azure Active Directory l’authentification SQL](sql-database-aad-authentication.md).
- Croisées requêtes de base de données à l’aide de noms de composant trois ou quatre. (Requêtes de bases de données croisées en lecture seule sont pris en charge à l’aide de [requête de base de données élastique](sql-database-elastic-query-overview.md).)
- Chaîne de la propriété de base de données externe, le paramètre digne de confiance
- Collecteur de données
- Diagrammes de base de données
- Messagerie de base de données
- DATABASEPROPERTY (utilisez DATABASEPROPERTYEX à la place)
- Connexions EXECUTE AS
- Chiffrement : gestion de clés extensible
- Gestion des événements : événements, les notifications d’événements, les notifications de requête
- Fonctionnalités relatives à la position de fichier de base de données, la taille et les fichiers de base de données qui sont automatiquement gérés par Microsoft Azure.
- Fonctionnalités relatives à la disponibilité élevée, qui est gérée via votre compte Microsoft Azure : sauvegarder, restaurer, AlwaysOn, la mise en miroir de base de données, journaux, différents modes de récupération. Pour plus d’informations, voir sauvegarde de base de données SQL Azure et restaurer.
- Fonctionnalités qui s’appuient sur le lecteur de journal s’exécutant sur base de données SQL : réplication par émission, Capture de données modifiées.
- Fonctionnalités qui s’appuient sur l’Agent SQL Server ou la base de données MSDB : tâches, les alertes, les opérateurs, gestion basée sur la stratégie, de base de données, serveurs d’administration centrale.
- FILESTREAM
- Fonctions : fn_get_sql, fn_virtualfilestats, fn_virtualservernodes
- Tables temporaires globales
- Paramètres du serveur liés au matériel : mémoire, threads de travail, affinité du processeur, indicateurs de suivi, etc.. Utiliser les niveaux de service à la place.
- HAS_DBACCESS
- SUPPRIMER LES STATISTIQUES TRAVAIL
- Serveurs liés, OPENQUERY, OPENROWSET, OPENDATASOURCE, insérer en bloc et noms à quatre éléments
- Serveurs maître/cible
- .NET framework [intégration CLR avec SQL Server](http://msdn.microsoft.com/library/ms254963.aspx)
- Gestionnaire de ressources
- Recherche sémantique
- Références de serveur. USE base de données adaptées à la place des informations d’identification.
- Éléments au niveau du serveur : serveur rôles, IS_SRVROLEMEMBER, sys.login_token. Autorisations au niveau du serveur ne sont pas disponibles, bien que certains sont remplacés par des autorisations au niveau de la base de données. Certains DMV au niveau du serveur n’est pas disponibles, bien que certains sont remplacés par DMV au niveau de la base de données.
- Express sans serveur : localdb, instances d’utilisateur
- Intermédiaire de service
- INSTRUCTION SET REMOTE_PROC_TRANSACTIONS
- ARRÊT
- sp_addmessage
- options de sp_configure et RECONFIGURE. Certaines options sont disponibles à l’aide de [ALTER de base de données inclus dans l’étendue de CONFIGURATION](https://msdn.microsoft.com/library/mt629158.aspx).
- sp_helpuser
- sp_migrate_user_to_contained
- Audit de SQL Server. Utiliser la base de données SQL audit à la place.
- Générateur de profils SQL Server
- Trace SQL Server
- Indicateurs de suivi. Certains éléments d’indicateur de suivi des messages ont été déplacés vers les modes de compatibilité.
- Débogage Transact-SQL
- Déclencheurs : À portée de serveur ou déclencheurs d’ouverture de session
- Instruction USE : pour modifier le contexte de base de données à une autre base de données, vous devez apporter une nouvelle connexion à la nouvelle base de données.


## <a name="full-transact-sql-reference"></a>Référence Transact-SQL complète

Pour plus d’informations sur la grammaire Transact-SQL, l’utilisation et d’exemples, voir [Référence Transact-SQL (moteur de base de données)](https://msdn.microsoft.com/library/bb510741.aspx) dans la documentation en ligne de SQL Server. 

### <a name="about-the-applies-to-tags"></a>Sur les balises « S’applique à »

La référence Transact-SQL inclut les sujets relatifs aux versions de SQL Server 2008 du présent. Sous le titre de la rubrique est une icône de la barre, répertoriant les quatre plateformes de SQL Server et indiquant les conditions d’application. Par exemple, les groupes de disponibilité ont été introduites dans SQL Server 2012. La rubrique [Créer un groupe AVAILABILTY](https://msdn.microsoft.com/library/ff878399.aspx) indique que l’instruction s’applique aux ** SQL Server (en commençant par 2012). L’instruction ne s’applique pas à SQL Server 2008, SQL Server 2008 R2, base de données SQL Azure, SQL Azure Data Warehouse ou Parallel Data Warehouse.

Dans certains cas, l’objet d’une rubrique général peut être utilisé dans un produit, mais il existe des différences mineures entre les produits. Les différences sont indiquées en points centraux dans la rubrique le cas échéant.

