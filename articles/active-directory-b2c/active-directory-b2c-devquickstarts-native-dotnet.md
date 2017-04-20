<properties
    pageTitle="Azure Active Directory B2C | Microsoft Azure"
    description="Découvrez comment créer une application de bureau de Windows qui inclut se connecter, d’abonnement, et gestion des profils à l’aide de Azure Active Directory B2C."
    services="active-directory-b2c"
    documentationCenter=".net"
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="07/22/2016"
    ms.author="dastrock"/>

# <a name="azure-ad-b2c-build-a-windows-desktop-app"></a>Azure AD B2C : Créer une application de bureau de Windows

À l’aide de B2C Azure Active Directory (AD Azure), vous pouvez ajouter des fonctionnalités de gestion des identités en libre-service puissantes à votre application de bureau en quelques étapes. Cet article vous explique comment créer une application « liste de tâches » .NET Windows Presentation Foundation (WPF) qui inclut des utilisateurs d’abonnement, connexion et gestion des profils. L’application inclura prise en charge d’abonnement et se connecter à l’aide d’un nom d’utilisateur ou de la messagerie électronique. Il inclut également prise en charge d’abonnement et se connecter à l’aide de comptes sociaux tels que Facebook et Google.

## <a name="get-an-azure-ad-b2c-directory"></a>Obtenir un répertoire Azure AD B2C

Avant de pouvoir utiliser Azure AD B2C, vous devez créer un répertoire ou du client.  Un répertoire est un conteneur pour toutes vos utilisateurs, applications, groupes et plus encore. Si vous n’avez pas encore, [créez un répertoire B2C](active-directory-b2c-get-started.md) avant de vous continuez dans ce guide.

## <a name="create-an-application"></a>Créer une application

Ensuite, vous devez créer une application dans votre annuaire B2C. Cela donne des informations Azure AD dont il a besoin pour communiquer en toute sécurité avec votre application. Pour créer une application, suivez [ces instructions](active-directory-b2c-app-registration.md).  N’oubliez pas :

- Inclure un **native client** dans l’application.
- Copier le **Rediriger URI** `urn:ietf:wg:oauth:2.0:oob`. Il est l’URL par défaut pour cet exemple de code.
- Copiez l' **ID de l’Application** qui est affectée à votre application. Vous en aurez besoin ultérieurement.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Créer vos stratégies

Dans Azure AD B2C, chaque expérience utilisateur est définie par une [stratégie](active-directory-b2c-reference-policies.md). Cet exemple de code contient trois expériences d’identité : s’inscrire, se connecter et modifier le profil. Vous devez créer une stratégie pour chaque type, comme décrit dans l' [article de référence de stratégie](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Lorsque vous créez les trois stratégies, veillez à :

- Sélectionnez **ID d’utilisateur d’abonnement** ou **d’abonnement à un courrier électronique** dans la carte de fournisseurs d’identité.
- Cliquez sur **nom d’affichage** et d’autres attributs d’abonnement dans votre stratégie d’inscription.
- Choisissez revendications **nom d’affichage** et **l’ID de l’objet** comme application réclamations au titre de chaque stratégie. Vous pouvez choisir d’autres réclamations également.
- Copier le **nom** de chaque stratégie après sa création. Il doit avoir le préfixe `b2c_1_`.  Vous aurez besoin ultérieurement ces noms de stratégie.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Une fois que vous avez créé les trois stratégies, vous êtes prêt à créer votre application.

## <a name="download-the-code"></a>Télécharger le code

