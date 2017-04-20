<properties
    pageTitle="Scénarios d’utilisation et les considérations relatives au déploiement d’Azure AD rejoindre | Microsoft Azure"
    description="Explique comment les administrateurs peuvent configurer Azure AD joindre pour leurs utilisateurs finaux (employés, les étudiants, les autres utilisateurs). Il présente également les différents scénarios réels pour l’utilisation de Azure AD rejoindre."
    services="active-directory"
    documentationCenter=""
    authors="femila"
    manager="swadhwa"
    editor=""
    tags="azure-classic-portal"/>

< balises ms.workload="identity ms.service= « active directory » « ms.tgt_pltfrm="na » ms.devlang="na « ms.topic="article « ms.date="09/27/2016 »

    ms.author="femila"/>

# <a name="usage-scenarios-and-deployment-considerations-for-azure-ad-join"></a>Scénarios d’utilisation et les considérations relatives au déploiement d’Azure AD rejoindre

## <a name="usage-scenarios-for-azure-ad-join"></a>Scénarios d’utilisation pour Azure AD rejoindre
### <a name="scenario-1-businesses-largely-in-the-cloud"></a>Scénario 1 : Entreprises principalement dans le cloud

Azure Active Directory rejoindre (Azure AD rejoindre) avantages si vous employer et de gestion des identités pour votre entreprise dans le cloud ou déplacez plus tôt dans le cloud. Vous pouvez utiliser un compte que vous avez créé dans Azure Active Directory pour se connecter à Windows 10. Par le biais [du processus de la première expérience d’exécution (FRX)](active-directory-azureadjoin-user-frx.md), ou en joignant Azure AD dans [le menu Paramètres](active-directory-azureadjoin-user-upgrade.md), vos utilisateurs peuvent participer à leurs ordinateurs à Azure Active Directory.  Vos utilisateurs peuvent également profiter authentification (SSO) accès unique aux ressources de nuage tel qu’Office 365, dans leur navigateur ou dans les applications Office.

### <a name="scenario-2-educational-institutions"></a>Scénario 2 : Les établissements d’enseignement

Les établissements d’enseignement disposent généralement deux types d’utilisateur : les enseignants et les étudiants. Enseignants sont considérés comme des membres à long terme de l’organisation. Création de comptes locaux de ces convient. Mais les étudiants sont shorter-term membres de l’organisation et leurs comptes pouvant être gérées dans Azure Active Directory. Cela signifie qu’échelle répertoire peut être envoyé vers le cloud au lieu d’être stockées localement. Cela signifie également que les étudiants seront en mesure de se connecter à Windows avec leurs comptes Azure AD et accéder aux ressources Office 365 dans les applications Office.

### <a name="scenario-3-retail-businesses"></a>Scénario 3 : Activités de vente au détail

Activités de vente au détail ont saisonnier et les employés à long terme. En règle générale, vous créez des comptes locaux et que vous utilisez machines à un domaine pour les employés à plein temps à long terme. Mais saisonnier est shorter-term membres de l’organisation, et il convient de gérer leurs comptes où les licences utilisateur peuvent être déplacés plus facilement autour. Lorsque vous créez leurs comptes d’utilisateurs dans le nuage avec des licences Office 365, ces utilisateurs obtenir les avantages de se connecter à Windows et Office applications avec un compte Azure AD, tandis que vous gérez plus de flexibilité avec leurs licences après la formation.

### <a name="scenario-4-additional-scenarios"></a>Scénario 4 : Des scénarios supplémentaires

Ainsi que les avantages décrites précédemment, vous bénéficiez d’avoir à vos utilisateurs de participer à leurs appareils à Azure Active Directory en raison d’une jonction simplifiée, gestion des appareils efficace, d’inscription gestion automatique appareil mobile et de l’authentification unique pour Azure AD et ressources locales.  


## <a name="deployment-considerations-for-azure-ad-join"></a>Considérations relatives au déploiement d’Azure AD rejoindre

### <a name="enable-your-users-to-join-a-company-owned-device-directly-to-azure-ad"></a>Permettre à vos utilisateurs à rejoindre un appareil société propriétaire directement à Azure AD


Les entreprises peuvent fournir des comptes exclusivement le nuage, aux organisations et partenaires. Ces partenaires accédez facilement aux applications d’entreprise et des ressources avec l’authentification unique. Ce scénario s’applique aux utilisateurs qui accèdent à ressources principalement dans le cloud, tels que les applications Office 365 ou SaaS qui s’appuient sur Azure AD pour l’authentification.

### <a name="prerequisites"></a>Conditions préalables
**Au niveau de l’entreprise (administrateur)**

*   Abonnement Azure avec Azure Active Directory  

**Au niveau utilisateur**

*   Windows 10 (éditions professionnelle et entreprise)

### <a name="administrator-tasks"></a>Tâches de l’administrateur
* [Configurer l’enregistrement de dispositifs](active-directory-azureadjoin-setup.md)

### <a name="user-tasks"></a>Tâches de l’utilisateur
* [Configurer un nouvel appareil Windows 10 avec Azure Active Directory lors de l’installation](active-directory-azureadjoin-user-frx.md)
* [Configurer un appareil Windows 10 avec Azure AD dans le menu Paramètres](active-directory-azureadjoin-user-upgrade.md)
* [Participer à un appareil Windows 10 personnel à votre organisation](active-directory-azureadjoin-personal-device.md)



## <a name="enable-byod-in-your-organization-for-windows-10"></a>Activer BYOD dans votre organisation pour Windows 10
Vous pouvez configurer vos utilisateurs et les employés à utiliser leur personnel Windows périphériques (BYOD) pour accéder aux applications d’entreprise et des ressources. Vos utilisateurs peuvent ajouter leurs comptes Azure AD (comptes professionnel ou scolaire) à un appareil Windows personnel pour accéder aux ressources de façon sécurisée et la conformité.

### <a name="prerequisites"></a>Conditions préalables
**Au niveau de l’entreprise (administrateur)**

*   Abonnement AD Azure

**Au niveau utilisateur**

*   Windows 10 (éditions professionnelle et entreprise)


### <a name="administrator-tasks"></a>Tâches de l’administrateur

* [Configurer l’enregistrement de dispositifs](active-directory-azureadjoin-setup.md)

### <a name="user-tasks"></a>Tâches de l’utilisateur
* [Participer à un appareil Windows 10 personnel à votre organisation](active-directory-azureadjoin-personal-device.md)


## <a name="additional-information"></a>Informations supplémentaires
* [Windows 10 pour les entreprises : modalités d’utilisation des appareils pour le travail](active-directory-azureadjoin-windows10-devices-overview.md)
* [Étendre les fonctionnalités de cloud à des appareils Windows 10 par le biais Azure Active Directory rejoindre](active-directory-azureadjoin-user-upgrade.md)
* [Authentification des identités sans mot de passe via Microsoft Passport](active-directory-azureadjoin-passport.md)
* [Découvrez les scénarios d’utilisation pour Azure AD rejoindre](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Se connecter à un domaine appareils à Azure Active Directory pour Windows 10 expériences](active-directory-azureadjoin-devices-group-policy.md)
* [Configurer la Azure AD rejoindre](active-directory-azureadjoin-setup.md)
