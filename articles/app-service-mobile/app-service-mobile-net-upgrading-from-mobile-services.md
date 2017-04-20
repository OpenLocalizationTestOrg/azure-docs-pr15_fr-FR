<properties
    pageTitle="Mise à niveau à partir de Services mobiles au Service application Azure"
    description="Découvrez comment mettre à niveau facilement votre application de Services mobiles pour une application application Service Mobile"
    services="app-service\mobile"
    documentationCenter=""
    authors="adrianhall"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="upgrade-your-existing-net-azure-mobile-service-to-app-service"></a>Mise à niveau de Service Mobile Azure .NET existant au Service d’application

Application Service Mobile est une nouvelle méthode pour créer des applications mobiles à l’aide de Microsoft Azure. Pour plus d’informations, voir [Quelles sont les applications Mobile ?].

Cette rubrique décrit comment mettre à niveau une application de serveur principal .NET existante à partir d’Azure Mobile Services à un nouveau les applications application Service Mobile. Pendant que vous effectuez cette mise à niveau, votre application de Services mobiles existante peut continuer à fonctionner.   Si vous devez mettre à niveau une application principale Node.js, reportez-vous à [la mise à niveau vos Services Mobile Node.js](./app-service-mobile-node-backend-upgrading-from-mobile-services.md).

Lorsqu’un serveur principal mobile est mis à niveau vers Azure Application Service, il a accès à toutes les fonctionnalités de Service d’application et facturés en fonction de l' [application Service tarifs], pas les Services mobiles tarifs.

##<a name="migrate-vs-upgrade"></a>Migration et mise à niveau

