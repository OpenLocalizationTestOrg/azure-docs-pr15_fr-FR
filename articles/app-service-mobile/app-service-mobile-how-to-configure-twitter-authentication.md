<properties
    pageTitle="Comment configurer l’authentification Twitter pour votre application de Services d’application"
    description="Découvrez comment configurer l’authentification Twitter pour votre application de Services d’application."
    services="app-service"
    documentationCenter=""
    authors="mattchenderson"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="mahender"/>

# <a name="how-to-configure-your-app-service-application-to-use-twitter-login"></a>Comment configurer votre application de Service d’application pour utiliser la connexion Twitter

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Cette rubrique vous montre comment configurer le Service d’application Azure pour utiliser Twitter comme fournisseur d’authentification.

Pour effectuer la procédure décrite dans cette rubrique, vous devez disposer d’un compte Twitter qui comporte un messagerie vérifié adresse et numéro de téléphone. Pour créer un nouveau compte Twitter, accédez à <a href="http://go.microsoft.com/fwlink/p/?LinkID=268287" target="_blank">twitter.com</a>.

## <a name="register"> </a>Enregistrer votre application avec Twitter


1. Connectez-vous au [portail Azure]et accédez à votre application. Copiez votre **URL**. Vous allez utiliser cette pour configurer votre application Twitter.

2. Accédez au site Web [Développeurs Twitter] , connectez-vous à l’aide de vos informations d’identification du compte Twitter, puis cliquez sur **Créer une nouvelle application**.

3. Tapez le **nom** et une **Description** pour votre nouvelle application. Collez votre application **URL** pour la valeur de **site Web** . Ensuite, pour l' **URL de rappel**, collez l' **URL de rappel** que vous avez copiée précédemment. Il s’agit de votre passerelle application Mobile ajouté par le chemin d’accès, _/.auth/login/twitter/callback_. Par exemple, `https://contoso.azurewebsites.net/.auth/login/twitter/callback`. Vérifiez que vous utilisez le schéma HTTPS.

3.  Dans le bas de la page, lisez et acceptez les termes du contrat. Puis cliquez sur **créer votre application Twitter**. Cela permet d’enregistrer l’application s’affiche les détails de l’application.

4. Cliquez sur l’onglet **paramètres** , activez **Autoriser cette application à utiliser pour vous connecter avec Twitter**, puis cliquez sur **Paramètres de mise à jour**.

5. Sélectionnez l’onglet **clés et des jetons d’accès** . Prenez note des valeurs de **Consommateur touche (API)** et un **code secret consommateur (API Secret)**.

    > [AZURE.NOTE] Le code secret consommateur est une information d’identification de sécurité importants. Vous ne pouvez partager ce code secret avec tout le monde ou distribuer avec votre application.


## <a name="secrets"> </a>Informations ajouter Twitter à votre application

13. Dans le [portail Azure], accédez à votre application. Cliquez sur **paramètres**, puis **authentification / autorisation**.

14. Si l’authentification / fonctionnalité d’autorisation n’est pas activée, définissez le commutateur **on**.

15. Cliquez sur **Twitter**. Collez le code Secret de l’application et ID d’application valeurs que vous avez obtenu précédemment. Cliquez ensuite sur **OK**.

    ![][1]

    Par défaut, application Service fournit une authentification, mais ne pas restreindre les autorisations d’accès aux votre API et le contenu du site. Vous devez autoriser les utilisateurs dans votre code de l’application.

17. (Facultatif) Pour limiter l’accès à votre site pour seulement les utilisateurs authentifiés par Twitter, définissez **Action à effectuer lors de la requête n’est pas authentifié** sur **Twitter**. Cela nécessite que toutes les demandes être authentifié, et toutes les demandes non authentifiés sont redirigés vers Twitter pour l’authentification.

17. Cliquez sur **Enregistrer**.

Vous êtes maintenant prêt à utiliser Twitter pour l’authentification dans votre application.

## <a name="related-content"> </a>Contenu associé

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]



<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-twitter-authentication/app-service-twitter-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-twitter-authentication/mobile-app-twitter-settings.png

<!-- URLs. -->

[Développeurs Twitter]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[Portail Azure]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-xamarin-ios-get-started-users.md
