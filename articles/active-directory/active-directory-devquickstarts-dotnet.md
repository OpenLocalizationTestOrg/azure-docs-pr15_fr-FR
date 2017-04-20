<properties
    pageTitle="Azure AD .NET mise en route | Microsoft Azure"
    description="Découvrez comment créer une application de bureau de Windows .NET qui s’intègre à Azure AD pour se connecter et appelle Azure AD protégé API utilisant OAuth."
    services="active-directory"
    documentationCenter=".net"
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="dastrock"/>


# <a name="integrate-azure-ad-into-a-windows-desktop-wpf-app"></a>Intégrer Azure AD dans une application de bureau WPF Windows

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Si vous créez une application de bureau, Azure AD rend simple et rapide pour vous authentifier vos utilisateurs avec leur compte Active Directory.  Il permet également de votre application consomme en toute sécurité tout web API protégé par Azure AD, tels que les API d’Office 365 ou l’API Azure.

Pour .NET natives clients qui doivent accéder aux ressources protégées, Azure AD fournit la bibliothèque de l’authentification Active Directory, ou terme ADAL.  But du terme ADAL dans vie consiste à faciliter pour votre application obtenir des jetons d’accès.  Pour montrer simplement combien il est facile, ici nous allons créer une application de la liste des tâches .NET WPF qui :

