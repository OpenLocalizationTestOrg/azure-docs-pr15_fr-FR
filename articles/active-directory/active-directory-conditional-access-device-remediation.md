<properties
    pageTitle="Résolution des problèmes d’accès Azure Active Directory | Microsoft Azure"
    description="Découvrez les étapes que vous pouvez suivre pour résoudre les problèmes d’accès avec les ressources en ligne de votre organisation."
    services="active-directory"
    keywords="accès conditionnelle basé sur un appareil, d’inscription pour appareils, activer enregistrer et périphériques d’inscription appareils mobiles et la gestion des périphériques"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/23/2016"
    ms.author="markvi"/>


# <a name="troubleshooting-for-azure-active-directory-access-issues"></a>Résolution des problèmes d’accès Azure Active Directory

Vous essayez d’accéder à SharePoint Online intranet votre organisation, et vous obtenez un message d’erreur « accès refusé ». Que fais-tu ?

Cet article décrit les étapes de mise à jour qui peuvent vous aider à résoudre les problèmes d’accès avec les ressources en ligne de votre organisation.

Pour vous aider à résoudre Azure Active Directory (AD Azure) accéder aux problèmes, accédez à la section de l’article qui recouvre votre plateforme de l’appareil :

-   Appareil Windows
-   sur un appareil iOS (vérifier précédent bientôt pour résoudre les iPhone et iPad.)
-   Appareil Android (revérifier plus rapidement pour obtenir de l’aide Android téléphones et tablettes.)

## <a name="access-from-a-windows-device"></a>Accès à partir d’un appareil Windows

Si votre appareil exécute une des plates-formes suivantes, rechercher dans les sections suivantes pour le message d’erreur qui s’affiche lorsque vous essayez d’accéder à une application ou un service :

- Windows 10
- Windows 8.1
- Windows 8
- Windows 7
- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2

### <a name="device-is-not-registered"></a>Appareil n’est pas enregistré

Si votre appareil n’est pas enregistré avec Azure Active Directory et l’application est protégée par une stratégie basée sur un appareil, une page qui affiche l’un de ces messages d’erreur peut s’afficher :

![« Vous ne pouvez pas y accéder à partir de là » des messages pour les appareils non enregistrés] (./media/active-directory-conditional-access-device-remediation/01.png "Scénario")

Si votre périphérique est à un domaine à Active Directory de votre organisation, procédez comme suit :

1.  Vérifiez que vous êtes connecté à Windows à l’aide de votre compte professionnel (votre compte Active Directory).
2.  Se connecter à votre réseau d’entreprise via un réseau privé virtuel (VPN) ou DirectAccess.
3.  Une fois que vous êtes connecté, appuyez sur la touche du logo Windows + la touche L pour verrouiller votre session Windows.
4.  Entrez vos informations d’identification du compte de travail pour déverrouiller votre session Windows.
5.  Attendez une minute, puis réessayez d’accéder à l’application ou le service.
6.  Si vous voyez la même page, cliquez sur le lien **obtenir plus d’informations** et puis contactez votre administrateur avec les détails.

Si votre appareil n’est pas à un domaine et s’exécute Windows 10, vous avez deux possibilités :

- Exécuter Azure AD jointure
- Ajouter votre compte professionnel ou scolaire à Windows

Pour plus d’informations sur la façon dont ces options sont différentes, voir les [périphériques à l’aide de Windows 10 dans votre espace de travail](active-directory-azureadjoin-windows10-devices.md).

Pour exécuter Azure AD rejoindre, procédez comme suit pour la plateforme de sur que votre appareil s’exécute. (Azure AD jointure n’est pas disponible sur les téléphones Windows).

**Mise à jour de Windows 10 l’anniversaire de mariage**

1.  Ouvrez l’application de **paramètres** .
2.  Cliquez sur **comptes** > **accès Professionnel ou scolaire**.
3.  Cliquez sur **se connecter**.
4.  Cliquez sur **participer à cet appareil à Azure Active Directory**.
5.  S’authentifier à votre organisation, fournissent l’authentification multifacteur si vous y êtes invité, puis suivez les étapes qui sont affichent.
6.  Déconnectez-vous, puis connectez-vous avec votre compte professionnel.
7.  Réessayez d’accéder à l’application.


**Mise à jour de Windows 10 novembre 2015**

1.  Ouvrez l’application de **paramètres** .
2.  Cliquez sur **système** > **sur**.
3.  Cliquez sur **joindre Azure AD**.
4.  S’authentifier à votre organisation, fournissent l’authentification multifacteur si vous y êtes invité, puis suivez les étapes qui sont affichent.
5.  Déconnectez-vous, puis connectez-vous avec votre compte professionnel (votre compte Azure AD).
6.  Réessayez d’accéder à l’application.

Pour ajouter votre compte professionnel ou scolaire, procédez comme suit :

**Mise à jour de Windows 10 l’anniversaire de mariage**

1.  Ouvrez l’application de **paramètres** .
2.  Cliquez sur **comptes** > **accès Professionnel ou scolaire**.
3.  Cliquez sur **se connecter**.
4.  S’authentifier à votre organisation, fournissent l’authentification multifacteur si vous y êtes invité, puis suivez les étapes qui sont affichent.
5.  Réessayez d’accéder à l’application.


**Mise à jour de Windows 10 novembre 2015**

1.  Ouvrez l’application de **paramètres** .
2.  Cliquez sur **comptes** > **vos comptes**.
3.  Cliquez sur **Ajouter un travail compte professionnel ou scolaire**.
4.  S’authentifier à votre organisation, fournissent l’authentification multifacteur si vous y êtes invité, puis suivez les étapes qui sont affichent.
5.  Réessayez d’accéder à l’application.

Si votre appareil n’est pas à un domaine et s’exécute Windows 8.1, pour effectuer une jointure poste de travail et s’inscrire à Microsoft Intune, procédez comme suit :

1.  Ouvrez **paramètres du PC**.
2.  Cliquez sur **réseau** > **espace de travail**.
3.  Cliquez sur **joindre**.
4.  S’authentifier à votre organisation, fournissent l’authentification multifacteur si vous y êtes invité, puis suivez les étapes qui sont affichent.
5.  Cliquez sur **Activer**.
6.  Réessayez d’accéder à l’application.


### <a name="browser-is-not-supported"></a>Navigateur n’est pas pris en charge

Vous avez peut être interdit si vous essayez d’accéder à une application ou un service en utilisant l’un des navigateurs suivants :

- Chrome, Firefox ou tout autre navigateur autre que Microsoft Edge ou Microsoft Internet Explorer dans Windows 10 ou Windows Server 2016
- Firefox dans Windows 8.1, Windows 7, Windows Server 2012 R2, Windows Server 2012 ou Windows Server 2008 R2

Vous verrez une page d’erreur qui ressemble à ceci :

![Message « Vous ne pouvez pas y accéder à partir de là » pour les navigateurs non pris en charge] (./media/active-directory-conditional-access-device-remediation/02.png "Scénario")

La mise à jour uniquement consiste à utiliser un navigateur qui prend en charge de l’application pour votre plateforme de l’appareil.

## <a name="next-steps"></a>Étapes suivantes

[Accès à conditionnelle Azure Active Directory](active-directory-conditional-access.md)
