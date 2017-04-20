<properties
    pageTitle="Comment utiliser iOS Kit de développement pour applications Mobile Azure"
    description="Comment utiliser iOS Kit de développement pour applications Mobile Azure"
    services="app-service\mobile"
    documentationCenter="ios"
    authors="ysxu"
    manager="yochayk"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="yuaxu"/>

# <a name="how-to-use-ios-client-library-for-azure-mobile-apps"></a>Comment utiliser iOS bibliothèque du Client pour les applications Mobile Azure

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

Ce guide vous apprend à effectuer des scénarios courants à l’aide de dernière [Azure Mobile applications iOS SDK][1]. Si vous débutez dans les applications Mobile Azure, première complète [Azure Mobile applications Quick Start] pour créer un serveur principal, créez une table, puis télécharger un projet Xcode iOS prédéfinies. Dans ce guide, nous concentrer sur le côté client iOS SDK. Pour en savoir plus sur le Kit de développement côté serveur pour le serveur principal, voir la HOWTOs du Kit de développement logiciel serveur.

## <a name="reference-documentation"></a>Documentation de référence

La documentation de référence pour le client iOS SDK se trouve ici : [applications Mobile Azure iOS référence Client][2].

## <a name="supported-platforms"></a>Plateformes prises en charge

Le Kit de développement logiciel iOS prend en charge les projets objectif C, projets Swift 2.2 ou 2.3 Swift pour iOS versions 8.0 ou version ultérieures.

L’authentification « flux de serveur » utilise un mode d’affichage Web pour l’interface utilisateur présentée.  Si l’appareil n’est pas en mesure de présenter un WebView UI, puis une autre méthode d’authentification est requise qui est en dehors de l’étendue du produit.  
Ce SDK n’est donc pas approprié pour le type d’espion ou de la même manière des périphériques restreints.

##<a name="Setup"></a>Le programme d’installation et configuration requise

Ce guide suppose que vous avez créé un serveur principal avec une table. Ce guide part du principe que la table a le même schéma que les tables dans ces didacticiels. Ce guide suppose également que dans votre code, vous référencez `MicrosoftAzureMobile.framework` et importer `MicrosoftAzureMobile/MicrosoftAzureMobile.h`.

##<a name="create-client"></a>Comment : créer des clients

Pour accéder à un serveur principal applications Mobile Azure dans votre projet, créez un `MSClient`. Remplacer `AppUrl` avec l’URL de l’application. Vous pouvez être amené à `gatewayURLString` et `applicationKey` vide. Si vous configurez une passerelle pour l’authentification, remplir `gatewayURLString` avec l’URL de la passerelle.

**Objectif C**:

```
MSClient *client = [MSClient clientWithApplicationURLString:@"AppUrl"];
```

**Swift**:

```
let client = MSClient(applicationURLString: "AppUrl")
```


##<a name="table-reference"></a>Comment : créer la référence de Table

Access ou mettre à jour les données, créer une référence à la table principale. Remplacer `TodoItem` avec le nom du tableau

**Objectif C**:

```
MSTable *table = [client tableWithName:@"TodoItem"];
```

**Swift**:

```
let table = client.tableWithName("TodoItem")
```


##<a name="querying"></a>Comment : interroger des données

Pour créer une requête de base de données, de la requête la `MSTable` objet. La requête suivante obtient tous les éléments dans `TodoItem` et enregistre le texte de chaque élément.

**Objectif C**:

```
[table readWithCompletion:^(MSQueryResult *result, NSError *error) {
        if(error) { // error is nil if no error occured
                NSLog(@"ERROR %@", error);
        } else {
                for(NSDictionary *item in result.items) { // items is NSArray of records that match query
                        NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
                }
        }
}];
```

**Swift**:

```
table.readWithCompletion { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let items = result?.items {
        for item in items {
            print("Todo Item: ", item["text"])
        }
    }
}
```

##<a name="filtering"></a>Comment : filtrer les données renvoyées

Pour filtrer les résultats, il existe de nombreuses options disponibles.

Pour filtrer à l’aide d’un prédicat, utilisez un `NSPredicate` et `readWithPredicate`. Les filtres suivants renvoyé des données pour rechercher uniquement incomplètes tâches.

**Objectif C**:

```
// Create a predicate that finds items where complete is false
NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];
// Query the TodoItem table
[table readWithPredicate:predicate completion:^(MSQueryResult *result, NSError *error) {
        if(error) {
                NSLog(@"ERROR %@", error);
        } else {
                for(NSDictionary *item in result.items) {
                        NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
                }
        }
}];
```

**Swift**:

