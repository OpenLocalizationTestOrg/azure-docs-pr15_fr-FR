<properties
    pageTitle="Configurer un appareil Windows 10 avec Azure AD à partir des paramètres | Microsoft Azure"
    description="Explique comment les utilisateurs peuvent participer à Azure Active Directory via le menu Paramètres."
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

# <a name="set-up-a-windows-10-device-with-azure-ad-from-settings"></a>Configurer un appareil Windows 10 avec Azure AD à partir des paramètres
Si vous utilisez déjà Windows 7 ou Windows 8 et que votre ordinateur ou appareil a été mis à niveau vers Windows 10, vous pouvez joindre à Azure Active Directory (AD Azure) via le menu Paramètres.

## <a name="to-join-to-azure-ad-from-the-settings-menu"></a>Pour joindre à Azure AD dans le menu Paramètres


1. Dans le menu **Démarrer** , cliquez sur l’icône **paramètres** .
2. Dans **paramètres**, sélectionnez **système**->**sur**->**rejoindre Azure AD**.
<center>
![Participer à Azure AD dans le menu paramètres](./media/active-directory-azureadjoin/active-directory-azureadjoin-settings.png)</center>

3. Cliquez sur **Continuer** dans la fenêtre de message Azure AD rejoindre.
<center>
![Fenêtre de message jointure Azure AD](./media/active-directory-azureadjoin/active-directory-azureadjoin-message.png)</center>
4. Fournir vos informations d’identification se connecter. Cette expérience de connexion doit inclure toutes les étapes nécessaires à l’authentification complète. Si vous faites partie d’un client fédéré, votre administrateur vous fournira l’expérience de fédération est hébergé par votre organisation.
<center>
![Fournir des informations d’identification de connexion à](./media/active-directory-azureadjoin/active-directory-azureadjoin-sign-in.png)</center>
5. Si votre organisation a configuré l’authentification multifacteur Azure pour joindre à Azure Active Directory, fournissent le second facteur avant de poursuivre.
6. Cliquez sur **Accepter** dans l’écran **Autoriser cet appareil à gérer** .
7. Vous devriez voir le message « votre périphérique est maintenant joint à votre organisation dans Azure AD ».


## <a name="additional-information"></a>Informations supplémentaires
* [Découvrez les scénarios d’utilisation pour Azure AD rejoindre](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Se connecter à un domaine appareils à Azure Active Directory pour Windows 10 expériences](active-directory-azureadjoin-devices-group-policy.md)
* [Configurer la Azure AD rejoindre](active-directory-azureadjoin-setup.md)
* [Authentification des identités sans mot de passe via Microsoft Passport](active-directory-azureadjoin-passport.md)
