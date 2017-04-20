<properties
    pageTitle="Comment configurer l’authentification Azure Active Directory pour votre application de Services d’application"
    description="Découvrez comment configurer l’authentification Azure Active Directory pour votre application de Services d’application."
    authors="mattchenderson"
    services="app-service"
    documentationCenter=""
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

# <a name="how-to-configure-your-app-service-application-to-use-azure-active-directory-login"></a>Comment configurer votre application de Service d’application pour utiliser la connexion Azure Active Directory

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Cette rubrique vous montre comment configurer les Services d’application Azure pour utiliser Azure Active Directory comme fournisseur d’authentification.

## <a name="express"> </a>Configurer Azure Active Directory à l’aide de paramètres express

13. Dans le [portail Azure], accédez à votre application. Cliquez sur **paramètres**, puis sur **Authentification/autorisation**.

14. Si l’authentification / fonctionnalité d’autorisation n’est pas activée, définissez le commutateur **on**.

15. Cliquez sur **Azure Active Directory**, puis cliquez sur **Express** sous **Mode d’administration**.

16. Cliquez sur **OK** pour enregistrer l’application Azure Active Directory. Cela créera un nouvel enregistrement. Si vous voulez choisir un enregistrement existant à la place, cliquez sur **Sélectionner une application existante** et recherchez le nom d’un enregistrement créé précédemment au sein de votre client.
Cliquez sur l’enregistrement pour le sélectionner et cliquez sur **OK**. Puis cliquez sur **OK** dans la carte de paramètres Azure Active Directory.

    ![][0]

    Par défaut, application Service fournit une authentification, mais ne pas restreindre les autorisations d’accès aux votre API et le contenu du site. Vous devez autoriser les utilisateurs dans votre code de l’application.

17. (Facultatif) Pour limiter l’accès à votre site pour seulement les utilisateurs authentifiés par Azure Active Directory, définissez **l’Action à effectuer lors de la requête n’est pas authentifié** pour **vous connecter avec Azure Active Directory**. Cela nécessite que toutes les demandes être authentifié, et toutes les demandes non authentifiés sont redirigés vers Azure Active Directory pour l’authentification.

17. Cliquez sur **Enregistrer**.

Vous êtes maintenant prêt à utiliser Azure Active Directory pour l’authentification dans votre application.

## <a name="advanced"> </a>(Autre méthode) configurer manuellement Azure Active Directory avec des paramètres avancés
Vous pouvez également choisir de fournir des paramètres de configuration manuelle. Il s’agit de la meilleure solution si le client AAD que vous souhaitez utiliser est la différent entre le client avec lequel vous vous connecter à Azure. Pour terminer la configuration, vous devez d’abord créer un enregistrement dans Azure Active Directory, et vous devez fournir des informations d’inscription au Service d’application.

### <a name="register"> </a>Enregistrer votre application avec Azure Active Directory

1. Connectez-vous au [portail Azure]et accédez à votre application. Copiez votre **URL**. Vous allez utiliser cette pour configurer votre application Azure Active Directory.

3. Connectez-vous au [portail classique Azure] et accédez à **Active Directory**.

    ![][2]

4. Sélectionnez votre annuaire, puis l’onglet **Applications** en haut. Cliquez sur **Ajouter** en bas pour créer un nouvel enregistrement d’application.

5. Cliquez sur **Ajouter une application de développe de mon organisation**.

6. Dans l’Assistant Ajout de l’Application, entrez un **nom** pour votre application, puis cliquez sur le type de **L’API de Web Application et/ou Web** . Cliquez ensuite sur pour continuer.

7. Dans la zone **URL de session** , collez l’URL de l’application que vous avez copiée précédemment. Entrez ce même URL dans la zone **URI ID de l’application** . Cliquez ensuite sur pour continuer.

8. Une fois que l’application a été ajoutée, cliquez sur l’onglet **configurer** . Modifier l' **URL de réponse** sous **authentification unique** à l’URL de votre application ajoutée par le chemin d’accès, _/.auth/login/aad/callback_. Par exemple, `https://contoso.azurewebsites.net/.auth/login/aad/callback`. Vérifiez que vous utilisez le schéma HTTPS.

    ![][3]

9. Cliquez sur **Enregistrer**. Copiez ensuite l' **ID de Client** de l’application. Vous allez configurer votre application pour utiliser cette version ultérieure.

10. Dans la barre de commandes en bas, cliquez sur les **Points de terminaison de vue**, puis copiez l’URL du **Document de métadonnées de fédération** et télécharger ce document ou y accéder dans un navigateur.

