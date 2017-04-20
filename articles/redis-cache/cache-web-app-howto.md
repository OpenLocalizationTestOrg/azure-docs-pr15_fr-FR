<properties 
    pageTitle="Comment créer une application Web avec Cache Redis | Microsoft Azure" 
    description="Apprenez à créer une application Web avec Cache Redis" 
    services="redis-cache" 
    documentationCenter="" 
    authors="steved0x" 
    manager="douge" 
    editor=""/>

<tags 
    ms.service="cache" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="cache-redis" 
    ms.devlang="na" 
    ms.topic="hero-article" 
    ms.date="10/11/2016" 
    ms.author="sdanie"/>

# <a name="how-to-create-a-web-app-with-redis-cache"></a>Comment créer une application Web avec Cache Redis

> [AZURE.SELECTOR]
- [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
- [ASP.NET](cache-web-app-howto.md)
- [Node.js](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

Ce didacticiel montre comment créer et déployer une application web ASP.NET pour une application web dans le Service d’application Azure à l’aide de Visual Studio 2015. L’exemple d’application affiche une liste des statistiques d’équipe à partir d’une base de données et affiche les différentes façons d’utiliser Azure Redis Cache pour stocker et récupérer des données à partir du cache. Lorsque vous exécutez le didacticiel, vous aurez une application web en cours d’exécution qui lit et écrit dans une base de données, optimisée avec Azure Redis Cache et hébergé dans Azure.

Vous allez découvrir :

-   Comment créer une application web ASP.NET MVC 5 dans Visual Studio.
-   Comment accéder aux données à partir d’une base de données à l’aide de Framework entité.
-   Comment améliorer les performances de données et réduire la charge de la base de données en stockant et récupérant des données à l’aide de Cache Redis Azure.
-   Comment utiliser un Redis triés ensemble pour récupérer les 5 équipes supérieure.
-   La mise en service les ressources Azure pour l’application à l’aide d’un modèle de gestionnaire de ressources.
-   Découvrez comment publier l’application sur Azure à l’aide de Visual Studio.

## <a name="prerequisites"></a>Conditions préalables

Pour terminer ce didacticiel, vous devez disposer les composants requis suivants.

-   [Compte Azure](#azure-account)
-   [Visual Studio 2015 avec le Kit de développement Azure pour .NET](#visual-studio-2015-with-the-azure-sdk-for-net)

### <a name="azure-account"></a>Compte Azure

Vous avez besoin d’un compte Azure pour terminer le didacticiel. Vous pouvez :

* [Ouvrir un compte Azure gratuitement](/pricing/free-trial/?WT.mc_id=redis_cache_hero). Vous obtenez crédits pouvant être utilisées pour essayer services Azure payants. Même une fois que les crédits sont utilisés vers le haut, vous pouvez conserver le compte et utiliser les fonctionnalités et services Azure gratuits.
* [Avantages d’abonné activer Visual Studio](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=redis_cache_hero). Votre abonnement MSDN vous donne crédits chaque mois que vous pouvez utiliser pour les services Azure payants.

### <a name="visual-studio-2015-with-the-azure-sdk-for-net"></a>Visual Studio 2015 avec le Kit de développement Azure pour .NET

Le didacticiel est écrit pour Visual Studio 2015 avec le [Kit de développement logiciel Azure pour .NET](../dotnet-sdk.md) point 2.8.2 ou version ultérieure. [Téléchargez la dernière Azure SDK Visual 2015 Studio ici](http://go.microsoft.com/fwlink/?linkid=518003). Visual Studio est automatiquement installé avec le Kit de développement si vous n’avez pas dessus.

Si vous avez Visual Studio 2013, vous pouvez [Télécharger la dernière version du SDK Azure pour Visual Studio 2013](http://go.microsoft.com/fwlink/?LinkID=324322). Certains écrans peut s’afficher différemment dans les illustrations présentées dans ce didacticiel.

>[AZURE.NOTE] En fonction du nombre des dépendances SDK vous disposez déjà sur votre ordinateur, l’installation du Kit de développement peut prendre beaucoup de temps, de quelques minutes au moins d’une demi-heure.

## <a name="create-the-visual-studio-project"></a>Créer le projet Visual Studio

1. Ouvrez Visual Studio et cliquez sur **fichier**, **Nouveau**, **projet**.

2. Développez le nœud **Visual c#** dans la liste des **modèles** , sélectionnez **Cloud**, puis cliquez sur **Application Web ASP.NET**. Assurez-vous que **.NET Framework 4.5.2** est sélectionnée.  Tapez **ContosoTeamStats** dans la zone de texte **nom** , puis cliquez sur **OK**.
 
    ![Créer des projets][cache-create-project]

3. Sélectionnez **MVC** comme type de projet. Désactivez la case à cocher **hôte dans le cloud** . Vous devez [configurer les ressources Azure](#provision-the-azure-resources) et [publier l’application sur Azure](#publish-the-application-to-azure) dans les étapes suivantes dans le didacticiel. Pour obtenir un exemple de mise en service d’une application web application Service Visual Studio en laissant **hôte dans le cloud** activée, voir [utiliser des applications Web dans le Service d’application Azure, à l’aide de ASP.NET et Visual Studio](../app-service-web/web-sites-dotnet-get-started.md).

    ![Sélectionnez le modèle de projet][cache-select-template]

4. Cliquez sur **OK** pour créer le projet.

## <a name="create-the-aspnet-mvc-application"></a>Créer l’application MVC ASP.NET

Dans cette section du didacticiel, vous devez créer la demande de base qui lit et affiche les statistiques d’équipe à partir d’une base de données.

-   [Ajouter le modèle](#add-the-model)
-   [Ajouter le contrôleur](#add-the-controller)
-   [Configurer des vues](#configure-the-views)

### <a name="add-the-model"></a>Ajouter le modèle

1. Droit **modèles** dans **L’Explorateur de solutions**, puis cliquez sur **Ajouter**, **classe**. 

    ![Ajouter un modèle][cache-model-add-class]

2. Entrez `Team` pour la classe nommer et cliquez sur **Ajouter**.

    ![Ajouter une classe de modèle][cache-model-add-class-dialog]

3. Remplacer le `using` instructions en haut de la `Team.cs` fichier avec les éléments suivants à l’aide d’instructions.


        using System;
        using System.Collections.Generic;
        using System.Data.Entity;
        using System.Data.Entity.SqlServer;


4. Remplacez la définition de la `Team` classe avec l’extrait de code suivantes qui contient une mise à jour `Team` classe Définition ainsi que d’autres classes Framework entité d’assistance. Pour plus d’informations sur la première approche de code Framework entité qui est utilisée dans ce didacticiel, voir [Code tout d’abord à une base de données](https://msdn.microsoft.com/data/jj193542).


        public class Team
        {
            public int ID { get; set; }
            public string Name { get; set; }
            public int Wins { get; set; }
            public int Losses { get; set; }
            public int Ties { get; set; }
        
            static public void PlayGames(IEnumerable<Team> teams)
            {
                // Simple random generation of statistics.
                Random r = new Random();
        
                foreach (var t in teams)
                {
                    t.Wins = r.Next(33);
                    t.Losses = r.Next(33);
                    t.Ties = r.Next(0, 5);
                }
            }
        }
        
        public class TeamContext : DbContext
        {
            public TeamContext()
                : base("TeamContext")
            {
            }
        
            public DbSet<Team> Teams { get; set; }
        }
        
        public class TeamInitializer : CreateDatabaseIfNotExists<TeamContext>
        {
            protected override void Seed(TeamContext context)
            {
                var teams = new List<Team>
                {
                    new Team{Name="Adventure Works Cycles"},
                    new Team{Name="Alpine Ski House"},
                    new Team{Name="Blue Yonder Airlines"},
                    new Team{Name="Coho Vineyard"},
                    new Team{Name="Contoso, Ltd."},
                    new Team{Name="Fabrikam, Inc."},
                    new Team{Name="Lucerne Publishing"},
                    new Team{Name="Northwind Traders"},
                    new Team{Name="Consolidated Messenger"},
                    new Team{Name="Fourth Coffee"},
                    new Team{Name="Graphic Design Institute"},
                    new Team{Name="Nod Publishers"}
                };
        
                Team.PlayGames(teams);
        
                teams.ForEach(t => context.Teams.Add(t));
                context.SaveChanges();
            }
        }
        
        public class TeamConfiguration : DbConfiguration
        {
            public TeamConfiguration()
            {
                SetExecutionStrategy("System.Data.SqlClient", () => new SqlAzureExecutionStrategy());
            }
        }


2. Dans l' **Explorateur de solutions**, double-cliquez sur **web.config** pour l’ouvrir.

    ![Web.config][cache-web-config]

3.  Ajouter la chaîne de connexion à la `connectionStrings` section. Le nom de la chaîne de connexion doit correspondre au nom de la classe de contexte de base de données entité Framework qui est `TeamContext`.

        <add name="TeamContext" connectionString="Data Source=(LocalDB)\v11.0;AttachDbFilename=|DataDirectory|\Teams.mdf;Integrated Security=True" providerName="System.Data.SqlClient" />


    Après avoir ajouté, la `connectionStrings` section doit ressembler à l’exemple suivant.


        <connectionStrings>
            <add name="DefaultConnection" connectionString="Data Source=(LocalDb)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\aspnet-ContosoTeamStats-20160216120918.mdf;Initial Catalog=aspnet-ContosoTeamStats-20160216120918;Integrated Security=True"
                providerName="System.Data.SqlClient" />
            <add name="TeamContext" connectionString="Data Source=(LocalDB)\v11.0;AttachDbFilename=|DataDirectory|\Teams.mdf;Integrated Security=True"  providerName="System.Data.SqlClient" />
        </connectionStrings>

### <a name="add-the-controller"></a>Ajouter le contrôleur

1. Appuyez sur **F6** pour générer le projet. 
2. Dans l' **Explorateur de solutions**, cliquez sur le dossier **contrôleurs** et choisissez **Ajouter**, **contrôleur**.

    ![Ajouter contrôleur][cache-add-controller]

3. Choisissez **Contrôleur de 5 MVC avec les affichages, à l’aide de Framework entité**, puis cliquez sur **Ajouter**. Si vous obtenez une erreur après avoir cliqué sur **Ajouter**, vérifiez que vous avez créé tout d’abord le projet.

    ![Ajouter une classe contrôleur][cache-add-controller-class]

5. Sélectionnez **équipe (ContosoTeamStats.Models)** dans la liste déroulante de **classe du modèle** . Sélectionnez **TeamContext (ContosoTeamStats.Models)** dans la liste déroulante **classe de contexte de données** . Type de `TeamsController` dans la zone nom **contrôleur** (si elle n’est pas automatiquement rempli). Cliquez sur **Ajouter** pour créer la classe contrôleur et ajouter les affichages par défaut.

    ![Configurer contrôleur][cache-configure-controller]

4. Dans l' **Explorateur de solutions**, développez **Global.asax** et double-cliquez sur **Global.asax.cs** pour l’ouvrir.

    ![Global.asax.cs][cache-global-asax]

5. Ajoutez les deux à l’aide d’instructions dans la partie supérieure du fichier dans l’autre à l’aide d’instructions.


        using System.Data.Entity;
        using ContosoTeamStats.Models;


6. Ajoutez la ligne suivante du code à la fin de la `Application_Start` méthode.


        Database.SetInitializer<TeamContext>(new TeamInitializer());


7. Dans l' **Explorateur de solutions**, développez `App_Start` et double-cliquez sur `RouteConfig.cs`.

    ![RouteConfig.cs][cache-RouteConfig-cs]

8. Remplacer `controller = "Home"` dans le code suivant dans la `RegisterRoutes` méthode avec `controller = "Teams"` comme le montre l’exemple suivant.


        routes.MapRoute(
            name: "Default",
            url: "{controller}/{action}/{id}",
            defaults: new { controller = "Teams", action = "Index", id = UrlParameter.Optional }
        );


### <a name="configure-the-views"></a>Configurer des vues

1. Dans l' **Explorateur de solutions**, développez le dossier **affichages** , puis sur le dossier **partagé** , puis double-cliquez sur **_Layout.cshtml**. 

    ![_Layout.cshtml][cache-layout-cshtml]

2. Modifier le contenu de la `title` élément et remplacer `My ASP.NET Application` avec `Contoso Team Stats` comme le montre l’exemple suivant.


        <title>@ViewBag.Title - Contoso Team Stats</title>


3. Dans la `body` section, mettre à jour de la première `Html.ActionLink` instruction et remplacer `Application name` avec `Contoso Team Stats` et remplacer `Home` avec `Teams`.
    -   Avant :`@Html.ActionLink("Application name", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" })`
    -   Après :`@Html.ActionLink("Contoso Team Stats", "Index", "Teams", new { area = "" }, new { @class = "navbar-brand" })`

    ![Modifications du code][cache-layout-cshtml-code]

4. Appuyez sur **Ctrl + F5** pour générer et exécuter l’application. Cette version de l’application lit les résultats directement à partir de la base de données. Notez les **Créer**, **Modifier**, **Détails**et **Supprimer des** actions qui ont été ajoutées automatiquement à l’application par la structure **Contrôleur de 5 MVC avec les affichages, à l’aide de Framework entité** . Dans la section suivante de ce didacticiel, vous allez ajouter Redis Cache afin d’optimiser l’accès aux données et fournir des fonctionnalités supplémentaires à l’application.

![Application de départ][cache-starter-application]

## <a name="configure-the-application-to-use-redis-cache"></a>Configurer l’application pour utiliser le Cache Redis

Dans cette section du didacticiel, vous devez configurer l’exemple d’application pour stocker et récupérer les statistiques d’équipe Contoso à partir d’une instance de Cache Redis Azure à l’aide du client de cache [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) .

-   [Configurer l’application à utiliser StackExchange.Redis](#configure-the-application-to-use-stackexchangeredis)
-   [Mettre à jour la classe TeamsController pour renvoyer les résultats du cache ou la base de données](#update-the-teamscontroller-class-to-return-results-from-the-cache-or-the-database)
-   [Mettre à jour les méthodes de créer, modifier et supprimer pour l’utiliser avec le cache](#update-the-create-edit-and-delete-methods-to-work-with-the-cache)
-   [Mise à jour de la vue équipes Index pour l’utiliser avec le cache](#update-the-teams-index-view-to-work-with-the-cache)


### <a name="configure-the-application-to-use-stackexchangeredis"></a>Configurer l’application à utiliser StackExchange.Redis

1. Pour configurer une application cliente dans Visual Studio en utilisant le package StackExchange.Redis NuGet, droit sur le projet dans **L’Explorateur de solutions** , puis sélectionnez **Manage NuGet Packages**. 

    ![Gérer les packages NuGet][redis-cache-manage-nuget-menu]

2. Tapez **StackExchange.Redis** dans la zone de texte Rechercher, sélectionnez la version souhaitée dans les résultats, puis cliquez sur **installer**.

    ![Package StackExchange.Redis NuGet][redis-cache-stack-exchange-nuget]

    Le package NuGet télécharge et ajoute les références assembly requis pour votre application cliente pour accéder à Azure Redis Cache avec le client de cache StackExchange.Redis. Si vous préférez utiliser une version de la bibliothèque de client **StackExchange.Redis** fort, choisissez **StackExchange.Redis.StrongName**; Sinon, choisissez **StackExchange.Redis**.

3. Dans l' **Explorateur de solutions**, développez le dossier **Controllers** et double-cliquez sur **TeamsController.cs** pour l’ouvrir.

    ![Contrôleur d’équipes][cache-teamscontroller]

4. Ajoutez les deux à l’aide des instructions **TeamsController.cs**.

        using System.Configuration;
        using StackExchange.Redis;

5. Ajoutez les propriétés suivantes deux à la `TeamsController` cours.

        // Redis Connection string info
        private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
        {
            string cacheConnection = ConfigurationManager.AppSettings["CacheConnection"].ToString();
            return ConnectionMultiplexer.Connect(cacheConnection);
        });
    
        public static ConnectionMultiplexer Connection
        {
            get
            {
                return lazyConnection.Value;
            }
        }
  
1. Créer un fichier sur votre ordinateur appelé `WebAppPlusCacheAppSecrets.config` et le placer dans un emplacement qui ne sont pas vérifiés à l’aide du code source de votre application exemple, si vous décidez d’archiver un emplacement. Dans cet exemple la `AppSettingsSecrets.config` fichier se trouve à `C:\AppSecrets\WebAppPlusCacheAppSecrets.config`.

    Modifier la `WebAppPlusCacheAppSecrets.config` et ajoutez le contenu suivant. Si vous exécutez l’application localement ces informations sont utilisées pour se connecter à votre instance Azure Redis Cache. Plus loin dans le didacticiel, vous allez configurer une instance de Cache Redis Azure et mettre à jour le nom du cache et le mot de passe. Si vous ne prévoyez d’exécuter l’exemple d’application localement, vous pouvez ignorer la création de ce fichier et les étapes suivantes qui font référence au fichier, car lorsque vous déployez Azure l’application récupère les informations de connexion du cache du paramètre d’application pour l’application Web et non à partir de ce fichier. Dans la mesure où les `WebAppPlusCacheAppSecrets.config` n’est pas déployée vers Azure avec votre application, vous n’en avez besoin, sauf si vous vous apprêtez à exécuter l’application localement.


        <appSettings>
          <add key="CacheConnection" value="MyCache.redis.cache.windows.net,abortConnect=false,ssl=true,password=..."/>
        </appSettings>


2. Dans l' **Explorateur de solutions**, double-cliquez sur **web.config** pour l’ouvrir.

    ![Web.config][cache-web-config]

3. Ajoutez le code suivant `file` attribut la `appSettings` élément. Si vous avez utilisé un autre nom de fichier ou un emplacement, remplacez ces valeurs pour les règles indiqués dans l’exemple.
    -   Avant :`<appSettings>`
    -   Après :` <appSettings file="C:\AppSecrets\WebAppPlusCacheAppSecrets.config">`

    Le runtime ASP.NET fusionne le contenu du fichier externe avec les marques de révision dans le `<appSettings>` élément. Le runtime ignore l’attribut de fichier si le fichier spécifié est introuvable. Votre secrets (la chaîne de connexion à votre cache) ne sont pas inclus dans le cadre du code source pour l’application. Lorsque vous déployez votre application web Azure, le `WebAppPlusCacheAppSecrests.config` fichier ne sont pas déployé (c’est ce que vous voulez). Il existe plusieurs méthodes pour spécifier ces secrets dans Azure, et dans ce didacticiel qu’ils sont configurés automatiquement lorsque vous [fournir les ressources Azure](#provision-the-azure-resources) dans un didacticiel ultérieur étape. Pour plus d’informations sur l’utilisation des secrets dans Azure, voir [meilleures pratiques pour les mots de passe déploiement et d’autres données sensibles ASP.NET et Azure Application Service](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure).


### <a name="update-the-teamscontroller-class-to-return-results-from-the-cache-or-the-database"></a>Mettre à jour la classe TeamsController pour renvoyer les résultats du cache ou la base de données

Dans cet exemple, statistiques d’équipe peuvent être récupérés à partir de la base de données ou à partir du cache. Statistiques d’équipe sont stockées dans le cache comme une série `List<Team>`et également comme un jeu trié à l’aide des types de données Redis. Lors de la récupération des éléments d’un jeu trié, vous pouvez extraire certaines, toutes ou la requête pour certains éléments. Dans cet exemple, vous allez interroger le jeu trié pour les 5 équipes supérieure classés selon le nombre de wins.

>[AZURE.NOTE] Il n’est pas nécessaire pour stocker les statistiques d’équipe dans plusieurs formats dans le cache pour pouvoir utiliser Azure Redis Cache. Ce didacticiel utilise plusieurs formats afin d’illustrer certains des différentes façons et différents types de données que vous pouvez utiliser en cache des données.



1. Ajoutez les éléments suivants à l’aide des instructions pour la `TeamsController.cs` fichier en haut avec l’autre à l’aide des instructions.

        using System.Diagnostics;
        using Newtonsoft.Json;

2. Remplacer le fichier `public ActionResult Index()` méthode avec l’implémentation suivante.


        // GET: Teams
        public ActionResult Index(string actionType, string resultType)
        {
            List<Team> teams = null;
        
            switch(actionType)
            {
                case "playGames": // Play a new season of games.
                    PlayGames();
                    break;
        
                case "clearCache": // Clear the results from the cache.
                    ClearCachedTeams();
                    break;
        
                case "rebuildDB": // Rebuild the database with sample data.
                    RebuildDB();
                    break;
            }
        
            // Measure the time it takes to retrieve the results.
            Stopwatch sw = Stopwatch.StartNew();
        
            switch(resultType)
            {
                case "teamsSortedSet": // Retrieve teams from sorted set.
                    teams = GetFromSortedSet();
                    break;
        
                case "teamsSortedSetTop5": // Retrieve the top 5 teams from the sorted set.
                    teams = GetFromSortedSetTop5();
                    break;
        
                case "teamsList": // Retrieve teams from the cached List<Team>.
                    teams = GetFromList();
                    break;
        
                case "fromDB": // Retrieve results from the database.
                default:
                    teams = GetFromDB();
                    break;
            }
        
            sw.Stop();
            double ms = sw.ElapsedTicks / (Stopwatch.Frequency / (1000.0));

            // Add the elapsed time of the operation to the ViewBag.msg.
            ViewBag.msg += " MS: " + ms.ToString();
        
            return View(teams);
        }


3. Ajouter les trois méthodes suivantes pour la `TeamsController` classe pour implémenter le `playGames`, `clearCache`, et `rebuildDB` types d’actions à partir de l’instruction switch ajoutés dans l’extrait de code précédent.

    La `PlayGames` méthode met à jour les statistiques d’équipe en simuler une saison des jeux, enregistre les résultats dans la base de données et efface les données maintenant obsolètes à partir du cache.


        void PlayGames()
        {
            ViewBag.msg += "Updating team statistics. ";
            // Play a "season" of games.
            var teams = from t in db.Teams
                        select t;
    
            Team.PlayGames(teams);
    
            db.SaveChanges();
    
            // Clear any cached results
            ClearCachedTeams();
        }


    La `RebuildDB` méthode réinitialise la base de données à l’ensemble par défaut des équipes, génère des statistiques pour les et efface les données maintenant obsolètes à partir du cache.
    
        void RebuildDB()
        {
            ViewBag.msg += "Rebuilding DB. ";
            // Delete and re-initialize the database with sample data.
            db.Database.Delete();
            db.Database.Initialize(true);

            // Clear any cached results
            ClearCachedTeams();
        }


    La `ClearCachedTeams` méthode supprime les statistiques mis en cache d’équipe à partir du cache.

    
        void ClearCachedTeams()
        {
            IDatabase cache = Connection.GetDatabase();
            cache.KeyDelete("teamsList");
            cache.KeyDelete("teamsSortedSet");
            ViewBag.msg += "Team data removed from cache. ";
        } 


4. Ajouter les quatre méthodes suivantes pour la `TeamsController` classe pour implémenter les différentes façons de récupérer les statistiques d’équipe dans le cache et la base de données. Chacune de ces méthodes retourne une `List<Team>` qui est ensuite affiché par l’affichage.

    La `GetFromDB` méthode lit les statistiques d’équipe à partir de la base de données.

        List<Team> GetFromDB()
        {
            ViewBag.msg += "Results read from DB. ";
            var results = from t in db.Teams
                orderby t.Wins descending
                select t; 
    
            return results.ToList<Team>();
        }


    La `GetFromList` méthode lit les statistiques d’équipe à partir de cache comme une série `List<Team>`. S’il existe une absence de cache, les statistiques d’équipe sont lire à partir de la base de données et ensuite stockés dans le cache pour une utilisation ultérieure. Dans cet exemple, nous utilisons JSON.NET sérialisation pour sérialiser les objets .NET vers et depuis le cache. Pour plus d’informations, voir [comment utiliser des objets .NET dans Azure Redis Cache](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache).

        List<Team> GetFromList()
        {
            List<Team> teams = null;

            IDatabase cache = Connection.GetDatabase();
            string serializedTeams = cache.StringGet("teamsList");
            if (!String.IsNullOrEmpty(serializedTeams))
            {
                teams = JsonConvert.DeserializeObject<List<Team>>(serializedTeams);

                ViewBag.msg += "List read from cache. ";
            }
            else
            {
                ViewBag.msg += "Teams list cache miss. ";
                // Get from database and store in cache
                teams = GetFromDB();

                ViewBag.msg += "Storing results to cache. ";
                cache.StringSet("teamsList", JsonConvert.SerializeObject(teams));
            }
            return teams;
        }


    La `GetFromSortedSet` méthode lit les statistiques d’équipe à partir d’un jeu trié mis en cache. S’il existe une absence de cache, les statistiques d’équipe sont lire à partir de la base de données et stockées dans le cache sous la forme d’un jeu trié.


        List<Team> GetFromSortedSet()
        {
            List<Team> teams = null;
            IDatabase cache = Connection.GetDatabase();
            // If the key teamsSortedSet is not present, this method returns a 0 length collection.
            var teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", order: Order.Descending);
            if (teamsSortedSet.Count() > 0)
            {
                ViewBag.msg += "Reading sorted set from cache. ";
                teams = new List<Team>();
                foreach (var t in teamsSortedSet)
                {
                    Team tt = JsonConvert.DeserializeObject<Team>(t.Element);
                    teams.Add(tt);
                }
            }
            else
            {
                ViewBag.msg += "Teams sorted set cache miss. ";
    
                // Read from DB
                teams = GetFromDB();
    
                ViewBag.msg += "Storing results to cache. ";
                foreach (var t in teams)
                {
                    Console.WriteLine("Adding to sorted set: {0} - {1}", t.Name, t.Wins);
                    cache.SortedSetAdd("teamsSortedSet", JsonConvert.SerializeObject(t), t.Wins);
                }
            }
            return teams;
        }


    La `GetFromSortedSetTop5` méthode lit les équipes haut 5 à partir de l’ensemble trié mis en cache. Il commence par vérifier le cache de l’existence de la `teamsSortedSet` clé. Si cette clé n’est pas présente, la `GetFromSortedSet` méthode est appelée pour lire les statistiques d’équipe et le stocker dans le cache. Suivant le jeu trié mis en cache est interrogé pour les 5 équipes supérieure qui sont retournés.


        List<Team> GetFromSortedSetTop5()
        {
            List<Team> teams = null;
            IDatabase cache = Connection.GetDatabase();

            // If the key teamsSortedSet is not present, this method returns a 0 length collection.
            var teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", stop: 4, order: Order.Descending);
            if(teamsSortedSet.Count() == 0)
            {
                // Load the entire sorted set into the cache.
                GetFromSortedSet();

                // Retrieve the top 5 teams.
                teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", stop: 4, order: Order.Descending);
            }

            ViewBag.msg += "Retrieving top 5 teams from cache. ";
            // Get the top 5 teams from the sorted set
            teams = new List<Team>();
            foreach (var team in teamsSortedSet)
            {
                teams.Add(JsonConvert.DeserializeObject<Team>(team.Element));
            }
            return teams;
        }


### <a name="update-the-create-edit-and-delete-methods-to-work-with-the-cache"></a>Mettre à jour les méthodes de créer, modifier et supprimer pour l’utiliser avec le cache

Le code de la structure qui a été créé dans le cadre de cet exemple inclut des méthodes pour ajouter, modifier et supprimer des équipes. Chaque fois qu’une équipe est ajoutée, modifiée ou supprimée, les données dans le cache sont obsolètes. Dans cette section, vous devez modifier ces trois méthodes pour effacer les équipes mis en cache afin que le cache ne sera pas synchronisé avec la base de données.

1. Accédez à la `Create(Team team)` méthode dans le `TeamsController` cours. Ajoutez un appel à la `ClearCachedTeams` méthode, comme illustré dans l’exemple suivant.


        // POST: Teams/Create
        // To protect from overposting attacks, please enable the specific properties you want to bind to, for 
        // more details see http://go.microsoft.com/fwlink/?LinkId=317598.
        [HttpPost]
        [ValidateAntiForgeryToken]
        public ActionResult Create([Bind(Include = "ID,Name,Wins,Losses,Ties")] Team team)
        {
            if (ModelState.IsValid)
            {
                db.Teams.Add(team);
                db.SaveChanges();
                // When a team is added, the cache is out of date.
                // Clear the cached teams.
                ClearCachedTeams();
                return RedirectToAction("Index");
            }
    
            return View(team);
        }


2. Accédez à la `Edit(Team team)` méthode dans le `TeamsController` cours. Ajoutez un appel à la `ClearCachedTeams` méthode, comme illustré dans l’exemple suivant.


        // POST: Teams/Edit/5
        // To protect from overposting attacks, please enable the specific properties you want to bind to, for 
        // more details see http://go.microsoft.com/fwlink/?LinkId=317598.
        [HttpPost]
        [ValidateAntiForgeryToken]
        public ActionResult Edit([Bind(Include = "ID,Name,Wins,Losses,Ties")] Team team)
        {
            if (ModelState.IsValid)
            {
                db.Entry(team).State = EntityState.Modified;
                db.SaveChanges();
                // When a team is edited, the cache is out of date.
                // Clear the cached teams.
                ClearCachedTeams();
                return RedirectToAction("Index");
            }
            return View(team);
        }


3. Accédez à la `DeleteConfirmed(int id)` méthode dans le `TeamsController` cours. Ajoutez un appel à la `ClearCachedTeams` méthode, comme illustré dans l’exemple suivant.


        // POST: Teams/Delete/5
        [HttpPost, ActionName("Delete")]
        [ValidateAntiForgeryToken]
        public ActionResult DeleteConfirmed(int id)
        {
            Team team = db.Teams.Find(id);
            db.Teams.Remove(team);
            db.SaveChanges();
            // When a team is deleted, the cache is out of date.
            // Clear the cached teams.
            ClearCachedTeams();
            return RedirectToAction("Index");
        }


### <a name="update-the-teams-index-view-to-work-with-the-cache"></a>Mise à jour de la vue équipes Index pour l’utiliser avec le cache

1. Dans l' **Explorateur de solutions**, développez le dossier **affichages** , puis le dossier **équipes** , puis double-cliquez sur **Index.cshtml**.

    ![Index.cshtml][cache-views-teams-index-cshtml]

2. Dans la partie supérieure du fichier, recherchez l’élément paragraphe suivant.

    ![Table d’action][cache-teams-index-table]

    Il s’agit le lien pour créer une nouvelle équipe. Remplacez l’élément paragraphe par le tableau suivant. Le tableau suivant comporte des liens d’action pour créer une nouvelle équipe, lecture d’une nouvelle saison des jeux, vider le cache de, récupérer les équipes du cache dans plusieurs formats, récupération les équipes à partir de la base de données et reconstruction de la base de données avec les données exemple novateur.


        <table class="table">
            <tr>
                <td>
                    @Html.ActionLink("Create New", "Create")
                </td>
                <td>
                    @Html.ActionLink("Play Season", "Index", new { actionType = "playGames" })
                </td>
                <td>
                    @Html.ActionLink("Clear Cache", "Index", new { actionType = "clearCache" })
                </td>
                <td>
                    @Html.ActionLink("List from Cache", "Index", new { resultType = "teamsList" })
                </td>
                <td>
                    @Html.ActionLink("Sorted Set from Cache", "Index", new { resultType = "teamsSortedSet" })
                </td>
                <td>
                    @Html.ActionLink("Top 5 Teams from Cache", "Index", new { resultType = "teamsSortedSetTop5" })
                </td>
                <td>
                    @Html.ActionLink("Load from DB", "Index", new { resultType = "fromDB" })
                </td>
                <td>
                    @Html.ActionLink("Rebuild DB", "Index", new { actionType = "rebuildDB" })
                </td>
            </tr>    
        </table>


3. Faites défiler vers le bas du fichier **Index.cshtml** et ajoutez le code suivant `tr` élément afin qu’il soit la dernière ligne de la dernière table dans le fichier.

        <tr><td colspan="5">@ViewBag.Msg</td></tr>

    Cette ligne affiche la valeur de `ViewBag.Msg` qui contient un rapport d’état concernant l’opération active qui est définie lorsque vous cliquez sur un des liens action de l’étape précédente.   

    ![Message d’état][cache-status-message]

4. Appuyez sur **F6** pour générer le projet.

## <a name="provision-the-azure-resources"></a>Configurer les ressources Azure

Pour héberger votre application dans Azure, vous devez tout d’abord activer les services Azure requis par votre application. L’exemple d’application dans ce didacticiel utilise les services Azure suivants.

-   Cache Redis Azure
-   Application Service Web App
-   Base de données SQL

Pour déployer ces services sur un groupe de ressources nouvelle ou existante de votre choix, cliquez sur le bouton **déployer vers Azure** suivant.

[! [Déployer sur Azure] [deploybutton]](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-web-app-redis-cache-sql-database%2Fazuredeploy.json)

Ce bouton **déployer vers Azure** utilise le modèle de [Démarrage rapide Azure](https://github.com/Azure/azure-quickstart-templates) [créer une application Web plus Redis Cache et base de données SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-redis-cache-sql-database) pour mise en service de ces services et définir la chaîne de connexion pour la base de données SQL et le paramètre de la chaîne de connexion Cache Redis Azure applications.

>[AZURE.NOTE] Si vous n’avez pas un compte Azure, vous pouvez [créer un compte Azure gratuit](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero) en quelques minutes.

Cliquer sur le bouton **déployer vers Azure** vous permet d’accéder au portail Azure et lance le processus de création les ressources décrites par le modèle.

![Déploiement d’Azure][cache-deploy-to-azure-step-1]

1. Sur la carte de **déploiement personnalisé** , sélectionnez l’abonnement Azure à utiliser et sélectionnez un groupe de ressources existant ou créez-en un, puis spécifiez l’emplacement de groupe de ressources.
2. Sur la carte de **paramètres** , spécifiez un nom de compte d’administrateur (**ADMINISTRATORLOGIN** - n’utilisez pas **administrateur**), mot de passe administrateur (**ADMINISTRATORLOGINPASSWORD**) et le nom de base de données (**DATABASENAME**). Les autres paramètres sont configurés pour un Service gratuit de l’application d’hébergement plan et options de coût inférieures pour la base de données SQL et Azure Redis Cache, qui ne comportent pas d’un niveau gratuit.
3. Modifier les autres paramètres si vous le souhaitez, ou conservez les valeurs par défaut, puis cliquez sur **OK**.


![Déploiement d’Azure][cache-deploy-to-azure-step-2]

1. Cliquez sur **les conditions juridiques révision**.
2. Lire les conditions sur la carte **d’achat** et cliquez sur **acheter**.
3. Pour commencer la mise en service les ressources, cliquez sur **créer** dans la carte de **déploiement personnalisée** .

Pour visualiser l’avancement de votre déploiement, cliquez sur l’icône de notification, puis cliquez sur **mise en route de déploiement**.

![Mise en route de déploiement][cache-deployment-started]

Vous pouvez afficher l’état de votre déploiement sur la carte **Microsoft.Template** .

![Déploiement d’Azure][cache-deploy-to-azure-step-3]

Lors de la configuration terminée, vous pouvez publier votre application sur Azure à partir de Visual Studio.

>[AZURE.NOTE] Toutes les erreurs pouvant se produire pendant le processus de mise en service sont affichent sur la carte **Microsoft.Template** . Les erreurs courantes sont serveurs SQL trop ou trop de Service d’application gratuit d’hébergement plans par abonnement. Résoudre les erreurs et redémarrez le processus en cliquant sur **redéployez** sur la carte **Microsoft.Template** ou le bouton **déployer vers Azure** dans ce didacticiel.

## <a name="publish-the-application-to-azure"></a>Publier l’application sur Azure

Dans cette étape de ce didacticiel, vous publiez l’application à Azure et exécuter dans le cloud.

1. Cliquez sur le projet **ContosoTeamStats** dans Visual Studio et choisissez **Publier**.

    ![Publier][cache-publish-app]

2. Cliquez sur **Service d’application Microsoft Azure**.

    ![Publier][cache-publish-to-app-service]

3. Sélectionnez l’abonnement utilisé lorsque vous créez les ressources Azure, développez le groupe de ressources contenant les ressources, sélectionnez l’application Web souhaitée, puis cliquez sur **OK**. Si vous avez utilisé le bouton **déployer vers Azure** votre nom de l’application Web commence par le **site Web** suivi de certains caractères supplémentaires.

    ![Sélectionnez Web App][cache-select-web-app]

4. Cliquez sur **Valider la connexion** pour vérifier vos paramètres, puis cliquez sur **Publier**.

    ![Publier][cache-publish]

    Après quelques instants doivent effectuer le processus de publication et un navigateur sera lancé avec l’exécution exemple d’application. Si vous obtenez une erreur DNS lors de la validation ou de la publication, et le processus de mise en service pour les ressources de l’application Azure terminée récemment, patientez un moment et réessayez.

    ![Cache ajoutée][cache-added-to-application]

Le tableau suivant décrit chaque lien d’action à partir de l’exemple d’application.

| Action                  | Description                                                                                                                                                      |
|-------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Créer une nouvelle              | Créer une nouvelle équipe.                                                                                                                                               |
| Lire saison             | Lire une saison des jeux, mettre à jour les statistiques d’équipe et désactivez les plus à jour des données à partir du cache de l’équipe.                                                                          |
| Vider le Cache             | Effacer les statistiques d’équipe à partir du cache.                                                                                                                             |
| Liste à partir du Cache         | Récupérer les statistiques d’équipe à partir du cache. S’il existe une absence de cache, charger les statistiques à partir de la base de données et enregistrer dans le cache pour une utilisation ultérieure.                                        |
| Jeu trié cache   | Récupérer les statistiques d’équipe à partir du cache à l’aide d’un jeu trié. S’il existe une absence de cache, charger les statistiques à partir de la base de données et enregistrer dans le cache à l’aide d’un jeu trié.  |
| Permet aux équipes de 5 principaux cache  | Extraire les 5 équipes supérieure du cache à l’aide d’un jeu trié. S’il existe une absence de cache, charger les statistiques à partir de la base de données et enregistrer dans le cache à l’aide d’un jeu trié. |
| Charger à partir de la base de données            | Récupérer les statistiques d’équipe à partir de la base de données.                                                                                                                       |
| Reconstruire la base de données              | Reconstruire la base de données et recharger les exemples de données d’équipe.                                                                                                        |
| Modifier / détails / supprimer | Modifier une équipe, afficher les détails d’une équipe, supprimer une équipe.                                                                                                             |


Cliquez sur certaines des actions et tester les récupérer les données à partir de différentes sources. Pas les différences dans le temps que nécessaire pour terminer les différentes méthodes de récupération des données à partir de la base de données et le cache.

## <a name="delete-the-resources-when-you-are-finished-with-the-application"></a>Supprimer les ressources lorsque vous avez terminé avec l’application

Lorsque vous avez terminé avec l’exemple d’application didacticiel, vous pouvez supprimer les ressources Azure utilisées afin de conserver les ressources de coûts et. Si vous utilisez le bouton **déployer vers Azure** dans la section [configurer les ressources Azure](#provision-the-azure-resources) et toutes vos ressources incluses dans le même groupe de ressources, vous pouvez les supprimer ensemble en une seule opération en supprimant le groupe de ressources.

1. Connectez-vous au [portail Azure](https://portal.azure.com) et cliquez sur **groupes de ressources**.
2. Tapez le nom de votre groupe de ressources dans la zone de texte de **filtrer les éléments...** .
3. Cliquez sur **...** à droite de votre groupe de ressources.
4. Cliquez sur **Supprimer**.

    ![Supprimer][cache-delete-resource-group]

5. Tapez le nom de votre groupe de ressources, cliquez sur **Supprimer**.

    ![Confirmer la suppression][cache-delete-confirm]

Après quelques instants le groupe de ressources et toutes ses ressources qu’il contient sont supprimés.

>[AZURE.IMPORTANT] Notez que la suppression d’un groupe de ressources est irréversible et que le groupe de ressources et toutes les ressources qu’il contient sont supprimés définitivement. Vérifiez que ne pas accidentellement supprimer le groupe de ressources incorrecte ou des ressources. Si vous avez créé les ressources pour l’hébergement de cet exemple à l’intérieur d’un groupe de ressources existant, vous pouvez supprimer individuellement chaque ressource de leurs cartes correspondantes.

## <a name="run-the-sample-application-on-your-local-machine"></a>Exécuter l’exemple d’application sur votre ordinateur local

Pour exécuter l’application localement sur votre ordinateur, vous avez besoin d’une instance de Cache Redis Azure dans lesquelles vous pouvez mettre en cache vos données. 

-   Si vous avez publié votre application Azure comme décrit dans la section précédente, vous pouvez utiliser l’instance de Cache Redis Azure qui a été mis en service au cours de cette étape.
-   Si vous avez une autre instance Azure Redis Cache existante, vous pouvez utiliser que pour exécuter cet exemple localement.
-   Si vous avez besoin créer une instance de Cache Redis Azure, vous pouvez suivre les étapes décrites dans [créer un cache](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

Une fois que vous avez sélectionné ou créé le cache à utiliser, recherchez le cache dans le portail Azure et récupérer les [touches d’accès rapide](cache-configure.md#access-keys) pour le cache et le [nom d’hôte](cache-configure.md#properties) . Pour plus d’informations, voir [configurer Redis les paramètres du cache](cache-configure.md#configure-redis-cache-settings).

1. Ouvrir le `WebAppPlusCacheAppSecrets.config` fichier que vous avez créé lors de l’étape de [configurer l’application d’utiliser le Cache Redis](#configure-the-application-to-use-redis-cache) de ce didacticiel à l’aide de l’éditeur de votre choix.

2. Modifier la `value` attribut et remplacer `MyCache.redis.cache.windows.net` par le [nom d’hôte](cache-configure.md#properties) du cache et spécifier la [clé primaire ou secondaire](cache-configure.md#access-keys) de votre cache en tant que le mot de passe.


        <appSettings>
          <add key="CacheConnection" value="MyCache.redis.cache.windows.net,abortConnect=false,ssl=true,password=..."/>
        </appSettings>


3. Appuyez sur **Ctrl + F5** pour exécuter l’application.

>[AZURE.NOTE] Notez que, étant donné que l’application, y compris la base de données est en cours d’exécution en local et le Cache Redis est hébergé dans Azure, le cache peut s’afficher sous effectuer la base de données. Pour de meilleures performances, l’application cliente et une instance de Cache Redis Azure doivent être au même emplacement. 

## <a name="next-steps"></a>Étapes suivantes

-   En savoir plus sur la [Mise en route d’ASP.NET MVC 5](http://www.asp.net/mvc/overview/getting-started/introduction/getting-started) sur le site [ASP.NET](http://asp.net/) .
-   Pour plus d’exemples de création d’une application Web ASP.NET dans le Service d’application, consultez [créer et déployer une application web ASP.NET dans le Service d’application Azure](https://github.com/Microsoft/HealthClinic.biz/wiki/Create-and-deploy-an-ASP.NET-web-app-in-Azure-App-Service) à partir de la connexion de [HealthClinic.biz](https://github.com/Microsoft/HealthClinic.biz) 2015 [Démo](https://blogs.msdn.microsoft.com/visualstudio/2015/12/08/connectdemos-2015-healthclinic-biz/).
    -   Pour des Démarrages rapides supplémentaires à partir de la démonstration HealthClinic.biz, consultez [Azure développeur outils Démarrages rapides](https://github.com/Microsoft/HealthClinic.biz/wiki/Azure-Developer-Tools-Quickstarts).
-   Plus d’informations sur l’approche [Code tout d’abord à une base de données](https://msdn.microsoft.com/data/jj193542) infrastructure entité qui est utilisée dans ce didacticiel.
-   En savoir plus sur les [applications web dans le Service d’application Azure](../app-service-web/app-service-web-overview.md).
-   Découvrez comment faire pour [surveiller](cache-how-to-monitor.md) le cache dans le portail Azure.

-   Explorer les fonctionnalités premium d’Azure Redis Cache
    -   [Comment configurer persistance Premium Azure Redis cache](cache-how-to-premium-persistence.md)
    -   [Comment faire pour configurer le cluster Premium Azure Redis cache](cache-how-to-premium-clustering.md)
    -   [Comment configurer la prise en charge de réseau virtuel pour un Cache de Redis Premium Azure](cache-how-to-premium-vnet.md)
    -   Consultez le [Forum aux questions Azure Redis Cache](cache-faq.md#what-redis-cache-offering-and-size-should-i-use) pour plus d’informations sur la taille, le débit et la bande passante avec cache premium.



<!-- IMAGES -->
[cache-starter-application]: ./media/cache-web-app-howto/cache-starter-application.png
[cache-added-to-application]: ./media/cache-web-app-howto/cache-added-to-application.png
[cache-create-project]: ./media/cache-web-app-howto/cache-create-project.png
[cache-select-template]: ./media/cache-web-app-howto/cache-select-template.png
[cache-model-add-class]: ./media/cache-web-app-howto/cache-model-add-class.png
[cache-model-add-class-dialog]: ./media/cache-web-app-howto/cache-model-add-class-dialog.png
[cache-add-controller]: ./media/cache-web-app-howto/cache-add-controller.png
[cache-add-controller-class]: ./media/cache-web-app-howto/cache-add-controller-class.png
[cache-configure-controller]: ./media/cache-web-app-howto/cache-configure-controller.png
[cache-global-asax]: ./media/cache-web-app-howto/cache-global-asax.png
[cache-RouteConfig-cs]: ./media/cache-web-app-howto/cache-RouteConfig-cs.png
[cache-layout-cshtml]: ./media/cache-web-app-howto/cache-layout-cshtml.png
[cache-layout-cshtml-code]: ./media/cache-web-app-howto/cache-layout-cshtml-code.png
[redis-cache-manage-nuget-menu]: ./media/cache-web-app-howto/redis-cache-manage-nuget-menu.png
[redis-cache-stack-exchange-nuget]: ./media/cache-web-app-howto/redis-cache-stack-exchange-nuget.png
[cache-teamscontroller]: ./media/cache-web-app-howto/cache-teamscontroller.png
[cache-web-config]: ./media/cache-web-app-howto/cache-web-config.png
[cache-views-teams-index-cshtml]: ./media/cache-web-app-howto/cache-views-teams-index-cshtml.png
[cache-teams-index-table]: ./media/cache-web-app-howto/cache-teams-index-table.png
[cache-status-message]: ./media/cache-web-app-howto/cache-status-message.png
[deploybutton]: ./media/cache-web-app-howto/deploybutton.png
[cache-deploy-to-azure-step-1]: ./media/cache-web-app-howto/cache-deploy-to-azure-step-1.png
[cache-deploy-to-azure-step-2]: ./media/cache-web-app-howto/cache-deploy-to-azure-step-2.png
[cache-deploy-to-azure-step-3]: ./media/cache-web-app-howto/cache-deploy-to-azure-step-3.png
[cache-deployment-started]: ./media/cache-web-app-howto/cache-deployment-started.png
[cache-publish-app]: ./media/cache-web-app-howto/cache-publish-app.png
[cache-publish-to-app-service]: ./media/cache-web-app-howto/cache-publish-to-app-service.png
[cache-select-web-app]: ./media/cache-web-app-howto/cache-select-web-app.png
[cache-publish]: ./media/cache-web-app-howto/cache-publish.png
[cache-delete-resource-group]: ./media/cache-web-app-howto/cache-delete-resource-group.png
[cache-delete-confirm]: ./media/cache-web-app-howto/cache-delete-confirm.png

