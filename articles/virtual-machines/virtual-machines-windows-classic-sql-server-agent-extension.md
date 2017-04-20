<properties
    pageTitle="Extension de l’Agent SQL Server pour SQL Server machines virtuelles (standard) | Microsoft Azure"
    description="Cette rubrique décrit comment gérer l’extension de l’agent SQL Server permettant d’automatiser les tâches d’administration de SQL Server spécifiques. Ces incluent la sauvegarde automatique, correction automatique et l’intégration de l’archivage sécurisé Azure clé. Cette rubrique utilise le mode de déploiement classique."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="rothja"
    manager="jhubbard"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="10/27/2016"
    ms.author="jroth"/>

# <a name="sql-server-agent-extension-for-sql-server-vms-classic"></a>Extension de l’Agent SQL Server pour SQL Server machines virtuelles (classique)

> [AZURE.SELECTOR]
- [Gestionnaire de ressources](virtual-machines-windows-sql-server-agent-extension.md)
- [Classique](virtual-machines-windows-classic-sql-server-agent-extension.md)

L’Extension de l’Agent SQL Server IaaS (SQLIaaSAgent) s’exécute sur Azure machines virtuelles pour automatiser les tâches d’administration. Cette rubrique fournit une vue d’ensemble des services pris en charge par l’extension ainsi que des instructions pour l’installation, état et la suppression.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Pour afficher la version du Gestionnaire de ressources de cet article, consultez [Extension de l’Agent SQL Server Gestionnaire de ressources machines virtuelles SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

## <a name="supported-services"></a>Services pris en charge

L’Extension de l’Agent de SQL Server IaaS prend en charge les tâches d’administration suivantes :

| Fonction d’administration | Description |
|---------------------|-------------------------------|
| **SQL des sauvegardes automatiques** | Permet d’automatiser la planification des sauvegardes pour toutes les bases de données pour l’instance par défaut de SQL Server dans la machine virtuelle. Pour plus d’informations, voir [sauvegarde pour SQL Server dans Machines virtuelles Azure (classique) automatique](virtual-machines-windows-classic-sql-automated-backup.md).|
| **SQL automatisé correction** | Configure une période de maintenance pendant laquelle les mises à jour de votre ordinateur virtuel peuvent avoir lieu, afin d’éviter les mises à jour pendant les heures de pointe pour votre charge de travail. Pour plus d’informations, voir [automatisé d’appliquer un correctif pour SQL Server dans Machines virtuelles Azure (standard)](virtual-machines-windows-classic-sql-automated-patching.md).|
| **Intégration de l’archivage sécurisé clés Azure** | Vous permet d’installer et configurer l’archivage sécurisé de clé Azure sur votre machine virtuelle de SQL Server automatiquement. Pour plus d’informations, voir [Configurer l’intégration Azure clé l’archivage sécurisé pour SQL Server sur Azure machines virtuelles (standard)](virtual-machines-windows-classic-ps-sql-keyvault.md).|

## <a name="prerequisites"></a>Conditions préalables

Conditions requises pour utiliser l’Extension de l’Agent de SQL Server IaaS sur votre ordinateur virtuel :

### <a name="operating-system"></a>Système d'exploitation :

- Windows Server 2012
- Windows Server 2012 R2

### <a name="sql-server-versions"></a>Versions de SQL Server :

- SQL Server 2012
- SQL Server 2014
- SQL Server 2016

### <a name="azure-powershell"></a>Azure PowerShell :

[Téléchargez et configurez les commandes PowerShell Azure dernières](../powershell-install-configure.md).

Démarrez Windows PowerShell et se connecter à votre abonnement Azure avec la commande **Ajouter AzureAccount** .

    Add-AzureAccount

Si vous avez plusieurs abonnements, utilisez **Sélectionnez AzureSubscription** pour sélectionner l’abonnement qui contient votre cible machine virtuelle classique.

    Select-AzureSubscription -SubscriptionName <subscriptionname>

À ce stade, vous pouvez obtenir une liste des ordinateurs virtuels classiques et leurs noms de service associé avec la commande **Get-AzureVM** .

    Get-AzureVM

## <a name="installation"></a>Installation

Pour les machines virtuelles classiques, vous devez utiliser PowerShell pour installer l’Extension de l’Agent de SQL Server IaaS et configurer ses services associés. Utiliser l’applet de commande PowerShell **Set-AzureVMSqlServerExtension** pour installer l’extension. Par exemple, la commande suivante installe l’extension sur un ordinateur Windows serveur virtuel (classique) et lui attribue le nom « SQLIaaSExtension ».

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -ReferenceName "SQLIaasExtension" -Version "1.2" | Update-AzureVM

Si vous mettez à jour vers la dernière version de l’Extension de l’Agent SQL IaaS, vous devez redémarrer votre machine virtuelle après mise à jour de l’extension.

>[AZURE.NOTE] Machines virtuelles classiques n’avez pas une option pour installer et configurer l’Extension de l’Agent SQL IaaS via le portail.

## <a name="status"></a>État

Un moyen de vérifier que l’extension est installée consiste à afficher l’état de l’agent dans le portail Azure. Sélectionnez **tous les paramètres** dans la carte de machine virtuelle, puis cliquez sur **extensions**. Vous devez voir l’extension **SQLIaaSAgent** répertoriée.

![SQL Server Agent IaaS Extension portail Azure](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-portal.png)

Vous pouvez également utiliser l’applet de commande **Get-AzureVMSqlServerExtension** Powershell Azure.

    Get-AzureVM –ServiceName "service" –Name "vmname" | Get-AzureVMSqlServerExtension

## <a name="removal"></a>Suppression   

Dans le portail Azure, vous pouvez désinstaller l’extension en cliquant sur les points de suspension sur la carte **Extensions** de propriétés de votre machine virtuelle. Cliquez ensuite sur **Supprimer**.

![Désinstallez l’Extension SQL Server Agent IaaS portail Azure](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-uninstall.png)

Vous pouvez également utiliser l’applet de commande Powershell **Supprimer AzureVMSqlServerExtension** .

    Get-AzureVM –ServiceName "service" –Name "vmname" | Remove-AzureVMSqlServerExtension | Update-AzureVM

## <a name="next-steps"></a>Étapes suivantes

Commencer à utiliser un des services pris en charge par l’extension. Pour plus d’informations, voir les rubriques référencées dans la section [services pris en charge](#supported-services) de cet article.

Pour plus d’informations sur l’exécution de SQL Server Azure machines virtuelles en fonctionnement, consultez [SQL Server dans la vue d’ensemble des Machines virtuelles Azure](virtual-machines-windows-sql-server-iaas-overview.md).
