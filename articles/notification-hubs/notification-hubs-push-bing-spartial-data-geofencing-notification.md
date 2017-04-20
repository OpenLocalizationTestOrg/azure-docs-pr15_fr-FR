<properties
    pageTitle="Notifications push extraits geo avec Hubs de Notification Azure et données spatiales Bing | Microsoft Azure"
    description="Dans ce didacticiel, vous allez apprendre à livrer des notifications push basée sur l’emplacement de concentrateur de Notification Azure et de données spatiales Bing."
    services="notification-hubs"
    documentationCenter="windows"
    keywords="notification d’émission, de notification d’émission"
    authors="dend"
    manager="yuaxu"
    editor="dend"/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows-phone"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="05/31/2016"
    ms.author="dendeli"/>
    
# <a name="geo-fenced-push-notifications-with-azure-notification-hubs-and-bing-spatial-data"></a>Notifications push extraits geo avec Hubs de Notification Azure et données spatiales Bing
 
 > [AZURE.NOTE] Pour effectuer ce didacticiel, vous devez disposer d’un compte Azure actif. Si vous n’avez pas un compte, vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Pour plus d’informations, voir [Azure la version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02).

Dans ce didacticiel, vous allez apprendre à livrer des notifications push basée sur l’emplacement de concentrateur de Notification Azure et de données spatiales Bing, exploité à partir d’une application de plateforme Windows universel.

##<a name="prerequisites"></a>Conditions préalables
Tout d’abord, vous devez vous assurer que vous disposez tous les logiciels et services composants requis :