```
// Create a predicate that finds items where complete is false
let predicate =  NSPredicate(format: "complete == NO")
// Query the TodoItem table
table.readWithPredicate(predicate) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let items = result?.items {
        for item in items {
            print("Todo Item: ", item["text"])
        }
    }
}
```

##<a name="query-object"></a>Comment : utiliser Query

Pour exécuter une requête complexe (y compris le tri et la pagination), créez un `MSQuery` objet, directement ou en utilisant un prédicat :

**Objectif C**:

```
MSQuery *query = [table query];
MSQuery *query = [table queryWithPredicate: [NSPredicate predicateWithFormat:@"complete == NO"]];
```

**Swift**:

```
let query = table.query()
let query = table.queryWithPredicate(NSPredicate(format: "complete == NO"))
```

`MSQuery`vous permet de contrôler plusieurs comportements de requête.

* Spécifier l’ordre des résultats
* Limiter les champs à renvoyer
* Limiter le nombre d’enregistrements pour renvoyer
* Spécifier le nombre total de réponse
* Spécifier les paramètres de chaîne de requête personnalisée de demande
* Appliquer des fonctions supplémentaires

Exécuter une `MSQuery` requête en appelant `readWithCompletion` sur l’objet.

## <a name="sorting"></a>Comment : trier des données avec Query

Pour trier les résultats, nous allons étudier un exemple. Pour trier par « texte » du champ dans l’ordre croissant, puis par ordre décroissant sur « totale », appeler `MSQuery` comme suit :

**Objectif C**:

```
[query orderByAscending:@"text"];
[query orderByDescending:@"complete"];
[query readWithCompletion:^(MSQueryResult *result, NSError *error) {
        if(error) {
                NSLog(@"ERROR %@", error);
        } else {
                for(NSDictionary *item in result.items) {
                        NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
                }
        }
}];
```

**Swift**:

```
query.orderByAscending("text")
query.orderByDescending("complete")
query.readWithCompletion { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let items = result?.items {
        for item in items {
            print("Todo Item: ", item["text"])
        }
    }
}
```


## <a name="selecting"></a><a name="parameters"></a>Comment : limiter les champs, développez les paramètres de chaîne de requête avec Query

Pour limiter les champs à retourner dans une requête, spécifiez les noms des champs dans la propriété **selectFields** . Cet exemple renvoie uniquement le texte et les champs terminées :

**Objectif C**:

```
query.selectFields = @[@"text", @"complete"];
```

**Swift**:

```
query.selectFields = ["text", "complete"]
```

Pour inclure les paramètres de chaîne de requête supplémentaires dans la demande de serveur (par exemple, car un script côté serveur personnalisé utilise les), remplir `query.parameters` comme suit :

**Objectif C**:

```
query.parameters = @{
    @"myKey1" : @"value1",
    @"myKey2" : @"value2",
};
```

**Swift**:

```
query.parameters = ["myKey1": "value1", "myKey2": "value2"]
```

## <a name="paging"></a>Comment : configurer la taille de la Page

Avec les applications Azure Mobile, la taille de la page contrôle le nombre d’enregistrements extrait à la fois des tables principal. Un appel à `pull` puis de lots des données, en fonction de ce format de page, jusqu'à ce qu’il y a aucun enregistrement plus pour extraire des données.

Il est possible de configurer une taille de page à l’aide de **MSPullSettings** comme illustré ci-dessous. La taille de la page par défaut est achevée à 50, et l’exemple ci-dessous transforme en 3.

Vous pouvez configurer une taille de page différents pour des raisons de performances. Si vous avez un grand nombre d’enregistrements de données de petite taille, une taille de page élevée réduit le nombre d’aller-retour de serveur. 

Ce paramètre détermine la taille de page sur le côté client. Si le client vous invite à indiquer une taille de page plus grande que le système principal applications Mobile prend en charge, la taille de page est limitée au maximum que le système principal est configuré pour prendre en charge. 

Ce paramètre est également le _nombre_ d’enregistrements de données, pas la _taille en octets_.

