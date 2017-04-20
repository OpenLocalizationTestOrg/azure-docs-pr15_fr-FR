<properties
    pageTitle="Partage des comptes à l’aide d’Azure AD |  Microsoft Azure"
    description="Décrit comment Azure Active Directory permet aux organisations de partager en toute sécurité des comptes pour les applications en local et les services en nuage grand public."
    services="active-directory"
    documentationCenter=""
    authors="msStevenPo"
    manager="femila"
    editor=""/>

 <tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/09/2016"  
    ms.author="stevenpo"/>

# <a name="sharing-accounts-with-azure-ad"></a>Partage des comptes avec Azure AD

## <a name="overview"></a>Vue d’ensemble
Organisations doivent parfois utiliser un seul nom d’utilisateur et mot de passe pour plusieurs personnes. Cela se produit généralement dans les deux cas :

- Lorsque vous accédez à applications qui nécessitent une connexion unique et un mot de passe pour chaque utilisateur, si applications locaux ou consommateur services cloud (par exemple, les comptes d’entreprise médias sociaux).
- Lorsque vous créez des environnements multi-utilisateur. Vous devrez un compte local unique qui dispose de privilèges et peut être utilisé pour le programme d’installation, l’administration et récupération des activités (par exemple, le compte local « administrateur général » pour Office 365) ou la racine dans force de vente de base.

En règle générale, ces comptes seraient être partagés par distribuer les informations d’identification (nom d’utilisateur et mot de passe) aux personnes appropriées, ou les enregistrer dans un emplacement partagé dans lequel plusieurs agents approuvés puissent y accéder.

Le modèle de partage traditionnel comporte plusieurs inconvénients :

- Activer l’accès aux nouvelles applications, vous devez distribuer les informations d’identification à toutes les personnes nécessitant un accès.
- Chaque application partagée peut-être nécessiter son propre jeu d’informations d’identification partagées, obliger les utilisateurs à mémoriser plusieurs jeux d’informations d’identification. Lorsque les utilisateurs disposent de se rappeler de nombreuses informations d’identification, le risque augmente qu’ils aura recours à pratiques risquées. (par exemple, écriture vers le bas les mots de passe).
- Vous ne pouvez pas déterminer qui a accès à l’application.
- Vous ne peut pas déterminer qui a *accès* une application.
- Lorsque vous avez besoin de supprimer l’accès à l’application, vous devez mettre à jour les informations d’identification et redistribuer à toutes les personnes nécessitant un accès à cette application.

## <a name="azure-active-directory-account-sharing"></a>Partage de compte Azure Active Directory

Azure AD fournit une nouvelle approche à l’aide de comptes partagés qui élimine ces inconvénients.

L’administrateur Azure AD configure quelles applications un utilisateur peut accéder à l’aide du panneau d’accès et choisir le type de meilleures authentification unique adaptée à cette application. Un de ces types, *basée sur le mot de passe connexion unique*, vous permet d’Azure AD ou la définir comme un type de « intermédiaire » pendant le processus d’authentification pour cette application.

Connexion des utilisateurs dans une seule fois avec leur compte professionnel. Il s’agit le même compte qu’ils utilisent régulièrement pour accéder à leur messagerie ou le bureau. Ils peuvent découvrir et accéder aux uniquement les applications elles sont affectées. Avec les comptes partagés, cette liste des applications peut inclure n’importe quel nombre d’informations d’identification partagés. Mémoriser ou notez les différents comptes qu'ils utilisent peut-être ne doit pas l’utilisateur final.

Comptes partagés non seulement augmentent une vue d’ensemble et améliorent la convivialité, ils également améliorent votre sécurité. Les utilisateurs dotés des autorisations pour utiliser les informations d’identification ne voyez pas le mot de passe partagé, mais préférez obtiennent l’autorisation d’utiliser le mot de passe dans le cadre d’un flux d’authentification orchestré. En outre, avec certaines applications de l’authentification unique de mot de passe, vous avez la possibilité d’avoir Azure AD régulièrement survol (mise à jour) le mot de passe à l’aide de mots de passe grandes et complexes, augmentation de la sécurité du compte. L’administrateur peut facilement accorder ou révoquer l’accès à l’application et également savoir qui a accès au compte et qui l’accès par le passé.

Azure AD prend en charge les comptes partagés pour toute Suite de mobilité d’entreprise (Services), me Premium ou base utilisateurs sous licence, pour tous les types de mot de passe unique à partir d’applications. Vous pouvez partager des comptes pour une des milliers d’applications intégrées préalable dans la galerie d’applications et que vous pouvez ajouter votre propre application de mot de passe d’authentification avec [SSO des applications personnalisées](active-directory-sso-integrate-saas-apps.md).

Fonctionnalités Azure AD activer le partage de compte sont les suivantes :

- [Mot de passe de session unique](active-directory-appssoaccess-whatis.md#password-based-single-sign-on)
- Mot de passe authentification un agent unique
- [Affectation de groupe](active-directory-accessmanagement-self-service-group-management.md)
- Applications de mot de passe personnalisé
- [Application du tableau de bord/des rapports d’utilisation](active-directory-passwords-get-insights.md)
- Portails d’accès de l’utilisateur final
- [Proxy d’application](active-directory-application-proxy-get-started.md)
- [Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/all/)

## <a name="sharing-an-account"></a>Partage d’un compte
Pour utiliser Azure AD pour partager un compte que vous avez besoin :

- Ajouter une application [Galerie d’applications](https://azure.microsoft.com/marketplace/active-directory/) ou une [application personnalisée](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx)
- Configurer l’application pour le mot de passe authentification unique (SSO)
- Utiliser [d’affectation basées sur les groupes](active-directory-accessmanagement-group-saasapps.md) et sélectionnez l’option pour entrer des informations d’identification partagée
- Facultatif : dans certaines applications, tels que Facebook, Twitter ou LinkedIn, vous pouvez activer l’option de [mot de passe automatisé Azure AD retournement](http://blogs.technet.com/b/ad/archive/2015/02/20/azure-ad-automated-password-roll-over-for-facebook-twitter-and-linkedin-now-in-preview.aspx)

Vous pouvez également agrandir votre compte partagé plus sécurisées avec multifacteur l’authentification Multifacteur (pour en savoir plus sur la [sécurisation des applications avec Azure AD](../multi-factor-authentication/multi-factor-authentication-get-started.md)) et vous pouvez déléguer la gestion qui a accès à l’application à l’aide de gestion de groupe [Azure AD libre-service](active-directory-accessmanagement-self-service-group-management.md) .

## <a name="related-articles"></a>Articles connexes

- [Index des articles de gestion des applications dans Azure Active Directory](active-directory-apps-index.md)
- [Protection des applications avec accès conditionnel](active-directory-conditional-access.md)
- [Groupe libre-service gestion/SSA](active-directory-accessmanagement-self-service-group-management.md)