Le code de ce didacticiel [étant conservées sur GitHub](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet). Pour créer l’exemple fur, vous pouvez [télécharger un projet de structure sous forme de fichier .zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/skeleton.zip). Vous pouvez également cloner la structure :

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet.git
```

L’application terminée est également [disponible sous forme de fichier .zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/complete.zip) ou sur la `complete` branche du même référentiel.

Après avoir téléchargé l’exemple de code, ouvrez le fichier .sln Visual Studio pour commencer. La `TaskClient` projet est l’application de bureau WPF l’utilisateur interagit avec. Pour les besoins de ce didacticiel, il appelle un site web tâche principale API, hébergé dans Azure, qui stocke la liste des tâches de chaque utilisateur.  Vous n’avez pas besoin de créer l’API web, vous l’avez déjà en cours d’exécution pour vous.

Pour découvrir comment une API web authentifie en toute sécurité les requêtes à l’aide de Azure AD B2C, consultez l' [API web mise en route article](active-directory-b2c-devquickstarts-api-dotnet.md).

## <a name="execute-policies"></a>Appliquer des règles
Votre application communique avec Azure AD B2C en envoyant des messages d’authentification qui spécifient la stratégie qu’ils souhaitent exécuter dans le cadre de la requête HTTP. Pour les applications de bureau .NET, vous pouvez utiliser l’aperçu bibliothèque d’authentification Microsoft (MSAL) pour envoyer des messages d’authentification OAuth 2.0, exécutez les stratégies et obtenez jetons que web API d’appel.

### <a name="install-msal"></a>Installer MSAL
Ajouter MSAL à la `TaskClient` projet à l’aide de la Console du Gestionnaire de Package Visual Studio.

```
PM> Install-Package Microsoft.Identity.Client -IncludePrerelease
```

### <a name="enter-your-b2c-details"></a>Entrez vos informations B2C
Ouvrez le fichier `Globals.cs` et remplacez toutes les valeurs de propriété par votre propre. Cette classe est utilisée dans l’ensemble de `TaskClient` aux valeurs de référence fréquemment utilisée.

```C#
public static class Globals
{
    ...

    // TODO: Replace these five default with your own configuration values
    public static string tenant = "fabrikamb2c.onmicrosoft.com";
    public static string clientId = "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6";
    public static string signInPolicy = "b2c_1_sign_in";
    public static string signUpPolicy = "b2c_1_sign_up";
    public static string editProfilePolicy = "b2c_1_edit_profile";

