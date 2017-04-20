<properties
    pageTitle="Configurer la Geo-réplication de base de données SQL Azure avec Transact-SQL | Microsoft Azure"
    description="Configurer la Geo-réplication de base de données SQL Azure à l’aide de Transact-SQL"
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
    ms.workload="NA"
    ms.date="10/13/2016"
    ms.author="carlrab"/>

# <a name="configure-geo-replication-for-azure-sql-database-with-transact-sql"></a>Configurer la Geo-réplication de base de données SQL Azure avec Transact-SQL

> [AZURE.SELECTOR]
- [Vue d’ensemble](sql-database-geo-replication-overview.md)
- [Portail Azure](sql-database-geo-replication-portal.md)
- [PowerShell](sql-database-geo-replication-powershell.md)
- [T-SQL](sql-database-geo-replication-transact-sql.md)

Cet article vous explique comment configurer la réplication Geo Active pour une base de données SQL Azure avec Transact-SQL.

Pour déclencher le basculement à l’aide de Transact-SQL, consultez [Démarrer un basculement planifié ou de base de données SQL Azure avec Transact-SQL](sql-database-geo-replication-failover-transact-sql.md).

>[AZURE.NOTE] Réplication Geo active (lisibles secondaires) est désormais disponible pour toutes les bases de données de tous les niveaux de service. Dans avril 2017 le type secondaire illisible sera être déclassé et bases de données non lisible existantes passeront automatiquement vers secondaires lisibles.

Pour configurer la réplication Geo Active à l’aide de Transact-SQL, vous devez les éléments suivants :

- Un abonnement Azure.
- Un serveur de base de données SQL Azure logique <MyLocalServer> et une base de données SQL <MyDB> -la base de données principale que vous souhaitez répliquer.
- Une ou plusieurs base de données SQL Azure serveurs logiques < MySecondaryServer(n) > - les serveurs logiques qui seront les serveurs partenaire dans laquelle vous créerez des bases de données secondaires.
- Une connexion qui est DBManager sur le serveur principal, ont db_ownership de la base de données locale que vous allez geo-réplication, et être DBManager sur les serveurs partenaire à laquelle vous allez configurer la réplication de Geo.
- SQL Server Management Studio (SSMS)

> [AZURE.IMPORTANT] Il est recommandé d’utiliser toujours la dernière version de Management Studio doit pour rester synchronisé avec les mises à jour Microsoft Azure et base de données SQL. [Mettre à jour de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).


## <a name="add-secondary-database"></a>Ajouter une base de données secondaire

