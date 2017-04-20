<properties
    pageTitle="# Configurer l’enregistrement du dispositif automatique pour appareils joint au domaine Windows 7 | Microsoft Azure"
    description="Étapes à suivre pour configurer votre domaine Windows 7 joint appareils enregistrer automatiquement avec Azure AD. et les étapes nécessaires pour déployer le package de logiciel appareil d’inscription à votre domaine Windows 7 joint appareils à l’aide d’un système de distribution des logiciels tels que le Gestionnaire de Configuration de System Center."
    services="active-directory"
    documentationCenter=""
    authors="femila"
    manager="swadhwa"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/21/2016"
    ms.author="MarkVi"/>

# <a name="configure-automatic-device-registration-for-windows-7-domain-joined-devices"></a>Configurer l’enregistrement du dispositif automatique pour appareils joint au domaine Windows 7

En tant qu’administrateur informatique, vous pouvez configurer vos périphériques de domaine joint Windows 7 pour enregistrer automatiquement avec Azure AD. Pour ce faire, vous devez déployer l’ensemble de logiciels d’inscription appareil à votre domaine Windows 7 jointes appareils à l’aide d’un système de distribution des logiciels tels que le Gestionnaire de Configuration de System Center. Veillez à lire et d’appliquer les conditions préalables répertoriées dans l’enregistrement appareil automatique avec des appareils Azure Active Directory pour Windows à un domaine.

>[AZURE.NOTE]
 Pour voir dernières obtenir des instructions sur la configuration d’inscription automatique appareils mobiles, [configuration de l’enregistrement automatique du domaine Windows joint appareils avec Azure Active Directory](active-directory-conditional-access-automatic-device-registration-setup.md).

##<a name="installing-the-device-registration-software-package-on-windows-7-domain-joined-devices"></a>Installation des logiciels appareil d’inscription sur Windows 7 domaine rejoint appareils

Enregistrement d’appareil pour Windows 7 n’est disponible en tant que [package MSI téléchargeable](https://connect.microsoft.com/site1164). Le package doit être installé sur des ordinateurs Windows 7 qui sont joints à un domaine Active Directory. Vous devez déployer le package en utilisant un système de distribution des logiciels tels que le Gestionnaire de Configuration de System Center. Le package MSI prend en charge les options d’installation en mode silencieux standard à l’aide de /quiet paramètre.
Le logiciel est disponible au téléchargement sur le [site Web Microsoft Connect](https://connect.microsoft.com/site1164). Ici, vous pouvez sélectionner et télécharger puis joindre poste de travail pour Windows 7.

![](./media/active-directory-conditional-access/device-registration-process-windows7.gif)

## <a name="workplace-join-with-azure-active-directory"></a>Jointure poste de travail avec Azure Active Directory
D’inscription appareils mobiles pour les appareils Windows 7 joint au domaine ne nécessitent ni inclure une interface utilisateur. Une fois installé sur l’ordinateur, tout utilisateur de domaine qui se connecte à l’ordinateur sera automatiquement et silencieusement enregistré avec un objet périphérique dans Azure Active Directory. Il y aura un objet de périphérique dans Azure AD pour chaque utilisateur enregistré du périphérique physique.

Le programme d’installation crée une tâche planifiée sur le système qui s’exécute dans le contexte de l’utilisateur et déclenché à la connexion utilisateur. La tâche inscrit silencieusement l’utilisateur et appareil avec Azure AD après signes de l’utilisateur est terminée.
La tâche planifiée se trouve dans la bibliothèque du Planificateur de tâches sous **Microsoft** > **Rejoindre d’espace de travail**.
La tâche s’exécute et enregistrer toutes les utilisateurs Active Directory qui se connecter à l’ordinateur.
L’illustration suivante répertorie le processus étape par étape pour l’enregistrement automatique.

![](./media/active-directory-conditional-access/automatic-device-registration-windows7.png)

1. Un utilisateur (travailleurs de l’information) ouvre une session sur un ordinateur client Windows 7 à l’aide des informations d’identification de domaine Active Directory.
1. L’espace de travail participer à une tâche planifiée est exécutée.
1. L’utilisateur est authentifié en mode silencieux avec AD FS à l’aide de l’authentification Windows intégrée.
1. Le PC Windows 7 est enregistré à l’utilisateur dans Active Directory Azure.
1. Un objet périphérique et un certificat est créé dans Azure AD. L’objet représente le user@device.
1. Le certificat rejoindre d’espace de travail est stocké sur l’ordinateur.

## <a name="unregistering-your-windows-7-domain-joined-devices"></a>Annuler l’inscription de votre domaine Windows 7 rejoint appareils

Vous pouvez choisir d’annuler l’enregistrement de vos périphériques de Windows 7 domaine joint en procédant comme suit : désinstaller la jointure par le logiciel à partir de votre domaine Windows 7 rejoint appareils à l’aide d’un système de distribution des logiciels tels que le Gestionnaire de Configuration de System Center.

Ensuite, ouvrez une invite de commandes sur l’ordinateur Windows 7 et exécutez la commande suivante pour annuler l’inscription de l’appareil :

    %ProgramFiles%\Microsoft Workplace Join\AutoWorkplace.exe /leave

>[AZURE.NOTE]
>Cette commande doit être exécutée sur l’ordinateur dans le contexte de chaque utilisateur du domaine qui a signé.
Appareils joint au domaine Observateur d’événements et erreurs pour Windows 7.

Le journal des événements Windows sur l’ordinateur Windows 7 affiche les messages relatifs à l’espace de travail rejoindre. Vous pouvez rechercher des messages pour les événements poste de travail rejoindre réussies ou non. Le journal des événements sont accessibles dans l’événement visionneuse sous journaux des Applications et Services > espace de travail Microsoft rejoindre.

## <a name="additional-topics"></a>Autres rubriques

- [Vue d’ensemble d’inscription pour appareils Active Directory Azure](active-directory-conditional-access-device-registration-overview.md)
- [Enregistrement de dispositif automatique avec des appareils Azure Active Directory for Windows Domain-Joined](active-directory-conditional-access-automatic-device-registration.md)
- [Configurer l’inscription automatique appareil pour les appareils Windows 8.1 joint au domaine](active-directory-conditional-access-automatic-device-registration-windows-8-1.md)
- [Enregistrement de dispositif automatique avec des appareils à un domaine Azure Active Directory pour Windows 10](active-directory-azureadjoin-devices-group-policy.md)
