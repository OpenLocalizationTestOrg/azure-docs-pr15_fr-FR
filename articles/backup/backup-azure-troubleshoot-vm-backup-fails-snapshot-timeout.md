<properties
   pageTitle="Sauvegarde machine virtuelle Azure échoue : pas pu communiquer avec l’agent machine virtuelle état instantané - tâches de sub machine virtuelle instantané dépassé | Microsoft Azure"
   description="Symptômes causes et solutions liés à des échecs de sauvegarde machine virtuelle Azure Impossible de communiquer avec l’agent machine virtuelle état instantané. Instantané machine virtuelle sous tâche a expiré d’erreur"
   services="backup"
   documentationCenter=""
   authors="genlin"
   manager="cfreeman"
   editor=""/>

<tags
    ms.service="backup"
    ms.workload="storage-backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="jimpark; markgal;genli"/>

# <a name="azure-vm-backup-fails-could-not-communicate-with-the-vm-agent-for-snapshot-status---snapshot-vm-sub-task-timed-out"></a>Sauvegarde machine virtuelle Azure échoue : pas pu communiquer avec l’agent machine virtuelle état instantané - tâches de sub machine virtuelle instantané a expiré

## <a name="summary"></a>Résumé

Après avoir enregistré et planification d’une Machine virtuelle (machine virtuelle) pour Azure sauvegarde, le service de sauvegarde Azure lance le travail de sauvegarde à l’heure planifiée en communication avec l’extension de sauvegarde dans la machine virtuelle pour prendre un instantané de point dans le temps. Il existe conditions qui peuvent empêcher l’instantané déclenchement qui permet d’accéder à un échec de la sauvegarde. Cet article fournit des étapes de dépannage pour les problèmes liés aux échecs de sauvegarde machine virtuelle Azure liés à l’erreur de temporisation instantané.

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="symptom"></a>Manifestation

