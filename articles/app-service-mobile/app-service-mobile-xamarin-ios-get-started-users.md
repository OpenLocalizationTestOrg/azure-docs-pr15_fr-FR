<properties
    pageTitle="Prise en main d’authentification pour les applications mobiles dans iOS Xamarin"
    description="Découvrez comment utiliser les applications mobiles pour authentifier les utilisateurs de votre application iOS Xamarin via un grand nombre de fournisseurs d’identité, y compris AAD, Google, Facebook, Twitter et Microsoft."
    services="app-service\mobile"
    documentationCenter="xamarin"
    authors="adrianhall"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="na"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="add-authentication-to-your-xamarinios-app"></a>Ajouter une authentification dans votre application Xamarin.iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

Cette rubrique vous montre comment authentifier les utilisateurs d’une application application Service Mobile depuis votre application cliente. Dans ce didacticiel, vous ajoutez une authentification au projet de démarrage rapide Xamarin.iOS à l’aide d’un fournisseur d’identité est pris en charge par le Service d’application. Une fois en cours correctement authentifié et autorisé par votre application Mobile, la valeur de l’ID utilisateur s’affiche et vous pourrez plus accéder aux données de la table à accès restreint.

Vous devez effectuer tout d’abord le didacticiel [créer une application Xamarin.iOS]. Si vous n’utilisez pas le projet de serveur de démarrage rapide téléchargé, vous devez ajouter le package d’extension d’authentification à votre projet. Pour plus d’informations sur les packages d’extension de serveur, voir [collaborer avec le Kit de développement pour applications Mobile Azure du serveur principal .NET](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

##<a name="register-your-app-for-authentication-and-configure-app-services"></a>Enregistrer votre application pour l’authentification et configurer les Services d’application

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="restrict-permissions-to-authenticated-users"></a>Restreindre les autorisations pour les utilisateurs authentifiés

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

&nbsp;&nbsp;4. dans Visual Studio ou Xamarin Studio, exécutez le projet client sur un appareil ou un émulateur. Vérifiez qu’une exception non gérée avec un code d’état de 401 (non autorisé) est élevée après le démarrage de l’application. L’échec est connecté à la console du débogueur. Si dans Visual Studio, vous devriez voir l’échec dans la fenêtre de sortie.

&nbsp;&nbsp;Cette erreur non autorisée se produit car l’application tente d’accéder à votre serveur principal application Mobile en tant qu’utilisateur non authentifié. La table *TodoItem* maintenant requiert une authentification.

Ensuite, vous met à jour l’application client pour demander des ressources à partir de la version serveur application Mobile avec un utilisateur authentifié.

##<a name="add-authentication-to-the-app"></a>Ajouter une authentification à l’application

Dans cette section, vous allez modifier l’application pour afficher un écran de connexion avant d’afficher des données. Lorsque l’application démarre, il ne sera pas pas se connecter à votre Service d’application et ne s’affichent pas toutes les données. Une fois que la première fois que l’utilisateur effectue le mouvement d’actualisation, l’écran de connexion s’affichent ; une fois la connexion établie la liste des tâches s’affiche.

1. Dans le projet client, ouvrez le fichier **QSTodoService.cs** et ajoutez les éléments suivants à l’aide de déclaration et `MobileServiceUser` avec accesseur à la classe QSTodoService :

    ```
        using UIKit;
    ```

        // Logged in user
        private MobileServiceUser user;
        public MobileServiceUser User { get { return user; } }

2. Ajouter la nouvelle méthode nommée **authentifier** à **QSTodoService** avec la définition suivante :


        public async Task Authenticate(UIViewController view)
        {
            try
            {
                user = await client.LoginAsync(view, MobileServiceAuthenticationProvider.Facebook);
            }
            catch (Exception ex)
            {
                Console.Error.WriteLine (@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
            }
        }

    >[AZURE.NOTE] Si vous utilisez un fournisseur d’identité autre qu’un Facebook, modifiez la valeur passée à **LoginAsync** au-dessus d’une des opérations suivantes : _MicrosoftAccount_, _Twitter_, _Google_ou _WindowsAzureActiveDirectory_.

3. Ouvrez **QSTodoListViewController.cs**. Modifier la définition de méthode d' **ViewDidLoad** supprimant l’appel à **RefreshAsync()** à la fin :

        public override async void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            todoService = QSTodoService.DefaultService;
           await todoService.InitializeStoreAsync ();

           RefreshControl.ValueChanged += async (sender, e) => {
                await RefreshAsync ();
           }

            // Comment out the call to RefreshAsync
            // await RefreshAsync ();
        }


4. Modifier la méthode **RefreshAsync** s’authentifier si la propriété de **l’utilisateur** est null. Ajoutez le code suivant en haut de la définition de méthode :

        // start of RefreshAsync method
        if (todoService.User == null) {
            await QSTodoService.DefaultService.Authenticate (this);
            if (todoService.User == null) {
                Console.WriteLine ("couldn't login!!");
                return;
            }
        }
        // rest of RefreshAsync method

5. Dans Visual Studio ou Xamarin Studio connecté à votre hôte Xamarin créer sur votre Mac, exécutez le projet client ciblage d’un périphérique ou émulateur. Vérifiez que l’application n’affiche aucune donnée.

    Effectuer le mouvement d’actualisation en tirant vers le bas de la liste d’éléments, qui va entraîner l’écran de connexion doit apparaître. Une fois que vous avez entré les informations d’identification valides, l’application s’affiche la liste des tâches, et vous pouvez mettre à jour les données.


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Créer une application Xamarin.iOS]: app-service-mobile-xamarin-ios-get-started.md
