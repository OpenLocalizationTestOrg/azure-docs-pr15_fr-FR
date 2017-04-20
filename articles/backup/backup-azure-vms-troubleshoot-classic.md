<properties
    pageTitle="Résoudre les problèmes de sauvegarde des machines virtuelles Azure | Microsoft Azure"
    description="Résoudre les problèmes de sauvegarde et restauration des machines virtuelles Azure"
    services="backup"
    documentationCenter=""
    authors="trinadhk"
    manager="shreeshd"
    editor=""/>

<tags
    ms.service="backup"
    ms.workload="storage-backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/26/2016"
    ms.author="trinadhk;jimpark;"/>


# <a name="troubleshoot-azure-virtual-machine-backup"></a>Résoudre les problèmes de sauvegarde des machines virtuelles Azure

> [AZURE.SELECTOR]
- [Archivage sécurisé des services de récupération](backup-azure-vms-troubleshoot.md)
- [Archivage sécurisé sauvegarde](backup-azure-vms-troubleshoot-classic.md)

Vous pouvez résoudre les erreurs produites lors de l’utilisation de sauvegardes Azure avec les informations figurant dans le tableau ci-dessous.

## <a name="discovery"></a>Découverte

| Opération de sauvegarde | Détails de l’erreur | Solution de contournement |
| -------- | -------- | -------|
| Découverte | Impossible de découvrir les nouveaux éléments - Microsoft Azure sauvegarde a rencontré et erreur interne. Patientez quelques minutes et puis recommencez l’opération. | Relancez le processus de détection après 15 minutes.
| Découverte | Impossible de découvrir les nouveaux éléments – découverte une autre opération est déjà en cours. Patientez jusqu'à ce que l’opération de découverte actuelle est terminée. | Aucun |