Si vous augmentez la taille de la page client, [vous devez également augmenter la taille de la page sur le serveur](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#_how-to-adjust-the-table-paging-size).

**Objectif C**:

```
  MSPullSettings *pullSettings = [[MSPullSettings alloc] initWithPageSize:3];
  [table  pullWithQuery:query queryId:@nil settings:pullSettings
                        completion:^(NSError * _Nullable error) {
                               if(error) {
                    NSLog(@"ERROR %@", error);
                } 
                           }];
```


**Swift**:

```
let pullSettings = MSPullSettings(pageSize: 3)
table.pullWithQuery(query, queryId:nil, settings: pullSettings) { (error) in
    if let err = error {
        print("ERROR ", err)
    } 
}
```

##<a name="inserting"></a>Comment : insérer des données

Pour insérer une nouvelle ligne au tableau, créez un `NSDictionary` et d’appeler `table insert`. Si [Schéma dynamique] est activée, le système principal mobile Azure Application Service génère automatiquement les nouvelles colonnes en fonction de la `NSDictionary`.

Si `id` n’est pas fourni, le système principal génère automatiquement un nouvel ID unique. Indiquez vos propres `id` pour utiliser la messagerie adresses, des noms d’utilisateurs, ou des valeurs de votre propre personnalisé en tant qu’identifiant. Fournir vos propres ID peut alléger les jointures et la base de données orientée métier logique.

La `result` contient le nouvel élément a été inséré. Selon la logique du serveur, elle peut avoir des données supplémentaires ou modifiées par rapport à ce qui a été transmis au serveur.

**Objectif C**:

```
NSDictionary *newItem = @{@"id": @"custom-id", @"text": @"my new item", @"complete" : @NO};
[table insert:newItem completion:^(NSDictionary *result, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
    }
}];
```

**Swift**:

```
let newItem = ["id": "custom-id", "text": "my new item", "complete": false]
table.insert(newItem) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let item = result {
        print("Todo Item: ", item["text"])
    }
}
```

##<a name="modifying"></a>Comment : modifier des données

Pour mettre à jour une ligne existante, modifier un élément et un appel `update`:

**Objectif C**:

```
NSMutableDictionary *newItem = [oldItem mutableCopy]; // oldItem is NSDictionary
[newItem setValue:@"Updated text" forKey:@"text"];
[table update:newItem completion:^(NSDictionary *result, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
    }
}];
```

**Swift**:

```
if let newItem = oldItem.mutableCopy() as? NSMutableDictionary {
    newItem["text"] = "Updated text"
    table2.update(newItem as [NSObject: AnyObject], completion: { (result, error) -> Void in
        if let err = error {
            print("ERROR ", err)
        } else if let item = result {
            print("Todo Item: ", item["text"])
        }
    })
}
```

Vous pouvez également fournir l’ID de ligne et le champ mis à jour :

**Objectif C**:

```
[table update:@{@"id":@"custom-id", @"text":"my EDITED item"} completion:^(NSDictionary *result, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
    }
}];
```

**Swift**:

```
table.update(["id": "custom-id", "text": "my EDITED item"]) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let item = result {
        print("Todo Item: ", item["text"])
    }
}
```

Au minimum, le `id` attribut doit être défini lorsque vous effectuez des mises à jour.

##<a name="deleting"></a>Comment : supprimer des données

Pour supprimer un élément, appeler `delete` avec l’élément :

**Objectif C**:

```
[table delete:item completion:^(id itemId, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item ID: %@", itemId);
    }
}];
```

**Swift**:

```
table.delete(newItem as [NSObject: AnyObject]) { (itemId, error) in
    if let err = error {
        print("ERROR ", err)
    } else {
        print("Todo Item ID: ", itemId)
    }
}
```

Vous pouvez également supprimer en fournissant un ID de ligne :

**Objectif C**:

```
[table deleteWithId:@"37BBF396-11F0-4B39-85C8-B319C729AF6D" completion:^(id itemId, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item ID: %@", itemId);
    }
}];
```

**Swift**:

```
table.deleteWithId("37BBF396-11F0-4B39-85C8-B319C729AF6D") { (itemId, error) in
    if let err = error {
        print("ERROR ", err)
    } else {
        print("Todo Item ID: ", itemId)
    }
}
```

Au minimum, le `id` attribut doit être défini lorsque supprime de fabrication.

##<a name="customapi"></a>Comment : appeler API personnalisé

Avec une API personnalisée, vous pouvez exposer toutes les fonctionnalités serveur principal. Établir une correspondance entre une opération de tableau ne doit pas nécessairement. Non seulement avoir plus de contrôle sur la messagerie, vous pouvez même en lecture/ensemble en-têtes et modifier le format de corps de réponse. Pour apprendre à créer une API personnalisée sur le serveur principal, lisez [API personnalisé](app-service-mobile-node-backend-how-to-use-server-sdk.md#work-easy-apis)

Pour appeler une API personnalisée, appelez `MSClient.invokeAPI`. La demande et réponse contenu sont considérées comme JSON. Utiliser d’autres types de médias, [Utilisez l’autre surcharge de `invokeAPI` ] [ 5].  Pour rendre un `GET` demander au lieu d’un `POST` demander, le paramètre du jeu de `HTTPMethod` à `"GET"` et paramètre `body` à `nil` (dans la mesure où les requêtes GET n’ont pas corps de message.) Si votre API personnalisé prend en charge les autres verbes HTTP, modifier `HTTPMethod` correctement.

**Objectif C**:

```
[self.client invokeAPI:@"sendEmail"
                  body:@{ @"contents": @"Hello world!" }
            HTTPMethod:@"POST"
            parameters:@{ @"to": @"bill@contoso.com", @"subject" : @"Hi!" }
               headers:nil
            completion: ^(NSData *result, NSHTTPURLResponse *response, NSError *error) {
                if(error) {
                    NSLog(@"ERROR %@", error);
                } else {
                    // Do something with result
                }
            }];
```

**Swift**:

```
client.invokeAPI("sendEmail",
            body: [ "contents": "Hello World" ],
            HTTPMethod: "POST",
            parameters: [ "to": "bill@contoso.com", "subject" : "Hi!" ],
            headers: nil)
            {
                (result, response, error) -> Void in
                if let err = error {
                    print("ERROR ", err)
                } else if let res = result {
                          // Do something with result
                }
        }
```

##<a name="templates"></a>Comment : modèles push de Registre pour envoyer des notifications de disponibilité sur plusieurs plateformes

Pour enregistrer des modèles, passer des modèles avec votre méthode **client.push registerDeviceToken** dans votre application client.

**Objectif C**:

```
[client.push registerDeviceToken:deviceToken template:iOSTemplate completion:^(NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    }
}];
```

**Swift**:

```
    client.push?.registerDeviceToken(NSData(), template: iOSTemplate, completion: { (error) in
        if let err = error {
            print("ERROR ", err)
        }
    })
```

Vos modèles sont de type NSDictionary et peuvent contenir plusieurs modèles dans le format suivant :

**Objectif C**:

```
NSDictionary *iOSTemplate = @{ @"templateName": @{ @"body": @{ @"aps": @{ @"alert": @"$(message)" } } } };
```

**Swift**:

```
let iOSTemplate = ["templateName": ["body": ["aps": ["alert": "$(message)"]]]]
```

Toutes les balises sont supprimés de la demande de sécurité.  Pour ajouter des indicateurs aux installations ou des modèles dans les installations, voir [collaborer avec le Kit de développement pour applications Mobile Azure du serveur principal .NET][4].  Pour envoyer une notification à l’aide de ces modèles enregistrés, travailler avec [Notification Hubs API][3].

##<a name="errors"></a>Comment : gérer les erreurs

Lorsque vous appelez un serveur Azure Application Service mobile principal, le bloc de saisie semi-automatique contient un `NSError` paramètre. Lorsqu’une erreur se produit, ce paramètre est non nulle. Dans votre code, vérifiez ce paramètre et gérer l’erreur selon vos besoins, comme illustré dans extraits de code précédents.

Le fichier [`<WindowsAzureMobileServices/MSError.h>`] [6] définit les constantes `MSErrorResponseKey`, `MSErrorRequestKey`, et `MSErrorServerItemKey`. Pour obtenir des données supplémentaires relatives à l’erreur :

**Objectif C**:

```
NSDictionary *serverItem = [error.userInfo objectForKey:MSErrorServerItemKey];
```

**Swift**:

```
let serverItem = error.userInfo[MSErrorServerItemKey]
```

En outre, le fichier définit des constantes pour chaque code d’erreur :

**Objectif C**:

```
if (error.code == MSErrorPreconditionFailed) {
```

**Swift**:

```
if (error.code == MSErrorPreconditionFailed) {
```

## <a name="adal"></a>Comment : authentifier les utilisateurs avec la bibliothèque d’authentification Active Directory

Vous pouvez utiliser la bibliothèque terme (Active Directory authentification ADAL) pour vous connecter les utilisateurs à votre application à l’aide d’Azure Active Directory. Authentification du flux client à l’aide d’un fournisseur d’identité SDK est préférable d’utiliser la `loginWithProvider:completion:` méthode.  Authentification du flux client fournit un aspect UX plus natif et permet de personnaliser.

1. Configurez la principale application mobile pour se connecter AAD en procédant [Comment faire pour configurer le Service d’application pour la connexion Active Directory] [ 7] didacticiel. Veillez à terminer l’étape facultative d’enregistrement d’une application cliente native. Pour iOS, nous vous recommandons la redirection URI est de la forme `<app-scheme>://<bundle-id>`. Pour plus d’informations, voir le [démarrage rapide iOS ADAL][8].

2. Installez le terme ADAL à l’aide de Cocoapods. Modifier votre Podfile pour inclure la définition suivante, en remplaçant **Votre projet** par le nom de votre projet Xcode :

        source 'https://github.com/CocoaPods/Specs.git'
        link_with ['YOUR-PROJECT']
        xcodeproj 'YOUR-PROJECT'

   et du bloc :

        pod 'ADALiOS'

3. En utilisant le Terminal, exécuter `pod install` à partir du répertoire contenant votre projet et ouvrez l’espace de travail Xcode généré (et non sur le projet).

4. Ajoutez le code suivant à votre application, en fonction de la langue que vous utilisez. Dans chacune, vérifiez ces remplacements :

    * Remplacez **Insertion-autorité-ici** par le nom du client dans lequel vous sa mise en service votre application. Le format doit être https://login.windows.net/contoso.onmicrosoft.com. Cette valeur peut être copiée à partir de l’onglet de domaine dans votre Azure Active Directory [portail classique Azure].
    * Remplacez **Insertion-ressource-ID-ici** l’ID client pour la principale de l’application mobile. Vous pouvez obtenir l’ID de client à partir de l’onglet **Avancé** , sous **Paramètres Azure Active Directory** dans le portail.
    * Remplacez **Insertion-CLIENT-ID-ici** par l’ID de client que vous avez copiée à partir de l’application cliente native.
    * Remplacez **Insertion-redirection-URI-ici** par le point de terminaison de votre site _/.auth/login/done_ , à l’aide du schéma HTTPS. Cette valeur doit être similaire à _https://contoso.azurewebsites.net/.auth/login/done_.

**Objectif C**:

    #import <ADALiOS/ADAuthenticationContext.h>
    #import <ADALiOS/ADAuthenticationSettings.h>
    // ...
    - (void) authenticate:(UIViewController*) parent
               completion:(void (^) (MSUser*, NSError*))completionBlock;
    {
        NSString *authority = @"INSERT-AUTHORITY-HERE";
        NSString *resourceId = @"INSERT-RESOURCE-ID-HERE";
        NSString *clientId = @"INSERT-CLIENT-ID-HERE";
        NSURL *redirectUri = [[NSURL alloc]initWithString:@"INSERT-REDIRECT-URI-HERE"];
        ADAuthenticationError *error;
        ADAuthenticationContext *authContext = [ADAuthenticationContext authenticationContextWithAuthority:authority error:&error];
        authContext.parentController = parent;
        [ADAuthenticationSettings sharedInstance].enableFullScreen = YES;
        [authContext acquireTokenWithResource:resourceId
                                     clientId:clientId
                                  redirectUri:redirectUri
                              completionBlock:^(ADAuthenticationResult *result) {
                                  if (result.status != AD_SUCCEEDED)
                                  {
                                      completionBlock(nil, result.error);;
                                  }
                                  else
                                  {
                                      NSDictionary *payload = @{
                                                                @"access_token" : result.tokenCacheStoreItem.accessToken
                                                                };
                                      [client loginWithProvider:@"aad" token:payload completion:completionBlock];
                                  }
                              }];
    }


**Swift**:

    // add the following imports to your bridging header:
    //      #import <ADALiOS/ADAuthenticationContext.h>
    //      #import <ADALiOS/ADAuthenticationSettings.h>

    func authenticate(parent: UIViewController, completion: (MSUser?, NSError?) -> Void) {
        let authority = "INSERT-AUTHORITY-HERE"
        let resourceId = "INSERT-RESOURCE-ID-HERE"
        let clientId = "INSERT-CLIENT-ID-HERE"
        let redirectUri = NSURL(string: "INSERT-REDIRECT-URI-HERE")
        var error: AutoreleasingUnsafeMutablePointer<ADAuthenticationError?> = nil
        let authContext = ADAuthenticationContext(authority: authority, error: error)
        authContext.parentController = parent
        ADAuthenticationSettings.sharedInstance().enableFullScreen = true
        authContext.acquireTokenWithResource(resourceId, clientId: clientId, redirectUri: redirectUri) { (result) in
                if result.status != AD_SUCCEEDED {
                    completion(nil, result.error)
                }
                else {
                    let payload: [String: String] = ["access_token": result.tokenCacheStoreItem.accessToken]
                    client.loginWithProvider("aad", token: payload, completion: completion)
                }
            }
    }

## <a name="facebook-sdk"></a>Comment : authentification des utilisateurs avec Facebook SDK pour iOS

Vous pouvez utiliser le SDK Facebook pour iOS pour vous connecter les utilisateurs à votre application à l’aide de Facebook.  À l’aide d’une authentification de flux client est préférable d’utiliser la `loginWithProvider:completion:` méthode.  L’authentification du flux client fournit un aspect UX plus natif et permet de personnaliser.

1. Configurez la principale application mobile pour connexion Facebook en procédant [Comment faire pour configurer le Service d’application pour la connexion Facebook] [ 9] didacticiel.

2. Installer le SDK Facebook pour iOS en suivant le [Kit de développement logiciel Facebook pour iOS - mise en route] [ 10] documentation. Au lieu de créer une application, vous pouvez ajouter la plateforme iOS à votre enregistrement existant. 

3. Documentation de Facebook inclut du code objectif-c dans le délégué de l’application. Si vous utilisez **Swift**, vous pouvez utiliser les conversions suivantes pour AppDelegate.swift :
  
        // Add the following import to your bridging header:
        //      #import <FBSDKCoreKit/FBSDKCoreKit.h>
        
        func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject : AnyObject]?) -> Bool {
            FBSDKApplicationDelegate.sharedInstance().application(application, didFinishLaunchingWithOptions: launchOptions)
            // Add any custom logic here.
            return true
        }

        func application(application: UIApplication, openURL url: NSURL, sourceApplication: String?, annotation: AnyObject?) -> Bool {
            let handled = FBSDKApplicationDelegate.sharedInstance().application(application, openURL: url, sourceApplication: sourceApplication, annotation: annotation)
            // Add any custom logic here.
            return handled
        }

