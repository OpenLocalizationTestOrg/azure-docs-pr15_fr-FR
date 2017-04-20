<properties
pageTitle="Application Native Azure Active Directory 2.0 .NET | Microsoft Azure"
description="Découvrez comment créer une application native .NET qui vous permet de comptes d’utilisateurs avec les deux Account Microsoft personnel et Professionnel ou scolaire."
services="active-directory"
documentationCenter=""
authors="dstrockis"
manager="mbaldwin"
editor=""/>

<tags
ms.service="active-directory"
ms.workload="identity"
ms.tgt_pltfrm="na"
ms.devlang="dotnet"
ms.topic="article"
ms.date="07/30/2016"
ms.author="dastrock; vittorib"/>

# <a name="add-sign-in-to-a-windows-desktop-app"></a>Ajouter de se connecter à une application de bureau de Windows

Avec le point de terminaison de la version 2.0, vous pouvez ajouter rapidement l’authentification à vos applications de bureau avec prise en charge pour les deux comptes Microsoft personnels et Professionnel ou scolaire comptes.  Il permet également de votre application de communiquer en toute sécurité avec un site web et api, ainsi que [Le graphique Microsoft](https://graph.microsoft.io) et quelques-unes des [API d’unifiée Office 365](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2).

> [AZURE.NOTE] Pas tous les scénarios Azure Active Directory (AD) et les fonctionnalités sont prises en charge par le point de terminaison version 2.0.  Pour déterminer si vous devez utiliser le point de terminaison version 2.0, lisez les [limitations de la version 2.0](active-directory-v2-limitations.md).

Pour les [applications natives .NET qui s’exécutent sur un appareil](active-directory-v2-flows.md#mobile-and-native-apps), Azure AD fournit la bibliothèque d’authentification identité Microsoft ou MSAL.  But de MSAL de vie consiste à faciliter pour votre application obtenir des jetons pour appeler des services web.  Pour montrer simplement combien il est facile, ici nous allons créer une application de la liste des tâches .NET WPF qui :

- Se connecte l’utilisateur et obtient jetons à l’aide du [protocole d’authentification 2.0 jeton](active-directory-v2-protocols.md#oauth2-authorization-code-flow)d’accès.
- En toute sécurité appelle un service web de la liste des tâches, qui est également sécurisé par OAuth 2.0 serveur principal.
- Déconnecte l’utilisateur.

## <a name="download-sample-code"></a>Télécharger des exemples de code

Le code de ce didacticiel étant conservée [sur GitHub](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet).  Pour suivre, vous pouvez [Télécharger structure de l’application comme un fichier zip](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/skeleton.zip) ou cloner la structure :

    git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git

L’application terminée est fournie à la fin de ce didacticiel également.

## <a name="register-an-app"></a>Enregistrer une application
Créer une nouvelle application en [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), ou suivez [la procédure détaillée](active-directory-v2-app-registration.md).  Veillez à :

- Copier vers le bas de l' **Id de l’Application** affecté à votre application, vous en aurez besoin plus rapidement.
- Ajouter la plateforme **Mobile** pour votre application.

## <a name="install--configure-msal"></a>Installer et configurer MSAL
Maintenant que vous avez une application inscrite auprès de Microsoft, vous pouvez installer MSAL et écrivez votre code liées à l’identité.  Dans l’ordre de MSAL être en mesure de communiquer le point de terminaison version 2.0, vous devez lui fournir des informations sur l’inscription de votre application.

-   Commencez par ajouter MSAL au projet TodoListClient à l’aide de la Console du Gestionnaire de Package.

```
PM> Install-Package Microsoft.Identity.Client -ProjectName TodoListClient -IncludePrerelease
```

-   Dans le projet TodoListClient, ouvrez `app.config`.  Remplacer les valeurs des éléments dans la `<appSettings>` section pour refléter les valeurs que vous saisissez dans le portail d’inscription de l’application.  Votre code font référence ces valeurs chaque fois qu’il utilise MSAL.
    -   La `ida:ClientId` est l' **Id de l’Application** de votre application que vous avez copiée à partir du portail.

- Dans le projet de liste des tâches-Service, ouvrez `web.config` à la racine du projet.  
    - Remplacer le `ida:Audience` valeur avec le même **Id de l’Application** à partir du portail.

## <a name="use-msal-to-get-tokens"></a>MSAL permet d’obtenir des jetons
Le principe de base derrière MSAL est que chaque fois que votre application a besoin d’un jeton d’accès, vous appelez simplement `app.AcquireToken(...)`, et MSAL fait le reste.  

-   Dans la `TodoListClient` ouvert le projet, `MainWindow.xaml.cs` et recherchez le `OnInitialized(...)` méthode.  La première étape consiste à initialisation votre application `PublicClientApplication` -classe principale du MSAL représentant les applications natives.  Il s’agit de l’endroit où vous passez MSAL les coordonnées dont il a besoin pour communiquer avec Azure AD et lui dire comment mettre en cache des jetons.

```C#
protected override async void OnInitialized(EventArgs e)
{
        base.OnInitialized(e);

        app = new PublicClientApplication(new FileCache());
        AuthenticationResult result = null;
        ...
}
```

- Démarrage de l’application, nous voulons vérifier et voir si l’utilisateur est déjà connecté à l’application.  Toutefois, nous ne voulez pas appeler une interface utilisateur connexion encore : nous allons définir l’utilisateur, cliquez sur « Connexion » pour le faire.  Également dans le `OnInitialized(...)` méthode :

```C#
// As the app starts, we want to check to see if the user is already signed in.
// You can do so by trying to get a token from MSAL, using the method
// AcquireTokenSilent.  This forces MSAL to throw an exception if it cannot
// get a token for the user without showing a UI.
try
{
    result = await app.AcquireTokenSilentAsync(new string[] { clientId });
    // If we got here, a valid token is in the cache - or MSAL was able to get a new oen via refresh token.
    // Proceed to fetch the user's tasks from the TodoListService via the GetTodoList() method.
    
    SignInButton.Content = "Clear Cache";
    GetTodoList();
}
catch (MsalException ex)
{
    if (ex.ErrorCode == "user_interaction_required")
    {
        // If user interaction is required, the app should take no action,
        // and simply show the user the sign in button.
    }
    else
    {
        // Here, we catch all other MsalExceptions
        string message = ex.Message;
        if (ex.InnerException != null)
        {
            message += "Inner Exception : " + ex.InnerException.Message;
        }
        MessageBox.Show(message);
    }
}

```

- Si l’utilisateur n’est pas connecté et ils cliquent sur le bouton « Connexion », nous voulons appeler une interface utilisateur de connexion et que l’utilisateur à entrer ses informations d’identification.  Implémenter le Gestionnaire de bouton se connecter :

```C#
private async void SignIn(object sender = null, RoutedEventArgs args = null)
{
        // TODO: Sign the user out if they clicked the "Clear Cache" button

// If the user clicked the 'Sign-In' button, force
// MSAL to prompt the user for credentials by using
// AcquireTokenAsync, a method that is guaranteed to show a prompt to the user.
// MSAL will get a token for the TodoListService and cache it for you.

AuthenticationResult result = null;
try
{
    result = await app.AcquireTokenAsync(new string[] { clientId });
    SignInButton.Content = "Clear Cache";
    GetTodoList();
}
catch (MsalException ex)
{
    // If MSAL cannot get a token, it will throw an exception.
    // If the user canceled the login, it will result in the
    // error code 'authentication_canceled'.

    if (ex.ErrorCode == "authentication_canceled")
    {
        MessageBox.Show("Sign in was canceled by the user");
    }
    else
    {
        // An unexpected error occurred.
        string message = ex.Message;
        if (ex.InnerException != null)
        {
            message += "Inner Exception : " + ex.InnerException.Message;
        }

        MessageBox.Show(message);
    }

    return;
}


}
```

- Si l’utilisateur avec succès signes complémentaire, MSAL recevra et mettre en cache un jeton pour vous, et vous pouvez continuer à appeler le `GetTodoList()` méthode en toute sécurité.  Tout ce qui reste pour obtenir des tâches d’un utilisateur consiste à mettre en œuvre la `GetTodoList()` méthode.

```C#
private async void GetTodoList()
{

AuthenticationResult result = null;
try
{
    // Here, we try to get an access token to call the TodoListService
    // without invoking any UI prompt.  AcquireTokenSilentAsync forces
    // MSAL to throw an exception if it cannot get a token silently.


    result = await app.AcquireTokenSilentAsync(new string[] { clientId });
}
catch (MsalException ex)
{
    // MSAL couldn't get a token silently, so show the user a message
    // and let them click the Sign-In button.

    if (ex.ErrorCode == "user_interaction_required")
    {
        MessageBox.Show("Please sign in first");
        SignInButton.Content = "Sign In";
    }
    else
    {
        // In any other case, an unexpected error occurred.

        string message = ex.Message;
        if (ex.InnerException != null)
        {
            message += "Inner Exception : " + ex.InnerException.Message;
        }
        MessageBox.Show(message);
    }

    return;
}

// Once the token has been returned by MSAL,
// add it to the http authorization header,
// before making the call to access the To Do list service.

httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.Token);


        ...
...


- When the user is done managing their To-Do List, they may finally sign out of the app by clicking the "Clear Cache" button.

```C#
private async void SignIn(object sender = null, RoutedEventArgs args = null)
{
        // If the user clicked the 'clear cache' button,
        // clear the MSAL token cache and show the user as signed out.
        // It's also necessary to clear the cookies from the browser
        // control so the next user has a chance to sign in.

        if (SignInButton.Content.ToString() == "Clear Cache")
        {
                TodoList.ItemsSource = string.Empty;
                app.UserTokenCache.Clear(app.ClientId);
                ClearCookies();
                SignInButton.Content = "Sign In";
                return;
        }

        ...
```

## <a name="run"></a>Exécuter

Félicitations ! Vous avez à présent une application WPF .NET travail qui a la capacité d’authentification des utilisateurs et en toute sécurité appel API Web utilisant OAuth 2.0.  Exécuter les deux vos projets, puis connectez-vous avec un compte Microsoft personnel ou un compte professionnel ou scolaire.  Ajouter des tâches à la liste des tâches de cet utilisateur.  Se déconnecter, puis reconnectez-vous en tant qu’un autre utilisateur à accéder à leur liste des tâches.  Fermez l’application et réexécuter.  Avez-vous remarqué la session de l’utilisateur reste inchangée : c’est parce que l’application met en cache les jetons dans un fichier local.

MSAL facilite incorporer des fonctionnalités identité courantes dans votre application, à l’aide de comptes personnels et Professionnel.  Il s’occupe de tout le travail dirty pour vous - gestion du cache, OAuth protocoles pris en charge, proposez à l’utilisateur avec une connexion de l’interface utilisateur, l’actualisation des jetons qui a expiré et bien plus encore.  Tout ce que vous devez vraiment savoir est un appel API unique, `app.AcquireTokenAsync(...)`.

Pour référence, l’exemple terminé (sans vos valeurs configuration) [est fourni sous forme d’un fichier zip ici](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/complete.zip), ou vous pouvez cloner GitHub :

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git```

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez désormais déplacer vers des rubriques plus avancées.  Vous souhaiterez peut-être essayer :

- [Sécurisation de l’API Web TodoListService avec le point de terminaison version 2.0](active-directory-v2-devquickstarts-dotnet-api.md)

Pour des ressources supplémentaires, consultez :  

- [Le guide du développeur version 2.0 >>](active-directory-appmodel-v2-overview.md)
- [StackOverflow « msal » balise >>](http://stackoverflow.com/questions/tagged/msal)

## <a name="get-security-updates-for-our-products"></a>Obtenir des mises à jour de sécurité pour nos produits

Nous vous invitons à obtenir des notifications d’incidents de sécurité se produire en visitant [cette page](https://technet.microsoft.com/security/dd252948) et l’abonnement aux alertes avis de sécurité.
