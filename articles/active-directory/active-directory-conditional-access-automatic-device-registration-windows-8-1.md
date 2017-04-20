<properties
    pageTitle="Configurer l’inscription automatique appareil pour les appareils Windows 8.1 joint au domaine | Microsoft Azure"
    description=" Étapes de configuration de stratégie de groupe pour les appareils Windows 8.1 à un domaine enregistrer automatiquement avec Azure AD. "
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
    ms.author="Markvi"/>

# <a name="configure-automatic-device-registration-for-windows-81-domain-joined-devices"></a>Configurer l’inscription automatique appareil pour les appareils Windows 8.1 joint au domaine

Vous pouvez utiliser une stratégie de groupe Active Directory pour configurer vos périphériques de domaine joint Windows 8.1 pour enregistrer automatiquement avec Azure AD. Pour configurer la stratégie de groupe, vous devez disposer au moins un ordinateur de Windows Server 2012 R2 ou Windows 8.1 de joints domaine avec la fonctionnalité de gestion des stratégies de groupe installée. Une fois que cette stratégie de groupe est activée pour votre domaine, tout utilisateur de domaine qui se connecte à l’ordinateur sera automatiquement et silencieusement enregistré avec un objet périphérique dans Azure Active Directory. Il y aura un objet de périphérique dans Azure AD pour chaque utilisateur enregistré du périphérique physique. Veillez à lire et d’appliquer les conditions préalables à partir de l’appareil l’enregistrement automatique avec des appareils Azure Active Directory for Windows Domain-Joined.

>[AZURE.NOTE]
 Pour voir dernières obtenir des instructions sur la configuration d’inscription automatique appareils mobiles, [configuration de l’enregistrement automatique du domaine Windows joint appareils avec Azure Active Directory](active-directory-conditional-access-automatic-device-registration-setup.md).



## <a name="configure-the-group-policy-for-your-windows-81-domain-joined-devices"></a>Configurer la stratégie de groupe pour votre domaine joint les appareils Windows 8.1

1. Ouvrez le Gestionnaire de serveur et accédez à **Outils** > **Gestion des stratégies de groupe**.
2. À partir de gestion des stratégies de groupe, naviguez vers le nœud de domaine qui correspond au domaine dans lequel vous souhaitez activer **Jointure poste de travail automatique**.
3. Droit **d’Objets stratégie de groupe** , puis sélectionnez **Nouveau**. Nommez votre objet de stratégie de groupe, par exemple, **Automatique rejoindre d’espace de travail**. Cliquez sur **OK**.
4. Avec le bouton droit sur votre nouvel objet de stratégie de groupe, puis sélectionnez **Modifier**.
5. Accédez à la **Configuration de l’ordinateur** > **stratégies** > **modèles d’administration** > **composants Windows** > **rejoindre d’espace de travail**.
6. Clic droit automatiquement les ordinateurs clients de jointure poste de travail, puis sélectionnez **Modifier**.
7. Sélectionnez la case activé, puis sur Appliquer. Cliquez sur **OK**.
8. Vous pouvez désormais lier l’objet de stratégie de groupe à l’emplacement de votre choix. Pour activer cette stratégie pour tous les appareils Windows 8.1 joint au domaine auprès de votre organisation, lier la stratégie de groupe pour le domaine.

## <a name="unregistering-your-windows-81-domain-joined-devices"></a>Annuler l’inscription de votre domaine Windows 8.1 rejoint appareils

Vous pouvez choisir d’annuler l’enregistrement de vos appareils Windows 8.1 domaine joint en procédant comme suit : modifier les paramètres de l’espace de travail participer à une stratégie de groupe créés dans la section précédente. Définir l’automatiquement poste de travail jointure ordinateurs stratégie de client sur désactivé. Cela empêchera nouveaux périphériques de rejoindre automatiquement l’espace de travail.

Unregister les ordinateurs Windows 8.1 joint au domaine existants en effectuant l’une des deux options suivantes :

* Option 1 : **Unregister un Windows 8.1 domaine appareil à l’aide des paramètres du PC**
  1. Sur l’appareil Windows 8.1, accédez aux **Paramètres du PC** > **réseau** > **espace de travail**
  2. Sélectionnez **Quitter**.
Ce processus doit être répété pour chaque utilisateur de domaine qui est connecté à l’ordinateur et a été automatiquement rejoint un espace de travail.

* Option 2 : Annuler l’inscription d’un appareil Windows 8.1 de joints domaine à l’aide d’un script
    1. Ouvrez une invite de commande sur l’ordinateur Windows 8.1 et exécutez la commande suivante :` %SystemRoot%\System32\AutoWorkplace.exe leave`
   
Cette commande doit être exécutée sur l’ordinateur dans le contexte de chaque utilisateur du domaine qui a signé.

##<a name="event-viewer--errors-for-windows-81-domain-joined-devices"></a>Appareils joint au domaine Observateur d’événements et erreurs pour Windows 8.1

Le journal des événements Windows sur un ordinateur Windows 8.1 affiche les messages relatifs à l’enregistrement de dispositifs. Vous trouverez des messages pour les événements réussies ou non. 

Le journal des événements sont accessibles dans l’événement visionneuse sous Services **les journaux**des Applications et > **Microsoft** > **Windows > espace de travail rejoindre**.

##<a name="additional-details"></a>Détails supplémentaires

La stratégie de groupe permet à une tâche planifiée sur le système qui s’exécute dans le contexte de l’utilisateur et déclenché à la connexion utilisateur. La tâche silencieusement enregistre l’utilisateur et l’appareil avec Azure AD une fois la connexion terminée. La tâche planifiée, vous pouvez trouver sur les appareils Windows 8.1 dans la bibliothèque du Planificateur de tâches sous **Microsoft** > **Windows** > **Rejoindre d’espace de travail**. La tâche s’exécute et inscrire tous les utilisateurs Active Directory que vous connecter à. 

## <a name="additional-topics"></a>Autres rubriques
- [Vue d’ensemble d’inscription pour appareils Active Directory Azure](active-directory-conditional-access-device-registration-overview.md)
- [Enregistrement de dispositif automatique avec des appareils à un domaine Azure Active Directory pour Windows 10](active-directory-conditional-access-automatic-device-registration.md)
- [Configurer l’enregistrement du dispositif automatique pour appareils joint au domaine Windows 7](active-directory-conditional-access-automatic-device-registration-windows7.md)