4. Outre l’ajout de `FBSDKCoreKit.framework` à votre projet, aussi ajouter une référence à `FBSDKLoginKit.framework` de la même façon. 

4. Ajoutez le code suivant à votre application, en fonction de la langue que vous utilisez. 

**Objectif C**:

    #import <FBSDKLoginKit/FBSDKLoginKit.h>
    #import <FBSDKCoreKit/FBSDKAccessToken.h>
    // ...
    - (void) authenticate:(UIViewController*) parent
               completion:(void (^) (MSUser*, NSError*)) completionBlock;
    {       
        FBSDKLoginManager *loginManager = [[FBSDKLoginManager alloc] init];
        [loginManager
         logInWithReadPermissions: @[@"public_profile"]
         fromViewController:parent
         handler:^(FBSDKLoginManagerLoginResult *result, NSError *error) {
             if (error) {
                 completionBlock(nil, error);
             } else if (result.isCancelled) {
                 completionBlock(nil, error);
             } else {
                 NSDictionary *payload = @{
                                           @"access_token":result.token.tokenString
                                           };
                 [client loginWithProvider:@"facebook" token:payload completion:completionBlock];
             }
         }];
    }

**Swift**:

    // Add the following imports to your bridging header:
    //      #import <FBSDKLoginKit/FBSDKLoginKit.h>
    //      #import <FBSDKCoreKit/FBSDKAccessToken.h>
    
    func authenticate(parent: UIViewController, completion: (MSUser?, NSError?) -> Void) {
        let loginManager = FBSDKLoginManager()
        loginManager.logInWithReadPermissions(["public_profile"], fromViewController: parent) { (result, error) in
            if (error != nil) {
                completion(nil, error)
            }
            else if result.isCancelled {
                completion(nil, error)
            }
            else {
                let payload: [String: String] = ["access_token": result.token.tokenString]
                client.loginWithProvider("facebook", token: payload, completion: completion)
            }
        }
    }

