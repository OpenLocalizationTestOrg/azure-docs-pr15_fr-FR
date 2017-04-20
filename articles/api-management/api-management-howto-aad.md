<properties 
    pageTitle="Comment autoriser des comptes pour les développeurs à l’aide de Azure Active Directory gestion des API Azure" 
    description="Découvrez comment autoriser les utilisateurs à l’aide d’Azure Active Directory gestion des API." 
    services="api-management" 
    documentationCenter="API Management" 
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

# <a name="how-to-authorize-developer-accounts-using-azure-active-directory-in-azure-api-management"></a>Comment autoriser des comptes pour les développeurs à l’aide de Azure Active Directory gestion des API Azure


## <a name="overview"></a>Vue d’ensemble
Ce guide vous montre comment activer l’accès au portail développeur pour tous les utilisateurs dans un ou plusieurs répertoires Azure Active. Ce guide vous montre comment gérer les groupes d’utilisateurs Azure Active Directory en ajoutant des groupes externes qui contiennent les utilisateurs d’Azure Active Directory.

>Pour effectuer les étapes décrites dans ce guide, vous devez tout d’abord un Azure Active Directory dans lequel vous souhaitez créer une application.

## <a name="how-to-authorize-developer-accounts-using-azure-active-directory"></a>Comment autoriser des comptes pour les développeurs à l’aide de Azure Active Directory

Pour commencer, cliquez sur **Gérer** dans le portail classique Azure pour votre service de gestion de l’API. Cela vous permet d’accéder au portail de gestion de l’API de publisher.

![Portail de Publisher][api-management-management-console]

>Si vous n’avez pas encore créé une instance de service de gestion des API, voir [créer une instance de service de gestion de l’API][] dans le didacticiel [mise en route de la gestion des API Azure][] .

Cliquez sur **sécurité** dans le menu de **Gestion de l’API** gauche et **Identités externes**.

![Identités externes][api-management-security-external-identities]

Cliquez sur **Azure Active Directory**. Prenez note de **l’URL de redirection** et le commutateur à Azure Active Directory dans le portail classique Azure.

![Identités externes][api-management-security-aad-new]

Cliquez sur le bouton **Ajouter** pour créer une nouvelle application Azure Active Directory, puis sélectionnez **Ajouter une application de développe de mon organisation**.

![Ajouter une nouvelle application Azure Active Directory][api-management-new-aad-application-menu]

Entrez un nom pour l’application, sélectionnez **application Web et/ou API Web**, puis cliquez sur le bouton suivant.

![Nouvelle application Azure Active Directory][api-management-new-aad-application-1]

Pour l' **authentification URL**, entrez l’URL authentification de votre portail développeur. Dans cet exemple, l' **authentification URL** est `https://aad03.portal.current.int-azure-api.net/signin`. 

Pour l' **Application ID URL**, entrez le domaine par défaut ou un domaine personnalisé pour Azure Active Directory, puis ajouter une chaîne unique. Dans cet exemple, le domaine par défaut de **https://contoso5api.onmicrosoft.com** est utilisé avec le suffixe **/api/API** spécifié.

![De nouvelles propriétés d’application Azure Active Directory][api-management-new-aad-application-2]

Cliquez sur le bouton de vérification pour enregistrer et créer la nouvelle application et accédez à l’onglet **configurer** pour configurer la nouvelle application.

![Créé d’application Azure Active Directory][api-management-new-aad-app-created]

Si plusieurs annuaires Active Directory Azure vont être utilisées pour cette application, cliquez sur **Oui** pour **l’Application est partagée**. La valeur par défaut est **Aucun**.

![Application est cliente multiples][api-management-aad-app-multi-tenant]

Copiez l' **URL de redirection** dans la section **Azure Active Directory** de l’onglet **Identités externe** dans le portail publisher et collez-le dans la zone de texte **URL de réponse** . 

![URL de réponse][api-management-aad-reply-url]

Faites défiler vers le bas de l’onglet Configurer, sélectionnez la liste déroulante **Autorisations des applications** et sélectionnez **lecture répertoire de données**.

![Autorisations des applications][api-management-aad-app-permissions]

Sélectionnez le menu déroulant **Autorisations accordées aux délégués** et cochez **Activer l’authentification et lire des profils des utilisateurs**.

![Autorisations déléguées][api-management-aad-delegated-permissions]

>Pour plus d’informations sur l’application et les autorisations déléguées, voir [accéder à l’API de graphique][].

Copier l' **Id de Client** dans le Presse-papiers.

![Id de client][api-management-aad-app-client-id]

Revenez au portail publisher et collez-le dans l' **Id de Client** copiée à partir de la configuration d’application Azure Active Directory.

