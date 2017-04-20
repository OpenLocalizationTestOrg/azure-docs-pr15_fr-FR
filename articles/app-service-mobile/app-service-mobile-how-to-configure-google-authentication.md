<properties
    pageTitle="Comment configurer l’authentification Google pour votre application de Services d’application"
    description="Découvrez comment configurer l’authentification Google pour votre application de Services d’application."
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

# <a name="how-to-configure-your-app-service-application-to-use-google-login"></a>Comment configurer votre application de Service d’application pour utiliser la connexion de Google

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Cette rubrique vous montre comment configurer le Service d’application Azure pour utiliser Google comme fournisseur d’authentification.

Pour effectuer la procédure décrite dans cette rubrique, vous devez disposer d’un compte Google qui contient une adresse de messagerie vérifié. Pour créer un nouveau compte Google, accédez à [accounts.google.com](http://go.microsoft.com/fwlink/p/?LinkId=268302).

## <a name="register"> </a>Enregistrer votre application avec Google

1. Connectez-vous au [portail Azure]et accédez à votre application. Copiez votre **URL**, ce qui vous permet d’ultérieurement configurer votre application de Google.

2. Accédez au site Web [API de Google](http://go.microsoft.com/fwlink/p/?LinkId=268303) , connectez-vous à l’aide de vos informations d’identification du compte Google, cliquez sur **Créer un projet**, indiquez un **nom de projet**, puis cliquez sur **créer**.

3. Sous **API de mise en réseau** , cliquez sur **API Google +** , puis sur **Activer**.

4. Dans la navigation à gauche, **les informations d’identification** > **OAuth consentement écran**, puis sélectionnez votre **adresse de messagerie**, entrez un **Nom de produit**, puis cliquez sur **Enregistrer**.

5. Dans l’onglet **informations d’identification** , cliquez sur **créer les informations d’identification** > **ID client OAuth**, puis sélectionnez **application Web**.

6. Collez l' application Service **URL** que vous avez copiée précédemment dans **Autorisés JavaScript origines**, puis collez votre URI de redirection dans **URI de rediriger autorisés**. La redirection URI est l’URL de votre application ajoutée par le chemin d’accès, _/.auth/login/google/callback_. Par exemple, `https://contoso.azurewebsites.net/.auth/login/google/callback`. Vérifiez que vous utilisez le schéma HTTPS. Puis cliquez sur **créer**.

7. Dans l’écran suivant, prenez note des valeurs de l’ID de client et le client secret.


    > [AZURE.IMPORTANT]
    Le code secret client est une information d’identification de sécurité importants. Ne partager ce code secret avec tout le monde ou distribuer dans une application cliente.


## <a name="secrets"> </a>Google ajouter des informations à votre application

8. Dans le [portail Azure], accédez à votre application. Cliquez sur **paramètres**, puis **authentification / autorisation**.

9. Si l’authentification / fonctionnalité d’autorisation n’est pas activée, définissez le commutateur **on**.

10. Cliquez sur **Google**. Coller les valeurs d’ID de l’application et le code Secret de l’application que vous avez obtenu précédemment et vous pouvez également activer des étendues de que votre application a besoin. Cliquez ensuite sur **OK**.

    ![][1]

    Par défaut, application Service fournit une authentification, mais ne pas restreindre les autorisations d’accès aux votre API et le contenu du site. Vous devez autoriser les utilisateurs dans votre code de l’application.

17. (Facultatif) Pour limiter l’accès à votre site pour seulement les utilisateurs authentifiés par Google, définissez **Action à effectuer lors de la requête n’est pas authentifié** sur **Google**. Cela nécessite que toutes les demandes être authentifié, et toutes les demandes non authentifiés sont redirigés vers Google pour l’authentification.

12. Cliquez sur **Enregistrer**.

Vous êtes maintenant prêt à utiliser Google pour l’authentification dans votre application.

## <a name="related-content"> </a>Contenu associé

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]


<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303

[Portail Azure]: https://portal.azure.com/

