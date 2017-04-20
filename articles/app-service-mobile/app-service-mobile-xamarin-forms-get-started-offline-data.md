<properties
    pageTitle="Activer la synchronisation hors connexion pour votre application de Mobile Azure (Xamarin.Forms) | Microsoft Azure"
    description="Découvrez comment utiliser l’application Mobile Application Service cache et synchronisation des données en mode hors connexion dans votre application Xamarin.Forms"
    documentationCenter="xamarin"
    authors="adrianhall"
    manager="yochayk"
    editor=""
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="adrianha"/>

# <a name="enable-offline-sync-for-your-xamarinforms-mobile-app"></a>Activer la synchronisation hors connexion pour votre application mobile Xamarin.Forms

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Vue d’ensemble
Ce didacticiel présente la fonctionnalité de synchronisation hors connexion des applications Mobile Azure pour Xamarin.Forms. Synchronisation hors connexion permet aux utilisateurs finaux d’interagir avec une application mobile, l’affichage, l’ajout ou modification de données--même lorsqu’il n’y a aucune connexion réseau. Modifications sont stockées dans une base de données locale. Une fois que l’appareil est en ligne, ces modifications sont synchronisées avec le service distant.

Ce didacticiel est basé sur la solution de démarrage rapide Xamarin.Forms pour les applications mobiles que vous créez lorsque vous exécutez le didacticiel [créer une application iOS Xamarin]. La solution de démarrage rapide pour Xamarin.Forms contient le code pour prendre en charge synchronisation hors connexion, qui simplement doit être activé. Dans ce didacticiel, vous mettez à jour la solution de démarrage rapide pour activer les fonctionnalités des applications de Mobile Azure en mode hors connexion. Nous avons également mettre en surbrillance le code en mode hors connexion spécifiques dans l’application. Si vous n’utilisez pas la solution de démarrage rapide téléchargé, vous devez ajouter les packages d’extension de données access à votre projet. Pour plus d’informations sur les packages d’extension de serveur, voir [collaborer avec le Kit de développement pour applications Mobile Azure du serveur principal .NET][1].

Pour en savoir plus sur la fonctionnalité de synchronisation hors connexion, reportez-vous à la rubrique [Synchronisation hors connexion de données dans les applications Mobile Azure][2].

## <a name="enable-offline-sync-functionality-in-the-quickstart-solution"></a>Activer la fonctionnalité de synchronisation hors connexion dans la solution de démarrage rapide

Le code de synchronisation hors connexion est inclus dans le projet à l’aide de directives de préprocesseur c#. Lorsque la **hors connexion\_synchronisation\_activé** symbole est défini, ces chemins d’accès de code sont inclus dans la version. Pour les applications Windows, vous devez également installer la plateforme SQLite.

1. Dans Visual Studio, cliquez sur la solution > **Gérer les Packages NuGet pour Solution...**, puis recherchez et installez le package NuGet **Microsoft.Azure.Mobile.Client.SQLiteStore** pour tous les projets dans la solution.

2. Dans l’Explorateur de solutions, ouvrez le fichier TodoItemManager.cs du projet avec **Portable** dans le nom, qui est un projet de bibliothèque de classes Portable, supprimez puis la directive préprocesseur suivante :

        #define OFFLINE_SYNC_ENABLED

4. (Facultatif) Pour prendre en charge les appareils Windows, installez un des packages runtime SQLite suivants :

    * **Runtime Windows 8.1 :** Installer [SQLite pour Windows 8.1][3].
    * **Windows Phone 8.1 :** Installer [SQLite pour Windows Phone 8.1][4].
    * **Plateforme Windows universel** Installer [SQLite l’universel universel de Windows][5].

    Bien que le démarrage rapide ne contient pas d’un projet universel de Windows, la plateforme Windows universel est pris en charge avec des formulaires Xamarin.

5. (Facultatif) Dans chaque projet d’application Windows, cliquez sur **références** > **Ajouter une référence**, développez le dossier **Windows** > **Extensions**.
    Activer les appropriées **SQLite pour Windows** SDK ainsi que le Kit de développement **Windows Visual C++ 2013 Runtime pour** .
    Les noms de SQLite SDK varient légèrement avec chaque plateforme Windows.

## <a name="review-the-client-sync-code"></a>Passez en revue le code de synchronisation client