    ...
}
```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]


### <a name="create-the-publicclientapplication"></a>Créer la PublicClientApplication
La classe principale de MSAL est `PublicClientApplication`. Ce cours représente votre application dans le système Azure AD B2C. Lorsque l’application initialise, créez une instance de `PublicClientApplication` dans `MainWindow.xaml.cs`. Cela peut être utilisé dans l’ensemble de la fenêtre.

```C#
protected async override void OnInitialized(EventArgs e)
{
    base.OnInitialized(e);

    pca = new PublicClientApplication(Globals.clientId)
    {
        // MSAL implements an in-memory cache by default.  Since we want tokens to persist when the user closes the app, 
        // we've extended the MSAL TokenCache and created a simple FileCache in this app.
        UserTokenCache = new FileCache(),
    };
    
    ...
```

### <a name="initiate-a-sign-up-flow"></a>Démarrer un flux d’abonnement
Lorsqu’un utilisateur choisit de signes vers le haut, vous souhaitez démarrer un flux d’abonnement qui utilise la stratégie d’inscription que vous avez créé. À l’aide de MSAL, vous appelez simplement `pca.AcquireTokenAsync(...)`. Les paramètres que vous passez à `AcquireTokenAsync(...)` déterminer quel jeton vous recevez, la stratégie utilisée dans la demande d’authentification et bien plus encore.

```C#
private async void SignUp(object sender, RoutedEventArgs e)
{
    AuthenticationResult result = null;
    try
    {
        // Use the app's clientId here as the scope parameter, indicating that
        // you want a token to the your app's backend web API (represented by
        // the cloud hosted task API).  Use the UiOptions.ForceLogin flag to
        // indicate to MSAL that it should show a sign-up UI no matter what.
        result = await pca.AcquireTokenAsync(new string[] { Globals.clientId },
                string.Empty, UiOptions.ForceLogin, null, null, Globals.authority,
                Globals.signUpPolicy);

        // Upon success, indicate in the app that the user is signed in.
        SignInButton.Visibility = Visibility.Collapsed;
        SignUpButton.Visibility = Visibility.Collapsed;
        EditProfileButton.Visibility = Visibility.Visible;
        SignOutButton.Visibility = Visibility.Visible;

        // When the request completes successfully, you can get user 
        // information from the AuthenticationResult
        UsernameLabel.Content = result.User.Name;

        // After the sign up successfully completes, display the user's To-Do List
        GetTodoList();
    }

    // Handle any exeptions that occurred during execution of the policy.
    catch (MsalException ex)
    {
        if (ex.ErrorCode != "authentication_canceled")
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

### <a name="initiate-a-sign-in-flow"></a>Démarrer un flux de connexion
Vous pouvez démarrer un flux de connexion de la même façon que vous lancez un flux d’inscription. Lorsqu’un utilisateur se connecte, vérifiez le même appel à MSAL, cette fois à l’aide de votre stratégie de connexion :

```C#
private async void SignIn(object sender = null, RoutedEventArgs args = null)
{
    AuthenticationResult result = null;
    try
    {
        result = await pca.AcquireTokenAsync(new string[] { Globals.clientId },
                    string.Empty, UiOptions.ForceLogin, null, null, Globals.authority,
                    Globals.signInPolicy);
        ...
```

### <a name="initiate-an-edit-profile-flow"></a>Démarrer un flux de modifier le profil
Là encore, vous pouvez exécuter une stratégie de modifier le profil de la même manière :

```C#
private async void EditProfile(object sender, RoutedEventArgs e)
{
    AuthenticationResult result = null;
    try
    {
        result = await pca.AcquireTokenAsync(new string[] { Globals.clientId },
                    string.Empty, UiOptions.ForceLogin, null, null, Globals.authority,
                    Globals.editProfilePolicy);
```

Dans tous les cas, MSAL renvoie un jeton dans `AuthenticationResult` ou lève une exception. Chaque fois que vous obtenez un jeton de MSAL, vous pouvez utiliser la `AuthenticationResult.User` objet pour mettre à jour les données utilisateur dans l’application, telles que l’interface utilisateur. Terme ADAL met également en cache le jeton à utiliser dans d’autres parties de l’application.


### <a name="check-for-tokens-on-app-start"></a>Vérifier la présence de jetons au démarrage de l’application
Vous pouvez également utiliser MSAL pour effectuer le suivi des état de connexion à l’utilisateur.  Dans cette application, nous voulons l’utilisateur doit rester connecté une fois qu’ils Fermez l’application et rouvrez-le.  Revenir à l’intérieur de la `OnInitialized` remplacer, utilisez de MSAL `AcquireTokenSilent` mis en cache méthode pour vérifier les jetons :

```C#
AuthenticationResult result = null;
try
{
    // If the user has has a token cached with any policy, we'll display them as signed-in.
    TokenCacheItem tci = pca.UserTokenCache.ReadItems(Globals.clientId).Where(i => i.Scope.Contains(Globals.clientId) && !string.IsNullOrEmpty(i.Policy)).FirstOrDefault();
    string existingPolicy = tci == null ? null : tci.Policy;
    result = await pca.AcquireTokenSilentAsync(new string[] { Globals.clientId }, string.Empty, Globals.authority, existingPolicy, false);

    SignInButton.Visibility = Visibility.Collapsed;
    SignUpButton.Visibility = Visibility.Collapsed;
    EditProfileButton.Visibility = Visibility.Visible;
    SignOutButton.Visibility = Visibility.Visible;
    UsernameLabel.Content = result.User.Name;
    GetTodoList();
}
catch (MsalException ex)
{
    if (ex.ErrorCode == "failed_to_acquire_token_silently")
    {
        // There are no tokens in the cache.  Proceed without calling the To Do list service.
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
```

## <a name="call-the-task-api"></a>Appeler la tâche API
Vous avez maintenant utilisé MSAL pour exécuter des stratégies et d’obtenir les jetons.  Lorsque vous voulez utiliser un ces jetons pour appeler la tâche API, vous pouvez utiliser à nouveau de MSAL `AcquireTokenSilent` mis en cache méthode pour vérifier les jetons :

```C#
private async void GetTodoList()
{
    AuthenticationResult result = null;
    try
    {
        // Here we want to check for a cached token, independent of whatever policy was used to acquire it.
        TokenCacheItem tci = pca.UserTokenCache.ReadItems(Globals.clientId).Where(i => i.Scope.Contains(Globals.clientId) && !string.IsNullOrEmpty(i.Policy)).FirstOrDefault();
        string existingPolicy = tci == null ? null : tci.Policy;

        // Use AcquireTokenSilent to indicate that MSAL should throw an exception if a token cannot be acquired
        result = await pca.AcquireTokenSilentAsync(new string[] { Globals.clientId }, string.Empty, Globals.authority, existingPolicy, false);

    }
    // If a token could not be acquired silently, we'll catch the exception and show the user a message.
    catch (MsalException ex)
    {
        // There is no access token in the cache, so prompt the user to sign-in.
        if (ex.ErrorCode == "failed_to_acquire_token_silently")
        {
            MessageBox.Show("Please sign up or sign in first");
            SignInButton.Visibility = Visibility.Visible;
            SignUpButton.Visibility = Visibility.Visible;
            EditProfileButton.Visibility = Visibility.Collapsed;
            SignOutButton.Visibility = Visibility.Collapsed;
            UsernameLabel.Content = string.Empty;
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
    ...
```

Lors de l’appel vers `AcquireTokenSilentAsync(...)` a réussi et un jeton se trouve dans le cache, vous pouvez ajouter le jeton à la `Authorization` en-tête de la requête HTTP. Le site web de tâche API utilisera cet en-tête pour authentifier la demande pour lire la liste des tâches de l’utilisateur :

```C#
    ...
    // Once the token has been returned by MSAL, add it to the http authorization header, before making the call to access the To Do list service.
    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.Token);

    // Call the To Do list service.
    HttpResponseMessage response = await httpClient.GetAsync(Globals.taskServiceUrl + "/api/tasks");
    ...
```

## <a name="sign-the-user-out"></a>Déconnexion de l’utilisateur
Enfin, vous pouvez utiliser MSAL à la fin d’une session avec l’application lorsque l’utilisateur sélectionne **se déconnecter**.  Lorsque vous utilisez MSAL, cela revient en désactivant tous les jetons à partir du cache de jeton :

```C#
private void SignOut(object sender, RoutedEventArgs e)
{
    // Clear any remnants of the user's session.
    pca.UserTokenCache.Clear(Globals.clientId);

    // This is a helper method that clears browser cookies in the browser control that MSAL uses, it is not part of MSAL.
    ClearCookies();

    // Update the UI to show the user as signed out.
    TaskList.ItemsSource = string.Empty;
    SignInButton.Visibility = Visibility.Visible;
    SignUpButton.Visibility = Visibility.Visible;
    EditProfileButton.Visibility = Visibility.Collapsed;
    SignOutButton.Visibility = Visibility.Collapsed;
    return;
}
```

## <a name="run-the-sample-app"></a>Exécuter l’application d’exemple

Enfin, créer et exécuter l’exemple.  Inscrivez-vous à l’application à l’aide d’un nom d’utilisateur ou une adresse de messagerie. Déconnectez et vous reconnectez en tant que le même utilisateur. Modifier le profil de cet utilisateur. Se déconnecter et s’inscrire à l’aide d’un autre utilisateur.

## <a name="add-social-idps"></a>Ajouter IDPs mise en réseau

Pour l’instant, l’application prend en charge uniquement inscription de l’utilisateur et connexion qui utilisent des **comptes locaux**. Il s’agit des comptes stockés dans votre annuaire B2C qui utilisent un nom d’utilisateur et mot de passe. En utilisant Azure AD B2C, vous pouvez ajouter la prise en charge des autres fournisseurs d’identité (IDPs) sans modifier votre code.

Pour ajouter IDPs sociales dans votre application, commencez en suivant les instructions détaillées dans les articles suivants. Pour chaque IDP que vous souhaitez prendre en charge, vous devez enregistrer une application de ce système et obtenir un ID client.

- [Configurer la Facebook comme un IDP](active-directory-b2c-setup-fb-app.md)
- [Configurer la Google comme un IDP](active-directory-b2c-setup-goog-app.md)
- [Configurer la Amazon comme un IDP](active-directory-b2c-setup-amzn-app.md)
- [Configurer la LinkedIn comme un IDP](active-directory-b2c-setup-li-app.md)

Après avoir ajouté les fournisseurs d’identité à votre répertoire B2C, vous devez modifier chacune de vos stratégies de trois pour inclure les nouvelle IDPs, comme décrit dans l' [article de référence de stratégie](active-directory-b2c-reference-policies.md). Après avoir enregistré votre stratégies, exécutez l’application à nouveau. Vous devriez voir les nouveau IDPs ajoutés en tant que connexion et des expériences d’options d’abonnement dans chacune des votre identité.

Vous pouvez tester les vos stratégies et observer les effets dans votre application exemple. Ajouter ou supprimer IDPs, manipuler revendications application ou modifier les attributs d’abonnement. Expérience jusqu'à ce que vous pouvez voir comment stratégies, les demandes d’authentification et MSAL relier.

Pour référence, l’exemple complet [est fourni sous forme d’un fichier .zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/complete.zip). Vous pouvez également cloner GitHub :

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet.git```
