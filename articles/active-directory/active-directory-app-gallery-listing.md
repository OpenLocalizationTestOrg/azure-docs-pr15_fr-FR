<properties
   pageTitle="Liste de votre application dans la galerie des applications Azure Active Directory"
   description="Comment obtenir la liste une application qui prend en charge de l’authentification unique dans la galerie Azure Active Directory | Microsoft Azure"
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="bryanla"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/16/2016"
   ms.author="mbaldwin"/>


# <a name="listing-your-application-in-the-azure-active-directory-application-gallery"></a>Liste de votre application dans la galerie des applications Azure Active Directory

Pour afficher une application qui prend en charge de l’authentification unique avec Azure Active Directory dans la [Galerie Azure AD](https://azure.microsoft.com/marketplace/active-directory/all/), l’application doit tout d’abord implémenter l’un des modes d’intégration suivants :

* **Se connecter OpenID** - intégration directe avec Azure Active Directory à l’aide de OpenID se connecter pour l’authentification et de l’API de Microsoft Azure AD pour une configuration. Si vous débutez une intégration et que votre application ne prend pas en charge SAML, il s’agit du mode recommander.

* **SAML** – votre application déjà a la possibilité de configurer des fournisseurs d’identité tiers à l’aide du protocole SAML.

Configuration requise de la liste pour chaque mode est en dessous.

##<a name="openid-connect-integration"></a>OpenID connecter l’intégration

Pour intégrer votre application Azure AD, en suivant les [instructions pour les développeurs](active-directory-authentication-scenarios.md). Répondez aux questions ci-dessous, puis envoyer à waadpartners@microsoft.com.

* Fournir des informations d’identification pour un compte ou le client de test avec votre application qui peut être utilisée par l’équipe Azure AD pour tester l’intégration.  

* Fournir des instructions sur comment l’équipe Azure AD peut se connecter et se connecter une instance d’Azure AD à votre application à l’aide de la [Azure AD consentement framework](active-directory-integrating-applications.md#overview-of-the-consent-framework). 

* Fournir des instructions supplémentaires requises pour l’équipe Azure AD tester authentification unique avec votre application. 

* Fournir les informations ci-dessous :

> Nom de la société :
> 
> Site Web d’entreprise :
> 
> Nom de l’application :
> 
> Description de l’application (limite de 256 caractères) :
> 
> Site Web application (information) :
> 
> Site Web du Support technique application ou les informations de Contact :
> 
> ID de client de l’application, comme indiqué dans les détails de l’application en https://manage.windowsazure.com :
> 
> URL de l’abonnement à un l’application dans lequel les clients accédez se pour inscrire aux et et/ou achètent l’application :
> 
> Sélectionnez jusqu'à trois catégories de votre application soit répertoriée sous (pour les catégories disponibles, voir la place de marché Azure Active Directory) :
> 
> Joindre Application petite icône (fichier PNG, 45px par 45px, couleur d’arrière-plan unie) :
> 
> Joindre Application grandes icônes (fichier PNG, 215px par 215px, couleur d’arrière-plan unie) :
> 
> Joindre un Logo d’Application (fichier PNG, 150px par 122px, couleur d’arrière-plan transparent) :

##<a name="saml-integration"></a>Intégration SAML

N’importe quelle application prenant en charge SAML 2.0 peut être intégrée directement à un client Azure AD à l’aide de [ces instructions pour ajouter une application personnalisée](active-directory-saas-custom-apps.md). Une fois que vous avez testé que l’intégration de l’application fonctionne avec Azure AD, envoyez les informations suivantes pour <waadpartners@microsoft.com>.

* Fournir des informations d’identification pour un compte ou le client de test avec votre application qui peut être utilisée par l’équipe Azure AD pour tester l’intégration.  

* Fournir les valeurs de (service du consommateur assertion) SAML authentification URL, URL de l’émetteur (ID entité) et l’URL de réponse pour votre application, comme indiqué [ici](active-directory-saas-custom-apps.md). Si vous fournissez généralement les valeurs suivantes dans le cadre d’un fichier de métadonnées SAML, puis envoyez qui également.

* Fournir une brève description de la configuration d’Azure AD comme fournisseur d’identité dans votre application à l’aide de SAML 2.0. Si votre application prend en charge la configuration Azure AD comme fournisseur d’identité via un portail d’administration libre-service, puis vérifiez que les informations d’identification fournies ci-dessus incluent la possibilité pour configurer ce paramètre.

* Fournir les informations ci-dessous :

> Nom de la société :
> 
> Site Web d’entreprise :
> 
> Nom de l’application :
> 
> Description de l’application (limite de 256 caractères) :
> 
> Site Web application (information) :
> 
> Site Web du Support technique application ou les informations de Contact :
> 
> URL de l’abonnement à un l’application dans lequel les clients accédez se pour inscrire aux et et/ou achètent l’application :
> 
> Choisir jusqu'à trois catégories de votre application soit répertoriée sous (pour les catégories disponibles, voir la [Azure Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/))) :
> 
> Joindre Application petite icône (fichier PNG, 45px par 45px, couleur d’arrière-plan unie) :
> 
> Joindre Application grandes icônes (fichier PNG, 215px par 215px, couleur d’arrière-plan unie) :
> 
> Joindre un Logo d’Application (fichier PNG, 150px par 122px, couleur d’arrière-plan transparent) :
