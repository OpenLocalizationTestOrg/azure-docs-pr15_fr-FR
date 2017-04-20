<properties
    pageTitle="Toutes les rubriques de service de base de données SQL | Microsoft Azure"
    description="Table de toutes les rubriques pour le service Azure nommée base de données SQL qui existent sur http://azure.microsoft.com/documentation/articles/, titre et description."
    services="sql-database"
    documentationCenter=""
    authors="MightyPen"
    manager="jhubbard"
    editor="MightyPen"/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/05/2016"
    ms.author="genemi"/>


# <a name="all-topics-for-azure-sql-database-service"></a>Toutes les rubriques de service de base de données SQL Azure

Cette rubrique répertorie toutes les rubriques qui s’applique directement au service de **Base de données SQL** d’Azure. Vous pouvez rechercher cette page Web pour les mots clés à l’aide de **Ctrl + F**, pour trouver les sujets qui intéressent.




## <a name="new"></a>Nouveau

| &nbsp; | Titre | Description |
| --: | :-- | :-- |
| 1 | [Daxko/CSI utilisées Azure afin d’accélérer son cycle de développement et améliorer ses services client et les performances](sql-database-implementation-daxko.md) | Découvrez comment Daxko/CSI utilise la base de données SQL pour accélérer son cycle de développement et améliorer ses services client et les performances |
| 2 | [Azure donne envergure GEP et gagner du temps](sql-database-implementation-gep.md) | Découvrez comment GEP utilise la base de données SQL pour atteindre les clients plus globales et plus efficaces |
| 3 | [Avec Azure, SnelStart a développé rapidement ses services professionnels à un rythme de 1 000 nouvelles Azure SQL bases de données par mois](sql-database-implementation-snelstart.md) | Découvrez comment SnelStart utilise la base de données SQL pour rapidement développé ses services professionnels à un rythme de 1 000 nouvelles Azure SQL bases de données par mois |
| 4 | [Umbraco utilise la base de données SQL Azure pour rapidement des services de mise en service et les proportions des milliers de clients dans le cloud](sql-database-implementation-umbraco.md) | Découvrez comment Umbraco utilise la base de données SQL pour rapidement la mise en service et services d’échelle pour des milliers de clients dans le cloud |
| 5 | [Gérer la base de données SQL Azure avec PowerShell](sql-database-manage-powershell.md) | Gestion de la base de données SQL Azure avec PowerShell. |
| 6 | [Prise en charge SSMS pour l’authentification Multifacteur d’Azure AD avec la base de données SQL et SQL Data Warehouse](sql-database-ssms-mfa-authentication.md) | Utiliser l’authentification Multi-prises en charge avec SSMS pour base de données SQL et SQL Data Warehouse. |
| 7 | [Expliquant élastique unités de Transaction de base de données (eDTUs) et les unités de Transaction de base de données (DTUs)](sql-database-what-is-a-dtu.md) | Unité de transaction comprendre quels une Azure SQL base de données est. |


## <a name="updated-articles-sql-database"></a>Articles mis à jour, base de données SQL

Cette section répertorie les articles qui ont été mis à jour récemment, où la mise à jour a été grandes ou significative. Pour chaque article mis à jour, un extrait de code approximative du texte ajouté démarque est affichée. Les articles ont été mis à jour dans la plage de dates de **2016-08-22** à **2016-10-05**.

