<properties
    pageTitle="Azure AD iOS prise en main | Microsoft Azure"
    description="Découvrez comment créer une application iOS qui s’intègre à Azure AD pour se connecter et appelle Azure AD protégé API utilisant OAuth."
    services="active-directory"
    documentationCenter="ios"
    authors="brandwe"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="brandwe"/>

# <a name="integrate-azure-ad-into-an-ios-app"></a>Intégrer Azure AD dans un application iOS

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)] 

Azure AD offre la bibliothèque de l’authentification Active Directory, ou terme ADAL, pour les clients iOS qui doivent accéder aux ressources protégées.  But du terme ADAL dans vie consiste à faciliter pour votre application obtenir des jetons d’accès.  Pour montrer simplement combien il est facile, ici nous allons créer une application de la liste des tâches objectif C qui :

-   Obtient jetons pour l’appel de l’API Azure AD Graph à l’aide du [protocole d’authentification 2.0 jeton](https://msdn.microsoft.com/library/azure/dn645545.aspx)d’accès.
-   Recherche dans un répertoire pour les utilisateurs avec un alias de donnée.

Pour créer l’application de travail complète, vous devez :

2. Enregistrer votre application avec Azure AD.
3. Installer et configurer le terme ADAL.
5. Terme ADAL permet d’obtenir des jetons à partir d’Azure AD.

Pour démarrer, [Téléchargez la structure de l’application](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/skeleton.zip) ou [Télécharger l’exemple terminé](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip).  Vous devez également un client Azure AD dans lequel vous pouvez créer des utilisateurs et enregistrer une application.  Si vous n’avez pas déjà un client, [Découvrez comment obtenir un](active-directory-howto-tenant.md).

> [AZURE.TIP] Essayez de l’aperçu de notre nouveau [portail pour les développeurs](https://identity.microsoft.com/Docs/iOS) qui vous aideront à être opérationnel avec Azure Active Directory en quelques minutes !  Le portail de développement vous guidera tout au long du processus d’enregistrement une application et intégration Azure AD dans votre code.  Lorsque vous avez terminé, vous aurez accès une application simple qui peut authentifier les utilisateurs dans votre client et un serveur principal qui peuvent accepter des jetons et effectuer la validation. 

## <a name="1-determine-what-your-redirect-uri-will-be-for-ios"></a>*1. Déterminez quels seront vos URI rediriger pour iOS*

Afin de pouvoir lancer en toute sécurité vos applications dans certains scénarios SSO nous avons besoin de créer un **URI rediriger** dans un format particulier. Un URI rediriger est utilisé pour vous assurer que les jetons renvoyant à l’application correcte posées pour eux.

Le format iOS pour un URI rediriger est la suivante :

```
<app-scheme>://<bundle-id>
```

-   **jeu d’aap** - cela est enregistré dans votre projet XCode. Il est comment les autres applications peuvent vous appeler. Vous pouvez trouver cette sous Info.plist -> types d’URL -> identificateur d’URL. Vous devez créer une si vous n’avez pas une ou plusieurs configuré.
-   **id de l’ensemble de guides** - il s’agit de l’identificateur de groupe se trouvant sous « identité » désactiver les paramètres de votre projet dans XCode.

Voici un exemple de ce code de démarrage rapide : ***msquickstart://com.microsoft.azureactivedirectory.samples.graph.QuickStart***

## <a name="2-register-the-directorysearcher-application"></a>*2. enregistrer l’Application DirectorySearcher*
Pour activer votre application obtenir des jetons, vous devez tout d’abord l’enregistrer dans votre client Azure AD et lui accorder l’autorisation d’accéder à l’API Azure AD Graph :

-   Se connecter au portail de gestion Azure
-   Dans le volet de navigation gauche, cliquez sur **Active Directory**
-   Sélectionnez un client dans lesquelles vous pouvez enregistrer l’application.
-   Cliquez sur l’onglet **Applications** , puis cliquez sur **Ajouter** dans le bac d’alimentation bas.
-   Suivez les invites et créez une nouvelle **Application cliente Native**.
    -   Le **nom** de l’application décrira votre application aux utilisateurs finaux
    -   **Rediriger Uri** est une combinaison de schéma et chaîne qui utilise Azure AD pour renvoyer les réponses jetons.  Entrez une valeur spécifique à votre application en fonction des informations ci-dessus.
-   Une fois que vous avez terminé l’enregistrement, AAD affecter votre application un identificateur unique client.  Vous devez cette valeur dans les sections suivantes, donc copier à partir de l’onglet **configurer** .
- Également dans l’onglet **configurer** , recherchez la section « Autorisations pour les autres Applications ».  Pour l’application « Azure Active Directory », ajoutez l’autorisation de **l’annuaire de l’organisation de votre accès** sous **Autorisations d’accès délégué**.  Cela permettra à votre application à l’API de graphique pour les utilisateurs de la requête.

## <a name="3-install--configure-adal"></a>*3. installer et configurer le terme ADAL*
Maintenant que vous avez une application dans Azure Active Directory, vous pouvez installer terme ADAL et écrivez votre code liées à l’identité.  Dans l’ordre de terme ADAL être en mesure de communiquer avec Azure Active Directory, vous devez lui fournir des informations sur l’inscription de votre application.
-   Commencez par ajouter le terme ADAL au projet DirectorySearcher à l’aide de Cocapods.

```
$ vi Podfile
```
Ajoutez le code suivant cette podfile :

```
source 'https://github.com/CocoaPods/Specs.git'
link_with ['QuickStart']
xcodeproj 'QuickStart'

pod 'ADALiOS'
```

Maintenant charger la podfile à l’aide de cocoapods. Cela créera un nouvel espace de travail XCode vous allez charger.

```
$ pod install
...
$ open QuickStart.xcworkspace
```

-   Dans le projet de démarrage rapide, ouvrez le fichier plist `settings.plist`.  Remplacer les valeurs des éléments dans la section pour refléter les valeurs que vous saisissez dans le portail Azure.  Votre code font référence ces valeurs chaque fois qu’il utilise le terme ADAL.
    -   La `tenant` est le domaine de votre client Azure AD, par exemple contoso.onmicrosoft.com
    -   La `clientId` est l’identifiant du client de votre application que vous avez copiée à partir du portail.
    -   La `redirectUri` est la redirection url que vous avez enregistré dans le portail.

## <a name="4--use-adal-to-get-tokens-from-aad"></a>*4. Utilisez terme ADAL pour obtenir des jetons de AAD*
Le principe de base derrière le terme ADAL est que chaque fois que votre application a besoin d’un jeton d’accès, il appelle tout simplement une completionBlock `+(void) getToken : `, et le terme ADAL fait le reste.  

-   Dans la `QuickStart` ouvert le projet, `GraphAPICaller.m` et recherchez le `// TODO: getToken for generic Web API flows. Returns a token with no additional parameters provided.` commentaire dans la partie supérieure.  Il s’agit de l’endroit où vous passez terme ADAL les coordonnées à un CompletionBlock pour communiquer avec Azure AD et lui dire comment mettre en cache des jetons.

```ObjC
+(void) getToken : (BOOL) clearCache
           parent:(UIViewController*) parent
completionHandler:(void (^) (NSString*, NSError*))completionBlock;
{
    AppData* data = [AppData getInstance];
    if(data.userItem){
        completionBlock(data.userItem.accessToken, nil);
        return;
    }

    ADAuthenticationError *error;
    authContext = [ADAuthenticationContext authenticationContextWithAuthority:data.authority error:&error];
    authContext.parentController = parent;
    NSURL *redirectUri = [[NSURL alloc]initWithString:data.redirectUriString];

    [ADAuthenticationSettings sharedInstance].enableFullScreen = YES;
    [authContext acquireTokenWithResource:data.resourceId
                                 clientId:data.clientId
                              redirectUri:redirectUri
                           promptBehavior:AD_PROMPT_AUTO
                                   userId:data.userItem.userInformation.userId
                     extraQueryParameters: @"nux=1" // if this strikes you as strange it was legacy to display the correct mobile UX. You most likely won't need it in your code.
                          completionBlock:^(ADAuthenticationResult *result) {

                              if (result.status != AD_SUCCEEDED)
                              {
                                  completionBlock(nil, result.error);
                              }
                              else
                              {
                                  data.userItem = result.tokenCacheStoreItem;
                                  completionBlock(result.tokenCacheStoreItem.accessToken, nil);
                              }
                          }];
}

```

- À présent, nous devons utiliser ce jeton pour rechercher des utilisateurs dans le graphique. Trouver la `// TODO: implement SearchUsersList` commentThis méthode apporte une requête GET à l’API Azure AD Graph à requête pour les utilisateurs dont UPN commence par le terme de recherche donné.  Mais afin de l’API de graphique d’une requête, vous devez inclure un access_token dans les `Authorization` en-tête de la demande - il s’agit de procéder à un terme ADAL.

```ObjC
+(void) searchUserList:(NSString*)searchString
                parent:(UIViewController*) parent
       completionBlock:(void (^) (NSMutableArray* Users, NSError* error)) completionBlock
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    AppData* data = [AppData getInstance];

    NSString *graphURL = [NSString stringWithFormat:@"%@%@/users?api-version=%@&$filter=startswith(userPrincipalName, '%@')", data.taskWebApiUrlString, data.tenant, data.apiversion, searchString];


    [self craftRequest:[self.class trimString:graphURL]
                parent:parent
     completionHandler:^(NSMutableURLRequest *request, NSError *error) {

         if (error != nil)
         {
             completionBlock(nil, error);
         }
         else
         {

             NSOperationQueue *queue = [[NSOperationQueue alloc]init];

             [NSURLConnection sendAsynchronousRequest:request queue:queue completionHandler:^(NSURLResponse *response, NSData *data, NSError *error) {

                 if (error == nil && data != nil){

                     NSDictionary *dataReturned = [NSJSONSerialization JSONObjectWithData:data options:0 error:nil];

                     // We can grab the top most JSON node to get our graph data.
                     NSArray *graphDataArray = [dataReturned objectForKey:@"value"];

                     // Don't be thrown off by the key name being "value". It really is the name of the
                     // first node. :-)

                     //each object is a key value pair
                     NSDictionary *keyValuePairs;
                     NSMutableArray* Users = [[NSMutableArray alloc]init];

                     for(int i =0; i < graphDataArray.count; i++)
                     {
                         keyValuePairs = [graphDataArray objectAtIndex:i];

                         User *s = [[User alloc]init];
                         s.upn = [keyValuePairs valueForKey:@"userPrincipalName"];
                         s.name =[keyValuePairs valueForKey:@"givenName"];

                         [Users addObject:s];
                     }

                     completionBlock(Users, nil);
                 }
                 else
                 {
                     completionBlock(nil, error);
                 }

             }];
         }
     }];

}

```
- Lorsque votre application demande un jeton en appelant `getToken(...)`, terme ADAL essaiera de retourner un jeton sans demandant à l’utilisateur pour les informations d’identification.  Si terme ADAL détermine que l’utilisateur doit se connecter à obtenir un jeton, il sera affiche une boîte de dialogue connexion, collecter les informations d’identification de l’utilisateur et renvoyer un jeton si celle-ci est réussie.  Si le terme ADAL ne peut pas retourner un jeton pour une raison quelconque, il génère une `AdalException`.
- Notez que la `AuthenticationResult` objet contient un `tokenCacheStoreItem` objet qui peut être utilisé pour collecter les informations de votre application peut nécessiter.  Dans le démarrage rapide, `tokenCacheStoreItem` est utilisée pour déterminer si authenitcation a déjà eu lieu.


## <a name="step-5-build-and-run-the-application"></a>Étape 5 : Créer et exécuter l’application



Félicitations ! Vous avez une application iOS pour travailler avec la fonctionnalité permettant d’authentification des utilisateurs, en toute sécurité appelez d’API Web utilisant OAuth 2.0 et obtenez des informations de base sur l’utilisateur.  Si vous n’avez pas déjà, c’est le temps de remplir votre client avec certains utilisateurs.  Exécuter votre application de démarrage rapide, puis connectez-vous à l’aide d’un de ces utilisateurs.  Rechercher d’autres utilisateurs en fonction de leur UPN.  Fermez l’application et réexécuter.  Avez-vous remarqué la session de l’utilisateur reste intacte.

Terme ADAL facilite l’incorporer toutes ces fonctionnalités identité courantes dans votre application.  Il s’occupe de tout le travail dirty pour vous - gestion du cache, OAuth protocoles pris en charge, proposez à l’utilisateur avec une connexion de l’interface utilisateur, l’actualisation des jetons qui a expiré et bien plus encore.  Tout ce que vous devez vraiment savoir est un seul appel d’API, `getToken`.

Pour référence, l’échantillon terminée (sans vos valeurs configuration) est fourni [ici](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip).  

## <a name="additional-scenarios"></a>Scénarios supplémentaires
Vous pouvez désormais atteindre des scénarios supplémentaires.  Vous souhaiterez peut-être essayer :

- [Sécuriser un site Web Node.JS API avec Azure Active Directory](active-directory-devquickstarts-webapi-nodejs.md)
- Découvrez [comment activer l’authentification unique entre application sur iOS à l’aide du terme ADAL](active-directory-sso-ios.md)  

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]
