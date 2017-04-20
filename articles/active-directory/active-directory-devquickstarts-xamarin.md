<properties
    pageTitle="Azure AD Xamarin mise en route | Microsoft Azure"
    description="Découvrez comment créer une application Xamarin qui s’intègre à Azure AD pour se connecter et appelle Azure AD protégé API utilisant OAuth."
    services="active-directory"
    documentationCenter="xamarin"
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="mobile-xamarin"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="dastrock"/>


# <a name="integrate-azure-ad-into-a-xamarin-app"></a>Intégrer Azure AD dans une application Xamarin

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Xamarin vous permet d’écrire des applications mobiles dans c#, qui peuvent s’exécuter sur iOS, Android et Windows (PC et périphériques mobiles). Si vous créez une application à l’aide de Xamarin, Azure AD rend simple et rapide pour vous authentifier vos utilisateurs avec leur compte Active Directory. Il permet également de votre application consomme en toute sécurité tout web API protégé par Azure AD, tels que les API d’Office 365 ou l’API Azure.

Pour les applications Xamarin qui doivent accéder aux ressources protégées, Azure AD fournit la bibliothèque de l’authentification Active Directory, ou terme ADAL. But du terme ADAL dans vie consiste à faciliter pour votre application obtenir des jetons d’accès. Pour montrer simplement combien il est facile, ici nous allons créer une application « Répertoire recherche » qui :

-   S’exécute sur iOS, Android, ordinateur de bureau de Windows, Windows Phone et Windows Store.
- Utilise une bibliothèque de classes portable unique (PCL) pour authentifier les utilisateurs et obtenir des jetons pour l’API Azure AD Graph
-   Recherche dans un répertoire pour les utilisateurs avec un UPN donné.

Pour créer l’application de travail complète, vous devez :

2. Configurer votre environnement de développement Xamarin
2. Enregistrer votre application avec Azure AD.
3. Installer et configurer le terme ADAL.
5. Terme ADAL permet d’obtenir des jetons à partir d’Azure AD.

Pour démarrer, [télécharger un projet de structure](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/skeleton.zip) ou [Télécharger l’exemple terminé](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/complete.zip). Chacun est une solution Visual Studio 2013. Vous devez également un client Azure AD dans lequel vous pouvez créer des utilisateurs et enregistrer une application. Si vous n’avez pas déjà un client, [Découvrez comment obtenir un](active-directory-howto-tenant.md).

## <a name="0-set-up-your-xamarin-development-environment"></a>*0. configurer votre environnement de développement Xamarin*
Étant donné que ce didacticiel inclut des projets pour iOS, Android et Windows, vous devez Visual Studio et Xamarin ensemble. Pour créer l’environnement nécessaire, suivez les instructions complètes à [configurer et installer pour Visual Studio et Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) sur MSDN. Ces instructions incluent matériel, que vous pouvez consulter pour en savoir plus sur Xamarin pendant que vous patientez pour les programmes d’installation terminer. 

Une fois que vous avez terminé la configuration nécessaire, ouvrez la solution dans Visual Studio pour commencer. Vous trouverez des projets de six : cinq projets spécifique à la plateforme et une bibliothèque de classes portable qui est partagée sur toutes les plateformes`DirectorySearcher.cs`

## <a name="1-register-the-directory-searcher-application"></a>*1. enregistrer l’Application de recherche du répertoire*
Pour activer votre application obtenir des jetons, vous devez tout d’abord l’enregistrer dans votre client Azure AD et lui accorder l’autorisation d’accéder à l’API Azure AD Graph :