| &nbsp; | Article | Texte mis à jour, l’extrait de code | Mise à jour lorsque |
| --: | :-- | :-- | :-- |
| 8 | [Gérer la base de données SQL Azure avec PowerShell](sql-database-command-line-tools.md) | Créer un groupe de ressources pour notre base de données SQL et les ressources connexes Azure avec l’applet de commande New-AzureRmResourceGroup (https://msdn.microsoft.com/library/azure/mt759837.aspx). *$resourceGroupName = « resourcegroup1 » $resourceGroupLocation = « northcentralus « nouveau AzureRmResourceGroup-nommer $resourceGroupName-emplacement $resourceGroupLocation* Pour plus d’informations, voir Utilisation de PowerShell Azure avec le Gestionnaire de ressources Azure (… / powershell-azure-ressource-manager.md). Pour un exemple de script, voir créer une base de données SQL script PowerShell (sql-base de données-get-mise en route-powershell.md create-a-sql-database-powershell-script). **Créer une base de données SQL server** Créer un serveur de base de données SQL avec l’applet de commande New-AzureRmSqlServer (https://msdn.microsoft.com/library/azure/mt603715.aspx). Remplacez *server1* par le nom de votre serveur. Nom du serveur doit être unique sur tous les serveurs de base de données SQL Azure. Si le nom du serveur est déjà effectuée, vous obtenez une erreur. Cette commande peut prendre plusieurs minutes. La resou | 2016-09-14 |
| 9 | [Copier une base de données SQL Azure à l’aide de PowerShell](sql-database-copy-powershell.md) |   SQL de base de données source (les fichiers de base de données pour copier)--$sourceDbName = « 1 » $sourceDbServerName = « Serveur1 » $sourceDbResourceGroupName = copie de base de données SQL « rg1 » (la nouvelle base de données doit être créé)--$copyDbName = « db1_copy » $copyDbServerName = « server2 » $copyDbResourceGroupName = « rg2 » copier une base de données sur le même serveur--New-AzureRmSqlDatabaseCopy - ResourceGroupName $sourceDbResourceGroupName - nom du serveur $sourceDbServerName - DatabaseName $sourceDbName - CopyDatabaseName $copyDbName copier une base de données vers un autre serveur--New-AzureRmSqlDatabaseCopy - ResourceGroupName $sourceDbResourceGroupName - nom du serveur $sourceDbServerName - DatabaseName $sourceDbName - CopyResourceGroupName $copyDbResourceGroupName - CopyServerName $copyDbServerName - CopyDatabaseName $copyDbName copiez une base de données dans un pool de base de données élastique--$poolName = « pool1 » New-AzureRmSqlDatabaseCopy - ResourceGroupName $ sourceDbResourceGroupName - nom du serveur $sourceDbServerName - DatabaseName $sourceDbName - CopyReso | 2016-09-08 |
| 10 | [Créer un pool de base de données élastique avec c#](sql-database-elastic-pool-create-csharp.md) |   Console.WriteLine (« serveur pare-feu... ») ;  FirewallRuleGetResponse fwr = CreateOrUpdateFirewallRule (_sqlMgmtClient, _resourceGroupName, _serverName, _firewallRuleName, _startIpAddress, _endIpAddress) ;  Console.WriteLine (« pare-feu Server : « + fwr. FirewallRule.Id) ;  Console.WriteLine (« élastique pool... ») ;  Epr ElasticPoolCreateOrUpdateResponse = CreateOrUpdateElasticDatabasePool (_sqlMgmtClient, _resourceGroupName, _serverName, _poolName, _poolEdition, _poolDtus, _databaseMinDtus, _databaseMaxDtus) ;  Console.WriteLine (« pool élastique : « + epr. ElasticPool.Id) ;  Console.WriteLine("Database...") ;  Dbr DatabaseCreateOrUpdateResponse = CreateOrUpdateDatabase (_sqlMgmtClient, _resourceGroupName, _serverName, _databaseName, _poolName) ;  Console.WriteLine (« base de données : « + dbr. Database.Id) ;  Console.WriteLine (« appuyez sur une touche pour continuer... ») ;  Console.ReadKey() ;  } statique ResourceGroup CreateOrUpdateResourceGroup(ResourceManagementClient resourceMgmtClient, string subscriptionId, string resourceGroupNa | 2016-09-14 |
| 11 | [Script PowerShell pour identifier les bases de données appropriés pour un pool élastique de base de données](sql-database-elastic-pool-database-assessment-powershell.md) | Candidats **pour élastique pool de base de données, nous exclure maître et les bases de données qui se trouvent déjà dans un pool. Vous pouvez ajouter plusieurs bases de données à la liste exclue ci-dessous selon vos besoins** $ListOfDBs = Get-AzureRmSqlDatabase - nom du serveur $servername. Split('.') 0 - ResourceGroupName $ResourceGroupName / Where-Object {$_. DatabaseName - aussitôt (« principale ») - et $_. CurrentServiceLevelObjectiveName - aussitôt (« ElasticPool »)- et $_. CurrentServiceObjectiveName - aussitôt (« ElasticPool »)} $outputConnectionString = « Source de données = $outputServerName ; sécurité intégrée = false ; Initial catalogue = $outputdatabaseName ; Id d’utilisateur = $outputDBUsername ; Mot de passe = $outputDBpassword « $destinationTableName = resource_stats_output » « **créer une table de base de données de sortie pour la collecte de métriques** $sql = » si NOT EXISTS (sélectionnez * à partir de sys.objects où object_id = OBJECT_ID(N'$($destinationTableName)') et tapez dans (N'U')) $($destinationTableName) début créer une Table (nom_base_de_données varchar (128), ralenti varchar (20), end_time date/heure, le flottement avg_cpu, Moy_ | 2016-09-29 |
| 12 | [Didacticiel de base de données SQL : créer une base de données SQL en minutes à l’aide du portail Azure](sql-database-get-started.md) |   ! Nouvelle base de données sql 1 (. / media/sql-database-get-started/sql-database-new-database-1.png) 3. Cliquez sur **Base de données SQL** pour ouvrir la carte de base de données SQL. Le contenu de cette carte varie selon le nombre de vos abonnements et vos objets existants (par exemple, les serveurs existants).  ! Nouvelle base de données sql 2 (. / media/sql-database-get-started/sql-database-new-database-2.png) 4. Dans la zone de texte **nom de la base de données** , attribuez un nom à votre première base de données - par exemple « ma base de données ». Une coche verte indique que vous avez fourni un nom valide.  ! Nouvelle base de données sql 3 (. / media/sql-database-get-started/sql-database-new-database-3.png) 5. Si vous avez plusieurs abonnements, sélectionnez un abonnement. 6. Sous le **groupe de ressources**, cliquez sur **Créer nouveau** et donnez un nom à votre groupe de ressources première - par exemple « Mes--groupe de ressources ». Une coche verte indique que vous avez fourni un nom valide.  ! Nouvelle base de données sql 4 (. / media/sql-database-get-started/sql-database-new-database-4.png) 7. Sous **Sélectionnez source* | 2016-09-08 |
| 13 | [Essayez de la base de données SQL : Utiliser c# pour créer une base de données SQL avec la bibliothèque de base de données SQL pour .NET](sql-database-get-started-csharp.md) | **Créer un service principal pour accéder aux ressources** Le script PowerShell suivant crée l’application Active Directory (AD) et l’entité de service que nous avons besoin de s’authentifier notre application C. Le script renvoie des valeurs qu'avons-nous besoin pour l’exemple précédent C. Pour plus d’informations, voir utiliser Azure PowerShell pour créer une entité de service d’accès aux ressources (… / ressource-groupe-authentifier-service-principal.md).  Se connecter à Azure.  AzureRmAccount ajouter si vous disposez de plusieurs abonnements, supprimez les commentaires et l’abonnement que vous voulez travailler avec la valeur.  $subscriptionId = « {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx} » Set-AzureRmContext - SubscriptionId $subscriptionId fournir ces valeurs pour votre nouvelle application AAD.  $appName est le nom complet de votre application, doit être unique dans votre annuaire.  $uri n’a pas besoin être un uri réel.  $secret est un mot de passe que vous créez.  $appName = « {-nom de l’application} » $uri = « http://{app-name »} $secret = « {application du mot de passe} » créer une application AAD $azureAdApplication = New-AzureRmADApp | 2016-09-23 |
| 14 | [Gestion des bases de données SQL Azure à l’aide du portail Azure](sql-database-manage-portal.md) | Pour afficher ou mettre à jour vos paramètres de base de données, cliquez sur le paramètre souhaité dans la carte de base de données SQL : ! Paramètres de base de données SQL (. / media/sql-database-manage-portal/settings.png) **comment rechercher un nom de serveur complet de bases de données SQL ?** Pour afficher le nom de votre serveur de bases de données, cliquez sur **vue d’ensemble** sur la carte de **base de données SQL** et notez le nom du serveur : ! Paramètres de base de données SQL (. / media/sql-database-manage-portal/server-name.png) **comment gérer les règles de pare-feu pour contrôler l’accès à mon SQL server et la base de données ?** Pour afficher, créer ou mettre à jour les règles de pare-feu, cliquez sur **définir pare-feu server** dans la carte de **base de données SQL** . Pour plus d’informations, voir Configurer une règle de pare-feu au niveau du serveur de base de données SQL Azure à l’aide du portail Azure (sql-base de données-configurer-pare-feu-settings.md). ! règles de pare-feu (. / media/sql-database-manage-portal/sql-database-firewall.png) **comment modifier mes SQL de base de données service niveau niveau de performance ou ?** Pour mettre à jour le niveau de service niveau ou les performances d’une base de données SQL, cliquez sur ** tarification couche (s | 20 / 09 / 2016 |





## <a name="get-started"></a>Prise en main

| &nbsp; | Titre | Description |
| --: | :-- | :-- |
| 15 | [Crée des applications clients à plusieurs avec la base de données SQL Azure avec isolement et l’efficacité](sql-database-build-multi-tenant-apps.md) | Découvrez comment base de données SQL crée des applications clients multiples |
| 16 | [Connectez-vous à la base de données SQL avec SQL Server Management Studio, puis exécutez un exemple de requête T-SQL](sql-database-connect-query-ssms.md) | Découvrez comment vous connecter à la base de données SQL Azure à l’aide de SQL Server Management Studio (SSMS). Ensuite, exécutez un exemple de requête à l’aide de Transact-SQL (T-SQL). |
| 17 | [Se connecter à la base de données SQL à l’aide de .NET (c#)](sql-database-develop-dotnet-simple.md) | Utilisez l’exemple de code dans cette rapide commencer à générer une application moderne avec c# et sauvegardée par une puissante base de données relationnel dans le cloud avec la base de données SQL Azure. |
| 18 | [Créer un nouveau pool de base de données élastique grâce au portail Azure](sql-database-elastic-pool-create-portal.md) | Comment ajouter un pool de base de données élastique scalable à votre configuration de base de données SQL pour faciliter leur administration et partage au sein de nombreuses bases de données des ressources. |
| 19 | [Explorer les didacticiels de base de données SQL Azure](sql-database-explore-tutorials.md) | Découvrez les fonctionnalités et les fonctionnalités de base de données SQL |
| 20 | [Didacticiel de base de données SQL : créer une base de données SQL en minutes à l’aide du portail Azure](sql-database-get-started.md) | Découvrez comment configurer un serveur logique de base de données SQL, serveur pare-feu règle, base de données SQL et exemples de données. En outre, découvrez comment vous connecter avec les outils clients, configurer des utilisateurs et configurer une règle de pare-feu de base de données. |
| 21 | [Base de données SQL Azure permet de sécuriser et protéger](sql-database-helps-secures-and-protects.md) | Découvrez comment la base de données SQL contribue à sécuriser et protéger |
| 22 | [Base de données SQL Azure apprend au fur &amp; s’adapte à celles-ci](sql-database-learn-and-adapt.md) | Découvrez comment la base de données SQL apprend au fur et s’adapte à celles-ci |
| 23 | [Choisissez un option de SQL Server cloud : base de données SQL Azure (PaaS) ou SQL Server sur Azure machines virtuelles (IaaS)](sql-database-paas-vs-sql-server-iaas.md) | En savoir quelle option de SQL Server cloud adaptée à votre application : base de données SQL Azure (PaaS) ou SQL Server dans le cloud Azure machines virtuelles en fonctionnement. |
| 24 | [Échelles de base de données SQL Azure à la volée](sql-database-scale-on-the-fly.md) | Découvrez comment la base de données SQL s’adapte à la volée |
| 25 | [Explorer les Démarrages rapides de Solution de base de données SQL Azure](sql-database-solution-quick-starts.md) | Découvrez les Solutions de base de données SQL Azure |
| 26 | [Base de données SQL Azure fonctionne dans votre environnement](sql-database-works-in-your-environment.md) | Découvrir comment base de données SQL permet, protège et protège |



## <a name="build-an-app"></a>Créer une application

| &nbsp; | Titre | Description |
| --: | :-- | :-- |
| 27 | [Résoudre les problèmes, diagnostiquer et d’éviter les erreurs de connexion SQL et erreurs transitoires pour la base de données SQL](sql-database-connectivity-issues.md) | Découvrez comment résoudre les problèmes, diagnostiquer et d’éviter une erreur de connexion SQL ou transitoires dans la base de données SQL Azure.  |
| 28 | [Se connecter à une base de données SQL avec Visual Studio](sql-database-connect-query.md) | Écrire un programme dans c# pour interroger et se connecter à la base de données SQL. Informations sur les adresses IP, chaînes de connexion, connexion sécurisée et Visual Studio gratuit. |
| 29 | [Ports au-delà 1433 pour ADO.NET 4.5 et V12 de base de données SQL](sql-database-develop-direct-route-ports-adonet-v12.md) | Connexions client ADO.NET vers V12 de base de données SQL Azure parfois ignorer le proxy et d’interagissent directement avec la base de données. Ports différent de 1433 sont importants. |
| 30 | [Codes d’erreur SQL pour les applications clientes de base de données SQL : erreur de connexion et d’autres problèmes de base de données](sql-database-develop-error-messages.md) | Découvrez les codes d’erreur SQL pour les applications clientes de base de données SQL, tels que les erreurs de connexion de base de données, des problèmes de copie de base de données et des erreurs générales.  |
| 31 | [Se connecter à la base de données SQL à l’aide de PHP sur Windows](sql-database-develop-php-simple.md) | Présente un exemple de programme PHP qui se connecte à la base de données SQL Azure à partir d’un client Windows et fournit des liens vers les composants logiciels nécessaires requises par le client. |
| 32 | [Essayez de la base de données SQL : Utiliser c# pour créer une base de données SQL avec la bibliothèque de base de données SQL pour .NET](sql-database-get-started-csharp.md) | Essayez de base de données SQL pour développer des applications SQL et c# et créer une base de données SQL Azure avec c# à l’aide de la bibliothèque de base de données SQL pour .NET. |
| 33 | [Mise en route des fonctionnalités JSON dans la base de données SQL Azure](sql-database-json-features.md) | Base de données SQL Azure vous permet d’analyse, requête et le format des données en notation Notation JSON (JavaScript Object). |
| 34 | [Résoudre les problèmes de connexion à la base de données SQL Azure](sql-database-troubleshoot-common-connection-issues.md) | Étapes pour identifier et résoudre les erreurs de connexion de base de données SQL Azure. |
| 35 | [Erreur « Base de données sur le serveur n’est pas disponible pour le moment » lors de la connexion à la base de données sql](sql-database-troubleshoot-connection.md) | Résoudre les problèmes de la base de données sur serveur n’est pas disponible actuellement erreur lorsqu’une application se connecte à la base de données SQL. |



## <a name="develop"></a>Développer

| &nbsp; | Titre | Description |
| --: | :-- | :-- |
| 36 | [Obtenir les valeurs requises pour l’authentification d’une application pour accéder aux base de données SQL à partir du code](sql-database-client-id-keys.md) | Créer une entité de service pour l’accès à la base de données SQL à partir de code. |
| 37 | [Se connecter à la base de données SQL à l’aide de Java avec JDBC sur Windows](sql-database-develop-java-simple.md) | Présente un exemple de code Java que vous pouvez utiliser pour vous connecter à la base de données SQL Azure. L’exemple utilise JDBC, et il s’exécute sur un ordinateur client Windows. |
| 38 | [Se connecter à la base de données SQL à l’aide de Node.js](sql-database-develop-nodejs-simple.md) | Présente un exemple de code Node.js que vous pouvez utiliser pour vous connecter à la base de données SQL Azure. |
| 39 | [Vue d’ensemble de développement de base de données SQL](sql-database-develop-overview.md) | En savoir plus sur les bibliothèques de connectivité disponibles et des pratiques recommandées pour les applications qui se connectent à la base de données SQL. |
| 40 | [Se connecter à la base de données SQL à l’aide de Python](sql-database-develop-python-simple.md) | Présente un exemple de code Python que vous pouvez utiliser pour vous connecter à la base de données SQL Azure. |
| 41 | [Se connecter à la base de données SQL à l’aide de Ruby](sql-database-develop-ruby-simple.md) | Donnez un exemple de code prononciation que vous pouvez exécuter pour vous connecter à la base de données SQL Azure. |
| 42 | [Prise en main des Tables temporelles dans la base de données SQL Azure](sql-database-temporal-tables.md) | Découvrez comment commencer à utiliser à l’aide de Tables temporelles dans la base de données SQL Azure. |



## <a name="performance-and-scale"></a>Performances et échelle

| &nbsp; | Titre | Description |
| --: | :-- | :-- |
| 43 | [Conseiller de base de données SQL](sql-database-advisor.md) | Le Gestionnaire de base de données SQL Azure fournit des recommandations pour vos bases de données SQL existant qui permet d’améliorer les performances des requêtes en cours. |
| 44 | [Conseiller de base de données SQL à l’aide du portail Azure](sql-database-advisor-portal.md) | Vous pouvez utiliser le Gestionnaire de base de données SQL Azure dans le portail Azure pour consulter et mettre en œuvre des recommandations pour vos bases de données SQL existant qui permet d’améliorer les performances des requêtes en cours. |
| 45 | [Vue d’ensemble de test d’évaluation de la base de données SQL Azure](sql-database-benchmark-overview.md) | Cette rubrique décrit la référence de base de données SQL Azure servi à mesurer les performances de base de données SQL Azure. |
| 46 | [Quand un pool élastique de base de données doit être utilisé ?](sql-database-elastic-pool-guidance.md) | Un pool élastique de base de données est un ensemble de ressources disponibles qui sont partagés par un groupe de bases de données élastiques. Ce document fournit des conseils pour vous aider à évaluer l’utilité de l’utilisation d’un pool élastique de base de données pour un groupe de bases de données. |
| 47 | [Prise en main des outils de base de données élastique](sql-database-elastic-scale-get-started.md) | Principes fondamentaux de fonctionnalité d’outils de base de données élastique de base de données SQL Azure, y compris facile pour exécuter l’exemple d’application. |
| 48 | [Forum aux questions sur base de données SQL](sql-database-faq.md) | Réponses aux clients de questions courantes demander de bases de données cloud et base de données SQL Azure, système de gestion de base de données relationnelle de Microsoft (SGBDR) et base de données en tant que service dans le cloud. |
| 49 | [Prise en main en mémoire (Preview) dans la base de données SQL](sql-database-in-memory.md) | Technologies SQL en mémoire améliorent considérablement les performances des transactions et les charges de travail analytique. Découvrez comment tirer parti de ces technologies. |
| 50 | [Utiliser en mémoire OLTP (preview) pour améliorer les performances de votre application dans la base de données SQL](sql-database-in-memory-oltp-migration.md) | Adopter en mémoire OLTP pour améliorer les performances de transactions dans une base de données SQL existante. |
| 51 | [Espace de stockage en mémoire OLTP moniteur](sql-database-in-memory-oltp-monitoring.md) | Estimation et moniteur stockage en mémoire XTP utilisent, capacité ; résoudre l’erreur capacité 41823 |
| 52 | [Fonctionnement du magasin de requête dans la base de données SQL Azure](sql-database-operate-query-store.md) | Découvrez comment utiliser le magasin de requête dans la base de données SQL Azure |
| 53 | [Performances un aperçu de la base de données SQL](sql-database-performance.md) | La base de données SQL Azure fournit des outils de performance pour vous aider à identifier les domaines qui peuvent d’améliorer les performances des requêtes en cours. |
| 54 | [Base de données SQL Azure et de performances des bases de données unique](sql-database-performance-guidance.md) | Cet article peut vous aider à déterminer le niveau de service à sélectionner pour votre application. Il recommande également des solutions d’optimisation de votre application pour tirer le meilleur parti de base de données SQL Azure. |
| 55 | [Aperçu de performances de requête de base de données SQL Azure](sql-database-query-performance.md) | Analyse des performances de requête identifie les requêtes d’utilisation du processeur la plupart pour une base de données SQL Azure. |
| 56 | [Modifier le niveau et les performances niveau de service (tarification couche) d’une base de données SQL](sql-database-scale-up.md) | Modifier le niveau de service et niveau de performance d’une base de données SQL Azure montre comment mettre à l’échelle de votre base de données SQL vers le haut ou vers le bas. Modifier le niveau de tarification d’une base de données SQL Azure. |
| 57 | [Analyse des performances de base de données dans la base de données SQL Azure](sql-database-single-database-monitor.md) | Découvrez les options pour l’analyse de votre base de données avec des outils Azure et vues de gestion dynamiques. |
| 58 | [Conseils de réglage de performances de base de données SQL](sql-database-troubleshoot-performance.md) | Conseils pour les performances de base de données SQL Azure par le biais d’évaluation et d’amélioration du produit. |
| 59 | [Comment utiliser le traitement par lots pour améliorer les performances de l’application de base de données SQL](sql-database-use-batching-to-improve-performance.md) | La rubrique fournit des preuves que le traitement par lots des opérations de base de données imroves considérablement la vitesse et extensibilité élevées de vos applications de base de données SQL Azure. Bien que ces techniques de traitement par lots fonctionnent pour les bases de données SQL Server, l’objectif de l’article se trouve sur Azure. |



## <a name="tools-for-scaling-out"></a>Outils pour l’évolution horizontale

| &nbsp; | Titre | Description |
| --: | :-- | :-- |
| 60 | [Créer des modèles pour les applications SaaS partagées et la base de données SQL Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md) | Cet article décrit la configuration requise et les modèles d’architecture données courantes de base de données partagé applications qui s’exécutent dans un environnement cloud devront prendre en considération et les compromis divers associés à ces modèles. Il également explique comment base de données SQL Azure, ses pools élastiques et outils élastiques, aider à répondre à ces exigences de manière sans compromis. |
| 61 | [Migrer des bases de données existantes à l’horizontale](sql-database-elastic-convert-to-use-elastic-tools.md) | Convertir des bases de données sharded pour utiliser les outils de base de données élastique en créant un éclater Gestionnaire de carte |
| 62 | [Création de bases de données scalable cloud](sql-database-elastic-database-client-library.md) | Créer des applications de base de données .NET scalable avec la bibliothèque de client de base de données élastique |
| 63 | [Gestionnaire de cartes compteurs de performance pour partagé](sql-database-elastic-database-perf-counters.md) | ShardMapManager classe et données dépendantes routage des compteurs de performance |
| 64 | [Ajout d’un partagé à l’aide des outils de base de données élastique](sql-database-elastic-scale-add-a-shard.md) | Comment utiliser élastique API échelle pour ajouter nouveau milieu des fragments à un éclater définie. |
| 65 | [Déployer un service de publipostage de fractionnement](sql-database-elastic-scale-configure-deploy-split-and-merge.md) | Le fractionnement et de la fusion avec les outils de base de données élastique |
| 66 | [Routage dépendant des données](sql-database-elastic-scale-data-dependent-routing.md) | L’utilisation de la classe ShardMapManager dans les applications .NET pour le routage, une fonctionnalité de base de données SQL Azure élastiques bases de données dépendant des données |
| 67 | [Outils de base de données élastique Forum aux questions](sql-database-elastic-scale-faq.md) | Forum aux Questions sur échelle élastique de base de données SQL Azure. |
| 68 | [Élastique glossaire d’outils de base de données](sql-database-elastic-scale-glossary.md) | Explication des termes utilisés pour les outils de base de données élastique |
| 69 | [Mise à l’échelle avec la base de données SQL Azure](sql-database-elastic-scale-introduction.md) | Logiciel en tant qu’un développeurs de Service (SaaS) pouvez facilement créer élastiques, scalable bases de données dans le cloud à l’aide de ces outils |
| 70 | [Informations d’identification utilisées pour accéder à la bibliothèque de client de base de données élastique](sql-database-elastic-scale-manage-credentials.md) | Comment définir le niveau d’informations d’identification, d’administration en lecture seule, pour les applications de base de données élastique |
| 71 | [Interrogation de plusieurs partagé](sql-database-elastic-scale-multishard-querying.md) | Exécuter des requêtes sur milieu des fragments à l’aide de la bibliothèque de client élastique de base de données. |
| 72 | [Déplacement de données entre les bases de données plus grande échelle cloud](sql-database-elastic-scale-overview-split-and-merge.md) | Explique comment manipuler milieu des fragments et déplacer des données via un service autonome à l’aide d’API élastique bases de données. |
| 73 | [Mettre à l’échelle des bases de données avec le Gestionnaire de carte partagé](sql-database-elastic-scale-shard-map-management.md) | Comment utiliser le ShardMapManager, une bibliothèque de base de données élastique client |
| 74 | [Configuration de la sécurité de fusion de fractionnement](sql-database-elastic-scale-split-merge-security-configuration.md) | Configurer la x409 certificats pour le chiffrement |
| 75 | [Mettre à niveau une application pour utiliser la dernière bibliothèque client élastique de base de données](sql-database-elastic-scale-upgrade-client-library.md) | Mettre à niveau des applications et des bibliothèques à l’aide de Nuget |
| 76 | [Bibliothèque de client de base de données élastique avec entité Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) | Utiliser une bibliothèque de client de base de données élastique et entité Framework pour le codage de bases de données |
| 77 | [À l’aide de la bibliothèque de client de base de données élastique Dapper](sql-database-elastic-scale-working-with-dapper.md) | Utilisation de bibliothèque de client de base de données élastique avec Dapper. |
| 78 | [Applications clients à plusieurs grâce aux outils de base de données élastique et sécurité au niveau de la ligne](sql-database-elastic-tools-multi-tenant-row-level-security.md) | Découvrez comment utiliser les outils de base de données élastique avec sécurité au niveau de la ligne pour créer une application avec un niveau de données extensibles hautement sur base de données SQL Azure qui prend en charge plusieurs clients milieu des fragments. |



## <a name="elastic-jobs"></a>Tâches élastiques

| &nbsp; | Titre | Description |
| --: | :-- | :-- |
| 79 | [Créer et gérer à l’échelle des bases de données SQL Azure à l’aide de travaux élastique (preview)](sql-database-elastic-jobs-create-and-manage.md) | Passez en revue la création et la gestion d’un travail élastique de base de données. |
| 80 | [Mise en route des tâches de base de données élastique](sql-database-elastic-jobs-getting-started.md) | comment utiliser les tâches de base de données élastique |
| 81 | [Gestion des bases de données plus grande échelle cloud](sql-database-elastic-jobs-overview.md) | Montre le service de tâches de base de données élastique |
| 82 | [Créer et gérer un travaux élastique de base de données SQL de base de données à l’aide de PowerShell (preview)](sql-database-elastic-jobs-powershell.md) | PowerShell utilisée pour gérer des pools de base de données SQL Azure |
| 83 | [L’installation de vue d’ensemble des tâches de base de données élastique](sql-database-elastic-jobs-service-installation.md) | Passez en revue l’installation de la fonctionnalité de travail élastique. |
| 84 | [Désinstaller les composants de tâches de base de données élastique](sql-database-elastic-jobs-uninstall.md) | Comment désinstaller l’outil de tâches de base de données élastique |



## <a name="elastic-pools"></a>Pools élastiques

| &nbsp; | Titre | Description |
| --: | :-- | :-- |
| 85 | [Qu’est un pool élastique Azure ?](sql-database-elastic-pool.md) | Gérer des centaines ou des milliers de bases de données à l’aide d’un pool. Un seul prix pour un ensemble d’unités de performances peut être distribué sur le pool. Déplacer des bases de données ou l’arrière au seront. |
| 86 | [Créer un pool de base de données élastique avec c#](sql-database-elastic-pool-create-csharp.md) | Utiliser des techniques de développement c# de base de données pour créer un pool élastique scalable de la base de données dans la base de données SQL Azure afin que vous pouvez partager les ressources entre plusieurs bases de données. |
| 87 | [Créer un nouveau pool de base de données élastique avec PowerShell](sql-database-elastic-pool-create-powershell.md) | Découvrez comment utiliser PowerShell pour la base de données SQL Azure horizontale ressources en créant un pool élastique scalable de la base de données pour gérer plusieurs bases de données. |
| 88 | [Script PowerShell pour identifier les bases de données appropriés pour un pool élastique de base de données](sql-database-elastic-pool-database-assessment-powershell.md) | Un pool élastique de base de données est un ensemble de ressources disponibles qui sont partagés par un groupe de bases de données élastiques. Ce document fournit un script Powershell pour aider à évaluer la pertinence de l’utilisation d’un pool élastique de base de données pour un groupe de bases de données. |
| 89 | [Surveiller et gérer un pool de base de données élastique avec c#](sql-database-elastic-pool-manage-csharp.md) | Utiliser des techniques de développement c# de base de données pour gérer un regroupement élastique de base de données de base de données SQL Azure. |
| 90 | [Surveiller et gérer un pool de base de données élastique grâce au portail Azure](sql-database-elastic-pool-manage-portal.md) | Découvrez comment utiliser le portail Azure et analyse décisionnelle intégrée de bases de données SQL pour gérer, surveiller et un pool élastique scalable de la base de données pour optimiser les performances de base de données et gérer les coûts de taille de droite. |
| 91 | [Surveiller et gérer un pool de base de données élastique avec PowerShell](sql-database-elastic-pool-manage-powershell.md) | Découvrez comment utiliser PowerShell pour gérer un pool élastique de base de données. |
| 92 | [Surveiller et gérer un pool de base de données élastique avec Transact-SQL](sql-database-elastic-pool-manage-tsql.md) | T-SQL permet de créer une base de données SQL Azure dans un pool élastique. Ou, utilisez T-SQL pour déplacer la datbase et déconnexion des pools. |
| 93 | [Facturation du pool élastique de base de données et les prix des informations](sql-database-elastic-pool-price.md) | Informations de tarification spécifiques pour des pools élastique de base de données. |



## <a name="elastic-query"></a>Requête élastique

| &nbsp; | Titre | Description |
| --: | :-- | :-- |
| 94 | [Dans d’autres bases de données plus grande échelle cloud (preview)](sql-database-elastic-query-getting-started.md) | comment utiliser les requêtes de base de données de base de données |
| 95 | [Prise en main des requêtes de bases de données croisées (partition verticale) (preview)](sql-database-elastic-query-getting-started-vertical.md) | comment utiliser une requête élastique avec verticalement partition bases de données |
| 96 | [Création de rapports entre les bases de données plus grande échelle cloud (preview)](sql-database-elastic-query-horizontal-partitioning.md) | comment configurer des requêtes élastiques sur partitions horizontales |
| 97 | [Azure SQL de base de données de base de données élastique requête vue d’ensemble (preview)](sql-database-elastic-query-overview.md) | Vue d’ensemble de la fonctionnalité de requête élastique |
| 98 | [Interrogation de bases de données cloud avec différents schémas (preview)](sql-database-elastic-query-vertical-partitioning.md) | comment configurer des requêtes de bases de données croisées sur partitions verticales |



## <a name="elastic-transaction"></a>Transaction élastique

| &nbsp; | Titre | Description |
| --: | :-- | :-- |
| 99 | [Transactions distribuées entre les bases de données cloud](sql-database-elastic-transactions-overview.md) | Vue d’ensemble des Transactions de base de données élastique avec la base de données SQL Azure |



## <a name="backup-and-recovery"></a>Sauvegarde et restauration

| &nbsp; | Titre | Description |
| --: | :-- | :-- |
| 100 | [Sauvegardes de base de données SQL](sql-database-automated-backups.md) | En savoir plus sur base de données SQL intégrés des sauvegardes de base de données qui vous permettent d’opérationnelle sauvegarder une base de données SQL Azure à un point antérieur dans le temps ou copier une base de données dans une base de données dans une zone géographique (35 jours). |
| 101 | [Vue d’ensemble de la continuité avec la base de données SQL Azure](sql-database-business-continuity.md) | Découvrez comment prend en charge la base de données SQL Azure cloud continuité des activités et récupération de base de données et vous aide à vos applications cloud critiques en cours d’exécution. |
| 102 | [Comment restaurer une seule table à partir d’une sauvegarde de base de données SQL Azure](sql-database-cloud-migrate-restore-single-table-azure-backup.md) | Découvrez comment restaurer une seule table à partir de la sauvegarde de la base de données SQL Azure. |
| 103 | [Concevoir une application pour sinistre cloud à l’aide de la réplication Geo Active dans la base de données SQL](sql-database-designing-cloud-solutions-for-disaster-recovery.md) | Apprenez à créer des solutions de reprise cloud pour la planification de la continuité entreprise à l’aide de la réplication geo pour la sauvegarde de données d’application avec la base de données SQL Azure. |
| 104 | [Restaurer une base de données SQL Azure ou basculement sur un site secondaire](sql-database-disaster-recovery.md) | Découvrez comment récupérer une base de données à partir d’un centre de données régional ou panne avec les fonctions de Geo restaurer et Azure SQL de base de données Active Geo réplication. |
| 105 | [Exécution d’exploration de récupération d’urgence](sql-database-disaster-recovery-drills.md) | Découvrez des conseils et des pratiques recommandées pour l’utilisation de la base de données SQL Azure pour effectuer des exercices de récupération d’urgence qui aideront à conservent votre mission critique applications d’entreprise résistants d’échecs et des interruptions. |
| 106 | [Stratégies de récupération pour les applications à l’aide du Pool élastique de base de données SQL](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md) | Découvrez comment concevoir votre solution de cloud pour la reprise en choisissant le modèle de basculement approprié. |
| 107 | [Utilisation de la classe RecoveryManager pour résoudre les problèmes de carte partagé](sql-database-elastic-database-recovery-manager.md) | Utilisez la classe RecoveryManager pour résoudre les problèmes avec des cartes partagé |
| 108 | [Importer un fichier à DOS pour créer une base de données SQL Azure](sql-database-import.md) | Créer une base de données SQL Azure en important un fichier à DOS existant. |
| 109 | [Restaurer une base de données SQL Azure à un point antérieur dans le temps grâce au portail d’Azure](sql-database-point-in-time-restore-portal.md) | Restaurer une base de données SQL Azure à un point antérieur dans le temps. |
| 110 | [Restaurer une base de données SQL Azure à un point antérieur dans le temps avec PowerShell](sql-database-point-in-time-restore-powershell.md) | Restaurer une base de données SQL Azure à un point antérieur dans le temps |
| 112 | [Restaurer une base de données SQL Azure à l’aide des sauvegardes automatisées de base de données](sql-database-recovery-using-backups.md) | En savoir plus sur la restauration Point-à-temps, qui permet de restaurer une base de données SQL Azure à un point antérieur dans le temps (35 jours). |
| 113 | [Restaurer une base de données SQL Azure supprimé à l’aide du portail Azure](sql-database-restore-deleted-database-portal.md) | Restaurer une base de données SQL Azure supprimé (Azure Portal). |
| 114 | [Restaurer une base de données SQL Azure supprimés à l’aide de PowerShell](sql-database-restore-deleted-database-powershell.md) | Restaurer une base de données SQL Azure supprimés (PowerShell). |
| 115 | [Restaurer une base de données à un point antérieur dans le temps, restaurer une base de données supprimé ou récupérer à partir de panne du centre de données](sql-database-troubleshoot-backup-and-restore.md) | Découvrez comment récupérer une base de données cloud d’erreurs et défaillances à l’aide des sauvegardes et réplicas dans la base de données SQL Azure. |



## <a name="migrate"></a>Migrer

| &nbsp; | Titre | Description |
| --: | :-- | :-- |
| 116 | [Exporter une base de données SQL Server vers un fichier à DOS à l’aide de SqlPackage](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md) | Base de données de SQL Microsoft Azure, migration de base de données, base de données d’exportation, exporter le fichier à DOS, sqlpackage |
| 117 | [Exporter une base de données SQL Server vers un fichier à DOS à l’aide de SQL Server Management Studio](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md) | Base de données de SQL Microsoft Azure, migration de base de données, base de données d’exportation, exporter le fichier à DOS, Assistant Exporter les données couche Application |
| 118 | [Importer dans la base de données SQL à partir d’un fichier à DOS à l’aide de SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md) | Base de données de SQL Microsoft Azure, migration de base de données, Importer base de données, importer un fichier à DOS, sqlpackage |
| 119 | [Importer à partir d’à DOS à l’aide de SSMS de la base de données SQL](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md) | Base de données Microsoft Azure SQL, base de données déployez, migration de base de données, base de données de l’importation, exportation de base de données, l’Assistant migration |
| 120 | [Migrer la base de données SQL Server à SQL de base de données à l’aide de déploiement d’une base de données à l’Assistant de base de données Microsoft Azure](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md) | Base de données de SQL Microsoft Azure, migration de base de données, l’Assistant de base de données Microsoft Azure |
| 121 | [Migrer la base de données SQL Server à la base de données SQL Azure à l’aide de la réplication de transactions](sql-database-cloud-migrate-compatible-using-transactional-replication.md) | Base de données de SQL Microsoft Azure, migration de base de données, Importer base de données, transactions réplication |
| 122 | [Déterminer la compatibilité de base de données SQL à l’aide de SqlPackage.exe](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md) | Microsoft Azure SQL base de données, migration de base de données, la compatibilité de base de données SQL, SqlPackage |
| 123 | [Utiliser SQL Server Management Studio pour compatibilité déterminer la base de données SQL avant la migration de base de données SQL Azure](sql-database-cloud-migrate-determine-compatibility-ssms.md) | Microsoft Azure SQL base de données, migration de base de données, la compatibilité de base de données SQL, exporter des données couche Assistant Création d’applications |
| 124 | [Utiliser l’Assistant Migration SQL Azure pour les problèmes de compatibilité de base de données de corriger SQL Server avant la migration de base de données SQL Azure](sql-database-cloud-migrate-fix-compatibility-issues.md) | Microsoft Azure SQL base de données, migration de base de données, sa compatibilité, l’Assistant Migration SQL Azure |
| 125 | [Migrer une base de données SQL Server à l’aide de SQL Server Data Tools pour Visual Studio de la base de données SQL Azure](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md) | Microsoft Azure SQL base de données, migration de base de données, sa compatibilité, Assistant de Migration SQL Azure, SSDT |
| 126 | [Corriger les problèmes de compatibilité de base de données SQL Server à l’aide de SQL Server Management Studio avant la migration de base de données SQL](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md) | Microsoft Azure SQL base de données, migration de base de données, sa compatibilité, l’Assistant Migration SQL Azure |
| 127 | [Archiver une base de données SQL Azure vers un fichier à DOS à l’aide de PowerShell](sql-database-export-powershell.md) | Archiver une base de données SQL Azure vers un fichier à DOS à l’aide de PowerShell |
| 128 | [Importer un fichier à DOS pour créer une base de données SQL Azure à l’aide de PowerShell](sql-database-import-powershell.md) | Importer un fichier à DOS pour créer une base de données SQL Azure à l’aide de PowerShell |
| 129 | [Charger les données d’un CSV dans SQL Azure Data Warehouse (fichiers plats)](sql-database-load-from-csv-with-bcp.md) | Pour une taille de données de petite taille, utilise bcp pour importer des données dans une base de données SQL Azure. |
| 130 | [Déplacer des bases de données entre des serveurs, entre les abonnements et entrant et sortant Azure](sql-database-troubleshoot-moving-data.md) | Actions rapides à copier, déplacer et migrer les données et les bases de données dans la base de données SQL Azure. |



## <a name="move-data-in-and-out"></a>Déplacer des données et arrière

| &nbsp; | Titre | Description |
| --: | :-- | :-- |
| 131 | [Migration de base de données SQL Server à SQL de base de données dans le cloud](sql-database-cloud-migrate.md) | Découvrez qu’en est-il local SQL Server migration base de données à la base de données SQL Azure dans le cloud. Utilisez les outils de migration de base de données pour tester la compatibilité avant la migration de la base de données. |
| 132 | [Copier une base de données SQL Azure](sql-database-copy.md) | Créer une copie d’une base de données SQL Azure |
| 133 | [Archiver une base de données SQL Azure vers un fichier à DOS à l’aide du portail Azure](sql-database-export.md) | Archiver une base de données SQL Azure vers un fichier à DOS à l’aide du portail Azure |



## <a name="security"></a>Sécurité

| &nbsp; | Titre | Description |
| --: | :-- | :-- |
| 134 | [Connexion à la base de données SQL ou SQL Data Warehouse à l’aide de l’authentification Azure Active Directory](sql-database-aad-authentication.md) | Découvrez comment vous connecter à la base de données SQL à l’aide de l’authentification Azure Active Directory. |
| 135 | [Toujours chiffrés : Protection des données sensibles dans la base de données SQL et stocker vos clés de chiffrement dans le magasin de certificats Windows](sql-database-always-encrypted.md) | Protection des données sensibles dans votre base de données SQL en minutes. |
| 136 | [Toujours chiffrés : Protection des données sensibles dans la base de données SQL et stocker vos clés de chiffrement dans l’archivage sécurisé de clé Azure](sql-database-always-encrypted-azure-key-vault.md) | Protection des données sensibles dans votre base de données SQL en minutes. |
| 137 | [Base de données SQL - prise en charge des clients de niveau inférieur et les modifications de point de terminaison IP pour l’audit](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md) | Découvrez les prise en charge des clients de niveau inférieur de la base de données SQL et IP modifications de point de terminaison pour l’audit. |
| 138 | [Configurer des règles de pare-feu au niveau du serveur de base de données SQL Azure à l’aide de PowerShell](sql-database-configure-firewall-settings-powershell.md) | Découvrez comment configurer le pare-feu pour les adresses IP qui accèdent aux bases de données SQL Azure. |
| 139 | [Configurer les règles de pare-feu au niveau du serveur de base de données SQL Azure à l’aide de l’API REST](sql-database-configure-firewall-settings-rest.md) | Découvrez comment configurer le pare-feu pour les adresses IP qui accèdent aux bases de données SQL Azure. |
| 140 | [Configurer les règles de pare-feu au niveau du serveur et au niveau de la base de données de base de données SQL Azure à l’aide de T-SQL](sql-database-configure-firewall-settings-tsql.md) | Découvrez comment configurer le pare-feu pour les adresses IP qui accèdent aux bases de données SQL Azure. |
| 141 | [Prise en main SQL de base de données dynamique données Masking (Azure Portal)](sql-database-dynamic-data-masking-get-started.md) | Comment prendre en main SQL de base de données dynamique données Masking dans le portail Azure |
| 142 | [Prise en main SQL de base de données dynamique données Masking (Azure classique Portal)](sql-database-dynamic-data-masking-get-started-portal.md) | Comment prendre en main SQL de base de données dynamique données Masking dans le portail classique Azure |
| 143 | [Configurer les règles de pare-feu de base de données SQL Azure \- vue d’ensemble](sql-database-firewall-configure.md) | Découvrez comment configurer un pare-feu de base de données SQL avec les règles de pare-feu au niveau du serveur et au niveau de la base de données afin de gérer l’accès. |
| 144 | [Didacticiel de base de données SQL : créer des comptes d’utilisateurs à accéder et gérer une base de données de base de données SQL](sql-database-get-started-security.md) | Apprenez à créer des comptes d’utilisateur pour accéder et gérer une base de données. |
| 145 | [Authentification de base de données SQL et d’autorisation : accorder l’accès](sql-database-manage-logins.md) | En savoir plus sur la gestion de la sécurité de base de données SQL, notamment comment gérer la sécurité access et de la connexion de base de données via le compte principal au niveau du serveur. |
| 146 | [Limitations et conseils de sécurité de base de données SQL azure](sql-database-security-guidelines.md) | Découvrez les instructions de la base de données SQL Microsoft Azure et limitations liées à la sécurité. |
| 147 | [Prise en main de détection de base de données SQL](sql-database-threat-detection-get-started.md) | Comment se familiariser avec la détection de menace SQL de base de données dans le portail Azure |
| 148 | [Comment effectuer des tâches d’administration courantes telles que la réinitialisation de mot de passe administrateur dans la base de données SQL Azure](sql-database-troubleshoot-permissions.md) | Décrit comment effectuer des tâches administratives dans la base de données SQL. Par exemple, la réinitialisation de mot de passe administrateur, octroi et suppression de l’accès. |



## <a name="manage-your-database"></a>Gérer votre base de données

| &nbsp; | Titre | Description |
| --: | :-- | :-- |
| 149 | [Prise en main d’audit de base de données SQL](sql-database-auditing-get-started.md) | Prise en main d’audit de base de données SQL |
| 150 | [Configurer une règle de pare-feu au niveau du serveur de base de données SQL Azure à l’aide du portail Azure](sql-database-configure-firewall-settings.md) | Découvrez comment configurer le pare-feu pour les adresses IP qui accèdent Azure SQL server. |
| 151 | [Copier une base de données SQL Azure à l’aide du portail Azure](sql-database-copy-portal.md) | Créer une copie d’une base de données SQL Azure |
| 152 | [Gestion des bases de données SQL Azure à l’aide de Automation Azure](sql-database-manage-automation.md) | Découvrez comment le service Azure Automation peut être utilisé pour gérer les bases de données SQL Azure à l’échelle. |
| 153 | [Vue d’ensemble : outils de gestion pour la base de données SQL](sql-database-manage-overview.md) | Compare les outils et les options de gestion de base de données SQL Azure |
| 154 | [Base de données SQL Azure à l’aide des vues de gestion dynamiques de surveillance](sql-database-monitoring-with-dmvs.md) | Apprenez à détecter et diagnostiquer les problèmes de performances courants à l’aide des vues de gestion dynamiques pour analyser la base de données SQL Microsoft Azure. |
| 155 | [Protéger votre base de données SQL](sql-database-security.md) | En savoir plus sur la sécurité de base de données SQL Azure et SQL Server, notamment les différences entre le cloud et SQL Server en local lorsqu’il s’agit de l’authentification, l’autorisation, la sécurité de connexion, le chiffrement et la conformité. |



## <a name="extended-events"></a>Événements étendus

| &nbsp; | Titre | Description |
| --: | :-- | :-- |
| 156 | [Code de cible fichier événement pour les événements étendus dans la base de données SQL](sql-database-xevent-code-event-file.md) | Fournit des PowerShell et Transact-SQL pour un échantillon de code en deux phases qui montre la cible du fichier de l’événement dans un événement étendu sur base de données SQL Azure. Stockage Azure est une partie obligatoire de ce scénario. |
| 157 | [Anneau de code cible tampon étendus événements dans la base de données SQL](sql-database-xevent-code-ring-buffer.md) | Fournit un exemple de code Transact-SQL qui a créé rapide et facile à l’aide de la cible du tampon en anneau, dans la base de données SQL Azure. |
| 158 | [Événements étendus dans la base de données SQL](sql-database-xevent-db-diff-from-svr.md) | Décrit les événements étendus (XEvents) dans la base de données SQL Azure, et comment les sessions d’événements diffèrent légèrement de sessions d’événements dans Microsoft SQL Server. |



## <a name="geo-replication"></a>Geo réplication

| &nbsp; | Titre | Description |
| --: | :-- | :-- |
| 159 | [Démarrer une conversation de basculement planifié ou de base de données SQL Azure avec le portail Azure](sql-database-geo-replication-failover-portal.md) | Lancer un basculement planifié ou de base de données SQL Azure à l’aide du portail Azure |
| 160 | [Lancer un basculement planifié ou de base de données SQL Azure avec PowerShell](sql-database-geo-replication-failover-powershell.md) | Lancer un basculement planifié ou de base de données SQL Azure à l’aide de PowerShell |
| 161 | [Lancer un basculement planifié ou de base de données SQL Azure avec Transact-SQL](sql-database-geo-replication-failover-transact-sql.md) | Lancer un basculement planifié ou de base de données SQL Azure à l’aide de Transact-SQL |
| 162 | [Vue d’ensemble : SQL de base de données Active Geo-réplication](sql-database-geo-replication-overview.md) | Réplication de Geo active vous permet d’installation 4 copies de votre base de données dans un des centres de données Azure. |
| 163 | [Configurer la Geo réplication de base de données SQL Azure avec le portail Azure](sql-database-geo-replication-portal.md) | Configurer la Geo-réplication de base de données SQL Azure à l’aide du portail Azure |
| 164 | [Configurer la Geo-réplication de base de données SQL Azure avec PowerShell](sql-database-geo-replication-powershell.md) | Configurer la réplication Geo Active pour la base de données SQL Azure à l’aide de PowerShell |
| 165 | [Comment gérer la sécurité de base de données SQL Azure après sinistre](sql-database-geo-replication-security-config.md) | Cette rubrique explique les considérations relatives à la sécurité pour gérer la sécurité après un basculement ou restaurer une base de données. |
| 166 | [Configurer la Geo-réplication de base de données SQL Azure avec Transact-SQL](sql-database-geo-replication-transact-sql.md) | Configurer la Geo-réplication de base de données SQL Azure à l’aide de Transact-SQL |
| 167 | [Geo-restaurer une base de données SQL Azure à partir d’une sauvegarde geo redondants à l’aide du portail Azure](sql-database-geo-restore-portal.md) | Geo-restaurer une base de données SQL Azure à partir d’une sauvegarde geo redondantes (Azure Portal). |
| 168 | [Restaurer une base de données SQL Azure à partir d’une sauvegarde geo redondants à l’aide de PowerShell](sql-database-geo-restore-powershell.md) | Restaurer une base de données SQL Azure dans un nouveau serveur à partir d’une sauvegarde geo redondants |



## <a name="upgrade"></a>Mise à niveau

| &nbsp; | Titre | Description |
| --: | :-- | :-- |
| 169 | [Amélioration des performances de requête avec compatibilité 130 niveau dans la base de données SQL Azure](sql-database-compatibility-level-query-performance-130.md) | Étapes et des outils permettant de savoir à quel niveau de compatibilité adapté à votre base de données sur base de données SQL Azure ou Microsoft SQL Server |
| 170 | [Gestion des mises à niveau des applications cloud à l’aide de la base de données Active Geo-réplication SQL](sql-database-manage-application-rolling-upgrade.md) | Apprenez à utiliser la base de données SQL Azure geo-réplication pour prendre en charge les mises à niveau en ligne de votre application cloud. |
| 171 | [Mise à niveau vers V12 de base de données SQL Azure à l’aide du portail Azure](sql-database-upgrade-server-portal.md) | Explique comment mettre à niveau vers V12 de base de données SQL Azure, notamment comment mettre à niveau des bases de données Web et entreprise et comment mettre à niveau un serveur 11 migration ses bases de données directement dans un pool élastique de base de données à l’aide du portail Azure. |
| 172 | [Mise à niveau vers V12 de base de données SQL Azure à l’aide de PowerShell](sql-database-upgrade-server-powershell.md) | Explique comment mettre à niveau vers V12 de base de données SQL Azure, notamment comment mettre à niveau des bases de données Web et entreprise et comment mettre à niveau un serveur 11 migration ses bases de données directement dans un pool élastique de base de données à l’aide de PowerShell. |
| 173 | [Planifier et préparer la mise à niveau vers V12 de base de données SQL](sql-database-v12-plan-prepare-upgrade.md) | Décrit les préparations et les limites de mise à niveau vers la version V12 d’Azure SQL de base de données. |
| 174 | [Quelles sont les nouveautés dans V12 de base de données SQL](sql-database-v12-whats-new.md) | Explique pourquoi les systèmes d’entreprise qui sont à l’aide de base de données SQL Azure dans le cloud lui facilitera par la mise à niveau vers la version V12. |
| 175 | [Forum aux questions sur coucher de soleil Web et Business Edition](sql-database-web-business-sunset-faq.md) | Savoir quand les bases de données Web SQL Azure et entreprise validité prend fin et en savoir plus sur les fonctionnalités des nouvelle niveaux de service. |



## <a name="tools"></a>Outils

| &nbsp; | Titre | Description |
| --: | :-- | :-- |
| 176 | [Gérer la base de données SQL Azure avec PowerShell](sql-database-command-line-tools.md) | Gestion de la base de données SQL Azure avec PowerShell. |
| 177 | [Didacticiel de base de données SQL : se connecter Excel à une base de données SQL Azure et créer un rapport](sql-database-connect-excel.md) | Découvrez comment établir une connexion à la base de données SQL Azure dans le cloud Microsoft Excel. Importer des données dans Excel pour l’exploration de données et de rapports. |
| 178 | [Créer une base de données SQL et effectuer des tâches courantes de configuration de base de données avec les applets de commande PowerShell](sql-database-get-started-powershell.md) | Découvrez comment créer une base de données SQL avec PowerShell. Tâches de configuration de base de données courantes peuvent être gérés par le biais applets de commande PowerShell. |
| 179 | [Mise en route de la synchronisation de données SQL Azure (Preview)](sql-database-get-started-sql-data-sync.md) | Ce didacticiel vous permet de commencer à utiliser la synchronisation de données SQL Azure (Preview). |
| 180 | [Gestion de base de données SQL Azure avec SQL Server Management Studio](sql-database-manage-azure-ssms.md) | Découvrez comment utiliser SQL Server Management Studio pour gérer les bases de données et les serveurs de base de données SQL. |
| 181 | [Gestion des bases de données SQL Azure à l’aide du portail Azure](sql-database-manage-portal.md) | Découvrez comment utiliser le portail Azure pour gérer une base de données relationnel dans le cloud à l’aide du portail Azure. |
| 182 | [Modifier le niveau et les performances niveau de service (tarification couche) d’une base de données SQL avec PowerShell](sql-database-scale-up-powershell.md) | Modifier le niveau de service et niveau de performance d’une base de données SQL Azure montre comment mettre à l’échelle de votre base de données SQL vers le haut ou vers le bas avec PowerShell. Modifier le niveau de tarification d’une base de données SQL Azure avec PowerShell. |
| 183 | [Utiliser SQL Server Management Studio dans Azure RemoteApp pour vous connecter à la base de données SQL](sql-database-ssms-remoteapp.md) | Utilisez ce didacticiel pour apprendre à utiliser SQL Server Management Studio dans Azure RemoteApp pour la sécurité et de performances lors de la connexion à la base de données SQL |



## <a name="reference"></a>Référence

| &nbsp; | Titre | Description |
| --: | :-- | :-- |
| 184 | [Bibliothèques de connexions de base de données SQL et SQL Server](sql-database-libraries.md) | Affiche le numéro de version minimale pour chaque pilote client programmes peuvent utiliser pour vous connecter à la base de données SQL Azure ou sur Microsoft SQL Server. Un lien est fourni pour plus d’informations de version sur les pilotes qui sont publiés par la Communauté plutôt qu’à Microsoft. |
| 185 | [Limites de ressources de base de données SQL Azure](sql-database-resource-limits.md) | Cette page décrit certaines limites communes ressource pour la base de données SQL Azure. |
| 186 | [Différences Transact-SQL de base de données SQL Azure](sql-database-transact-sql-information.md) | Instructions Transact-SQL moins entièrement pris en charge dans la base de données SQL Azure |



## <a name="miscellaneous"></a>Divers

| &nbsp; | Titre | Description |
| --: | :-- | :-- |
| 187 | [Copier une base de données SQL Azure à l’aide de PowerShell](sql-database-copy-powershell.md) | Créer une copie d’une base de données SQL Azure à l’aide de PowerShell |
| 188 | [Copier une base de données SQL Azure à l’aide de Transact-SQL](sql-database-copy-transact-sql.md) | Créer une copie d’une base de données SQL Azure à l’aide de Transact-SQL |
| 189 | [Instructions et Limitations de général de la base de données SQL Azure](sql-database-general-limitations.md) | Cette page décrit certaines limitations générales pour la base de données SQL Azure, ainsi que des zones de l’interopérabilité et la prise en charge. |
| 190 | [Recommandations couche tarification de base de données SQL](sql-database-service-tier-advisor.md) | Lorsque vous modifiez les tarifs niveaux dans le portail Azure, tarifs recommandations couche est fournis recommander la couche qui correspond le mieux adapté à la charge de travail un existant Azure SQL de bases de données en cours d’exécution. Niveaux de tarification décrire le niveau de performances et de niveau de service d’une base de données SQL. |


&nbsp;

<hr/>

<a name="extras_append"></a>

## <a name="extras"></a>Suppléments

<a name="extra_related_articles"></a>

### <a name="related-articles-from-other-azure-services"></a>Articles connexes à partir d’autres services Azure

- [Sauvegarder votre application de Services d’application Azure dans Azure](../app-service-web/web-sites-backup.md)

<a name="extra_documentation_tools"></a>

### <a name="documentation-tools"></a>Outils de documentation

- [Mises à jour annoncés pour la base de données SQL Azure](http://azure.microsoft.com/updates/?service=sql-database)

- [Effectuer une recherche dans tous les types de documentation de Microsoft Azure, avec les filtres](http://azure.microsoft.com/search/documentation/)

<a name="extra_learning_path_graphics"></a>

### <a name="learning-path-graphics"></a>Graphiques de chemin d’accès d’apprentissage

- [Graphiques de chemin d’accès de formation pour tous les services Microsoft Azure](http://azure.microsoft.com/documentation/learning-paths/)

- [Rubriques d’apprentissage : Découvrez la base de données SQL Azure](http://azure.microsoft.com/documentation/learning-paths/sql-database-training-learn-sql-database/)

- [D’apprentissage : Échelle élastique base de données SQL](http://azure.microsoft.com/documentation/learning-paths/sql-database-elastic-scale/)


<!--
AzIxAA.ExtraAppend.sql-database.txt
C:\_MainW\VS15\AzureIndexAllArticles\AzureIndexAllArticles\In-Out\In\

This bullet link is improperly disallowed by publishing automation due to presence of string 'docuXXmentation/arXXticles':
- [Search SQL Database documentation, with filters](http://azure.microsoft.com/docuXXmentation/arXXticles/?service=sql-database)
-->


