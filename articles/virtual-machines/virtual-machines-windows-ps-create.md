<properties
    pageTitle="Créer une machine virtuelle Azure à l’aide de PowerShell | Microsoft Azure"
    description="Utiliser PowerShell Azure et Azure le Gestionnaire de ressources pour créer facilement une nouvelle machine virtuelle exécutant Windows Server."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/21/2016"
    ms.author="davidmu"/>

# <a name="create-a-windows-vm-using-resource-manager-and-powershell"></a>Créer une machine virtuelle Windows à l’aide du Gestionnaire de ressources et PowerShell

Cet article vous explique comment créer rapidement une Machine virtuelle Azure exécutant Windows Server et les ressources que nécessaires à l’aide du [Gestionnaire de ressources](../azure-resource-manager/resource-group-overview.md) et PowerShell. 

Toutes les étapes décrites dans cet article sont nécessaires pour créer une machine virtuelle et il doit prendre environ 30 minutes pour effectuer les étapes. Remplacer les valeurs de paramètre exemple dans les commandes des noms représentatives pour votre environnement.

## <a name="step-1-install-azure-powershell"></a>Étape 1 : Installer PowerShell Azure

Découvrez [comment installer et configurer Azure PowerShell](../powershell-install-configure.md) pour plus d’informations sur l’installation de la dernière version d’Azure PowerShell, en sélectionnant votre abonnement et se connecter à votre compte.
        
## <a name="step-2-create-a-resource-group"></a>Étape 2 : Créer un groupe de ressources

Toutes les ressources doivent être contenus dans un groupe de ressources, donc vous permet de créer d’abord.  

1. Obtenir une liste des emplacements disponibles où les ressources peuvent être créés.

    ```powershell
    Get-AzureRmLocation | sort Location | Select Location
    ```

2. Définir l’emplacement pour les ressources. Cette commande définit l’emplacement **centralus**.

    ```powershell
    $location = "centralus"
    ```
    
3. Créer un groupe de ressources. Cette commande crée le groupe de ressources nommé **myResourceGroup** à l’emplacement que vous avez défini.

    ```powershell
    $myResourceGroup = "myResourceGroup"
    New-AzureRmResourceGroup -Name $myResourceGroup -Location $location
    ```
    
## <a name="step-3-create-a-storage-account"></a>Étape 3 : Créer un compte de stockage

Un [compte de stockage](../storage/storage-introduction.md) est nécessaire pour stocker le disque dur virtuel qui est utilisé par la machine virtuelle que vous créez. Noms des comptes de stockage doivent être comprise entre 3 et 24 caractères et peuvent contenir des nombres et les lettres minuscules uniquement.

1. Testez le nom du compte de stockage pour que l’unicité. Cette commande teste le nom **myStorageAccount**.

    ```powershell
    $myStorageAccountName = "mystorageaccount"
    Get-AzureRmStorageAccountNameAvailability $myStorageAccountName
    ```
    
    Si cette commande renvoie la **valeur True**, votre nom proposé est unique dans Azure. 
    
2. À présent, créez le compte de stockage.
    
    ```powershell    
    $myStorageAccount = New-AzureRmStorageAccount -ResourceGroupName $myResourceGroup -Name $myStorageAccountName -SkuName "Standard_LRS" -Kind "Storage" -Location $location
    ```
    
## <a name="step-4-create-a-virtual-network"></a>Étape 4 : Créer un réseau virtuel

Toutes les machines virtuelles font partie d’un [réseau virtuel](../virtual-network/virtual-networks-overview.md).

1. Créer un sous-réseau pour le réseau virtuel. Cette commande crée un sous-réseau nommé **mySubnet** avec un préfixe d’adresse 10.0.0.0/24.
        
    ```powershell
    $mySubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet" -AddressPrefix 10.0.0.0/24
    ```
    
2. À présent, créez le réseau virtuel. Cette commande crée un réseau virtuel nommé **myVnet** à l’aide du sous-réseau que vous avez créé et un préfixe d’adresse de **10.0.0.0/16**.

    ```powershell
    $myVnet = New-AzureRmVirtualNetwork -Name "myVnet" -ResourceGroupName $myResourceGroup -Location $location -AddressPrefix 10.0.0.0/16 -Subnet $mySubnet
    ```
        
## <a name="step-5-create-a-public-ip-address-and-network-interface"></a>Étape 5 : Créer une interface de réseau et adresse IP publique

Pour activer la communication avec la machine virtuelle dans le réseau virtuel, vous avez besoin d’une [adresse IP publique](../virtual-network/virtual-network-ip-addresses-overview-arm.md) et une interface réseau.

