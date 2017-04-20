<properties
    pageTitle="Comment travailler avec le Kit de développement logiciel du serveur principal .NET pour les applications mobiles | Service application Azure"
    description="Apprendre à utiliser avec le Kit de développement logiciel du serveur principal .NET pour Azure Application Service Mobile actuelles."
    keywords="service d’application, service application azure, l’application mobile, service mobile, échelle, format SVG, application, azure application déploiement"
    services="app-service\mobile"
    documentationCenter=""
    authors="adrianhall"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-multiple"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="work-with-the-net-backend-server-sdk-for-azure-mobile-apps"></a>Utilisez le Kit de développement logiciel du serveur principal .NET pour applications Mobile Azure

[AZURE.INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]

Cette rubrique vous montre comment utiliser le serveur principal de .NET SDK dans les scénarios Azure Application Service Mobile applications clés. Le Kit de développement Azure Mobile applications vous permet de travailler avec les clients mobiles à partir de votre application ASP.NET.

>[AZURE.TIP] Le [serveur .NET SDK pour les applications Mobile Azure] [ 2] est open source sur GitHub. Le référentiel contient tout le code source, y compris la suite de tests serveur entier SDK unité et quelques exemples de projets.

## <a name="reference-documentation"></a>Documentation de référence

La documentation de référence pour le serveur SDK se trouve ici : [Azure Mobile applications .NET référence][1].

## <a name="create-app"></a>Comment : créer une application Mobile .NET principale

Si vous démarrez un nouveau projet, vous pouvez créer une application de Service d’application via le [portail Azure] ou Visual Studio. Vous pouvez exécuter l’application de Service d’application localement ou publiez le projet dans votre application mobile application Service sur le nuage.  

Si vous ajoutez des fonctionnalités mobiles à un projet existant, consultez la section [téléchargement et l’initialisation le Kit de développement](#install-sdk) .

### <a name="create-a-net-backend-using-the-azure-portal"></a>Créer un serveur principal .NET à l’aide du portail Azure

Pour créer un serveur principal de Service d’application mobile, soit suivre le [didacticiel de démarrage rapide] [ 3] ou procédez comme suit :

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service-classic](../../includes/app-service-mobile-dotnet-backend-create-new-service-classic.md)]

Dans la carte de _mise en route_ , sous **créer une table API**, choisissez **c#** comme votre **langue principale**. Cliquez sur **Télécharger**, extraire les fichiers de projet compressés sur votre ordinateur local et ouvrez la solution dans Visual Studio.

### <a name="create-a-net-backend-using-visual-studio-2013-and-visual-studio-2015"></a>Créer un serveur principal .NET à l’aide de Visual Studio 2013 et Visual Studio 2015

Installez le [Kit de développement logiciel Azure pour .NET] [ 4] (version 2.9.0 ou version ultérieure) pour créer un projet d’applications Mobile Azure dans Visual Studio. Une fois que vous avez installé le Kit de développement, créer une application ASP.NET procédant comme suit :

1. Ouvrir la boîte de dialogue **Nouveau projet** (à partir du *fichier* > **Nouveau** > **projet...**).
2. Développez **modèles** > **Visual c#**et sélectionnez **Web**.
3. Sélectionnez **l’Application Web ASP.NET**.
4. Renseignez le nom du projet. Cliquez ensuite sur **OK**.
5. Sous _ASP.NET 4.5.2 modèles_, sélectionnez **l’Application Mobile Azure**. Vérifier l' **hôte dans le cloud** pour créer un serveur principal mobile dans le cloud à laquelle vous pouvez publier ce projet.
6. Cliquez sur **OK**.

## <a name="install-sdk"></a>Comment : Téléchargez et initialisation le Kit de développement

Le Kit de développement est disponible sur [NuGet.org]. Ce package inclut les fonctionnalités de base requises pour commencer à utiliser le Kit de développement. Pour initialisation le Kit de développement, vous devez effectuer des actions sur l’objet **HttpConfiguration** .

### <a name="install-the-sdk"></a>Installez le Kit de développement

Pour installer le Kit de développement, avec le bouton droit sur le projet serveur dans Visual Studio, sélectionnez **Manage NuGet Packages**, rechercher le package [Microsoft.Azure.Mobile.Server] , puis cliquez sur **installer**.

