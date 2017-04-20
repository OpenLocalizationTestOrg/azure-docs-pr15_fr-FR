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

## <a name="backup"></a>Sauvegarde

| Opération de sauvegarde | Détails de l’erreur | Solution de contournement |
| -------- | -------- | -------|
|Sauvegarde|    N’a pas pu effectuer l’opération comme machine virtuelle n’existe plus. -Arrêter la protection de machine virtuelle sans supprimer les données de sauvegarde. Plus de détails au http://go.microsoft.com/fwlink/?LinkId=808124   |Cela se produit lorsque la machine virtuelle principale est supprimée, mais la stratégie de sauvegarde continue à prendre une machine virtuelle effectuer la sauvegarde. Pour corriger cette erreur : <ol><li> Recréez la machine virtuelle avec les mêmes nom et le même nom de groupe de ressources [nom du service cloud]<br>(OR)</li><li> Arrêter la protection de machine virtuelle avec ou sans supprimer les données de sauvegarde. [Obtenir plus d’informations](http://go.microsoft.com/fwlink/?LinkId=808124)</li></ol>|
|Sauvegarde|Impossible de communiquer avec l’agent machine virtuelle état instantané. -S’assurer que machine virtuelle a accès à internet. En outre, mettre à jour l’agent machine virtuelle comme indiqué dans le guide de dépannage en http://go.microsoft.com/fwlink/?LinkId=800034 |Cette erreur est levée si un problème avec l’Agent machine virtuelle ou accès réseau à l’infrastructure Azure est bloqué d’une certaine manière. Pour en savoir plus sur le débogage la machine virtuelle instantané problèmes.<br> Si l’agent machine virtuelle n’est pas à l’origine des problèmes, puis redémarrez l’ordinateur virtuel. Dans certains cas un état incorrect de la machine virtuelle peut entraîner des problèmes et redémarrer la machine virtuelle réinitialise cette « état incorrect »|
|Sauvegarde|    Échec de l’opération d’extension de services de récupération. -Vérifiez que dernière machine virtuelle agent est présent sur la machine virtuelle et service de l’agent est en cours d’exécution. Veuillez réessayer opération de sauvegarde et en cas d’échec, contactez le support technique Microsoft.|   Cette erreur est générée lorsque l’agent machine virtuelle est mis à jour. Consultez la section « Mise à jour de l’Agent machine virtuelle » ci-dessous pour mettre à jour de l’agent de machine virtuelle.|
|Sauvegarde |Machine virtuelle n’existe pas. -Vérifiez que cette machine virtuelle existe ou sélectionnez-en une machine virtuelle différente. | Cela se produit lorsque la machine virtuelle principale est supprimée, mais la stratégie de sauvegarde continue à prendre une machine virtuelle effectuer la sauvegarde. Pour corriger cette erreur : <ol><li> Recréez la machine virtuelle avec les mêmes nom et le même nom de groupe de ressources [nom du service cloud]<br>(OR)<br></li><li>Arrêter la protection de machine virtuelle sans supprimer les données de sauvegarde. [Obtenir plus d’informations](http://go.microsoft.com/fwlink/?LinkId=808124)</li></ol>|
|Sauvegarde |Échec de l’exécution de commande. -Une autre opération est en cours sur cet élément. Patientez jusqu'à la fin de l’opération précédente et recommencez |Une sauvegarde existante ou une tâche de restauration de la machine virtuelle est en cours d’exécution et une nouvelle tâche ne peut pas être démarrée pendant l’exécution de la tâche existante.|
| Sauvegarde | Copier des disques durs virtuels de l’archivage sécurisé sauvegarde dépassé - recommencez l’opération dans quelques minutes. Si le problème persiste, contactez le Support Microsoft. | Cela se produit lorsqu’il y a trop de données à copier. Vérifiez si vous avez moins de 16 disques de données. |
| Sauvegarde | La sauvegarde a échoué avec une erreur interne - recommencez l’opération dans quelques minutes. Si le problème persiste, contactez le Support Microsoft | Vous pouvez obtenir cette erreur pour 2 raisons : <ol><li> Il existe un problème temporaire de l’accès au stockage de machine virtuelle. Vérifiez [L’état Azure](https://azure.microsoft.com/en-us/status/) pour déterminer si les questions par la suite au cluster/stockage/réseau dans la zone. Veuillez réessayer le problème de sauvegarde billet est réduit. <li>La machine virtuelle d’origine a été supprimée et par conséquent sauvegarde ne peut pas être effectuée. Pour conserver les données de sauvegarde d’un ordinateur virtuel supprimé mais arrêter les erreurs de sauvegarde, ôter la protection de la machine virtuelle et choisissez l’option Conserver les données. Cette commande arrête le planning de sauvegarde et également les messages d’erreur périodique. |
| Sauvegarde | Impossible d’installer les Services de récupération Azure extension sur l’élément sélectionné - Agent machine virtuelle est requis pour le poste de Services de récupération Azure. Installez l’agent machine virtuelle Azure, puis redémarrez l’opération d’enregistrement | <ol> <li>Vérifiez si l’agent machine virtuelle a été installé correctement. <li>Assurez-vous que l’indicateur sur la configuration de la machine virtuelle est correctement définie.</ol> [Pour en savoir plus](#validating-vm-agent-installation) sur l’installation de l’agent machine virtuelle et comment faire pour valider l’installation de l’agent machine virtuelle. |
| Sauvegarde | Extension Échec de l’installation de l’erreur « COM + n’a pas pu communiquer avec l’administrateur des transactions Microsoft Distributed | Cela signifie généralement que le service COM + ne fonctionne pas. Contacter le support technique Microsoft pour vous aider à résoudre ce problème. |
| Sauvegarde | Opération de capture instantanée a échoué avec l’erreur opération VSS « ce lecteur est verrouillé par le chiffrement de lecteur BitLocker. Vous devez déverrouiller ce lecteur le panneau de configuration. | Activer ou désactiver BitLocker pour tous les lecteurs de la machine virtuelle et observez si le problème VSS est résolu |
| Sauvegarde | Machines virtuelles ayant des disques durs virtuels stockés sur le stockage Premium ne sont pas prises en charge pour la sauvegarde | Aucun |
| Sauvegarde | Machine virtuelle Azure non trouvé. | Cela se produit lorsque la machine virtuelle principale est supprimée, mais la stratégie de sauvegarde continue à prendre une machine virtuelle effectuer la sauvegarde. Pour corriger cette erreur : <ol><li>Recréez la machine virtuelle avec les mêmes nom et le même nom de groupe de ressources [nom du service cloud] <br>(OR) <li> Désactiver la protection par pour cet ordinateur virtuel afin que les travaux de sauvegarde n’est pas créés </ol> |
| Sauvegarde | Agent de machine virtuelle n’est pas présent sur la machine virtuelle - Veuillez installer la préalable indispensable, agent machine virtuelle et redémarrer l’opération. | [Pour en savoir plus](#vm-agent) sur l’installation de l’agent machine virtuelle et comment faire pour valider l’installation de l’agent machine virtuelle. |

## <a name="jobs"></a>Tâches

| Opération | Détails de l’erreur | Solution de contournement |
| -------- | -------- | -------|
| Annuler le travail | Annulation n’est pas prise en charge pour ce type de tâche - Patientez jusqu'à ce que le travail est terminé. | Aucun |
| Annuler le travail | La tâche n’est pas dans un état peut être annulé - Patientez jusqu'à ce que le travail est terminé. <br>OR<br> La tâche sélectionnée n’est pas dans un état peut être annulé : attendez la fin du travail.| Dans toute probabilité la tâche est presque terminée ; Patientez jusqu'à ce que le travail est terminé |
| Annuler le travail | Impossible d’annuler la tâche, car il n’est pas en cours - annulation est uniquement prise en charge pour les tâches qui sont en cours. Annulez le tentative sur une valeur de progression en tâche. | Cela se produit en raison d’un état temporaires. Attendez une minute et réessayez l’opération d’annulation |
| Annuler le travail | Impossible d’annuler la tâche - Patientez jusqu'à ce que la tâche se termine. | Aucun |


## <a name="restore"></a>Restaurer
| Opération | Détails de l’erreur | Solution de contournement |
| -------- | -------- | -------|
| Restaurer | Échec de la restauration avec l’erreur interne du Cloud | <ol><li>Service de nuage à laquelle vous voulez restaurer est configuré avec les paramètres DNS. Vous pouvez vérifier <br>$deployment = get-AzureDeployment - ServiceName « ServiceName% »-emplacement « Production » Get-AzureDns - DnsSettings $deployment. DnsSettings<br>S’il existe adresse configurée, cela signifie que les paramètres DNS sont configurés.<br> <li>Service de nuage à laquelle vous voulez restaurer est configuré avec adresse IP réservée et machines virtuelles existantes dans le service cloud sont dans un état arrêté.<br>Vous pouvez vérifier qu'un service cloud a réservé IP à l’aide de la suite applets de commande powershell :<br>$deployment = get-AzureDeployment - ServiceName « ServiceName% »-emplacement « Production » $ logicielle. ReservedIPName <br><li>Vous essayez de restaurer une machine virtuelle avec des configurations réseau spéciaux suivants au même service cloud. <br>-Machines virtuelles sous configuration d’équilibrage de charge (internes et externes)<br>-Machines virtuelles avec plusieurs adresses IP réservés<br>-Machines virtuelles avec plusieurs cartes réseau<br>Sélectionnez un nouveau service cloud dans l’interface utilisateur ou reportez-vous à [restaurer considérations](./backup-azure-arm-restore-vms.md/#restoring-vms-with-special-network-configurations) pour les machines virtuelles avec des configurations réseau spécial</ol> |
| Restaurer | Le nom DNS sélectionné est déjà utilisé - Veuillez spécifier un autre nom DNS et essayez à nouveau. | Le nom DNS ici fait référence au nom du service cloud (généralement se terminant par. cloudapp.net). Il doit être unique. Si vous rencontrez cette erreur, vous devez choisir un autre nom machine virtuelle pendant la restauration. <br><br> Notez que cette erreur est visible uniquement pour les utilisateurs du portail Azure. L’opération de restauration via PowerShell réussit, car il restaure les disques uniquement et ne crée pas de la machine virtuelle. L’erreur soit confrontée lors de la machine virtuelle est explicitement que vous avez créée après restauration le disque. |
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

## <a name="troubleshoot-vm-snapshot-issues"></a>Résoudre les problèmes de capture instantanée de machine virtuelle
Machine virtuelle sauvegarde repose sur l’émission commande instantané vers stockage sous-jacent. N’ayant ne pas accès à stockage ou retard dans l’exécution des tâches instantané peut échouer la sauvegarde. Les éléments suivants peuvent entraîner des échec capture instantanée de la tâche.

1. Accès réseau au stockage est bloqué à l’aide de NSG<br>
   Découvrez comment [Activer l’accès réseau](backup-azure-vms-prepare.md#2-network-connectivity) au stockage à l’aide d’une création de listes autorisées d’adresses IP ou via un serveur proxy.
2.  Machines virtuelles avec sauvegarde Sql Server configuré peuvent entraîner retard capture instantanée de la tâche <br>
    Par défaut machine virtuelle sauvegarder sauvegarde complète de problèmes VSS sur machines virtuelles Windows. Machines virtuelles qui exécutent les serveurs Sql Server et Sql Server sauvegarde est configurée, ceci peut entraîner retard dans l’exécution de capture instantanée. Définissez clé de Registre suivante si vous rencontrez des erreurs de sauvegarde, en raison de problèmes de capture instantanée.

    ```
    [HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
    "USEVSSCOPYBACKUP"="TRUE"
    ```
3.  État de la machine virtuelle rapporté incorrectement machine virtuelle étant arrêt dans RDP.  <br>
    Si vous avez arrêté l’ordinateur virtuel dans RDP, vérifiez dans le portail que machine virtuelle statut est pris en compte correctement. Si ce n’est pas le cas, arrêtez la machine virtuelle dans le portail à l’aide d’option « Arrêt » dans le tableau de bord machine virtuelle.
4.  Si plus de partage de quatre machines virtuelles le même service en nuage, configurez plusieurs règles de sauvegarde pour l’étape les temps de sauvegarde donc pas que plus de quatre sauvegardes de machines virtuelles sont démarrés en même temps. Essayez de faire passer le début des heures heure cm entre les stratégies de sauvegarde. 
5.  Machine virtuelle est en cours d’exécution en processeur/mémoire haute.<br>
    Si la machine virtuelle s’exécute à élevée du processeur usage(>90%) ou mémoire, instantané tâche est en attente, retardée et seront finit par arrivées à expiration. Essayez de sauvegarde à la demande dans ce cas.

<br>

## <a name="networking"></a>Mise en réseau
Comme toutes les extensions, extension sauvegarde besoin d’y accéder à l’internet public pour l’utiliser. N’ayant ne pas accès à l’internet public manifeste lui-même dans de différentes façons :

- L’installation de l’extension peut échouer
- Les opérations de sauvegarde (par exemple, disque instantané) peuvent échouer
- Affichage de l’état de l’opération de sauvegarde peut échouer

Qu’il soit nécessaire pour la résolution d’adresses internet public a été exprimé [ici](http://blogs.msdn.com/b/mast/archive/2014/06/18/azure-vm-provisioning-stuck-on-quot-installing-extensions-on-virtual-machine-quot.aspx). Vous devez vérifier la configuration de DNS pour la VNET et vérifiez que les URI Azure peuvent être résolu.

Une fois que la résolution de noms est terminée correctement, accès aux adresses IP Azure doit également être fourni. Pour débloquer un accès à l’infrastructure Azure, suivez une des opérations suivantes :

1. Liste d’autorisation du centre de données Azure plages d’adresses IP.
    - Obtenir la liste des [centres de données Azure adresses IP](https://www.microsoft.com/download/details.aspx?id=41653) pour être autorisés.
    - Débloquer les adresses IP à l’aide de l’applet de commande [New-NetRoute](https://technet.microsoft.com/library/hh826148.aspx) . Exécuter cette applet de commande au sein de la machine virtuelle Azure, dans une fenêtre PowerShell avec élévation de privilèges (exécutée en tant qu’administrateur).
    - Ajouter des règles à la NSG (si vous possédez un en place) pour autoriser l’accès aux adresses IP.
2. Créer un chemin d’accès pour le trafic HTTP
    - Si vous avez certaines limitations réseau en place (groupe de sécurité réseau, par exemple) déployer un serveur proxy HTTP pour acheminer le trafic. Étapes à suivre pour déployer un serveur HTTP Proxy accessibles [ici](backup-azure-vms-prepare.md#2-network-connectivity).
    - Ajouter des règles à la NSG (si vous possédez un en place) pour autoriser l’accès à INTERNET à partir du HTTP Proxy.

>[AZURE.NOTE] DHCP doit être activé à l’intérieur de l’invité de sauvegarde machine virtuelle IaaS fonctionne.  Si vous avez besoin d’une adresse IP privée statique, vous devez le configurer via la plateforme. L’option DHCP à l’intérieur de la machine virtuelle doit être activée à gauche.
Afficher plus d’informations sur la [définition d’une adresse IP privée interne statique](../virtual-network/virtual-networks-reserved-private-ip.md).
