<properties
    pageTitle="Activer la synchronisation hors connexion pour votre application universel Windows plateforme (UWP) avec les applications Mobile | Service application Azure"
    description="Découvrez comment utiliser une application Mobile Azure cache et synchronisation des données en mode hors connexion dans votre application universel Windows plateforme (UWP)."
    documentationCenter="windows"
    authors="adrianhall"
    manager="erikre"
    editor=""
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="enable-offline-sync-for-your-windows-app"></a>Activer la synchronisation hors connexion pour votre application Windows

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Vue d’ensemble

Ce didacticiel montre comment ajouter une prise en charge en mode hors connexion à une application universel Windows plateforme (UWP) à l’aide d’une application Mobile Azure principal. Synchronisation hors connexion permet aux utilisateurs finaux d’interagir avec une application mobile, l’affichage, l’ajout ou modification des données - même lorsqu’il n’y a aucune connexion réseau. Modifications sont stockées dans une base de données locale. Une fois que l’appareil est en ligne, ces modifications sont synchronisées avec le serveur principal à distance.

Dans ce didacticiel, vous mettez à jour le projet application UWP à partir du didacticiel [créer une application Windows] pour prendre en charge les fonctionnalités des applications de Mobile Azure en mode hors connexion. Si vous n’utilisez pas le projet de serveur de démarrage rapide téléchargé, vous devez ajouter les packages d’extension de données access à votre projet. Pour plus d’informations sur les packages d’extension de serveur, voir [collaborer avec le Kit de développement pour applications Mobile Azure du serveur principal .NET](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Pour en savoir plus sur la fonctionnalité de synchronisation hors connexion, voir la rubrique [Synchronisation hors connexion de données dans les applications Mobile Azure].

## <a name="requirements"></a>Configuration requise

Ce didacticiel requiert les composants requis suivants :

* Visual Studio 2013 sur Windows 8.1 ou version ultérieure.
* Fin de [créer une application Windows][créer une application windows].
* [Azure Mobile Services SQLite Store][sqlite store nuget]
* [SQLite pour le développement de la plateforme Windows universel](http://www.sqlite.org/downloads)

## <a name="update-the-client-app-to-support-offline-features"></a>Mettre à jour l’application client pour prendre en charge des fonctionnalités en mode hors connexion

Azure application Mobile en mode hors connexion fonctionnalités vous permettent d’interagir avec une base de données locale lorsque vous êtes dans un scénario en mode hors connexion. Pour utiliser ces fonctionnalités dans votre application, vous initialisez un [SyncContext] [ synccontext] dans un magasin local. Puis référencer votre table via l’interface[IMobileServiceSyncTable] [IMobileServiceSyncTable]. SQLite est utilisé comme le magasin local sur l’appareil.

1. Installez le [runtime SQLite pour la plateforme Windows universel](http://sqlite.org/2016/sqlite-uwp-3120200.vsix).

2. Dans Visual Studio, ouvrez le Gestionnaire de package NuGet pour le projet application UWP que vous avez effectuées dans le didacticiel [créer une application Windows] .
    Rechercher et installer le package NuGet **Microsoft.Azure.Mobile.Client.SQLiteStore** .

4. Dans l’Explorateur de solutions, cliquez sur **références** > **Ajouter une référence...**  >  **Universel de Windows** > **Extensions**, puis activez **SQLite de plateforme Windows universel** et **Runtime 2015 Visual C++ pour les applications de la plateforme Windows universel**.

    ![Ajouter une référence de SQLite UWP][1]

5. Ouvrez le fichier MainPage.xaml.cs et ne commentez pas la `#define OFFLINE_SYNC_ENABLED` définition.

6. Dans Visual Studio, appuyez sur la touche **F5** pour reconstruire et exécutez l’application client. L’application fonctionne de la même manière qu’avant que vous avez activé la synchronisation hors connexion. Toutefois, la base de données locale est maintenant remplie avec des données qui peuvent être utilisées dans un scénario en mode hors connexion.

## <a name="update-sync"></a>Mettre à jour l’application pour vous déconnecter le système principal

Dans cette section, vous annulez la connexion à votre serveur principal application Mobile pour simuler une situation en mode hors connexion. Lorsque vous ajoutez des éléments de données, votre gestionnaire d’exceptions vous indique que l’application est en mode hors connexion. Dans cet état, les nouveaux éléments ajoutés dans le magasin local et sont synchronisés avec le système principal de l’application mobile lors de la prochaine exécution push dans un état connecté.

1. Modifier App.xaml.cs dans le projet partagé. Commentez l’initialisation de la **MobileServiceClient** et ajoutez la ligne suivante, qui utilise une URL de l’application mobile non valides :

         public static MobileServiceClient MobileService = new MobileServiceClient("https://your-service.azurewebsites.fail");

    Vous pouvez également illustrer comportement en mode hors connexion en désactivant Wi-Fi et réseaux cellulaires sur l’appareil ou utiliser le mode avion.

2. Appuyez sur **F5** pour générer et exécuter l’application. Notez votre synchronisation a échoué lors de l’actualisation lorsque l’application est lancée.

3. Entrez de nouveaux éléments et notez que push échoue dont l’état [CancelledByNetworkError] chaque fois que vous cliquez sur **Enregistrer**. Toutefois, les nouvelles tâches existent dans le magasin local jusqu'à ce qu’ils peuvent être installés sur le système principal de l’application mobile.  Dans une application de production, si vous supprimez ces exceptions de l’application client se comporte comme s’il est toujours connecté sur le serveur principal de l’application mobile.

4. Fermez l’application, puis redémarrez-le pour vérifier que les nouveaux éléments que vous avez créé sont conservées au magasin local.

5. (Facultatif) Dans Visual Studio, ouvrez **l’Explorateur de serveur**. Accédez à votre base de données dans **Azure**->**Bases de données SQL**. Avec le bouton droit de votre base de données et sélectionnez **Ouvrir dans l’Explorateur d’objets SQL Server**. Maintenant que vous pouvez accéder à votre tableau de base de données SQL et son contenu. Vérifiez que les données dans la base de données principale n’a pas changé.

6. (Facultatif) Utiliser un outil reste comme Fiddler ou Postman pour votre serveur principal mobile, à l’aide d’une requête GET dans l’écran de la requête `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`.

## <a name="update-online-app"></a>Mettre à jour l’application pour vous reconnecter la principale application Mobile

Dans cette section, vous reconnectez l’application sur le serveur principal de l’application mobile. Ces modifications simulent une reconnexion réseau dans l’application.

Lorsque vous exécutez tout d’abord l’application, la `OnNavigatedTo` Gestionnaire d’événements appelle `InitLocalStoreAsync`. Cette méthode appelle à son tour `SyncAsync` pour synchroniser votre magasin local avec la base de données principale. L’application tente de synchroniser au démarrage.

1. Ouvrez App.xaml.cs dans le projet partagé, puis supprimez les commentaires de votre précédente initialisation de `MobileServiceClient` à utiliser le bon l’URL de l’application mobile.

2. Appuyez sur la touche **F5** pour régénérer et exécuter l’application. L’application synchronise vos modifications locales avec le serveur principal de l’application Mobile Azure à l’aide de push » et « tirer lorsque la `OnNavigatedTo` Gestionnaire d’événements s’exécute.

3. (Facultatif) Afficher les données mises à jour à l’aide de l’Explorateur d’objets SQL Server ou un outil reste comme Fiddler. Notez les données ont été synchronisée entre la base de données Azure Mobile application principale et le magasin local.

4. Dans l’application, cliquez sur la case à cocher en regard des éléments quelques effectuer dans le magasin local.

  `UpdateCheckedTodoItem`appels `SyncAsync` pour synchroniser les terminé l’élément avec le système principal de l’application Mobile. `SyncAsync`appels push et extraction. Toutefois, **chaque fois que vous exécutez une extraction par rapport à une table qui le client a apporter des modifications à une campagne est toujours exécutée automatiquement**. Ce comportement garantit que toutes les tables dans le magasin local ainsi que des relations restent cohérentes. Ce problème peut entraîner un push inattendu.  Pour plus d’informations sur ce problème, voir [Synchronisation des données en mode hors connexion dans les applications Mobile Azure].


##<a name="api-summary"></a>API résumé

Pour prendre en charge les fonctionnalités de services mobiles en mode hors connexion, nous avons utilisé l’interface [IMobileServiceSyncTable] et initialisé [MobileServiceClient.SyncContext] [ synccontext] avec une base de données locale SQLite. En mode hors connexion, les opérations CRUD normales pour les applications mobiles fonctionnent comme si l’application est toujours connectée tandis que les opérations se produisent par rapport au magasin local. Les méthodes suivantes sont utilisées pour synchroniser le magasin local avec le serveur :

*  **[PushAsync]** Étant donné que cette méthode est un membre de [IMobileServicesSyncContext], modifications dans toutes les tables sont déplacées vers le serveur principal. Uniquement les enregistrements des modifications locales sont envoyés au serveur.

* **[PullAsync] ** 
   une extraction est démarrée à partir d’un [IMobileServiceSyncTable]. Lorsqu’il existe des marques de révision dans le tableau, un push implicite est exécuté pour vous assurer que toutes les tables dans le magasin local ainsi que des relations restent cohérentes. Le paramètre *pushOtherTables* contrôle si d’autres tables dans le contexte sont enfoncées un push implicite. Le paramètre de *requête* prend une [IMobileServiceTableQuery<T> ] [ IMobileServiceTableQuery] 
   ou chaîne de requête OData pour filtrer les données renvoyées. Le paramètre *queryId* est utilisé pour définir la synchronisation incrémentielle. Pour plus d’informations, voir  [Synchronisation des données en mode hors connexion dans les applications Mobile Azure](app-service-mobile-offline-data-sync.md#how-sync-works).

* **[PurgeAsync]** Votre application doit appeler régulièrement cette méthode pour éliminer les données obsolètes à partir du magasin local. Utilisez le paramètre *forcer* lorsque vous avez besoin supprimer définitivement les modifications qui n’ont pas encore été synchronisées.

Pour plus d’informations sur ces concepts, voir [Synchronisation des données en mode hors connexion dans les applications Mobile Azure](app-service-mobile-offline-data-sync.md#how-sync-works).

## <a name="more-info"></a>Plus d’informations

Les rubriques suivantes fournissent des informations générales supplémentaires sur la fonctionnalité de synchronisation hors connexion des applications mobiles :

* [Synchronisation de données hors connexion dans les applications mobiles Azure]
* [Comment faire pour les applications mobiles Azure .NET SDK][8]

<!-- Anchors. -->
[Update the app to support offline features]: #enable-offline-app
[Update the sync behavior of the app]: #update-sync
[Update the app to reconnect your Mobile Apps backend]: #update-online-app
[Next Steps]:#next-steps

<!-- Images -->
[1]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/app-service-mobile-add-reference-sqlite-dialog.png
[11]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/app-service-mobile-add-wp81-reference-sqlite-dialog.png
[13]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/cpu-architecture.png


<!-- URLs. -->
[Synchronisation de données hors connexion dans les applications mobiles Azure]: app-service-mobile-offline-data-sync.md
[créer une application windows]: app-service-mobile-windows-store-dotnet-get-started.md
[SQLite for Windows 8.1]: http://go.microsoft.com/fwlink/?LinkID=716919
[SQLite for Windows Phone 8.1]: http://go.microsoft.com/fwlink/?LinkID=716920
[SQLite for Windows 10]: http://go.microsoft.com/fwlink/?LinkID=716921
[synccontext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[sqlite store nuget]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client.SQLiteStore/
[IMobileServiceSyncTable]: https://msdn.microsoft.com/library/azure/mt691742(v=azure.10).aspx
[IMobileServiceTableQuery]: https://msdn.microsoft.com/library/azure/dn250631(v=azure.10).aspx
[IMobileServicesSyncContext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.imobileservicesynccontext(v=azure.10).aspx
[MobileServicePushFailedException]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushfailedexception(v=azure.10).aspx
[Status]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushcompletionresult.status(v=azure.10).aspx
[CancelledByNetworkError]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushstatus(v=azure.10).aspx
[PullAsync]: https://msdn.microsoft.com/library/azure/mt667558(v=azure.10).aspx
[PushAsync]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileservicesynccontextextensions.pushasync(v=azure.10).aspx
[PurgeAsync]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.imobileservicesynctable.purgeasync(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
