<properties
    pageTitle="Activer la synchronisation hors connexion pour votre application de Mobile Azure (Cordova) | Microsoft Azure"
    description="Découvrez comment utiliser l’application Mobile Application Service cache et synchronisation des données en mode hors connexion dans votre application Cordova"
    documentationCenter="cordova"
    authors="adrianhall"
    manager="erikre"
    editor=""
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-cordova-ios"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="enable-offline-sync-for-your-cordova-mobile-app"></a>Activer la synchronisation hors connexion pour votre application mobile Cordova

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

Ce didacticiel présente la fonctionnalité de synchronisation hors connexion des applications Mobile Azure pour Cordova. Synchronisation hors connexion permet aux utilisateurs finaux d’interagir avec une application mobile&mdash;l’affichage, l’ajout ou modification de données&mdash;même lorsqu’il n’y a aucune connexion réseau. Modifications sont stockées dans une base de données locale ; une fois que l’appareil est en ligne, ces modifications sont synchronisées avec le service distant.

Ce didacticiel est basé sur la solution de démarrage rapide Cordova pour les applications mobiles que vous créez lorsque vous exécutez le didacticiel [Démarrer Apache Cordova rapide]. Dans ce didacticiel, vous met à jour la solution de démarrage rapide pour ajouter des fonctionnalités des applications de Mobile Azure en mode hors connexion. Nous met également en surbrillance le code en mode hors connexion spécifiques dans l’application.

Pour en savoir plus sur la fonctionnalité de synchronisation hors connexion, voir la rubrique [Synchronisation hors connexion de données dans les applications Mobile Azure]. Pour plus d’informations d’utilisation de l’API, voir le fichier [Lisez-moi] dans le plug-in.

## <a name="add-offline-sync-to-the-quickstart-solution"></a>Ajoutez la synchronisation hors connexion à la solution de démarrage rapide

Le code de synchronisation hors connexion doit être ajouté à l’application. Synchronisation hors connexion nécessite le plug-in cordova-sqlite-stockage, qui est automatiquement ajouté à votre application lorsque le plug-in applications Mobile Azure est inclus dans le projet. Le projet de démarrage rapide inclut à la fois de ces plug-ins.

1. Dans l’Explorateur de solutions de Visual Studio, ouvrez index.js et remplacez le code suivant

        var client,            // Connection to the Azure Mobile App backend
          todoItemTable;      // Reference to a table endpoint on backend

    avec ce code :

        var client,             // Connection to the Azure Mobile App backend
          todoItemTable, syncContext; // Reference to table and sync context

2. Ensuite, remplacez le code suivant :

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net');

    avec ce code :

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net');

        // Note: Requires at least version 2.0.0-beta6 of the Azure Mobile Apps plugin
        var store = new WindowsAzure.MobileServiceSqliteStore('store.db');

        store.defineTable({
          name: 'todoitem',
          columnDefinitions: {
              id: 'string',
              text: 'string',
              deleted: 'boolean',
              complete: 'boolean'
          }
        });

        // Get the sync context from the client
        syncContext = client.getSyncContext();

    Les compléments de code précédente initialisation du magasin local et définissez une table locale qui correspondent aux valeurs de colonne utilisés dans votre Azure back-end. (Vous n’avez pas besoin d’inclure toutes les valeurs de colonne dans ce code.)

    Vous obtenez une référence au contexte de synchronisation en appelant **getSyncContext**. Le contexte de synchronisation permet de conserver les relations entre tables en suivi et de distribution de modifications dans une application client a modifié lorsque **push** est appelée de toutes les tables.

3. Mettre à jour l’URL de l’application à votre URL de l’application Mobile application.

