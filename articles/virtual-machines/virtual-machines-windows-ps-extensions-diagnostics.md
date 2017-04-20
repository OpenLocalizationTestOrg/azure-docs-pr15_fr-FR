<properties
    pageTitle="Utiliser PowerShell pour activer les Diagnostics de Azure dans une machine virtuelle exécutant Windows | Microsoft Azure"
    services="virtual-machines-windows"
    documentationCenter=""
    description="Découvrez comment utiliser PowerShell pour activer les Diagnostics de Azure dans une machine virtuelle exécutant Windows"
    authors="sbtron"
    manager="timlt"
    editor=""/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/15/2015"
    ms.author="saurabh"/>


# <a name="use-powershell-to-enable-azure-diagnostics-in-a-virtual-machine-running-windows"></a>Utiliser PowerShell pour activer les Diagnostics de Azure dans une machine virtuelle exécutant Windows

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Diagnostics de Windows Azure est la capacité dans Azure qui active la collecte de données de diagnostic sur une application déployée. Vous pouvez utiliser l’extension diagnostics pour collecter les données des diagnostics, tels que les journaux d’application ou compteurs de performance d’une Azure machine virtuelle () qui exécute Windows. Cet article décrit comment utiliser Windows PowerShell pour activer l’extension diagnostics pour une machine virtuelle. Découvrez [comment installer et configurer Azure PowerShell](../powershell-install-configure.md) pour la configuration requise pour cet article.

## <a name="enable-the-diagnostics-extension-if-you-use-the-resource-manager-deployment-model"></a>Activer l’extension diagnostics si vous utilisez le modèle de déploiement du Gestionnaire de ressources

Vous pouvez activer l’extension diagnostics pendant que vous créez une machine virtuelle Windows via le modèle de déploiement d’Azure le Gestionnaire de ressources en ajoutant la configuration de l’extension au modèle de gestionnaire de ressources. Voir [créer une machine virtuelle Windows avec la surveillance et les diagnostics à l’aide du modèle Azure le Gestionnaire de ressources](virtual-machines-windows-extensions-diagnostics-template.md).

