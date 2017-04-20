<properties
   pageTitle="Présentation de la sécurité Gestion des identités Azure | Microsoft Azure"
   description=" Identité et accès gestion des solutions aide Microsoft IT protéger l’accès aux applications et ressources au sein du centre de données d’entreprise et dans le cloud, l’activation des niveaux supplémentaires de validation telles que l’authentification multifacteur et les stratégies d’accès conditionnelle. Cet article fournit une vue d’ensemble des principales fonctionnalités de sécurité Azure qui aident à la gestion des identités. "
   services="security"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/09/2016"
   ms.author="terrylan"/>

# <a name="azure-identity-management-security-overview"></a>Présentation de la sécurité Gestion des identités Azure

Identité et accès gestion des solutions aide Microsoft IT protéger l’accès aux applications et ressources au sein du centre de données d’entreprise et dans le cloud, l’activation des niveaux supplémentaires de validation telles que l’authentification multifacteur et les stratégies d’accès conditionnelle. Surveiller l’activité suspecte par le biais sécurité avancée rapports, l’audit et d’alertes vous aide à limiter les problèmes potentiels de sécurité. [Azure Active Directory Premium](../active-directory/active-directory-editions.md) fournit de l’authentification unique à des milliers de cloud (SaaS) applications et accéder aux applications web que vous exécutez en local.

Avantages de sécurité d’Azure Active Directory (AD) la possibilité de :

- Créer et gérer une identité unique pour chaque utilisateur au sein de votre entreprise hybride, synchronisation des utilisateurs, les groupes et les appareils mobiles
- Fournir un accès authentification unique à vos applications, y compris des milliers d’applications SaaS préalable intégrées
- Activer la sécurité de l’application access en appliquant authentification multifacteur basée sur les règles pour les deux local et applications en nuage
- Mise en service d’accès à distance sécurisé aux applications web de local via Azure AD Application Proxy

L’objectif de cet article consiste à fournir une vue d’ensemble des principales fonctionnalités de sécurité Azure qui aident à la gestion des identités. Nous fournissons également des liens vers des articles qui donnent des détails de chaque fonctionnalité afin que vous pouvez en savoir plus.  

L’article se concentre sur les fonctionnalités de gestion d’identité Azure core suivantes :

- Authentification unique
- Proxy inverse
- Authentification multifacteur
- Contrôle de la sécurité, des alertes et les rapports d’apprentissage automatique
- Gestion des identités et accès consommateur
- Inscription de l’appareil
- Gestion des identités dotés de privilèges
- Protection d’identité
- Gestion des identités hybride

## <a name="single-sign-on"></a>Authentification unique

Authentification (SSO) moyen unique ne pourrez plus accéder à l’ensemble des applications et des ressources que vous avez besoin pour collaborer, en vous connectant qu’une seule fois à l’aide d’un seul compte d’utilisateur. Une fois connecté, vous pouvez accéder à toutes les applications que vous avez besoin sans devoir s’authentifier (par exemple, tapez un mot de passe) une seconde fois.

De nombreuses organisations s’appuient sur logiciel en tant qu’une applications de service (SaaS) tels qu’Office 365, zone et Salesforce pour la productivité des utilisateurs. Traditionnellement, votre service informatique nécessaire individuellement créer et mettre à jour des comptes d’utilisateurs dans chaque application SaaS, et les utilisateurs devaient n’oubliez pas d’un mot de passe pour chaque application SaaS.

Azure AD élargit Active Directory local dans le nuage, qui permet aux utilisateurs d’utiliser leur propre compte d’organisation primaire non seulement se connecter à leur appareil à un domaine et ressources d’entreprise, mais également toutes les du web et des applications SaaS nécessaires pour leur travail.

Non seulement les utilisateurs inutile de gérer plusieurs jeux de noms d’utilisateur et les mots de passe, accès aux applications peut être générés ou désélectionnez générés automatiquement en fonction des groupes de d’organisation et leur statut en tant qu’employé. Azure AD présente la sécurité et accéder aux contrôles gouvernance qui vous permettent de centraliser l’accès des utilisateurs entre les applications SaaS.

Pour en savoir plus :

- [Vue d’ensemble de l’authentification unique](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/)
- [Quel est l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../active-directory/active-directory-appssoaccess-whatis.md)
- [Intégration d’Azure Active Directory SSO aux applications SaaS](../active-directory/active-directory-sso-integrate-saas-apps.md)

## <a name="reverse-proxy"></a>Proxy inverse

