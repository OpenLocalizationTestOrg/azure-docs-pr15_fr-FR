<properties 
    pageTitle="Redéployez machines virtuelles Windows | Microsoft Azure" 
    description="Décrit comment redéployez machines virtuelles de Windows pour limiter les problèmes de connexion RDP." 
    services="virtual-machines-windows" 
    documentationCenter="virtual-machines" 
    authors="iainfoulds" 
    manager="timlt"
    tags="azure-resource-manager,top-support-issue" 
/>
    

<tags 
    ms.service="virtual-machines-windows" 
    ms.devlang="na" 
    ms.topic="support-article" 
    ms.tgt_pltfrm="vm-windows"
    ms.workload="infrastructure" 
    ms.date="09/19/2016" 
    ms.author="iainfou" 
/>


# <a name="redeploy-virtual-machine-to-new-azure-node"></a>Redéployez machine virtuelle au nouveau nœud Azure

Si vous avez été en difficulté résolution des problèmes de bureau à distance (RDP) connexion ou application access à basés sur Windows Azure machine virtuelle (), redéployez la machine virtuelle peuvent vous aider. Lorsque vous redéployez une machine virtuelle, il déplace la machine virtuelle vers un nouveau nœud au sein de l’infrastructure Azure et puis sous tension il précédent, en conservant tous vos options de configuration et les ressources associées. Cet article vous explique comment redéployez une machine virtuelle à l’aide de PowerShell Azure ou du portail Azure.

> [AZURE.NOTE] Une fois que vous redéployez une machine virtuelle, le disque temporaire est perdu et les adresses IP dynamiques associés aux interface réseau virtuelle sont mis à jour. 

## <a name="using-azure-powershell"></a>À l’aide de PowerShell Azure

Vérifiez que vous disposez de la dernière PowerShell Azure 1.x installé sur votre ordinateur. Pour plus d’informations, voir [comment installer et configurer Azure PowerShell](../powershell-install-configure.md).

Utilisez cette commande PowerShell Azure pour redéployez votre machine virtuelle :

    Set-AzureRmVM -Redeploy -ResourceGroupName $rgname -Name $vmname 


[AZURE.INCLUDE [virtual-machines-common-redeploy-to-new-node](../../includes/virtual-machines-common-redeploy-to-new-node.md)]


## <a name="next-steps"></a>Étapes suivantes
Si vous rencontrez des problèmes de connexion à votre ordinateur virtuel, vous pouvez trouver de l’aide spécifique sur [résolution des problèmes de connexions RDP](virtual-machines-windows-troubleshoot-rdp-connection.md) ou [RDP détaillée des étapes de dépannage](virtual-machines-windows-detailed-troubleshoot-rdp.md). Si vous ne pouvez pas accéder à une application s’exécutant sur votre ordinateur virtuel, vous pouvez également lire des [problèmes de dépannage des applications](virtual-machines-windows-troubleshoot-app-connection.md).