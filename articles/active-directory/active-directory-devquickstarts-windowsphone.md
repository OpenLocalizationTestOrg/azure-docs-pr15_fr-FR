<properties
    pageTitle="Azure AD Windows Phone prise en main | Microsoft Azure"
    description="Découvrez comment créer une application Windows Phone qui s’intègre à Azure AD pour se connecter et appelle Azure AD protégé API utilisant OAuth."
    services="active-directory"
    documentationCenter="windows"
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="mobile-windows-phone"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="dastrock"/>



# <a name="integrate-azure-ad-with-a-windows-phone-app"></a>Intégrer Azure AD une application Windows Phone

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Si vous créez une application Windows Phone 8.1, Azure AD rend simple et rapide pour vous authentifier vos utilisateurs avec leur compte Active Directory.  Il permet également de votre application consomme en toute sécurité tout web API protégé par Azure AD, tels que les API d’Office 365 ou l’API Azure.

> [AZURE.NOTE] Cet exemple utilise ADAL version 2.0.  Pour les dernières technologies, vous souhaiterez peut-être plutôt essayer notre [didacticiel universel Windows à l’aide de ADAL version 3.0](active-directory-devquickstarts-windowsstore.md).  Si vous créez bien une application pour Windows Phone 8.1, il s’agit de l’emplacement approprié.  Version 2.0 ADAL est entièrement pris en et les procédures recommandées de développement d’applications agianst Windows Phone 8.1 utilise Azure AD.

Pour .NET natives clients qui doivent accéder aux ressources protégées, Azure AD fournit la bibliothèque de l’authentification Active Directory, ou terme ADAL.  But du terme ADAL dans vie consiste à faciliter pour votre application obtenir des jetons d’accès.  Pour montrer simplement combien il est facile, ici nous allons créer une application Windows Phone 8.1 « répertoire recherche » qui :