Vous pouvez utiliser l’instruction **ALTER DATABASE** pour créer une base de données secondaire répliquée geo sur un serveur partenaire. Vous exécutez cette instruction sur la base de données principale du serveur contenant la base de données doivent être répliquées. La base de données répliquée geo (le « base de données principale ») a le même nom que la base de données répliquée et créera par défaut, le même niveau de service en tant que la base de données principale. La base de données secondaire peut être lisible ou non lisible et peut être une seule base de données ou un databbase élastique. Pour plus d’informations, voir [Modifier une base de données (Transact-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) et les [Niveaux de Service](sql-database-service-tiers.md).
Une fois que la base de données secondaire est créé et mises en culture, données commenceront réplication asynchrone à partir de la base de données principale. Les étapes suivantes décrivent comment configurer la réplication de Geo à l’aide de Management Studio. Étapes de création non lisible et compréhensible secondaires, avec une seule base de données ou une base de données élastique, sont fournis.

> [AZURE.NOTE] Si une base de données existe sur le serveur partenaire avec le même nom que la base de données principale, que la commande échouera.


### <a name="add-non-readable-secondary-single-database"></a>Ajouter illisible secondaire (base de données unique)

Procédez comme suit pour créer un secondaire non lisible comme une seule base de données.

1. À l’aide de la version 13.0.600.65 ou version ultérieure de SQL Server Management Studio.

     > [AZURE.IMPORTANT] Téléchargez la [dernière](https://msdn.microsoft.com/library/mt238290.aspx) version de SQL Server Management Studio. Il est recommandé d’utiliser toujours la dernière version de Management Studio doit pour rester synchronisé avec les mises à jour au portail Azure.


2. Ouvrez le dossier de bases de données, développez le dossier **Bases de données système** , avec le bouton droit sur **maître**, puis cliquez sur **Nouvelle requête**.

3. Utilisez l’instruction **ALTER DATABASE** suivante pour convertir une base de données locale en une réplication Geo primaire avec une base de données secondaire illisible sur MySecondaryServer1 où MySecondaryServer1 est le nom de votre serveur convivial.

        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer1> WITH (ALLOW_CONNECTIONS = NO);

4. Cliquez sur **exécuter** pour exécuter la requête.


### <a name="add-readable-secondary-single-database"></a>Ajouter lisible secondaire (base de données unique)
Procédez comme suit pour créer un secondaire lisible comme une seule base de données.

1. Dans Management Studio, connectez-vous à votre serveur logique de base de données SQL Azure.

2. Ouvrez le dossier de bases de données, développez le dossier **Bases de données système** , avec le bouton droit sur **maître**, puis cliquez sur **Nouvelle requête**.

3. Utilisez l’instruction **ALTER DATABASE** suivante pour convertir une base de données locale en une réplication Geo primaire avec une base de données secondaire lisible sur un serveur secondaire.

        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer2> WITH (ALLOW_CONNECTIONS = ALL);

4. Cliquez sur **exécuter** pour exécuter la requête.



### <a name="add-non-readable-secondary-elastic-database"></a>Ajouter illisible secondaire (base de données élastique)

La procédure suivante permet de créer secondaire non lisible comme une base de données élastique.

1. Dans Management Studio, connectez-vous à votre serveur logique de base de données SQL Azure.

2. Ouvrez le dossier de bases de données, développez le dossier **Bases de données système** , avec le bouton droit sur **maître**, puis cliquez sur **Nouvelle requête**.

3. Utilisez l’instruction **ALTER DATABASE** suivante pour convertir une base de données locale en une réplication Geo primaire avec une base de données secondaire illisible sur un serveur secondaire dans un pool élastique.

        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer3> WITH (ALLOW_CONNECTIONS = NO
           , SERVICE_OBJECTIVE = ELASTIC_POOL (name = MyElasticPool1));

4. Cliquez sur **exécuter** pour exécuter la requête.



### <a name="add-readable-secondary-elastic-database"></a>Ajouter lisible secondaire (base de données élastique)
Utilisez les étapes suivantes pour créer secondaire lisible comme une base de données élastique.

1. Dans Management Studio, connectez-vous à votre serveur logique de base de données SQL Azure.

2. Ouvrez le dossier de bases de données, développez le dossier **Bases de données système** , avec le bouton droit sur **maître**, puis cliquez sur **Nouvelle requête**.

3. Utilisez l’instruction **ALTER DATABASE** suivante pour convertir une base de données locale en une réplication Geo primaire avec une base de données secondaire lisible sur un serveur secondaire dans un pool élastique.

        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer4> WITH (ALLOW_CONNECTIONS = ALL
           , SERVICE_OBJECTIVE = ELASTIC_POOL (name = MyElasticPool2));

4. Cliquez sur **exécuter** pour exécuter la requête.



## <a name="remove-secondary-database"></a>Supprimer la base de données secondaire

Vous pouvez utiliser l’instruction **ALTER DATABASE** pour terminer définitivement le partenariat de réplication entre une base de données secondaire et principal. Cette instruction est exécutée sur la base de données maître sur lequel se trouve la base de données principale. À l’issue de la relation, la base de données secondaire devient une base de données en lecture / écriture normal. Si la connectivité à une base de données secondaire a provoqué la commande a réussi, mais le moniteur secondaire devient en lecture / écriture après restauration de connectivité. Pour plus d’informations, voir [Modifier une base de données (Transact-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) et les [Niveaux de Service](sql-database-service-tiers.md).

Procédez comme suit pour supprimer répliquées geo secondaire d’un partenariat Geo réplication.

1. Dans Management Studio, connectez-vous à votre serveur logique de base de données SQL Azure.

2. Ouvrez le dossier de bases de données, développez le dossier **Bases de données système** , avec le bouton droit sur **maître**, puis cliquez sur **Nouvelle requête**.

3. Utilisez l’instruction **ALTER DATABASE** suivante pour supprimer un secondaire répliquées geo.

        ALTER DATABASE <MyDB>
           REMOVE SECONDARY ON SERVER <MySecondaryServer1>;

4. Cliquez sur **exécuter** pour exécuter la requête.

## <a name="monitor-geo-replication-configuration-and-health"></a>État et la configuration de la réplication de Geo moniteur

Tâches de surveillance incluent analyse du fonctionnement de la réplication de données et de contrôle de la configuration de la réplication Geo.  Vous pouvez utiliser la vue de gestion dynamique **sys.dm_geo_replication_links** dans la base de données principale pour renvoyer des informations sur tous les liens de réplication de sortie pour chaque base de données sur le serveur logique de base de données SQL Azure. Cet affichage contient une ligne pour chaque du lien de réplication entre les bases de données principales et secondaires. Vous pouvez utiliser la vue de gestion dynamique **sys.dm_replication_link_status** pour renvoyer une ligne pour chaque base de données SQL Azure qui est en cours un lien de réplication réplication. Cela inclut les bases de données principales et secondaires. S’il existe plus d’un lien de réplication continue pour une base de données principale donnée, cette table contient une ligne pour chacun des relations. L’affichage est créé dans toutes les bases de données, y compris le maître logique. Toutefois, interroger cet affichage dans le masque des logiques renvoie un jeu vide. Vous pouvez utiliser la vue de gestion dynamique **sys.dm_operation_status** pour afficher l’état de toutes les opérations de base de données, y compris l’état des liens de réplication. Pour plus d’informations, voir [sys.geo_replication_links (de base de données SQL Azure)](https://msdn.microsoft.com/library/mt575501.aspx), [sys.dm_geo_replication_link_status (de base de données SQL Azure)](https://msdn.microsoft.com/library/mt575504.aspx)et [sys.dm_operation_status (de base de données SQL Azure)](https://msdn.microsoft.com/library/dn270022.aspx).

Procédez comme suit pour surveiller un partenariat Geo réplication.

1. Dans Management Studio, connectez-vous à votre serveur logique de base de données SQL Azure.

2. Ouvrez le dossier de bases de données, développez le dossier **Bases de données système** , avec le bouton droit sur **maître**, puis cliquez sur **Nouvelle requête**.

3. Utilisez l’instruction suivante pour afficher toutes les bases de données avec des liens de réplication Geo.

        SELECT database_id, start_date, modify_date, partner_server, partner_database, replication_state_desc, role, secondary_allow_connections_desc FROM [sys].geo_replication_links;

4. Cliquez sur **exécuter** pour exécuter la requête.
5. Ouvrez le dossier de bases de données, développez le dossier **Bases de données système** , avec le bouton droit sur **MyDB**, puis cliquez sur **Nouvelle requête**.
6. L’instruction suivante permet de représenter les retards de réplication et la dernière réplication de mes bases de données secondaires de MyDB.

        SELECT link_guid, partner_server, last_replication, replication_lag_sec FROM sys.dm_geo_replication_link_status

7. Cliquez sur **exécuter** pour exécuter la requête.
8. L’instruction suivante permet de représenter les plus récentes opérations geo réplication associées à la base de données MyDB.

        SELECT * FROM sys.dm_operation_status where major_resource_id = 'MyDB'
        ORDER BY start_time DESC

9. Cliquez sur **exécuter** pour exécuter la requête.

## <a name="upgrade-a-non-readable-secondary-to-readable"></a>Mise à niveau secondaire non lisible à lisible

Dans avril 2017 le type secondaire illisible sera être déclassé et bases de données non lisible existantes passeront automatiquement vers secondaires lisibles. Si vous utilisez secondaires illisible aujourd'hui et que vous souhaitez mettre à niveau pour être lu, vous pouvez utiliser les étapes suivantes pour chaque moniteur secondaire.

> [AZURE.IMPORTANT] Il n’existe aucune méthode libre-service de mise à niveau en place de secondaire non lisible à lisible. Si vous supprimez votre uniquement secondaire, puis la base de données principale restera s’affiche-t-il jusqu'à ce que la nouvelle secondaire est intégralement synchronisée. Si votre application SLA requiert que le serveur principal est toujours protégé, vous devez envisager de créer secondaire en parallèle dans un autre serveur avant d’appliquer la mise à niveau procédure ci-dessus. Remarque chaque principal peut contenir jusqu'à 4 bases de données secondaires.


1. Tout d’abord, connectez-vous au serveur *secondaire* et abandonner la base de données secondaire non lisible :  
        
        DROP DATABASE <MyNonReadableSecondaryDB>;

2. Maintenant se connecter au serveur *principal* et ajouter un nouvelle secondaire lisible

        ALTER DATABASE <MyDB>
            ADD SECONDARY ON SERVER <MySecondaryServer> WITH (ALLOW_CONNECTIONS = ALL);




## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur Geo-réplication Active, consultez - [Geo-réplication](sql-database-geo-replication-overview.md) Active
- Pour une vue d’ensemble de la continuité de gestion et scénarios, voir [vue d’ensemble de la continuité de gestion](sql-database-business-continuity.md)