## <a name="twitter-fabric"></a>Comment : authentification des utilisateurs avec tissu Twitter pour iOS

Vous pouvez utiliser tissu pour iOS pour vous connecter les utilisateurs à votre application à l’aide de Twitter. Authentification du flux client est préférable d’utiliser la `loginWithProvider:completion:` méthode, tel qu’il fournit un aspect UX plus natif et permet de personnaliser.

1. Configurer la principale application mobile pour se connecter Twitter en suivant le didacticiel [Comment faire pour configurer le Service d’application pour la connexion Twitter](app-service-mobile-how-to-configure-twitter-authentication.md) .

2. Ajouter tissu à votre projet en [tissu pour iOS - mise en route] de la documentation et en configurant TwitterKit.

    > [AZURE.NOTE] Par défaut, TISSU crée une application Twitter à votre place. Vous pouvez éviter la création d’une application en enregistrant le code Secret consommateur vous avez créé précédemment à l’aide d’extraits de code suivants et clé de consommateur.  Par ailleurs, vous pouvez remplacer les valeurs de clé de consommateur et le code Secret consommateur que vous fournissez au Service d’application avec les valeurs que vous voyez dans le [Tableau de bord TISSU]. Si vous choisissez cette option, veillez à définir l’URL de rappel à une valeur d’espace réservé, tel que `https://<yoursitename>.azurewebsites.net/.auth/login/twitter/callback`.

    Si vous choisissez d’utiliser les secrets que vous avez créée, ajoutez le code suivant à votre délégué de l’application :
    
    **Objectif C**:

        #import <Fabric/Fabric.h>
        #import <TwitterKit/TwitterKit.h>
        // ...
        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
        {
            [[Twitter sharedInstance] startWithConsumerKey:@"your_key" consumerSecret:@"your_secret"];
            [Fabric with:@[[Twitter class]]];
            // Add any custom logic here.
            return YES;
        }
        
    **Swift**:
    
        import Fabric
        import TwitterKit
        // ...
        func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject : AnyObject]?) -> Bool {
            Twitter.sharedInstance().startWithConsumerKey("your_key", consumerSecret: "your_secret")
            Fabric.with([Twitter.self])
            // Add any custom logic here.
            return true
        }
    
