<properties
    pageTitle="Automatisé d’appliquer un correctif pour SQL Server machines virtuelles (responsable de ressources) | Microsoft Azure"
    description="Explique la fonctionnalité de correction automatique pour SQL Server Machines virtuelles en cours d’exécution dans Azure à l’aide du Gestionnaire de ressources."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="rothja"
    manager="jhubbard"
    editor=""
    tags="azure-resource-manager"/>
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="08/19/2016"
    ms.author="jroth" />

# <a name="automated-patching-for-sql-server-in-azure-virtual-machines-resource-manager"></a>Automatisé d’appliquer un correctif pour SQL Server dans Azure Machines virtuelles (responsable de ressources)

> [AZURE.SELECTOR]
- [Gestionnaire de ressources](virtual-machines-windows-sql-automated-patching.md)
- [Classique](virtual-machines-windows-classic-sql-automated-patching.md)

Correctifs automatisé établit une période de maintenance pour une Machine virtuelle Azure qui exécute SQL Server. Mises à jour automatiques ne peuvent être installés au cours de cette fenêtre de maintenance. Pour SQL Server, cette rescriction garantit mises à jour système et tout redémarrage associé en cas simultanément la mieux possibles pour la base de données. Correctifs automatisé dépend de l' [Extension de l’Agent de SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modèle de déploiement classique. Pour afficher la version classique de cet article, consultez [Automatisé d’appliquer un correctif pour SQL Server dans Azure Machines virtuelles classique](virtual-machines-windows-classic-sql-automated-patching.md).

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

- [Installer les dernières commandes PowerShell Azure](../powershell-install-configure.md) si vous envisagez de configurer la correction automatique avec PowerShell.

>[AZURE.NOTE] Correctifs automatisé dépend de l’Extension de l’Agent de SQL Server IaaS. Images de la galerie actuelles machine virtuelle SQL ajouter cette extension par défaut. Pour plus d’informations, voir [Extension de l’Agent de SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md).

## <a name="settings"></a>Paramètres

Le tableau suivant décrit les options qui peuvent être configurées pour la correction automatique. Les étapes de configuration varient selon que vous utilisez le portail Azure ou les commandes PowerShell de Windows Azure.

|Paramètre|Valeurs possibles|Description|
|---|---|---|
|**Automatisé correction**|Activer/désactiver (désactivé)|Active ou désactive la correction automatique pour une machine virtuelle Azure.|
|**Planification de la maintenance**|Tous les jours, lundi, mardi, mercredi, jeudi, vendredi, samedi, dimanche|L’Échéancier pour télécharger et installer les mises à jour Windows et Microsoft SQL Server pour votre machine virtuelle.|
|**Heure de début de maintenance**|0-24|L’heure de début local pour mettre à jour de la machine virtuelle.|
|**Durée de fenêtre de maintenance**|30-180|Le nombre de minutes autorisés pour effectuer le téléchargement et installation des mises à jour.|
|**Catégorie du correctif**|Important|La catégorie des mises à jour pour télécharger et installer.|

## <a name="configuration-in-the-portal"></a>Configuration du portail
Vous pouvez utiliser le portail Azure configurer correction automatique pendant la mise en service ou pour machines virtuelles existantes.

### <a name="new-vms"></a>Nouvelles machines virtuelles
Utiliser le portail Azure pour configurer la correction automatique lorsque vous créez une Machine virtuelle SQL Server dans le modèle de déploiement du Gestionnaire de ressources.

Dans la carte de **paramètres de SQL Server** , sélectionnez **correction automatique**. L’écran portail Azure suivant indique la carte **SQL automatisé correction** .

![SQL automatisé correction portail Azure](./media/virtual-machines-windows-sql-automated-patching/azure-sql-arm-patching.png)

Pour le contexte, consultez la rubrique complète sur [une machine virtuelle SQL Server dans Azure](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="existing-vms"></a>Machines virtuelles existantes
Pour des machines virtuelles SQL Server existantes, sélectionnez votre machine virtuelle SQL Server. Sélectionnez ensuite la section **configuration de SQL Server** de la cuillère **paramètres** .

![Correction automatique de SQL pour machines virtuelles existantes](./media/virtual-machines-windows-sql-automated-patching/azure-sql-rm-patching-existing-vms.png)

Dans la carte de **configuration SQL Server** , cliquez sur le bouton **Modifier** dans l’automatisé correction de section.

![Configurer SQL automatisé correction pour machines virtuelles existantes](./media/virtual-machines-windows-sql-automated-patching/azure-sql-rm-patching-configuration.png)

Lorsque vous avez terminé, cliquez sur le bouton **OK** en bas de la cuillère de **configuration SQL Server** pour enregistrer vos modifications.

Si vous activez la correction automatique pour la première fois, Azure configure l’Agent SQL Server IaaS en arrière-plan. Pendant ce temps, le portail Azure figuriez pas que la correction automatique est configuré. Patientez quelques minutes pour que l’agent soit installé, configuré. Une fois que le portail Azure reflète les nouveaux paramètres.

>[AZURE.NOTE] Vous pouvez également configurer automatisé correction à l’aide d’un modèle. Pour plus d’informations, voir [démarrage rapide Azure modèle pour la correction automatique](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-autopatching-update).

## <a name="configuration-with-powershell"></a>Configuration avec PowerShell

Après la configuration de votre SQL VM, utiliser PowerShell pour configurer la correction automatique.

Dans l’exemple suivant, PowerShell sert à configurer la correction automatique sur un ordinateur existant SQL Server virtuel. La commande **AzureRM.Compute\New AzureVMSqlServerAutoPatchingConfig** configure une nouvelle fenêtre de gestion des mises à jour automatiques.

    $vmname = "vmname"
    $resourcegroupname = "resourcegroupname"
    $aps = AzureRM.Compute\New-AzureVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"

    Set-AzureRmVMSqlServerExtension -AutoPatchingSettings $aps -VMName $vmname -ResourceGroupName $resourcegroupname

En fonction de cet exemple, le tableau suivant décrit l’effet pratique la cible machine virtuelle Azure :

|Paramètre|Effet|
|---|---|
|**Jour de la semaine**|Correctifs installés chaque jeudi.|
|**MaintenanceWindowStartingHour**|Mises à jour de début à 11:00 am.|
|**MaintenanceWindowsDuration**|Correctifs doivent être installés après 120 minutes. En fonction de l’heure de début, ils doivent compléter par 1:00 pm.|
|**PatchCategory**|Le paramètre n’est possible que pour ce paramètre est **Important**.|

Cela peut prendre plusieurs minutes pour installer et configurer l’Agent de SQL Server IaaS.

Pour désactiver la correction automatique, exécutez le script, même sans le **-Activer** paramètre à **AzureRM.Compute\New AzureVMSqlServerAutoPatchingConfig**. L’absence de la **-Activer** paramètre indique la commande pour désactiver la fonctionnalité.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur d’autres tâches automation disponibles, voir [Extension de l’Agent de SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md).

Pour plus d’informations sur l’exécution de SQL Server sur machines virtuelles Azure, voir [SQL Server dans la vue d’ensemble des Machines virtuelles Azure](virtual-machines-windows-sql-server-iaas-overview.md).
