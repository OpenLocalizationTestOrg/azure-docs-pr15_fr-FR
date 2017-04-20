<properties
    pageTitle="Comment configurer l’authentification Account Microsoft pour votre application de Services d’application"
    description="Découvrez comment configurer l’authentification Account Microsoft pour votre application de Services d’application."
    authors="mattchenderson"
    services="app-service"
    documentationCenter=""
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="mahender"/>

# <a name="how-to-configure-your-app-service-application-to-use-microsoft-account-login"></a>Comment configurer votre application de Service d’application à utiliser la connexion Account Microsoft

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Cette rubrique vous montre comment configurer le Service d’application Azure pour utiliser Account Microsoft comme fournisseur d’authentification. 

## <a name="register-microsoft-account"> </a>Enregistrer votre application avec un compte Microsoft

1. Connectez-vous au [portail Azure]et accédez à votre application. Copiez votre **URL**, laquelle plus tard vous permet de configurer votre application avec Microsoft Account.

2. Accédez à la page [Mes Applications] dans Microsoft Account Developer Center et ouvrez une session avec votre compte Microsoft, le cas échéant.

3. Cliquez sur **Ajouter une application**, puis tapez un nom de l’application, puis cliquez sur **créer une application**.

4. Prenez note de l' **ID de l’Application**, car vous en aurez besoin ultérieurement. 

5. Sous « Plateformes », cliquez sur **Ajouter la plateforme** et sélectionnez « Web ».

6. Sous « Rediriger URI » fournir le point de terminaison pour votre application, puis cliquez sur **Enregistrer**. 
 
    >[AZURE.NOTE]Votre URI de redirection est l’URL de votre application ajoutée par le chemin d’accès, _/.auth/login/microsoftaccount/callback_. Par exemple, `https://contoso.azurewebsites.net/.auth/login/microsoftaccount/callback`.   
    >Vérifiez que vous utilisez le schéma HTTPS.

7. Sous « Application Secrets », cliquez sur **Générer un nouveau mot de passe**. Prenez note de la valeur qui s’affiche. Une fois que vous avez quitté la page, il affichera pas à nouveau.


    > [AZURE.IMPORTANT] Le mot de passe est une information d’identification de sécurité importants. Vous ne pouvez partager le mot de passe avec tout le monde ou distribuer dans une application cliente.

## <a name="secrets"> </a>Informations ajouter un compte Microsoft pour votre application de Service d’application

1. Revenez dans le [portail Azure], accédez à votre application, cliquez sur **paramètres** > **authentification / autorisation**.

2. Si l’authentification / fonctionnalité d’autorisation n’est pas activée, sa mise **sous**tension.

3. Cliquez sur **Le compte Microsoft**. Coller les valeurs d’ID de l’Application et mot de passe que vous avez obtenu précédemment et vous pouvez également activer des étendues de que votre application a besoin. Cliquez ensuite sur **OK**.

    ![][1]

    Par défaut, application Service fournit une authentification, mais ne pas restreindre les autorisations d’accès aux votre API et le contenu du site. Vous devez autoriser les utilisateurs dans votre code de l’application.

4. (Facultatif) Pour limiter l’accès à votre site pour seulement les utilisateurs authentifiés par compte Microsoft, définissez **l’Action à effectuer lors de la requête n’est pas authentifié** au **Compte Microsoft**. Cela nécessite que toutes les demandes être authentifié, et toutes les demandes non authentifiés sont redirigés vers compte Microsoft pour l’authentification.

5. Cliquez sur **Enregistrer**.

Vous êtes maintenant prêt à utiliser Microsoft Account pour l’authentification dans votre application.

## <a name="related-content"> </a>Contenu associé

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]


<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/app-service-microsoftaccount-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/mobile-app-microsoftaccount-settings.png

<!-- URLs. -->

[Mes Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Portail Azure]: https://portal.azure.com/
