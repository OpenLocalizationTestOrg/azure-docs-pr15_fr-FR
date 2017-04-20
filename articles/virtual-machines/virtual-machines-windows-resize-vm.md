<properties
    pageTitle="Redimensionner une machine virtuelle Windows | Microsoft Azure"
    description="Redimensionner une machine virtuelle Windows créée dans le modèle de déploiement Gestionnaire de ressources, à l’aide de Powershell Azure."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="Drewm3"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="na"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/19/2016"
    ms.author="drewm"/>

    
# <a name="resize-a-windows-vm"></a>Redimensionner une machine virtuelle Windows

Cet article vous explique comment redimensionner une machine virtuelle Windows, créé dans le modèle de déploiement Gestionnaire de ressources à l’aide de Powershell Azure.

Après avoir créé une machine virtuelle (), vous pouvez ajuster la machine virtuelle vers le haut ou vers le bas en modifiant la taille de la mémoire virtuelle. Dans certains cas, vous devez d’abord libérer la machine virtuelle. Cela peut arriver si la nouvelle taille n’est pas disponible sur le cluster matériel qui héberge actuellement la machine virtuelle.

## <a name="resize-a-windows-vm-not-in-an-availability-set"></a>Redimensionner une machine virtuelle Windows pas dans un jeu de disponibilité

1. Répertorier les tailles de mémoire virtuelle sont disponibles sur le cluster matériel où la machine virtuelle est hébergée. 

    ```powershell
    Get-AzureRmVMSize -ResourceGroupName <resourceGroupName> -VMName <vmName> 
    ```

2. Si la taille souhaitée est répertoriée, exécutez les commandes suivantes pour redimensionner la machine virtuelle. Si la taille souhaitée ne figure pas, passez à l’étape 3.

    ```powershell
    $vm = Get-AzureRmVM -ResourceGroupName <resourceGroupName> -VMName <vmName>
    $vm.HardwareProfile.VmSize = "<newVMsize>"
    Update-AzureRmVM -VM $vm -ResourceGroupName <resourceGroupName>
    ```

3. Si la taille souhaitée n’est pas répertoriée, exécutez les commandes suivantes pour libérer la machine virtuelle, redimensionner, puis redémarrez l’ordinateur virtuel.

    ```powershell
    $rgname = "<resourceGroupName>"
    $vmname = "<vmName>"
    Stop-AzureRmVM -ResourceGroupName $rgname -VMName $vmname -Force
    $vm = Get-AzureRmVM -ResourceGroupName $rgname -VMName $vmname
    $vm.HardwareProfile.VmSize = "<newVMSize>"
    Update-AzureRmVM -VM $vm -ResourceGroupName $rgname
    Start-AzureRmVM -ResourceGroupName $rgname -Name $vmname
    ```

> [AZURE.WARNING] Libérer la machine virtuelle mises à jour de toutes les adresses IP dynamiques attribuées à la machine virtuelle. Les disques du système d’exploitation et les données ne sont pas concernés. 

## <a name="resize-a-windows-vm-in-an-availability-set"></a>Redimensionner une machine virtuelle Windows dans un jeu de disponibilité

Si la nouvelle taille d’un ordinateur virtuel dans un jeu de disponibilité n’est pas disponible sur le cluster matériel qui héberge actuellement la machine virtuelle, tous les ordinateurs virtuels dans l’ensemble de disponibilité devrez désencombrer la pour redimensionner la machine virtuelle. Vous devrez également mettre à jour la taille des autres machines virtuelles dans la disponibilité définie après qu’une machine virtuelle a été redimensionnée. Pour redimensionner une machine virtuelle dans un jeu de disponibilité, procédez comme suit.

1. Répertorier les tailles de mémoire virtuelle sont disponibles sur le cluster matériel où la machine virtuelle est hébergée.

    ```powershell
    Get-AzureRmVMSize -ResourceGroupName <resourceGroupName> -VMName <vmName>
    ```

2. Si la taille souhaitée est répertoriée, exécutez les commandes suivantes pour redimensionner la machine virtuelle. Si elle n’est pas répertorié, passez à l’étape 3.

    ```powershell
    $vm = Get-AzureRmVM -ResourceGroupName <resourceGroupName> -VMName <vmName>
    $vm.HardwareProfile.VmSize = "<newVmSize>"
    Update-AzureRmVM -VM $vm -ResourceGroupName <resourceGroupName>
    ```

3. Si la taille souhaitée n’est pas répertoriée, poursuivez les étapes suivantes pour libérer tous les ordinateurs virtuels dans l’ensemble de disponibilité, redimensionner machines virtuelles et les redémarrer.

4.  Arrêter tous les ordinateurs virtuels dans l’ensemble de disponibilité.

    ```powershell
    $rg = "<resourceGroupName>"
    $as = Get-AzureRmAvailabilitySet -ResourceGroupName $rg
    $vmIds = $as.VirtualMachinesReferences
    foreach ($vmId in $vmIDs){
        $string = $vmID.Id.Split("/")
        $vmName = $string[8]
        Stop-AzureRmVM -ResourceGroupName $rg -Name $vmName -Force
    } 
    ```
              
5.  Redimensionner et redémarrez les ordinateurs virtuels dans l’ensemble de disponibilité.

    ```powershell
    $rg = "<resourceGroupName>"
    $newSize = "<newVmSize>"
    $as = Get-AzureRmAvailabilitySet -ResourceGroupName $rg
    $vmIds = $as.VirtualMachinesReferences
    foreach ($vmId in $vmIDs){
        $string = $vmID.Id.Split("/")
        $vmName = $string[8]
        $vm = Get-AzureRmVM -ResourceGroupName $rg -Name $vmName
        $vm.HardwareProfile.VmSize = $newSize
        Update-AzureRmVM -ResourceGroupName $rg -VM $vm
        Start-AzureRmVM -ResourceGroupName $rg -Name $vmName
    }
    ```

## <a name="next-steps"></a>Étapes suivantes

- Pour une extensibilité supplémentaire, exécutez plusieurs instances de machine virtuelle et évoluer. Pour plus d’informations, voir [Ajuster automatiquement machines Windows dans un jeu d’échelle de Machine virtuelle](../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md).



