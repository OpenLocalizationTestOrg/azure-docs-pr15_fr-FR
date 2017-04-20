

<properties
    pageTitle="Participer à un appareil personnel à votre organisation | Microsoft Azure"
    description="Explique comment les utilisateurs peuvent enregistrer leurs appareils Windows 10 personnels pour leur réseau d’entreprise et fournit les étapes de déploiement d’un scénario BYOD."
    services="active-directory"
    documentationCenter=""
    authors="femila"
    manager="swadhwa"
    editor=""
    tags="azure-classic-portal"/>
<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="femila"/>

# <a name="join-a-personal-device-to-your-organization"></a>Participer à un appareil personnel à votre organisation

## <a name="to-join-a-windows-10-device-to-your-organization"></a>Participer à un appareil Windows 10 à votre organisation

1.  Dans le menu **Démarrer** , sélectionnez **paramètres**.
2.  Sélectionnez **comptes**, puis cliquez sur **votre compte**.
3.  Cliquez sur **Ajouter compte scolaire ou**et tapez dans votre compte professionnel.
4.  Dans la page se connecter pour votre organisation, entrez votre nom d’utilisateur et mot de passe, puis cliquez sur **OK**.
5.  Vous demandera compliqué l’authentification multifacteur. (Ce défi est configurable par un administrateur informatique).
6.  Azure Active Directory (AD Azure) vérifie si le périphérique nécessite d’inscription de gestion des périphériques mobiles.
7.  Windows enregistre le périphérique dans l’annuaire de l’organisation dans Azure AD et s’inscrit dans la gestion des appareils mobiles, le cas échéant.
8.  Si vous êtes un utilisateur géré, Windows vous permet d’accéder au bureau via l’automatique se connecter.
9.  Si vous êtes un utilisateur fédéré, vous serez sur un écran de connexion Windows pour entrer vos informations d’identification.

## <a name="additional-information"></a>Informations supplémentaires
* [Windows 10 pour les entreprises : modalités d’utilisation des appareils pour le travail](active-directory-azureadjoin-windows10-devices-overview.md)
* [Étendre les fonctionnalités de cloud à des appareils Windows 10 par le biais Azure Active Directory rejoindre](active-directory-azureadjoin-user-upgrade.md)
* [Authentification des identités sans mot de passe via Microsoft Passport](active-directory-azureadjoin-passport.md)
* [Découvrez les scénarios d’utilisation pour Azure AD rejoindre](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Se connecter à un domaine appareils à Azure Active Directory pour Windows 10 expériences](active-directory-azureadjoin-devices-group-policy.md)
* [Configurer la Azure AD rejoindre](active-directory-azureadjoin-setup.md)
