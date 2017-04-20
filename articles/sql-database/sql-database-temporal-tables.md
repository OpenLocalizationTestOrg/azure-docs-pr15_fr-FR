<properties
   pageTitle="Prise en main des Tables temporelles dans la base de données SQL Azure | Microsoft Azure"
   description="Découvrez comment commencer à utiliser à l’aide de Tables temporelles dans la base de données SQL Azure."
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sql-database"
   ms.date="08/29/2016"
   ms.author="carlrab"/>

#<a name="getting-started-with-temporal-tables-in-azure-sql-database"></a>Prise en main des Tables temporelles dans la base de données SQL Azure

Tables temporelles sont une nouvelle fonctionnalité de programmabilité de base de données SQL Azure qui permet de suivre et analyser l’historique des modifications apportées à vos données, sans avoir besoin d’un codage personnalisé complète. Tables temporelles conservent données étroitement liées au contexte heure afin que faits stockés peuvent être interprétées comme étant valide uniquement pendant la période spécifique. Cette propriété de Tables temporelles permet une analyse efficace temporelles et aux analyses lors de l’obtention de l’évolution des données.

##<a name="temporal-scenario"></a>Scénario temporelle

Cet article décrit les étapes pour utiliser les Tables temporelles dans un scénario d’application. Supposons que vous voulez effectuer le suivi de l’activité des utilisateurs sur un nouveau site Web est développé à partir de zéro ou sur un site Web existant que vous souhaitez étendre avec analytique d’activité utilisateur. Dans cet exemple simplifié, nous part du principe que le nombre de pages web visités pendant une période de temps est un indicateur qui doit être capturé et surveiller dans la base de données de site Web est hébergé sur base de données SQL Azure. L’objectif de l’analyse de l’historique d’activités des utilisateurs est pour obtenir des entrées à recréer le site Web et offrir la meilleure expérience pour les visiteurs.

Le modèle de base de données de ce scénario est très simple, mesure d’activité utilisateur est représenté par un champ entier unique, **PageVisited**et est capturé ainsi que des informations de base dans le profil utilisateur. En outre, pour une analyse en fonction de temps, vous conservez une série de lignes pour chaque utilisateur, où chaque ligne représente le nombre de pages d’un utilisateur particulier visités pendant une période donnée.

![Schéma](./media/sql-database-temporal-tables/AzureTemporal1.png)

Peut être effectué en, vous n’avez pas besoin de placer n’importe quel effort dans votre application pour gérer les informations de cette activité. Avec les Tables temporelles, ce processus est automatisé - vous donnant une flexibilité totale lors de la conception de site Web et plus de temps pour vous concentrer sur l’analyse de données lui-même. La seule chose que vous avez à faire est pour vous assurer que la table **WebSiteInfo** est configuré comme [temporel système version](https://msdn.microsoft.com/library/dn935015.aspx#Anchor_0). Les étapes exactes pour utiliser les Tables temporelles dans ce scénario sont décrites ci-dessous.

##<a name="step-1-configure-tables-as-temporal"></a>Étape 1 : Configurer les tables en tant que temporelle

Selon que vous démarrage du développement nouvelle ou mise à niveau une application existante, vous sera créer les tables temporelles ou modifier des modèles existants en ajoutant des attributs temporelles. Ici, en général votre scénario peut être une combinaison de ces deux options. Effectuer ces action à l’aide de [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) (SSMS), [SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) (SSDT) ou tout autre outil de développement de Transact-SQL.


> [AZURE.IMPORTANT] Il est recommandé d’utiliser toujours la dernière version de Management Studio doit pour rester synchronisé avec les mises à jour Microsoft Azure et base de données SQL. [Mettre à jour de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).


###<a name="create-new-table"></a>Créer la nouvelle table

Utiliser élément de menu contextuel « Nouvelle Table version système » dans l’Explorateur d’objets SSMS pour ouvrir l’éditeur de requête avec un script de modèle de table temporel et ensuite utiliser « Spécifier des valeurs pour les paramètres de modèle « (Ctrl + Maj + M) pour remplir le modèle :

![SSMSNewTable](./media/sql-database-temporal-tables/AzureTemporal2.png)

Dans SSDT, choisissez modèle « Temporel tableau (système version) » lors de l’ajout de nouveaux éléments au projet de base de données. Qui vous ouvrez le Concepteur de tables et vous permettent de spécifier facilement la disposition du tableau :

![SSDTNewTable](./media/sql-database-temporal-tables/AzureTemporal3.png)

Vous pouvez également une table temporel create en spécifiant les instructions Transact-SQL directement, comme illustré dans l’exemple ci-dessous. Notez que les éléments de chaque table temporel obligatoires sont la définition de période et la clause SYSTEM_VERSIONING avec une référence à une autre table utilisateur qui stockera les versions historiques ligne :

````
CREATE TABLE WebsiteUserInfo 
(  
    [UserID] int NOT NULL PRIMARY KEY CLUSTERED 
  , [UserName] nvarchar(100) NOT NULL
  , [PagesVisited] int NOT NULL 
  , [ValidFrom] datetime2 (0) GENERATED ALWAYS AS ROW START
  , [ValidTo] datetime2 (0) GENERATED ALWAYS AS ROW END
  , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo)
 )  
 WITH (SYSTEM_VERSIONING = ON (HISTORY_TABLE = dbo.WebsiteUserInfoHistory));
