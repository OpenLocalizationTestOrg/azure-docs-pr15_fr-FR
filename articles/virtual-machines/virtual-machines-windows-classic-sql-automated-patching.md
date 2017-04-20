<properties
    pageTitle="Automatisé d’appliquer un correctif pour SQL Server machines virtuelles (standard) | Microsoft Azure"
    description="Explique la fonctionnalité de correction automatique pour SQL Server Machines virtuelles en cours d’exécution dans Azure en utilisant le mode de déploiement classique."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="rothja"
    manager="jhubbard"
    editor=""
    tags="azure-service-management" />
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="09/26/2016"
    ms.author="jroth" />

# <a name="automated-patching-for-sql-server-in-azure-virtual-machines-classic"></a>Automatisé d’appliquer un correctif pour SQL Server dans Azure Machines virtuelles (classique)

> [AZURE.SELECTOR]
- [Gestionnaire de ressources](virtual-machines-windows-sql-automated-patching.md)
- [Classique](virtual-machines-windows-classic-sql-automated-patching.md)

Correctifs automatisé établit une période de maintenance pour une Machine virtuelle Azure qui exécute SQL Server. Mises à jour automatiques ne peuvent être installés au cours de cette fenêtre de maintenance. Pour SQL Server, ainsi que système mises à jour et tout redémarrage associé se produire aux mieux délais pour la base de données. Correctifs automatisé dépend de l' [Extension de l’Agent de SQL Server IaaS](virtual-machines-windows-classic-sql-server-agent-extension.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Pour afficher la version du Gestionnaire de ressources de cet article, consultez [Automatisé d’appliquer un correctif pour SQL Server dans le Gestionnaire de ressources Machines virtuelles Azure](virtual-machines-windows-sql-automated-patching.md).

## <a name="prerequisites"></a>Conditions préalables

Pour utiliser la correction automatique, vous pouvez les conditions préalables suivantes :

**Système d’exploitation**:

- Windows Server 2012
- Windows Server 2012 R2

**Version de SQL Server**:

- SQL Server 2012
- SQL Server 2014
- SQL Server 2016

**Azure PowerShell**:

- [Installer les dernières commandes PowerShell Azure](../powershell-install-configure.md).

**Extension SQL Server IaaS**:

- [Installez l’Extension SQL Server IaaS](virtual-machines-windows-classic-sql-server-agent-extension.md).

## <a name="settings"></a>Paramètres

Le tableau suivant décrit les options qui peuvent être configurées pour la correction automatique. Pour les machines virtuelles classiques, vous devez utiliser PowerShell pour configurer ces paramètres.

|Paramètre|Valeurs possibles|Description|
|---|---|---|
|**Automatisé correction**|Activer/désactiver (désactivé)|Active ou désactive la correction automatique pour une machine virtuelle Azure.|
|**Planification de la maintenance**|Tous les jours, lundi, mardi, mercredi, jeudi, vendredi, samedi, dimanche|L’Échéancier pour télécharger et installer les mises à jour Windows et Microsoft SQL Server pour votre machine virtuelle.|
|**Heure de début de maintenance**|0-24|L’heure de début local pour mettre à jour de la machine virtuelle.|
|**Durée de fenêtre de maintenance**|30-180|Le nombre de minutes autorisés pour effectuer le téléchargement et installation des mises à jour.|
|**Catégorie du correctif**|Important|La catégorie des mises à jour pour télécharger et installer.|

## <a name="configuration-with-powershell"></a>Configuration avec PowerShell

Dans l’exemple suivant, PowerShell sert à configurer la correction automatique sur un ordinateur existant SQL Server virtuel. La commande **New-AzureVMSqlServerAutoPatchingConfig** configure une nouvelle fenêtre de gestion des mises à jour automatiques.

    $aps = New-AzureVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoPatchingSettings $aps | Update-AzureVM

En fonction de cet exemple, le tableau suivant décrit l’effet pratique la cible machine virtuelle Azure :

|Paramètre|Effet|
|---|---|
|**Jour de la semaine**|Correctifs installés chaque jeudi.|
|**MaintenanceWindowStartingHour**|Mises à jour de début à 11:00 am.|
|**MaintenanceWindowsDuration**|Correctifs doivent être installés après 120 minutes. En fonction de l’heure de début, ils doivent compléter par 1:00 pm.|
|**PatchCategory**|Le paramètre n’est possible que pour ce paramètre est « Important ».|

Cela peut prendre plusieurs minutes pour installer et configurer l’Agent de SQL Server IaaS.

Pour désactiver la correction automatique, exécutez la même script sans-paramètre Activer la AzureVMSqlServerAutoPatchingConfig de nouveau. À l’instar de l’installation, il peut prendre plusieurs minutes pour désactiver la correction automatique.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur d’autres tâches automation disponibles, voir [Extension de l’Agent de SQL Server IaaS](virtual-machines-windows-classic-sql-server-agent-extension.md).

Pour plus d’informations sur l’exécution de SQL Server sur machines virtuelles Azure, voir [SQL Server dans la vue d’ensemble des Machines virtuelles Azure](virtual-machines-windows-sql-server-iaas-overview.md).
