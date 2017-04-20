<properties 
    pageTitle="À l’aide de la bibliothèque de client de base de données élastique entité Framework | Microsoft Azure" 
    description="Utiliser une bibliothèque de client de base de données élastique et entité Framework pour le codage de bases de données" 
    services="sql-database" 
    documentationCenter="" 
    manager="jhubbard" 
    authors="torsteng" 
    editor=""/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/27/2016" 
    ms.author="torsteng"/>

# <a name="elastic-database-client-library-with-entity-framework"></a>Bibliothèque de client de base de données élastique avec entité Framework 
 
Ce document indique les modifications dans une application Framework entité qui sont nécessaires à l’intégration avec les [Outils de base de données élastique](sql-database-elastic-scale-introduction.md). Le focus se trouve sur [Éclater mapper gestion](sql-database-elastic-scale-shard-map-management.md) et le [routage dépendant des données](sql-database-elastic-scale-data-dependent-routing.md) avec l’entité Framework **Code première** approche de rédaction. Le didacticiel [Code First – nouvelle base de données](http://msdn.microsoft.com/data/jj193542.aspx) pour FE sert de notre exemple en cours d’exécution dans l’ensemble de ce document. L’exemple de code qui accompagnent ce document fait partie d’outils d’une base de données élastique définir des échantillons dans les exemples de Code Visual Studio.
  
## <a name="downloading-and-running-the-sample-code"></a>Téléchargement et l’exécution de l’exemple de Code
Pour télécharger le code de cet article :

* Visual Studio 2012 ou version ultérieure est requise. 
* Démarrez Visual Studio. 
* Dans Visual Studio, sélectionnez Fichier -> Nouveau projet. 
* Dans la boîte de dialogue « Nouveau projet », accédez aux **Exemples en ligne** pour **Visual c#** et type « db élastique » dans la zone de recherche dans le coin supérieur droit.
    
    ![Entité Framework et élastique de base de données exemple d’application][1] 

    Sélectionnez l’échantillon appelé **Élastique outils DB pour SQL Azure – entité Framework intégration**. Après avoir accepté la licence, du chargement de l’échantillon. 

Pour exécuter l’exemple, vous devez créer trois bases de données vides dans la base de données SQL Azure :

* Base de données Gestionnaire de carte partagé
* Base de données partagé 1
* Base de données partagé 2

Une fois que vous avez créé ces bases de données, renseignez les espaces réservés dans **Program.cs** avec le nom de votre serveur de base de données SQL Azure, les noms de base de données et vos informations d’identification pour vous connecter aux bases de données. Générez la solution dans Visual Studio. Visual Studio télécharge les requis packages NuGet pour la bibliothèque de client élastique de base de données, à l’entité Framework et transitoires traitement dans le cadre du processus de génération de l’erreur. Vérifiez que la restauration des packages NuGet est activé pour votre solution. Vous pouvez activer ce paramètre en cliquant sur le fichier de solution dans l’Explorateur de solutions Visual Studio. 

## <a name="entity-framework-workflows"></a>Flux de travail entité Framework 

Les développeurs entité Framework s’appuient sur une des suivantes quatre flux de travail pour créer des applications et pour garantir la persistance pour les objets d’application : 

* **Code First (nouvelle base de données)**: FE le développeur crée le modèle dans le code de l’application, puis FE génère la base de données à partir de celui-ci. 
* **Code First (base de données existante)**: le développeur vous permet de FE générer le code d’application pour le modèle à partir d’une base de données existante.
* **Premier modèle**: le développeur crée le modèle dans le Concepteur de FE, puis FE crée la base de données à partir du modèle.
* **Première base de données**: le développeur utilise FE outils pour déduire le modèle à partir d’une base de données existante. 

Toutes ces approches s’appuient sur la classe DbContext pour gérer les connexions de base de données et le schéma de base de données pour une application en toute transparence. Que nous allons aborder plus en détail plus loin dans le document, différents constructeurs sur la classe base DbContext autorisent différents niveaux de contrôle sur la création d’une connexion, la création de démarrage et le schéma de base de données. Problèmes proviennent principalement du fait que la gestion de la connexion de base de données fournie par FE croise avec les fonctionnalités de gestion de connexion des interfaces routage dépendantes données fournies par la bibliothèque cliente élastique de base de données. 

## <a name="elastic-database-tools-assumptions"></a>Hypothèses d’outils de base de données élastique 

Pour les définitions de termes, voir [Glossaire des outils de base de données élastique](sql-database-elastic-scale-glossary.md).

Avec la bibliothèque de client de base de données élastique, vous définissez partitions de vos données d’application appelées shardlets. Shardlets sont identifiés par une clé ont et sont mappés aux bases de données. Une application peut avoir autant de bases de données selon vos besoins et distribuer le shardlets pour offrir suffisamment capacités ou performances donné exigences métiers actuelles. Le mappage ont des valeurs de clé aux bases de données est stocké par un mappage éclater fourni par le client de base de données élastique API. Nous appelons cette fonctionnalité de **Gestion de carte éclater**ou SMM abrégé. La carte éclater sert également l’intermédiaire des connexions de base de données pour les requêtes comportant une clé ont. Nous faire référence à cette fonctionnalité comme **routage dépendant des données**. 
 
Le Gestionnaire de carte éclater protège les utilisateurs des vues n’est pas cohérente en données shardlet pouvant se produire lors des opérations de gestion shardlet simultanées (par exemple, en déplaçant les données à partir d’un seul partagé à un autre) sont produisent. Pour ce faire, les cartes éclater gérées par l’intermédiaire d’une bibliothèque client les connexions de base de données pour une application. Ainsi, la fonctionnalité carte éclater automatiquement arrêter une connexion de base de données lorsque des opérations de gestion partagé pourraient avoir un impact sur shardlet que la connexion a été créée pour. Cette approche doit intégrer à certaines des fonctionnalités de FE, telles que la création de nouvelles connexions à partir d’un existant pour vérifier l’existence de base de données. En règle générale, nous avons observé que les constructeurs DbContext standards uniquement Professionnel fiable pour les connexions de base de données fermée que vous pouvez en toute sécurité cloner pour FE fonctionnent. À la place, le principe de conception de base de données élastique est à négocier uniquement des connexions ouvertes. On pourrait penser que la fermeture d’une connexion demandée par la bibliothèque cliente avant transmettant l’à la DbContext FE peut résoudre ce problème. Toutefois, en fermant la connexion et dépendre FE rouvrir, une foregoes les tests de validation et la cohérence effectuées par la bibliothèque. La fonctionnalité de migrations de FE, cependant, utilise ces connexions pour gérer le schéma de base de données sous-jacente de façon transparente à l’application. Dans l’idéal, nous aimerions conserver et combiner ces fonctionnalités de la bibliothèque de client de base de données élastique et FE dans la même application. La section suivante explique ces propriétés et les conditions plus en détail. 


## <a name="requirements"></a>Configuration requise 

Lorsque vous travaillez avec la bibliothèque de client de base de données élastique et l’API Framework entité, nous souhaitons conserver les propriétés suivantes : 

* **Horizontale**: pour ajouter ou supprimer des bases de données de la couche de données de l’application sharded comme bon vous semble pour les demandes de capacité de l’application. Cela signifie que contrôle sur la la création et la suppression des bases de données et à l’aide de la partagé de base de données élastique mappent API manager pour gérer les bases de données et les mappages de shardlets. 

* **Cohérence**: l’application utilise ont et utilise les fonctions routage de données dépendant de la bibliothèque client. Pour éviter toute corruption ou résultats de la requête incorrect, les connexions sont demandées via le Gestionnaire de carte partagé. Conserve également validation et la cohérence.
 
* **Code First**: pour conserver la commodité du premier paradigme de FE code. Dans le premier Code, classes dans l’application sont mappées en toute transparence avec les structures de base de données sous-jacentes. Le code d’application interagit avec DbSets qui masque la plupart des aspects impliqués dans le traitement de base de données sous-jacente.
 
* **Schéma**: entité Framework gère la création du schéma de base de données initiale et l’évolution de schéma suivantes via migrations. En conservant ces fonctionnalités, adapter votre application est facile à mesure que les données évoluent. 

Le guide suivant indique comment conforme à ces exigences pour les applications Code First à l’aide des outils de base de données élastique. 

## <a name="data-dependent-routing-using-ef-dbcontext"></a>Données dépendantes routage à l’aide de DbContext FE 

Connexions de base de données avec Framework entité sont généralement gérées par le biais sous-classes de **DbContext**. Créer ces sous-classes par dérivation de **DbContext**. C’est ici que vous définissez votre **DbSets** qui implémenter les collections reposant sur une base de données des objets CLR pour votre application. Dans le contexte de routage dépendant des données, nous pouvons identifier diverses propriétés utiles qui ne possèdent pas nécessairement pour d’autres scénarios d’application premières FE code : 

* La base de données existe déjà et a été enregistré dans la carte éclater élastique de base de données. 
* Le schéma de l’application a déjà été déployé sur la base de données (décrite ci-dessous). 
* Connexions de routage dépendant des données à la base de données sont demandées par la carte partagé. 

Pour intégrer **DbContexts** avec routage dépendant des données pour horizontale :

1. Créer des connexions de base de données physique via les interfaces client élastique de base de données du Gestionnaire de carte partagé, 
2. Renvoyer à la ligne de la connexion avec celle-ci **DbContext**
3. Passer la connexion vers le bas dans les classes de base **DbContext** afin de que tout le traitement sur le côté FE se passe-t-il également. 

L’exemple suivant illustre cette approche. (Ce code est également dans le projet Visual Studio accompagnement)

    public class ElasticScaleContext<T> : DbContext
    {
    public DbSet<Blog> Blogs { get; set; }
    …

        // C'tor for data dependent routing. This call will open a validated connection 
        // routed to the proper shard by the shard map manager. 
        // Note that the base class c'tor call will fail for an open connection
        // if migrations need to be done and SQL credentials are used. This is the reason for the 
        // separation of c'tors into the data-dependent routing case (this c'tor) and the internal c'tor for new shards.
        public ElasticScaleContext(ShardMap shardMap, T shardingKey, string connectionStr)
            : base(CreateDDRConnection(shardMap, shardingKey, connectionStr), 
            true /* contextOwnsConnection */)
        {
        }

        // Only static methods are allowed in calls into base class c'tors.
        private static DbConnection CreateDDRConnection(
        ShardMap shardMap, 
        T shardingKey, 
        string connectionStr)
        {
            // No initialization
            Database.SetInitializer<ElasticScaleContext<T>>(null);

            // Ask shard map to broker a validated connection for the given key
            SqlConnection conn = shardMap.OpenConnectionForKey<T>
                                (shardingKey, connectionStr, ConnectionOptions.Validate);
            return conn;
        }    

## <a name="main-points"></a>Points principaux
* Un nouveau constructeur remplace le constructeur par défaut dans celle-ci DbContext 
* Le nouveau constructeur accepte les arguments requis pour le routage dépendant des données via la bibliothèque de client de base de données élastique :
    * la carte éclater pour accéder aux interfaces routage dépendant des données,
    * la touche ont pour identifier le shardlet,
    * une chaîne de connexion avec les informations d’identification pour la connexion de routage dépendant des données à l’éclater. 
 
* L’appel vers le dernier tient un détour une méthode statique qui effectue toutes les étapes nécessaires pour le routage dépendant des données. 
   * Il utilise l’appel OpenConnectionForKey des interfaces client élastique de base de données sur la carte éclater pour établir une connexion ouverte.
   * Le mappage éclater crée la connexion ouverte à partagé contenant le shardlet pour la clé ont donné.
   * Cette connexion ouverte est passée au constructeur de classe de base de DbContext pour indiquer que cette connexion est devant être utilisé par FE au lieu de laisser FE créer automatiquement une nouvelle connexion. Ainsi, la connexion a été marqué par le client de base de données élastique API afin qu’il peut garantir la cohérence sous opérations de gestion de carte partagé.
 
  
Utilisez le nouveau constructeur pour votre sous-classe DbContext au lieu du constructeur par défaut dans votre code. Voici un exemple : 

    // Create and save a new blog.

    Console.Write("Enter a name for a new blog: "); 
    var name = Console.ReadLine(); 

    using (var db = new ElasticScaleContext<int>( 
                            sharding.ShardMap,  
                            tenantId1,  
                            connStrBldr.ConnectionString)) 
    { 
        var blog = new Blog { Name = name }; 
        db.Blogs.Add(blog); 
        db.SaveChanges(); 

        // Display all Blogs for tenant 1 
        var query = from b in db.Blogs 
                    orderby b.Name 
                    select b; 
     … 
    }

Le nouveau constructeur ouvre la connexion à la partagé qui contient les données pour la shardlet identifié par la valeur de **tenantid1**. Le code dans le bloc **à l’aide de** demeure inchangé pour accéder aux **DbSet** pour les blogs à l’aide de FE sur l’éclater pour **tenantid1**. Cette opération modifie sémantique pour le code de l’en utilisant bloquer tel que toutes les opérations de base de données sont maintenant limitées à l’une éclater où **tenantid1** est conservé. Par exemple, une requête LINQ sur les blogs **DbSet** retournerait uniquement blogs que qui se trouve sur le partagé actuel, mais pas ceux qui sont stockés dans d’autres milieu des fragments.  

#### <a name="transient-faults-handling"></a>Gestion de défaillances temporaires
L’équipe Microsoft Patterns & Practices publié le [The transitoires défaillance Application bloc de gestion des](https://msdn.microsoft.com/library/dn440719.aspx). La bibliothèque est utilisée avec une bibliothèque de client échelle élastique en combinaison avec FE. Cependant, assurez-vous que n’importe quelle exception transitoire renvoie à un emplacement dans lequel nous pouvons vous assurer que le nouveau constructeur est utilisé après une défaillance transitoire afin qu’une nouvelle tentative de connexion à l’aide de constructeurs que nous avons modifié. Sinon, une connexion à l’éclater correct n’est pas garantie, et il n’y a aucune garanties que la connexion est conservée lorsque des modifications au mappage d’éclater sont. 

Le code suivant illustre comment une stratégie de nouvelles tentatives SQL peut être utilisée autour constructeurs sous-classe **DbContext** nouveau : 

    SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() => 
    { 
        using (var db = new ElasticScaleContext<int>( 
                                sharding.ShardMap,  
                                tenantId1,  
                                connStrBldr.ConnectionString)) 
            { 
                    var blog = new Blog { Name = name }; 
                    db.Blogs.Add(blog); 
                    db.SaveChanges(); 
            … 
            } 
        }); 

**SqlDatabaseUtils.SqlRetryPolicy** dans le code ci-dessus est définie comme un **SqlDatabaseTransientErrorDetectionStrategy** avec un nombre de tentatives de 10 et délai d’attente 5 secondes entre les essais. Cette approche est similaire aux recommandations FE et transactions initialisé par l’utilisateur (voir [Limitations avec une nouvelle tentative de stratégies d’exécution (EF6 compter)](http://msdn.microsoft.com/data/dn307226). Les deux situations nécessitent que le programme d’application contrôle l’étendue dans laquelle l’exception transitoire retourne : à rouvrez la transaction ou (comme illustré) recréer le contexte à partir du constructeur NOMPROPRE qui utilise la bibliothèque de client élastique de base de données.

La nécessité de contrôler l’endroit où les exceptions transitoires prennent dans l’étendue exclut également l’utilisation de la **SqlAzureExecutionStrategy** intégrés qui est fourni avec FE. **SqlAzureExecutionStrategy** serait réouverture d’une connexion, mais pas utiliser **OpenConnectionForKey** et par conséquent contourner tous les la validation qui est effectuée dans le cadre de l’appel **OpenConnectionForKey** . En revanche, le code utilise la intégrés **DefaultExecutionStrategy** également fournie avec FE. Contrairement à **SqlAzureExecutionStrategy**, qu’il fonctionne correctement en combinaison avec la stratégie de réessayer de la gestion de pannes transitoires. La stratégie d’exécution est définie dans la classe **ElasticScaleDbConfiguration** . Notez que nous avons décidé de ne pas utiliser **DefaultSqlExecutionStrategy** car il propose pour utiliser **SqlAzureExecutionStrategy** en cas d’exceptions transitoires - qui peuvent amener à comportement incorrect comme indiqué. Pour plus d’informations sur les politiques de réessayer différentes et FE, voir [Connexion résilience dans FE](http://msdn.microsoft.com/data/dn456835.aspx).     

#### <a name="constructor-rewrites"></a>Constructeur réécritures
Les exemples de code ci-dessus illustrent le par défaut constructeur réécriture nécessaire pour votre application pour utiliser des données dépendantes routage avec l’infrastructure d’entité. Le tableau suivant généralise cette approche aux autres constructeurs. 


Constructeur en cours  | Constructeur réécrit de données | Constructeur de base | Notes
---------- | ----------- | ------------|----------
MyContext() |ElasticScaleContext (ShardMap, TKey) |DbContext (DbConnection, bool) |La connexion doit être une fonction de la carte partagé et la clé de routage dépendant des données. Vous avez besoin pour la création de la connexion automatique dérivation par FE et à la place utilisez le mappage partagé à négocier la connexion. 
MyContext(string)|ElasticScaleContext (ShardMap, TKey) |DbContext (DbConnection, bool) |La connexion est une fonction de la carte partagé et la clé de routage dépendant des données. Une chaîne de connexion ou le nom de base de données fixe ne fonctionne pas comme elles validation dérivation par le mappage partagé. 
MyContext(DbCompiledModel) |ElasticScaleContext (ShardMap, TKey, DbCompiledModel) |DbContext (DbConnection, DbCompiledModel, booléen) |La connexion d’obtenir créée pour la clé de carte et ont donné partagé avec le modèle fourni. Le modèle compilé sera passé à la base c'tor.
MyContext (DbConnection, bool) |ElasticScaleContext (ShardMap, TKey, booléen) |DbContext (DbConnection, bool) |La connexion doit être déduit à partir de la carte partagé et la clé. Il ne peut pas être fournie comme une entrée (à moins que cette entrée a été déjà utilisant la carte partagé et la clé). La valeur booléenne est passée. 
MyContext (chaîne, DbCompiledModel) |ElasticScaleContext (ShardMap, TKey, DbCompiledModel) |DbContext (DbConnection, DbCompiledModel, booléen) |La connexion doit être déduit à partir de la carte partagé et la clé. Il ne peut pas être fournie comme une entrée (à moins que cette entrée a été à l’aide de la carte partagé et la clé). Le modèle compilé sera passé. 
MyContext (ObjectContext, bool) |ElasticScaleContext (ShardMap, bool TKey, ObjectContext) |DbContext (ObjectContext, bool) |Le nouveau constructeur doit s’assurer que toutes les connexions dans ObjectContext passé comme entrée sont ré-acheminée vers une connexion gérée par échelle élastique. Une description détaillée de ObjectContexts est au-delà de la portée de ce document.
MyContext (DbConnection, DbCompiledModel, booléen) |ElasticScaleContext (ShardMap, bool TKey, DbCompiledModel)| DbContext (DbConnection, DbCompiledModel, booléen) ; |La connexion doit être déduit à partir de la carte partagé et la clé. La connexion ne peut pas être fournie sous forme d’entrée (à moins que cette entrée a été déjà utilisant la carte partagé et la clé). Modèle et booléen sont transmis à dernier. 

## <a name="shard-schema-deployment-through-ef-migrations"></a>Déploiement de schéma partagé par le biais migrations FE 

Gestion des schémas automatique est une commodité fournie par l’infrastructure d’entité. Dans le contexte des applications à l’aide des outils de base de données élastique, nous souhaitons conserver cette fonctionnalité pour configurer automatiquement le schéma au milieu des fragments nouvellement créés lorsque des bases de données sont ajoutés à l’application sharded. Le cas d’utilisation principale consiste à augmenter la capacité au niveau de données pour les applications sharded utilisant FE. Lieu d’utiliser des fonctionnalités de FE pour la gestion des schémas permet de réduire les efforts d’administration de la base de données avec une application sharded basée sur FE. 

Déploiement de schéma par le biais migrations FE fonctionne au mieux sur les **connexions non ouverts**. Ceci est contrairement au scénario de données dépendants de routage qui dépend de la connexion ouverte fournie par le client de base de données élastique API. Une autre différence est l’exigence de cohérence : lors de la souhaitable pour garantir la cohérence de toutes les connexions de routage dépendant des données à protéger contre toute manipulation de carte éclater simultanées, il n’est pas un problème avec le déploiement de schéma initial à une base de données qui a ne pas encore été enregistrés dans la carte éclater et ne pas encore été attribué à maintenir shardlets. Nous pouvons donc s’appuient sur les connexions de base de données pour cette scénarios, plutôt que d’utiliser le routage dépendant des données.  

Cela permet d’accéder à une approche où déploiement du schéma par le biais migrations FE est étroitement avec l’enregistrement de la nouvelle base de données comme un partagé dans map partagé de l’application. Elle repose sur les composants requis suivants : 

* La base de données a déjà été créée. 
* La base de données est vide, qu’il contient aucun schéma utilisateur et aucune donnée utilisateur.
* La base de données ne sont pas encore accessibles via le client de base de données élastique API pour le routage dépendant des données. 

Avec ces conditions préalables en place, nous pouvons créer un non ouvert régulières **SqlConnection** pour démarrer migrations FE pour le déploiement de schéma. Le code suivant illustre cette approche. 

        // Enter a new shard - i.e. an empty database - to the shard map, allocate a first tenant to it  
        // and kick off EF intialization of the database to deploy schema 

        public void RegisterNewShard(string server, string database, string connStr, int key) 
        { 

            Shard shard = this.ShardMap.CreateShard(new ShardLocation(server, database)); 

            SqlConnectionStringBuilder connStrBldr = new SqlConnectionStringBuilder(connStr); 
            connStrBldr.DataSource = server; 
            connStrBldr.InitialCatalog = database; 

            // Go into a DbContext to trigger migrations and schema deployment for the new shard. 
            // This requires an un-opened connection. 
            using (var db = new ElasticScaleContext<int>(connStrBldr.ConnectionString)) 
            { 
                // Run a query to engage EF migrations 
                (from b in db.Blogs 
                    select b).Count(); 
            } 

            // Register the mapping of the tenant to the shard in the shard map. 
            // After this step, data-dependent routing on the shard map can be used 

            this.ShardMap.CreatePointMapping(key, shard); 
        } 
 

Cet exemple illustre la méthode **RegisterNewShard** qui enregistre la partagé dans l’Explorateur d’éclater, déploie le schéma via migrations FE et stocke un mappage d’une clé ont à la partagé. Il s’appuie sur un constructeur de celle-ci **DbContext** (**ElasticScaleContext** dans l’échantillon) qui prend une chaîne de connexion SQL comme entrée. Le code de ce constructeur est directe, comme le montre l’exemple suivant : 


        // C'tor to deploy schema and migrations to a new shard 
        protected internal ElasticScaleContext(string connectionString) 
            : base(SetInitializerForConnection(connectionString)) 
        { 
        } 

        // Only static methods are allowed in calls into base class c'tors 
        private static string SetInitializerForConnection(string connnectionString) 
        { 
            // We want existence checks so that the schema can get deployed 
            Database.SetInitializer<ElasticScaleContext<T>>( 
        new CreateDatabaseIfNotExists<ElasticScaleContext<T>>()); 

            return connnectionString; 
        } 
 
Une utilisiez la version du constructeur héritée de la classe de base. Mais le code doit s’assurer que l’initialiseur par défaut pour FE est utilisé lors de la connexion. Par conséquent, la courte détour dans la méthode statique avant d’appeler le constructeur de classe de base avec la chaîne de connexion. Notez que l’enregistrement du milieu des fragments doit s’exécuter dans un domaine autre application ou un processus pour vous assurer que les paramètres initialiseur pour FE ne sont pas en conflit. 


## <a name="limitations"></a>Limitations 

Les approches décrites dans ce document entraînent quelques limitations : 

* Vous devez tout d’abord migrer vers une base de données SQL Server normal avant d’utiliser la bibliothèque de client de base de données élastique FE que les applications utilisant **LocalDb** . Mise à l’échelle une application via ont avec une échelle élastique n’est pas possible avec **LocalDb**. Notez que développement pouvez toujours utiliser **LocalDb**. 

* Toute modification apportée à l’application qui impliquent la modification de schéma de base de données devez traitée migrations FE sur tous les milieu des fragments. L’exemple de code pour ce document ne présente pas comment effectuer cette opération. Envisagez d’utiliser la base de données mise à jour avec un paramètre ConnectionString pour parcourir toutes les milieu des fragments ; ou extraire le script T-SQL pour la migration en attente à l’aide de la base de données mise à jour avec le – Script option et appliquer le script T-SQL à votre milieu des fragments.  

* Étant donné une demande, il est supposé que toutes ses traitement de base de données est contenue dans une seule partagé comme identifié par la clé ont fournie par la demande. Toutefois, cette hypothèse n’est pas toujours vraie. Par exemple, quand il n’est pas possible d’apporter une touche ont disponibles. Pour contourner ce problème, la bibliothèque cliente fournit la classe **MultiShardQuery** qui mettent en œuvre, une abstraction de connexion pour l’interrogation sur plusieurs milieu des fragments. Apprendre à utiliser la **MultiShardQuery** en combinaison avec FE est au-delà de la portée de ce document

## <a name="conclusion"></a>Conclusion

Les étapes décrites dans ce document, les applications de FE peuvent utiliser des capacités de la bibliothèque du client de base de données élastique pour routage dépendant des données en refactorisant constructeurs des sous-classes **DbContext** utilisées dans l’application FE. Cela limite les modifications requises pour ces emplacements où **DbContext** classes existent déjà. En outre, FE applications peuvent continuer à bénéficier de déploiement de schéma automatique en combinant les étapes qui appellent les migrations FE nécessaires à l’enregistrement de nouveau milieu des fragments et les mappages dans la carte partagé. 


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-use-entity-framework-applications-visual-studio/sample.png
 