-   Se connecter au [portail de gestion Azure](https://manage.windowsazure.com)
-   Dans le volet de navigation gauche, cliquez sur **Active Directory**
-   Sélectionnez un client dans lesquelles vous pouvez enregistrer l’application.
-   Cliquez sur l’onglet **Applications** , puis cliquez sur **Ajouter** dans le bac d’alimentation bas.
-   Suivez les invites et créez une nouvelle **Application cliente Native**.
    -   Le **nom** de l’application décrira votre application aux utilisateurs finaux
    -   **Rediriger Uri** est une combinaison de schéma et chaîne qui utilise Azure AD pour renvoyer les réponses jetons. Entrez une valeur, par exemple, `http://DirectorySearcher`.
-   Une fois que vous avez terminé l’enregistrement, AAD affecter votre application un identificateur unique client. Vous devez cette valeur dans les sections suivantes, donc copier à partir de l’onglet **configurer** .
- Également dans l’onglet **configurer** , recherchez la section « Autorisations pour les autres Applications ». Pour l’application « Azure Active Directory », ajoutez l’autorisation de **l’annuaire de l’organisation de votre accès** sous **Autorisations d’accès délégué**. Cela permettra à votre application à l’API de graphique pour les utilisateurs de la requête.

## <a name="2-install--configure-adal"></a>*2. installer et configurer le terme ADAL*
Maintenant que vous avez une application dans Azure Active Directory, vous pouvez installer terme ADAL et écrivez votre code liées à l’identité. Dans l’ordre de terme ADAL être en mesure de communiquer avec Azure Active Directory, vous devez lui fournir des informations sur l’inscription de votre application.
-   Commencez par ajouter le terme ADAL à chacun des projets dans la solution à l’aide de la Console du Gestionnaire de Package.

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirectorySearcherLib
`

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Android
`

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Desktop
`

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-iOS
`

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Universal
`

- Vous devriez remarquer que deux références de la bibliothèque sont ajoutés à chaque projet - la partie PCL du terme ADAL et une partie spécifique à la plateforme.

-   Dans le projet DirectorySearcherLib, ouvrez `DirectorySearcher.cs`. Modifiez les valeurs de membre de classe pour refléter les valeurs que vous saisissez dans le portail Azure. Votre code font référence ces valeurs chaque fois qu’il utilise le terme ADAL.
    -   La `tenant` est le domaine de votre client Azure AD, par exemple contoso.onmicrosoft.com
    -   La `clientId` est l’identifiant du client de votre application que vous avez copiée à partir du portail.
    - La `returnUri` est la redirectUri que vous avez entré dans le portail, par exemple, `http://DirectorySearcher`.

## <a name="3--use-adal-to-get-tokens-from-aad"></a>*3. Utilisez terme ADAL pour obtenir des jetons de AAD*
*Près* toute logique d’authentification de l’application se situe dans `DirectorySearcher.SearchByAlias(...)`. Tout ce qui est nécessaire dans les projets spécifique à la plateforme consiste à passer un paramètre contextuels pour la `DirectorySearcher` PCL.

- Pour commencer, ouvrez `DirectorySearcher.cs` et ajouter un paramètre à la `SearchByAlias(...)` méthode. `IPlatformParameters`est le paramètre contextuel qui encapsule les objets spécifique à la plate-forme terme ADAL a besoin pour effectuer une authentification.

```C#
public static async Task<List<User>> SearchByAlias(string alias, IPlatformParameters parent)
{
```

-   Ensuite, initialisation la `AuthenticationContext` -ADAL de classe principale. Il s’agit de l’endroit où vous passez terme ADAL les coordonnées nécessaire pour communiquer avec Azure AD. Puis appelez `AcquireTokenAsync(...)`, qui accepte la `IPlatformParameters` objet et appelle le flux d’authentification nécessaire pour renvoyer un jeton à l’application.

```C#
...
    AuthenticationResult authResult = null;
    try
    {
        AuthenticationContext authContext = new AuthenticationContext(authority);
        authResult = await authContext.AcquireTokenAsync(graphResourceUri, clientId, returnUri, parent);
    }
    catch (Exception ee)
    {
        results.Add(new User { error = ee.Message });
        return results;
    }
...
```
- `AcquireTokenAsync(...)`tout d’abord tentera de renvoyer un jeton pour la ressource demandée (l’API de graphique dans le cas présent) sans avertir l’utilisateur à entrer ses informations d’identification (via la mise en cache ou l’actualisation des jetons anciens). Uniquement si nécessaire, il s’affichent l’utilisateur le signe Azure AD dans la page avant d’acquérir le jeton demandé.


- Vous pouvez ensuite joindre le jeton d’accès à la demande de l’API de graphique dans l’en-tête d’autorisation :

```C#
...
    request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", authResult.AccessToken);
...
```

C’est tout pour la `DirectorySearcher` PCL votre application de messagerie du liées à l’identité code.  Ne reste plus qu’à appeler le `SearchByAlias(...)` méthode dans des affichages de chaque plate-forme et où nécessaire ajouter le code permettant de gérer correctement le cycle de vie de l’interface utilisateur.

####<a name="android"></a>Android :
- Dans `MainActivity.cs`, ajoutez un appel à `SearchByAlias(...)` sur le bouton Gestionnaire de clic :

```C#
List<User> results = await DirectorySearcher.SearchByAlias(searchTermText.Text, new PlatformParameters(this));
```
- Vous devez également remplacer la `OnActivityResult` méthode du cycle de vie pour transférer n’importe quelle authentification redirige vers la méthode appropriée.  Terme ADAL fournit une méthode d’assistance pour cela dans Android :

```C#
...
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);
    AuthenticationAgentContinuationHelper.SetAuthenticationAgentContinuationEventArgs(requestCode, resultCode, data);
}
...
```

####<a name="windows-desktop"></a>Ordinateurs de bureau de Windows :
- Dans `MainWindow.xaml.cs`, il vous suffit de passer un appel à `SearchByAlias(...)` en passant un `WindowInteropHelper` sur le bureau `PlatformParameters` objet :

```C#
List<User> results = await DirectorySearcher.SearchByAlias(
  SearchTermText.Text,
  new PlatformParameters(PromptBehavior.Auto, this.Handle));
```

####<a name="ios"></a>iOS :
- Dans `DirSearchClient_iOSViewController.cs`, iOS `PlatformParameters` objet prend simplement une référence à la vue contrôleur :

```C#
List<User> results = await DirectorySearcher.SearchByAlias(
  SearchTermText.Text,
  new PlatformParameters(PromptBehavior.Auto, this.Handle));
```

####<a name="windows-universal"></a>Universel de Windows :
- Dans Windows universel, ouvrez `MainPage.xaml.cs` et mise en œuvre la `Search` méthode, qui utilise une méthode d’assistance dans un projet partagé pour mettre à jour de l’interface utilisateur si nécessaire.

```C#
...
    List<User> results = await DirectorySearcherLib.DirectorySearcher.SearchByAlias(SearchTermText.Text, new PlatformParameters(PromptBehavior.Auto, false));
...
```

Félicitations ! Vous avez terminé l’application Xamarin qui a la possibilité d’authentification des utilisateurs et d’appeler en toute sécurité d’API Web utilisant OAuth 2.0 sur cinq différentes plateformes. Si vous n’avez pas déjà, c’est le temps de remplir votre client avec certains utilisateurs. Exécuter votre application DirectorySearcher, puis connectez-vous à l’aide d’un de ces utilisateurs. Rechercher d’autres utilisateurs en fonction de leur UPN.

Terme ADAL facilite l’incorporation de fonctionnalités d’identité courantes dans votre application. Il s’occupe de tout le travail dirty pour vous - gestion du cache, OAuth protocoles pris en charge, proposez à l’utilisateur avec une connexion de l’interface utilisateur, l’actualisation des jetons qui a expiré et bien plus encore. Tout ce que vous devez vraiment savoir est un seul appel d’API, `authContext.AcquireToken*(…)`.

Pour référence, l’échantillon terminée (sans vos valeurs configuration) est fourni [ici](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/complete.zip). Vous pouvez maintenant déplacer sur aux scénarios d’identité supplémentaire. Vous souhaiterez peut-être essayer :

[Sécuriser un site Web .NET API avec Azure AD >>](active-directory-devquickstarts-webapi-dotnet.md)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]