````

Lorsque vous créez table temporel version système, le tableau d’historique accompagnement avec la configuration par défaut est automatiquement créé. Le tableau d’historique par défaut contient un index arbre-B groupé dans les colonnes période (début, fin) avec la compression de page activée. Cette configuration est optimisée pour la plupart des scénarios dans lesquels tables temporelles sont utilisés, en particulier pour les [données d’audit](https://msdn.microsoft.com/library/mt631669.aspx#Anchor_0). 

Dans ce cas particulier, nous souhaitons effectuer une analyse des tendances temporelles sur un historique de données plus de temps et des jeux de données plus grande, pourquoi le choix de stockage pour le tableau d’historique est un index columnstore groupé. Un columnstore groupé offre très une bonne compression et pour les requêtes analytiques. Tables temporelles vous permettent de configurer des index sur les tables en cours et temporelles complètement indépendamment. 

**Remarque**: Columnstore index sont uniquement disponibles dans le niveau de service premium.

Le script suivant montre comment index par défaut sur le tableau d’historique peut être modifiée pour le columnstore groupé :

````
CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory
ON dbo.WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON); 
````

Tables temporelles sont représentées dans l’Explorateur d’objets avec l’icône spécifique pour faciliter leur identification, tandis que sa table historique s’affiche comme un nœud enfant.

![ALTER table](./media/sql-database-temporal-tables/AzureTemporal4.png)

###<a name="alter-existing-table-to-temporal"></a>Modifier une table existante à temporelle

Nous allons expliquer le scénario de remplacement dans lequel la table WebsiteUserInfo existe déjà, mais n’a pas été conçue pour conserver un historique des modifications. Dans ce cas, vous pouvez simplement étendre la table existante pour devenir temporel, comme le montre l’exemple suivant :

````
ALTER TABLE WebsiteUserInfo 
ADD 
    ValidFrom datetime2 (0) GENERATED ALWAYS AS ROW START HIDDEN  
        constraint DF_ValidFrom DEFAULT DATEADD(SECOND, -1, SYSUTCDATETIME())
    , ValidTo datetime2 (0)  GENERATED ALWAYS AS ROW END HIDDEN   
        constraint DF_ValidTo DEFAULT '9999.12.31 23:59:59.99'
    , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo); 

ALTER TABLE WebsiteUserInfo  
SET (SYSTEM_VERSIONING = ON (HISTORY_TABLE = dbo.WebsiteUserInfoHistory));
GO

CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory
ON dbo.WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON); 
````

##<a name="step-2-run-your-workload-regularly"></a>Étape 2 : Exécuter régulièrement votre charge de travail

Le principal avantage de Tables temporelles est que vous n’avez pas besoin de modifier ou ajuster votre site Web d’une manière pour effectuer le suivi des modifications. Une fois créé, Tables temporelles persistent en toute transparence les versions précédentes de ligne chaque fois que vous effectuez des modifications sur vos données. 

Pour tirer pleinement parti automatique suivi des modifications pour ce scénario particulier, nous allons simplement mettre à jour colonne **PagesVisited** chaque fois que lorsque l’utilisateur termine sa session sur le site Web :

````
UPDATE WebsiteUserInfo  SET [PagesVisited] = 5 
WHERE [UserID] = 1;
````

Il est important de noter que la requête mise à jour n’a pas besoin de savoir l’heure exacte lors de l’opération proprement dite ni comment les données historiques seront préservées pour les analyser ultérieurement. Ces deux aspects sont gérées automatiquement par la base de données SQL Azure. Le diagramme suivant illustre comment les données de l’historique sont générées sur chaque mise à jour.

![TemporalArchitecture](./media/sql-database-temporal-tables/AzureTemporal5.png)

##<a name="step-3-perform-historical-data-analysis"></a>Étape 3 : Effectuer une analyse de données historiques

