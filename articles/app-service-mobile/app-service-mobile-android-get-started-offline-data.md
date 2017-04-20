<properties
    pageTitle="Activer la synchronisation hors connexion pour votre application de Mobile Azure (Android)"
    description="Découvrez comment utiliser l’application Service Mobile applications cache et synchronisation des données en mode hors connexion dans votre application Android"
    documentationCenter="android"
    authors="ysxu"
    manager="erikre"
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="yuaxu"/>

# <a name="enable-offline-sync-for-your-android-mobile-app"></a>Activer la synchronisation hors connexion pour votre application mobile Android

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Vue d’ensemble

Ce didacticiel décrit la fonctionnalité de synchronisation hors connexion des applications Mobile Azure pour Android. Synchronisation hors connexion permet aux utilisateurs finaux d’interagir avec une application mobile&mdash;l’affichage, l’ajout ou modification de données&mdash;même lorsqu’il n’y a aucune connexion réseau. Modifications sont stockées dans une base de données locale. Une fois que l’appareil est en ligne, ces modifications sont synchronisées avec le serveur principal à distance.

S’il s’agit de votre première utilisation d’applications Mobile Azure, vous devez effectuer tout d’abord le didacticiel [créer une application Android]. Si vous n’utilisez pas le projet de serveur de démarrage rapide téléchargé, vous devez ajouter les packages d’extension de données access à votre projet. Pour plus d’informations sur les packages d’extension de serveur, voir [collaborer avec le Kit de développement pour applications Mobile Azure du serveur principal .NET](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Pour en savoir plus sur la fonctionnalité de synchronisation hors connexion, voir la rubrique [Synchronisation hors connexion de données dans les applications Mobile Azure].

## <a name="update-the-app-to-support-offline-sync"></a>Mettre à jour l’application pour prendre en charge synchronisation hors connexion

Avec la synchronisation hors connexion, vous lire et écrivez à partir d’une *table de synchronisation* (à l’aide de l’interface *IMobileServiceSyncTable* ), qui fait partie d’une base de données **SQLite** sur votre appareil.

Pour envoyer et extraire des modifications entre l’appareil et Azure Mobile Services, vous utilisez un *contexte de synchronisation* (*MobileServiceClient.SyncContext*), qui vous initialisez avec la base de données locale pour stocker les données localement.

1. Dans `TodoActivity.java`, commentez la définition existante de `mToDoTable` et ne commentez pas la version de la table synchronisation :

        private MobileServiceSyncTable<ToDoItem> mToDoTable;

2. Dans la `onCreate` méthode, commentez l’initialisation existante de `mToDoTable` et ne commentez pas cette définition :

        mToDoTable = mClient.getSyncTable("ToDoItem", ToDoItem.class);

3. Dans `refreshItemsFromTable` Commentez la définition de `results` et ne commentez pas cette définition :

        // Offline Sync
        final List<ToDoItem> results = refreshItemsFromMobileServiceTableSyncTable();

4. Commentez la définition de `refreshItemsFromMobileServiceTable`.

5. Supprimez les commentaires de la définition de `refreshItemsFromMobileServiceTableSyncTable`:

        private List<ToDoItem> refreshItemsFromMobileServiceTableSyncTable() throws ExecutionException, InterruptedException {
            //sync the data
            sync().get();
            Query query = QueryOperations.field("complete").
                    eq(val(false));
            return mToDoTable.read(query).get();
        }

6. Supprimez les commentaires de la définition de `sync`:

        private AsyncTask<Void, Void, Void> sync() {
            AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
                @Override
                protected Void doInBackground(Void... params) {
                    try {
                        MobileServiceSyncContext syncContext = mClient.getSyncContext();
                        syncContext.push().get();
                        mToDoTable.pull(null).get();
                    } catch (final Exception e) {
                        createAndShowDialogFromTask(e, "Error");
                    }
                    return null;
                }
            };
            return runAsyncTask(task);
        }

## <a name="test-the-app"></a>Tester l’application

Dans cette section, vous testez le comportement avec Wi-Fi sur et puis désactivez l’option Wi-Fi pour créer un scénario en mode hors connexion.

Lorsque vous ajoutez des éléments de données, ils sont stockés dans le magasin SQLite local, mais pas synchronisés avec le service mobile jusqu'à ce que vous appuyez sur le bouton **Actualiser** . Autres applications peuvent avoir des besoins différents concernant les applications lorsque les données doivent être synchronisés, mais à des fins de démonstration ce didacticiel a l’utilisateur demander de manière explicite.

Lorsque vous appuyez sur ce bouton, une nouvelle tâche d’arrière-plan démarre. Il pousse tout d’abord toutes les modifications apportées au magasin local à l’aide de contexte de synchronisation, puis extrait tous les modifié des données à partir d’Azure à la table locale.

### <a name="offline-testing"></a>Test en mode hors connexion

1. Placez le périphérique ou simulator en *Mode avion*. Cela crée un scénario hors connexion.

2. Ajouter *des tâches* ou marquer certains éléments comme étant terminée. Fermez le périphérique ou simulator (ou forcer l’application) et redémarrez. Vérifiez que vos modifications ont été conservées sur le périphérique, car ils sont conservés dans le magasin SQLite local.

3. Afficher le contenu de la table Azure *TodoItem* avec un SQL outil tel que *SQL Server Management Studio*, ou un client reste tel que *Fiddler* ou *Postman*. Vérifiez que les nouveaux éléments n’ont _pas_ été synchronisées sur le serveur

    + Pour un serveur principal Node.js, accédez au [portail Azure](https://portal.azure.com/)et dans votre application Mobile et cliquez sur **Tables facile** > **TodoItem** pour afficher le contenu de la `TodoItem` table.
    + Pour un serveur principal .NET, afficher le contenu de tableau avec un outil SQL telles que *SQL Server Management Studio*, ou un client reste tel que *Fiddler* ou *Postman*.

4. Activez Wi-Fi de l’appareil ou simulator. Ensuite, appuyez sur le bouton **Actualiser** .

5. Afficher les données TodoItem à nouveau dans le portail Azure. La TodoItems nouveaux et modifiés doit maintenant apparaître.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Synchronisation de données hors connexion dans les applications mobiles Azure]

* [Cloud garde : synchronisation hors connexion dans Azure Services mobiles] \(Remarque : la vidéo est activée Services mobiles, mais la synchronisation hors connexion fonctionne de la même façon dans les applications Mobile Azure\)


<!-- URLs. -->

[Synchronisation de données hors connexion dans les applications mobiles Azure]: app-service-mobile-offline-data-sync.md

[Créer une application Android]: app-service-mobile-android-get-started.md

[Couverture du cloud : Synchronisation hors connexion dans Azure Services mobiles]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: http://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/

