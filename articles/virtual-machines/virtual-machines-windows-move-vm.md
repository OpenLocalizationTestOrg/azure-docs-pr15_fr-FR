<properties
    pageTitle="Déplacer une machine virtuelle Windows | Microsoft Azure"
    description="Déplacer une machine virtuelle Windows vers un autre groupe ressource ou d’abonnement Azure dans le modèle de déploiement du Gestionnaire de ressources."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/08/2016"
    ms.author="cynthn"/>

    


# <a name="move-a-windows-vm-to-another-azure-subscription-or-resource-group"></a>Déplacer une machine virtuelle Windows vers un autre groupe de ressource ou d’abonnement Azure 

Cet article décrit comment déplacer une machine virtuelle Windows entre plusieurs abonnements ou des groupes de ressources. Déplacement entre les abonnements peut être utile si vous avez créé une machine virtuelle dans un abonnement personnel et souhaite pour le déplacer vers l’abonnement de votre entreprise pour effectuer votre travail.

> [AZURE.NOTE] Nouvelle ressource ID est créée dans le cadre du déplacement. Une fois que la machine virtuelle a été déplacée, vous devez mettre à jour vos outils et les scripts à utiliser la nouvelle ressource ID. 


[AZURE.INCLUDE [virtual-machines-common-move-vm](../../includes/virtual-machines-common-move-vm.md)]


## <a name="use-powershell-to-move-a-vm"></a>Utiliser Powershell pour déplacer une machine virtuelle

Pour déplacer une machine virtuelle vers un autre groupe de ressources, vous devez vous assurer que vous déplacez également toutes les ressources dépendantes. Pour utiliser l’applet de commande Move-AzureRMResource, vous devez le nom de ressource et le type de ressource. Vous pouvez accéder à la fois à partir de l’applet de commande Rechercher AzureRMResource.

    Find-AzureRMResource -ResourceGroupNameContains "<sourceResourceGroupName>"
    

Pour déplacer une machine virtuelle, nous avons besoin déplacer plusieurs ressources. Nous puissions simplement créer des variables séparées pour chaque ressource et puis répertorier. Cet exemple inclut la plupart des ressources de base pour une machine virtuelle, mais vous pouvez ajouter d’autres selon vos besoins.

    $sourceRG = "<sourceResourceGroupName>"
    $destinationRG = "<destinationResourceGroupName>"
    
    $vm = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Compute/virtualMachines" -ResourceName "<vmName>"
    $storageAccount = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Storage/storageAccounts" -ResourceName "<storageAccountName>"
    $diagStorageAccount = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Storage/storageAccounts" -ResourceName "<diagnosticStorageAccountName>"
    $vNet = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Network/virtualNetworks" -ResourceName "<vNetName>"
    $nic = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Network/networkInterfaces" -ResourceName "<nicName>"
    $ip = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Network/publicIPAddresses" -ResourceName "<ipName>"
    $nsg = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Network/networkSecurityGroups" -ResourceName "<nsgName>"
    
    Move-AzureRmResource -DestinationResourceGroupName $destinationRG -ResourceId $vm.ResourceId, $storageAccount.ResourceId, $diagStorageAccount.ResourceId, $vNet.ResourceId, $nic.ResourceId, $ip.ResourceId, $nsg.ResourceId

Pour déplacer les ressources vers un autre abonnement, incluez le paramètre **- DestinationSubscriptionId** . 

    Move-AzureRmResource -DestinationSubscriptionId "<destinationSubscriptionID>" -DestinationResourceGroupName $destinationRG -ResourceId $vm.ResourceId, $storageAccount.ResourceId, $diagStorageAccount.ResourceId, $vNet.ResourceId, $nic.ResourceId, $ip.ResourceId, $nsg.ResourceId



Vous êtes invité à confirmer que vous voulez déplacer les ressources spécifiées. Tapez **Y** pour confirmer que vous voulez déplacer les ressources.

  
## <a name="next-steps"></a>Étapes suivantes

Vous pouvez déplacer différents types de ressources entre les abonnements et les groupes de ressources. Pour plus d’informations, voir [déplacer des ressources à nouveau groupe de ressources ou d’abonnement](../resource-group-move-resources.md).    