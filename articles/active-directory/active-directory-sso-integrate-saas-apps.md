<properties
    pageTitle="Intégration d’Azure Active Directory SSO aux applications SaaS |  Microsoft Azure"
    description="Activer l’authentification d’authentification unique et approvisionnement gestion centralisée d’accès des applications SaaS dans Azure Active Directory de l’utilisateur. Découvrez comment intégrer Azure Active Directory aux applications SaaS."
    services="active-directory"
      keywords="intégration d’Azure AD aux applications SaaS"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="09/30/2016"
    ms.author="curtand"/>

# <a name="integrate-azure-active-directory-single-sign-on-with-saas-apps"></a>Intégration d’Azure Active Directory SSO aux applications SaaS  

> [AZURE.SELECTOR]
- [Portail Azure](active-directory-enterprise-apps-manage-sso.md)
- [Portail classique Azure](active-directory-sso-integrate-saas-apps.md)

[AZURE.INCLUDE [active-directory-sso-use-case-intro](../../includes/active-directory-sso-use-case-intro.md)]

Pour commencer la configuration de l’authentification unique pour une application qui vous importez dans votre organisation, vous utiliserez un répertoire existant dans Azure Active Directory (AD Azure). Vous pouvez utiliser un répertoire Azure AD que vous obtenez via Microsoft Azure, Office 365 ou Windows Intune. Si vous avez deux ou plusieurs d'entre eux, voir [administrer votre annuaire Azure AD](active-directory-administer.md) pour déterminer l’objet à utiliser.

## <a name="authentication"></a>Authentification

Pour les applications qui prennent en charge la SAML 2.0, Web-Federation, ou protocoles OpenID se connecter, par Azure Active Directory certificats d’établir des relations d’approbation de signature. Pour plus d’informations, voir [Gestion des certificats pour fédéré de l’authentification unique](active-directory-sso-certs.md).

Pour les applications qui prennent en charge uniquement HTML basée sur les formulaires de se connecter, Azure Active Directory utilise « stockage de mot de passe » pour établir des relations de gestion de la confidentialité. Ainsi, les utilisateurs de votre organisation d’être connecté automatiquement à une application SaaS par Azure AD en utilisant les informations de compte utilisateur de l’application SaaS. Azure AD recueille et en toute sécurité stocke les informations de compte d’utilisateur et le mot de passe associé. Pour plus d’informations, voir [mot de passe de l’authentification unique](active-directory-appssoaccess-whatis.md#password-based-single-sign-on).

## <a name="authorization"></a>Autorisation

Un compte généré permet à un utilisateur être autorisés à utiliser une application après que qu’ils ont l’authentification par le biais de l’authentification unique. Mise en service de l’utilisateur peut être traité manuellement, ou dans certains cas, vous pouvez ajouter et supprimer des informations sur l’utilisateur de l’application de SaaS en fonction des modifications apportées dans Azure Active Directory. Pour plus d’informations sur l’utilisation des connecteurs Azure AD existants de mise en service automatisé, voir [automatisé utilisateur mise en service et la désactivation des comptes pour les applications SaaS](active-directory-saas-app-provisioning.md).

Dans le cas contraire, vous pouvez ajouter manuellement les informations utilisateur pour une application, ou utiliser d’autres solutions de mise en service qui sont disponibles sur le marché.

## <a name="access"></a>Access

Azure AD propose plusieurs manières personnalisables pour déployer des applications pour les utilisateurs finaux de votre organisation. Vous ne sont pas verrouillées dans un déploiement particulier ou d’une solution d’accès. Vous pouvez utiliser [la solution qui correspond le mieux à vos besoins](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users).

## <a name="additional-considerations-for-applications-already-in-use"></a>Autres considérations relatives aux applications déjà en cours d’utilisation

Configuration de l’authentification unique sur pour une application qui utilise déjà votre organisation est un processus différent du processus de création de nouveaux comptes d’une nouvelle application. Il existe quelques étapes préliminaires, y compris : mappage des identités d’utilisateurs dans l’application avec les identités Azure AD et comprendre comment les utilisateurs connaissent se connecter à une application après son intégration.

> [AZURE.NOTE] Pour configurer l’authentification unique pour une application existante, vous devez disposer de droits d’administrateur global dans les deux Azure Active Directory et de l’application SaaS.

### <a name="mapping-user-accounts"></a>Mappage des comptes d’utilisateurs

Identité d’un utilisateur a généralement un identificateur unique qui peut être une adresse de messagerie ou le nom d’utilisateur principal (UPN). Vous devrez lien (carte) identité de l’application de chaque utilisateur à respectives identité Azure AD. Il existe deux façons d’y parvenir selon la manière dont l’exigence d’authentification de votre application.

Pour plus d’informations sur le mappage des identités d’applications avec des identités Azure AD, voir [revendications personnalisation émises dans le jeton SAML](http://social.technet.microsoft.com/wiki/contents/articles/31257.azure-active-directory-customizing-claims-issued-in-the-saml-token-for-pre-integrated-apps.aspx) et [personnalisation mappages d’attributs de mise en service](active-directory-saas-customizing-attribute-mappings.md).

### <a name="understanding-the-users-log-in-experience"></a>Présentation de journal de l’utilisateur dans expérience

Lorsque vous intégrez l’authentification unique pour une application qui est déjà en cours d’utilisation, il est important de savoir que l’expérience utilisateur est affectée. Pour toutes les applications, les utilisateurs commence à l’aide de leurs informations d’identification Azure AD pour vous connecter. Il pourrait également être qu’ils doivent utiliser un portail différent pour accéder aux applications.

L’authentification unique pour certaines applications peut être exécuté sur le signe de l’application dans l’interface, mais pour d’autres applications, l’utilisateur a traitée un portail central tel que ([Mes applications](http://myapps.microsoft.com) ou [Office 365](http://portal.office.com/myapps)) pour vous connecter. En savoir plus sur les différents types de l’authentification unique et leur expérience utilisateur dans [Quel est l’accès aux applications et l’authentification unique avec Azure Active Directory](active-directory-appssoaccess-whatis.md).

Une autre ressource précieuse est *Suppressing utilisateur consentement* dans l’article [guidage développeurs](active-directory-applications-guiding-developers-for-lob-applications.md) .

## <a name="next-steps"></a>Étapes suivantes


Pour les applications SaaS que vous trouvez dans la galerie d’applications, Azure AD fournit une série de [didacticiels sur l’intégration des applications SaaS](active-directory-saas-tutorial-list.md).

Si l’application n’est pas dans la galerie d’applications, vous pouvez [l’ajouter à la galerie d’applications Azure AD comme une application personnalisée](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx).

Il est beaucoup plus de détails sur l’ensemble de ces problèmes dans la bibliothèque Azure.com, en commençant par [Quel est l’accès aux applications et l’authentification unique avec Azure Active Directory.](active-directory-appssoaccess-whatis.md).

## <a name="see-also"></a>Voir aussi

- [Index des articles de gestion des applications dans Azure Active Directory](active-directory-apps-index.md)