1. Créer l’adresse IP. Cette commande crée une adresse IP publique nommée **myPublicIp** avec une méthode d’allocation de **dynamique**.
 
    ```powershell
    $myPublicIp = New-AzureRmPublicIpAddress -Name "myPublicIp" -ResourceGroupName $myResourceGroup -Location $location -AllocationMethod Dynamic
    ```
        
2. Créer l’interface réseau. Cette commande crée une interface de réseau nommée **myNIC**.

    ```powershell
    $myNIC = New-AzureRmNetworkInterface -Name "myNIC" -ResourceGroupName $myResourceGroup -Location $location -SubnetId $myVnet.Subnets[0].Id -PublicIpAddressId $myPublicIp.Id
    ```
       
## <a name="step-6-create-a-virtual-machine"></a>Étape 6 : Créer une machine virtuelle

À présent que tous les éléments en place, il est temps de créer la machine virtuelle.

1. Exécutez cette commande pour définir le nom du compte administrateur et mot de passe de la machine virtuelle.

        $cred = Get-Credential -Message "Type the name and password of the local administrator account."
        
    Le mot de passe doit être en 12-123 caractères et avoir au moins une minuscule, un caractère en majuscule, un nombre et un caractère spécial. 
        
2. Créer l’objet de configuration de la machine virtuelle. Cette commande crée un objet de configuration nommé **myVmConfig** qui définit le nom de la machine virtuelle et la taille de la machine virtuelle.

    ```powershell
    $myVm = New-AzureRmVMConfig -VMName "myVM" -VMSize "Standard_DS1_v2"
    ```
     
    Pour obtenir une liste de tailles disponibles pour une machine virtuelle, voir [formats pour les machines virtuelles dans Azure](virtual-machines-windows-sizes.md) .
    
3. Configurer les paramètres de système d’exploitation pour la machine virtuelle. Cette commande définit le nom de l’ordinateur, le type de système d’exploitation et informations d’identification de compte pour la machine virtuelle.

    ```powershell
    $myVM = Set-AzureRmVMOperatingSystem -VM $myVM -Windows -ComputerName "myVM" -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    ```
    
4. Définir l’image à utiliser pour la mise en service de la machine virtuelle. Cette commande définit l’image de Windows Server à utiliser pour la machine virtuelle. 

    ```powershell
    $myVM = Set-AzureRmVMSourceImage -VM $myVM -PublisherName "MicrosoftWindowsServer" -Offer "WindowsServer" -Skus "2012-R2-Datacenter" -Version "latest"
    ```
        
    Pour plus d’informations sur la sélection des images à utiliser, reportez-vous à [Naviguer et sélectionner les images machine virtuelle Windows dans Azure avec PowerShell ou de l’infrastructure du langage commun](virtual-machines-windows-cli-ps-findimage.md).
        
5. Ajoutez l’interface réseau que vous avez créé à la configuration.

    ```powershell
    $myVM = Add-AzureRmVMNetworkInterface -VM $myVM -Id $myNIC.Id
    ```
        
6. Définir le nom et l’emplacement du disque dur machine virtuelle. Le fichier de disque dur virtuel est stocké dans un conteneur. Cette commande crée le disque dans un conteneur nommé **vhds/WindowsVMosDisk.vhd** dans le compte de stockage que vous avez créé.

    ```powershell
    $blobPath = "vhds/myOsDisk1.vhd"
    $osDiskUri = $myStorageAccount.PrimaryEndpoints.Blob.ToString() + $blobPath
    ```
        
7. Ajoutez les informations de disque système d’exploitation à la configuration de la machine virtuelle. Remplacez la valeur de **$diskName** par un nom pour le disque système d’exploitation. Créez la variable et ajoutez les informations de disque à la configuration.
    
    ```powershell
    $vm = Set-AzureRmVMOSDisk -VM $myVM -Name "myOsDisk1" -VhdUri $osDiskUri -CreateOption fromImage
    ```
        
8. Enfin, créez la machine virtuelle.

    ```
    New-AzureRmVM -ResourceGroupName $myResourceGroup -Location $location -VM $myVM
    ```
                                  
## <a name="next-steps"></a>Étapes suivantes

- S’il y a des problèmes avec le déploiement, une étape suivante serait pour examiner les [déploiements de groupe de ressources de résolution des problèmes avec Azure portail](../resource-manager-troubleshoot-deployments-portal.md)
- Découvrez comment gérer la machine virtuelle que vous avez créé en passant [machines virtuelles gérer à l’aide du Gestionnaire de ressources Azure et PowerShell](virtual-machines-windows-ps-manage.md).
- Tirer parti de l’utilisation d’un modèle pour créer une machine virtuelle en utilisant les informations dans [créer une machine virtuelle Windows avec un modèle de gestionnaire de ressources](virtual-machines-windows-ps-template.md)