-   Obtient jetons pour l’appel de l’API Azure AD Graph à l’aide du [protocole d’authentification 2.0 jeton](https://msdn.microsoft.com/library/azure/dn645545.aspx)d’accès.
-   Recherche dans un répertoire pour les utilisateurs avec un alias de donnée.
-   Signes les utilisateurs.

Pour créer l’application de travail complète, vous devez :

2. Enregistrer votre application avec Azure AD.
3. Installer et configurer le terme ADAL.
5. Terme ADAL permet d’obtenir des jetons à partir d’Azure AD.

Pour démarrer, [Téléchargez la structure de l’application](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/skeleton.zip) ou [Télécharger l’exemple terminé](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/complete.zip).  Vous devez également un client Azure AD dans lequel vous pouvez créer des utilisateurs et enregistrer une application.  Si vous n’avez pas déjà un client, [Découvrez comment obtenir un](active-directory-howto-tenant.md).

## <a name="1-register-the-directorysearcher-application"></a>*1. enregistrer l’Application DirectorySearcher*
Pour activer votre application obtenir des jetons, vous devez tout d’abord l’enregistrer dans votre client Azure AD et lui accorder l’autorisation d’accéder à l’API Azure AD Graph :

-   Se connecter au portail de gestion Azure
-   Dans le volet de navigation gauche, cliquez sur **Active Directory**
-   Sélectionnez un client dans lesquelles vous pouvez enregistrer l’application.
-   Cliquez sur l’onglet **Applications** , puis cliquez sur **Ajouter** dans le bac d’alimentation bas.
-   Suivez les invites et créez une nouvelle **Application cliente Native**.
    -   Le **nom** de l’application décrira votre application aux utilisateurs finaux
    -   **Rediriger Uri** est une combinaison de schéma et chaîne qui utilise Azure AD pour renvoyer les réponses jetons.  Entrez une valeur spécifique à votre application, par exemple, `http://DirectorySearcher`.
-   Une fois que vous avez terminé l’enregistrement, AAD affecter votre application un identificateur unique client.  Vous devez cette valeur dans les sections suivantes, donc copier à partir de l’onglet **configurer** .
- Également dans l’onglet **configurer** , recherchez la section « Autorisations pour les autres Applications ».  Pour l’application « Azure Active Directory », ajoutez l’autorisation de **l’annuaire de l’organisation de votre accès** sous **Autorisations d’accès délégué**.  Cela permettra à votre application à l’API de graphique pour les utilisateurs de la requête.

## <a name="2-install--configure-adal"></a>*2. installer et configurer le terme ADAL*
Maintenant que vous avez une application dans Azure Active Directory, vous pouvez installer terme ADAL et écrivez votre code liées à l’identité.  Dans l’ordre de terme ADAL être en mesure de communiquer avec Azure Active Directory, vous devez lui fournir des informations sur l’inscription de votre application.
-   Commencez par ajouter le terme ADAL au projet DirectorySearcher à l’aide de la Console du Gestionnaire de Package.

```
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
```

-   Dans le projet DirectorySearcher, ouvrez `app.config`.  Remplacer les valeurs des éléments dans la `<appSettings>` section pour refléter les valeurs que vous saisissez dans le portail Azure.  Votre code font référence ces valeurs chaque fois qu’il utilise le terme ADAL.
    -   La `ida:Tenant` est le domaine de votre client Azure AD, par exemple contoso.onmicrosoft.com
    -   La `ida:ClientId` est l’identifiant du client de votre application que vous avez copiée à partir du portail.
    -   La `ida:RedirectUri` est la redirection url que vous avez enregistré dans le portail.

## <a name="3--use-adal-to-get-tokens-from-aad"></a>*3. Utilisez terme ADAL pour obtenir des jetons de AAD*
Le principe de base derrière le terme ADAL est que chaque fois que votre application a besoin d’un jeton d’accès, il appelle simplement `authContext.AcquireTokenAsync(...)`, et le terme ADAL fait le reste.  

-   Dans la `DirectorySearcher` ouvert le projet, `MainWindow.xaml.cs` et recherchez le `MainWindow()` méthode.  La première étape consiste à initialisation votre application `AuthenticationContext` -ADAL de classe principale.  Il s’agit de l’endroit où vous passez terme ADAL les coordonnées dont il a besoin pour communiquer avec Azure AD et lui dire comment mettre en cache des jetons.

```C#
public MainWindow()
{
    InitializeComponent();

    authContext = new AuthenticationContext(authority, new FileCache());

    CheckForCachedToken();
}
```

- Recherchez maintenant le `Search(...)` méthode, qui est appelé lorsque l’utilisateur cliks la « recherche » bouton dans l’interface utilisateur de l’application.  Cette méthode effectue une requête GET à l’API Azure AD Graph à requête pour les utilisateurs dont UPN commence par le terme de recherche donné.  Mais afin de l’API de graphique d’une requête, vous devez inclure un access_token dans les `Authorization` en-tête de la demande - il s’agit de procéder à un terme ADAL.

```C#
private async void Search(object sender, RoutedEventArgs e)
{
    // Validate the Input String
    if (string.IsNullOrEmpty(SearchText.Text))
    {
        MessageBox.Show("Please enter a value for the To Do item name");
        return;
    }

    // Get an Access Token for the Graph API
    AuthenticationResult result = null;
    try
    {
        result = await authContext.AcquireTokenAsync(graphResourceId, clientId, redirectUri, new PlatformParameters(PromptBehavior.Auto));
        UserNameLabel.Content = result.UserInfo.DisplayableId;
        SignOutButton.Visibility = Visibility.Visible;
    }
    catch (AdalException ex)
    {
        // An unexpected error occurred, or user canceled the sign in.
        if (ex.ErrorCode != "access_denied")
            MessageBox.Show(ex.Message);

        return;
    }

    ...
}
```
- Lorsque votre application demande un jeton en appelant `AcquireTokenAsync(...)`, terme ADAL tentera de renvoyer un jeton sans demandant à l’utilisateur pour les informations d’identification.  Si terme ADAL détermine que l’utilisateur doit se connecter à obtenir un jeton, il sera affiche une boîte de dialogue connexion, collecter les informations d’identification de l’utilisateur et renvoyer un jeton si celle-ci est réussie.  Si le terme ADAL ne peut pas retourner un jeton pour une raison quelconque, il génère une `AdalException`.
- Notez que la `AuthenticationResult` objet contient un `UserInfo` objet qui peut être utilisé pour collecter les informations de votre application peut nécessiter.  Dans le composant DirectorySearcher, `UserInfo` est utilisé pour personnaliser l’interface utilisateur de l’application avec l’id de l’utilisateur.

- Lorsque l’utilisateur clique sur le bouton « Se déconnecter », nous voulons pour vous assurer que l’appel suivant à `AcquireTokenAsync(...)` demande à l’utilisateur pour vous connecter.  Avec le terme ADAL, c’est aussi simple que vider le cache de jeton :

```C#
private void SignOut(object sender = null, RoutedEventArgs args = null)
{
    // Clear the token cache
    authContext.TokenCache.Clear();

    ...
}
```

- Toutefois, si l’utilisateur ne clique pas le bouton « Se déconnecter », vous souhaiterez maintenir la session de l’utilisateur pour la prochaine fois qu’ils exécutent le DirectorySearcher.  Lorsque l’application est lancée, vous pouvez vérifier du cache de jetons du terme ADAL pour un jeton existant et mettre à jour l’interface utilisateur en conséquence.  Dans la `CheckForCachedToken()` méthode, effectuez un autre appel à `AcquireTokenAsync(...)`, cette fois en passant la `PromptBehavior.Never` paramètre.  `PromptBehavior.Never`indiquera terme ADAL que l’utilisateur ne doit pas être invité pour se connecter et terme ADAL doit à la place lever une exception s’il est impossible de renvoyer un jeton.

```C#
public async void CheckForCachedToken() 
{
    // As the application starts, try to get an access token without prompting the user.  If one exists, show the user as signed in.
    AuthenticationResult result = null;
    try
    {
        result = await authContext.AcquireTokenAsync(graphResourceId, clientId, redirectUri, new PlatformParameters(PromptBehavior.Never));
    }
    catch (AdalException ex)
    {
        if (ex.ErrorCode != "user_interaction_required")
        {
            // An unexpected error occurred.
            MessageBox.Show(ex.Message);
        }

        // If user interaction is required, proceed to main page without singing the user in.
        return;
    }

    // A valid token is in the cache
    SignOutButton.Visibility = Visibility.Visible;
    UserNameLabel.Content = result.UserInfo.DisplayableId;
}
```

Félicitations ! Vous avez une application WPF .NET qui comporte la fonctionnalité permettant d’authentification des utilisateurs, en toute sécurité appelez API Web utilisant OAuth 2.0 de travail et obtenez des informations de base sur l’utilisateur.  Si vous n’avez pas déjà, c’est le temps de remplir votre client avec certains utilisateurs.  Exécuter votre application DirectorySearcher, puis connectez-vous à l’aide d’un de ces utilisateurs.  Rechercher d’autres utilisateurs en fonction de leur UPN.  Fermez l’application et réexécuter.  Avez-vous remarqué la session de l’utilisateur reste intacte.  Déconnectez-vous, puis reconnectez-vous en tant qu’un autre utilisateur.

Terme ADAL facilite l’incorporer toutes ces fonctionnalités identité courantes dans votre application.  Il s’occupe de tout le travail dirty pour vous - gestion du cache, OAuth protocoles pris en charge, proposez à l’utilisateur avec une connexion de l’interface utilisateur, l’actualisation des jetons qui a expiré et bien plus encore.  Tout ce que vous devez vraiment savoir est un appel API unique, `authContext.AcquireTokenAsync(...)`.

Pour référence, l’échantillon terminée (sans vos valeurs configuration) est fourni [ici](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/complete.zip).  Vous pouvez désormais atteindre des scénarios supplémentaires.  Vous souhaiterez peut-être essayer :

[Sécuriser un site Web .NET API avec Azure AD >>](active-directory-devquickstarts-webapi-dotnet.md)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]