Voici un bref aperçu de ce qui est déjà inclus dans le code du didacticiel à l’intérieur de la `#if OFFLINE_SYNC_ENABLED` directives. La fonctionnalité de synchronisation hors connexion est dans le fichier de projet TodoItemManager.cs dans le projet de bibliothèque de classes Portable. Pour une vue d’ensemble conceptuelle de la fonction, voir [Synchronisation des données en mode hors connexion dans les applications Mobile Azure][2].

* Avant de pouvoir effectuer les opérations de table, le magasin local doit être initialisé. La base de données de magasin local est initialisé dans le constructeur **TodoItemManager** en utilisant le code suivant :

        var store = new MobileServiceSQLiteStore(OfflineDbPath);
        store.DefineTable<TodoItem>();

        //Initializes the SyncContext using the default IMobileServiceSyncHandler.
        this.client.SyncContext.InitializeAsync(store);

        this.todoTable = client.GetSyncTable<TodoItem>();

    Ce code crée un nouveau SQLite base de données locale à l’aide de la classe **MobileServiceSQLiteStore** .

    La méthode **DefineTable** crée une table dans le magasin local qui correspond aux champs dans le type fourni.  Le type ne possède pas d’inclure toutes les colonnes qui se trouvent dans la base de données distante. Il est possible de stocker un sous-ensemble de colonnes.

* Le champ **todoTable** de **TodoItemManager** est un type de **IMobileServiceSyncTable** au lieu de **IMobileServiceTable**. Cette classe utilise la base de données locale pour toutes les créer, lire, mettre à jour et supprimer des opérations de table (CRUD). Vous décidez lorsque ces modifications sont envoyées sur le serveur principal application Mobile en appelant **PushAsync** sur **IMobileServiceSyncContext**. Le contexte de synchronisation permet de conserver les relations entre tables en suivi et de distribution de modifications dans une application client a modifié lorsque **PushAsync** est appelé de toutes les tables.

    La méthode **SyncAsync** suivante est appelée à synchroniser avec le serveur principal application Mobile :

        public async Task SyncAsync()
        {
            ReadOnlyCollection<MobileServiceTableOperationError> syncErrors = null;

            try
            {
                await this.client.SyncContext.PushAsync();

                await this.todoTable.PullAsync(
                    "allTodoItems",
                    this.todoTable.CreateQuery());
            }
            catch (MobileServicePushFailedException exc)
            {
                if (exc.PushResult != null)
                {
                    syncErrors = exc.PushResult.Errors;
                }
            }

            // Simple error/conflict handling.
            if (syncErrors != null)
            {
                foreach (var error in syncErrors)
                {
                    if (error.OperationKind == MobileServiceTableOperationKind.Update && error.Result != null)
                    {
                        //Update failed, reverting to server's copy.
                        await error.CancelAndUpdateItemAsync(error.Result);
                    }
                    else
                    {
                        // Discard local change.
                        await error.CancelAndDiscardItemAsync();
                    }

                    Debug.WriteLine(@"Error executing sync operation. Item: {0} ({1}). Operation discarded.",
                        error.TableName, error.Item["id"]);
                }
            }
        }

    Cet exemple utilise simple gestion d’erreurs avec le Gestionnaire de synchronisation par défaut. Une application réelle serait traiter les erreurs divers tels que les conditions de réseau et serveur est en conflit en utilisant une mise en œuvre **IMobileServiceSyncHandler** personnalisée.

## <a name="offline-sync-considerations"></a>Considérations relatives à la synchronisation hors connexion

Dans l’échantillon, la méthode **SyncAsync** est appelée uniquement au démarrage et lorsqu’une synchronisation est demandée.  Pour lancer une synchronisation dans une application Android ou iOS, extraire vers le bas dans la liste d’éléments ; pour Windows, utilisez le bouton **synchroniser** . Dans une application réelle, vous pouvez créer également le déclencheur synchronisation lorsque l’état du réseau change.

Lorsqu’une extraction est exécutée sur une table qui comporte en attente mises à jour locales suivie par le contexte, qui extraient des opération déclencheurs automatiquement un push contexte précédent. Lors de l’actualisation, ajout et réalisation d’éléments dans cet exemple, vous pouvez omettre l’appel **PushAsync** explicite.

Dans le code fourni, tous les enregistrements dans la table TodoItem distante sont interrogés, mais il est également possible filtrer les enregistrements en passant un id de requête et la requête à **PushAsync**. Pour plus d’informations, consultez la section *Synchronisation incrémentielles* au sein de [Synchronisation des données en mode hors connexion dans les applications Mobile Azure][2].

