<properties
    pageTitle="Ajouter une authentification sur Cordova Apache avec les applications Mobile | Service application Azure"
    description="Découvrez comment utiliser les applications Mobile dans le Service d’application Azure pour authentifier les utilisateurs de votre application Cordova Apache via un grand nombre de fournisseurs d’identité, y compris Google, Facebook, Twitter et Microsoft."
    services="app-service\mobile"
    documentationCenter="javascript"
    authors="adrianhall"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="na"
    ms.tgt_pltfrm="mobile-html"
    ms.devlang="javascript"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="add-authentication-to-your-apache-cordova-app"></a>Ajouter une authentification dans votre application Apache Cordova

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]
    
## <a name="summary"></a>Résumé

Dans ce didacticiel, vous ajoutez l’authentification vers le projet de démarrage rapide de liste des tâches sur Cordova Apache à l’aide d’un fournisseur d’identité pris en charge. Ce didacticiel est basé sur le didacticiel [prise en main applications Mobile] , vous devez effectuer tout d’abord.

##<a name="register"></a>Enregistrer votre application pour l’authentification et configurer le Service d’application

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

[Regardez une vidéo montrant une procédure similaire](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-8-Azure-authentication)

##<a name="permissions"></a>Restreindre les autorisations pour les utilisateurs authentifiés

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

À présent, vous pouvez vérifier que l’accès anonyme à votre serveur principal a été désactivé. Dans Visual Studio, ouvrez le projet que vous avez créé lorsque vous la fin du didacticiel [prise en main applications Mobile], puis exécutez votre application dans l' **Émulateur Android de Google** et vérifiez qu’une erreur de connexion inattendue apparaît après le démarrage de l’application.

Ensuite, vous met à jour l’application pour l’authentification des utilisateurs avant de demander des ressources à partir de l’application Mobile principal.

##<a name="add-authentication"></a>Ajouter une authentification à l’application

1. Ouvrez votre projet dans **Visual Studio**, puis ouvrez la `www/index.html` fichier pour le modifier.

2. Recherchez la `Content-Security-Policy` balise meta dans la section d’en-tête.  Vous devrez ajouter l’hôte OAuth à la liste des sources autorisés.

  	| Fournisseur               | Nom du fournisseur SDK | Hôte OAuth                  |
  	| :--------------------- | :---------------- | :-------------------------- |
  	| Azure Active Directory | AAD               | https://login.Windows.NET   |
  	| Facebook               | Facebook          | https://www.Facebook.com    |
  	| Google                 | Google            | https://Accounts.Google.com |
  	| Microsoft              | MicrosoftAccount  | https://login.Live.com      |
  	| Twitter                | Twitter           | https://API.twitter.com     |

    Voici un exemple du contenu-stratégies de sécurité (implémenté pour Azure Active Directory) :

        <meta http-equiv="Content-Security-Policy" content="default-src 'self'
            data: gap: https://login.windows.net https://yourapp.azurewebsites.net; style-src 'self'">

    Vous devez remplacer `https://login.windows.net` par l’hôte OAuth dans le tableau ci-dessus.  Pour plus d’informations sur cette balise meta, consultez la [documentation de la stratégie de sécurité du contenu] .

    Notez que certains fournisseurs d’authentification n’exigent aucune que stratégie de sécurité de contenu change lorsqu’il est utilisé sur les appareils mobiles appropriées.  Par exemple, aucune modification du contenu de stratégie de sécurité n’est requises lorsque vous utilisez l’authentification Google sur un appareil Android.

3. Ouvrir le `www/js/index.js` pour la modification de fichiers, recherchez la `onDeviceReady()` méthode, et sous la création de client code ajoutez ce qui suit :

        // Login to the service
        client.login('SDK_Provider_Name')
            .then(function () {

                // BEGINNING OF ORIGINAL CODE

                // Create a table reference
                todoItemTable = client.getTable('todoitem');

                // Refresh the todoItems
                refreshDisplay();

                // Wire up the UI Event Handler for the Add Item
                $('#add-item').submit(addItemHandler);
                $('#refresh').on('click', refreshDisplay);

                // END OF ORIGINAL CODE

            }, handleError);

    Notez que ce code remplace le code existant qui crée la référence de table et de l’interface utilisateur.

    La méthode login() commence l’authentification par le fournisseur. La méthode login() est une fonction asynchrone qui retourne une vente JavaScript.  Le reste de l’initialisation est placé à l’intérieur de la réponse de vente qui n’est pas exécutée jusqu'à ce que la méthode login() est terminée.

4. Dans le code que vous venez d’ajouter, remplacez `SDK_Provider_Name` avec le nom de votre fournisseur de connexion. Par exemple, pour Azure Active Directory, utilisez `client.login('aad')`.

4. Exécuter votre projet.  Quand le projet a terminé l’initialisation, votre application affiche la page de connexion OAuth pour le fournisseur d’authentification choisi.

##<a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus [Sur l’authentification] avec le Service d’application Azure.
* Continuez le didacticiel en ajoutant des [Notifications de transmission] dans votre application Apache Cordova.

Découvrez comment utiliser le SDK.

* [Kit de développement logiciel Apache Cordova]
* [ASP.NET Server (SDK)]
* [Node.js Server (SDK)]

<!-- URLs. -->
[Prise en main applications Mobile]: app-service-mobile-cordova-get-started.md
[Documentation de la stratégie de sécurité du contenu]: https://cordova.apache.org/docs/en/latest/guide/appdev/whitelist/index.html
[Notifications de transmission]: app-service-mobile-cordova-get-started-push.md
[À propos de l’authentification]: app-service-mobile-auth.md
[Kit de développement logiciel Apache Cordova]: app-service-mobile-cordova-how-to-use-client-library.md 
[ASP.NET Server (SDK)]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js Server (SDK)]: app-service-mobile-node-backend-how-to-use-server-sdk.md
