<properties
   pageTitle="Azure AD Connect : Mise à niveau automatique | Microsoft Azure"
   description="Cette rubrique décrit la fonctionnalité de mise à niveau automatique intégrée dans Azure AD Connect synchroniser."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/24/2016"
   ms.author="billmath"/>

# <a name="azure-ad-connect-automatic-upgrade"></a>Azure AD Connect : Mise à niveau automatique
Cette fonctionnalité a été introduite avec génération 1.1.105.0 (publiée février 2016).

## <a name="overview"></a>Vue d’ensemble
En prenant soin de que votre installation Azure AD Connect n’est toujours à jour n’a jamais été plus simple avec la fonctionnalité de **mise à niveau automatique** . Cette fonctionnalité est activée par défaut pour les installations express et mises à niveau de la synchronisation d’annuaire. Lorsqu’une nouvelle version est disponible, votre installation est automatiquement mis à niveau.

Mise à niveau automatique est activé par défaut pour les éléments suivants :

- Express paramètres installation et mises à niveau de la synchronisation d’annuaire.
- À l’aide de SQL Express LocalDB, qui est ce que paramètres Express toujours utilisent. Synchronisation d’annuaire avec SQL Express également utiliser LocalDB.
- Le compte Active Directory est le compte MSOL_ par défaut créé par les paramètres de Express et de synchronisation d’annuaire.
- Avoir moins de 100 000 objets dans le méta-verse.

L’état actuel de mise à niveau automatique peut être affiché avec l’applet de commande PowerShell `Get-ADSyncAutoUpgrade`. Il comporte les états suivants :

État | Commentaire
---- | ----
Activé | Mise à niveau automatique est activée.
Suspendue | Défini par le système uniquement. Le système n’est plus autorisé à recevoir des mises à jour automatiques.
Désactivé | Mise à niveau automatique est désactivé.

Vous pouvez modifier entre **activé** et **désactivé** avec `Set-ADSyncAutoUpgrade`. Seul le système doit définir l’état **suspendu**.

