<properties
    pageTitle="Azure AD et d’Applications : guider les développeurs | Microsoft Azure"
    description="Écrites pour les professionnels de l’informatique, cet article fournit des instructions pour intégrer des applications Azure Active Directory."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/03/2016"
    ms.author="kgremban"/>

# <a name="azure-ad-and-applications-develop-line-of-business-apps"></a>Azure AD et d’applications : développez des applications métier

Ce guide fournit une vue d’ensemble du développement d’applications (métier)-métier pour Azure Active Directory (AD). Est destiné aux administrateurs globaux Active Directory/Office 365.

## <a name="overview"></a>Vue d’ensemble

Création d’applications intégrées à Azure AD permet aux utilisateurs de votre organisation de l’authentification unique avec Office 365. Demander à l’application dans Azure AD vous permet de que contrôler la stratégie d’authentification de l’application. Pour en savoir plus sur conditionnel access et la protection des applications avec l’authentification multifacteur (MFA) voir [règles d’accès de configuration](active-directory-conditional-access-azuread-connected-apps.md).

Enregistrer votre application pour utiliser Azure Active Directory. Inscrit l’application signifie que votre permet aux développeurs Azure AD d’authentification des utilisateurs et demander l’accès à des ressources de l’utilisateur telles que la messagerie, calendrier et des documents.

Tout membre de votre annuaire (pas invités) peut enregistrer une application, également appelée *Création d’un objet application*.

Enregistrement d’une application permet à tout utilisateur à effectuer les opérations suivantes :

- Obtenir une identité pour leur application Azure AD reconnaît
- Obtenir un ou plusieurs secrets/clés que l’application peut utiliser pour s’authentifier à Active Directory
- Personnalisez l’application dans le portail Azure avec un nom personnalisé, le logo, etc..
- Appliquer des fonctionnalités d’autorisation Azure AD à leur application, y compris :
  - Contrôle d’accès basé sur un rôle (RBAC)
  - Azure Active Directory en tant que serveur d’autorisation oAuth (secure une API exposée par l’application)

- Déclarer les autorisations requises nécessaires à l’application de la fonction comme prévu, y compris :-autorisations des applications (administrateurs globaux uniquement). Par exemple : l’appartenance aux rôles dans un autre Azure AD application ou du rôle d’appartenance par rapport à un Azure ressource, groupe de ressources, ou d’abonnement - autorisations déléguées (tous les utilisateurs). Par exemple : Azure AD, se connecter et profil de lecture


> [AZURE.NOTE]Par défaut, tous les membres peuvent enregistrer une application. Pour savoir comment limiter les autorisations pour l’enregistrement des applications à des membres spécifiques, voir [comment les applications sont ajoutées à Azure Active Directory](active-directory-how-applications-are-added.md#who-has-permission-to-add-applications-to-my-azure-ad-instance).

Voici ce que vous, l’administrateur global, devez suivre pour aider les développeurs à rendre leur application prêt pour la production :

- Configurer les règles d’accès (access stratégie/MFA)
- Configurer l’application pour exiger d’affectation d’utilisateurs et attribuer aux utilisateurs
- Supprimer l’expérience de consentement utilisateur par défaut

## <a name="configure-access-rules"></a>Configurer des règles d’accès

Configurer des règles d’accès de l’application à vos applications SaaS. Par exemple, vous pouvez exiger l’authentification Multifacteur ou autoriser uniquement l’accès aux utilisateurs de réseaux approuvés. Les détails pour ce sont disponibles dans le document [règles d’accès de configuration](active-directory-conditional-access-azuread-connected-apps.md).

## <a name="configure-the-app-to-require-user-assignment-and-assign-users"></a>Configurer l’application pour exiger d’affectation d’utilisateurs et attribuer aux utilisateurs

Par défaut, les utilisateurs peuvent accéder aux applications sans assignée. Toutefois, si l’application expose rôles ou si vous souhaitez que l’application apparaisse dans le panneau d’accès d’un utilisateur, vous devez demander attribution de l’utilisateur.

[Pour demander une attribution de l’utilisateur](active-directory-applications-guiding-developers-requiring-user-assignment.md)

Si vous êtes abonné à Azure AD Premium ou Suite de mobilité d’entreprise (EM), nous vous recommandons vivement de l’utilisation des groupes. Affectation de groupes à l’application vous permet de déléguer la gestion d’un accès en continu au propriétaire du groupe. Vous pouvez créer un groupe ou demandez à la personne responsable de votre organisation pour créer un groupe à l’aide du centre de gestion de groupe.

[Affectation d’utilisateurs à l’application](active-directory-applications-guiding-developers-assigning-users.md)  
[Affectation de groupes à l’application](active-directory-applications-guiding-developers-assigning-groups.md)

## <a name="suppress-user-consent"></a>Supprimer consentement de l’utilisateur

Par défaut, chaque utilisateur accède à une expérience consentement pour vous connecter. L’expérience de Microsoft, demander aux utilisateurs d’octroyer des autorisations à une application peut être déconcertant pour les utilisateurs qui ne savent pas ces décisions.

Pour les applications que vous faites confiance, vous pouvez simplifier l’expérience utilisateur en consentez à l’application au nom de votre organisation.

Pour plus d’informations sur consentement de l’utilisateur et de l’expérience consentement dans Azure, voir [Intégration des Applications avec Azure Active Directory](active-directory-integrating-applications.md).

##<a name="related-articles"></a>Articles connexes

- [Activer l’accès à distance sécurisé aux applications locales avec Azure AD Application Proxy](active-directory-application-proxy-get-started.md)
- [Accès conditionnel Azure Preview pour les applications SaaS](active-directory-conditional-access-azuread-connected-apps.md)
- [Gestion de l’accès aux applications avec Azure Active Directory](active-directory-managing-access-to-apps.md)
- [Index des articles de gestion des applications dans Azure Active Directory](active-directory-apps-index.md)
