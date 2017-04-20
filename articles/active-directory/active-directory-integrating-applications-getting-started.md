<properties
   pageTitle="Guide de démarrage rapide intégration d’Azure Active Directory avec les applications prise |  Microsoft Azure"
   description="Cet article est un guide de mise en route d’intégration Azure Active Directory (AD) avec les applications en local et nuage."
   services="active-directory"
   documentationCenter=""
   authors="ihenkel"
   manager="femila"
   editor=""/>

   <tags
      ms.service="active-directory"
      ms.devlang="na"
      ms.topic="article"
      ms.tgt_pltfrm="na"
      ms.workload="identity"
      ms.date="02/09/2016"
      ms.author="inhenk"/>

# <a name="integrating-azure-active-directory-with-applications-getting-started-guide"></a>Intégration d’Azure Active Directory avec les applications prise guide de démarrage rapide
## <a name="overview"></a>Vue d’ensemble
Cette rubrique est destinée à vous donner une feuille de route pour l’intégration des applications avec Azure Active Directory (AD). Chacune des sections ci-dessous contiennent un résumé d’une rubrique plus détaillée pour vous pouvez d’identifier les parties de ce guide de mise en route vous intéressent.  Suivez les liens pour obtenir de plus amples sur chaque objet.

## <a name="before-you-begin-take-inventory"></a>Avant de commencer, prenez stock
Avant de passer dans pour intégrer des applications Azure AD, il est important de savoir où que vous soyez et à l’endroit où vous voulez accéder.  Les questions suivantes sont là pour vous aider à penser à votre projet integration d’application Azure AD.

### <a name="application-inventory"></a>Inventaire des applications
- Où se trouvent tous vos applications ? Leur propriétaire ?
- Quel type d’authentification vos applications ont-ils besoin ?
- Qui a besoin d’accéder à des applications ?
- Vous voulez déployer une nouvelle application ?
  - Vous créer internes et déployer sur une instance cluster Azure ?
  - Utiliserez-vous qui est disponible dans la galerie des applications Azure ?

### <a name="user-and-group-inventory"></a>Inventaire des utilisateurs et des groupes
- Emplacement des vos comptes d’utilisateurs
 - Active Directory local
 - Azure AD
 - Dans une base de données distinct application dont vous êtes propriétaire
 - Dans les applications non sanctionnées
 - Tous les éléments ci-dessus
- Les attributions de rôle et les autorisations des utilisateurs individuels actuellement dois-je ? Vous devez examiner leur accès ou que vous êtes sûr que vos affectations d’accès et le rôle utilisateur sont appropriées maintenant ?
- Groupes sont déjà établies dans Active Directory local ?
 - Comment sont organisées vos groupes ?
 - Quels sont les membres du groupe ?
 - Quelles affectations autorisations/rôle les groupes actuellement dois-je ?
- Vous devez nettoyer les bases de données utilisateur/groupe avant d’intégrer ?  (Il s’agit d’une question assez importante. Garbage dans celle des entrées).

### <a name="access-management-inventory"></a>Inventaire de gestion des accès
- Comment gérer actuellement l’accès utilisateur aux applications ? A-t-il besoin pour modifier ?  Avez-vous d’autres façons de gérer l’accès, par exemple avec [RBAC](role-based-access-control-configure.md) par exemple ?
- Qui doit accès à quoi ?

Vous ne disposez peut-être les réponses à toutes ces questions avance sans toutefois que cela OK.  Ce guide peut vous aider à répondre à certaines de ces questions et certaines des décisions avisées.

## <a name="prerequisites"></a>Conditions préalables
- Un abonnement Azure et un répertoire Azure Active Directory.  Si vous n’avez pas un abonnement Azure, vous pouvez essayer Azure gratuitement pendant 30 jours. [Essayez-le !](https://azure.microsoft.com/trial/get-started-active-directory/)

## <a name="application-integration-with-azure-ad"></a>Intégration d’applications avec Azure AD
### <a name="finding-unsanctioned-cloud-applications-with-cloud-app-discovery"></a>Recherche non sanctionnée applications cloud avec la découverte de l’application Cloud
Comme indiqué ci-dessus, il peut y avoir des applications qui n’ont pas été gérées par votre organisation jusqu'à présent.  Dans le cadre du processus de stock, il est possible trouver les applications cloud non sanctionnée. Voir [trouver les applications en nuage non sanctionnée avec la découverte de l’application Cloud](active-directory-cloudappdiscovery-whatis.md).

### <a name="authentication-types"></a>Types d’authentification
Chacun de vos applications peut-être avoir des besoins d’authentification différents. Avec Azure Active Directory, les certificats de signature peuvent être utilisé avec les applications qui utilisent SAML 2.0, Web-Federation, ou OpenID connecter protocoles ainsi que votre mot de passe de session unique. Pour plus d’informations sur l’application types d’authentification pour une utilisation avec Azure AD voir [Gestion des certificats pour fédéré de l’authentification unique dans Azure Active Directory](active-directory-sso-certs.md) et [votre mot de passe selon l’authentification unique](active-directory-appssoaccess-whatis.md).

### <a name="enabling-sso-with-azure-ad-app-proxy"></a>L’activation de l’authentification unique avec Azure AD application Proxy
Avec Microsoft Azure AD Proxy de l’Application, vous pouvez donner accès aux applications situé à l’intérieur de votre réseau privé en toute sécurité, en tout lieu et sur n’importe quel appareil. Une fois que vous avez installé un connecteur de proxy application au sein de votre environnement, il peut être configuré facilement avec Azure AD.

### <a name="integrating-applications-with-azure-ad"></a>Intégration d’applications avec Azure Active Directory
Les articles suivants traitent les différentes façons applications intégrant Azure AD et fournissent des instructions.

- [Déterminer quelles Active Directory à utiliser](active-directory-administer.md)
- [À l’aide d’applications dans la galerie des applications Azure](active-directory-appssoaccess-whatis.md)
- [Intégration de liste des didacticiels applications SaaS](active-directory-saas-tutorial-list.md)

## <a name="managing-access-to-applications"></a>Gestion de l’accès aux applications
Les articles suivants décrivent manières de gérer l’accès aux applications une fois qu’ils ont été intégrés avec Azure AD à l’aide de Azure AD connecteurs et Azure AD.

- [Gestion de l’accès aux applications à l’aide d’Azure AD](active-directory-managing-access-to-apps.md)
- [Automatisation avec des connecteurs Azure AD](active-directory-saas-app-provisioning.md)
- [Affectation d’utilisateurs à l’application](active-directory-applications-guiding-developers-assigning-users.md)
- [Affectation de groupes à l’application](active-directory-applications-guiding-developers-assigning-groups.md)
- [Partage des comptes](active-directory-sharing-accounts.md)

## <a name="integrating-custom-applications"></a>Intégration des applications personnalisées
Si vous écrivez une nouvelle application et souhaitez aider les développeurs en tirant parti de la puissance Azure Active Directory, consultez [les développeurs guidage](active-directory-applications-guiding-developers-for-lob-applications.md).

Si vous voulez ajouter votre application personnalisée à la galerie des applications Azure, voir [« Mettre votre propre application » avec une configuration SAML Azure AD libre-service](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx).

## <a name="see-also"></a>Voir aussi

- [Index des articles de gestion des applications dans Azure Active Directory](active-directory-apps-index.md)