###<a name="server-project-setup"></a>Initialisation du projet serveur

Un projet de serveur principal .NET est initialisé similaire à d’autres projets ASP.NET, en incluant une classe de démarrage OWIN. Assurez-vous que vous avez référencé le package NuGet `Microsoft.Owin.Host.SystemWeb`. Pour ajouter cette classe dans Visual Studio, avec le bouton droit sur votre projet serveur, puis sélectionnez **Ajouter** > 
**Un nouvel élément**, puis **Web** > **Général** > **démarrage OWIN cours**.  Un cours est créé avec l’attribut suivant :

    [assembly: OwinStartup(typeof(YourServiceName.YourStartupClassName))]

Dans la `Configuration()` méthode de votre classe de démarrage OWIN, utilisez un objet **HttpConfiguration** pour configurer l’environnement d’applications Mobile Azure.
L’exemple suivant initialise le projet serveur avec aucune fonctionnalités ajoutées :

    // in OWIN startup class
    public void Configuration(IAppBuilder app)
    {
        HttpConfiguration config = new HttpConfiguration();

        new MobileAppConfiguration()
            // no added features
            .ApplyTo(config);

        app.UseWebApi(config);
    }

Pour activer les fonctionnalités individuelles, vous devez appeler les méthodes d’extension sur l’objet **MobileAppConfiguration** avant **ApplyTo**. Par exemple, le code suivant ajoute les itinéraires par défaut pour tous les contrôleurs API qui ont l’attribut `[MobileAppController]` lors de l’initialisation :

    new MobileAppConfiguration()
        .MapApiControllers()
        .ApplyTo(config);

Démarrage rapide pour les serveurs à partir du portail Azure appelle **UseDefaultConfiguration()**. Cette équivaut à la configuration suivante :

        new MobileAppConfiguration()
            .AddMobileAppHomeController()             // from the Home package
            .MapApiControllers()
            .AddTables(                               // from the Tables package
                new MobileAppTableConfiguration()
                    .MapTableControllers()
                    .AddEntityFramework()             // from the Entity package
                )
            .AddPushNotifications()                   // from the Notifications package
            .MapLegacyCrossDomainController()         // from the CrossDomain package
            .ApplyTo(config);

Les méthodes d’extension utilisées sont :

* `AddMobileAppHomeController()`Fournit la page d’accueil par défaut applications Mobile Azure.
* `MapApiControllers()`Fournit des fonctions de l’API personnalisées pour WebAPI contrôleurs décorée avec la `[MobileAppController]` attribut.
* `AddTables()`fournit un mappage de la `/tables` contrôleurs de table des points de terminaison.
* `AddTablesWithEntityFramework()`est une courte disponible pour le mappage de la `/tables` contrôleurs basés sur des points de terminaison de l’aide d’infrastructure entité.
* `AddPushNotifications()`Fournit une méthode simple d’inscription des appareils pour Hubs de Notification.
* `MapLegacyCrossDomainController()`Fournit des en-têtes CORS standard pour le développement local.

### <a name="sdk-extensions"></a>Extensions SDK

Les packages d’extension NuGet suivants fournissent différentes fonctionnalités mobiles qui peuvent être utilisées par votre application. Vous activez les extensions lors de l’initialisation à l’aide de l’objet **MobileAppConfiguration** .

- [Microsoft.Azure.Mobile.Server.Quickstart] prend en charge la configuration d’applications Mobile simple. Ajouté à la configuration en appelant la méthode d’extension **UseDefaultConfiguration** lors de l’initialisation. Cette extension contient les extensions de suivantes : les Notifications, l’authentification, entité, Tables, domaines et les packages d’accueil. Ce logiciel est utilisé par le démarrage rapide d’applications mobiles disponibles sur le portail Azure.

- [Microsoft.Azure.Mobile.Server.Home](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Home/) 
   mettent en œuvre, la valeur par défaut *cette application mobile est en cours d’exécution page* pour la racine du site web. Ajouter à la configuration en appelant la méthode d’extension   **AddMobileAppHomeController** .

- [Microsoft.Azure.Mobile.Server.Tables](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Tables/) 
   inclut des classes pour l’utilisation de données et le pipeline de données de configuration. Ajouter à la configuration en appelant la méthode d’extension **AddTables** .