Pour activer l’extension de diagnostic sur un ordinateur virtuel existant qui a été créé via le modèle de déploiement du Gestionnaire de ressources, vous pouvez utiliser l’applet de commande PowerShell [Set-AzureRMVMDiagnosticsExtension](https://msdn.microsoft.com/library/mt603499.aspx) comme illustré ci-dessous.


    $vm_resourcegroup = "myvmresourcegroup"
    $vm_name = "myvm"
    $diagnosticsconfig_path = "DiagnosticsPubConfig.xml"

    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name -DiagnosticsConfigurationPath $diagnosticsconfig_path


*$diagnosticsconfig_path* est le chemin d’accès au fichier qui contient la configuration de diagnostics au format XML, comme décrit dans l' [exemple](#sample-diagnostics-configuration) ci-dessous.  

Si le fichier de configuration diagnostics spécifie un élément **StorageAccount** avec un nom de compte de stockage, le script *Jeu AzureRMVMDiagnosticsExtension* définira automatiquement l’extension diagnostics pour envoyer des données de diagnostic à ce compte de stockage. Pour ce faire, le compte de stockage doit être placé dans le même abonnement en tant que la machine virtuelle.

Si aucune **StorageAccount** a été spécifié dans la configuration des diagnostics, vous devez passer le paramètre *StorageAccountName* à l’applet de commande. Si le paramètre *StorageAccountName* est spécifié, l’applet de commande utilisent toujours le compte de stockage spécifié dans le paramètre et non à celui qui est spécifié dans le fichier de configuration de diagnostics.

Si le compte de stockage diagnostics se trouve dans un autre abonnement à partir de la machine virtuelle, vous devez passer explicitement dans les paramètres *StorageAccountName* et *StorageAccountKey* à l’applet de commande. Le paramètre *StorageAccountKey* n’est pas nécessaire lors du compte de stockage diagnostics se trouve dans le même abonnement, comme l’applet de commande peut automatiquement la requête et définir la valeur de clé lors de l’activation de l’extension de diagnostics. Toutefois, si le compte de stockage diagnostics se trouve dans un autre abonnement, l’applet de commande peut ne pas être en mesure d’obtenir la clé automatiquement et vous devez spécifier explicitement la clé via le paramètre *StorageAccountKey* .  

    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name -DiagnosticsConfigurationPath $diagnosticsconfig_path -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key

Une fois que l’extension diagnostics est activée sur un ordinateur virtuel, vous pouvez obtenir les paramètres actuels à l’aide de l’applet de commande [Get-AzureRMVmDiagnosticsExtension](https://msdn.microsoft.com/library/mt603678.aspx) .

    Get-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name

L’applet de commande renvoie *PublicSettings*, qui contient la configuration XML dans un format codé en base 64. Pour lire le code XML, vous devez le décoder.

    $publicsettings = (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name).PublicSettings
    $encodedconfig = (ConvertFrom-Json -InputObject $publicsettings).xmlCfg
    $xmlconfig = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($encodedconfig))
    Write-Host $xmlconfig

L’applet de commande [Supprimer AzureRMVmDiagnosticsExtension](https://msdn.microsoft.com/library/mt603782.aspx) peut être utilisée pour supprimer l’extension diagnostics à partir de la machine virtuelle.  

## <a name="enable-the-diagnostics-extension-if-you-use-the-classic-deployment-model"></a>Activer l’extension diagnostics si vous utilisez le modèle de déploiement classique

Vous pouvez utiliser l’applet de commande [Set-AzureVMDiagnosticsExtension](https://msdn.microsoft.com/library/mt589189.aspx) pour activer une extension de diagnostic sur un ordinateur virtuel que vous créez via le modèle de déploiement classique. L’exemple suivant montre comment créer une nouvelle machine virtuelle via le modèle de déploiement classique avec l’extension diagnostics activée.

    $VM = New-AzureVMConfig -Name $VM -InstanceSize Small -ImageName $VMImage
    $VM = Add-AzureProvisioningConfig -VM $VM -AdminUsername $Username -Password $Password -Windows
    $VM = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $Config_Path -VM $VM -StorageContext $Storage_Context
    New-AzureVM -Location $Location -ServiceName $Service_Name -VM $VM

Pour activer l’extension de diagnostic sur un ordinateur virtuel existant qui a été créé via le modèle de déploiement classique, utilisez l’applet de commande [Get-AzureVM](https://msdn.microsoft.com/library/mt589152.aspx) pour obtenir la configuration de la machine virtuelle. Puis mettez à jour la configuration de la machine virtuelle pour inclure l’extension diagnostics à l’aide de l’applet de commande [Set-AzureVMDiagnosticsExtension](https://msdn.microsoft.com/library/mt589189.aspx) . Enfin, appliquez la configuration de mise à jour de la machine virtuelle à l’aide de [Mise à jour AzureVM](https://msdn.microsoft.com/library/mt589121.aspx).

    $VM = Get-AzureVM -ServiceName $Service_Name -Name $VM_Name
    $VM_Update = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $Config_Path -VM $VM -StorageContext $Storage_Context
    Update-AzureVM -ServiceName $Service_Name -Name $VM_Name -VM $VM_Update.VM

## <a name="sample-diagnostics-configuration"></a>Exemple de configuration de diagnostics

Le code XML suivant peut être utilisé pour la configuration du public diagnostics avec les scripts ci-dessus. Cet exemple de configuration transférera plusieurs compteurs de performances sur le compte de stockage diagnostics, ainsi que des erreurs de l’application, la sécurité et la canaux système dans les journaux des événements Windows et des erreurs dans les journaux d’infrastructure diagnostics.

La configuration doit être mis à jour pour inclure les éléments suivants :

- L’attribut *resourceID* de l’élément de **mesures** doit être mis à jour avec l’ID de ressource de la machine virtuelle.
    - L’ID de ressource peut être créée à l’aide du modèle suivant : « /resourceGroups//abonnements / {*ID de l’abonnement de l’abonnement avec la machine virtuelle*} {*le nom resourcegroup pour la machine virtuelle*} / {*le nom de la machine virtuelle*} providers/Microsoft.Compute/virtualMachines/ ».
    - Par exemple, si l’ID de l’abonnement de l’abonnement dans lequel la machine virtuelle est en cours d’exécution est **11111111-1111-1111-1111-111111111111**, le nom du groupe de ressources du groupe de ressources est **MyResourceGroup**et le nom de la machine virtuelle est **MyWindowsVM**, la valeur pour *resourceID* serait :

        ```
        <Metrics resourceId="/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/MyWindowsVM" >
        ```

    - Pour plus d’informations sur la façon de mesures sont générés basée sur la configuration de compteurs et d’indicateurs de performance, consultez [Diagnostics Azure indicateurs tableau de stockage](virtual-machines-windows-extensions-diagnostics-template.md#wadmetrics-tables-in-storage).

- L’élément **StorageAccount** doit être mis à jour avec le nom du compte de stockage diagnostics.

    ```
    <?xml version="1.0" encoding="utf-8"?>
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
        <WadCfg>
          <DiagnosticMonitorConfiguration overallQuotaInMB="4096">
            <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error"/>
            <PerformanceCounters scheduledTransferPeriod="PT1M">
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="CPU utilization" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Privileged Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="CPU privileged time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% User Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="CPU user time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Processor Information(_Total)\Processor Frequency" sampleRate="PT15S" unit="Count">
            <annotation displayName="CPU frequency" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\System\Processes" sampleRate="PT15S" unit="Count">
            <annotation displayName="Processes" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Process(_Total)\Thread Count" sampleRate="PT15S" unit="Count">
            <annotation displayName="Threads" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Process(_Total)\Handle Count" sampleRate="PT15S" unit="Count">
            <annotation displayName="Handles" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\% Committed Bytes In Use" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Memory usage" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory available" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory committed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Commit Limit" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory commit limit" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Pool Paged Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory paged pool" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Pool Nonpaged Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory non-paged pool" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk active time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Read Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk active read time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Write Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk active write time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Transfers/sec" sampleRate="PT15S" unit="CountPerSecond">
            <annotation displayName="Disk operations" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Reads/sec" sampleRate="PT15S" unit="CountPerSecond">
            <annotation displayName="Disk read operations" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Writes/sec" sampleRate="PT15S" unit="CountPerSecond">
            <annotation displayName="Disk write operations" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
            <annotation displayName="Disk speed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Read Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
            <annotation displayName="Disk read speed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Write Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
            <annotation displayName="Disk write speed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Queue Length" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk average queue length" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Read Queue Length" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk average read queue length" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Write Queue Length" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk average write queue length" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(_Total)\% Free Space" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk free space (percentage)" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(_Total)\Free Megabytes" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk free space (MB)" locale="en-us"/>
          </PerformanceCounterConfiguration>
        </PerformanceCounters>
        <Metrics resourceId="(Update with resource ID for the VM)" >
            <MetricAggregation scheduledTransferPeriod="PT1H"/>
            <MetricAggregation scheduledTransferPeriod="PT1M"/>
        </Metrics>
        <WindowsEventLog scheduledTransferPeriod="PT1M">
          <DataSource name="Application!*[System[(Level = 1 or Level = 2)]]"/>
          <DataSource name="Security!*[System[(Level = 1 or Level = 2)]"/>
          <DataSource name="System!*[System[(Level = 1 or Level = 2)]]"/>
        </WindowsEventLog>
          </DiagnosticMonitorConfiguration>
        </WadCfg>
        <StorageAccount>(Update with diagnostics storage account name)</StorageAccount>
    </PublicConfig>
    ```

## <a name="next-steps"></a>Étapes suivantes
- Pour obtenir des conseils supplémentaires sur l’utilisation de la fonctionnalité Diagnostics Azure et autres techniques pour résoudre les problèmes, voir [Activation des Diagnostics dans Azure Cloud Services et Machines virtuelles](../cloud-services/cloud-services-dotnet-diagnostics.md).
- [Schéma de configurations Diagnostics](https://msdn.microsoft.com/library/azure/mt634524.aspx) explique les différentes options de configurations XML pour l’extension diagnostics.
