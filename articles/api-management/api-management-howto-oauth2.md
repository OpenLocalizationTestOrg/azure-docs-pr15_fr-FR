<properties 
    pageTitle="Comment autoriser des comptes pour les développeurs à l’aide de OAuth 2.0 Gestion des API Azure" 
    description="Découvrez comment autoriser les utilisateurs à l’aide de OAuth 2.0 Gestion des API." 
    services="api-management" 
    documentationCenter="" 
    authors="steved0x" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="sdanie"/>

# <a name="how-to-authorize-developer-accounts-using-oauth-20-in-azure-api-management"></a>Comment autoriser des comptes pour les développeurs à l’aide de OAuth 2.0 Gestion des API Azure

De nombreuses API prennent en charge [OAuth 2.0](http://oauth.net/2/) pour sécuriser l’API et de vous assurer que seuls les utilisateurs autorisés ont accès, et ils peuvent accéder uniquement à laquelle elles Tirez parti de ressources. Pour pouvoir utiliser Console développeur interactive de la direction de l’API Azure avec ces API, le service permet de vous permettent de configurer votre instance du service pour travailler avec votre OAuth 2.0 API activé.

## <a name="prerequisites"> </a>Conditions préalables

Ce guide vous montre comment configurer votre instance de service de gestion de l’API pour utiliser l’autorisation OAuth 2.0 pour les comptes pour les développeurs, mais ne vous affiche la configuration d’un fournisseur OAuth 2.0. La configuration pour chaque fournisseur OAuth 2.0 est différente, bien que les étapes sont similaires, et les éléments des informations utilisées pour configurer OAuth 2.0 dans votre instance de service de gestion de l’API sont identiques. Cette rubrique présente des exemples à l’aide d’Azure Active Directory comme un fournisseur de services 2.0 OAuth.

>[AZURE.NOTE] Pour plus d’informations sur la configuration 2.0 OAuth à l’aide d’Azure Active Directory, consultez l’exemple [WebApp-GraphAPI-DotNet][] .

## <a name="step1"> </a>Configurer un serveur d’autorisation OAuth 2.0 la gestion des API

Pour commencer, cliquez sur **Gérer** dans le portail classique Azure pour votre service de gestion de l’API. Cela vous permet d’accéder au portail de gestion de l’API de publisher.

![Portail de Publisher][api-management-management-console]

>[AZURE.NOTE] Si vous n’avez pas encore créé une instance de service de gestion des API, voir [créer une instance de service de gestion de l’API][] dans le didacticiel [mise en route de la gestion des API Azure][] .

Cliquez sur **sécurité** dans le menu de **Gestion de l’API** gauche et cliquez sur **2.0 OAuth**, puis cliquez sur **Ajouter un serveur d’autorisation**.

![OAuth 2.0][api-management-oauth2]

Après avoir cliqué sur **Ajouter un serveur d’autorisation**, le nouveau formulaire de serveur d’autorisation s’affiche.

![Nouveau serveur][api-management-oauth2-server-1]

Entrez un nom et une description facultative dans les champs **nom** et une **Description** . 

>[AZURE.NOTE] Ces champs sont utilisés pour identifier le serveur de l’autorisation OAuth 2.0 au sein de l’instance actuelle du service Gestion des API et leurs valeurs ne proviennent pas le serveur OAuth 2.0.

Entrez l' **URL de la page d’inscription Client**. Cette page est l’endroit où les utilisateurs peuvent créer et gérer leurs comptes et varie selon le fournisseur OAuth 2.0 utilisé. L' **URL de la page d’inscription Client** pointe vers la page à laquelle les utilisateurs peuvent utiliser pour créer et configurer leurs propres comptes pour les fournisseurs de OAuth 2.0 qui prennent en charge de la gestion des utilisateurs de comptes. Certaines organisations ne configurer ou utiliser cette fonctionnalité, même si le fournisseur OAuth 2.0 prend en charge. Si votre fournisseur OAuth 2.0 n’a pas de gestion des utilisateurs de comptes configurés, entrez l’espace réservé URL ici tels que l’URL de votre société ou une URL tel que `https://placeholder.contoso.com`.

La section suivante de l’écran contient les paramètres de **code d’autorisation accorder types**, **URL de point de terminaison d’autorisation**et **méthode de demande d’autorisation** .

![Nouveau serveur][api-management-oauth2-server-2]

Spécifiez **code d’autorisation accorder types** en cochant les types de votre choix. **Code d’autorisation** est indiqué par défaut.

Entrez l' **URL du point de terminaison d’autorisation**. Pour Azure Active Directory, cette URL est similaire à l’URL suivante, où `<client_id>` est remplacée par l’id de client qui identifie votre application sur le serveur 2.0 OAuth.

    https://login.windows.net/<client_id>/oauth2/authorize

La **méthode de demande d’autorisation** indique comment la demande d’autorisation est envoyée au serveur OAuth 2.0. **Obtenir** est sélectionné par défaut.

La section suivante est l’endroit où **l’URL du point de terminaison jeton**, **méthodes d’authentification Client**, **jeton d’accès procédé permettant d’envoyer**et **étendue par défaut** sont spécifiés.

![Nouveau serveur][api-management-oauth2-server-3]

Pour un serveur Azure Active Directory OAuth 2.0, l' **URL du point de terminaison des jetons** a au format suivant, où `<APPID>` comporte la mise en forme de `yourapp.onmicrosoft.com`.

    https://login.windows.net/<APPID>/oauth2/token

Le paramètre par défaut pour les **méthodes d’authentification Client** est **simple**et **jeton d’accès procédé permettant d’envoyer** **l’en-tête d’autorisation**. Ces valeurs sont configurés sur cette section du formulaire, ainsi que l' **étendue par défaut**.

La section **informations d’identification Client** contient **l’ID de Client** et le **secret Client**, qui sont obtenus au cours du processus de création et la configuration de votre serveur OAuth 2.0. Une fois que **l’ID de Client** et le **Client secret** sont spécifiés, la **redirect_uri** pour le **code d’autorisation** est générée. Cet URI est utilisé pour configurer l’URL de réponse dans votre configuration serveur OAuth 2.0.

![Nouveau serveur][api-management-oauth2-server-4]

Si le **code d’autorisation accorder types** est défini sur **mot de passe de propriétaire de la ressource**, la section **informations d’identification de mot de passe de propriétaire de la ressource** est utilisée pour spécifier les informations d’identification ; Sinon, vous pouvez laisser vide.

![Nouveau serveur][api-management-oauth2-server-5]

Une fois que le formulaire est terminé, cliquez sur **Enregistrer** pour enregistrer la configuration du serveur API Management OAuth 2.0 d’autorisation. Une fois la configuration du serveur est enregistrée, vous pouvez configurer API pour utiliser cette configuration, comme indiqué dans la section suivante.

## <a name="step2"> </a>Configurer une API pour utiliser l’autorisation utilisateur OAuth 2.0

Cliquez sur **API** dans le menu de **Gestion de l’API** gauche cliquez sur le nom de l’API de votre choix et cliquez sur **sécurité**, puis cochez la case pour **OAuth 2.0**.

![Autorisation de l’utilisateur][api-management-user-authorization]

Sélectionnez le **serveur d’autorisation** souhaité dans la liste déroulante, puis cliquez sur **Enregistrer**.

![Autorisation de l’utilisateur][api-management-user-authorization-save]

## <a name="step3"> </a>Tester l’autorisation utilisateur OAuth 2.0 dans le portail de développement

Une fois que vous avez configuré votre serveur de l’autorisation OAuth 2.0 et configuré votre API pour utiliser ce serveur, vous pouvez le tester en accédant au portail développeur et appel d’une API.  Dans le menu supérieur droit, cliquez sur **portail pour les développeurs** .

![Portail pour les développeurs][api-management-developer-portal-menu]

Cliquez sur **API** dans le menu supérieur et sélectionnez **API l’écho**.

![API écho][api-management-apis-echo-api]

>[AZURE.NOTE] Si vous n'avez qu’une seule API configurée ou visible à votre compte, puis en cliquant sur API vous permet d’accéder directement aux opérations de cette API.

Sélectionnez l’opération **Obtenir une ressource** et cliquez sur **Console ouverte**, puis sélectionnez le **code d’autorisation** dans le menu déroulant.

![Ouvrir la console][api-management-open-console]

Lorsque le **code d’autorisation** est sélectionnée, une fenêtre contextuelle s’affiche avec le formulaire de connexion du fournisseur OAuth 2.0. Dans cet exemple, l’écran de connexion est fourni par Azure Active Directory.

>[AZURE.NOTE] Si vous avez désactivé fenêtres pop-up vous demandera afin de les activer par le navigateur. Après avoir activé les, sélectionnez un **code d’autorisation** à nouveau et le formulaire de connexion seront affiche.

![Connexion][api-management-oauth2-signin]

Une fois que vous avez connecté, les **en-têtes de demande** sont remplis avec un `Authorization : Bearer` en-tête qui autorise la demande.

![Demander jeton d’en-tête][api-management-request-header-token]

À ce stade vous pouvez configurer les valeurs souhaitées pour les autres paramètres et envoyez la demande. 

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’utilisation de OAuth 2.0 et la gestion de l’API, voir la vidéo suivante et [l’article](api-management-howto-protect-backend-with-aad.md)d’accompagnement.

> [AZURE.VIDEO protecting-web-api-backend-with-azure-active-directory-and-api-management]

[api-management-management-console]: ./media/api-management-howto-oauth2/api-management-management-console.png
[api-management-oauth2]: ./media/api-management-howto-oauth2/api-management-oauth2.png
[api-management-user-authorization]: ./media/api-management-howto-oauth2/api-management-user-authorization.png
[api-management-user-authorization-save]: ./media/api-management-howto-oauth2/api-management-user-authorization-save.png
[api-management-oauth2-signin]: ./media/api-management-howto-oauth2/api-management-oauth2-signin.png
[api-management-request-header-token]: ./media/api-management-howto-oauth2/api-management-request-header-token.png
[api-management-developer-portal-menu]: ./media/api-management-howto-oauth2/api-management-developer-portal-menu.png
[api-management-open-console]: ./media/api-management-howto-oauth2/api-management-open-console.png
[api-management-oauth2-server-1]: ./media/api-management-howto-oauth2/api-management-oauth2-server-1.png
[api-management-oauth2-server-2]: ./media/api-management-howto-oauth2/api-management-oauth2-server-2.png
[api-management-oauth2-server-3]: ./media/api-management-howto-oauth2/api-management-oauth2-server-3.png
[api-management-oauth2-server-4]: ./media/api-management-howto-oauth2/api-management-oauth2-server-4.png
[api-management-oauth2-server-5]: ./media/api-management-howto-oauth2/api-management-oauth2-server-5.png
[api-management-apis-echo-api]: ./media/api-management-howto-oauth2/api-management-apis-echo-api.png


[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Prise en main avec la gestion des API Azure]: api-management-get-started.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Créer une instance de service de gestion de l’API]: api-management-get-started.md#create-service-instance

[http://oauth.net/2/]: http://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