- [Microsoft.Azure.Mobile.Server.Entity](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Entity/) 
   permet à l’infrastructure entité pour accéder aux données dans la base de données SQL. Ajouter à la configuration en appelant la méthode d’extension **AddTablesWithEntityFramework** .

- [Microsoft.Azure.Mobile.Server.Authentication] Active l’authentification et logiciels intermédiaires OWIN utilisé pour valider les jetons de configuration. Ajouter à la configuration en appelant **AddAppServiceAuthentication**  
   et **IAppBuilder**. Méthodes d’extension **UseAppServiceAuthentication** .

- [Microsoft.Azure.Mobile.Server.Notifications] Active les notifications de transmission et définit un point de terminaison push d’inscription. Ajouter à la configuration en appelant la méthode d’extension **AddPushNotifications** .

- [Microsoft.Azure.Mobile.Server.CrossDomain](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.CrossDomain/) 
   crée un contrôleur qui gère les données dans les navigateurs web héritées à partir de votre application Mobile. Ajouter à la configuration en appelant la méthode d’extension   **MapLegacyCrossDomainController** .

- [Microsoft.Azure.Mobile.Server.Login] fournit la méthode AppServiceLoginHandler.CreateToken(), qui est une méthode statique utilisée dans le cadre des scénarios d’authentification personnalisé.   

## <a name="publish-server-project"></a>Comment : publier le projet serveur

Cette section vous montre comment publier votre projet principal .NET de Visual Studio. Vous pouvez également déployer votre projet principal à l’aide de Git ou une des autres méthodes décrites dans la [documentation de déploiement d’Azure Application Service](../app-service-web/web-sites-deploy.md).

1. Dans Visual Studio, régénérer le projet pour restaurer packages NuGet.

2. Dans l’Explorateur, droit sur le projet, cliquez sur **Publier**. La première fois que vous publiez, vous devez définir un profil de publication. Lorsque vous avez déjà un profil défini, vous pouvez sélectionner et cliquez sur **Publier**.

2. Si vous êtes invité à sélectionner une cible de publication, cliquez sur **Microsoft Azure Application Service** > **suivante**, puis (si nécessaire) se connecter avec vos informations d’identification Azure. 
   Visual Studio télécharge et stocke en toute sécurité vos paramètres directement à partir d’Azure de publication.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-1.png)

3. Choisissez votre **abonnement**, sélectionnez le **Type de ressource** de **l’affichage**, développez l' **Application Mobile**et cliquez sur la principale application Mobile, puis cliquez sur **OK**.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-2.png)

4. Vérifier les informations de profil de publication, cliquez sur **Publier**.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-3.png)

    Lorsque la principale application Mobile a publié avec succès, vous voyez une page d’accueil indiquant succès.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-success.png)

##<a name="define-table-controller"></a>Comment : définir un contrôleur de tableau

Définir un contrôleur de Table pour exposer une table SQL pour les clients mobiles.  Configuration d’un contrôleur de tableau requiert trois étapes :

1. Crée un objet de transfert de données (DTO).
2. Configurer une référence de table dans la classe DbContext Mobile.
3. Créez un contrôleur de tableau.

Un objet de transfert de données (DTO) est un objet c# simple qui hérite `EntityData`.  Par exemple :

    public class TodoItem : EntityData
    {
        public string Text { get; set; }
        public bool Complete {get; set;}
    }

Le DTO est utilisé pour définir la table dans la base de données SQL.  Pour créer l’entrée de base de données, ajoutez un `DbSet<>` propriété la DbContext que vous utilisez.  Dans le modèle de projet par défaut pour les applications Mobile Azure la DbContext est appelé `Models\MobileServiceContext.cs`:

    public class MobileServiceContext : DbContext
    {
        private const string connectionStringName = "Name=MS_TableConnectionString";

        public MobileServiceContext() : base(connectionStringName)
        {

        }

        public DbSet<TodoItem> TodoItems { get; set; }

        protected override void OnModelCreating(DbModelBuilder modelBuilder)
        {
            modelBuilder.Conventions.Add(
                new AttributeToColumnAnnotationConvention<TableColumnAttribute, string>(
                    "ServiceColumnTable", (property, attributes) => attributes.Single().ColumnType.ToString()));
        }
    }

Si vous avez installé le kit SDK Azure, vous pouvez désormais créer un contrôleur de table modèle comme suit :

