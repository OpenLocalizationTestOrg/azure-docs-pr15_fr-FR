<properties
   pageTitle="Extension de Script personnalisée sur un ordinateur Windows virtuel | Microsoft Azure"
   description="Automatiser les tâches de configuration de machine virtuelle Azure à l’aide de l’extension de Script personnalisé pour exécuter des scripts PowerShell sur un ordinateur virtuel Windows à distance"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="08/06/2015"
   ms.author="kundanap"/>

# <a name="custom-script-extension-for-windows-virtual-machines"></a>Extension de Script personnalisée pour machines virtuelles Windows

Cet article donne un aperçu de l’utilisation de l’extension de Script personnalisé sur Windows machines virtuelles à l’aide des applets de commande PowerShell Azure avec l’API de gestion de Service Azure.

Machine virtuelle () sont créées par Microsoft et extensions approuvées éditeurs tiers pour étendre les fonctionnalités de la machine virtuelle. Pour une vue d’ensemble des extensions machine virtuelle, voir [fonctionnalités et extensions de machine virtuelle Azure](virtual-machines-windows-extensions-features.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Découvrez comment [effectuer ces étapes en utilisant le modèle de gestionnaire de ressources](virtual-machines-windows-extensions-customscript.md).

## <a name="custom-script-extension-overview"></a>Vue d’ensemble de l’extension Script personnalisé

Avec l’extension de Script personnalisé pour Windows, vous pouvez exécuter des scripts PowerShell sur un ordinateur distant virtuel sans se connecter à celui-ci. Vous pouvez exécuter les scripts après la mise en service de la machine virtuelle, ou à tout moment pendant le cycle de vie de la machine virtuelle sans ouvrir les ports supplémentaires. Scénarios d’utilisation les plus courants pour l’exécution d’extension de Script personnalisé incluent en cours d’exécution, d’installation et configuration des logiciels supplémentaires sur l’ordinateur virtuel après sa configuration.

### <a name="prerequisites-for-running-the-custom-script-extension"></a>Conditions requises pour exécuter l’extension de Script personnalisé

1. Installer les <a href="http://azure.microsoft.com/downloads" target="_blank">applets de commande PowerShell Azure</a> version 0.8.0 ou version ultérieure.
2. Si vous souhaitez que les scripts à exécuter sur un ordinateur virtuel existant, vérifiez que l’Agent machine virtuelle est activé sur l’ordinateur virtuel. S’il n’est pas installé, suivez ces [étapes](virtual-machines-windows-classic-agents-and-extensions.md). Si la machine virtuelle est créée à partir du portail Azure, Agent machine virtuelle est installé par défaut.
3. Télécharger les scripts que vous voulez exécuter sur l’ordinateur virtuel au stockage Azure. Les scripts peuvent provenir d’un seul conteneur ou plusieurs conteneurs de stockage.
4. Le script doit être créé afin que le script d’entrée, qui est démarré par l’extension, démarre autres scripts.

## <a name="custom-script-extension-scenarios"></a>Scénarios d’extension de Script personnalisés

### <a name="upload-files-to-the-default-container"></a>Télécharger des fichiers dans le conteneur par défaut

L’exemple suivant montre comment vous pouvez exécuter votre scripts sur l’ordinateur virtuel si elles se trouvent dans le conteneur de stockage du compte par défaut de votre abonnement. Vous téléchargez vos scripts sur ContainerName. Vous pouvez vérifier le compte de stockage par défaut à l’aide de la **Get-AzureSubscription – par défaut** commande.

L’exemple suivant crée une machine virtuelle, mais vous pouvez également exécuter le même scénario sur un ordinateur virtuel existant.

    # Create a new VM in Azure.
    $vm = New-AzureVMConfig -Name $name -InstanceSize Small -ImageName $imagename
    $vm = Add-AzureProvisioningConfig -VM $vm -Windows -AdminUsername $username -Password $password
    // Add Custom Script extension to the VM. The container name refers to the storage container that contains the file.
    $vm = Set-AzureVMCustomScriptExtension -VM $vm -ContainerName $container -FileName 'start.ps1'
    New-AzureVM -ServiceName $servicename -Location $location -VMs $vm
    #  After the VM is created, the extension downloads the script from the storage location and executes it on the VM.

    # Viewing the  script execution output.
    $vm = Get-AzureVM -ServiceName $servicename -Name $name
    # Use the position of the extension in the output as index.
    $vm.ResourceExtensionStatusList[i].ExtensionSettingStatus.SubStatusList

### <a name="upload-files-to-a-non-default-storage-container"></a>Télécharger des fichiers dans un conteneur de stockage de ceux définis par défaut

Ce scénario montre comment utiliser un conteneur de stockage de ceux définis par défaut dans le même abonnement ou dans un autre abonnement pour le téléchargement de fichiers et les scripts. Cet exemple montre une machine virtuelle existante, mais les mêmes opérations réalisables pendant que vous créez une machine virtuelle.

        Get-AzureVM -Name $name -ServiceName $servicename | Set-AzureVMCustomScriptExtension -StorageAccountName $storageaccount -StorageAccountKey $storagekey -ContainerName $container -FileName 'file1.ps1','file2.ps1' -Run 'file.ps1' | Update-AzureVM

### <a name="upload-scripts-to-multiple-containers-across-different-storage-accounts"></a>Télécharger les scripts à plusieurs conteneurs entre des comptes de stockage différents

  Si les fichiers de script sont stockées sur plusieurs conteneurs, vous devez fournir l’URL de signatures (sa) d’un accès intégral partagé pour les fichiers pour exécuter les scripts.

      Get-AzureVM -Name $name -ServiceName $servicename | Set-AzureVMCustomScriptExtension -StorageAccountName $storageaccount -StorageAccountKey $storagekey -ContainerName $container -FileUri $fileUrl1, $fileUrl2 -Run 'file.ps1' | Update-AzureVM


### <a name="add-the-custom-script-extension-from-the-azure-portal"></a>Ajouter l’extension de Script personnalisé à partir du portail Azure

Accédez à la machine virtuelle dans le <a href="https://portal.azure.com/ " target="_blank">portail Azure</a> et ajoutez l’extension en spécifiant le fichier de script à exécuter.

  ![Spécifier le fichier de script][5]


### <a name="uninstall-the-custom-script-extension"></a>Désinstallez l’extension de Script personnalisé

Vous pouvez désinstaller l’extension de Script personnalisé à partir de la machine virtuelle à l’aide de la commande suivante.

      get-azureVM -ServiceName KPTRDemo -Name KPTRDemo | Set-AzureVMCustomScriptExtension -Uninstall | Update-AzureVM

### <a name="use-the-custom-script-extension-with-templates"></a>Utiliser l’extension de Script personnalisé avec les modèles

Pour en savoir plus sur l’utilisation de l’extension de Script personnalisé avec les modèles Azure le Gestionnaire de ressources, reportez-vous [à l’aide de l’extension de Script personnalisé pour les machines virtuelles Windows avec les modèles Azure le Gestionnaire de ressources](virtual-machines-windows-extensions-customscript.md).

<!--Image references-->
[5]: ./media/virtual-machines-windows-classic-extensions-customscript/addcse.png
