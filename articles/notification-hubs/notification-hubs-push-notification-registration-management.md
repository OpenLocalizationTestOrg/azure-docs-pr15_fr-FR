<properties
    pageTitle="Gestion de l’enregistrement"
    description="Cette rubrique explique comment enregistrer des appareils mobiles avec hubs de notification pour recevoir des notifications push."
    services="notification-hubs"
    documentationCenter=".net"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-multiple"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

# <a name="registration-management"></a>Gestion de l’enregistrement

##<a name="overview"></a>Vue d’ensemble

Cette rubrique explique comment enregistrer des appareils mobiles avec hubs de notification pour recevoir des notifications push. La rubrique décrit les enregistrements à un niveau élevé, puis présente les deux schémas principaux d’enregistrement de périphériques : enregistrant à partir de l’appareil directement dans le hub de notification et l’enregistrement à une version de serveur d’application. 


##<a name="what-is-device-registration"></a>Nouveautés d’inscription appareils mobiles

Enregistrement de dispositif avec un concentrateur de Notification s’effectue à l’aide d’une **Installation**ou **l’enregistrement** .

#### <a name="registrations"></a>Enregistrements
Un enregistrement associe la poignée de Service de Notification de plateforme (solution) pour un appareil balises et éventuellement un modèle. La poignée de solution peut être un ChannelURI, jeton d’appareil ou id d’inscription GCM. Balises sont utilisées pour acheminer les notifications à l’ensemble d’identificateurs d’unités correct. Pour plus d’informations, voir [routage et Expressions de balise](notification-hubs-tags-segment-push-message.md). Modèles sont utilisés pour mettre en œuvre transformation par l’enregistrement. Pour plus d’informations, voir [modèles](notification-hubs-templates-cross-platform-push-messages.md).

#### <a name="installations"></a>Installations
Une Installation est une version améliorée d’enregistrement qui inclut un ensemble de push propriétés associées. Il est la meilleure approche à l’enregistrement de vos appareils. Toutefois, il n'est pas pris en charge par côté client .NET Kit de développement logiciel ([SDK concentrateur de Notification pour les opérations de serveur principal](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)) à partir d’encore.  Cela signifie que si vous vous inscrivez à partir de l’appareil client lui-même, vous devez utiliser l’approche [API REST de Hubs Notification](https://msdn.microsoft.com/library/mt621153.aspx) au sein des installations. Si vous utilisez un service en aval, vous devriez pouvoir utiliser [SDK concentrateur de Notification pour les opérations de serveur principal](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

Il existe certains avantages clés à l’aide d’installations sont les suivantes :