## <a name="register"></a>S’inscrire
| Opération de sauvegarde | Détails de l’erreur | Solution de contournement |
| -------- | -------- | -------|
| S’inscrire | Nombre de disques de données de la machine virtuelle dépasse la limite gérée - détacher certains disques de données sur cet ordinateur virtuel et recommencez l’opération. Sauvegarde Azure prend en charge jusqu'à 16 disques de données associés à une machine virtuelle Azure pour la sauvegarde | Aucun |
| S’inscrire | Microsoft Azure Backup a rencontré une erreur interne - attendre quelques minutes, puis essayez à nouveau l’opération. Si le problème persiste, contactez le Support Microsoft. | Vous pouvez obtenir cette erreur en raison d’une des configurations suivantes non prises en charge de la machine virtuelle sur LRS Premium. <br> Stockage Premium machines virtuelles peut être sauvegardé à l’aide de l’archivage sécurisé des services de récupération. [Pour en savoir plus](backup-introduction-to-azure-backup.md/#back-up-and-restore-premium-storage-vms) |
| S’inscrire | Échec de l’enregistrement avec le délai d’expiration de l’Agent installer opération | Vérifiez si la version du système d’exploitation de la machine virtuelle est prise en charge. |
| S’inscrire | L’exécution de commande a échoué - une autre opération est en cours sur cet élément. Patientez jusqu'à la fin de l’opération précédente | Aucun |
| S’inscrire | Machines virtuelles ayant des disques durs virtuels stockés sur le stockage Premium ne sont pas prises en charge pour la sauvegarde | Aucun |
| S’inscrire | Agent de machine virtuelle n’est pas présent sur la machine virtuelle - Veuillez installer la préalable indispensable, agent machine virtuelle et redémarrer l’opération. | [Pour en savoir plus](#vm-agent) sur l’installation de l’agent machine virtuelle et comment faire pour valider l’installation de l’agent machine virtuelle. |

## <a name="backup"></a>Sauvegarde

| Opération de sauvegarde | Détails de l’erreur | Solution de contournement |
| -------- | -------- | -------|
| Sauvegarde | Impossible de communiquer avec l’agent machine virtuelle état instantané. Tâche de sub machine virtuelle instantané a expiré. -Veuillez consulter le guide de dépannage sur la façon de résoudre ce problème. | Cette erreur est levée si un problème avec l’Agent machine virtuelle ou accès réseau à l’infrastructure Azure est bloqué d’une certaine manière. Plus d’informations sur les [problèmes de débogage des instantané d’ordinateur virtuel](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md). <br> Si l’agent machine virtuelle n’est pas à l’origine des problèmes, puis redémarrez l’ordinateur virtuel. Dans certains cas un état incorrect de la machine virtuelle peut entraîner des problèmes et redémarrer la machine virtuelle réinitialise cette « état incorrect » |
| Sauvegarde | La sauvegarde a échoué avec une erreur interne - recommencez l’opération dans quelques minutes. Si le problème persiste, contactez le Support Microsoft | Vérifiez si actuellement un problème temporaire de l’accès au stockage de machine virtuelle. Vérifiez [L’état Azure](https://azure.microsoft.com/en-us/status/) pour déterminer si les questions par la suite au cluster/stockage/réseau dans la zone. Veuillez réessayer le problème de sauvegarde billet est réduit. |
| Sauvegarde | N’a pas pu effectuer l’opération comme machine virtuelle n’existe plus. | Sauvegarde ne peut pas être effectuée comme la machine virtuelle configurée pour la sauvegarde a été supprimée. Arrêter autre sauvegarde par accéder au mode éléments protégé, sélectionnez l’élément protégé et cliquez sur Désactiver la Protection. Vous pouvez conserver les données en sélectionnant l’option de données de sauvegarde conserver. Vous pouvez reprendre protection pour cet ordinateur virtuel en cliquant sur Configurer sur la protection de l’affichage des éléments enregistrés|
| Sauvegarde | Impossible d’installer les Services de récupération Azure extension sur l’élément sélectionné - Agent machine virtuelle est requis pour le poste de Services de récupération Azure. Installez l’agent machine virtuelle Azure, puis redémarrez l’opération d’enregistrement | <ol> <li>Vérifiez si l’agent machine virtuelle a été installé correctement. <li>Assurez-vous que l’indicateur sur la configuration de la machine virtuelle est correctement définie.</ol> [Pour en savoir plus](#validating-vm-agent-installation) sur l’installation de l’agent machine virtuelle et comment faire pour valider l’installation de l’agent machine virtuelle. |
| Sauvegarde | L’exécution de commande a échoué - une autre opération est en cours sur cet élément. Patientez jusqu'à la fin de l’opération précédente et recommencez | Une sauvegarde existante ou une tâche de restauration de la machine virtuelle est en cours d’exécution et une nouvelle tâche ne peut pas être démarrée pendant l’exécution de la tâche existante. |
| Sauvegarde | Extension Échec de l’installation de l’erreur « COM + n’a pas pu communiquer avec l’administrateur des transactions Microsoft Distributed | Cela signifie généralement que le service COM + ne fonctionne pas. Contacter le support technique Microsoft pour vous aider à résoudre ce problème. |
| Sauvegarde | Opération de capture instantanée a échoué avec l’erreur opération VSS « ce lecteur est verrouillé par le chiffrement de lecteur BitLocker. Vous devez déverrouiller ce lecteur le panneau de configuration. | Activer ou désactiver BitLocker pour tous les lecteurs de la machine virtuelle et observez si le problème VSS est résolu |
| Sauvegarde | Machines virtuelles ayant des disques durs virtuels stockés sur le stockage Premium ne sont pas prises en charge pour la sauvegarde | Aucun |
| Sauvegarde | Machine virtuelle Azure non trouvé. | Cela se produit lorsque la machine virtuelle principale est supprimée, mais la stratégie de sauvegarde continue à prendre une machine virtuelle effectuer la sauvegarde. Pour corriger cette erreur : <ol><li>Recréez la machine virtuelle avec les mêmes nom et le même nom de groupe de ressources [nom du service cloud] <br>(OR) <li> Désactiver la protection pour cet ordinateur virtuel afin que les sauvegardes suivantes ne seront pas déclenchées. </ol> |
| Sauvegarde | Agent de machine virtuelle n’est pas présent sur la machine virtuelle - Veuillez installer la préalable indispensable, agent machine virtuelle et redémarrer l’opération. | [Pour en savoir plus](#vm-agent) sur l’installation de l’agent machine virtuelle et comment faire pour valider l’installation de l’agent machine virtuelle. |

## <a name="jobs"></a>Tâches
| Opération | Détails de l’erreur | Solution de contournement |
| -------- | -------- | -------|
| Annuler le travail | Annulation n’est pas prise en charge pour ce type de tâche - Patientez jusqu'à ce que le travail est terminé. | Aucun |
| Annuler le travail | La tâche n’est pas dans un état peut être annulé - Patientez jusqu'à ce que le travail est terminé. <br>OR<br> La tâche sélectionnée n’est pas dans un état peut être annulé : attendez la fin du travail.| Dans toute probabilité, la tâche est presque terminée ; Patientez jusqu'à ce que le travail est terminé |
| Annuler le travail | Impossible d’annuler la tâche, car il n’est pas en cours - annulation est uniquement prise en charge pour les tâches qui sont en cours. Annulez le tentative sur une valeur de progression en tâche. | Cela se produit en raison d’un état temporaires. Attendez une minute et réessayez l’opération d’annulation |
| Annuler le travail | Impossible d’annuler la tâche - Patientez jusqu'à ce que la tâche se termine. | Aucun |


## <a name="restore"></a>Restaurer
| Opération | Détails de l’erreur | Solution de contournement |
| -------- | -------- | -------|
| Restaurer | Échec de la restauration avec l’erreur interne du Cloud | <ol><li>Service de nuage à laquelle vous voulez restaurer est configuré avec les paramètres DNS. Vous pouvez vérifier <br>$deployment = get-AzureDeployment - ServiceName « ServiceName% »-emplacement « Production » Get-AzureDns - DnsSettings $deployment. DnsSettings<br>S’il existe adresse configurée, cela signifie que les paramètres DNS sont configurés.<br> <li>Service de nuage à laquelle vous voulez restaurer est configuré avec adresse IP réservée et machines virtuelles existantes dans le service cloud sont dans un état arrêté.<br>Vous pouvez vérifier qu'un service cloud a réservé IP à l’aide de la suite applets de commande powershell :<br>$deployment = get-AzureDeployment - ServiceName « ServiceName% »-emplacement « Production » $ logicielle. ReservedIPName <br><li>Vous essayez de restaurer une machine virtuelle avec des configurations réseau spéciaux suivants au même service cloud. <br>-Machines virtuelles sous configuration d’équilibrage de charge (internes et externes)<br>-Machines virtuelles avec plusieurs adresses IP réservés<br>-Machines virtuelles avec plusieurs cartes réseau<br>Sélectionnez un nouveau service cloud dans l’interface utilisateur ou reportez-vous à [restaurer considérations](./backup-azure-restore-vms.md/#restoring-vms-with-special-network-configurations) pour les machines virtuelles avec des configurations réseau spécial</ol> |
| Restaurer | Le nom DNS sélectionné est déjà utilisé - Veuillez spécifier un autre nom DNS et essayez à nouveau. | Le nom DNS ici fait référence au nom du service cloud (généralement se terminant par. cloudapp.net). Il doit être unique. Si vous rencontrez cette erreur, vous devez choisir un autre nom machine virtuelle pendant la restauration. <br><br> Cette erreur s’affiche uniquement pour les utilisateurs du portail Azure. L’opération de restauration via PowerShell a réussi, car il restaure les disques uniquement et ne crée pas de la machine virtuelle. L’erreur soit confrontée lors de la machine virtuelle est explicitement que vous avez créée après restauration le disque. |
| Restaurer | La configuration réseau virtuel spécifié n’est pas correcte : spécifiez une configuration réseau virtuel différente et réessayez. | Aucun |
| Restaurer | Le service de nuage spécifié utilise une enquête réservé, ce qui ne correspond à la configuration de la machine virtuelle en cours de restauration - Veuillez spécifier un service de nuage différent qui n’utilise pas IP réservé ou choisir un autre point de récupération à restaurer à partir de. | Aucun |
| Restaurer | Service de nuage a atteint la limite du nombre de points de terminaison d’entrée, recommencez l’opération en spécifiant un service de nuage différent ou à l’aide d’un point de terminaison existant. | Aucun |
| Restaurer | Sauvegarde compte de stockage de l’archivage sécurisé et cible sont dans deux régions différentes : Assurez-vous que le compte de stockage spécifié dans restauration est dans la même région Azure en tant que l’archivage sécurisé sauvegarde. | Aucun |
| Restaurer | Compte de stockage spécifiée pour la restauration n’est pas pris en charge - comptes de stockage uniquement Basic/Standard avec localement redondantes ou paramètres de réplication redondants geo sont prises en charge. Sélectionnez un compte de stockage pris en charge | Aucun |
| Restaurer | Type de compte de stockage spécifiée pour la restauration n’est pas en ligne -, assurez-vous que le compte de stockage spécifié dans restauration est en ligne | Cela peut se produire en raison d’une erreur temporaire dans le stockage Azure ou en raison d’une panne. Choisissez un autre compte de stockage. |
| Restaurer | Quota de groupe de ressources a été atteinte - supprimer des groupes de ressources à partir d’Azure portal ou contactez le support Azure pour augmenter la limite. | Aucun |
| Restaurer | Sous-réseau sélectionné n’existe pas - sélectionnez un sous-réseau qui se trouve | Aucun |


## <a name="policy"></a>Stratégie
| Opération | Détails de l’erreur | Solution de contournement |
| -------- | -------- | -------|
| Créer des stratégies | Impossible de créer la stratégie - Réduisez les choix de rétention pour poursuivre la configuration de la stratégie. | Aucun |


## <a name="vm-agent"></a>Agent de machine virtuelle

### <a name="setting-up-the-vm-agent"></a>La configuration de l’Agent de machine virtuelle
En règle générale, l’Agent de machine virtuelle est déjà présent dans machines virtuelles créées à partir de la galerie Azure. Cependant, machines virtuelles qui sont déplacés du centres de données locale n’aurait pas l’Agent machine virtuelle est installé. Pour ces machines virtuelles, l’Agent machine virtuelle doit être installé explicitement. En savoir plus sur [l’installation de l’agent de machine virtuelle sur un ordinateur virtuel existant](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx).

Pour les machines virtuelles Windows :

- Téléchargez et installez l' [agent MSI](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Vous avez besoin des privilèges d’administrateur pour terminer l’installation.
- [Mettre à jour la propriété machine virtuelle](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) pour indiquer que l’agent est installé.

Pour les machines virtuelles Linux :

- Installer les dernières [Linux agent](https://github.com/Azure/WALinuxAgent) à partir de github.
- [Mettre à jour la propriété machine virtuelle](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) pour indiquer que l’agent est installé.


### <a name="updating-the-vm-agent"></a>Mise à jour de l’Agent de machine virtuelle
Pour les machines virtuelles Windows :

- Mise à jour de l’Agent de machine virtuelle est aussi simple que les [fichiers binaires Agent machine virtuelle](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)la réinstallation. Toutefois, vous devez vous assurer qu’aucune opération de sauvegarde ne s’exécute pendant que l’Agent machine virtuelle est mis à jour.

Pour les machines virtuelles Linux :

- Suivez les instructions de [Mise à jour Linux machine virtuelle Agent](../virtual-machines/virtual-machines-linux-update-agent.md).


### <a name="validating-vm-agent-installation"></a>Validation de l’installation de l’Agent de machine virtuelle
Comment faire pour vérifier la version de l’Agent machine virtuelle sur machines virtuelles Windows :

1. Ouvrez une session sur l’ordinateur virtuel Azure et accédez au dossier *C:\WindowsAzure\Packages*. Vous devriez trouver le fichier WaAppAgent.exe présenter.
2. Cliquez sur le fichier, accédez aux **Propriétés**et puis sélectionnez l’onglet **Détails** . Le champ Version du produit doit être 2.6.1198.718 ou une version ultérieure