![Id de client][api-management-client-id]

Revenez à la configuration Azure Active Directory et cliquez sur le **Sélectionnez durée** liste déroulante dans la section **clés** et spécifiez un intervalle. Dans cet exemple, **1 an** est utilisé.

![Clé][api-management-aad-key-before-save]

Cliquez sur **Enregistrer** pour enregistrer la configuration et afficher la clé. Copiez la clé dans le Presse-papiers.

>Prenez note de cette touche. Une fois que vous fermez la fenêtre de configuration Azure Active Directory, la clé ne peut pas être affichée à nouveau.

![Clé][api-management-aad-key-after-save]

Activez le portail publisher, puis collez la clé dans la zone de texte **Secret Client** .

![Secret client][api-management-client-secret]

**Clients autorisés** spécifie les répertoires qui ont accès à l’API de l’instance de service de gestion de l’API. Spécifiez les domaines des instances Azure Active Directory auquel vous voulez accorder l’accès. Vous pouvez séparer plusieurs domaines avec les sauts de ligne, espaces ou des virgules.

![Clients autorisés][api-management-client-allowed-tenants]

Plusieurs domaines peuvent être indiquées dans la section **Clients autorisés** . Avant de tous les utilisateurs peuvent se connecter à partir d’un domaine autre que celui d’origine où l’application a été enregistrée, un administrateur global du domaine autre doit accorder une autorisation de l’application pour accéder aux données de répertoire. Pour accorder une autorisation, un administrateur global doit se connecter à l’application et cliquez sur **Accepter**. Dans l’exemple suivant `miaoaad.onmicrosoft.com` a été ajouté au **Clients autorisés** et global administrateur à partir de ce domaine se connecte pour la première fois.

![Autorisations][api-management-permissions-form]

>Si un administrateur non global tente de se connecter avant que les autorisations sont accordées par un administrateur global, d’échec de la tentative de connexion et un écran d’erreur s’affiche.

Une fois la configuration souhaitée est spécifiée, cliquez sur **Enregistrer**.

![Enregistrer][api-management-client-allowed-tenants-save]

Une fois que les modifications sont enregistrées, les utilisateurs de Azure Active Directory spécifié peuvent vous connecter au portail de développement en suivant les étapes de [se connecter au portail développeur à l’aide d’un compte Azure Active Directory][].

## <a name="how-to-add-an-external-azure-active-directory-group"></a>Comment ajouter un externes groupe Azure Active Directory

Après avoir activé l’accès des utilisateurs dans un Azure Active Directory, vous pouvez ajouter des groupes Azure Active Directory dans la gestion de l’API pour gérer plus facilement l’association des développeurs dans le groupe avec les produits souhaités.

> Pour configurer un groupe Azure Active Directory externe, Azure Active Directory doit d’abord être configuré dans l’onglet identités en suivant la procédure décrite dans la section précédente. 

Groupes Azure Active Directory externes sont ajoutés à partir de l’onglet **visibilité** du produit pour lequel vous souhaitez accorder l’accès au groupe. Cliquez sur **produits**, puis cliquez sur le nom du produit souhaité.

![Configurer produit][api-management-configure-product]

Basculer vers l’onglet **visibilité** , puis cliquez sur **Ajouter des groupes à partir d’Azure Active Directory**.

![Ajouter des groupes][api-management-add-groups]

Sélectionnez le **Azure Active Directory client** dans la liste déroulante et tapez le nom du groupe souhaité dans les **groupes** à ajouter zone de texte.

![Sélectionner un groupe][api-management-select-group]

Ce nom de groupe sont accessibles dans la liste **groupes** pour votre Azure Active Directory, comme illustré dans l’exemple suivant.

![Liste de groupes Azure Active Directory][api-management-aad-groups-list]

Cliquez sur **Ajouter** pour valider le nom du groupe et ajoutez le groupe. Dans cet exemple de **Contoso 5 développeurs** groupe externe est ajouté. 

![Groupe ajouté][api-management-aad-group-added]

Cliquez sur **Enregistrer** pour enregistrer la sélection du nouveau groupe.

Une fois qu’un groupe Azure Azure Active Directory a été configuré à partir d’un produit, il est disponible pour être activée sous l’onglet **visibilité** pour les autres produits dans l’instance de service de gestion de l’API.

Pour consulter et configurer les propriétés pour les groupes externes une fois qu’ils ont été ajoutés, cliquez sur le nom du groupe à partir de l’onglet **groupes** .

![Gérer les groupes][api-management-groups]

À partir de là, vous pouvez modifier le **nom** et la **Description** du groupe.

![Modifier le groupe][api-management-edit-group]

