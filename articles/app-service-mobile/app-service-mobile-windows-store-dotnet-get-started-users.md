<properties
    pageTitle="Ajouter une authentification dans votre application de plateforme de Windows universel (UWP) | Applications mobiles Azure"
    description="Découvrez comment utiliser Azure Application Service Mobile applications pour authentifier les utilisateurs de votre application universel Windows plateforme (UWP) à l’aide d’une variété de fournisseurs d’identité, y compris : AAD, Google, Facebook, Twitter et Microsoft."
    services="app-service\mobile"
    documentationCenter="windows"
    authors="adrianhall"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="add-authentication-to-your-windows-app"></a>Ajouter une authentification dans votre application Windows

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

Cette rubrique vous montre comment ajouter l’authentification basée sur le cloud pour votre application mobile. Dans ce didacticiel, vous ajoutez l’authentification pour le projet de démarrage rapide universel Windows plateforme (UWP) pour applications Mobile à l’aide d’un fournisseur d’identité est pris en charge par le Service d’application Azure. Une fois en cours authentifié et autorisé par votre principale de l’application Mobile réussie, la valeur de l’ID utilisateur s’affiche.

Ce didacticiel est basé sur le démarrage rapide pour les applications Mobile. Vous devez effectuer tout d’abord le didacticiel [prise en main applications Mobile](app-service-mobile-windows-store-dotnet-get-started.md).

##<a name="register"></a>Enregistrer votre application pour l’authentification et configurer le Service d’application

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>Restreindre les autorisations pour les utilisateurs authentifiés

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

À présent, vous pouvez vérifier que l’accès anonyme à votre serveur principal a été désactivé. Le projet de l’application UWP définir comme projet de démarrage, déploiement et l’exécution de l’application ; Vérifiez qu’une exception non gérée avec un code d’état de 401 (non autorisé) est élevée après le démarrage de l’application. Cela se produit, car l’application tente d’accéder à votre Code de l’application Mobile en tant qu’utilisateur non authentifié, mais la table *TodoItem* maintenant requiert une authentification.

Ensuite, vous met à jour l’application pour l’authentification des utilisateurs avant de demander des ressources de votre application de Service.

##<a name="add-authentication"></a>Ajouter une authentification à l’application

1. Dans la série UWP application fichier MainPage.cs de projet et ajoutez l’extrait de code suivantes à la classe MainPage :
    
        // Define a member variable for storing the signed-in user. 
        private MobileServiceUser user;

        // Define a method that performs the authentication process
        // using a Facebook sign-in. 
        private async System.Threading.Tasks.Task<bool> AuthenticateAsync()
        {
            string message;
            bool success = false;
            try
            {
                // Change 'MobileService' to the name of your MobileServiceClient instance.
                // Sign-in using Facebook authentication.
                user = await App.MobileService
                    .LoginAsync(MobileServiceAuthenticationProvider.Facebook);
                message =
                    string.Format("You are now signed in - {0}", user.UserId);

                success = true;
            }
            catch (InvalidOperationException)
            {
                message = "You must log in. Login Required";
            }

            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
            return success;
        }

    Ce code authentifie l’utilisateur avec une connexion Facebook. Si vous utilisez un fournisseur d’identité autre que Facebook, définissez la valeur de **MobileServiceAuthenticationProvider** au-dessus de la valeur de votre fournisseur.

3. Commenter les ou supprimez l’appel à la méthode **ButtonRefresh_Click** (ou la méthode **InitLocalStoreAsync** ) dans le remplacement de méthode **OnNavigatedTo** existant. Ainsi, les données en cours de chargement avant que l’utilisateur est authentifié. Ensuite, vous allez ajouter un bouton **se connecter** à l’application qui se déclenche d’authentification.

4. Ajouter l’extrait de code suivantes à la classe MainPage :

        private async void ButtonLogin_Click(object sender, RoutedEventArgs e)
        {
            // Login the user and then load data from the mobile app.
            if (await AuthenticateAsync())
            {
                // Switch the buttons and load items from the mobile app.
                ButtonLogin.Visibility = Visibility.Collapsed;
                ButtonSave.Visibility = Visibility.Visible;
                //await InitLocalStoreAsync(); //offline sync support.
                await RefreshTodoItems();
            }
        }
        
5. Ouvrez le fichier de projet MainPage.xaml, recherchez l’élément qui définit le bouton **Enregistrer** et remplacez-le par le code suivant :

        <Button Name="ButtonSave" Visibility="Collapsed" Margin="0,8,8,0" 
                Click="ButtonSave_Click">
            <StackPanel Orientation="Horizontal">
                <SymbolIcon Symbol="Add"/>
                <TextBlock Margin="5">Save</TextBlock>
            </StackPanel>
        </Button>
        <Button Name="ButtonLogin" Visibility="Visible" Margin="0,8,8,0" 
                Click="ButtonLogin_Click" TabIndex="0">
            <StackPanel Orientation="Horizontal">
                <SymbolIcon Symbol="Permissions"/>
                <TextBlock Margin="5">Sign in</TextBlock> 
            </StackPanel>
        </Button>

9. Appuyez sur la touche F5 pour exécuter l’application, cliquez sur le bouton **se connecter** et connectez-vous à l’application auprès de votre fournisseur d’identité choisie. Une fois que votre connexion a réussi, l’application s’exécute sans erreur et vous êtes en mesure de votre serveur principal de la requête et mettre à jour de données.


##<a name="tokens"></a>Stocker le jeton d’authentification sur le client

L’exemple précédent a montré une norme de connexion, qui exige que le client de contacter le fournisseur d’identité et le Service d’application chaque fois que l’application démarre. Non seulement c’est cette méthode inefficace, vous pouvez exécuter dans l’utilisation-se rapporte problèmes doivent de nombreux clients essayez de vous lancer l’application en même temps. Une meilleure approche consiste à mettre en cache le jeton d’autorisation renvoyé par votre Service d’application et essayez d’utiliser cette première avant d’utiliser une connexion basée sur le fournisseur.

>[AZURE.NOTE]Vous pouvez mettre en cache le jeton émis par les Services d’application indépendamment si vous utilisez l’authentification gérés par le client ou gérés par le service. Ce didacticiel utilise gérés par le service d’authentification.

[AZURE.INCLUDE [mobile-windows-universal-dotnet-authenticate-app-with-token](../../includes/mobile-windows-universal-dotnet-authenticate-app-with-token.md)]

##<a name="next-steps"></a>Étapes suivantes

Maintenant que vous terminé ce didacticiel l’authentification de base, vous pouvez passer à un des didacticiels suivants :

+ [Ajouter les notifications push dans votre application](app-service-mobile-windows-store-dotnet-get-started-push.md)  
  Découvrez comment ajouter push notifications prennent en charge dans votre application et configurer la principale application Mobile pour Azure Notification Hubs permet d’envoyer les notifications push.

+ [Activer la synchronisation hors connexion pour votre application](app-service-mobile-windows-store-dotnet-get-started-offline-data.md)  
  Découvrez comment ajouter une prise en charge en mode hors connexion votre application à l’aide d’un serveur principal application Mobile. Synchronisation hors connexion permet aux utilisateurs finaux d’interagir avec une application mobile&mdash;l’affichage, l’ajout ou modification de données&mdash;même lorsqu’il n’y a aucune connexion réseau.


<!-- URLs. -->
[Get started with your mobile app]: app-service-mobile-windows-store-dotnet-get-started.md