1. Avec le bouton droit sur le dossier Controllers, puis sélectionnez **Ajouter** > **contrôleur...**.
2. Sélectionnez l’option **Contrôleur de Table Azure Mobile applications** , puis cliquez sur **Ajouter**.
3. Dans la boîte de dialogue **Ajouter un contrôleur** :
    * Dans la liste déroulante **classe du modèle** , sélectionnez votre nouveau DTO.
    * Dans la liste déroulante **DbContext** , sélectionnez la classe DbContext de Service Mobile.
    * Le nom du contrôleur est créé pour vous.
4. Cliquez sur **Ajouter**.

Le projet de serveur de démarrage rapide contient un exemple d’un simple **TodoItemController**.

### <a name="how-to-adjust-the-table-paging-size"></a>Comment : ajuster la taille de pagination du tableau

Par défaut, les applications Mobile Azure renvoie 50 enregistrements par demande.  Pagination garantit que le client n’occupe pas leur thread de l’interface utilisateur ni le serveur trop longtemps, assurer une bonne expérience. Pour modifier la taille de la table pagination, augmenter le côté serveur « autorisé la taille de la requête » et taille de la page côté client côté serveur « autorisé la taille de la requête » est ajusté à l’aide de la `EnableQuery` attribut :

    [EnableQuery(PageSize = 500)]

Assurez-vous PageSize est le même ou supérieure à la taille demandée par le client.  Reportez-vous à ce client documentation faire pour plus d’informations sur la modification de la taille de la page client.

## <a name="how-to-define-a-custom-api-controller"></a>Comment : définir un contrôleur API personnalisé

Le contrôleur API personnalisé fournit les fonctionnalités essentielles à votre serveur principal application Mobile en exposant un point de terminaison. Vous pouvez enregistrer un contrôleur de l’API mobiles spécifiques à l’aide de l’attribut [MobileAppController]. La `MobileAppController` attribut enregistre l’itinéraire, configure le sérialiseur JSON d’applications Mobile et Active la [vérification de version client](app-service-mobile-client-and-server-versioning.md).

1. Dans Visual Studio, cliquez sur le dossier contrôleurs, puis cliquez sur **Ajouter** > **contrôleur**, sélectionnez **contrôleur de 2 API Web&mdash;vide** et cliquez sur **Ajouter**.

2. Fournir un **nom de contrôleur**, tel que `CustomController`, puis cliquez sur **Ajouter**.

3. Dans le nouveau fichier de classe contrôleur, ajoutez les éléments suivants à l’aide de déclaration :

        using Microsoft.Azure.Mobile.Server.Config;

4. Appliquer l’attribut **[MobileAppController]** pour la définition de classe contrôleur API, comme dans l’exemple suivant :

        [MobileAppController]
        public class CustomController : ApiController
        {
              //...
        }

4. Dans un fichier App_Start/Startup.MobileApp.cs, ajoutez un appel à la méthode d’extension **MapApiControllers** , comme dans l’exemple suivant :

        new MobileAppConfiguration()
            .MapApiControllers()
            .ApplyTo(config);

Vous pouvez également utiliser la `UseDefaultConfiguration()` méthode d’extension au lieu de `MapApiControllers()`. N’importe quel contrôleur qui n’a pas **MobileAppControllerAttribute** appliqués est toujours accessibles par les clients, mais il ne peut pas être correctement utilisé par les clients à l’aide de n’importe quel client de l’application Mobile SDK.

## <a name="how-to-work-with-authentication"></a>Comment : utiliser l’authentification

Azure applications Mobile utilise l’application Service authentification / autorisation pour sécuriser la principale mobile.  Cette section vous montre comment effectuer les tâches suivantes liés à l’authentification dans votre projet de serveur principal .NET :