[AZURE.INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

>[AZURE.TIP] Il est recommandé d' [effectuer une migration](app-service-mobile-migrating-from-mobile-services.md) avant d’utiliser une mise à niveau. Ainsi, vous pouvez placer les deux versions de votre application sur le même Plan de Service de l’application et subir sans frais supplémentaires.

###<a name="improvements-in-mobile-apps-net-server-sdk"></a>Améliorations dans Mobile applications .NET server SDK

Mise à niveau vers le nouveau [Kit de développement Mobile applications](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) offre les avantages suivants :

- Plus de flexibilité sur les dépendances NuGet. L’environnement d’hébergement n’assure plus ses propres versions des packages NuGet, afin d’utiliser les versions compatibles alternatives. Cependant, s’il existe bugfixes critique nouvelles ou mises à jour de sécurité pour le Kit de développement Mobile serveur ou les dépendances, vous devez mettre à jour manuellement votre service.

- Plus de flexibilité dans le Kit de développement mobile. Vous pouvez contrôler quelles fonctionnalités de manière explicite et itinéraires sont configurés, tels que l’authentification et le point de terminaison de l’inscription push table API. Pour plus d’informations, voir [comment utiliser le Kit de développement pour applications Mobile Azure .NET server](app-service-mobile-net-upgrading-from-mobile-services.md#server-project-setup).

- Prise en charge pour les autres types de projets ASP.NET et itinéraires. Vous pouvez désormais héberger contrôleurs MVC et API Web dans le même projet que votre projet principal mobile.

- Prise en charge des nouvelles fonctionnalités application Service d’authentification, qui vous permettent d’utiliser une configuration d’authentification courantes sur le web et les applications mobiles.

##<a name="overview"></a>Vue d’ensemble de la mise à niveau simple

Dans de nombreux cas, la mise à niveau sera suffit de basculer vers le nouveau serveur d’applications Mobile SDK et republiez votre code sur une nouvelle instance de l’application Mobile. Il existe cependant certains scénarios qui nécessitent une configuration supplémentaire, tels que des scénarios d’authentification avancées, de travailler avec des tâches planifiées. Chacun d'entre eux est traité dans les sections suivantes.

>[AZURE.TIP] Il est conseillé de lire et de comprendre le reste de cette rubrique complètement avant de démarrer une mise à niveau. Notez les fonctionnalités que vous utilisez entourées en dessous.

Le client de Services mobiles SDK sont **pas** compatibles avec le nouveau serveur d’applications Mobile SDK. Afin d’assurer la continuité de service pour votre application, vous devez publier pas modifications à un site qui utilise des clients publiés. À la place, vous devez créer une nouvelle application mobile qui sert un doublon. Vous pouvez placer cette application sur le même plan de Service d’application pour éviter entraîner coût financier supplémentaire.

Vous aurez alors deux versions de l’application : reste la même et gère publiés applications dans la nature et l’autre dont vous pouvez ensuite mettre à niveau et cibles avec une nouvelle version du client. Vous pouvez déplacer et testez votre code à votre rythme, mais vous devez vous assurer que les correctifs que vous rendre appliqués aux deux. Une fois que vous estimez qu’un nombre client applications dans la nature ont mis à jour vers la dernière version de votre choix, vous pouvez supprimer l’application a été déplacée d’origine si vous le souhaitez.

Le plan complè pour le processus de mise à niveau se présente comme suit :

1. Créer une nouvelle application Mobile
2. Mettre à jour le projet pour utiliser le nouveau SDK Server
3. Publiez une nouvelle version de votre application cliente
4. (Facultatif) Supprimer votre instance migré d’origine

##<a name="mobile-app-version"></a>Création d’une seconde instance d’application
Mise à niveau de la première étape consiste à créer la ressource de l’application Mobile qui héberge la nouvelle version de votre application. Si vous avez déjà migré un service mobile existant, vous souhaiterez créer cette version sur le même plan d’hébergement. Ouvrez le [portail Azure] et accédez à votre application migrée. Notez le Service d’application Plan elle s’exécute sur.

Ensuite, créez la seconde instance d’application en suivant les [instructions de création d’une version serveur .NET](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#create-app). Lorsque vous êtes invité à sélectionner vous offre de Service d’application ou « hébergement plan » choisir l’offre de votre application migrée.

Vous souhaiterez probablement utiliser la même base de données et un concentrateur de Notification comme vous le faisiez dans les Services mobiles. Vous pouvez copier ces valeurs en ouvrant [Azure portal] et accédez à l’application d’origine, puis cliquez sur **paramètres** > **paramètres de l’Application**. Sous **Chaînes de connexion**, copiez `MS_NotificationHubConnectionString` et `MS_TableConnectionString`. Accédez à votre nouveau site de mise à niveau et les coller en remplaçant les valeurs existantes. Répétez cette procédure pour les autres paramètres d’application vos besoins d’application. Si vous N'utilisez pas un service a été déplacée, vous pouvez lire les chaînes de connexion et paramètres de l’application à partir de l’onglet **configuration** de la section Mobile Services du [portail classique Azure].

Faire une copie du projet ASP.NET pour votre application et publiez-le sur votre nouveau site. En utilisant une copie de votre application cliente mis à jour avec la nouvelle URL, valider que tout fonctionne comme prévu.

## <a name="updating-the-server-project"></a>Mise à jour le projet de serveur

Applications Mobile fournit un nouveau [Kit de développement Mobile application serveur] qui fournit une grande partie de la même fonctionnalité que le runtime de Services mobiles. Tout d’abord, vous devez supprimer toutes les références à des ensembles de Services mobiles. Dans le Gestionnaire de package NuGet, recherchez `WindowsAzure.MobileServices.Backend`. La plupart des applications verront plusieurs packages ici, y compris `WindowsAzure.MobileServices.Backend.Tables` et `WindowsAzure.MobileServices.Backend.Entity`. Dans ce cas, précédez le package les plus faibles dans l’arborescence de dépendances, tels que `Entity`et supprimez-la. Lorsque vous y êtes invité, ne supprimez pas tous les packages qui en dépendent. Répétez cette procédure jusqu'à ce que vous avez supprimé `WindowsAzure.MobileServices.Backend` lui-même.

À ce stade, vous aurez accès un projet qui n’est plus référence Mobile Services SDK.

Ensuite, vous ajouterez références le Kit de développement des applications mobiles. Cette mise à niveau, la plupart des développeurs souhaiteront télécharger et installer le `Microsoft.Azure.Mobile.Server.Quickstart` créer un package, comme cela puisse extraire dans l’ensemble requis.

Erreurs du compilateur un certain résultant de différences entre les kits de développement seront, mais ces faciles à adresse et sont décrites dans le reste de cette section.

### <a name="base-configuration"></a>Configuration de base

Ensuite, dans WebApiConfig.cs, vous pouvez remplacer :

        // Use this class to set configuration options for your mobile service
        ConfigOptions options = new ConfigOptions();

        // Use this class to set WebAPI configuration options
        HttpConfiguration config = ServiceConfig.Initialize(new ConfigBuilder(options));

avec

        HttpConfiguration config = new HttpConfiguration();
        new MobileAppConfiguration()
            .UseDefaultConfiguration()
        .ApplyTo(config);

>[AZURE.NOTE] Si vous souhaitez en savoir plus sur le nouveau serveur .NET SDK et explique comment ajouter/supprimer des fonctionnalités à partir de votre application, consultez la rubrique [comment utiliser le serveur .NET SDK] .

Si votre application fait utiliser des fonctionnalités d’authentification, vous devez également enregistrer une logiciels intermédiaires OWIN. Dans ce cas, vous devez déplacer le code de configuration ci-dessus dans une nouvelle classe OWIN démarrage.

1. Ajouter le package NuGet `Microsoft.Owin.Host.SystemWeb` si elle n’est pas déjà incluse dans votre projet.
2. Dans Visual Studio, cliquez avec le bouton droit sur votre projet, puis sélectionnez **Ajouter** -> **Un nouvel élément**. Sélectionnez **Web** -> **Général** -> **démarrage OWIN cours**.
3. Déplacer le code ci-dessus pour MobileAppConfiguration de `WebApiConfig.Register()` à la `Configuration()` méthode de votre nouvelle classe de démarrage.

Vérifiez que la `Configuration()` méthode se termine par :

        app.UseWebApi(config)
        app.UseAppServiceAuthentication(config);

Il existe des modifications supplémentaires liées à l’authentification qui sont décrites dans la section authentification complète ci-dessous.

### <a name="working-with-data"></a>Utilisation des données

Dans les Services mobiles, le nom de l’application mobile pris en charge en tant que le nom du schéma par défaut dans l’installation du Framework entité.

Pour vous assurer d’avoir le même schéma référencé comme auparavant, utilisez les éléments suivants pour définir le schéma dans le DbContext pour votre application :

        string schema = System.Configuration.ConfigurationManager.AppSettings.Get("MS_MobileServiceName");

Vérifiez que vous disposez MS_MobileServiceName définir si vous effectuez les étapes ci-dessus. Vous pouvez également fournir un autre nom de schéma si votre application personnalisé précédemment.

### <a name="system-properties"></a>Propriétés du système

#### <a name="naming"></a>Affectation des noms

Serveur Azure Mobile Services SDK, propriétés système toujours contient un trait de soulignement double (`__`) préfixe pour les propriétés :

- __createdAt
- __updatedAt
- __deleted
- __version

Le client de Services mobiles SDK disposer d’une logique spéciale pour l’analyse des propriétés du système dans ce format.

Dans les applications Azure Mobile, propriétés système n’est plus ont un format spécial et portent les noms suivants :

- créédans
- updatedAt
- supprimés
- Version

Le client Mobile applications SDK utiliser les nouveaux noms de propriétés système, afin que les aucune modification est nécessaire au code client. Toutefois, si vous apportez directement des appels reste à votre service puis vous devez modifier vos requêtes en conséquence.

#### <a name="local-store"></a>Magasin local

Les modifications apportées aux noms des propriétés système signifient qu’une base de données locale de synchronisation hors connexion pour les Services mobiles n’est pas compatible avec les applications Mobile. Si possible, vous devez éviter la mise à niveau client applications de Services mobiles aux applications Mobile jusqu'à après modifications en attente ont été envoyées au serveur. Ensuite, l’application mise à niveau doit utiliser un nouveau nom de fichier de base de données.

Si une application client est mis à niveau à partir de Services mobiles aux applications Mobile alors qu’il existe des modifications en mode hors connexion en attente dans la file d’attente opération, la base de données système doit mis à jour pour utiliser les nouveaux noms de colonne. Sur iOS, il est possible à l’aide des migrations légères pour modifier les noms de colonne. Sur Android et le client géré .NET, vous devez écrire SQL personnalisé pour renommer les colonnes pour vos tableaux d’objet de données.

Sur iOS, vous devez modifier votre schéma de données de base pour vos entités de données correspondre à la suivante. Notez que les propriétés `createdAt`, `updatedAt` et `version` n’est plus un `ms_` préfixe :

| Attribut |  Type   | Remarque                                                 |
|---------- |  ------ | -----------------------------------------------------|
| ID        | Chaîne, requises  | clé primaire Store à distance         |
| créédans | Date    | (facultatif) mappe vers propriété système créédans         |
| updatedAt | Date    | (facultatif) mappe vers propriété système updatedAt         |
| Version   | Chaîne  | (facultatif) permettant de détecter les conflits, mappe vers la version |

#### <a name="querying-system-properties"></a>Interroger des propriétés système

Dans les Services mobiles Azure, propriétés système ne sont pas envoyées par défaut, mais uniquement lorsqu’elles sont demandées à l’aide de la chaîne de requête `__systemProperties`. En revanche, dans les applications Mobile Azure système propriétés sont **toujours sélectionnée** dans la mesure où elles font partie du modèle d’objet du Kit de développement logiciel serveur.

Cette modification affecte principalement mises en œuvre personnalisées de gestionnaires de domaines, tels que les extensions de `MappedEntityDomainManager`. Dans les Services Mobile, si un client demande jamais des propriétés du système, il est possible d’utiliser un `MappedEntityDomainManager` qui n’est pas réellement mappé toutes les propriétés. Toutefois, dans les applications Azure Mobile, ces propriétés mappées provoquera une erreur dans les requêtes GET.

Le moyen le plus simple pour résoudre le problème consiste à modifier votre DTO afin qu’ils héritent de `ITableData` au lieu de `EntityData`. Ensuite, ajoutez la `[NotMapped]` attribut aux champs qui doivent être omis.

Par exemple, ce qui suit définit `TodoItem` sans propriétés système :

    using System.ComponentModel.DataAnnotations.Schema;

    public class TodoItem : ITableData
    {
        public string Text { get; set; }

        public bool Complete { get; set; }

        public string Id { get; set; }

        [NotMapped]
        public DateTimeOffset? CreatedAt { get; set; }

        [NotMapped]
        public DateTimeOffset? UpdatedAt { get; set; }

        [NotMapped]
        public bool Deleted { get; set; }

        [NotMapped]
        public byte[] Version { get; set; }
    }

Remarque : Si vous obtenez des erreurs `NotMapped`, ajoutez une référence à l’assembly `System.ComponentModel.DataAnnotations`.

### <a name="cors"></a>CORS

Services mobiles inclus une prise en charge pour CORS par la solution ASP.NET CORS d’habillage. Cette couche habillage a été supprimée pour donner le développeur plus de contrôle, afin que vous pouvez exploiter directement [CORS ASP.NET prennent en charge](http://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api).

Zones principales de problème si vous utilisez CORS sont que la `eTag` et `Location` en-têtes doivent être autorisés afin que le client SDK fonctionne correctement.

### <a name="push-notifications"></a>Notifications de transmission
Pour la distribution, l’élément principal que vous trouverez peut-être manquant dans le Kit de développement serveur est la classe PushRegistrationHandler. Les enregistrements sont gérées différemment dans les applications mobiles et les enregistrements il sont activées par défaut. Gestion des balises peut être effectuée par l’utilisation d’API personnalisé. Consultez les instructions [d’inscription des indicateurs](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#tags) pour plus d’informations.

### <a name="scheduled-jobs"></a>Tâches planifiées
Tâches planifiées ne sont pas intégrées dans les applications Mobile, afin que toutes les tâches existantes dans votre système principal .NET devra être mis à niveau individuellement. Une option consiste à créer une [Tâche Web] planifiée sur le site de code de l’application Mobile. Vous pouvez également configurer un contrôleur contenant votre code de projet et configurer le [Planificateur Azure] pour atteindre ce point de terminaison de la planification.

### <a name="miscellaneous-changes"></a>Modifications diverses
Tous les ApiControllers qui sera utilisés par un client mobile doit ont désormais la `[MobileAppController]` attribut. Ce n’est plus inclus par défaut afin que d’autres ApiControllers pour accéder ne sont pas affectées par les formateurs mobiles.

La `ApiServices` objet n’est plus partie du Kit de développement. Pour accéder aux paramètres de l’application Mobile, vous pouvez utiliser les éléments suivants :

    MobileAppSettingsDictionary settings = this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings();

De même, la journalisation est désormais possible à l’aide de l’écriture de trace ASP.NET standard :

    ITraceWriter traceWriter = this.Configuration.Services.GetTraceWriter();
    traceWriter.Info("Hello, World");  

##<a name="authentication"></a>Considérations relatives à l’authentification

Les composants d’authentification de Services mobiles ont été maintenant déplacées dans la fonctionnalité application Service d’authentification / d’autorisation. Vous pouvez en savoir plus sur l’activation de cette pour votre site en lisant la rubrique [authentification ajouter à votre application mobile](app-service-mobile-ios-get-started-users.md) .

Pour certains fournisseurs, tels que DAS Facebook et Google, vous devriez pouvoir tirer parti de l’enregistrement existant à partir de votre application de copie. Vous devez simplement accédez au portail du fournisseur d’identité et ajoutez une nouvelle URL de redirection à l’enregistrement. Ensuite configurer l’application Service d’authentification / d’autorisation avec l’ID de client et le code secret.

### <a name="controller-action-authorization"></a>Autorisation contrôleur action
Toutes les instances de le `[AuthorizeLevel(AuthorizationLevel.User)]` attribut doit maintenant être modifié pour utiliser ASP.NET standard `[Authorize]` attribut. En outre, contrôleurs sont désormais anonyme par défaut, comme dans d’autres applications ASP.NET.
Si vous utilisiez une des autres options AuthorizeLevel, telles que l’administrateur ou l’Application, notez qu’il s’agit disparu. Vous pouvez à la place configurer AuthorizationFilters pour secrets partagés ou configurer un Service AAD Principal pour activer des appels de service à service en toute sécurité.

### <a name="getting-additional-user-information"></a>Obtenir des informations utilisateur supplémentaires

Vous pouvez obtenir des informations utilisateur supplémentaires, y compris les jetons d’accès par le biais du `GetAppServiceIdentityAsync()` méthode :

        FacebookCredentials creds = await this.User.GetAppServiceIdentityAsync<FacebookCredentials>();

En outre, si votre application prend des dépendances sur les ID utilisateur, tels que les stocker dans une base de données, il est important de noter que les noms d’utilisateur entre Services mobiles et application Service Mobile applications sont différents. Vous pouvez toujours obtenir l’ID utilisateur de Services mobiles, via. Toutes les sous-classes ProviderCredentials ont une propriété de nom d’utilisateur. Pour continuer à partir de l’exemple avant :

        string mobileServicesUserId = creds.Provider + ":" + creds.UserId;

Si votre application prend des dépendances sur les identifiants utilisateur, il est important que vous tirer parti du même type d’enregistrement avec un fournisseur d’identité si possible. ID d’utilisateur sont généralement limitées à l’inscription de l’application qui a été utilisée, afin de la présentation d’un nouvel enregistrement peut provoquer des problèmes avec des utilisateurs à leurs données correspondants.

### <a name="custom-authentication"></a>Authentification personnalisée

Si votre application utilise une solution d’authentification personnalisée, vous devez vous assurer que le site mis à niveau a accès au système. Suivez les instructions de nouveau pour l’authentification personnalisée dans la [vue d’ensemble du Kit de développement logiciel .NET server] pour intégrer votre solution. Notez que les composants d’authentification personnalisés sont toujours en mode Aperçu avant.

##<a name="updating-clients"></a>Mise à jour des clients
Une fois que vous avez un serveur principal application Mobile opérationnel, vous pouvez travailler sur une nouvelle version de votre application cliente qui consomme. Applications Mobile inclut également une nouvelle version du client SDK et similaire à la mise à niveau du serveur ci-dessus, vous devrez supprimer toutes les références aux kits de développement logiciel Mobile Services avant d’installer les versions applications Mobile.

Un des principaux changements entre les versions est que les constructeurs n’avez plus besoin d’une touche application. Vous maintenant simplement passer dans l’URL de votre application Mobile. Par exemple, sur les clients .NET, la `MobileServiceClient` constructeur est maintenant :

        public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://contoso.azurewebsites.net", // URL of the Mobile App
        );

Vous pouvez lire sur l’installation du nouveau SDK et à l’aide de la nouvelle structure via les liens ci-dessous :

- [iOS version 3.0.0 ou version ultérieure](app-service-mobile-ios-how-to-use-client-library.md)
- [.NET (Windows/Xamarin) version 2.0.0 ou version ultérieure](app-service-mobile-dotnet-how-to-use-client-library.md)

Si votre application effectue utiliser des notifications push, notez les instructions d’enregistrement spécifique pour chaque plate-forme, comme des modifications ont été certaines il également.

Lorsque vous avez la nouvelle version du client prête, essayez-le par rapport à votre projet serveur mis à niveau. Après avoir validé qu’il fonctionne, vous pouvez lancer une nouvelle version de votre application à des clients. Au final, une fois que vos clients ont pu recevoir ces mises à jour, vous pouvez supprimer la version de Services mobiles de votre application. À ce stade, vous avez complètement mis à niveau vers un Service Mobile application à l’aide de la dernière server applications Mobile SDK.

<!-- URLs. -->

[Portail Azure]: https://portal.azure.com/
[Portail classique Azure]: https://manage.windowsazure.com/
[Quelles sont les applications Mobile ?]: app-service-mobile-value-prop.md
[I already use web sites and mobile services – how does App Service help me?]: /en-us/documentation/articles/app-service-mobile-value-prop-migration-from-mobile-services
[Kit de développement logiciel serveur application mobile]: http://www.nuget.org/packages/microsoft.azure.mobile.server
[Create a Mobile App]: app-service-mobile-xamarin-ios-get-started.md
[Add push notifications to your mobile app]: app-service-mobile-xamarin-ios-get-started-push.md
[Add authentication to your mobile app]: app-service-mobile-xamarin-ios-get-started-users.md
[Planificateur Azure]: /en-us/documentation/services/scheduler/
[Travail Web]: ../app-service-web/websites-webjobs-resources.md
[Comment utiliser le serveur .NET SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Migrate from Mobile Services to an App Service Mobile App]: app-service-mobile-migrating-from-mobile-services.md
[Migrate your existing Mobile Service to App Service]: app-service-mobile-migrating-from-mobile-services.md
[Service d’application tarifs]: https://azure.microsoft.com/en-us/pricing/details/app-service/
[Vue d’ensemble du Kit de développement logiciel .NET server]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