## <a name="run-the-client-app"></a>Exécuter l’application client

Avec la synchronisation hors connexion maintenant activée, exécutez l’application cliente au moins une fois sur chaque plate-forme pour remplir la base de données de magasin local. Plus tard, simuler un scénario en mode hors connexion et modifier les données dans le magasin local lorsque l’application est en mode hors connexion.

## <a name="update-the-sync-behavior-of-the-client-app"></a>Mettre à jour le comportement de synchronisation de l’application client

Dans cette section, modifiez le projet client pour simuler un scénario en mode hors connexion à l’aide d’une URL d’application non valide pour votre serveur principal. Par ailleurs, vous pouvez désactiver les connexions réseau en déplaçant votre appareil à « Mode avion ».  Lorsque vous ajoutez ou modifiez des éléments de données, ces modifications sont stockées dans le magasin local, mais pas synchronisées avec le magasin de données principal jusqu'à ce que la connexion est rétablie.

1. Dans l’Explorateur de solutions, ouvrez le fichier de projet Constants.cs du projet **Portable** et définissez la valeur de `ApplicationURL` pour qu’il pointe vers une URL non valide :

        public static string ApplicationURL = @"https://your-service.azurewebsites.net/";

2. Ouvrez le fichier TodoItemManager.cs du projet **Portable** , puis ajoutez **intercepter** pour la classe **Exception** de base pour le bloc **try... captures** dans **SyncAsync**. Ce bloc **intercepter** écrit le message d’exception à la console, comme suit :

            catch (Exception ex)
            {
                Console.Error.WriteLine(@"Exception: {0}", ex.Message);
            }

3. Générez et exécutez l’application client.  Ajouter quelques nouveaux éléments. Notez qu’une exception est enregistrée dans la console pour chaque tentative de synchronisation avec le serveur principal. Ces nouveaux éléments existent uniquement dans le magasin local jusqu'à ce qu’ils peuvent être installés sur le serveur principal mobile. L’application cliente se comporte comme s’il est connecté au serveur principal, prenant en charge que tous les créent, lire, mettre à jour les opérations de suppression (CRUD).

4. Fermez l’application, puis redémarrez-le pour vérifier que les nouveaux éléments que vous avez créé sont conservées au magasin local.

5. (Facultatif) Utilisez Visual Studio pour afficher votre table de base de données SQL Azure pour vérifier que les données dans la base de données principale n’a pas changé.

    Dans Visual Studio, ouvrez **l’Explorateur de serveur**. Accédez à votre base de données dans **Azure**->**Bases de données SQL**. Avec le bouton droit de votre base de données et sélectionnez **Ouvrir dans l’Explorateur d’objets SQL Server**. Maintenant que vous pouvez accéder à votre tableau de base de données SQL et son contenu.

## <a name="update-the-client-app-to-reconnect-your-mobile-backend"></a>Mettre à jour l’application client pour vous reconnecter la principale mobile

Dans cette section, vous reconnecter l’application sur le serveur principal mobile, simuler l’application revenant à un état en ligne. Lorsque vous effectuez le mouvement d’actualisation, données sont synchronisées à votre serveur principal mobile.

1. Rouvrez Constants.cs. Corriger la `applicationURL` pour pointer vers l’URL est correcte.

2. Reconstruire et exécutez l’application client. L’application tente de synchroniser avec le système principal de l’application mobile après le lancement. Vérifiez qu’aucune exception n’est enregistrée dans la console de débogage.

3. (Facultatif) Afficher les données mises à jour à l’aide de l’Explorateur d’objets SQL Server ou un outil reste comme Fiddler ou [Postman][6]. Notez que les données ont été synchronisées entre la base de données principale et le magasin local.

    Vous pouvez remarquer les données a été synchronisées entre la base de données et le magasin local contient les éléments que vous avez ajouté alors que votre application a été déconnectée.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Synchronisation de données hors connexion dans les applications mobiles Azure][2]
* [Comment faire pour les applications mobiles Azure .NET SDK][8]

<!-- URLs. -->
[1]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[2]: app-service-mobile-offline-data-sync.md
[3]: http://go.microsoft.com/fwlink/p/?LinkID=716919
[4]: http://go.microsoft.com/fwlink/p/?LinkID=716920
[5]: http://sqlite.org/2016/sqlite-uwp-3120200.vsix
[6]: https://www.getpostman.com/
[7]: http://www.telerik.com/fiddler
[8]: app-service-mobile-dotnet-how-to-use-client-library.md