* [Visual Studio 2015 mise à jour 1](https://www.visualstudio.com/en-us/downloads/download-visual-studio-vs.aspx) ou version ultérieure ([Community Edition](https://go.microsoft.com/fwlink/?LinkId=691978&clcid=0x409) fera également). 
* Version la plus récente du [Kit de développement logiciel Azure](https://azure.microsoft.com/downloads/). 
* [Centre de développement de cartes Bing compte](https://www.bingmapsportal.com/) (vous pouvez créer un gratuitement et associer à votre compte Microsoft). 

##<a name="getting-started"></a>Prise en main

Commençons par créer le projet. Dans Visual Studio, démarrez un nouveau projet de type **Application vierge (Windows universel)**.

![](./media/notification-hubs-geofence/notification-hubs-create-blank-app.png)

Une fois la création du projet est terminée, vous devez avoir l’atelier pour l’application elle-même. Maintenant nous allons définir tous les éléments de l’infrastructure geo-clôture. Étant donné que nous allons utiliser les services Bing pour cela, il est un point de terminaison API REST public qui nous permettent de cadres emplacement spécifique de la requête :

    http://spatial.virtualearth.net/REST/v1/data/
    
Vous devez spécifier les paramètres suivants pour la configuration :

* **ID de Source de données** et le **Nom de Source de données** – des API Bing Maps, des sources de données contiennent des métadonnées bucketed différents, tels que les emplacements et les horaires d’ouverture de l’opération. Vous pouvez en savoir plus sur ces ici. 
* **Nom de l’entité** – l’entité que vous souhaitez utiliser comme point de référence pour la notification. 
* **Clé de l’API Bing Maps** : il s’agit de la clé que vous avez obtenu précédemment lorsque vous avez créé le compte Centre de développement Bing.
 
Nous allons faire un approfondi sur le programme d’installation pour chacun des éléments ci-dessus.

##<a name="setting-up-the-data-source"></a>La configuration de la source de données

Vous pouvez le faire dans le centre de développement de cartes Bing. Simplement dans la barre de navigation supérieure, cliquez sur **sources de données** et sélectionnez **Gérer les Sources de données**.

![](./media/notification-hubs-geofence/bing-maps-manage-data.png)

Si vous n’avez pas travaillé avec l’API Bing Maps avant, il est probable ne comporter des sources de données actuelle, afin que vous pouvez de créer un nouvel identifiant en cliquant sur Télécharger des données à une source de données. Vérifiez que vous remplissez tous les champs requis :

![](./media/notification-hubs-geofence/bing-maps-create-data.png)

Vous vous demandez peut-être – qu’est le fichier de données et ce qui devriez vous être téléchargement ? Aux fins de ce test, vous pouvez utiliser simplement l’échantillon canaux de communication qui encadre une zone des gens de San Francisco :

    Bing Spatial Data Services, 1.0, TestBoundaries
    EntityID(Edm.String,primaryKey)|Name(Edm.String)|Longitude(Edm.Double)|Latitude(Edm.Double)|Boundary(Edm.Geography)
    1|SanFranciscoPier|||POLYGON ((-122.389825 37.776598,-122.389438 37.773087,-122.381885 37.771849,-122.382186 37.777022,-122.389825 37.776598))
    
Ce qui précède représente cette entité :

![](./media/notification-hubs-geofence/bing-maps-geofence.png)

Simplement copier et coller la chaîne ci-dessus dans un nouveau fichier enregistrer en tant que **NotificationHubsGeofence.pipe**et télécharger dans le centre de développement Bing.

>[AZURE.NOTE]Vous pouvez être invité à spécifier une nouvelle clé pour la **clé principale** qui est différente de la **Clé de requête**. Simplement créer une nouvelle clé via le tableau de bord et actualisez la page de téléchargement de source de données.

Une fois que vous téléchargez le fichier de données, vous avez besoin pour vous assurer que vous publiez la source de données. 

Accédez à **Gérer les Sources de données**, comme nous l’avons fait au-dessus, recherchez votre source de données dans la liste et dans la colonne **Actions** , cliquez sur **Publier** . Dans un peu, vous devriez voir votre source de données dans l’onglet **Sources de données publiées** :

![](./media/notification-hubs-geofence/bing-maps-published-data.png)

Si vous cliquez sur **Modifier**, vous ne pourrez pas voir en un clin de œil les emplacements qui nous introduites qu’il contient :

![](./media/notification-hubs-geofence/bing-maps-data-details.png)

À ce stade, le portail ne voyez pas les limites pour le geofence que nous avons créé – tout ce que nous avons besoin d’est une confirmation de l’emplacement spécifié se trouve à proximité droite.

Vous avez maintenant de la configuration requise pour la source de données. Pour en savoir plus sur l’URL de requête pour l’appel de l’API dans le centre de développement de cartes Bing, cliquez sur **sources de données** et sélectionnez les **Informations des sources de données**.

![](./media/notification-hubs-geofence/bing-maps-data-info.png)

L' **URL de la requête** est ce que nous sommes après ici. Voici le point de terminaison par rapport à laquelle nous pouvons exécuter des requêtes pour vérifier si l’appareil est actuellement dans les limites d’un emplacement ou non. Pour effectuer ce test, il suffit d’exécuter un appel GET par rapport à l’URL de la requête, avec les paramètres suivants ajoutés :

    ?spatialFilter=intersects(%27POINT%20LONGITUDE%20LATITUDE)%27)&$format=json&key=QUERY_KEY

Pointez de cette manière que vous spécifiez une cible qui nous obtenir de l’appareil et Bing Maps effectue automatiquement les calculs pour voir si elle se trouve dans le geofence. Une fois que vous exécutez la requête via un navigateur (ou ourlée), vous obtiendrez standard une réponse JSON :

![](./media/notification-hubs-geofence/bing-maps-json.png)

Cette réponse se produit uniquement lorsque le point est en réalité dans les limites désignés. Si elle n’est pas le cas, vous obtiendrez un pot vides **résultats** :

![](./media/notification-hubs-geofence/bing-maps-nores.png)

##<a name="setting-up-the-uwp-application"></a>La configuration de l’application UWP

Maintenant que nous avons la source de données prête, nous pouvons commencer à travailler sur l’application UWP qui nous amorcés précédemment.

Tout d’abord, nous devons activer les services d’emplacement pour notre application. Pour ce faire, double-cliquez sur `Package.appxmanifest` fichier dans **L’Explorateur**.

![](./media/notification-hubs-geofence/vs-package-manifest.png)

Dans l’onglet Propriétés de package que vous venez d’ouvrir, cliquez sur **fonctionnalités** et assurez-vous que vous sélectionnez **emplacement**:

![](./media/notification-hubs-geofence/vs-package-location.png)

Comme la fonctionnalité emplacement est déclarée, créez un nouveau dossier dans votre solution nommée `Core`et ajoutez un nouveau fichier dans ce dernier, appelé `LocationHelper.cs`:

![](./media/notification-hubs-geofence/vs-location-helper.png)

La `LocationHelper` classe elle-même est assez simple à ce stade : il ne fait nous permettent d’obtenir l’emplacement de l’utilisateur au sein du système API :

    using System;
    using System.Threading.Tasks;
    using Windows.Devices.Geolocation;

    namespace NotificationHubs.Geofence.Core
    {
        public class LocationHelper
        {
            private static readonly uint AppDesiredAccuracyInMeters = 10;

            public async static Task<Geoposition> GetCurrentLocation()
            {
                var accessStatus = await Geolocator.RequestAccessAsync();
                switch (accessStatus)
                {
                    case GeolocationAccessStatus.Allowed:
                        {
                            Geolocator geolocator = new Geolocator { DesiredAccuracyInMeters = AppDesiredAccuracyInMeters };

                            return await geolocator.GetGeopositionAsync();
                        }
                    default:
                        {
                            return null;
                        }
                }
            }

        }
    }

Vous pouvez en savoir plus sur l’obtention d’emplacement de l’utilisateur dans les applications UWP dans le [document MSDN](https://msdn.microsoft.com/library/windows/apps/mt219698.aspx)officiel.

Pour vérifier que l’acquisition d’emplacement fonctionne vraiment, ouvrez le côté de code de la page principale (`MainPage.xaml.cs`). Créer un nouveau gestionnaire d’événements pour les `Loaded` événement dans le `MainPage` constructeur :

    public MainPage()
    {
        this.InitializeComponent();
        this.Loaded += MainPage_Loaded;
    }

L’implémentation du Gestionnaire d’événements est la suivante :

    private async void MainPage_Loaded(object sender, RoutedEventArgs e)
    {
        var location = await LocationHelper.GetCurrentLocation();

        if (location != null)
        {
            Debug.WriteLine(string.Concat(location.Coordinate.Longitude,
                " ", location.Coordinate.Latitude));
        }
    }

Notez que nous avons déclaré le gestionnaire asynchrones, car `GetCurrentLocation` est await et par conséquent a besoin pour être exécutées dans un contexte asynchrone. En outre, étant donné que dans certaines circonstances nous pouvons vous retrouver avec un emplacement nul (par exemple, l’emplacement de services sont désactivés ou l’application a été refusée autorisations d’accès à l’emplacement), nous avons besoin pour vous assurer qu’il est géré correctement avec un contrôle de valeur null.

Exécutez l’application. Vérifiez que vous autorisez l’accès de l’emplacement :

![](./media/notification-hubs-geofence/notification-hubs-location-access.png)

Une fois les démarrages d’applications, vous devez être en mesure de voir les coordonnées dans la fenêtre de **sortie** :

![](./media/notification-hubs-geofence/notification-hubs-location-output.png)

Vous savez maintenant qu’emplacement acquisition works – hésitez pas à supprimer le Gestionnaire d’événements test pour chargé, car nous ne l’utilise plus.

L’étape suivante consiste à capturer les modifications d’emplacement. Pour ce faire, revenons à la `LocationHelper` de cours et ajouter le Gestionnaire d’événements `PositionChanged`:

    geolocator.PositionChanged += Geolocator_PositionChanged;

La mise en œuvre affiche les coordonnées de l’emplacement de la fenêtre de **sortie** :

    private static async void Geolocator_PositionChanged(Geolocator sender, PositionChangedEventArgs args)
    {
        await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
        {
            Debug.WriteLine(string.Concat(args.Position.Coordinate.Longitude, " ", args.Position.Coordinate.Latitude));
        });
    }

##<a name="setting-up-the-backend"></a>La configuration du serveur principal

Télécharger l' [Échantillon de serveur principal .NET de GitHub](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/NotifyUsers). Une fois le téléchargement terminé, ouvrez la `NotifyUsers` dossier et par la suite – la `NotifyUsers.sln` fichier.

Définir la `AppBackend` project en tant que le **Projet de démarrage** et la lancer.

![](./media/notification-hubs-geofence/vs-startup-project.png)

Le projet est déjà configuré pour envoyer les notifications push aux périphériques cible, afin que nous allez devoir faire uniquement deux choses : Remplacez la bonne connexion de chaîne pour le hub de notification et ajouter identification limite pour envoyer la notification uniquement lorsque l’utilisateur se trouve dans le geofence.

Pour configurer la chaîne de connexion, dans le `Models` dossier ouvert `Notifications.cs`. La `NotificationHubClient.CreateClientFromConnectionString` fonction doit contenir les informations relatives à votre plateforme de notification que vous pouvez obtenir dans le [Portail Azure](https://portal.azure.com) (apparence à l’intérieur de la carte de **Stratégies d’accès** dans **les paramètres**). Enregistrez le fichier de configuration mis à jour.

Maintenant, nous avons besoin de créer un modèle pour le résultat de l’API Bing Maps. Le moyen le plus simple pour ce faire est avec le bouton droit sur le `Models` dossier, **Ajouter** > **cours**. Nommez-la `GeofenceBoundary.cs`. Une fois terminé, copiez le JSON à partir de la réponse de l’API que nous l’avons vu dans la première section et en cours d’utilisation de Visual Studio **Modifier** > **Collage spécial** > **JSON Coller en tant que Classes**. 

De cette façon, nous vous assurer que l’objet est désérialisé exactement telle qu’elle a été conçue. Votre ensemble de cours résultant doit ressembler à ceci :

    namespace AppBackend.Models
    {
        public class Rootobject
        {
            public D d { get; set; }
        }

        public class D
        {
            public string __copyright { get; set; }
            public Result[] results { get; set; }
        }

        public class Result
        {
            public __Metadata __metadata { get; set; }
            public string EntityID { get; set; }
            public string Name { get; set; }
            public float Longitude { get; set; }
            public float Latitude { get; set; }
            public string Boundary { get; set; }
            public string Confidence { get; set; }
            public string Locality { get; set; }
            public string AddressLine { get; set; }
            public string AdminDistrict { get; set; }
            public string CountryRegion { get; set; }
            public string PostalCode { get; set; }
        }

        public class __Metadata
        {
            public string uri { get; set; }
        }
    }

Ensuite, ouvrez `Controllers`  >  `NotificationsController.cs`. Nous avons besoin de redéfinir certains l’appel billet au compte de latitude et longitude cible. Pour ce faire, ajoutez simplement deux chaînes à la signature de la fonction – `latitude` et `longitude`.

    public async Task<HttpResponseMessage> Post(string pns, [FromBody]string message, string to_tag, string latitude, string longitude)

Créer une nouvelle classe au sein du projet appelé `ApiHelper.cs` – nous allons l’utiliser pour vous connecter à Bing pour vérifier pointez croisements limite. Mettre en œuvre une `IsPointWithinBounds` fonction, comme suit :

    public class ApiHelper
    {
        public static readonly string ApiEndpoint = "{YOUR_QUERY_ENDPOINT}?spatialFilter=intersects(%27POINT%20({0}%20{1})%27)&$format=json&key={2}";
        public static readonly string ApiKey = "{YOUR_API_KEY}";

        public static bool IsPointWithinBounds(string longitude,string latitude)
        {
            var json = new WebClient().DownloadString(string.Format(ApiEndpoint, longitude, latitude, ApiKey));
            var result = JsonConvert.DeserializeObject<Rootobject>(json);
            if (result.d.results != null && result.d.results.Count() > 0)
            {
                return true;
            }
            else
            {
                return false;
            }
        }
    }

>[AZURE.NOTE] Veillez à remplacer le point de terminaison API avec l’URL de requête que vous avez obtenu précédemment à partir du centre de développement de Bing (même s’applique à la clé de l’API). 

S’il existe des résultats de la requête, cela signifie que le point spécifié se trouve dans les limites de la geofence, afin que nous retourner `true`. S’il n’y a aucun résultat, Bing est nous indiquant que le point est en dehors du cadre de la recherche, afin que nous retourner `false`.

Dans `NotificationsController.cs`, créez un chèque juste avant l’instruction switch :

    if (ApiHelper.IsPointWithinBounds(longitude, latitude))
    {
        switch (pns.ToLower())
        {
            case "wns":
                //// Windows 8.1 / Windows Phone 8.1
                var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
                            "From " + user + ": " + message + "</text></binding></visual></toast>";
                outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

                // Windows 10 specific Action Center support
                toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
                            "From " + user + ": " + message + "</text></binding></visual></toast>";
                outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

                break;
        }
    }

De cette façon, la notification est envoyée uniquement lorsque le point est dans les limites.

##<a name="testing-push-notifications-in-the-uwp-app"></a>Tester les notifications push dans l’application UWP

Pour revenir à l’application UWP, nous devons maintenant pouvoir tester les notifications. Dans la `LocationHelper` de classes, créez une nouvelle fonction – `SendLocationToBackend`:

    public static async Task SendLocationToBackend(string pns, string userTag, string message, string latitude, string longitude)
    {
        var POST_URL = "http://localhost:8741/api/notifications?pns=" +
            pns + "&to_tag=" + userTag + "&latitude=" + latitude + "&longitude=" + longitude;

        using (var httpClient = new HttpClient())
        {
            try
            {
                await httpClient.PostAsync(POST_URL, new StringContent("\"" + message + "\"",
                    System.Text.Encoding.UTF8, "application/json"));
            }
            catch (Exception ex)
            {
                Debug.WriteLine(ex.Message);
            }
        }
    }

>[AZURE.NOTE] Remplacez le `POST_URL` à l’emplacement de votre application web déployé que nous avons créé dans la section précédente. Pour l’instant, il est OK pour exécuter localement, mais lorsque vous travaillez sur le déploiement d’une version publique, vous devez héberger avec un fournisseur externe.

Nous allons maintenant Assurez-vous que nous allons enregistrer l’application UWP pour les notifications push. Dans Visual Studio, cliquez sur **projet** > **stocker** > **associer avec le magasin de l’application**.

![](./media/notification-hubs-geofence/vs-associate-with-store.png)

Une fois que vous êtes connecté à votre compte pour les développeurs, vérifiez que vous sélectionnez une application existante ou créez un nouveau et lui associez le package. 

Accédez au centre de développement et ouvrez l’application que vous venez de créer. Cliquez sur **Services** > **Notifications de transmission** > **site Services Live**.

![](./media/notification-hubs-geofence/ms-live-services.png)

Sur le site, prenez note de l' **Application Secret** et l' **Identificateur de Package de sécurité**. Vous devez à la fois dans le portail Azure – ouvrir votre concentrateur de notification, cliquez sur **paramètres** > **Notification Services** > **Windows (WNS)** et entrez les informations dans les champs requis.

![](./media/notification-hubs-geofence/notification-hubs-wns.png)

Cliquez sur **Enregistrer**.

Avec le bouton droit cliquez sur **références** dans **L’Explorateur** , sélectionnez **Manage NuGet Packages**. Nous devons ajouter une référence à la **bibliothèque managée Bus des services Microsoft Azure** – recherchez simplement `WindowsAzure.Messaging.Managed` et l’ajouter à votre projet.

![](./media/notification-hubs-geofence/vs-nuget.png)

Pour effectuer un test, nous pouvons créer la `MainPage_Loaded` là encore, d’événements et lui ajouter cet extrait de code :

    var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    var hub = new NotificationHub("HUB_NAME", "HUB_LISTEN_CONNECTION_STRING");
    var result = await hub.RegisterNativeAsync(channel.Uri);

    // Displays the registration ID so you know it was successful
    if (result.RegistrationId != null)
    {
        Debug.WriteLine("Reg successful.");
    }

Ce qui précède enregistre l’application avec le hub de notification. Vous êtes prêt à être envoyé ! 

Dans `LocationHelper`, à l’intérieur du `Geolocator_PositionChanged` gestionnaire, vous pouvez ajouter un morceau de code de test qui mettra forcer l’emplacement à l’intérieur de la geofence :

    await LocationHelper.SendLocationToBackend("wns", "TEST_USER", "TEST", "37.7746", "-122.3858");

Étant donné que nous ne passent pas les coordonnées réelles (qui peuvent ne pas être à l’intérieur des limites pour le moment) et utilisez les valeurs de test prédéfinies, nous verrons une notification apparaissant sur la mise à jour :

![](./media/notification-hubs-geofence/notification-hubs-test-notification.png)

##<a name="whats-next"></a>Quoi d’autre ?

Il existe quelques étapes que vous devrez peut-être suivre outre la ci-dessus pour vous assurer qu’elle est opérationnelle.

Tout d’abord, vous devrez peut-être vous assurer que geofences sont dynamiques. Cela nécessite un travail supplémentaire avec l’API Bing afin de pouvoir télécharger nouvelles frontières au sein de la source de données existante. Pour plus d’informations sur l’objet, consultez la [documentation de l’API Bing spatiale Data Services](https://msdn.microsoft.com/library/ff701734.aspx) .

Ensuite, que vous utilisez pour vous assurer que la remise est effectuée pour les participants à droite, vous souhaiterez peut-être spécifier les cibles via le [marquage](notification-hubs-tags-segment-push-message.md).

La solution ci-dessus décrit un scénario dans lequel vous pouvez avoir un large éventail de plateformes cibles, afin que nous ne pas limiter le gardiennage virtuel aux fonctionnalités spécifiques au système. Ceci étant dit, la plateforme Windows universel intègre des fonctionnalités pour [détecter les geofences droite - de-l’emploi](https://msdn.microsoft.com/windows/uwp/maps-and-location/set-up-a-geofence).

Pour plus d’informations concernant les fonctionnalités de Notification Hubs, consultez notre [portail de documentation](https://azure.microsoft.com/documentation/services/notification-hubs/).
