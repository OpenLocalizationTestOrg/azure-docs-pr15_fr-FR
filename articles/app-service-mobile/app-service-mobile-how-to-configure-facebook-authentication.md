<properties
    pageTitle="Comment configurer l’authentification Facebook pour votre application de Services d’application"
    description="Découvrez comment configurer l’authentification Facebook pour votre application de Services d’application."
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

# <a name="how-to-configure-your-app-service-application-to-use-facebook-login"></a>Comment configurer votre application de Service d’application pour utiliser la connexion Facebook

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Cette rubrique vous montre comment configurer le Service d’application Azure pour utiliser Facebook comme fournisseur d’authentification.

Pour effectuer la procédure décrite dans cette rubrique, vous devez disposer d’un compte Facebook qui comporte une adresse de messagerie vérifié et un numéro de téléphone mobile. Pour créer un nouveau compte Facebook, accédez à [facebook.com].

## <a name="register"> </a>Enregistrer votre application avec Facebook

1. Connectez-vous au [portail Azure]et accédez à votre application. Copiez votre **URL**. Vous allez utiliser cette pour configurer votre application Facebook.

2. Dans une autre fenêtre de navigateur, accédez au site Web de [Facebook développeurs] et connexion avec vos informations d’identification du compte Facebook.

3. (Facultatif) Si vous n’êtes pas déjà inscrit, cliquez sur **applications** > **Enregistrer en tant que développeur**, puis acceptez la stratégie et suivez la procédure d’inscription.

4. Cliquez sur **Mes applications** > **Ajouter une nouvelle application** > **site Web** > **Ignorer et créer des ID de l’application**. 

5. Dans la zone **Nom complet**, tapez un nom unique pour votre application, votre **Messagerie du Contact**, choisissez une **catégorie** pour votre application, puis cliquez sur **Créer des ID de l’application** et terminer la vérification de sécurité. Cela vous permet d’accéder au tableau de bord du développeur pour votre nouvelle application Facebook.

6. Sous « Connexion Facebook », cliquez sur **Mise en route**. Ajouter de votre application **Rediriger URI** **valide OAuth**rediriger URI, puis cliquez sur **Enregistrer les modifications**. 

    > [AZURE.NOTE] Votre URI de redirection est l’URL de votre application ajoutée par le chemin d’accès, _/.auth/login/facebook/callback_. Par exemple, `https://contoso.azurewebsites.net/.auth/login/facebook/callback`. Vérifiez que vous utilisez le schéma HTTPS.

6. Dans le volet de navigation gauche, cliquez sur **paramètres**. Dans le champ **Application Secret** , cliquez sur **Afficher**, fournir votre mot de passe si demandées, puis prenez note des valeurs **d’ID de l’application** et **d’Application Secret**. Vous utilisez ultérieurement pour configurer votre application dans Azure.

    > [AZURE.IMPORTANT] Le code secret application est une information d’identification de sécurité importants. Ne partager ce code secret avec tout le monde ou distribuer dans une application cliente.

7. Le compte Facebook qui a été utilisé pour enregistrer l’application est un administrateur de l’application. À ce stade, seuls les administrateurs peuvent vous connecter à cette application. Pour authentifier d’autres comptes de Facebook, cliquez sur **Application révision** et activer **< your--nom de l’application > rendre publique** activer l’accès public général en utilisant l’authentification Facebook.

## <a name="secrets"> </a>Facebook ajouter des informations à votre application

1. Dans le [portail Azure], accédez à votre application. Cliquez sur **paramètres** > **authentification / autorisation**et vérifiez que **l’Application de Service d’authentification** est **activé**.

2. Cliquez sur **Facebook**, collez les valeurs d’ID de l’application et le code Secret de l’application que vous avez obtenu précédemment, vous pouvez également activer toutes les étendues nécessaires à votre application, puis cliquez sur **OK**.

    ![][0]

    Par défaut, application Service fournit une authentification, mais ne pas restreindre les autorisations d’accès aux votre API et le contenu du site. Vous devez autoriser les utilisateurs dans votre code de l’application.

3. (Facultatif) Pour limiter l’accès à votre site pour seulement les utilisateurs authentifiés par Facebook, définissez **l’Action à effectuer lors de la requête n’est pas authentifié** à **Facebook**. Cela nécessite que toutes les demandes être authentifié, et toutes les demandes non authentifiés sont redirigés vers Facebook pour l’authentification.

4. Lorsque vous avez terminé d’authentification de configuration, cliquez sur **Enregistrer**.

Vous êtes maintenant prêt à utiliser Facebook pour l’authentification dans votre application.

## <a name="related-content"> </a>Contenu associé

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->
[0]: ./media/app-service-mobile-how-to-configure-facebook-authentication/mobile-app-facebook-settings.png

<!-- URLs. -->
[Développeurs Facebook]: http://go.microsoft.com/fwlink/p/?LinkId=268286
[Facebook.com]: http://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/
[Portail Azure]: https://portal.azure.com/