3. Ajoutez le code suivant à votre application, en fonction de la langue que vous utilisez. 

**Objectif C**:

    #import <TwitterKit/TwitterKit.h>
    // ...
    - (void)authenticate:(UIViewController*)parent completion:(void (^) (MSUser*, NSError*))completionBlock
    {
        [[Twitter sharedInstance] logInWithCompletion:^(TWTRSession *session, NSError *error) {
            if (session) {
                NSDictionary *payload = @{
                                            @"access_token":session.authToken,
                                            @"access_token_secret":session.authTokenSecret
                                        };
                [client loginWithProvider:@"twitter" token:payload completion:completionBlock];
            } else {
                completionBlock(nil, error);
            }
        }];
    }

**Swift**:

    import TwitterKit
    // ...
    func authenticate(parent: UIViewController, completion: (MSUser?, NSError?) -> Void) {
        let client = self.table!.client
        Twitter.sharedInstance().logInWithCompletion { session, error in
            if (session != nil) {
                let payload: [String: String] = ["access_token": session!.authToken, "access_token_secret": session!.authTokenSecret]
                client.loginWithProvider("twitter", token: payload, completion: completion)
            } else {
                completion(nil, error)
            }
        }
    }

## <a name="google-sdk"></a>Comment : authentification des utilisateurs avec Google connexion SDK pour iOS

