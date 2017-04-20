<properties
    pageTitle="Prise en main d’authentification pour les applications mobiles dans Xamarin Android"
    description="Découvrez comment utiliser les applications mobiles pour authentifier les utilisateurs de votre application Xamarin Android via un grand nombre de fournisseurs d’identité, y compris AAD, Google, Facebook, Twitter et Microsoft."
    services="app-service\mobile"
    documentationCenter="xamarin"
    authors="adrianhall"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-android"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="add-authentication-to-your-xamarinandroid-app"></a>Ajouter une authentification dans votre application Xamarin.Android

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

Cette rubrique vous montre comment authentifier les utilisateurs d’une application Mobile depuis votre application cliente. Dans ce didacticiel, vous ajoutez l’authentification au projet démarrage rapide à l’aide d’un fournisseur d’identité est pris en charge par les applications Mobile Azure. Une fois en cours authentifiés et autorisés dans l’application Mobile réussie, la valeur de l’ID utilisateur s’affiche.

Ce didacticiel est basé sur le démarrage rapide de l’application Mobile. Vous devez également terminer le didacticiel [créer une application Xamarin.Android]. Si vous n’utilisez pas le projet de serveur de démarrage rapide téléchargé, vous devez ajouter le package d’extension d’authentification à votre projet. Pour plus d’informations sur les packages d’extension de serveur, voir [collaborer avec le Kit de développement pour applications Mobile Azure du serveur principal .NET](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

##<a name="register"></a>Enregistrer votre application pour l’authentification et configurer les Services d’application

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>Restreindre les autorisations pour les utilisateurs authentifiés

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Dans Visual Studio ou Xamarin Studio, exécutez le projet client sur un appareil ou un émulateur. Vérifiez qu’une exception non gérée avec un code d’état de 401 (non autorisé) est élevée après le démarrage de l’application. Cela se produit car l’application tente d’accéder à votre serveur principal application Mobile en tant qu’utilisateur non authentifié. La table *TodoItem* maintenant requiert une authentification.

Ensuite, vous met à jour l’application client pour demander des ressources à partir de la version serveur application Mobile avec un utilisateur authentifié.

##<a name="add-authentication"></a>Ajouter une authentification à l’application

L’application est mis à jour pour obliger les utilisateurs à appuyez sur le bouton **se connecter** et s’authentifier avant que les données sont affichées.

1. Ajoutez le code suivant à la classe **TodoActivity** :

        // Define a authenticated user.
        private MobileServiceUser user;
        private async Task<bool> Authenticate()
        {
                var success = false;
                try
                {
                    // Sign in with Facebook login using a server-managed flow.
                    user = await client.LoginAsync(this,
                        MobileServiceAuthenticationProvider.Facebook);
                    CreateAndShowDialog(string.Format("you are now logged in - {0}",
                        user.UserId), "Logged in!");

                    success = true;
                }
                catch (Exception ex)
                {
                    CreateAndShowDialog(ex, "Authentication failed");
                }
                return success;
        }

        [Java.Interop.Export()]
        public async void LoginUser(View view)
        {
            // Load data only after authentication succeeds.
            if (await Authenticate())
            {
                //Hide the button after authentication succeeds.
                FindViewById<Button>(Resource.Id.buttonLoginUser).Visibility = ViewStates.Gone;

                // Load the data.
                OnRefreshItemsSelected();
            }
        }

    Cela crée une nouvelle méthode pour vous authentifier un utilisateur et un gestionnaire de méthode pour un nouveau bouton **se connecter** . L’utilisateur dans le code de l’exemple ci-dessus est authentifié à l’aide d’une connexion Facebook. Une boîte de dialogue sert à afficher l’ID d’utilisateur authentifié une seule fois.

    > [AZURE.NOTE] Si vous utilisez un fournisseur d’identité autre que Facebook, modifiez la valeur passée à **LoginAsync** au-dessus d’une des opérations suivantes : _MicrosoftAccount_, _Twitter_, _Google_ou _WindowsAzureActiveDirectory_.

3. Dans la méthode **OnCreate** , supprimer ou commenter les la ligne de code suivante :

        OnRefreshItemsSelected ();

4. Dans le fichier Activity_To_Do.axml, ajoutez la définition d’un bouton *LoginUser* suivante avant le bouton *AddItem* existant :

        <Button
            android:id="@+id/buttonLoginUser"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:onClick="LoginUser"
            android:text="@string/login_button_text" />

5. Ajoutez l’élément suivant dans le fichier de ressources Strings.xml :

        <string name="login_button_text">Sign in</string>

6. Dans Visual Studio ou Xamarin Studio, exécutez le projet client sur un appareil ou un émulateur et connectez-vous avec votre fournisseur d’identité choisie.

    Lorsque vous êtes connecté en avec succès, l’application affichera votre ID de connexion et la liste des tâches, et vous pouvez mettre à jour les données.


<!-- URLs. -->
[Créer une application Xamarin.Android]: app-service-mobile-xamarin-android-get-started.md
