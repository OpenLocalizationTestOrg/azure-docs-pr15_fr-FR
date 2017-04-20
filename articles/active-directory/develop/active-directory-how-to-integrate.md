<properties
   pageTitle="L’intégration avec Azure Active Directory | Microsoft Azure"
   description="Un guide sur les avantages d’et des ressources pour l’intégration avec Azure Active Directory."
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

# <a name="integrating-with-azure-active-directory"></a>Intégration à Azure Active Directory

[AZURE.INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Azure Active Directory fournit aux organisations avec la gestion des identités échelle de l’entreprise pour les applications cloud.  Azure AD intégration donne à vos utilisateurs une expérience de connexion simplifiée et permet à votre application sont conformes à la stratégie informatique.

## <a name="how-to-integrate"></a>Comment intégrer

Il existe plusieurs manières de votre application pour intégrer Azure AD.  Tirer parti de nombreuses ou en tant que quelques scénarios suivants, selon votre application.

### <a name="support-azure-ad-as-a-way-to-sign-in-to-your-application"></a>Prendre en charge Azure AD pour se connecter à votre Application ont

**Réduire friction de connexion et réduire les coûts de prise en charge.** À l’aide de Azure AD pour vous connecter à votre application, vos utilisateurs n’ont un nom plus et mot de passe à l’esprit.  En tant que développeur, vous aurez une moins mot de passe pour stocker et protéger.  Ne pas avoir à gérer les mots de passe oublié peut être une réduction considérable seule.  Azure AD met à se connecter pour certaines applications cloud les plus populaires du monde, y compris Office 365 et Microsoft Azure.  Avec des centaines de millions utilisateurs millions d’organisations, il est probable qu’est votre utilisateur est déjà connecté à Azure AD.  En savoir plus sur la [prise en charge l’ajout de connexion Azure AD](../active-directory-authentication-scenarios.md).

**Simplifier l’authentification vers le haut pour votre application.**  Au cours de l’inscription de votre application, Azure AD peut envoyer des informations importantes à un utilisateur afin que vous pouvez renseigner votre formulaire d’inscription ou éliminer complètement.  Les utilisateurs peuvent s’inscrire pour votre application à l’aide de leur compte Azure AD via une expérience consentement familières semblable à ceux figurant dans les réseaux sociaux et applications mobiles.  Tous les utilisateurs peuvent s’inscrire et se connecter à une application intégrée à Azure AD sans devoir implication des services informatiques.  En savoir plus sur [la signature en haut de votre application pour la connexion Azure AD compte](../../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md).

### <a name="browse-for-users-manage-user-provisioning-and-control-access-to-your-application"></a>Rechercher des utilisateurs, gérer la configuration des utilisateurs et contrôler l’accès à votre Application

**Rechercher les utilisateurs dans l’annuaire.**  Utiliser l’API Graph pour aider les utilisateurs à rechercher et parcourir les autres personnes dans leur organisation lorsque vous invitez d’autres personnes ou adresses de l’octroi d’accès, à la place qu’ils aient à taper courrier.  Les utilisateurs peuvent naviguer à l’aide d’une interface familière adresse livre style, telles que l’affichage des détails de la hiérarchie d’organisation.  En savoir plus sur l' [API de graphique](../active-directory-graph-api.md).

**Réutiliser groupes Active Directory et listes de distribution qu'est déjà gestion de votre client.**  Azure AD contient les groupes que votre client est déjà à l’aide pour la distribution de courrier électronique et gestion de l’accès.  À l’aide de l’API Graph, réutiliser ces groupes plutôt que votre client créer et gérer un ensemble de groupes dans votre application distinct.  Informations sur le groupe peuvent également être envoyées à votre application dans jetons de connexion.  En savoir plus sur l' [API de graphique](../active-directory-graph-api.md).

**Utilisez Azure AD pour contrôler les personnes ayant accès à votre application.**  Les administrateurs et les propriétaires de l’application dans Azure AD peuvent affecter accès à des applications à des utilisateurs et groupes.  À l’aide de l’API de graphique, vous pouvez lire cette liste et utilisez-le pour contrôler la mise en service et la désactivation des comptes de ressources et accéder au sein de votre application.

**Utilisez Azure AD pour les rôles en fonction de contrôle d’accès.**  Les administrateurs et les propriétaires de l’application peuvent affecter des utilisateurs et groupes aux rôles que vous définissez lorsque vous enregistrez votre application dans Azure AD.  Informations sur les rôles sont envoyées à votre application dans les jetons de connexion et peuvent également être lus à l’aide de l’API de graphique.  En savoir plus sur [l’utilisation de Azure AD pour l’autorisation](http://blogs.technet.com/b/ad/archive/2014/12/18/azure-active-directory-now-with-group-claims-and-application-roles.aspx).

### <a name="get-access-to-users-profile-calendar-email-contacts-files-and-more"></a>Accéder à du profil utilisateur, calendrier, messagerie, Contacts, fichiers et autres éléments

**Azure AD est le serveur d’autorisation pour Office 365 et d’autres services professionnels Microsoft.**  Si vous prenez en charge Azure AD pour se connecter à votre application ou la prise en charge de la liaison de vos comptes d’utilisateur actuel aux comptes d’utilisateurs Azure AD à l’aide de OAuth 2.0, vous pouvez demander l’accès en lecture et écriture à un profil utilisateur, calendrier, messagerie, contacts, fichiers et autres informations.  Vous pouvez en toute transparence écrire des événements au calendrier de l’utilisateur et lire ou écrire des fichiers dans leur OneDrive.  En savoir plus sur [l’accès à l’API d’Office 365](https://msdn.microsoft.com/office/office365/howto/platform-development-overview).

### <a name="promote-your-application-in-the-azure-and-office-365-marketplaces"></a>Promouvoir votre Application dans l’Azure et Office 365 marchés

**Promouvoir votre application aux millions d’organisations qui utilisent déjà Azure AD.**  Les utilisateurs qui rechercher et parcourir ces marchés utilisent déjà une ou plusieurs services de cloud, rendant compatibles avec les clients de service cloud.  Apprenez-en davantage sur promouvoir votre application dans [Le Azure Marketplace](https://azure.microsoft.com/marketplace/partner-program/).

**Lorsque les utilisateurs s’inscrire pour votre application, il s’affiche dans leur panneau d’accès Azure AD et le Lanceur d’applications Office 365.**  Les utilisateurs pourront rapidement et facilement revenir à votre application ultérieurement, amélioration engagement utilisateur.  En savoir plus sur [Azure AD accéder Panneau de configuration](../active-directory-saas-access-panel-introduction.md).

### <a name="secure-device-to-service-and-service-to-service-communication"></a>Sécuriser les communications de Service de l’appareil et Service à Service

**À l’aide de Azure AD pour la gestion des identités de services et les périphériques réduit le code que vous devez écrire et permet au service informatique pour gérer l’accès.**  Services et les périphériques peuvent obtenir jetons à partir d’Azure AD à l’aide de OAuth et utiliser ces jetons pour accéder aux API web.  À l’aide d’Azure AD vous pouvez éviter d’écrire du code d’authentification complexes.  Étant donné que les identités des services et des périphériques sont stockées dans AD Azure, informatique peut gérer les clés et à sa révocation en un seul endroit plutôt que de laisser procéder séparément dans votre application.

## <a name="benefits-of-integration"></a>Avantages de l’intégration

Intégration avec Azure AD est fourni avec avantages qui ne nécessitent pas d’écrire du code supplémentaire.

### <a name="integration-with-enterprise-identity-management"></a>Intégration avec la gestion des identités entreprise

**Aider votre application respecter les règles informatiques.**  Organisations intégrant leur système de gestion d’identité entreprise Azure AD, afin que lorsqu’une personne quitte une organisation, ils seront automatiquement perdre l’accès à votre application sans informatique ayant besoin d’effectuer des étapes supplémentaires.  INFORMATIQUES peuvent gérer qui peut accéder à votre application et déterminer quelles stratégies d’accès sont requises - par exemple une authentification multifacteur - réduire votre besoin d’écrire du code pour respecter les règles d’entreprise complexes.  Azure AD permet aux administrateurs de journal d’audit détaillés des qui connecté à votre application donc informatique peut effectuer le suivi de l’utilisation.

**Azure AD étend Active Directory dans le cloud afin que votre application peut intégrer AD.**  De nombreuses organisations dans le monde entier utilisent Active Directory comme leur connexion principal et un système de gestion des identités et nécessitent leurs applications pour fonctionner avec Active Directory.  Intégration avec Azure AD, votre application s’intègre à Active Directory.

### <a name="advanced-security-features"></a>Fonctionnalités de sécurité avancées

**Authentification multifacteur.**  Azure AD fournit une authentification multifacteur native.  Les administrateurs informatiques peuvent nécessiter l’authentification multifacteur pour accéder à votre application, afin que vous n’êtes pas obligé de cette prise en charge du code vous-même.  En savoir plus sur [L’authentification multifacteur](https://azure.microsoft.com/documentation/services/multi-factor-authentication/).

**Connexion anormale détection.**  Azure AD traite plus milliards signe-ins jour, lors de l’utilisation des algorithmes apprentissage machine pour détecter suspect et informer les administrateurs informatiques des éventuels problèmes.  Prend en charge la connexion Azure AD, votre application obtienne tous les avantages de cette. Plus d’informations sur [l’affichage Azure Active Directory état access](../active-directory-view-access-usage-reports.md).

**Accès conditionnel.**  Outre l’authentification multifacteur, les administrateurs peuvent exiger des conditions spécifiques respecter avant d’utilisateurs de se connecter à votre application.  Conditions qui peuvent être définies incluent la plage d’adresses IP de périphériques client, l’appartenance aux groupes spécifiées et l’état de l’appareil utilisé pour l’accès.  Apprenez-en davantage sur [accès conditionnel Azure Active Directory](../active-directory-conditional-access.md).

### <a name="easy-development"></a>Développement facile

**Protocoles standard.**  Microsoft s’engage à prendre en charge des normes industrielles.  Azure AD prend en charge les protocoles d’authentification SAML 2.0, OpenID connecter 1.0, OAuth 2.0 et Web-Federation 1.2.  L’API Graph est conforme à OData 4.0.  Si votre application déjà prend en charge les protocoles SAML 2.0 ou OpenID connecter 1.0 pour fédéré se connecter, prise en charge l’ajout de Azure AD peut être simple.  En savoir plus sur les [protocoles d’authentification Azure AD pris en charge](../active-directory-authentication-protocols.md).

**Bibliothèques d’ouvrir la source.**  Microsoft fournit des bibliothèques libres entièrement prise en charge pour les langues courantes et plateformes pour accélérer le développement.  Le code source est autorisé sous Apache 2.0 et vous êtes libre de branche et collaboration sur les projets.  En savoir plus sur les [bibliothèques de l’authentification Azure AD](../active-directory-authentication-libraries.md).

### <a name="worldwide-presence-and-high-availability"></a>Présence dans le monde entier et la disponibilité

**Azure AD est déployé dans les centres de données dans le monde entier et est gérée et surveiller la nuit.**  Azure AD s’agit du système de gestion des identités pour Microsoft Azure et Office 365 et est déployé dans 28 centres de données dans le monde entier.  Données de l’annuaire sont assurées d’être répliquées sur au moins trois centres de données.  Programmes d’équilibrage de charge global garantir l’accès aux utilisateurs la copie la plus proche de Azure AD contenant les données de leur et ré-router automatiquement les demandes aux autres centres de données si un problème est détecté.

## <a name="next-steps"></a>Étapes suivantes

[Commencer à écrire du code](../active-directory-developers-guide.md#getting-started).

[Utilisateurs de se connecter à l’utilisation Azure AD](../active-directory-authentication-scenarios.md)
