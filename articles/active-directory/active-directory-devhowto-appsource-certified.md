<properties
   pageTitle="Comment obtenir AppSource certifié pour Azure Active Directory | Microsoft Azure"
   description="Plus d’informations sur comment obtenir votre application AppSource certifié pour Azure Active Directory."
   services="active-directory"
   documentationCenter=""
   authors="skwan"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="10/28/2016"
   ms.author="skwan;bryanla"/>

#<a name="how-to-get-appsource-certified-for-azure-active-directory-ad"></a>Comment obtenir AppSource certifié pour Azure Active Directory (AD) 

Pour recevoir une certification AppSource pour Azure AD, votre application doit implémenter le signe client multiples dans un motif avec Azure AD en utilisant les protocoles OpenID se connecter, OAuth 2.0 ou SAML 2.0. 

Si vous n’êtes pas familiarisé avec la connexion Azure AD ou développement d’applications client multiples :

1. Commencez par lecture sur le [navigateur pour les scénarios Web App dans les scénarios d’authentification pour Azure AD][AAD-Auth-Scenarios-Browser-To-WebApp].  
2. Ensuite, consultez le Azure AD [guides de démarrage rapide web application][AAD-QuickStart-Web-Apps], qui vous montrer comment implémenter se connecter et incluent des exemples de code sont remplis. 

    > [AZURE.TIP] Essayez de l’aperçu de notre nouveau [portail pour les développeurs](https://identity.microsoft.com/Docs/Web) qui vous aideront à être opérationnel avec Azure Active Directory en quelques minutes !  Le portail de développement vous guidera tout au long du processus d’enregistrement une application et intégration Azure AD dans votre code.  Lorsque vous avez terminé, vous disposerez d’une application simple qui peut s’authentifier dans votre client et un principale, les utilisateurs qui peuvent accepter des jetons et effectuer la validation.

3. Pour savoir comment implémenter le modèle de connexion client à plusieurs avec Azure Active Directory, consultez [la connexion à tout utilisateur Azure Active Directory (AD) en utilisant le modèle d’application client multiples][AAD-Howto-Multitenant-Overview]

## <a name="related-content"></a>Contenu associé
Pour plus d’informations sur la création d’applications qui prennent en charge connexion Azure AD, ou pour obtenir de l’aide et support, consultez le [Guide du développeur Azure AD][AAD-Dev-Guide].

Utilisez la section commentaires Disqus suite de cet article pour fournir des commentaires et aidez-nous à affiner et mettre en forme notre contenu.

<!--Reference style links -->
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Auth-Scenarios-Browser-To-WebApp]: ./active-directory-authentication-scenarios.md#web-browser-to-web-application
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Howto-Multitenant-Overview]: ./active-directory-devhowto-multi-tenant-overview.md
[AAD-QuickStart-Web-Apps]: ./active-directory-developers-guide.md#web-application-quick-start-guides


<!--Image references-->