4. Ensuite, remplacez ce code :

        todoItemTable = client.getTable('todoitem'); // todoitem is the table name

    avec ce code :

        // todoItemTable = client.getTable('todoitem');

        // Initialize the sync context with the store
        syncContext.initialize(store).then(function () {

        // Get the local table reference.
        todoItemTable = client.getSyncTable('todoitem');

        syncContext.pushHandler = {
            onConflict: function (serverRecord, clientRecord, pushError) {
                // Handle the conflict.
                console.log("Sync conflict! " + pushError.getError().message);
                // Update failed, revert to server's copy.
                pushError.cancelAndDiscard();
              },
              onError: function (pushError) {
                  // Handle the error
                  // In the simulated offline state, you get "Sync error! Unexpected connection failure."
                  console.log("Sync error! " + pushError.getError().message);
              }
        };

        // Call a function to perform the actual sync
        syncBackend();

        // Refresh the todoItems
        refreshDisplay();

        // Wire up the UI Event Handler for the Add Item
        $('#add-item').submit(addItemHandler);
        $('#refresh').on('click', refreshDisplay);

    Le code précédent initialise le contexte de synchronisation, puis appelle getSyncTable (au lieu de getTable) pour obtenir une référence à la table locale.

    Ce code utilise la base de données locale pour toutes les créer, lire, mettre à jour et supprimer des opérations de table (CRUD).

    Cet exemple exécute simple gestion d’erreurs sur les conflits de synchronisation. Une application réelle serait traiter les erreurs différents, tels que les conditions de réseau et d’autres personnes serveur est en conflit. Pour des exemples de code, consultez l' [exemple de synchronisation hors connexion].

5. Ensuite, ajoutez cette fonction pour effectuer la synchronisation réelle.

        function syncBackend() {

          // Sync local store to Azure table when app loads, or when login complete.
          syncContext.push().then(function () {
              // Push completed

          });

          // Pull items from the Azure table after syncing to Azure.
          syncContext.pull(new WindowsAzure.Query('todoitem'));
        }

    Vous décidez du moment auquel envoyer les modifications sur le serveur principal application Mobile en appelant **push** sur l’objet **syncContext** utilisé par le client. Par exemple, vous pouvez ajouter un appel à **syncBackend** au gestionnaire d’événements bouton dans l’application telle qu’un nouveau bouton de synchronisation, ou vous pouvez ajouter l’appel à la fonction **addItemHandler** pour synchroniser dès qu’un nouvel élément est ajouté.

##<a name="offline-sync-considerations"></a>Considérations relatives à la synchronisation hors connexion

Dans l’échantillon, la méthode **push** de **syncContext** est appelée uniquement au démarrage de l’application de la fonction de rappel pour la connexion.  Dans une application réelle, vous pouvez également créer cette fonctionnalité de synchronisation déclenchée manuellement ou lorsque l’état du réseau change.

Lorsqu’une extraction est exécutée sur une table qui compte en attente de mises à jour locales suivis par le contexte, cette opération d’extraction déclenche automatiquement un push contexte précédent. Lors de l’actualisation, ajout et l’exécution d’éléments dans cet exemple, vous pouvez ignorer l’appel explicite **push** , dans la mesure où il peut être redondante (première vérification [Lisez-moi] pour l’état actuel de la fonctionnalité).

Dans le code fourni, tous les enregistrements dans la table todoItem distant sont interrogés, mais il est également possible filtrer les enregistrements en passant un id de requête et la requête **d’envoi**. Pour plus d’informations, voir la section *Synchronisation incrémentielles* au sein de [Synchronisation des données en mode hors connexion dans les applications Mobile Azure].

## <a name="optional-disable-authentication"></a>(Facultatif) Désactiver l’authentification

Si vous n’avez pas déjà défini l’authentification et que vous ne voulez pas configurer l’authentification avant la synchronisation hors connexion test, commentez la fonction de rappel pour la connexion, mais laissez le code à l’intérieur de la fonction de rappel supprimées.

Le code doit ressembler à ceci après commenter les lignes de connexion.

      // Login to the service.
      // client.login('twitter')
      //    .then(function () {
        syncContext.initialize(store).then(function () {
          // Leave the rest of the code in this callback function  uncommented.
                ...
        });
      // }, handleError);

À présent, l’application sera synchronisé avec le serveur principal Azure lorsque vous exécutez l’application à la place de lorsque vous êtes connecté.

## <a name="run-the-client-app"></a>Exécuter l’application client

Avec la synchronisation hors connexion maintenant activée, vous pouvez maintenant exécuter l’application cliente au moins une fois sur chaque plate-forme pour remplir la base de données de magasin local. Plus tard, vous simuler un scénario en mode hors connexion et modifier les données dans le magasin local lorsque l’application est en mode hors connexion.

## <a name="optional-test-the-sync-behavior"></a>(Facultatif) Tester le comportement de synchronisation

Dans cette section, vous allez modifier le projet client pour simuler un scénario en mode hors connexion à l’aide d’une URL d’application non valide pour votre serveur principal. Lorsque vous ajoutez ou modifiez des éléments de données, ces modifications sont stockées dans le magasin local, mais pas synchronisées avec le magasin de données principal jusqu'à ce que la connexion est rétablie.

1. Dans l’Explorateur de solutions, ouvrez le fichier de projet index.js et modifiez l’URL de l’application pour qu’il pointe vers une URL non valide, comme suit :

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net-fail');

2. Dans index.html, mettez à jour le fournisseur `<meta>` élément avec la même URL non valide.

        <meta http-equiv="Content-Security-Policy" content="default-src 'self' data: gap: http://yourmobileapp.azurewebsites.net-fail; style-src 'self'; media-src *">

3. Créer et exécuter l’application client et notez qu’une exception est enregistrée dans la console lorsque l’application tente de synchroniser avec le serveur principal après la connexion. Tous les nouveaux éléments que vous ajoutez existe uniquement dans le magasin local jusqu'à ce qu’ils peuvent être installés sur le serveur principal mobile. L’application cliente se comporte comme s’est connecté au serveur principal, prenant en charge que tous les créent, lire, mettre à jour les opérations de suppression (CRUD).

4. Fermez l’application, puis redémarrez-le pour vérifier que les nouveaux éléments que vous avez créé sont conservées au magasin local.

5. (Facultatif) Utilisez Visual Studio pour afficher votre table de base de données SQL Azure pour vérifier que les données dans la base de données principale n’a pas changé.

    Dans Visual Studio, ouvrez **l’Explorateur de serveur**. Accédez à votre base de données dans **Azure**->**Bases de données SQL**. Avec le bouton droit de votre base de données et sélectionnez **Ouvrir dans l’Explorateur d’objets SQL Server**. Maintenant que vous pouvez accéder à votre tableau de base de données SQL et son contenu.


## <a name="optional-test-the-reconnection-to-your-mobile-backend"></a>(Facultatif) Tester la reconnexion à votre serveur principal mobile

Dans cette section vous se reconnectera l’application sur le serveur principal mobile, simuler l’application revenant à un état en ligne. Lorsque vous vous connectez, données sont synchronisées avec votre version de serveur mobile.

1. Rouvrez index.js et corrigez l’URL de l’application pour qu’il pointe vers l’URL est correcte.

2. Rouvrez index.html et corrigez l’URL de l’application dans le fournisseur `<meta>` élément.

3. Reconstruire et exécutez l’application client. L’application tente de synchroniser avec le système principal de l’application mobile après la connexion. Vérifiez qu’aucune exception n’est enregistrée dans la console de débogage.

4. (Facultatif) Afficher les données mises à jour à l’aide de l’Explorateur d’objets SQL Server ou un outil reste comme Fiddler. Notez que les données ont été synchronisées entre la base de données principale et le magasin local.

    Vous pouvez remarquer les données a été synchronisées entre la base de données et le magasin local contient les éléments que vous avez ajouté alors que votre application a été déconnectée.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Synchronisation de données hors connexion dans les applications mobiles Azure]

* [Cloud garde : synchronisation hors connexion dans Azure Services mobiles] \(Remarque : la vidéo est activée Services mobiles, mais la synchronisation hors connexion fonctionne de la même façon dans les applications Mobile Azure\)

* [Outils Visual Studio pour Apache Cordova]

## <a name="next-steps"></a>Étapes suivantes

* Rechercher des fonctionnalités de synchronisation hors connexion plus avancées telles que la résolution des conflits dans l' [exemple de synchronisation hors connexion]
* Examinez la référence de l’API dans le [fichier Lisez-moi] de synchronisation hors connexion

<!-- ##Summary -->

<!-- Images -->

<!-- URLs. -->
[Démarrage rapide Cordova Apache]: app-service-mobile-cordova-get-started.md
[FICHIER LISEZ-MOI]: https://github.com/Azure/azure-mobile-apps-js-client#offline-data-sync-preview
[exemple de synchronisation hors connexion]: https://github.com/shrishrirang/azure-mobile-apps-quickstarts/tree/samples/client/cordova/ZUMOAPPNAME
[Synchronisation de données hors connexion dans les applications mobiles Azure]: app-service-mobile-offline-data-sync.md
[Couverture du cloud : Synchronisation hors connexion dans Azure Services mobiles]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Adding Authentication]: app-service-mobile-cordova-get-started-users.md
[authentication]: app-service-mobile-cordova-get-started-users.md
[Work with the .NET backend server SDK for Azure Mobile Apps]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Visual Studio Community 2015]: http://www.visualstudio.com/
[Outils Visual Studio pour Apache Cordova]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
[Apache Cordova SDK]: app-service-mobile-cordova-how-to-use-client-library.md
[ASP.NET Server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js Server SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md
