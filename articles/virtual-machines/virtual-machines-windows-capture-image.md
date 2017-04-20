<properties
    pageTitle="Capturer une image de machine virtuelle à partir de la machine virtuelle Azure GRG | Microsoft Azure"
    description="Découvrez comment faire pour capturer une image de machine virtuelle à partir d’une machine virtuelle Azure GRG créée dans le modèle de déploiement du Gestionnaire de ressources"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/20/2016"
    ms.author="cynthn"/>

# <a name="how-to-capture-a-vm-image-from-a-generalized-azure-vm"></a>Comment faire pour capturer une image de machine virtuelle à partir d’un ordinateur virtuel Azure GRG


Cet article vous explique comment utiliser PowerShell Azure pour créer une image d’un ordinateur virtuel Azure GRG. Vous pouvez ensuite utiliser l’image pour créer un autre ordinateur virtuel. L’image inclut le disque du système d’exploitation et les disques de données qui sont joints à la machine virtuelle. L’image n’inclut pas les ressources réseau virtuel, vous devez configurer ces ressources lorsque vous créez la nouvelle machine virtuelle. 


## <a name="prerequisites"></a>Conditions préalables

- Vous devez avoir déjà [generalized la machine virtuelle](virtual-machines-windows-generalize-vhd.md). Généralisation une machine virtuelle supprime toutes vos informations de compte personnel, entre autres et prépare l’ordinateur à utiliser en tant qu’image.

- Vous devez disposer Azure PowerShell version 1.0.x ou ultérieure. Si vous n’avez pas déjà installé PowerShell, Découvrez [comment installer et configurer Azure PowerShell](../powershell-install-configure.md) pour obtenir la procédure d’installation.


## <a name="log-in-to-azure-powershell"></a>Se connecter au PowerShell Azure

1. Ouvrez Azure PowerShell et se connecter à votre compte Azure.

    ```powershell
    Login-AzureRmAccount
    ```

    Une fenêtre contextuelle s’ouvre pour vous devez entrer vos informations d’identification de compte Azure.

2. Obtenir l’ID de l’abonnement pour vos abonnements disponibles.

    ```powershell
    Get-AzureRmSubscription
    ```

3. Définir l’abonnement approprié à l’aide de l’ID de l’abonnement.

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<subscriptionID>"
    ```

## <a name="deallocate-the-vm-and-set-the-state-to-generalized"></a>Libérer la machine virtuelle et définissez l’état à generalized       

1. Libérer les ressources machine virtuelle.

    ```powershell
    Stop-AzureRmVM -ResourceGroupName <resourceGroup> -Name <vmName>
    ```

    L' *état* de la machine virtuelle dans le portail Azure les modifications provenant de **arrêt** arrêté **(libéré)**.

2. Définir le statut de la machine virtuelle sur **Generalized**. 

    ```powershell
    Set-AzureRmVm -ResourceGroupName <resourceGroup> -Name <vmName> -Generalized
    ```

3. Vérifiez l’état de la machine virtuelle. La section **OSState/generalized** pour la machine virtuelle doit avoir le **DisplayStatus** défini sur **machine virtuelle generalized**.  

    ```powershell
    $vm = Get-AzureRmVM -ResourceGroupName <resourceGroup> -Name <vmName> -Status
    $vm.Statuses
    ```

## <a name="create-the-image"></a>Créer l’image 

1. Copier l’image machines virtuelles dans le conteneur de stockage de destination à l’aide de cette commande. L’image est créée dans le même compte de stockage que la machine virtuelle d’origine. La `-Path` paramètre enregistre une copie du modèle JSON localement. La `-DestinationContainerName` paramètre est le nom du conteneur vers lequel vous voulez stocker vos images. Si le conteneur n’existe pas, il est créé pour vous.

    ```powershell
    Save-AzureRmVMImage -ResourceGroupName <resourceGroupName> -Name <vmName> `
        -DestinationContainerName <destinationContainerName> -VHDNamePrefix <templateNamePrefix> `
        -Path <C:\local\Filepath\Filename.json>
    ```

    Vous pouvez obtenir l’URL de votre image à partir du modèle de fichier JSON. Accédez aux **ressources** > **storageProfile** > **osDisk** > **image** > section**uri** pour le chemin d’accès complet de votre image. L’URL de l’image ressemble à : `https://<storageAccountName>.blob.core.windows.net/system/Microsoft.Compute/Images/<imagesContainer>/<templatePrefix-osDisk>.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`.
    
    Vous pouvez également vérifier l’URI dans le portail. L’image est copiée dans un conteneur nommé **système** dans votre compte de stockage. 


## <a name="next-steps"></a>Étapes suivantes

- Vous pouvez désormais [créer une machine virtuelle à partir de l’image](virtual-machines-windows-create-vm-generalized.md).