* Création ou mise à jour une installation est entièrement idempotent. Vous pouvez donc le réessayer sans des questions concernant les enregistrements en double.
* Le modèle d’installation facilite la faire individuels push - le ciblage du périphérique spécifique. Une balise système **« $InstallationId : [ID] »** est ajouté automatiquement avec chaque enregistrement en fonction de l’installation. Ainsi, vous pouvez appeler un envoi à cette balise à un périphérique spécifique sans avoir à effectuer d’ajouter du code.
* L’utilisation des installations permet également vous permettent d’effectuer des mises à jour partielle d’inscription. La mise à jour partielle d’une installation est demandé avec une méthode correctif à l’aide de la [norme JSON correctif](https://tools.ietf.org/html/rfc6902). Ceci est particulièrement utile lorsque vous voulez mettre à jour les indicateurs dans l’enregistrement. Vous n’êtes pas obligé de développé l’inscription entière et puis renvoyer toutes les balises précédents à nouveau.

Une installation peut contenir les propriétés suivantes. Pour une liste complète de l’installation propriétés voir [créer ou remplacer une Installation avec l’API REST](https://msdn.microsoft.com/library/azure/mt621153.aspx) ou [Propriétés de l’Installation](https://msdn.microsoft.com/library/azure/microsoft.azure.notificationhubs.installation_properties.aspx) de la.

    // Example installation format to show some supported properties
    {
        installationId: "",
        expirationTime: "",
        tags: [],
        platform: "",
        pushChannel: "",
        ………
        templates: {
            "templateName1" : {
                body: "",
                tags: [] },
            "templateName2" : {
                body: "",
                // Headers are for Windows Store only
                headers: {
                    "X-WNS-Type": "wns/tile" }
                tags: [] }
        },
        secondaryTiles: {
            "tileId1": {
                pushChannel: "",
                tags: [],
                templates: {
                    "otherTemplate": {
                        bodyTemplate: "",
                        headers: {
                            ... }
                        tags: [] }
                }
            }
        }
    }

 

Il est important de noter que les enregistrements et les installations par défaut n’est plus expirent.

Les enregistrements et les installations doivent contenir une poignée de solution valide pour chaque appareil/canal. Étant donné que les poignées de solution ne peuvent être obtenues dans une application client sur le périphérique, un motif consiste à enregistrer directement sur votre appareil avec l’application client. En revanche, considérations relatives à la sécurité et la logique métier liés aux balises peuvent vous demander gérer d’inscription appareils mobiles dans l’application principale. 

#### <a name="templates"></a>Modèles

Si vous souhaitez utiliser des [modèles](notification-hubs-templates-cross-platform-push-messages.md), l’installation du périphérique contiennent également tous les modèles associés à votre appareil dans un JSON mettre en forme (voir l’exemple ci-dessus). Les noms de modèles permettent de modèles pour le même appareil cible.

Notez que chaque nom de modèle correspond à un corps de modèle et un jeu de balises facultatif. En outre, chaque plateforme peut contenir des propriétés du modèle supplémentaires. Pour Windows Store (à l’aide de WNS) et Windows Phone 8 (à l’aide de MPNS), un ensemble d’en-têtes supplémentaire peut faire partie du modèle. Dans le cas APNs, vous pouvez définir une propriété d’expiration pour une constante ou une expression de modèle. Pour une liste complète de l’installation de propriétés voir [créer ou remplacer une Installation avec reste](https://msdn.microsoft.com/library/azure/mt621153.aspx) à un sujet.

#### <a name="secondary-tiles-for-windows-store-apps"></a>Vignettes secondaires pour les applications du Windows Store

Pour les applications clientes du Windows Store, envoyer des notifications aux vignettes secondaires est identique à leur envoyer vers l’option principal. Il est également prise en charge dans les installations. Notez que les vignettes secondaires ont un ChannelUri différent, qui le Kit de développement de votre application client gère en toute transparence.

Le dictionnaire SecondaryTiles utilise la même TileId servant à créer l’objet SecondaryTiles dans votre application du Windows Store.
Comme avec la ChannelUri principal, ChannelUris des vignettes secondaires peut modifier à tout moment. Afin de garder les installations dans le hub de notification mis à jour, le périphérique doit actualiser les avec la ChannelUris en cours des vignettes secondaires.


##<a name="registration-management-from-the-device"></a>Gestion de l’inscription de l’appareil

Lors de la gestion d’inscription pour appareils à partir des applications clientes, le système principal n’est responsable de l’envoi des notifications. Applications clientes poignées solution mise à jour et enregistrer des indicateurs. L’image suivante illustre ce modèle.

![](./media/notification-hubs-registration-management/notification-hubs-registering-on-device.png)

Tout d’abord le périphérique récupère la poignée de solution à partir de la solution, puis inscrit directement dans le hub de notification. Une fois que l’enregistrement a réussi, le système principal de l’application peut envoyer une notification ciblage de cet enregistrement. Pour plus d’informations sur l’envoi de notifications, voir [routage et Expressions de balise](notification-hubs-tags-segment-push-message.md).
Notez que dans ce cas, vous allez utiliser écouter uniquement les droits nécessaires pour accéder à votre hubs notification à partir de l’appareil. Pour plus d’informations, voir [sécurité](notification-hubs-push-notification-security.md).

L’inscription de l’appareil est la méthode la plus simple, mais il a quelques inconvénients.
Le premier inconvénient est qu’une application client peut uniquement mettre à jour ses balises lorsque l’application est inactive. Par exemple, si un utilisateur possède deux appareils qui enregistrent balises relatives aux équipes de sport, lorsque le premier appareil s’inscrit à une balise supplémentaire (par exemple, Seahawks), le deuxième périphérique ne recevrez pas les notifications concernant la Seahawks jusqu'à ce que l’application sur le deuxième périphérique est exécutée une seconde fois. En règle générale, lorsque les balises sont affectés par plusieurs appareils, gestion des balises à partir du serveur principal est une option opportun.
Le deuxième inconvénient de gestion de l’inscription de l’application client est que, dans la mesure où les applications peuvent être piratées, sécurisation de l’enregistrement à des balises spécifiques nécessite prudent, comme indiqué dans la section « sécurité au niveau de l’indicateur. »



#### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-an-installation"></a>Exemple de code pour enregistrer avec un concentrateur de notification d’un appareil à l’aide d’une installation 

Pour le moment, il est uniquement prise en charge à l’aide de l' [API REST de Hubs Notification](https://msdn.microsoft.com/library/mt621153.aspx).

Vous pouvez également utiliser la méthode correctif à l’aide de la [norme JSON correctif](https://tools.ietf.org/html/rfc6902) de mise à jour de l’installation.

    class DeviceInstallation
    {
        public string installationId { get; set; }
        public string platform { get; set; }
        public string pushChannel { get; set; }
        public string[] tags { get; set; }
    }

    private async Task<HttpStatusCode> CreateOrUpdateInstallationAsync(DeviceInstallation deviceInstallation,
         string hubName, string listenConnectionString)
    {
        if (deviceInstallation.installationId == null)
            return HttpStatusCode.BadRequest;

        // Parse connection string (https://msdn.microsoft.com/library/azure/dn495627.aspx)
        ConnectionStringUtility connectionSaSUtil = new ConnectionStringUtility(listenConnectionString);
        string hubResource = "installations/" + deviceInstallation.installationId + "?";
        string apiVersion = "api-version=2015-04";

        // Determine the targetUri that we will sign
        string uri = connectionSaSUtil.Endpoint + hubName + "/" + hubResource + apiVersion;

        //=== Generate SaS Security Token for Authorization header ===
        // See, https://msdn.microsoft.com/library/azure/dn495627.aspx
        string SasToken = connectionSaSUtil.getSaSToken(uri, 60);

        using (var httpClient = new HttpClient())
        {
            string json = JsonConvert.SerializeObject(deviceInstallation);

            httpClient.DefaultRequestHeaders.Add("Authorization", SasToken);

            var response = await httpClient.PutAsync(uri, new StringContent(json, System.Text.Encoding.UTF8, "application/json"));
            return response.StatusCode;
        }
    }

    var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    string installationId = null;
    var settings = ApplicationData.Current.LocalSettings.Values;

    // If we have not stored a installation id in application data, create and store as application data.
    if (!settings.ContainsKey("__NHInstallationId"))
    {
        installationId = Guid.NewGuid().ToString();
        settings.Add("__NHInstallationId", installationId);
    }

    installationId = (string)settings["__NHInstallationId"];

    var deviceInstallation = new DeviceInstallation
    {
        installationId = installationId,
        platform = "wns",
        pushChannel = channel.Uri,
        //tags = tags.ToArray<string>()
    };

    var statusCode = await CreateOrUpdateInstallationAsync(deviceInstallation, 
                        "<HUBNAME>", "<SHARED LISTEN CONNECTION STRING>");

    if (statusCode != HttpStatusCode.Accepted)
    {
        var dialog = new MessageDialog(statusCode.ToString(), "Registration failed. Installation Id : " + installationId);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
    }
    else
    {
        var dialog = new MessageDialog("Registration successful using installation Id : " + installationId);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
    }

   

#### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-a-registration"></a>Exemple de code pour enregistrer avec un concentrateur de notification à partir d’un périphérique utilisant un enregistrement


Ces méthodes créent ou mettre à jour un enregistrement pour l’appareil sur lequel elles sont appelées. Cela signifie qu’afin de mettre à jour la poignée ou les balises, vous devez remplacer l’enregistrement entier. N’oubliez pas que les enregistrements sont transitoires, vous devez toujours un magasin fiable avec les balises actives nécessitant un périphérique spécifique.


    // Initialize the Notification Hub
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(listenConnString, hubName);

    // The Device id from the PNS
    var pushChannel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    // If you are registering from the client itself, then store this registration id in device
    // storage. Then when the app starts, you can check if a registration id already exists or not before
    // creating.
    var settings = ApplicationData.Current.LocalSettings.Values;

    // If we have not stored a registration id in application data, store in application data.
    if (!settings.ContainsKey("__NHRegistrationId"))
    {
        // make sure there are no existing registrations for this push handle (used for iOS and Android)    
        string newRegistrationId = null;
        var registrations = await hub.GetRegistrationsByChannelAsync(pushChannel.Uri, 100);
        foreach (RegistrationDescription registration in registrations)
        {
            if (newRegistrationId == null)
            {
                newRegistrationId = registration.RegistrationId;
            }
            else
            {
                await hub.DeleteRegistrationAsync(registration);
            }
        }

        newRegistrationId = await hub.CreateRegistrationIdAsync();

        settings.Add("__NHRegistrationId", newRegistrationId);
    }
     
    string regId = (string)settings["__NHRegistrationId"];

    RegistrationDescription registration = new WindowsRegistrationDescription(pushChannel.Uri);
    registration.RegistrationId = regId;
    registration.Tags = new HashSet<string>(YourTags);

    try
    {
        await hub.CreateOrUpdateRegistrationAsync(registration);
    }
    catch (Microsoft.WindowsAzure.Messaging.RegistrationGoneException e)
    {
        settings.Remove("__NHRegistrationId");
    }


## <a name="registration-management-from-a-backend"></a>Gestion de l’inscription à partir d’un serveur principal

Gestion des enregistrements à partir de la version serveur nécessite l’écriture de code supplémentaire. L’application à partir de l’appareil doit fournir la solution mis à jour gérer sur le serveur principal chaque démarrage de l’application (ainsi que des balises et des modèles), et le système principal devez mettre à jour cette poignée dans le hub de notification. L’image suivante illustre cette création.

![](./media/notification-hubs-registration-management/notification-hubs-registering-on-backend.png)

Les avantages de la gestion des enregistrements à partir de la version serveur incluent la possibilité pour modifier des balises à des enregistrements même lorsque l’application correspondante sur l’appareil est inactive et pour l’authentification de l’application client avant d’ajouter une balise à son enregistrement.


#### <a name="example-code-to-register-with-a-notification-hub-from-a-backend-using-an-installation"></a>Exemple de code pour enregistrer avec un concentrateur de notification à partir d’un serveur principal à l’aide d’une installation

Le périphérique client obtient sa poignée de solution et des propriétés installation nécessaires avant toujours et appelle une API personnalisée sur le serveur principal qui peut effectuer l’enregistrement et autorisez les balises etc.. Le serveur principal peut tirer parti du [SDK concentrateur de Notification pour les opérations de serveur principal](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

Vous pouvez également utiliser la méthode correctif à l’aide de la [norme JSON correctif](https://tools.ietf.org/html/rfc6902) de mise à jour de l’installation.
 

    // Initialize the Notification Hub
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(listenConnString, hubName);

    // Custom API on the backend
    public async Task<HttpResponseMessage> Put(DeviceInstallation deviceUpdate)
    {

        Installation installation = new Installation();
        installation.InstallationId = deviceUpdate.InstallationId;
        installation.PushChannel = deviceUpdate.Handle;
        installation.Tags = deviceUpdate.Tags;

        switch (deviceUpdate.Platform)
        {
            case "mpns":
                installation.Platform = NotificationPlatform.Mpns;
                break;
            case "wns":
                installation.Platform = NotificationPlatform.Wns;
                break;
            case "apns":
                installation.Platform = NotificationPlatform.Apns;
                break;
            case "gcm":
                installation.Platform = NotificationPlatform.Gcm;
                break;
            default:
                throw new HttpResponseException(HttpStatusCode.BadRequest);
        }


        // In the backend we can control if a user is allowed to add tags
        //installation.Tags = new List<string>(deviceUpdate.Tags);
        //installation.Tags.Add("username:" + username);

        await hub.CreateOrUpdateInstallationAsync(installation);

        return Request.CreateResponse(HttpStatusCode.OK);
    }


#### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-a-registration-id"></a>Exemple de code pour enregistrer avec un concentrateur de notification à partir d’un périphérique utilisant un id d’enregistrement

À partir de votre serveur principal de l’application, vous pouvez effectuer les opérations de CRUDS base sur les enregistrements. Par exemple :

    var hub = NotificationHubClient.CreateClientFromConnectionString("{connectionString}", "hubName");
            
    // create a registration description object of the correct type, e.g.
    var reg = new WindowsRegistrationDescription(channelUri, tags);

    // Create
    await hub.CreateRegistrationAsync(reg);

    // Get by id
    var r = await hub.GetRegistrationAsync<RegistrationDescription>("id");

    // update
    r.Tags.Add("myTag");

    // update on hub
    await hub.UpdateRegistrationAsync(r);

    // delete
    await hub.DeleteRegistrationAsync(r);


Le serveur principal doit gérer les accès concurrentiels entre les mises à jour l’enregistrement. Service Bus offre simultané de contrôle pour la gestion de l’enregistrement. Au niveau HTTP, cela est implémenté à l’aide de ETag sur les opérations de gestion de l’enregistrement. Cette fonctionnalité est utilisée en toute transparence par Microsoft SDKs, laquelle lever une exception si une mise à jour est rejeté pour des raisons de la concurrence. Le système principal de l’application est responsable de la gestion de ces exceptions et une nouvelle tentative de la mise à jour si nécessaire.