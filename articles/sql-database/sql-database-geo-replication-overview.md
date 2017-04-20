<properties
    pageTitle="Active Geo-réplication de base de données SQL Azure"
    description="Réplication de Geo active vous permet d’installation 4 copies de votre base de données dans un des centres de données Azure."
    services="sql-database"
    documentationCenter="na"
    authors="stevestein"
    manager="jhubbard"
    editor="monicar" />


<tags
    ms.service="sql-database"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="NA"
    ms.date="09/26/2016"
    ms.author="sstein" />

# <a name="overview-sql-database-active-geo-replication"></a>Vue d’ensemble : SQL de base de données Active Geo-réplication

Réplication de Geo active permet de vous permettent de configurer jusqu'à quatre bases de données secondaires lisibles dans les emplacements de centre de données identiques ou différents (régions). Bases de données secondaires sont disponibles pour l’interrogation et pour le basculement en cas de panne du centre de données ou l’incapacité à se connecter à la base de données principale.

>[AZURE.NOTE] Réplication Geo active (lisibles secondaires) est désormais disponible pour toutes les bases de données de tous les niveaux de service. Dans avril 2017, le type secondaire illisible validité prend fin et bases de données non lisible existantes passeront automatiquement vers secondaires lisibles.

 Vous pouvez configurer Geo-réplication Active à l’aide du [portail Azure](sql-database-geo-replication-portal.md), [PowerShell](sql-database-geo-replication-powershell.md), [Transact-SQL](sql-database-geo-replication-transact-sql.md), ou la [API REST - créer ou mettre à jour la base de données](https://msdn.microsoft.com/library/azure/mt163685.aspx).

> [AZURE.SELECTOR]
- [Configurer : Azure portail](sql-database-geo-replication-portal.md)
- [Configurer : PowerShell](sql-database-geo-replication-powershell.md)
- [Configurer : T-SQL](sql-database-geo-replication-transact-sql.md)

If pour une raison Échec de votre base de données principale, ou simplement doit être mis en mode hors connexion, vous pouvez *Basculer* à certaines de vos bases de données secondaires. Lorsque le basculement est activée pour une base de données secondaires, tous les autres secondaires sont automatiquement liés à la nouvelle primaire.

Vous pouvez basculer vers un secondaire à l’aide du [portail Azure](sql-database-geo-replication-failover-portal.md), [PowerShell](sql-database-geo-replication-failover-powershell.md), [Transact-SQL](sql-database-geo-replication-failover-transact-sql.md), [l’API REST - basculement planifiée](https://msdn.microsoft.com/ibrary/azure/mt575007.aspx)ou [API REST - basculement non planifié](https://msdn.microsoft.com/library/azure/mt582027.aspx).


> [AZURE.SELECTOR]
- [Basculement : Portail Azure](sql-database-geo-replication-failover-portal.md)
- [Basculement : PowerShell](sql-database-geo-replication-failover-powershell.md)
- [Basculement : T-SQL](sql-database-geo-replication-failover-transact-sql.md)

Après le basculement, vérifiez que la configuration requise d’authentification pour votre base de données et votre serveur est configurée sur la nouvelle primaire. Pour plus d’informations, consultez [sécurité de base de données SQL après sinistre](sql-database-geo-replication-security-config.md).


La fonctionnalité de réplication de Geo Active met en œuvre un mécanisme pour assurer la redondance de base de données au sein de la même région Microsoft Azure ou dans différentes régions (geo redondance). Réplication de Geo Active réplique asynchrone transactions validées à partir d’une base de données au maximum de quatre copies de la base de données sur différents serveurs, à l’aide de lire instantané validée isolement (objet) pour isoler. Lorsque la réplication Geo Active est configurée, une base de données secondaire est créée sur le serveur spécifié. La base de données d’origine devient la base de données principale. La base de données principale réplique asynchrone transactions validées sur chacune des bases de données secondaires. Seules les transactions complètes sont répliquées. Tout en un instant donné, la base de données secondaire peut être légèrement derrière la base de données principale, les données secondaires sont assurées de ne jamais avoir transactions partielles. Vous trouverez les données RPO en [Vue d’ensemble de continuité des activités](sql-database-business-continuity.md).

Un des principaux avantages de Geo-réplication Active est qu’il fournit une solution de récupération d’urgence au niveau de la base de données avec l’heure de récupération faible. Lorsque vous placez la base de données secondaire sur un serveur dans une zone différente, vous ajoutez la résilience maximale à votre application. Redondance entre région permet aux applications de récupérer d’une perte permanente d’un centre de données entier ou des parties d’un centre de données provoquée par sinistre naturel, une erreur humaine grave ou agit malveillants. La figure suivante montre qu'un exemple de Geo-réplication Active configuré avec un serveur principal dans la région du Nord États-Unis et secondaire dans la région Sud.

![Relation de geo réplication](./media/sql-database-active-geo-replication/geo-replication-relationship.png)

Un autre avantage est que les bases de données secondaires sont lisibles et peuvent être utilisés pour décharger les charges de travail en lecture seule tels que travaux de création de rapports. Si vous souhaitez uniquement utiliser la base de données secondaire pour l’équilibrage de charge, vous pouvez le créer dans la même région en tant que le serveur principal. Création d’un moniteur secondaire dans la même région, n’améliore pas la résilience à défaillance de l’application.  

Autres scénarios où la réplication Geo Active peut être utilisée :

- **Migration de base de données**: vous pouvez utiliser la réplication Geo Active pour migrer une base de données d’un serveur à un autre en ligne avec temps d’arrêt minimum.
- **Mises à niveau de l’application**: vous pouvez créer un secondaire supplémentaire une copie précédent fail mises à niveau de l’application.

Pour mettre en place continuité réel, rendant plus redondantes de base de données entre centres de données est uniquement une partie de la solution. Récupération d’une application (service) de bout en bout après qu’une défaillance grave requiert la récupération de tous les composants qui constituent le service et les services qui en dépendent. Exemples de ces composants sont le logiciel client (par exemple, un navigateur avec un code JavaScript personnalisé), de serveurs web frontaux, stockage et DNS. Il est essentiel que tous les composants sont résistants aux mêmes défaillances et devient disponibles dans la récupération heure objectifs de votre application. Par conséquent, vous devez identifier tous les services dépendants et comprendre les garanties et les fonctionnalités qu’ils fournissent. Ensuite, vous devez prendre des mesures adéquates pour vous assurer que les fonctions de service pendant le basculement des services dont elle dépend. Pour plus d’informations sur la conception de solutions de reprise, voir [Conception de Solutions de Cloud pour reprise après sinistre à l’aide de Active Geo-réplication](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

## <a name="active-geo-replication-capabilities"></a>Fonctions de réplication Geo actives
La fonctionnalité de réplication de Geo Active fournit les fonctionnalités essentielles suivantes :

- **La réplication asynchrone automatique**: vous ne pouvez créer une base de données secondaire en ajoutant une base de données existante. L’image secondaire peut être créé uniquement dans un autre serveur de base de données SQL Azure. Une fois créé, la base de données secondaire est remplie avec les données copiées à partir de la base de données principale. Ce processus est appelé amorçage. Une fois que la base de données secondaire a été créé et mises en culture, mises à jour de la base de données principale sont asynchrone automatiquement répliquées sur la base de données secondaire. Réplication asynchrone signifie que les transactions sont validées dans la base de données principale avant qu’ils sont répliquées vers la base de données secondaire. 

- **Plusieurs bases de données secondaires**: deux ou plusieurs bases de données secondaires augmentent redondance et le niveau de protection de la base de données principale et l’application. Si plusieurs bases de données secondaires existent, l’application reste protégée, même si une base de données secondaires échoue. Si une base de données secondaire et que celui-ci échoue, l’application est exposée à un risque plus élevé jusqu'à ce qu’une nouvelle base de données secondaire est créé.

- **Bases de données secondaires lisibles**: une application peut accéder à une base de données secondaire pour les opérations en lecture seule à l’aide d’entités de sécurité identiques ou différents utilisées pour accéder à la base de données principale. Les bases de données secondaires faire fonctionner en mode d’isolement instantané pour vérifier les mises à jour principale (relecture du journal) de la réplication n’est pas retardée par des requêtes exécutées sur le moniteur secondaire.

>[AZURE.NOTE] La relecture du journal est différée sur la base de données secondaire si schéma des mises à jour qu’il reçoit du site principal qui nécessitent un verrou de schéma sur la base de données secondaire.

- **Réplication geo active des bases de données élastique pool**: Geo-réplication Active peut être configurée pour une base de données dans n’importe quel pool élastique de base de données. La base de données secondaire peut se trouver dans un autre pool élastique de base de données. Pour les bases de données normales, le moniteur secondaire peut être une base de données élastique pool et vice versa dans la mesure où les niveaux de service sont identiques. 

- **Niveau de performance configurable de la base de données secondaire**: une base de données secondaire peut être créé avec le niveau de performances inférieur à principal. Bases de données principales et secondaires doivent avoir le même niveau de service. Cette option n’est pas recommandée pour les applications avec une activité écriture de base de données haute, car le décalage de réplication accrue accroît le risque de perte de données significative après un basculement. En outre, après le basculement de l’application sont impact sur les performances jusqu'à ce que la nouvelle primaire est mis à niveau vers un niveau de performances plus élevé. Le graphique de pourcentage journal IO sur portail Azure fournit un bon moyen d’estimer le niveau de performance minimal secondaire nécessaire pour soutenir la charge de réplication de. Par exemple, si votre base de données principale est P6 (1000 DTU) et son journal pour cent IO est 50 % secondaire doit être au moins P4 (DTU 500). Vous pouvez également récupérer les données du journal IO à l’aide des vues de base de données [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) ou [sys.dm_db_resource_stats]( https://msdn.microsoft.com/library/dn800981.aspx) .  Pour plus d’informations sur les niveaux de performances de base de données SQL, consultez [performances et options de base de données SQL](sql-database-service-tiers.md). 

- **Retour arrière et basculement contrôlée par l’utilisateur**: une base de données secondaire peut explicitement changé au rôle principal à tout moment par l’application ou l’utilisateur. Durant une panne réelle l’option « non planifiée » doit servir, qui promeut immédiatement secondaire à être le serveur principal. Lorsque le principal échec récupère et redevient disponible, le système marque principale récupérée comme secondaire et mettre à jour avec la nouvelle primaire automatiquement. En raison de la nature asynchrone de réplication, une petite quantité de données peut être perdue lors de basculement non planifié si un serveur principal échoue avant qu’il réplique les dernières modifications apportées à l’image secondaire. Lorsqu’un serveur principal avec plusieurs secondaires bascule, le système automatiquement reconfigure les relations de réplication et lie les secondaires restants à primaire récemment promu sans aucune intervention utilisateur. Après que l’interruption qui a provoqué le basculement est réduite, il peut être préférable de renvoyer l’application à la zone principale. Pour ce faire, la commande basculement doit être appelée avec l’option « planifiée ». 

- **Tout en conservant les informations d’identification et les règles de pare-feu synchronisés**: nous vous recommandons d’à l’aide de [règles de pare-feu de base de données](sql-database-firewall-configure.md) pour répliquées geo bases de données auquel cas ces règles peut être répliquée avec la base de données pour s’assurer que toutes les bases de données secondaires les mêmes règles de pare-feu que le serveur principal. Cette approche évite permettant aux clients manuellement configurer et gérer les règles de pare-feu sur les serveurs hébergeant à la fois les bases de données principales et secondaires. De même, à l’aide de [contenus aux utilisateurs de base de données](sql-database-manage-logins.md) pour l’accès aux données permet de bases de données principales et secondaires toujours ont le même utilisateur les informations d’identification, lors d’un basculement, il n’existe aucune interruptions en raison des erreurs de correspondance avec les noms d’accès et les mots de passe. Avec l’ajout [d’Azure Active Directory](../active-directory/active-directory-whatis.md), les clients peuvent gérer l’accès utilisateur aux bases de données principales et secondaires et en supprimant la nécessité de gestion des informations d’identification dans totalement de bases de données.

## <a name="upgrading-or-downgrading-a-primary-database"></a>La mise à niveau ou mise à niveau une base de données principale
Vous pouvez mettre à niveau ou mettre à niveau une base de données principale à un niveau de performance (dans le même niveau de service) sans se déconnecter des bases de données secondaires. Lorsque la mise à niveau, nous vous recommandons que vous mettre à niveau la base de données secondaire tout d’abord, puis mettre à niveau le serveur principal. Lors de la mise à niveau, inverser l’ordre : tout d’abord mettre à niveau le serveur principal et puis mettre à niveau le moniteur secondaire. 

La base de données secondaire doit être placé dans le même niveau de service en tant que le système principal, afin de migrer votre base de données principale vers un niveau de service différent, vous devez terminer le lien geo réplication et renommez la base de données secondaire ou simplement le faire glisser. Migrer le serveur principal vers le nouveau niveau de service, puis reconfigurer geo réplication. Votre nouvelle secondaire est créé automatiquement avec le même niveau de performance que le principal par défaut.

## <a name="preventing-the-loss-of-critical-data"></a>Éviter toute perte de données critiques
En raison de la latence élevée de réseaux étendus, copie en continu utilise un mécanisme de réplication asynchrone. Réplication asynchrone effectue une perte de données impérative si une erreur se produit. Toutefois, certaines applications peuvent nécessiter sans perte de données. Pour protéger ces mises à jour critiques, un développeur de l’application pouvez appeler la procédure système [sp_wait_for_database_copy_sync](https://msdn.microsoft.com/library/dn467644.aspx) immédiatement après validation de la transaction. L’appel est bloqué **sp_wait_for_database_copy_sync** le thread appelant jusqu'à ce que la dernière transaction validée a été répliquée sur la base de données secondaire. La procédure attend jusqu'à ce que toutes les transactions en file d’attente ont été reconnues par la base de données secondaire. **sp_wait_for_database_copy_sync** limitée à un lien de copie continue spécifique. Tout utilisateur possédant les droits de connexion à la base de données principale peut appeler cette procédure.

>[AZURE.NOTE] Le retard due à un appel de procédure **sp_wait_for_database_copy_sync** peut être significatif. Le délai dépend de la taille de la longueur de journal des transactions pour l’instant et cet appel ne retourne pas jusqu'à ce que la totalité du journal est répliqué. Évitez d’appeler cette procédure, sauf si cela est absolument nécessaire.

## <a name="programmatically-managing-active-geo-replication"></a>Gestion par programme de la réplication Geo Active

Comme indiqué précédemment, les Geo-réplication Active peut également gérée par programme à l’aide de PowerShell Azure et l’API REST. Les tableaux suivants décrivent l’ensemble des commandes disponibles.

- **API du Gestionnaire de ressources Azure et la sécurité basée sur les rôles**: Geo-réplication Active inclut un ensemble [d’Interfaces API Azure Gestionnaire de ressources]( https://msdn.microsoft.com/library/azure/mt163571.aspx) pour la gestion, y compris les [applets de commande PowerShell basée sur le Gestionnaire de ressources Azure](sql-database-geo-replication-powershell.md). Ces API exige l’utilisation des groupes de ressources et prend en charge de la sécurité basée sur les rôles (RBAC). Pour plus d’informations sur la façon de mettre en œuvre access rôles voir [Contrôle d’accès Azure Role-Based](../active-directory/role-based-access-control-configure.md).

>[AZURE.NOTE] Plusieurs nouvelles fonctionnalités de Geo-réplication Active uniquement prises en charge à l’aide de [Gestionnaire de ressources Azure](../azure-resource-manager/resource-group-overview.md) en fonction de [L’API REST de SQL Azure](https://msdn.microsoft.com/library/azure/mt163571.aspx) et les [applets de commande PowerShell de base de données SQL Azure](https://msdn.microsoft.com/library/azure/mt574084.aspx). L’API REST (classique)] (https://msdn.microsoft.com/library/azure/dn505719.aspx) et les [applets de commande (classique) de base de données SQL Azure](https://msdn.microsoft.com/library/azure/dn546723.aspx) étant pris en charge pour la compatibilité descendante à l’aide de l’API basées sur le Gestionnaire de ressources Azure sont recommandés. 


### <a name="transact-sql"></a>Transact-SQL

|Commande|Description|
|-------|-----------|
|[MODIFIER la base de données (base de données SQL Azure)](https://msdn.microsoft.com/library/mt574871.aspx)|Argument d’ajouter un serveur secondaire ON permet de créer une base de données secondaire pour la réplication de données démarre et une base de données existante|
|[MODIFIER la base de données (base de données SQL Azure)](https://msdn.microsoft.com/library/mt574871.aspx)|Permet de basculer d’une base de données secondaire pour être principal pour déclencher le basculement basculement ou FORCE_FAILOVER_ALLOW_DATA_LOSS
|[MODIFIER la base de données (base de données SQL Azure)](https://msdn.microsoft.com/library/mt574871.aspx)|SUPPRIMER le serveur secondaire ON permet de mettre fin à une réplication des données entre une base de données SQL et la base de données secondaire spécifiée.|
|[Sys.geo_replication_links (de base de données SQL Azure)](https://msdn.microsoft.com/library/mt575501.aspx)|Renvoie des informations sur tous les liens de réplication existants pour chaque base de données sur le serveur logique de base de données SQL Azure.|
|[Sys.dm_geo_replication_link_status (de base de données SQL Azure)](https://msdn.microsoft.com/library/mt575504.aspx)|Obtient la dernière date de réplication, dernière décalage de réplication et autres informations sur le lien de réplication pour une base de données SQL donnée.|
|[Sys.dm_operation_status (de base de données SQL Azure)](https://msdn.microsoft.com/library/dn270022.aspx)|Affiche l’état pour toutes les opérations de base de données, y compris l’état des liens de réplication.|
|[sp_wait_for_database_copy_sync (de base de données SQL Azure)](https://msdn.microsoft.com/library/dn467644.aspx)|entraîne l’application patienter jusqu'à ce que toutes les transactions validées sont répliquées et acceptées par la base de données secondaire active.|
||||

### <a name="powershell"></a>PowerShell

|Applet de commande|Description|
|------|-----------|
|[Get-AzureRmSqlDatabase](https://msdn.microsoft.com/en-us/library/azure/mt603648.aspx)|Obtient un ou plusieurs bases de données.|
|[Nouvelle AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt603689.aspx)|Crée une base de données secondaire pour une base de données existante et démarre la réplication de données.|
|[Jeu de AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/en-us/library/mt619393.aspx)|Bascule une base de données secondaire pour être principal pour déclencher le basculement.|
|[Supprimer AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/en-us/library/mt603457.aspx)|Met fin à la réplication des données entre une base de données SQL et la base de données secondaire spécifiée.|
|[Get-AzureRmSqlDatabaseReplicationLink](https://msdn.microsoft.com/library/mt619330.aspx)|Obtient les liens geo réplication entre une base de données SQL Azure et un groupe de ressources ou SQL Server.|
||||

### <a name="rest-api"></a>API REST

|API|Description|
|---|-----------|
|[Créer ou mettre à jour la base de données (mode de création = restaurer)](https://msdn.microsoft.com/library/azure/mt163685.aspx)|Crée, met à jour ou restaure un serveur principal ou une base de données secondaire.|
|[Obtenir créer ou mettre à jour d’état de base de données](https://msdn.microsoft.com/library/azure/mt643934.aspx)|Renvoie l’état pendant une opération de création.|
|[Définir la base de données secondaire en principal (basculement planifié)](https://msdn.microsoft.com/ibrary/azure/mt575007.aspx)|Promouvoir une base de données secondaire dans un partenariat Geo réplication pour devenir la nouvelle base de données principale.|
|[Définir la base de données secondaire en principal (basculement non planifié)](https://msdn.microsoft.com/library/azure/mt582027.aspx)|Pour forcer un basculement de la base de données secondaire et définir l’image secondaire en tant que le serveur principal.|
|[Obtenir des liens de réplication](https://msdn.microsoft.com/library/azure/mt600929.aspx)|Obtient tous les liens de réplication pour une base de données SQL donnée dans un partenariat geo réplication. Il extrait les informations visibles dans la vue de catalogue sys.geo_replication_links.|
|[Obtenir le lien de réplication](https://msdn.microsoft.com/library/azure/mt600778.aspx)|Obtient un lien de réplication spécifique pour une base de données SQL donnée dans un partenariat geo réplication. Il extrait les informations visibles dans la vue de catalogue sys.geo_replication_links.|
||||



## <a name="next-steps"></a>Étapes suivantes

- Pour une vue d’ensemble de la continuité de gestion et scénarios, voir [vue d’ensemble de la continuité de gestion](sql-database-business-continuity.md)
- Pour en savoir plus sur les sauvegardes de base de données SQL Azure automatisé, voir [sauvegardes automatisées de base de données SQL](sql-database-automated-backups.md).
- Pour en savoir plus sur l’utilisation des sauvegardes automatisées pour la récupération, voir [restaurer une base de données à partir des sauvegardes exécutée par le service](sql-database-recovery-using-backups.md).
- Pour en savoir plus sur l’utilisation des sauvegardes automatisées d’archivage, voir [Copier de base de données](sql-database-copy.md).
- Pour en savoir plus sur les conditions d’authentification pour un nouveau serveur principal et la base de données, consultez [sécurité de base de données SQL après sinistre](sql-database-geo-replication-security-config.md).
