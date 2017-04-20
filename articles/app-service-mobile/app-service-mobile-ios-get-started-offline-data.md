<properties
    pageTitle="Activer la synchronisation hors connexion pour votre application de Mobile Azure (iOS)"
    description="Découvrez comment utiliser l’application Service Mobile applications cache et synchronisation des données en mode hors connexion dans votre application iOS"
    documentationCenter="ios"
    authors="ysxu"
    manager="yochayk"
    editor=""
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="yuaxu"/>

# <a name="enable-offline-sync-for-your-ios-mobile-app"></a>Activer la synchronisation hors connexion pour votre application mobile iOS

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Vue d’ensemble

Ce didacticiel décrit la fonctionnalité de synchronisation hors connexion des applications Mobile Azure pour iOS. Synchronisation hors connexion permet aux utilisateurs finaux d’interagir avec une application mobile&mdash;l’affichage, l’ajout ou modification de données&mdash;même lorsqu’il n’y a aucune connexion réseau. Modifications sont stockées dans une base de données locale ; une fois que l’appareil est en ligne, ces modifications sont synchronisées avec le serveur principal à distance.

S’il s’agit de votre première utilisation d’applications Mobile Azure, vous devez effectuer tout d’abord le didacticiel [créer un application iOS]. Si vous n’utilisez pas le projet de serveur de démarrage rapide téléchargé, vous devez ajouter les packages d’extension de données access à votre projet. Pour plus d’informations sur les packages d’extension de serveur, voir [collaborer avec le Kit de développement pour applications Mobile Azure du serveur principal .NET](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Pour en savoir plus sur la fonctionnalité de synchronisation hors connexion, voir la rubrique [Synchronisation hors connexion de données dans les applications Mobile Azure].

## <a name="review-sync"></a>Passez en revue le code de synchronisation client

Le projet client que vous avez téléchargé le didacticiel [créer un application iOS] déjà contient code prenant en charge la synchronisation hors connexion à l’aide d’une base de données locale basée sur les données de base. Cette section est un résumé de ce qui est déjà inclus dans le code du didacticiel. Pour une vue d’ensemble conceptuelle de la fonction, voir [Synchronisation des données en mode hors connexion dans les applications Mobile Azure].

La fonctionnalité de synchronisation des données en mode hors connexion synchronisation des applications de Mobile Azure permet aux utilisateurs finaux d’interagir avec une base de données locale lorsque le réseau n’est pas accessible. Pour utiliser ces fonctionnalités dans votre application, vous initialisez le contexte de synchronisation de `MSClient` et faire référence à un magasin local. Puis faire référence à votre table via la `MSSyncTable` interface.

1. Dans **QSTodoService.m** (objectif-C) ou **ToDoTableViewController.swift** (Swift), notez le type du membre `syncTable` est `MSSyncTable`. Synchronisation hors connexion utilise cette interface de tableau de synchronisation à la place de `MSTable`. Lorsqu’une table de synchronisation est utilisée, toutes les opérations atteindre le magasin local et sont uniquement synchronisées avec le serveur distant principal avec opérations extraits et explicites.

    Pour obtenir une référence à une table de synchronisation, utilisez la méthode `syncTableWithName` sur `MSClient`. Pour supprimer la fonctionnalité de synchronisation hors connexion, utilisez `tableWithName` à la place.

2. Avant de pouvoir effectuer les opérations de table, le magasin local doit être initialisé. Voici le code approprié. 
    
    **Objectif C**:
    
    Dans la `QSTodoService.init` méthode :
    
    
            MSCoreDataStore *store = [[MSCoreDataStore alloc] initWithManagedObjectContext:context];
            self.client.syncContext = [[MSSyncContext alloc] initWithDelegate:nil dataSource:store callback:nil];
    
    
    **Swift**:
    
    Dans la `ToDoTableViewController.viewDidLoad` méthode :
    
    
            let client = MSClient(applicationURLString: "http:// ...") // URI of the Mobile App
            let managedObjectContext = (UIApplication.sharedApplication().delegate as! AppDelegate).managedObjectContext!
            self.store = MSCoreDataStore(managedObjectContext: managedObjectContext)
            client.syncContext = MSSyncContext(delegate: nil, dataSource: self.store, callback: nil)
    

    Cela crée un magasin local à l’aide de l’interface `MSCoreDataStore`, qui est fourni dans le Kit de développement des applications mobiles. Vous pouvez à la place un fournir un magasin local différent en mettant en œuvre la `MSSyncContextDataSource` protocole. 
    
    En outre, le premier paramètre de `MSSyncContext` est utilisé pour spécifier un gestionnaire de conflit. Étant donné que nous avons passé `nil`, nous allons le Gestionnaire de conflit par défaut, qui ne fonctionne pas sur n’importe quel conflit.
    
3. À présent, nous allons exécuter l’opération de synchronisation réel et obtenir des données depuis le serveur principal à distance.

    **Objectif C**:
    
    `syncData`tout d’abord pousse nouvelles modifications, puis appelle `pullData` pour obtenir des données à partir de la version de serveur distante. À son tour la méthode `pullData` Obtient les nouvelles données qui correspond à une requête :
    
    
            -(void)syncData:(QSCompletionBlock)completion
            {
                // push all changes in the sync context, then pull new data
                [self.client.syncContext pushWithCompletion:^(NSError *error) {
                    [self logErrorIfNotNil:error];
                    [self pullData:completion];
                }];
            }
    
            -(void)pullData:(QSCompletionBlock)completion
            {
                MSQuery *query = [self.syncTable query];
    
                // Pulls data from the remote server into the local table.
                // We're pulling all items and filtering in the view
                // query ID is used for incremental sync
                [self.syncTable pullWithQuery:query queryId:@"allTodoItems" completion:^(NSError *error) {
                    [self logErrorIfNotNil:error];
    
                    // Let the caller know that we have finished
                    if (completion != nil) {
                        dispatch_async(dispatch_get_main_queue(), completion);
                    }
                }];
            }
        
        
      **Swift**:
        
        
        func onRefresh(sender: UIRefreshControl!) {
            UIApplication.sharedApplication().networkActivityIndicatorVisible = true
            
            self.table!.pullWithQuery(self.table?.query(), queryId: "AllRecords") {
                (error) -> Void in
                
                UIApplication.sharedApplication().networkActivityIndicatorVisible = false
                
                if error != nil {
                    // A real application would handle various errors like network conditions,
                    // server conflicts, etc via the MSSyncContextDelegate
                    print("Error: \(error!.description)")
                    
                    // We will just discard our changes and keep the servers copy for simplicity
                    if let opErrors = error!.userInfo[MSErrorPushResultKey] as? Array<MSTableOperationError> {
                        for opError in opErrors {
                            print("Attempted operation to item \(opError.itemId)")
                            if (opError.operation == .Insert || opError.operation == .Delete) {
                                print("Insert/Delete, failed discarding changes")
                                opError.cancelOperationAndDiscardItemWithCompletion(nil)
                            } else {
                                print("Update failed, reverting to server's copy")
                                opError.cancelOperationAndUpdateItem(opError.serverItem!, completion: nil)
                            }
                        }
                    }
                }
                self.refreshControl?.endRefreshing()
            }
        } 
    
    
    Dans la version objectif-c, dans `syncData`, tout d’abord, nous appelons `pushWithCompletion` sur le contexte de synchronisation. Cette méthode est membre du `MSSyncContext` (plutôt que la synchronisation de tableau lui-même), car il pousser des modifications dans toutes les tables. Uniquement les enregistrements qui ont été modifiés d’une certaine manière localement (via les opérations CUD) seront envoyés au serveur. L’Assistant puis `pullData` est appelée, les appels `MSSyncTable.pullWithQuery` pour récupérer les données distantes et stocker dans la base de données locale.
    
    Dans la version rapide, il n’existe aucun appel à `pushWithCompletion`. C’est parce que l’opération push n’était pas strictement nécessaire. S’il existe toutes les modifications en attente dans le contexte de synchronisation pour la table qui effectue une opération de diffusion, extraire émet toujours un push tout d’abord. Toutefois, si vous avez plusieurs tables de synchronisation, il est mieux appeler explicitement push pour vous assurer que tout est cohérente dans l’ensemble des tables liées.
    
    Dans les versions à la fois l’objectif C et Swift, la méthode `pullWithQuery` vous permet de spécifier une requête pour filtrer les enregistrements que vous souhaitez récupérer. Dans cet exemple, la requête récupère simplement tous les enregistrements dans la base distante `TodoItem` table.
    
    Le second paramètre `pullWithQuery` est un ID de requête qui est utilisé pour *la synchronisation incrémentielle*. Synchronisation incrémentielle récupère uniquement les enregistrements modifiés depuis la dernière synchronisation, à l’aide de l’enregistrement `UpdatedAt` horodatage (appelé `updatedAt` dans le magasin local.) L’ID de requête doit être une chaîne descriptive unique pour chaque requête logique dans votre application. Pour refuser de recevoir de synchronisation incrémentielle, passez `nil` en tant que l’ID de requête. Notez que cela peut être potentiellement inefficace, dans la mesure où elle récupère tous les enregistrements de chaque opération d’extraction.

5. Le synchronise application objectif C quand nous modifier ou ajouter des données, un utilisateur effectue le mouvement d’actualisation, puis, sous la barre de lancement. L’application rapide synchronise lorsqu’un utilisateur effectue le mouvement d’actualisation et sur la barre de lancement. 

Étant donné que la synchronise application chaque fois que les données sont modifiées (objectif-C) ou à chaque démarrage de l’application (objectif C & Swift), l’application suppose que l’utilisateur est en ligne. Dans une autre section, nous met à jour l’application afin que les utilisateurs peuvent modifier même lorsqu’ils sont en mode hors connexion.

## <a name="review-core-data"></a>Passez en revue le modèle de données de base

Lorsque vous utilisez le magasin en mode hors connexion de données de base, vous devez définir des tables et des champs dans votre modèle de données. L’application exemple contient déjà un modèle de données avec le format approprié. Dans cette section, nous allons découvrir ces tables et comment ils sont utilisés.

- Ouvrez **QSDataModel.xcdatamodeld**. Il existe quatre tables définies--trois qui sont utilisées par le Kit de développement et une table pour le todo éléments eux-mêmes :     *MS_TableOperations : pour le suivi des éléments qui doivent être synchronisés avec le serveur    * MS_TableOperationErrors : pour le suivi des erreurs qui se produisent pendant la synchronisation hors connexion     *MS_TableConfig : pour le suivi de la dernière mise à jour de temps pour la dernière opération de synchronisation pour toutes les opérations d’extraction    * TodoItem : Permet de stocker les tâches. Les colonnes système **créédans**, **updatedAt**et **version** sont propriétés système facultatif.

>[AZURE.NOTE] Le Kit de développement Azure Mobile applications réserve noms de colonne qui est avec «**``**». Vous ne devez pas utiliser ce préfixe sur rien d’autre que des colonnes système, sinon vos noms de colonne seront modifiées lors de l’utilisation de la version de serveur distante.

- Lorsque vous utilisez la fonctionnalité de synchronisation hors connexion, vous devez définir les tables système, comme illustré ci-dessous.

    ### <a name="system-tables"></a>Tables système

    **MS_TableOperations**

    ![][defining-core-data-tableoperations-entity]

  	| Attribut  |    Type     |
  	|----------- |   ------    |
  	| ID         | Nombre entier 64  |
  	| ID d’élément     | Chaîne      |
  	| propriétés | Données binaires |
  	| table      | Chaîne      |
  	| tableKind  | Nombre entier 16  |

    <br>**MS_TableOperationErrors**

    ![][defining-core-data-tableoperationerrors-entity]

  	| Attribut  |    Type     |
  	|----------- |   ------    |
  	| ID         | Chaîne      |
  	| operationId | Nombre entier 64 |
  	| propriétés | Données binaires |
  	| tableKind  | Nombre entier 16  |

    <br>**MS_TableConfig**

    ![][defining-core-data-tableconfig-entity]

  	| Attribut  |    Type     |
  	|----------- |   ------    |
  	| ID         | Chaîne      |
  	| clé        | Chaîne      |
  	| type de clé    | Nombre entier 64  |
  	| table      | Chaîne      |
  	| valeur      | Chaîne      |

    ### <a name="data-table"></a>Table de données

    **TodoItem**

  	| Attribut    |  Type   | Remarque                                                   |
  	|-----------   |  ------ | -------------------------------------------------------|
  	| ID           | Chaîne, requises  | clé primaire Store à distance                            |
  	| Terminer     | Valeur booléenne | champ de l’élément TODO                                        |
  	| texte         | Chaîne  | champ de l’élément TODO                                        |
  	| créédans | Date    | (facultatif) mappe vers propriété système créédans         |
  	| updatedAt | Date    | (facultatif) mappe vers propriété système updatedAt         |
  	| Version   | Chaîne  | (facultatif) permettant de détecter les conflits, mappe vers la version |


## <a name="setup-sync"></a>Modifier le comportement de synchronisation de l’application

Dans cette section, vous allez modifier l’application afin qu’il n’est pas synchronisée sur Démarrer l’application, ou lors de l’insertion et mise à jour des éléments, mais uniquement lorsque le bouton Actualiser de mouvement est effectué.

**Objectif C**:

1. Dans **QSTodoListViewController.m**, modifiez la méthode **viewDidLoad** pour supprimer l’appel vers `[self refresh]` à la fin de la méthode. À présent, les données ne sont pas synchronisées avec le serveur au démarrage de l’application, mais plutôt sera le contenu du magasin local.

2. Dans **QSTodoService.m**, modifiez la définition de `addItem` afin qu’il n’est pas synchronisé après avoir inséré l’élément. Supprimer la `self syncData` bloquer et remplacer par les éléments suivants :

            if (completion != nil) {
                dispatch_async(dispatch_get_main_queue(), completion);
            }

3. Modifier la définition de `completeItem` qu’auparavant ; supprimer le bloc de `self syncData` et remplacer par les éléments suivants :

            if (completion != nil) {
                dispatch_async(dispatch_get_main_queue(), completion);
            }

**Swift**:

1. Dans `viewDidLoad` dans **ToDoTableViewController.swift**, commentez ces deux lignes, pour arrêter la synchronisation de démarrage de l’application. Au moment de la rédaction de cet article, l’application Swift Todo ne met pas à jour le service lorsqu’un utilisateur ajoute ou termine un élément, uniquement au démarrage de l’application.

        self.refreshControl?.beginRefreshing()
        self.onRefresh(self.refreshControl)


## <a name="test-app"></a>Tester l’application

Dans cette section, vous vous connectez à une URL non valide pour simuler un scénario en mode hors connexion. Lorsque vous ajoutez des éléments de données, ils seront stockés dans le magasin de données de base local, mais pas synchronisés avec le serveur principal mobile.

1. Modifiez l’URL de l’application Mobile dans **QSTodoService.m** en une URL non valide, puis réexécutez l’application :

    **Objectif-c** dans QSTodoService.m :
    
            self.client = [MSClient clientWithApplicationURLString:@"https://sitename.azurewebsites.net.fail"];
    
    **Swift** dans ToDoTableViewController.swift :

        let client = MSClient(applicationURLString: "https://sitename.azurewebsites.net.fail")

2. Ajouter des tâches. Fermez le simulator (ou forcer l’application) et redémarrez. Vérifiez que vos modifications ont été conservées.

3. Afficher le contenu de la table TodoItem distante :

    + Pour un serveur principal Node.js, accédez au [portail Azure](https://portal.azure.com/)et dans votre application Mobile et cliquez sur **Tables facile** > **TodoItem** pour afficher le contenu de la `TodoItem` table.
    + Pour un serveur principal .NET, afficher le contenu de tableau avec un outil SQL telles que SQL Server Management Studio, ou un client reste tel que Fiddler ou Postman.

    Vérifiez que les nouveaux éléments n’ont *pas* été synchronisées sur le serveur :

4. Changer l’URL pour la bonne sur **QSTodoService.m** , puis réexécutez l’application. Effectuer le mouvement d’actualisation en tirant vers le bas de la liste d’éléments. Vous verrez un compteur de progression.

5. Afficher les données TodoItem à nouveau. La TodoItems nouveaux et modifiés doit maintenant apparaître.

## <a name="summary"></a>Résumé

Pour prendre en charge la fonctionnalité de synchronisation hors connexion, nous avons utilisé le `MSSyncTable` interface et initialisé `MSClient.syncContext` avec un magasin local. Dans ce cas le magasin local a été une base de données en fonction des données de base.

Lorsque vous utilisez un magasin local de données de base, vous devez définir plusieurs tables avec les [Propriétés système correcte](#review-core-data).

Les opérations CRUD normales pour applications Mobile Azure fonctionnent comme si l’application est toujours connectée mais que toutes les opérations de se produisent par rapport au magasin local.

Quand nous voulons synchroniser le magasin local avec le serveur, nous avons utilisé le `MSSyncTable.pullWithQuery`méthode.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Synchronisation de données hors connexion dans les applications mobiles Azure]

* [Cloud garde : synchronisation hors connexion dans Azure Services mobiles] \(Remarque : la vidéo est activée Services mobiles, mais la synchronisation hors connexion fonctionne de la même façon dans les applications Mobile Azure\)

<!-- URLs. -->


[Créer un application iOS]: app-service-mobile-ios-get-started.md
[Synchronisation de données hors connexion dans les applications mobiles Azure]: app-service-mobile-offline-data-sync.md

[defining-core-data-tableoperationerrors-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperationerrors-entity.png
[defining-core-data-tableoperations-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperations-entity.png
[defining-core-data-tableconfig-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableconfig-entity.png
[defining-core-data-todoitem-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-todoitem-entity.png

[Couverture du cloud : Synchronisation hors connexion dans Azure Services mobiles]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: http://azure.microsoft.com/en-us/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/