Microsoft Azure sauvegarde pour l’infrastructure en tant que service (IaaS) machine virtuelle échoue et renvoie le message d’erreur suivant dans les détails de l’erreur du travail dans le [portail Azure](https://portal.azure.com/):

**Pas pu communiquer avec l’agent machine virtuelle état instantané - tâches de sub machine virtuelle instantané a expiré.**

## <a name="cause"></a>Cause
Il existe quatre causes courantes pour cette erreur :

- La machine virtuelle n’a pas accès à Internet.
- L’agent de machine virtuelle Microsoft Azure installé dans la machine virtuelle est obsolète (pour les machines virtuelles Linux).
- L’extension de sauvegarde ne parvient pas à mettre à jour ou charger.
- L’état des instantanés ne peuvent pas être récupérée ou les captures instantanées ne peuvent pas être effectuées.

## <a name="cause-1-the-vm-does-not-have-internet-access"></a>Cause 1 : La machine virtuelle n’a pas accès à Internet
Par l’exigence de déploiement, la machine virtuelle n’a aucun accès à Internet, ou restrictions en place qui empêchent l’accès à l’infrastructure Azure.

L’extension de sauvegarde nécessite une connexion pour les adresses IP publiques Azure pour fonctionner correctement. Il s’agit, car il envoie des commandes à un point de terminaison du stockage Azure (URL HTTP) pour gérer les instantanés de la machine virtuelle. Si l’extension n’a pas accès à l’Internet public, la sauvegarde échoue par la suite.

### <a name="solution"></a>Solution
Dans ce scénario, utilisez une des méthodes suivantes pour résoudre le problème :

- Plages d’adresses IP liste d’autorisation du centre de données Azure
- Créer un chemin d’accès pour le trafic HTTP

### <a name="to-whitelist-the-azure-datacenter-ip-ranges"></a>À la liste d’autorisation la Azure plages d’adresses IP du centre de données

1. Obtenir la [liste du centre de données Azure adresses IP](https://www.microsoft.com/download/details.aspx?id=41653) pour être autorisés.
2. Débloquer les adresses IP à l’aide de l’applet de commande New-NetRoute. Exécuter cette applet de commande dans la machine virtuelle Azure dans une fenêtre PowerShell avec élévation de privilèges (exécutée en tant qu’administrateur).
3. Ajouter des règles pour le groupe de sécurité réseau (NSG) si vous avez un pour autoriser l’accès aux adresses IP.

### <a name="to-create-a-path-for-http-traffic-to-flow"></a>Pour créer un chemin d’accès pour le trafic HTTP

1. Si vous avez des restrictions de réseau en place (par exemple, un NSG), déployer un serveur proxy HTTP pour acheminer le trafic.
2. Si vous avez un groupe de sécurité réseau (NSG), ajouter des règles pour autoriser l’accès à Internet à partir du proxy HTTP.

Découvrez comment [configurer un proxy HTTP des sauvegardes machine virtuelle](backup-azure-vms-prepare.md#using-an-http-proxy-for-vm-backups).

## <a name="cause-2-the-microsoft-azure-vm-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>Cause 2 : L’agent de machine virtuelle Microsoft Azure installé dans la machine virtuelle est obsolète (pour les machines virtuelles Linux)

### <a name="solution"></a>Solution
Échecs plus liées à l’agent ou extension pour les machines virtuelles Linux sont dues à des problèmes qui affectent un ancien agent machine virtuelle. En règle générale, les premières étapes pour résoudre ce problème sont les suivantes :

1. [Installer la dernière agent machine virtuelle Azure](https://github.com/Azure/WALinuxAgent).
2. Assurez-vous que l’agent Azure est en cours d’exécution sur l’ordinateur virtuel. Pour ce faire, exécutez la commande suivante :```ps -e```

    Si ce processus ne fonctionne pas, utilisez les commandes suivantes pour le redémarrer.

    Pour Ubuntu :```service walinuxagent start```

    Pour d’autres répartitions : ''' waagent démarrage du service
```

3. [Configure the auto restart agent](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).

4. Run a new test backup. If the failures persist, please collect logs from the following folders for further debugging.

    We require the following logs from the customer’s VM:

    - /var/lib/waagent/*.xml
    - /var/log/waagent.log
    - /var/log/azure/*

If we require verbose logging for waagent, follow these steps to enable this:

1. In the /etc/waagent.conf file, locate the following line:

    Enable verbose logging (y|n)

2. Change the **Logs.Verbose** value from n to y.
3. Save the change, and then restart waagent by following the previous steps in this section.

## Cause 3: The backup extension fails to update or load
If extensions cannot be loaded, then Backup fails because a snapshot cannot be taken.

### Solution
For Windows guests:

1. Verify that the iaasvmprovider service is enabled and has a startup type of automatic.
2. If this is not the configuration, enable the service to determine whether the next backup succeeds.

For Linux guests:

The latest version of VMSnapshot Linux (extension used by backup) is 1.0.91.0

If the backup extension still fails to update or load, you can force the VMSnapshot extension to be reloaded by uninstalling the extension. The next backup attempt will reload the extension.

### To uninstall the extension

1. Go to the [Azure portal](https://portal.azure.com/).
2. Locate the particular VM that has backup problems.
3. Click **Settings**.
4. Click **Extensions**.
5. Click **Vmsnapshot Extension**.
6. Click **uninstall**.

This will cause the extension to be reinstalled during the next backup.

## Cause 4: The snapshots status cannot be retrieved or the snapshots cannot be taken
VM backup relies on issuing snapshot command to underlying storage. The backup can fail because it has no access to storage or because of a delay in snapshot task execution.

### Solution
The following conditions can cause snapshot task failure:

| Cause | Solution |
| ----- | ----- |
| VMs that have Microsoft SQL Server Backup configured. By default, VM Backup runs a VSS Full backup on Windows VMs. On VMs that are running SQL Server-based servers and on which SQL Server Backup is configured, snapshot execution delays may occur. | Set following registry key if you are experiencing backup failures because of snapshot issues.<br><br>[HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT] "USEVSSCOPYBACKUP"="TRUE" |
| VM status is reported incorrectly because the VM is shut down in RDP. If you shut down the virtual machine in RDP, check the portal to determine whether that VM status is reflected correctly. | If it’s not, shut down the VM in the portal by using the ”Shutdown” option in the VM dashboard. |
| Many VMs from the same cloud service are configured to back up at the same time. | It’s a best practice to spread out the VMs from the same cloud service to have different backup schedules. |
| The VM is running at high CPU or memory usage. | If the VM is running at high CPU usage (more than 90 percent) or high memory usage, the snapshot task is queued and delayed and eventually times out. In this situation, try on-demand backup. |
|The VM cannot get host/fabric address from DHCP.|DHCP must be enabled inside the guest for IaaS VM Backup to work.  If the VM cannot get host/fabric address from DHCP response 245, then it cannot download ir run any extensions. If you need a static private IP, you should configure it through the platform. The DHCP option inside the VM should be left enabled. View more information about [Setting a Static Internal Private IP](../virtual-network/virtual-networks-reserved-private-ip.md).|
