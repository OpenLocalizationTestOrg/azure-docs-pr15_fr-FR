<properties
    pageTitle="Configurer un nouvel appareil avec Azure AD pendant l’installation | Microsoft Azure"
    description="Rubrique explique comment les utilisateurs peuvent configurer Azure AD rejoindre pendant sa première expérience de l’exécution."
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

# <a name="set-up-a-new-device-with-azure-ad-during-setup"></a>Configurer un nouvel appareil avec Azure Active Directory lors de l’installation

Dans Windows 10, les utilisateurs peuvent participer à leurs appareils à Azure Active Directory (AD Azure) dans le premier démarrage (FRX). Cela permet aux organisations de distribuer des appareils rétractable pour leurs employés ou les étudiants ou lui permettre de choisir leurs propres périphériques (CYOD).
Si les éditions Windows 10 Professional ou Windows 10 entreprise est installée sur un appareil, l’expérience par défaut est le processus de configuration pour les périphériques appartenant à l’entreprise.

## <a name="to-join-a-device-to-azure-ad"></a>Participer à un appareil à Azure Active Directory


1. Lorsque vous activez votre nouvel appareil et démarrez le processus d’installation, vous devriez voir le message **Prise prêt** . Suivez les invites pour configurer votre appareil.
2. Commencez par la personnalisation de votre région et langue. Puis acceptez les termes du contrat de licence logiciel Microsoft.
![Personnaliser votre région](./media/active-directory-azureadjoin/active-directory-azureadjoin-customize-region.png)
3. Sélectionnez le réseau que vous voulez utiliser pour la connexion à Internet.
4. Indiquez si vous utilisez un appareil personnel ou un appareil appartenant à l’entreprise. S’il s’agit de société, cliquez sur **cet appareil appartient à mon organisation**. Cette opération démarre l’expérience Azure AD rejoindre. Voici un écran que vous voyez si vous utilisez Windows 10 Professionnel.
<center>
![Qui est responsable de l’écran de cet ordinateur](./media/active-directory-azureadjoin/active-directory-azureadjoin-who-owns-pc.png)

5.  Entrez les informations d’identification qui ont été fournies par votre organisation.
<center>
![Écran de connexion](./media/active-directory-azureadjoin/active-directory-azureadjoin-sign-in.png)
6.  Une fois que vous avez entré votre nom d’utilisateur, un client correspondant se trouve dans Azure AD. Si vous êtes dans un domaine fédéré, vous êtes redirigé à votre serveur de Service (jeton de sécurité) en local, par exemple, Active Directory Federation Services (AD FS).
7. Si vous êtes un utilisateur dans un domaine non fédéré, entrez vos informations d’identification directement sur la page Azure AD-hébergé. Si la marque de l’entreprise a été configuré, vous sera également voir logo de votre organisation et en charge le texte.
8.  Vous êtes invité à compliqué l’authentification multifacteur. Ce problème peut être configuré par un administrateur informatique.
9.  Azure AD vérifie si cet utilisateur/appareil requiert l’inscription de gestion des appareils mobiles.
10. Windows enregistre le périphérique dans l’annuaire de l’organisation dans Azure AD et s’inscrit dans la gestion des appareils mobiles, le cas échéant.
11. Si vous êtes un utilisateur géré, Windows vous permet d’accéder au bureau via le processus de connexion automatique.
12. Si vous êtes un utilisateur fédéré, vous êtes invité à entrer vos informations d’identification à l’écran de connexion Windows.

> [AZURE.NOTE] Rejoindre un domaine Active Directory Windows Server en local dans l’expérience de prédéfinies Windows n’est pas pris en charge. Par conséquent, si vous souhaitez joindre un ordinateur à un domaine, vous devez sélectionner le lien **configurer Windows avec un compte local** à la place. Vous pouvez puis joignez le domaine à partir des paramètres sur votre ordinateur que vous avez terminé avant.

## <a name="additional-information"></a>Informations supplémentaires
* [Windows 10 pour les entreprises : modalités d’utilisation des appareils pour le travail](active-directory-azureadjoin-windows10-devices-overview.md)
* [Étendre les fonctionnalités de cloud à des appareils Windows 10 par le biais Azure Active Directory rejoindre](active-directory-azureadjoin-user-upgrade.md)
* [Authentification des identités sans mot de passe via Microsoft Passport](active-directory-azureadjoin-passport.md)
* [Découvrez les scénarios d’utilisation pour Azure AD rejoindre](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Se connecter à un domaine appareils à Azure Active Directory pour Windows 10 expériences](active-directory-azureadjoin-devices-group-policy.md)
* [Configurer la Azure AD rejoindre](active-directory-azureadjoin-setup.md)