Mise à niveau automatique utilise la santé connecter Azure AD pour l’infrastructure de mise à niveau. Mise à niveau automatique pour l’utiliser, vérifiez que vous avez ouvert les URL de votre serveur proxy pour **La santé Azure AD se connecter** en procédant comme décrit dans [Office 365 URL et plages d’adresses IP](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

Si le **Gestionnaire de services de synchronisation** de l’interface utilisateur s’exécute sur le serveur, la mise à niveau est suspendu jusqu'à ce que l’interface utilisateur est fermé.

## <a name="troubleshooting"></a>Résolution des problèmes
Si votre installation de se connecter ne met pas niveau lui-même comme prévu, puis procédez comme suit pour déterminer quel peut-être le problème.

Tout d’abord, vous ne recevrez pas la mise à niveau automatique à essayer le premier jour qu'une nouvelle version est disponible. Il existe un caractère aléatoire intentionnellement avant la tentative d’une mise à niveau afin de ne vous inquiétez pas si votre installation n’est pas mis à niveau immédiatement.

Si vous pensez que quelque chose ne convient pas, puis tout d’abord exécuter `Get-ADSyncAutoUpgrade` pour garantir la mise à niveau automatique est activée.

Ensuite, vérifiez que vous avez ouvert l’URL requis dans votre proxy ou un pare-feu. Mise à jour automatique utilise Azure AD connecter santé comme décrit dans [vue d’ensemble](#overview). Si vous utilisez un serveur proxy, assurez-vous que la santé a été configurée pour utiliser un [serveur proxy](active-directory-aadconnect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy). Également tester la [connectivité au niveau](active-directory-aadconnect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) à Azure AD.

Avec la connectivité à Azure AD vérifié, il est temps de vous intéresser dans les journaux d’événements. Démarrer l’Observateur d’événements et consultez le journal des événements **Application** . Ajouter un filtre de journal des événements pour la source **Azure AD connecter mise à niveau** et l’id d’événement plage **300-399**.  
![Filtre de journal des événements pour la mise à niveau automatique](./media/active-directory-aadconnect-feature-automatic-upgrade/eventlogfilter.png)  

Vous pouvez désormais voir les journaux d’événements associé à l’état de mise à niveau automatique.  
![Filtre de journal des événements pour la mise à niveau automatique](./media/active-directory-aadconnect-feature-automatic-upgrade/eventlogresult.png)  

Le code de résultat a un préfixe avec une vue d’ensemble de l’état.

Préfixe du code de résultat | Description
--- | ---
Opération réussie | L’installation a été mis à niveau.
UpgradeAborted | Un problème temporaire arrêté la mise à niveau. Il sera retentée à nouveau et l’attente est qu’il a réussi ultérieurement.
UpgradeNotSupported | Le système possède une configuration qui bloque le système automatiquement mis à niveau. Il sera retentée pour voir si la modification de l’état, mais l’objectif est que le système doit être mis à jour manuellement.

Voici une liste de messages les plus courants que vous recherchez. Il ne répertorie pas tous, mais le message de résultat doit être clair avec quel problème est.

Message de résultat | Description
--- | ---
**UpgradeAborted** |
UpgradeAbortedCouldNotSetUpgradeMarker | N’a pas pu écrire dans le Registre.
UpgradeAbortedInsufficientDatabasePermissions | Le groupe Administrateurs intégrés n’a pas les autorisations pour la base de données. Manuellement mettre à niveau vers la dernière version d’Azure AD Connect pour résoudre ce problème.
UpgradeAbortedInsufficientDiskSpace | Il n’est pas suffisamment d’espace disque pour prendre en charge d’une mise à niveau.
UpgradeAbortedSecurityGroupsNotPresent | Impossible de trouver et résoudre tous les groupes de sécurité utilisées par le moteur de synchronisation.
UpgradeAbortedServiceCanNotBeStarted | Impossible de démarrer le Service NT **Microsoft Azure AD Sync** .
UpgradeAbortedServiceCanNotBeStopped | Impossible d’arrêter le Service NT **Microsoft Azure AD Sync** .
UpgradeAbortedServiceIsNotRunning | Le Service NT **Microsoft Azure AD Sync** ne fonctionne pas.
UpgradeAbortedSyncCycleDisabled | L’option SyncCycle dans le [Planificateur](active-directory-aadconnectsync-feature-scheduler.md) a été désactivée.
UpgradeAbortedSyncExeInUse | Le [Gestionnaire de service de synchronisation l’interface utilisateur](active-directory-aadconnectsync-service-manager-ui.md) est ouvert sur le serveur.
UpgradeAbortedSyncOrConfigurationInProgress | L’Assistant installation est en cours d’exécution ou une synchronisation a été planifiée en dehors du planificateur.
**UpgradeNotSupported** |
UpgradeNotSupportedCustomizedSyncRules | Vous avez ajouté vos propres règles personnalisées à la configuration.
UpgradeNotSupportedDeviceWritebackEnabled | Vous avez activé la fonctionnalité [d’écriture différée appareil](active-directory-aadconnect-feature-device-writeback.md) .
UpgradeNotSupportedGroupWritebackEnabled | Vous avez activé la fonctionnalité [d’écriture différée du groupe](active-directory-aadconnect-feature-preview.md#group-writeback) .
UpgradeNotSupportedInvalidPersistedState | L’installation n’est pas des paramètres de Express ou une mise à niveau de la synchronisation d’annuaire.
UpgradeNotSupportedMetaverseSizeExceeeded | Vous avez plus de 100 000 objets dans le méta-verse.
UpgradeNotSupportedMultiForestSetup | Vous vous connectez à plus d’une forêt. Installation rapide se connecte uniquement à une forêt.
UpgradeNotSupportedNonLocalDbInstall | Vous n’utilisez pas une base de données SQL Server Express LocalDB.
UpgradeNotSupportedNonMsolAccount | Le [compte de connecteur Active Directory](active-directory-aadconnect-accounts-permissions.md#active-directory-account) n’est pas plus le compte MSOL_ par défaut.
UpgradeNotSupportedStagingModeEnabled | Le serveur est configuré pour être en [mode de mise en attente](active-directory-aadconnectsync-operations.md#staging-mode).
UpgradeNotSupportedUserWritebackEnabled | Vous avez activé la fonctionnalité [d’écriture différée utilisateur](active-directory-aadconnect-feature-preview.md#user-writeback) .

## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur [l’intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md).
