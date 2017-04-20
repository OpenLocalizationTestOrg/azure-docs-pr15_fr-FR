<properties
   pageTitle="Avantages d’utilisation hybride Azure pour Windows Server | Microsoft Azure"
   description="Découvrez comment optimiser les avantages de votre Windows Server Software Assurance pour retirer des licences locales Azure"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="07/13/2016"
   ms.author="georgem"/>

# <a name="azure-hybrid-use-benefit-for-windows-server"></a>Avantages d’utilisation hybride Azure pour Windows Server

Pour les clients à l’aide de Windows Server avec Software Assurance, vous pouvez placer vos localement licences Windows Server à Azure et exécuter des machines virtuelles Windows Server dans Azure un coût réduit. L’avantage Azure hybride utiliser permet de s’exécutent Windows Server machines virtuelles dans Azure et uniquement payer pour le taux de base cluster. Pour plus d’informations, voir les [avantages d’utilisation hybride Azure licences page](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Cet article explique comment déployer des machines virtuelles de serveur Windows dans Azure à utiliser cet avantage licence.

> [AZURE.NOTE] Vous ne pouvez pas utiliser images Azure Marketplace pour déployer des machines virtuelles du serveur Windows utilisant l’avantage d’utiliser Azure hybride. Vous devez déployer vos ordinateurs virtuels à l’aide de PowerShell ou le Gestionnaire de ressources de modèles pour inscrire correctement vos ordinateurs virtuels comme éligible pour cluster base taux remise.

## <a name="pre-requisites"></a>Conditions préalables
Il existe quelques conditions préalables pour pouvoir utiliser Azure hybride utilisation avantage pour Windows Server machines virtuelles dans Azure :

- Avoir installé le module Azure PowerShell
- Votre disque dur virtuel Windows Server ont téléchargées sur le stockage Azure

### <a name="install-azure-powershell"></a>Installer PowerShell Azure
Vérifiez que vous avez [installé et configuré la dernière PowerShell Azure](../powershell-install-configure.md). Même si vous vous apprêtez à déployer vos ordinateurs virtuels à l’aide de modèles de gestionnaire de ressources, vous avez encore besoin d’Azure PowerShell installé pour télécharger votre disque dur virtuel Windows Server (voir l’étape suivante).

### <a name="upload-a-windows-server-vhd"></a>Télécharger un disque dur virtuel Windows Server

Pour déployer un serveur Windows Azure ordinateur virtuel, vous devez d’abord créer un disque dur virtuel qui contient votre version de Windows Server base. Ce disque dur virtuel doit être correctement préparé via Sysprep avant de le télécharger sur Azure. Vous pouvez [en savoir plus sur la configuration requise de disque dur virtuel et processus Sysprep](./virtual-machines-windows-upload-image.md) et [Prise en charge Sysprep des rôles de serveur](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles). Sauvegarder la machine virtuelle avant d’exécuter Sysprep. Une fois que vous avez préparé votre disque dur virtuel, téléchargez le disque dur virtuel à votre compte de stockage Azure en utilisant la `Add-AzureRmVhd` applet de commande comme suit :

```
Add-AzureRmVhd -ResourceGroupName MyResourceGroup -Destination "https://mystorageaccount.blob.core.windows.net/vhds/myvhd.vhd" -LocalFilePath 'C:\Path\To\myvhd.vhd'
```

> [AZURE.NOTE] Microsoft SQL Server, SharePoint Server et Dynamics peuvent également utiliser votre licence Software Assurance. Vous avez besoin préparer l’image de Windows Server par l’installation de vos composants d’application et fournir des clés de licence en conséquence, puis téléchargement de l’image de disque dans Azure. Passez en revue la documentation appropriée pour l’exécution de Sysprep avec votre application, telles que les [Considérations pour l’installation de SQL Server à l’aide de Sysprep](https://msdn.microsoft.com/library/ee210754.aspx) ou [créer une Image de référence SharePoint Server 2016 (Sysprep)](http://social.technet.microsoft.com/wiki/contents/articles/33789.build-a-sharepoint-server-2016-reference-image-sysprep.aspx).

Vous pouvez également en savoir plus sur le [téléchargement du disque dur virtuel au processus d’Azure](./virtual-machines-windows-upload-image.md#upload-the-vm-image-to-your-storage-account).

> [AZURE.TIP] Cet article se concentre sur le déploiement des machines virtuelles Windows Server. Vous pouvez également déployer Windows Client machines virtuelles de la même manière. Dans les exemples suivants, vous remplacez `Server` avec `Client` correctement.

## <a name="deploy-a-vm-via-powershell-quick-start"></a>Déployer une machine virtuelle via PowerShell-démarrage rapide
Lorsque vous déployez votre machine virtuelle Server Windows via PowerShell, vous avez un paramètre supplémentaire pour `-LicenseType`. Une fois que votre disque dur virtuel téléchargé dans Azure, vous créez une nouvelle machine virtuelle en utilisant `New-AzureRmVM` et spécifier le type de licence comme suit :

```
New-AzureRmVM -ResourceGroupName MyResourceGroup -Location "West US" -VM $vm -LicenseType Windows_Server
```

Vous pouvez [en savoir plus détaillées sur le déploiement d’une machine virtuelle dans Azure via PowerShell](./virtual-machines-windows-hybrid-use-benefit-licensing.md#deploy-windows-server-vm-via-powershell-detailed-walkthrough) ci-dessous, ou consultez un guide sur les différentes étapes pour [créer une machine virtuelle Windows à l’aide du Gestionnaire de ressources et PowerShell](./virtual-machines-windows-ps-create.md)plus descriptif.

## <a name="deploy-a-vm-via-resource-manager"></a>Déployer une machine virtuelle via le Gestionnaire de ressources
Dans vos modèles Gestionnaire de ressources, un paramètre supplémentaire pour `licenseType` peut être spécifié. Vous pouvez en savoir plus sur la [Création de modèles Azure le Gestionnaire de ressources](../resource-group-authoring-templates.md). Une fois que votre disque dur virtuel téléchargé dans Azure, vous modifier le modèle de gestionnaire de ressources pour inclure le type de licence dans le cadre du fournisseur cluster et déployer votre modèle comme d’habitude :

```
"properties": {  
   "licenseType": "Windows_Server",
   "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
   },
```
 
## <a name="verify-your-vm-is-utilizing-the-licensing-benefit"></a>Vérifier que votre machine virtuelle utilise l’avantage de licence
Une fois que vous avez déployé votre machine virtuelle via la méthode déploiement PowerShell ou le Gestionnaire de ressources, vérifiez que le type de licence avec `Get-AzureRmVM` comme suit :
 
```
Get-AzureRmVM -ResourceGroup MyResourceGroup -Name MyVM
```

Le résultat est semblable à ce qui suit :

```
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Server
```

Cela contraste avec la machine virtuelle suivante déployée sans Azure hybride utilisation avantage gestion des licences, par exemple une machine virtuelle déployée directement à partir de la galerie Azure :

```
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : 
```
 
## <a name="detailed-powershell-walkthrough"></a>PowerShell détaillées

Les étapes PowerShell détaillées ci-dessous montrent un déploiement complet d’un ordinateur virtuel. Vous pouvez lire plus de contexte en ce qui concerne les applets de commande réel et différents composants entraînerait la création de [créer une machine virtuelle Windows à l’aide du Gestionnaire de ressources et PowerShell](./virtual-machines-windows-ps-create.md). Étape permettant de créer votre groupe de ressources, le compte de stockage et le réseau virtuel, puis définir votre machine virtuelle et enfin créer votre machine virtuelle.
 
Tout d’abord, obtenir des informations d’identification en toute sécurité, définir un emplacement et un nom de groupe de ressources :

```
$cred = Get-Credential
$location = "West US"
$resourceGroupName = "TestLicensing"
```

Créer une adresse IP publique :

```
$publicIPName = "testlicensingpublicip"
$publicIP = New-AzureRmPublicIpAddress -Name $publicIPName -ResourceGroupName $resourceGroupName -Location $location -AllocationMethod Dynamic
```

Définir votre sous-réseau, carte réseau et VNET :

```
$subnetName = "testlicensingsubnet"
$nicName = "testlicensingnic"
$vnetName = "testlicensingvnet"
$subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/8
$vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $resourceGroupName -Location $location -AddressPrefix 10.0.0.0/8 -Subnet $subnetconfig
$nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $resourceGroupName -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $publicIP.Id
```

Nommez votre machine virtuelle et créer une configuration de la machine virtuelle :

```
$vmName = "testlicensing"
$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A1"
```

Définir votre système d’exploitation :

```
$computerName = "testlicensing"
$vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
```

Ajouter votre carte réseau de la machine virtuelle :

```
$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
```

Définir le compte de stockage à utiliser :

```
$storageAcc = Get-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -AccountName testlicensing
```

Télécharger votre disque dur virtuel, correctement préparé et joindre à votre ordinateur virtuel pour une utilisation :

```
$osDiskName = "licensing.vhd"
$osDiskUri = '{0}vhds/{1}{2}.vhd' -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName
$urlOfUploadedImageVhd = "https://testlicensing.blob.core.windows.net/vhd/licensing.vhd"
$vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption FromImage -SourceImageUri $urlOfUploadedImageVhd -Windows
```

Enfin, créez votre machine virtuelle et définir le type de licence pour utiliser les avantages d’utilisation hybride Azure :

```
New-AzureRmVM -ResourceGroupName $resourceGroupName -Location $location -VM $vm -LicenseType Windows_Server
```

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les [avantages d’utilisation hybride Azure licences](https://azure.microsoft.com/pricing/hybrid-use-benefit/).

En savoir plus sur [l’utilisation des modèles de gestionnaire de ressources](../azure-resource-manager/resource-group-overview.md).
