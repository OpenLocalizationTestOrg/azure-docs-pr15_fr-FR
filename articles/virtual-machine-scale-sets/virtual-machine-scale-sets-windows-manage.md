<properties
    pageTitle="Gérer des ordinateurs virtuels dans un jeu d’échelle Machine virtuelle | Microsoft Azure"
    description="Gérer des machines virtuelles dans une échelle de machine virtuelle définie à l’aide de PowerShell Azure."
    services="virtual-machine-scale-sets"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machine-scale-sets"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="davidmu"/>

# <a name="manage-virtual-machines-in-a-virtual-machine-scale-set"></a>Gérer des machines virtuelles dans un jeu d’échelle machine virtuelle

Utiliser les tâches dans cet article pour gérer des machines virtuelles dans votre jeu d’échelle machine virtuelle.

La plupart des tâches qui impliquent la gestion d’une machine virtuelle dans un jeu d’échelle requièrent que vous connaissez l’ID de l’instance de l’ordinateur sur lequel vous voulez gérer. Vous pouvez utiliser [Explorateur de ressources Azure](https://resources.azure.com) pour rechercher l’ID de l’instance d’une machine virtuelle dans un jeu d’échelle. Explorateur de ressources permet également de vérifier l’état des tâches que vous avez terminé.

Découvrez [comment installer et configurer Azure PowerShell](../powershell-install-configure.md) pour plus d’informations sur l’installation de la dernière version d’Azure PowerShell, en sélectionnant votre abonnement et se connecter à votre compte.

## <a name="display-information-about-a-scale-set"></a>Afficher des informations sur un ensemble d’échelle

Vous pouvez obtenir des informations générales sur un ensemble d’échelle, qui est également appelé la vue d’instance. Ou, vous pouvez obtenir des informations plus spécifiques, telles que les informations relatives aux ressources dans l’ensemble d’échelle.

Remplacer les valeurs entre guillemets par le nom ou votre groupe de ressources et les proportions définir, puis exécutez la commande :

    Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"

Elle retourne comme suit :

    Id                                          : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Compute/virtualMachineScaleSets/myvmss1
    Name                                        : myvmss1
    Type                                        : Microsoft.Compute/virtualMachineScaleSets
    Location                                    : centralus
    Sku                                         :
      Name                                      : Standard_A0
      Tier                                      : Standard
      Capacity                                  : 3
    UpgradePolicy                               :
      Mode                                      : Manual
    VirtualMachineProfile                       :
      OsProfile                                 :
        ComputerNamePrefix                      : vmss1
        AdminUsername                           : admin1
        WindowsConfiguration                    :
          ProvisionVMAgent                      : True
          EnableAutomaticUpdates                : True
    StorageProfile                              :
      ImageReference                            :
        Publisher                               : MicrosoftWindowsServer
        Offer                                   : WindowsServer
        Sku                                     : 2012-R2-Datacenter
        Version                                 : latest
      OsDisk                                    :
        Name                                    : vmssosdisk
        Caching                                 : ReadOnly
        CreateOption                            : FromImage
        VhdContainers[0]                        : https://astore.blob.core.windows.net/vmss
        VhdContainers[1]                        : https://gstore.blob.core.windows.net/vmss
        VhdContainers[2]                        : https://mstore.blob.core.windows.net/vmss
        VhdContainers[3]                        : https://sstore.blob.core.windows.net/vmss
        VhdContainers[4]                        : https://ystore.blob.core.windows.net/vmss
    NetworkProfile                              :
      NetworkInterfaceConfigurations[0]         :
        Name                                    : mync1
        Primary                                 : True
        IpConfigurations[0]                     :
          Name                                  : ip1
          Subnet                                :
            Id                                  : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Network/virtualNetworks/myvn1/subnets/mysn1
          LoadBalancerBackendAddressPools[0]    :
            Id                                  : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Network/loadBalancers/mylb1/backendAddressPools/bepool1
        LoadBalancerInboundNatPools[0]          :
            Id                                  : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Network/loadBalancers/mylb1/inboundNatPools/natpool1
    ExtensionProfile                            :
      Extensions[0]                             :
        Name                                    : Microsoft.Insights.VMDiagnosticsSettings
        Publisher                               : Microsoft.Azure.Diagnostics
        Type                                    : IaaSDiagnostics
        TypeHandlerVersion                      : 1.5
        AutoUpgradeMinorVersion                 : True
        Settings                                : {"xmlCfg":"...","storageAccount":"astore"}
    ProvisioningState                           : Succeeded
    
Remplacez les valeurs entre guillemets par le nom de votre jeu de groupe et les proportions de ressources. Remplacer *#* avec l’identificateur d’instance de la machine virtuelle que vous souhaitez obtenir des informations, puis exécutez-le :

    Get-AzureRmVmssVM -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #
        
Elle retourne une valeur comme dans cet exemple :

    Id                            : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Compute/
                                    virtualMachineScaleSets/myvmss1/virtualMachines/0
    Name                          : myvmss1_0
    Type                          : Microsoft.Compute/virtualMachineScaleSets/virtualMachines
    Location                      : centralus
    InstanceId                    : 0
    Sku                           :
      Name                        : Standard_A0
      Tier                        : Standard
    LatestModelApplied            : True
    StorageProfile                :
      ImageReference              :
        Publisher                 : MicrosoftWindowsServer
        Offer                     : WindowsServer
        Sku                       : 2012-R2-Datacenter
        Version                   : 4.0.20160617
      OsDisk                      :
        OsType                    : Windows
        Name                      : vmssosdisk-os-0-e11cad52959b4b76a8d9f26c5190c4f8
        Vhd                       :
          Uri                     : https://astore.blob.core.windows.net/vmss/vmssosdisk-os-0-e11cad52959b4b76a8d9f26c5190c4f8.vhd
        Caching                   : ReadOnly
        CreateOption              : FromImage
    OsProfile                     :
      ComputerName                : myvmss1-0
      AdminUsername               : admin1
      WindowsConfiguration        :
        ProvisionVMAgent          : True
        EnableAutomaticUpdates    : True
    NetworkProfile                :
      NetworkInterfaces[0]        :
        Id                        : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Compute/virtualMachineScaleSets/
                                    myvmss1/virtualMachines/0/networkInterfaces/mync1
    ProvisioningState             : Succeeded
    Resources[0]                  :
      Id                          : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Compute/virtualMachines/
                                    myvmss1_0/extensions/Microsoft.Insights.VMDiagnosticsSettings
      Name                        : Microsoft.Insights.VMDiagnosticsSettings
      Type                        : Microsoft.Compute/virtualMachines/extensions
      Location                    : centralus
      Publisher                   : Microsoft.Azure.Diagnostics
      VirtualMachineExtensionType : IaaSDiagnostics
      TypeHandlerVersion          : 1.5
      AutoUpgradeMinorVersion     : True
      Settings                    : {"xmlCfg":"...","storageAccount":"astore"}
      ProvisioningState           : Succeeded
        
## <a name="start-a-virtual-machine-in-a-scale-set"></a>Démarrer une machine virtuelle dans un jeu d’échelle

Remplacez les valeurs entre guillemets par le nom de votre jeu de groupe et les proportions de ressources. Remplacer *#* avec l’identificateur de la machine virtuelle que vous souhaitez démarrer, puis exécutez-le :

    Start-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #

Dans l’Explorateur de ressources, nous voyons que l’état de l’instance est **en cours d’exécution**:

    "statuses": [
      {
        "code": "ProvisioningState/succeeded",
        "level": "Info",
        "displayStatus": "Provisioning succeeded",
        "time": "2016-03-15T02:10:08.0730839+00:00"
      },
      {
        "code": "PowerState/running",
        "level": "Info",
        "displayStatus": "VM running"
      }
    ]

Vous pouvez démarrer tous les ordinateurs virtuels dans l’échelle de la valeur en n’utilisant ne pas le paramètre - ID d’instance.
    
## <a name="stop-a-virtual-machine-in-a-scale-set"></a>Arrêter une machine virtuelle dans un jeu d’échelle

Remplacez les valeurs entre guillemets par le nom de votre jeu de groupe et les proportions de ressources. Remplacer *#* avec l’identificateur de la machine virtuelle que vous voulez arrêter, puis exécutez-le :

    Stop-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #

Dans l’Explorateur de ressources, nous voyons que l’état de l’instance est **libéré**:

    "statuses": [
      {
        "code": "ProvisioningState/succeeded",
        "level": "Info",
        "displayStatus": "Provisioning succeeded",
        "time": "2016-03-15T01:25:17.8792929+00:00"
      },
      {
        "code": "PowerState/deallocated",
        "level": "Info",
        "displayStatus": "VM deallocated"
      }
    ]
    
Pour arrêter une machine virtuelle et libérer pas, utilisez le paramètre - StayProvisioned. Vous pouvez arrêter de tous les ordinateurs virtuels dans le jeu en n’utilisant ne pas le paramètre - ID d’instance.
    
## <a name="restart-a-virtual-machine-in-a-scale-set"></a>Redémarrer une machine virtuelle dans un jeu d’échelle

Remplacer les valeurs entre guillemets par le nom de votre groupe de ressources et le jeu d’échelle. Remplacer *#* avec l’identificateur de la machine virtuelle que vous souhaitez redémarrer, puis exécutez-le :

    Restart-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #
    
Vous pouvez redémarrer tous les ordinateurs virtuels dans le jeu en n’utilisant ne pas le paramètre - ID d’instance.

## <a name="remove-a-virtual-machine-from-a-scale-set"></a>Supprimer une machine virtuelle à partir d’un ensemble d’échelle

Remplacer les valeurs entre guillemets par le nom de votre groupe de ressources et le jeu d’échelle. Remplacer *#* avec l’identificateur de la machine virtuelle que vous voulez supprimer, puis exécutez-le :  

    Remove-AzureRmVmss -ResourceGroupName "resource group name" –VMScaleSetName "scale set name" -InstanceId #

Vous pouvez supprimer l’ensemble d’échelle machine virtuelle tous en même temps en n’utilisant ne pas le paramètre - ID d’instance.

## <a name="change-the-capacity-of-a-scale-set"></a>Modifier la capacité d’un ensemble d’échelle

Vous pouvez ajouter ou supprimer des machines virtuelles en modifiant la capacité de l’ensemble. Obtenir le jeu d’échelle que vous voulez modifier, définir la capacité à ce que vous souhaitez, puis mettez à jour l’échelle définie avec la nouvelle capacité. Dans ces commandes, remplacez les valeurs entre guillemets par le nom de votre groupe de ressources et le jeu d’échelle.

  $vmss = get-AzureRmVmss - ResourceGroupName « nom groupe de ressources » - VMScaleSetName « nom du jeu échelle » $vmss.sku.capacity = 5 mise à jour AzureRmVmss - ResourceGroupName « nom groupe de ressources »-nommer « adapter le nom du jeu » - VirtualMachineScaleSet $vmss 

Si vous supprimez des machines virtuelles à partir du jeu d’échelle, les machines virtuelles avec les ID le plus élevés sont supprimés tout d’abord.