Les utilisateurs à partir d’Azure Active Directory configuré peuvent se connecter dans le portail pour les développeurs et affichage et s’abonner à tous les groupes pour lesquels ils disposent d’une visibilité en suivant les instructions dans la section suivante.

## <a name="how-to-log-in-to-the-developer-portal-using-an-azure-active-directory-account"></a>Comment se connecter au portail développeur à l’aide d’un compte Azure Active Directory

Pour vous connecter au portail développeur à l’aide d’un compte Azure Active Directory configuré dans les sections précédentes, ouvrir une nouvelle fenêtre de navigateur à l’aide de l' **authentification URL** à partir de la configuration d’application Active Directory, puis cliquez sur **Azure Active Directory**.

![Portail pour les développeurs][api-management-dev-portal-signin]

Entrez les informations d’identification d’un des utilisateurs dans votre Azure Active Directory, puis cliquez sur **se connecter**.

![Connexion][api-management-aad-signin]

Vous pouvez être invité avec un formulaire d’inscription si toute information supplémentaire est requise. Complétez le formulaire d’enregistrement, puis cliquez sur **s’inscrire**.

![Enregistrement][api-management-complete-registration]

Votre utilisateur est maintenant connecté au portail développeur pour votre instance de service de gestion de l’API.

![Enregistrement terminé][api-management-registration-complete]



[api-management-management-console]: ./media/api-management-howto-aad/api-management-management-console.png
[api-management-security-external-identities]: ./media/api-management-howto-aad/api-management-security-external-identities.png
[api-management-security-aad-new]: ./media/api-management-howto-aad/api-management-security-aad-new.png
[api-management-new-aad-application-menu]: ./media/api-management-howto-aad/api-management-new-aad-application-menu.png
[api-management-new-aad-application-1]: ./media/api-management-howto-aad/api-management-new-aad-application-1.png
[api-management-new-aad-application-2]: ./media/api-management-howto-aad/api-management-new-aad-application-2.png
[api-management-new-aad-app-created]: ./media/api-management-howto-aad/api-management-new-aad-app-created.png
[api-management-aad-app-permissions]: ./media/api-management-howto-aad/api-management-aad-app-permissions.png
[api-management-aad-app-client-id]: ./media/api-management-howto-aad/api-management-aad-app-client-id.png
[api-management-client-id]: ./media/api-management-howto-aad/api-management-client-id.png
[api-management-aad-key-before-save]: ./media/api-management-howto-aad/api-management-aad-key-before-save.png
[api-management-aad-key-after-save]: ./media/api-management-howto-aad/api-management-aad-key-after-save.png
[api-management-client-secret]: ./media/api-management-howto-aad/api-management-client-secret.png
[api-management-client-allowed-tenants]: ./media/api-management-howto-aad/api-management-client-allowed-tenants.png
[api-management-client-allowed-tenants-save]: ./media/api-management-howto-aad/api-management-client-allowed-tenants-save.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-aad/api-management-aad-delegated-permissions.png
[api-management-dev-portal-signin]: ./media/api-management-howto-aad/api-management-dev-portal-signin.png
[api-management-aad-signin]: ./media/api-management-howto-aad/api-management-aad-signin.png
[api-management-complete-registration]: ./media/api-management-howto-aad/api-management-complete-registration.png
[api-management-registration-complete]: ./media/api-management-howto-aad/api-management-registration-complete.png
[api-management-aad-app-multi-tenant]: ./media/api-management-howto-aad/api-management-aad-app-multi-tenant.png
[api-management-aad-reply-url]: ./media/api-management-howto-aad/api-management-aad-reply-url.png
[api-management-permissions-form]: ./media/api-management-howto-aad/api-management-permissions-form.png
[api-management-configure-product]: ./media/api-management-howto-aad/api-management-configure-product.png
[api-management-add-groups]: ./media/api-management-howto-aad/api-management-add-groups.png
[api-management-select-group]: ./media/api-management-howto-aad/api-management-select-group.png
[api-management-aad-groups-list]: ./media/api-management-howto-aad/api-management-aad-groups-list.png
[api-management-aad-group-added]: ./media/api-management-howto-aad/api-management-aad-group-added.png
[api-management-groups]: ./media/api-management-howto-aad/api-management-groups.png
[api-management-edit-group]: ./media/api-management-howto-aad/api-management-edit-group.png

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
[Accéder à l’API de graphique]: http://msdn.microsoft.com/library/azure/dn132599.aspx#BKMK_Graph

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Connectez-vous au portail développeur à l’aide d’un compte Azure Active Directory]: #Log-in-to-the-Developer-portal-using-an-Azure-Active-Directory-account