Lorsque le système temporel-contrôle de version est activé, analyse des données historiques est désormais une requête en s’éloignant de vous. Dans cet article, nous fournissons quelques exemples adresse analyse scénarios courants - d’apprendre tous les détails, d’Explorer différentes options introduites avec la clause [FOR SYSTEM_TIME](https://msdn.microsoft.com/library/dn935015.aspx#Anchor_3) .

Pour afficher les 10 principaux utilisateurs classés par le nombre de pages web visités à partir d’une heure plus tôt, exécutez cette requête :

````
DECLARE @hourAgo datetime2 = DATEADD(HOUR, -1, SYSUTCDATETIME());
SELECT TOP 10 * FROM dbo.WebsiteUserInfo FOR SYSTEM_TIME AS OF @hourAgo
ORDER BY PagesVisited DESC
````

Vous pouvez facilement modifier cette requête pour analyser les visites de site à partir d’un jour, un mois ou à tout moment dans le passé vous le souhaitez.

Pour effectuer des analyses statistiques base du jour précédent, utilisez l’exemple suivant :

````
DECLARE @twoDaysAgo datetime2 = DATEADD(DAY, -2, SYSUTCDATETIME());
DECLARE @aDayAgo datetime2 = DATEADD(DAY, -1, SYSUTCDATETIME());

SELECT UserID, SUM (PagesVisited) as TotalVisitedPages, AVG (PagesVisited) as AverageVisitedPages,
MAX (PagesVisited) AS MaxVisitedPages, MIN (PagesVisited) AS MinVisitedPages,
STDEV (PagesVisited) as StDevViistedPages
FROM dbo.WebsiteUserInfo 
FOR SYSTEM_TIME BETWEEN @twoDaysAgo AND @aDayAgo
GROUP BY UserId
````

Pour rechercher des activités d’un utilisateur spécifique, au sein d’une période donnée, utilisez la clause IN contenues :

````
DECLARE @hourAgo datetime2 = DATEADD(HOUR, -1, SYSUTCDATETIME());
DECLARE @twoHoursAgo datetime2 = DATEADD(HOUR, -2, SYSUTCDATETIME());
SELECT * FROM dbo.WebsiteUserInfo 
FOR SYSTEM_TIME CONTAINED IN (@twoHoursAgo, @hourAgo)
WHERE [UserID] = 1;
````

Visualisation graphique est particulièrement pratique pour les requêtes temporelles que vous pouvez afficher les tendances et modèles d’utilisation dans un intuitive façon très facilement :

![TemporalGraph](./media/sql-database-temporal-tables/AzureTemporal6.png)

##<a name="evolving-table-schema"></a>Évolution schéma de table

En règle générale, vous devez modifier le schéma de table temporel pendant que vous faites développement d’applications. Pour ce faire, exécutez simplement régulières ALTER TABLE instructions et base de données SQL Azure va correctement propager les modifications dans la table d’historique. Le script suivant montre comment vous pouvez ajouter attribut supplémentaire pour le suivi :

````
/*Add new column for tracking source IP address*/
ALTER TABLE dbo.WebsiteUserInfo 
ADD  [IPAddress] varchar(128) NOT NULL CONSTRAINT DF_Address DEFAULT 'N/A';
````

De même, vous pouvez modifier la définition de colonne pendant que votre charge de travail est actif :

````
/*Increase the length of name column*/
ALTER TABLE dbo.WebsiteUserInfo 
    ALTER COLUMN  UserName nvarchar(256) NOT NULL;
````

Enfin, vous pouvez supprimer une colonne qui vous n’avez plus besoin.

````
/*Drop unnecessary column */
ALTER TABLE dbo.WebsiteUserInfo 
    DROP COLUMN TemporaryColumn; 
````
    
Dernière [SSDT](https://msdn.microsoft.com/library/mt204009.aspx) permet également de modifier le schéma de table temporel lorsque vous êtes connecté à la base de données (mode en ligne) ou dans le cadre du projet de base de données (mode hors connexion).

##<a name="controlling-retention-of-historical-data"></a>Contrôle de conservation des données historiques

Avec les tables temporelles version système, le tableau d’historique peut augmenter la taille de la base de données plus de tables normales. Une table d’historique volumineux et croissante peut devenir un problème à la fois en raison des coûts de stockage intégral ainsi que qui impose un performances taxe sur interroger temporel. Par conséquent, le développement d’une stratégie de rétention des données pour la gestion des données dans le tableau d’historique est un aspect important de planification et la gestion du cycle de vie de chaque table temporel. Avec la base de données SQL Azure, vous avez les approches suivantes pour la gestion des données historiques dans la table temporelle :

- [Partition de table](https://msdn.microsoft.com/library/mt637341.aspx#Anchor_2)
- [Script de nettoyage personnalisé](https://msdn.microsoft.com/library/mt637341.aspx#Anchor_3)

##<a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les Tables temporel, consultez la [documentation MSDN](https://msdn.microsoft.com/library/dn935015.aspx).
Visitez le site Channel 9 pour entendre un [réussite client réel temporel implémentation](https://channel9.msdn.com/Blogs/jsturtevant/Azure-SQL-Temporal-Tables-with-RockStep-Solutions) et regarder une [démonstration temporelle en direct](https://channel9.msdn.com/Shows/Data-Exposed/Temporal-in-SQL-Server-2016).