Azure AD Application Proxy vous permet de publier des applications en local, tels que des sites [SharePoint](https://support.office.com/article/What-is-SharePoint-97b915e6-651b-43b2-827d-fb25777f446f?ui=en-US&rs=en-US&ad=US) , [Outlook Web App](https://technet.microsoft.com/library/jj657718.aspx)et [IIS](http://www.iis.net/)-en fonction des applications à l’intérieur de votre réseau privé et offre un accès sécurisé aux utilisateurs extérieurs à votre réseau. Proxy d’application donne accès à distance et authentification unique (SSO) pour de nombreux types d’applications web d’en local avec des milliers d’applications SaaS Azure AD prises en charge. Employés peuvent vous connecter à vos applications à partir d’accueil sur leurs propres périphériques et s’authentifier dans ce proxy sur le nuage.

Pour en savoir plus :

- [L’activation de Proxy de l’Application Azure AD](../active-directory/active-directory-application-proxy-enable.md)
- [Publier des applications à l’aide de Proxy d’Application Azure AD](../active-directory/active-directory-application-proxy-publish.md)
- [Seul-authentification avec Proxy d’Application](../active-directory/active-directory-application-proxy-sso-using-kcd.md)
- [Utilisation de l’accès conditionnel](../active-directory/active-directory-application-proxy-conditional-access.md)

## <a name="multi-factor-authentication"></a>Authentification multifacteur
L’authentification multifacteur Azure (MFA) est une méthode d’authentification qui nécessite l’utilisation de plusieurs méthodes de vérification et ajoute un second niveau de sécurité critique à l’utilisateur se-ins et les transactions. L’authentification Multifacteur contribue à garantir l’accès aux données et aux applications tout en répondant à la demande de l’utilisateur pour un processus de connexion simple. Il propose une authentification renforcée via une plage d’options de vérification, appel téléphonique, un message texte ou l’application mobile notification ou la vérification de code et 3e partie OAuth jetons.

Pour en savoir plus :

- [Authentification multifacteur](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
- [Quel est l’authentification multifacteur Azure ?](../multi-factor-authentication/multi-factor-authentication.md)
- [Fonctionnement de l’authentification multifacteur Azure](../multi-factor-authentication/multi-factor-authentication-how-it-works.md)

## <a name="security-monitoring-alerts-and-machine-learning-based-reports"></a>Contrôle de la sécurité, des alertes et les rapports d’apprentissage automatique

Contrôle de la sécurité et les alertes et les rapports apprentissage machine qui identifient les modèles d’accès n’est pas cohérente peuvent vous aider à protéger votre entreprise. Vous pouvez utiliser access Azure d’Active Directory et les rapports d’utilisation visibilité sur l’intégrité référentielle et la sécurité de l’annuaire de votre organisation. Grâce à ces informations, un administrateur de l’annuaire peut mieux déterminer où les risques de sécurité est peut-être dû afin qu’ils puissent correctement à limiter ces risques.

Dans le portail classique Azure, les rapports sont classés comme suit :

- Rapports anomalie – contiennent des événements qui nous identifiés comme anormale de connexion. Notre objectif est de prendre une telle activité et vous permettent d’être en mesure de déterminer si un événement est suspect.
- Les rapports Application intégrées – fournissent des informations sur l’utilisation des applications en nuage de votre organisation. Azure Active Directory offre l’intégration avec des milliers d’applications cloud.
- Rapports d’erreurs – indiquent les erreurs pouvant se produire lors de la configuration des comptes à des applications externes.
- Rapports spécifiques à l’utilisateur – afficher appareil/dans les données de l’activité d’un utilisateur spécifique.
- Journaux d’activité – contiennent un enregistrement de tous les événements vérifiés dans les dernières 24 heures, 7 derniers jours, ou 30 derniers jours, ainsi que les changements d’activité groupe et activité réinitialiser et d’enregistrement de mot de passe.

Pour en savoir plus :

- [Afficher vos rapports access et l’utilisation](../active-directory/active-directory-view-access-usage-reports.md)
- [Prise en main Azure Active Directory Reporting](../active-directory/active-directory-reporting-getting-started.md)
- [Guide de création de rapports d’Azure Active Directory](../active-directory/active-directory-reporting-guide.md)

## <a name="consumer-identity-and-access-management"></a>Gestion des identités et accès consommateur

Azure B2C annuaire Active est un service de gestion des identités globale, très disponible pour les applications pour les consommateurs qui s’adapte à des centaines de millions d’identités. Il peut être intégré au sein de mobile et web plateformes. Vos consommateurs peuvent vous connecter à toutes vos applications par le biais des expériences personnalisables à l’aide de leurs comptes mise en réseau existants ou en créant des nouvelles informations d’identification.

Par le passé, les développeurs d’applications qui veulent s’inscrire et se connecter consommateurs dans leurs applications seraient avez écrit leur propre code. Et serait ont utilisés systèmes ou bases de données locale pour stocker les noms d’utilisateur et les mots de passe. Azure B2C annuaire Active offre un meilleur moyen pour intégrer la gestion des identités consommateur applications à l’aide d’une plateforme sécurisée, basés sur des normes et un grand ensemble de stratégies extensibles à votre organisation.

Lorsque vous utilisez Azure Active Directory B2C, vos consommateurs peuvent s’inscrire pour vos applications à l’aide de leurs comptes sociales existants (Facebook, Google, Amazon, LinkedIn) ou en créant des nouvelles informations d’identification (adresse de messagerie et mot de passe, ou nom d’utilisateur et mot de passe).

Pour en savoir plus :

- [Qu’est Azure Active Directory B2C ?](https://azure.microsoft.com/services/active-directory-b2c/)
- [Azure Active Directory B2C aperçu : signer vers le haut et se connecter consommateurs dans vos applications](../active-directory-b2c/active-directory-b2c-overview.md)
- [Aperçu de B2C Azure Active Directory : Types d’Applications](../active-directory-b2c/active-directory-b2c-apps.md)

## <a name="device-registration"></a>Inscription de l’appareil

Enregistrement de dispositif Azure AD sert de scénarios basé sur un appareil [accès conditionnel](../active-directory/active-directory-conditional-access-on-premises-setup.md) . Lorsqu’un appareil est inscrit, Azure Active Directory d’inscription pour appareils fournit l’appareil avec une identité qui est utilisée pour authentifier le périphérique lorsque l’utilisateur se connecte. Le périphérique authentifié et des attributs de l’appareil, puis utilisable pour appliquer les stratégies d’accès conditionnel pour les applications qui sont hébergées dans le nuage et en local.

Lorsqu’il est combiné avec une solution de gestion (MDM) appareil mobile tel que Intune, les attributs du périphérique dans Azure Active Directory sont mises à jour des informations supplémentaires sur l’appareil. Permet de créer des règles d’accès conditionnel qui mettent en œuvre access à partir d’appareils pour répondre à vos normes de sécurité et de conformité.

Pour en savoir plus :

- [Prise en main Azure Active Directory d’inscription pour appareils](../active-directory/active-directory-conditional-access-device-registration-overview.md)
- [La configuration d’accès au conditionnelle locales avec Azure Active Directory d’inscription pour appareils](../active-directory/active-directory-conditional-access-on-premises-setup.md)
- [Enregistrement de dispositif automatique avec des appareils à un domaine Azure Active Directory pour Windows](../active-directory/active-directory-conditional-access-automatic-device-registration.md)

## <a name="privileged-identity-management"></a>Gestion des identités dotés de privilèges
Gestion des identités dotés de privilèges Azure Active Directory (AD) vous permet de gérer, contrôler et surveiller vos identités dotés de privilèges et l’accès aux ressources dans Azure AD ainsi que d’autres services en ligne Microsoft, tels que Office 365 ou Microsoft Intune.

Parfois, les utilisateurs ont besoin d’effectuer les opérations dotés de privilèges de ressources Azure ou Office 365, ou d’autres applications SaaS. Cela signifie souvent des organisations ont pour leur donner un accès privilégié permanent dans Azure Active Directory. Il s’agit d’un problème de sécurité croissant pour les ressources hébergé sur le nuage, car les organisations ne pourra pas suffisamment contrôler ce que font les utilisateurs avec leurs privilèges d’administrateur. En outre, si un compte d’utilisateur avec un accès privilégié est compromis, cette une violation peut affecter leur sécurité cloud globale. Gestion des identités Azure AD privilégié permet de résoudre ces risques.

Gestion des identités Azure AD privilégié vous permet de :

- Afficher les utilisateurs qui sont les administrateurs Azure AD
- Activer à la demande, « juste à temps « accès administratif à Microsoft Online Services tels qu’Office 365 et Intune
- Générez des rapports sur l’historique de l’accès administrateur et les modifications apportées aux affectations de l’administrateur
- Recevoir des alertes sur l’accès à un rôle dotés de privilèges

Pour en savoir plus :

- [Gestion des identités Azure AD privilégié](../active-directory/active-directory-privileged-identity-management-configure.md)
- [Rôles dans Azure AD privilégié gestion des identités](../active-directory/active-directory-privileged-identity-management-roles.md)
- [Azure AD dotés de privilèges gestion des identités : Comment ajouter ou supprimer un rôle d’utilisateur](../active-directory/active-directory-privileged-identity-management-how-to-add-role-to-user.md)

## <a name="identity-protection"></a>Protection d’identité
La Protection d’identité AD Azure est un service de sécurité qui offre une vue consolidée des événements de risque et vulnérabilités potentielles qui peuvent affecter des identités de votre organisation. Protection d’identité tire parti des fonctionnalités de détection de Azure Active Directory existant anomalie (accessible à partir de rapports d’activité anormale d’Azure AD) et présente les types d’événements risque permettant de détecter anomalies en temps réel.

Pour en savoir plus :

- [Protection d’identité Azure Active Directory](../active-directory/active-directory-identityprotection.md)
- [Canal 9 : Azure AD et identité diaporama : aperçu de la Protection d’identité](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="hybrid-identity-management"></a>Gestion des identités hybride

Approche de Microsoft pour identité étendues en local et le nuage, création d’une identité utilisateur unique pour l’authentification et l’autorisation à toutes les ressources, quel que soit l’emplacement.

Pour en savoir plus :

- [Hybride identité livre blanc](http://download.microsoft.com/download/D/B/A/DBA9E313-B833-48EE-998A-240AA799A8AB/Hybrid_Identity_White_Paper.pdf)
- [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/)
- [Blog de l’équipe Active Directory](https://blogs.technet.microsoft.com/ad/)