11. Dans l’élément **EntityDescriptor** racine, doit être un attribut **entityID** du formulaire `https://sts.windows.net/` suivi d’un GUID spécifique à votre client (appelé « ID de client »). Copiez cette valeur : il servira de votre **URL de l’émetteur**. Vous allez configurer votre application pour utiliser cette version ultérieure.

### <a name="secrets"> </a>Informations ajouter Azure Active Directory à votre application

13. Dans le [portail Azure], accédez à votre application. Cliquez sur **paramètres**, puis sur **Authentification/autorisation**.

14. Si la fonctionnalité d’authentification / d’autorisation n’est pas activée, activez le commutateur sur **activé**.

15. Cliquez sur **Azure Active Directory**, puis cliquez sur **Avancé** sous **Mode d’administration**. Collez la valeur Réf Client et l’URL de l’émetteur que vous avez obtenu précédemment. Cliquez ensuite sur **OK**.

    ![][1]

    Par défaut, application Service fournit une authentification, mais ne pas restreindre les autorisations d’accès aux votre API et le contenu du site. Vous devez autoriser les utilisateurs dans votre code de l’application.

17. (Facultatif) Pour limiter l’accès à votre site pour seulement les utilisateurs authentifiés par Azure Active Directory, définissez **l’Action à effectuer lors de la requête n’est pas authentifié** pour **vous connecter avec Azure Active Directory**. Cela nécessite que toutes les demandes être authentifié, et toutes les demandes non authentifiés sont redirigés vers Azure Active Directory pour l’authentification.

17. Cliquez sur **Enregistrer**.

Vous êtes maintenant prêt à utiliser Azure Active Directory pour l’authentification dans votre application.

## <a name="optional-configure-a-native-client-application"></a>(Facultatif) Configurer une application cliente natif

Azure Active Directory vous permet également d’enregistrer des clients natifs, qui offre un meilleur contrôle sur les autorisations de mappage. Vous devez ceci si vous souhaitez effectuer des connexions à l’aide d’une bibliothèque, telle que la **Bibliothèque de l’authentification Active Directory**.

1. Accédez à **Active Directory** dans le [portail classique Azure].

2. Sélectionnez votre annuaire, puis l’onglet **Applications** en haut. Cliquez sur **Ajouter** en bas pour créer un nouvel enregistrement d’application.

3. Cliquez sur **Ajouter une application de développe de mon organisation**.

4. Dans l’Assistant Ajout de l’Application, entrez un **nom** pour votre application, puis cliquez sur le type de **l’Application cliente Native** . Cliquez ensuite sur pour continuer.

5. Dans la zone **Rediriger URI** , entrez le point de terminaison de votre site _/.auth/login/done_ , à l’aide du schéma HTTPS. Cette valeur doit être similaire à _https://contoso.azurewebsites.net/.auth/login/done_. Si vous créez une application Windows, utilisez à la place le [package identificateur de sécurité](app-service-mobile-dotnet-how-to-use-client-library.md#package-sid) comme URI.

6. Une fois que l’application native a été ajoutée, cliquez sur l’onglet **configurer** . Rechercher l' **ID de Client** et prenez note de cette valeur.

7. Faire défiler la page vers le bas jusqu'à la section **autorisations à d’autres applications** , puis cliquez sur **Ajouter l’application**.

8. Recherchez l’application web que vous avez enregistré précédemment, puis cliquez sur l’icône plus. Cliquez ensuite sur le contrôle pour fermer la boîte de dialogue. Si l’application web ne peut pas être trouvé, accédez à son enregistrement et ajouter une nouvelle URL de réponse (par exemple, la version HTTP de l’URL de votre en cours), cliquez sur Enregistrer, puis répétez ces étapes : l’application doit figurer dans la liste.

9. Sur la nouvelle entrée que vous venez d’ajouter, ouvrez la liste déroulante **Autorisations déléguée** et sélectionnez **Access (appName)**. Cliquez sur **Enregistrer**.

Vous avez configuré une application cliente native qui peut accéder à votre application de Service d’application.

## <a name="related-content"> </a>Contenu associé

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/mobile-app-aad-express-settings.png
[1]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/mobile-app-aad-advanced-settings.png
[2]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-navigate-aad.png
[3]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-configure.png

<!-- URLs. -->

[Portail Azure]: https://portal.azure.com/
[Portail classique Azure]: https://manage.windowsazure.com/
[alternative method]:#advanced