Vous pouvez utiliser le Google connexion SDK pour iOS pour vous connecter les utilisateurs à votre application à l’aide d’un compte Google.  Google récemment annoncé que les modifications apportées à leurs stratégies de sécurité OAuth.  Ces modifications stratégie exige l’utilisation de Google SDK à l’avenir.

1. Configurer la principale application mobile pour se connecter Google en suivant le didacticiel [Comment faire pour configurer le Service d’application pour la connexion de Google](app-service-mobile-how-to-configure-google-authentication.md) .

2. Installer le SDK Google pour iOS en suivant la documentation [connexion Google pour iOS - démarrer l’intégration](https://developers.google.com/identity/sign-in/ios/start-integrating) . Vous pouvez ignorer la section « S’authentifier avec un serveur principal ».

3. Ajoutez les éléments suivants à votre délégué `signIn:didSignInForUser:withError:` méthode, en fonction de la langue que vous utilisez.

**Objectif C**:

        NSDictionary *payload = @{
                                  @"id_token":user.authentication.idToken,
                                  @"authorization_code":user.serverAuthCode
                                  };
        
        [client loginWithProvider:@"google" token:payload completion:^(MSUser *user, NSError *error) {
            // ...
        }];

**Swift**:

        let payload: [String: String] = ["id_token": user.authentication.idToken, "authorization_code": user.serverAuthCode]
        client.loginWithProvider("google", token: payload) { (user, error) in
            // ...
        }

4. Vérifiez que vous ajoutez également ce qui suit à `application:didFinishLaunchingWithOptions:` dans votre délégué de l’application, remplaçant « SERVER_CLIENT_ID » par le même ID que vous avez utilisé pour configurer le Service d’application à l’étape 1.

**Objectif C**:

        [GIDSignIn sharedInstance].serverClientID = @"SERVER_CLIENT_ID";
 
 **Swift**:
 
        GIDSignIn.sharedInstance().serverClientID = "SERVER_CLIENT_ID"

 
 5. Ajoutez le code suivant à votre application dans un UIViewController qui mettent en œuvre, la `GIDSignInUIDelegate` protocole, en fonction de la langue que vous utilisez.  Vous êtes déconnecté avant d’être connecté à nouveau, et bien que vous n’avez pas besoin d’entrer à nouveau vos informations d’identification, vous voyez une boîte de dialogue consentement.  Appelez cette méthode uniquement lorsque le jeton de session a expiré.
 
 **Objectif C**:

        #import <Google/SignIn.h>
        // ...
        - (void)authenticate
        {
                [GIDSignIn sharedInstance].uiDelegate = self;
                [[GIDSignIn sharedInstance] signOut];
                [[GIDSignIn sharedInstance] signIn];
        }
 
 **Swift**:
    
        // ...
        func authenticate() {
            GIDSignIn.sharedInstance().uiDelegate = self
            GIDSignIn.sharedInstance().signOut()
            GIDSignIn.sharedInstance().signIn()
        }
        
<!-- Anchors. -->

[What is Mobile Services]: #what-is
[Concepts]: #concepts
[Setup and Prerequisites]: #Setup
[How to: Create the Mobile Services client]: #create-client
[How to: Create a table reference]: #table-reference
[How to: Query data from a mobile service]: #querying
[Filter returned data]: #filtering
[Sort returned data]: #sorting
[Return data in pages]: #paging
[Select specific columns]: #selecting
[How to: Bind data to the user interface]: #binding
[How to: Insert data into a mobile service]: #inserting
[How to: Modify data in a mobile service]: #modifying
[How to: Authenticate users]: #authentication
[Cache authentication tokens]: #caching-tokens
[How to: Upload images and large files]: #blobs
[How to: Handle errors]: #errors
[How to: Design unit tests]: #unit-testing
[How to: Customize the client]: #customizing
[Customize request headers]: #custom-headers
[Customize data type serialization]: #custom-serialization
[Next Steps]: #next-steps
[How to: Use MSQuery]: #query-object

<!-- Images. -->

<!-- URLs. -->
[Guide de démarrage rapide d’applications mobiles Azure]: app-service-mobile-ios-get-started.md

[Add Mobile Services to Existing App]: /develop/mobile/tutorials/get-started-data
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Validate and modify data in Mobile Services by using server scripts]: /develop/mobile/tutorials/validate-modify-and-augment-data-ios
[Mobile Services SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[iOS SDK]: https://developer.apple.com/xcode

[Handling Expired Tokens]: http://go.microsoft.com/fwlink/p/?LinkId=301955
[Live Connect SDK]: http://go.microsoft.com/fwlink/p/?LinkId=301960
[Permissions]: http://msdn.microsoft.com/library/windowsazure/jj193161.aspx
[Service-side Authorization]: mobile-services-javascript-backend-service-side-authorization.md
[Use scripts to authorize users]: /develop/mobile/tutorials/authorize-users-in-scripts-ios
[Schéma dynamiques]: http://go.microsoft.com/fwlink/p/?LinkId=296271
[How to: access custom parameters]: /develop/mobile/how-to-guides/work-with-server-scripts#access-headers
[Create a table]: http://msdn.microsoft.com/library/windowsazure/jj193162.aspx
[NSDictionary object]: http://go.microsoft.com/fwlink/p/?LinkId=301965
[ASCII control codes C0 and C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[CLI to manage Mobile Services tables]: ../virtual-machines-command-line-tools.md#Mobile_Tables
[Conflict-Handler]: mobile-services-ios-handling-conflicts-offline-data.md#add-conflict-handling

[Tableau de bord TISSU]: https://www.fabric.io/home
[TISSU pour iOS - mise en route]: https://docs.fabric.io/ios/fabric/getting-started.html
[1]: https://github.com/Azure/azure-mobile-apps-ios-client/blob/master/README.md#ios-client-sdk
[2]: http://azure.github.io/azure-mobile-apps-ios-client/
[3]: https://msdn.microsoft.com/library/azure/dn495101.aspx
[4]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#tags
[5]: http://azure.github.io/azure-mobile-services/iOS/v3/Classes/MSClient.html#//api/name/invokeAPI:data:HTTPMethod:parameters:headers:completion:
[6]: https://github.com/Azure/azure-mobile-services/blob/master/sdk/iOS/src/MSError.h
[7]: app-service-mobile-how-to-configure-active-directory-authentication.md
[8]: ../active-directory/active-directory-devquickstarts-ios.md#em1-determine-what-your-redirect-uri-will-be-for-iosem
[9]: app-service-mobile-how-to-configure-facebook-authentication.md
[10]: https://developers.facebook.com/docs/ios/getting-started
