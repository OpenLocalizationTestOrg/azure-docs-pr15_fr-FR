<properties
    pageTitle="Télécharger un disque dur virtuel Windows pour le Gestionnaire de ressources | Microsoft Azure"
    description="Découvrez comment télécharger une Windows machine virtuelle dur locales Azure, à l’aide du modèle de déploiement Gestionnaire de ressources. Vous pouvez télécharger un disque dur virtuel de soit un GRG ou une machine virtuelle spécialisée."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="cynthn"/>

# <a name="upload-a-windows-vhd-from-an-on-premises-vm-to-azure"></a>Télécharger un disque dur virtuel à partir d’un ordinateur local virtuel vers Azure Windows 


Cet article vous explique comment créer et télécharger un disque dur virtuel Windows (disque dur virtuel) à utiliser lors de la création d’une machine virtuelle Azure. Vous pouvez télécharger un disque dur virtuel à partir d’un ordinateur virtuel GRG ou une machine virtuelle spécialisée. 

Pour plus d’informations sur disques et des disques durs virtuels dans Azure, voir [à propos des disques et des disques durs virtuels pour les machines virtuelles](virtual-machines-linux-about-disks-vhds.md).


## <a name="prepare-the-vm"></a>Préparer la machine virtuelle 

Vous pouvez télécharger des disques durs virtuels GRG et spécialisées sur Azure. Chaque type nécessite que vous préparez la machine virtuelle avant de commencer.

- **Disque dur virtuel generalized** - un disque dur virtuel GRG a eu toutes vos informations de compte personnel supprimées à l’aide de Sysprep. Si vous prévoyez d’utiliser le disque dur virtuel en tant qu’image pour créer de nouvelles machines virtuelles à partir de, vous devez :
    - [Préparer un disque dur virtuel Windows pour télécharger dans Azure](virtual-machines-windows-prepare-for-upload-vhd-image.md). 
    - [L’utilisation de la machine virtuelle Generalize Sysprep](virtual-machines-windows-generalize-vhd.md). 

- **Disque dur virtuel spécialisé** - un disque dur virtuel spécialisé conserve les comptes d’utilisateurs, applications et autres données d’état à partir de votre ordinateur virtuel d’origine. Si vous prévoyez d’utiliser le disque dur virtuel en tant que-consiste à créer un nouvel ordinateur virtuel, vérifiez les étapes suivantes sont effectuées. 
    - [Préparer un disque dur virtuel Windows pour télécharger dans Azure](virtual-machines-windows-prepare-for-upload-vhd-image.md). **Ne pas** généraliser la machine virtuelle à l’aide de Sysprep.
    - Supprimer les outils de virtualisation invité et les agents qui sont installées sur la machine virtuelle (c'est-à-dire les outils VMware).
    - Vérifiez que la machine virtuelle est configurée pour son adresse IP et appliquer les paramètres DNS via DHCP. Ceci garantit que le serveur obtienne une adresse IP dans le VNet lors de son démarrage. 

## <a name="log-in-to-azure"></a>Se connecter à Azure

Si vous n’avez pas PowerShell version 1.4 ou version ultérieure installée et en savoir [comment installer et configurer Azure PowerShell](../powershell-install-configure.md).

1. Ouvrez Azure PowerShell et se connecter à votre compte Azure. Une fenêtre contextuelle s’ouvre pour vous devez entrer vos informations d’identification de compte Azure.

    ```powershell
    Login-AzureRmAccount
    ```


2. Obtenir l’ID de l’abonnement pour vos abonnements disponibles.

    ```powershell
    Get-AzureRmSubscription
    ```

3. Définir l’abonnement approprié à l’aide de l’ID de l’abonnement. Remplacer `<subscriptionID>` avec l’ID de l’abonnement correcte.

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<subscriptionID>"
    ```

## <a name="get-the-storage-account"></a>Obtenir le compte de stockage

Vous avez besoin d’un compte de stockage dans Azure pour stocker l’image de machine virtuelle téléchargé. Vous pouvez utiliser un compte de stockage existant ou créez-en un. 

Pour afficher les comptes de stockage disponible, tapez :

```powershell
Get-AzureRmStorageAccount
```

Si vous souhaitez utiliser un compte de stockage existant, passez à la section [Télécharger l’image de la machine virtuelle](#upload-the-vm-vhd-to-your-storage-account) .

Si vous avez besoin créer un compte de stockage, procédez comme suit :

1. Vous avez besoin du nom du groupe de ressources où le compte de stockage doit être créé. Pour rechercher tous les groupes de ressources qui se trouvent dans votre abonnement, tapez :

    ```powershell
    Get-AzureRmResourceGroup
    ```

Pour créer un groupe de ressources nommé **myResourceGroup** dans la région **Ouest américain** , tapez :

    ```powershell
    New-AzureRmResourceGroup -Name myResourceGroup -Location "West US"
    ```

2. Créer un compte de stockage nommé **mystorageaccount** dans ce groupe de ressources à l’aide de l’applet de commande [New-AzureRmStorageAccount](https://msdn.microsoft.com/library/mt607148.aspx) :

    ```powershell
    New-AzureRmStorageAccount -ResourceGroupName myResourceGroup -Name mystorageaccount -Location "West US" -SkuName "Standard_LRS" -Kind "Storage"
    ```
            
    Valeurs valides pour - SkuName sont :

    - **Standard_LRS** - stockage localement redondants. 
    - **Standard_ZRS** - stockage redondante dans une Zone.
    - **Standard_GRS** - stockage redondants Geo. 
    - **Standard_RAGRS** - accès en lecture geo redondant stockage. 
    - **Premium_LRS** - stockage localement redondants Premium. 



## <a name="upload-the-vhd-to-your-storage-account"></a>Télécharger le disque dur virtuel à votre compte de stockage

Utiliser l’applet de commande [Add-AzureRmVhd](https://msdn.microsoft.com/library/mt603554.aspx) pour télécharger l’image dans un conteneur dans votre compte de stockage. Cet exemple transfère le fichier **myVHD.vhd** à partir de `"C:\Users\Public\Documents\Virtual hard disks\"` à un stockage compte nommé **mystorageaccount** dans le groupe de ressources **myResourceGroup** . Le fichier sera placé dans le conteneur nommé **mycontainer** et le nouveau nom de fichier sera **myUploadedVHD.vhd**.

```powershell
$rgName = "myResourceGroup"
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzureRmVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


En cas de réussite, vous obtenez une réponse qui ressemble à ceci :

```
  C:\> Add-AzureRmVhd -ResourceGroupName myResourceGroup -Destination https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
  MD5 hash is being calculated for the file C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd.
  MD5 hash calculation is completed.
  Elapsed time for the operation: 00:03:35
  Creating new page blob of size 53687091712...
  Elapsed time for upload: 01:12:49

  LocalFilePath           DestinationUri
  -------------           --------------
  C:\Users\Public\Doc...  https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd
```

En fonction de votre connexion réseau et la taille de votre fichier de disque dur virtuel, cette commande peut prendre un certain temps


## <a name="next-steps"></a>Étapes suivantes

- [Créer une machine virtuelle dans Azure à partir d’un disque dur virtuel GRG](virtual-machines-windows-create-vm-generalized.md)
- [Créer une machine virtuelle dans Azure à partir d’un disque dur virtuel spécialisé](virtual-machines-windows-create-vm-specialized.md) en joignant comme un disque du système d’exploitation lorsque vous créez un nouvel ordinateur virtuel.