+ [Comment : ajouter une authentification à un projet serveur](#add-auth)
+ [Comment : utiliser l’authentification personnalisée pour votre application](#custom-auth)
+ [Comment : extraire authentifié informations utilisateur](#user-info)
+ [Comment : limiter l’accès aux données pour les utilisateurs autorisés](#authorize)

### <a name="add-auth"></a>Comment : ajouter une authentification à un projet serveur

Vous pouvez ajouter l’authentification à votre projet serveur en étendant l’objet **MobileAppConfiguration** et en configurant des logiciels intermédiaires OWIN. Lorsque vous installez le package [Microsoft.Azure.Mobile.Server.Quickstart] et appelez la méthode d’extension **UseDefaultConfiguration** , vous pouvez passez à l’étape 3.

1. Dans Visual Studio, installez le package [Microsoft.Azure.Mobile.Server.Authentication] .

2. Dans le fichier de projet Startup.cs, ajoutez la ligne suivante de code au début de la méthode de **Configuration** :

        app.UseAppServiceAuthentication(config);

    Ce composant logiciels intermédiaires OWIN valide les jetons émis par la passerelle de Service d’application associée.

3. Ajouter la `[Authorize]` attribut à n’importe quel contrôleur ou une méthode qui requiert une authentification. 

Pour en savoir plus sur l’authentification des clients à votre version de serveur d’applications Mobile, voir [Ajouter l’authentification dans votre application](app-service-mobile-ios-get-started-users.md).

### <a name="custom-auth"></a>Comment : utiliser l’authentification personnalisée pour votre application

Si vous ne souhaitez pas utiliser un des fournisseurs d’authentification/autorisation du Service application, vous pouvez implémenter votre propre système de connexion. Installez le package [Microsoft.Azure.Mobile.Server.Login] pour vous aider à génération jeton d’authentification.  Fournir votre propre code de validation des informations d’identification utilisateur. Par exemple, vous pouvez vérifier contre les mots de passe salés et hachés dans une base de données. Dans l’exemple ci-dessous, la `isValidAssertion()` méthode (défini ailleurs) est chargée de ces contrôles.

L’authentification personnalisée est exposée en créant un ApiController et exposant `register` et `login` actions. Le client doit utiliser une interface utilisateur personnalisée pour collecter les informations de l’utilisateur.  Les informations sont envoyées puis à l’API avec un appel HTTP POST standard. Une fois que le serveur valide l’assertion, un jeton est émis à l’aide de la `AppServiceLoginHandler.CreateToken()` méthode.  L’utilisation **ne doit pas** ApiController la `[MobileAppController]` attribut. 

Exemple `login` action :

        public IHttpActionResult Post([FromBody] JObject assertion)
        {
            if (isValidAssertion(assertion)) // user-defined function, checks against a database
            {
                JwtSecurityToken token = AppServiceLoginHandler.CreateToken(new Claim[] { new Claim(JwtRegisteredClaimNames.Sub, assertion["username"]) },
                    mySigningKey,
                    myAppURL,
                    myAppURL,
                    TimeSpan.FromHours(24) );
                return Ok(new LoginResult()
                {
                    AuthenticationToken = token.RawData,
                    User = new LoginResultUser() { UserId = userName.ToString() }
                });
            }
            else // user assertion was not valid
            {
                return this.Request.CreateUnauthorizedResponse();
            }
        }

Dans l’exemple précédent, LoginResult et LoginResultUser sont des objets sérialisables exposer les propriétés requises. Le client attend des réponses à retourner en tant qu’objets JSON de l’écran de connexion :

        {
            "authenticationToken": "<token>",
            "user": {
                "userId": "<userId>"
            }
        }

La `AppServiceLoginHandler.CreateToken()` méthode inclut un paramètre de _l’émetteur_ et une _audience_ . Ces deux paramètres sont définies sur l’URL de la racine de votre application, à l’aide du schéma HTTPS. De même, vous devez définir _secretKey_ à que la valeur de votre application de la clé de signature. Ne pas distribuer la clé de signature dans un client tel qu’il peut être utilisé à pourra pas aller à touches et emprunt d’identité des utilisateurs. Vous pouvez obtenir la clé de signature pendant hébergé dans le Service d’application en faisant référence à la _site Web\_AUTH\_signature\_clé_ variable d’environnement. Si nécessaire dans un contexte de débogage local, suivez les instructions dans la section [débogage Local avec authentification](#local-debug) pour récupérer la clé et le stocker comme paramètre d’application.

Le jeton émis peut-être également inclure d’autres créances et une date d’expiration.  Au minimum, le jeton émis doit inclure une demande d’objet (**sub**).

Vous pouvez prendre en charge le client standard `loginAsync()` méthode par surcharger l’itinéraire d’authentification.  Si le client appelle `client.loginAsync('custom');` pour vous connecter, votre itinéraire doit être `/.auth/login/custom`.  Vous pouvez définir l’itinéraire du contrôleur d’authentification personnalisé à l’aide `MapHttpRoute()`:

    config.Routes.MapHttpRoute("custom", ".auth/login/custom", new { controller = "CustomAuth" });

>[AZURE.TIP] À l’aide de la `loginAsync()` approche garantit que le jeton d’authentification est relié à chaque appel ultérieur du service.

###<a name="user-info"></a>Comment : extraire authentifié informations utilisateur

Lorsqu’un utilisateur est authentifié par application de Service, vous pouvez accéder à l’ID utilisateur affecté et autres informations dans votre code principal .NET. Les informations utilisateur peuvent être utilisées pour prendre des décisions d’autorisation dans le système principal. Le code suivant obtient l’ID utilisateur associé à une demande :

    // Get the SID of the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;

L’identificateur de sécurité est dérivé de l’ID utilisateur spécifiques du fournisseur et est statique pour un utilisateur donné et un fournisseur de connexion.  L’identificateur de sécurité est null pour les jetons d’authentification non valides.

Service d’application vous permet également de demander des objections spécifiques à votre fournisseur de connexion. Chaque fournisseur d’identité peut fournir plus d’informations à l’aide du fournisseur d’identité SDK.  Par exemple, vous pouvez utiliser l’API Graph Facebook pour plus d’informations de vos amis.  Vous pouvez spécifier les revendications qui sont demandées dans la carte de fournisseur dans le portail Azure. Certaines revendications nécessitent une configuration supplémentaire avec le fournisseur d’identité.

Le code suivant appelle la méthode d’extension **GetAppServiceIdentityAsync** pour obtenir les informations d’identification de connexion, qui incluent le jeton d’accès nécessaire pour effectuer des requêtes par rapport à l’API Graph Facebook :

    // Get the credentials for the logged-in user.
    var credentials =
        await this.User
        .GetAppServiceIdentityAsync<FacebookCredentials>(this.Request);

    if (credentials.Provider == "Facebook")
    {
        // Create a query string with the Facebook access token.
        var fbRequestUrl = "https://graph.facebook.com/me/feed?access_token="
            + credentials.AccessToken;

        // Create an HttpClient request.
        var client = new System.Net.Http.HttpClient();

        // Request the current user info from Facebook.
        var resp = await client.GetAsync(fbRequestUrl);
        resp.EnsureSuccessStatusCode();

        // Do something here with the Facebook user information.
        var fbInfo = await resp.Content.ReadAsStringAsync();
    }

Ajouter un à l’aide de déclaration `System.Security.Principal` pour fournir la méthode d’extension **GetAppServiceIdentityAsync** .

### <a name="authorize"></a>Comment : limiter l’accès aux données pour les utilisateurs autorisés

Dans la section précédente, nous avons montré comment récupérer l’ID utilisateur d’un utilisateur authentifié. Vous pouvez limiter l’accès aux données et autres ressources en fonction de cette valeur. Par exemple, ajout d’une colonne ID utilisateur aux tables et le filtrage des résultats de la requête par l’ID utilisateur sont un moyen simple pour limiter les données renvoyées uniquement aux utilisateurs autorisés. Le code suivant renvoie des lignes de données uniquement lorsque l’identificateur de sécurité correspond à la valeur dans la colonne ID utilisateur sur la table TodoItem :

    // Get the SID of the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;

    // Only return data rows that belong to the current user.
    return Query().Where(t => t.UserId == sid);

La `Query()` méthode renvoie un `IQueryable` qui peut être modifié par LINQ pour gérer le filtrage.

## <a name="how-to-add-push-notifications-to-a-server-project"></a>Comment : ajouter push notifications à un projet serveur

Ajouter les notifications push à votre projet serveur en étendant l’objet **MobileAppConfiguration** et création d’un client Hubs de Notification.

1. Dans Visual Studio, avec le bouton droit de la project server et cliquez sur **Gérer les Packages NuGet**, recherchez `Microsoft.Azure.Mobile.Server.Notifications`, puis cliquez sur **installer**. 

2. Répétez cette étape pour installer le `Microsoft.Azure.NotificationHubs` package, qui inclut la bibliothèque de client Hubs de Notification.

3. Dans App_Start/Startup.MobileApp.cs et ajoutez un appel à la méthode d’extension **AddPushNotifications()** lors de l’initialisation :

        new MobileAppConfiguration()
            // other features...
            .AddPushNotifications()
            .ApplyTo(config);

4. Ajouter le code suivant crée un client Hubs de Notification :

        // Get the settings for the server project.
        HttpConfiguration config = this.Configuration;
        MobileAppSettingsDictionary settings =
            config.GetMobileAppSettingsProvider().GetMobileAppSettings();

        // Get the Notification Hubs credentials for the Mobile App.
        string notificationHubName = settings.NotificationHubName;
        string notificationHubConnection = settings
            .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // Create a new Notification Hub client.
        NotificationHubClient hub = NotificationHubClient
            .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

Vous pouvez désormais utiliser le client Hubs de Notification pour envoyer les notifications push pour les appareils inscrits. Pour plus d’informations, voir [les notifications push ajouter dans votre application](app-service-mobile-ios-get-started-push.md). Pour en savoir plus sur la Notification Hubs, voir [Vue d’ensemble des Hubs de Notification](../notification-hubs/notification-hubs-push-notification-overview.md).

##<a name="tags"></a>Comment : activer destinée aux push à l’aide de balises

Notification Hubs vous permet d’envoyer des notifications ciblées pour les enregistrements spécifiques à l’aide de balises. Plusieurs balises sont automatiquement créés :

* L’ID d’Installation identifie un périphérique spécifique.
* L’Id d’utilisateur en fonction de l’identificateur de sécurité authentifié identifie un utilisateur spécifique.

L’ID d’installation sont accessibles à partir de la propriété **ID** sur la **MobileServiceClient**.  L’exemple suivant montre comment utiliser l’ID d’installation pour ajouter un indicateur à une installation spécifique dans Hubs de Notification :

    hub.PatchInstallation("my-installation-id", new[]
    {
        new PartialUpdateOperation
        {
            Operation = UpdateOperationType.Add,
            Path = "/tags",
            Value = "{my-tag}"
        }
    });

Les balises fournis par le client lors de l’inscription de notification push sont ignorées par le serveur principal lors de la création de l’installation. Pour activer un client ajouter des balises à l’installation, vous devez créer une API personnalisée qui ajoute des balises à l’aide du modèle précédent. 

Voir les [indicateurs de notification push Client ajouté] [ 5] dans l’exemple de démarrage rapide terminé application Service Mobile applications pour obtenir un exemple.

##<a name="push-user"></a>Comment : envoyer des notifications push pour un utilisateur authentifié

Lorsqu’un utilisateur authentifié enregistre pour les notifications push, une balise ID utilisateur est ajoutée automatiquement à l’enregistrement. À l’aide de cette balise, vous pouvez envoyer les notifications push pour tous les périphériques inscrites à cette personne. Le code suivant obtient l’identificateur de sécurité de l’utilisateur qui effectue la demande et envoie une notification de push modèle à chaque enregistrement appareil pour cette personne :

    // Get the current user SID and create a tag for the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;
    string userTag = "_UserId:" + sid;

    // Build a dictionary for the template with the item message text.
    var notification = new Dictionary<string, string> { { "message", item.Text } };

    // Send a template notification to the user ID.
    await hub.SendTemplateNotificationAsync(notification, userTag);

Lorsque vous enregistrez les notifications push à partir d’un client authentifié, vérifiez que l’authentification est terminée avant d’essayer d’inscription. Pour plus d’informations, voir [Activer pour les utilisateurs] [ 6] dans l’exemple de démarrage rapide terminé application Service Mobile applications pour .NET principal.

## <a name="how-to-debug-and-troubleshoot-the-net-server-sdk"></a>Comment : déboguer et dépanner le Kit de développement .NET Server

Azure Application Service fournit plusieurs débogage et résolution des problèmes techniques pour les applications ASP.NET :

- [Analyse d’un Service d’application Azure](../app-service-web/web-sites-monitor.md)
- [Activer la journalisation des diagnostics dans le Service d’application Azure](../app-service-web/web-sites-enable-diagnostic-log.md)
- [Résoudre les problèmes d’un Service d’application Azure dans Visual Studio](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md)

### <a name="logging"></a>Journalisation

Vous pouvez écrire dans les journaux de diagnostic application Service à l’aide de l’écriture de trace ASP.NET standard. Avant de pouvoir écrire dans les journaux, vous devez activer diagnostics dans votre système principal de l’application Mobile.

Pour activer les diagnostics et écrire dans les journaux :

1. Suivez les étapes de [l’activation des diagnostics](../app-service-web/web-sites-enable-diagnostic-log.md#enablediag).

2. Ajoutez les éléments suivants à l’aide d’instruction dans votre fichier de code :

        using System.Web.Http.Tracing;

3. Créer un writer trace pour écrire à partir du serveur principal .NET dans les journaux de diagnostic, comme suit :

        ITraceWriter traceWriter = this.Configuration.Services.GetTraceWriter();
        traceWriter.Info("Hello, World");

4. Publier à nouveau votre projet server et accéder à la principale application Mobile pour exécuter le chemin d’accès de code avec l’enregistrement.

5. Téléchargez et évaluez les journaux, comme décrit dans [Comment : télécharger les journaux](../app-service-web/web-sites-enable-diagnostic-log.md#download).

### <a name="local-debug"></a>Débogage local avec authentification

Vous pouvez exécuter votre application localement pour tester des modifications avant de les publier dans le cloud. Pour les serveurs principaux de la plupart des applications Mobile Azure, appuyez sur *F5* dans Visual Studio. Il existe cependant certaines considérations supplémentaires lors de l’utilisation de l’authentification.

Vous devez disposer d’une application mobile sur le nuage avec application de Service d’authentification/autorisation configurée, et votre client doit avoir le point de terminaison cloud spécifié en tant que l’hôte de remplacement. Consultez la documentation de votre plate-forme client pour les étapes spécifiques nécessaires.

Assurez-vous que votre serveur principal mobile a [Microsoft.Azure.Mobile.Server.Authentication] installé. Puis, dans la classe de démarrage de votre application OWIN, ajoutez le code suivant, après `MobileAppConfiguration` a été appliqué à votre `HttpConfiguration`:

        app.UseAppServiceAuthentication(new AppServiceAuthenticationOptions()
        {
            SigningKey = ConfigurationManager.AppSettings["authSigningKey"],
            ValidAudiences = new[] { ConfigurationManager.AppSettings["authAudience"] },
            ValidIssuers = new[] { ConfigurationManager.AppSettings["authIssuer"] },
            TokenHandler = config.GetAppServiceTokenHandler()
        });

Dans l’exemple précédent, vous devez configurer les paramètres d’application _authAudience_ et _authIssuer_ dans votre fichier Web.config à chacun l’URL de la racine de votre application, à l’aide du schéma HTTPS. De même, vous devez définir _authSigningKey_ à que la valeur de votre application de la clé de signature. Pour obtenir la clé de signature :

1. Accédez à votre application au sein du [portail Azure] 
2. Cliquez sur **Outils**, **Kudu**, **accédez**.
3. Dans le site Kudu gestion, cliquez sur **environnement**.
4. Rechercher la valeur pour _site Web\_AUTH\_signature\_clé_. 

Utiliser la clé de signature pour le paramètre _authSigningKey_ dans votre fichier config application locale.  La principale mobile est maintenant équipée pour valider les jetons s’exécute en local, le client obtient le jeton du point de terminaison sur le nuage.

[1]: https://msdn.microsoft.com/library/azure/dn961176.aspx
[2]: https://github.com/Azure/azure-mobile-apps-net-server
[3]: app-service-mobile-ios-get-started.md
[4]: https://azure.microsoft.com/downloads/
[5]: https://github.com/Azure-Samples/app-service-mobile-dotnet-backend-quickstart/blob/master/README.md#client-added-push-notification-tags
[6]: https://github.com/Azure-Samples/app-service-mobile-dotnet-backend-quickstart/blob/master/README.md#push-to-users
[Portail Azure]: https://portal.azure.com
[NuGet.org]: http://www.nuget.org/
[Microsoft.Azure.Mobile.Server]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/
[Microsoft.Azure.Mobile.Server.Quickstart]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Quickstart/
[Microsoft.Azure.Mobile.Server.Authentication]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Authentication/
[Microsoft.Azure.Mobile.Server.Login]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Login/
[Microsoft.Azure.Mobile.Server.Notifications]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Notifications/
[MapHttpAttributeRoutes]: https://msdn.microsoft.com/library/dn479134(v=vs.118).aspx