-   Obtient jetons pour l’appel de l’API Azure AD Graph à l’aide du [protocole d’authentification 2.0 jeton](https://msdn.microsoft.com/library/azure/dn645545.aspx)d’accès.
-   Recherche dans un répertoire pour les utilisateurs avec un UPN donné.
-   Signes les utilisateurs.

Pour créer l’application de travail complète, vous devez :

2. Enregistrer votre application avec Azure AD.
3. Installer et configurer le terme ADAL.
5. Terme ADAL permet d’obtenir des jetons à partir d’Azure AD.

Pour démarrer, [télécharger un projet de structure](https://github.com/AzureADQuickStarts/NativeClient-WindowsPhone/archive/skeleton.zip) ou [Télécharger l’exemple terminé](https://github.com/AzureADQuickStarts/NativeClient-WindowsPhone/archive/complete.zip).  Chacun est une solution Visual Studio 2013.  Vous devez également un client Azure AD dans lequel vous pouvez créer des utilisateurs et enregistrer une application.  Si vous n’avez pas déjà un client, [Découvrez comment obtenir un](active-directory-howto-tenant.md).

## <a name="1-register-the-directory-searcher-application"></a>*1. enregistrer l’Application de recherche du répertoire*
Pour activer votre application obtenir des jetons, vous devez tout d’abord l’enregistrer dans votre client Azure AD et lui accorder l’autorisation d’accéder à l’API Azure AD Graph :

-   Se connecter au [portail de gestion Azure](https://manage.windowsazure.com)
-   Dans le volet de navigation gauche, cliquez sur **Active Directory**
-   Sélectionnez un client dans lesquelles vous pouvez enregistrer l’application.
-   Cliquez sur l’onglet **Applications** , puis cliquez sur **Ajouter** dans le bac d’alimentation bas.
-   Suivez les invites et créez une nouvelle **Application cliente Native**.
    -   Le **nom** de l’application décrira votre application aux utilisateurs finaux
    -   **Rediriger Uri** est une combinaison de schéma et chaîne qui utilise Azure AD pour renvoyer les réponses jetons.  Entrez une valeur d’espace réservé pour le moment, par exemple, `http://DirectorySearcher`.  Nous allons remplacer cette valeur ultérieurement.
-   Une fois que vous avez terminé l’enregistrement, AAD affecter votre application un identificateur unique client.  Vous devez cette valeur dans les sections suivantes, donc copier à partir de l’onglet **configurer** .
- Également dans l’onglet **configurer** , recherchez la section « Autorisations pour les autres Applications ».  Pour l’application « Azure Active Directory », ajoutez l’autorisation de **l’annuaire de l’organisation de votre accès** sous **Autorisations d’accès délégué**.  Cela permettra à votre application à l’API de graphique pour les utilisateurs de la requête.

## <a name="2-install--configure-adal"></a>*2. installer et configurer le terme ADAL*
Maintenant que vous avez une application dans Azure Active Directory, vous pouvez installer terme ADAL et écrivez votre code liées à l’identité.  Dans l’ordre de terme ADAL être en mesure de communiquer avec Azure Active Directory, vous devez lui fournir des informations sur l’inscription de votre application.
-   Commencez par ajouter le terme ADAL au projet DirectorySearcher à l’aide de la Console du Gestionnaire de Package.

```
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
```

-   Dans le projet DirectorySearcher, ouvrez `MainPage.xaml.cs`.  Remplacer les valeurs de la `Config Values` région pour refléter les valeurs que vous saisissez dans le portail Azure.  Votre code font référence ces valeurs chaque fois qu’il utilise le terme ADAL.
    -   La `tenant` est le domaine de votre client Azure AD, par exemple contoso.onmicrosoft.com
    -   La `clientId` est l’identifiant du client de votre application que vous avez copiée à partir du portail.
-   Vous devez maintenant découvrir l’uri de rappel pour votre application Windows Phone.  Définissez un point d’arrêt sur cette ligne de la `MainPage` méthode :

```
redirectURI = Windows.Security.Authentication.Web.WebAuthenticationBroker.GetCurrentApplicationCallbackUri();
```
- Exécuter l’application, puis copiez côté la valeur de `redirectUri` lorsque le point d’arrêt est atteint.  Il doit ressembler à

```
ms-app://s-1-15-2-1352796503-54529114-405753024-3540103335-3203256200-511895534-1429095407/
```

- Retour sous l’onglet **configuration** de votre application dans le portail de gestion Azure, remplacez la valeur de la **RedirectUri** avec cette valeur.  

## <a name="3--use-adal-to-get-tokens-from-aad"></a>*3. Utilisez terme ADAL pour obtenir des jetons de AAD*
Le principe de base derrière le terme ADAL est que chaque fois que votre application a besoin d’un jeton d’accès, il appelle simplement `authContext.AcquireToken(…)`, et le terme ADAL fait le reste.  

-   La première étape consiste à initialisation votre application `AuthenticationContext` -ADAL de classe principale.  Il s’agit de l’endroit où vous passez terme ADAL les coordonnées dont il a besoin pour communiquer avec Azure AD et lui dire comment mettre en cache des jetons.

```C#
public MainPage()
{
    ...

    // ADAL for Windows Phone 8.1 builds AuthenticationContext instances through a factory
    authContext = AuthenticationContext.CreateAsync(authority).GetResults();
}
```

- Recherchez maintenant le `Search(...)` méthode, qui est appelé lorsque l’utilisateur cliks la « recherche » bouton dans l’interface utilisateur de l’application.  Cette méthode effectue une requête GET à l’API Azure AD Graph à requête pour les utilisateurs dont UPN commence par le terme de recherche donné.  Mais afin de l’API de graphique d’une requête, vous devez inclure un access_token dans les `Authorization` en-tête de la demande - il s’agit de procéder à un terme ADAL.

```C#
private async void Search(object sender, RoutedEventArgs e)
{
    ...

    // Try to get a token without triggering any user prompt.
    // ADAL will check whether the requested token is in ADAL's token cache or can otherwise be obtained without user interaction.
    AuthenticationResult result = await authContext.AcquireTokenSilentAsync(graphResourceId, clientId);
    if (result != null && result.Status == AuthenticationStatus.Success)
    {
        // A token was successfully retrieved.
        QueryGraph(result);
    }
    else
    {
        // Acquiring a token without user interaction was not possible.
        // Trigger an authentication experience and specify that once a token has been obtained the QueryGraph method should be called
        authContext.AcquireTokenAndContinue(graphResourceId, clientId, redirectURI, QueryGraph);
    }
}
```
- Si l’authentification interactive est nécessaire, terme ADAL utilisera de Windows Phone Web authentification intermédiaire (carnet d’adresses) et page de connexion de [modèle de continuation](http://www.cloudidentity.com/blog/2014/06/16/adal-for-windows-phone-8-1-deep-dive/) pour afficher la publicité Azure.  Lorsque l’utilisateur se connecte, votre application doit transmettre terme ADAL les résultats de l’interaction de carnet d’adresses.  Ceci est aussi simple que la mise en œuvre la `ContinueWebAuthentication` interface :

```C#
// This method is automatically invoked when the application
// is reactivated after an authentication interaction through WebAuthenticationBroker.
public async void ContinueWebAuthentication(WebAuthenticationBrokerContinuationEventArgs args)
{
    // pass the authentication interaction results to ADAL, which will
    // conclude the token acquisition operation and invoke the callback specified in AcquireTokenAndContinue.
    await authContext.ContinueAcquireTokenAsync(args);
}
```

- Il est temps à utiliser le `AuthenticationResult` qui terme ADAL retournée dans votre application.  Dans la `QueryGraph(...)` rappel, joindre l’access_token que vous avez acquis à la requête GET dans l’en-tête d’autorisation :

```C#
private async void QueryGraph(AuthenticationResult result)
{
    if (result.Status != AuthenticationStatus.Success)
    {
        MessageDialog dialog = new MessageDialog(string.Format("If the error continues, please contact your administrator.\n\nError: {0}\n\nError Description:\n\n{1}", result.Error, result.ErrorDescription), "Sorry, an error occurred while signing you in.");
        await dialog.ShowAsync();
    }

    // Add the access token to the Authorization Header of the call to the Graph API, and call the Graph API.
    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

    ...
}
```
- Vous pouvez également utiliser la `AuthenticationResult` objet pour afficher des informations sur l’utilisateur dans votre application. Dans la `QueryGraph(...)` méthode, le résultat permet de représenter l’id d’utilisateur dans la page :

```C#
// Update the Page UI to represent the signed in user
ActiveUser.Text = result.UserInfo.DisplayableId;
```
- Enfin, vous pouvez utiliser terme ADAL pour vous connecter à l’utilisateur se déconnecter de l’application également.  Lorsque l’utilisateur clique sur le bouton « Se déconnecter », nous voulons pour vous assurer que l’appel suivant à `AcquireTokenSilentAsync(...)` échouera.  Avec le terme ADAL, c’est aussi simple que vider le cache de jeton :

```C#
private void SignOut()
{
    // Clear session state from the token cache.
    authContext.TokenCache.Clear();

    ...
}
```

Félicitations ! Vous avez un Windows Phone app qui comporte la fonctionnalité permettant d’authentification des utilisateurs, en toute sécurité appelez API Web utilisant OAuth 2.0 qui fonctionne et obtenez des informations de base sur l’utilisateur.  Si vous n’avez pas déjà, c’est le temps de remplir votre client avec certains utilisateurs.  Exécuter votre application DirectorySearcher, puis connectez-vous à l’aide d’un de ces utilisateurs.  Rechercher d’autres utilisateurs en fonction de leur UPN.  Fermez l’application et réexécuter.  Avez-vous remarqué la session de l’utilisateur reste intacte.  Déconnectez-vous, puis reconnectez-vous en tant qu’un autre utilisateur.

Terme ADAL facilite l’incorporer toutes ces fonctionnalités identité courantes dans votre application.  Il s’occupe de tout le travail dirty pour vous - gestion du cache, OAuth protocoles pris en charge, proposez à l’utilisateur avec une connexion de l’interface utilisateur, l’actualisation des jetons qui a expiré et bien plus encore.  Tout ce que vous devez vraiment savoir est un seul appel d’API, `authContext.AcquireToken*(…)`.

Pour référence, l’échantillon terminée (sans vos valeurs configuration) est fourni [ici](https://github.com/AzureADQuickStarts/NativeClient-WindowsPhone/archive/complete.zip).  Vous pouvez maintenant déplacer sur aux scénarios d’identité supplémentaire.  Vous souhaiterez peut-être essayer :

[Sécuriser un site Web .NET API avec Azure AD >>](active-directory-devquickstarts-webapi-dotnet.md)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]