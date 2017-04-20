<properties
    pageTitle="Créer une copie de votre ordinateur Windows virtuel | Microsoft Azure"
    description="Apprenez à créer une copie de votre ordinateur de virtuel Azure spécialisées exécutant Windows, dans le modèle de déploiement du Gestionnaire de ressources."
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
    ms.date="09/21/2016"
    ms.author="cynthn"/>

# <a name="create-a-vm-from-a-specialized-vhd"></a>Créer une machine virtuelle à partir d’un disque dur virtuel spécialisé

Créer une nouvelle machine virtuelle en associant un disque dur virtuel spécialisé que le disque du système d’exploitation à l’aide de Powershell. Un disque dur virtuel spécialisé conserve les comptes d’utilisateurs, applications et autres données d’état à partir de votre ordinateur virtuel d’origine. 

Si vous voulez créer une machine virtuelle à partir d’un disque dur virtuel GRG, voir [créer une machine virtuelle à partir d’une image de disque dur virtuel GRG](virtual-machines-windows-create-vm-generalized.md).

## <a name="create-the-subnet-and-vnet"></a>Créer le sous-réseau et vNet

Créer la vNet et sous réseau du [réseau virtuel](../virtual-network/virtual-networks-overview.md).

1. Créer le sous-réseau. Cet exemple crée un sous-réseau nommé **mySubNet**, dans le groupe de ressources **myResourceGroup**et affecte le préfixe d’adresse sous-réseau **10.0.0.0/24**.

    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubNet"
    $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```

2. Créer la vNet. Cet exemple définit le nom de réseau virtuel peut être **myVnetName**, l’emplacement US **Ouest**et le préfixe d’adresse pour le réseau virtuel à **10.0.0.0/16**. 

    ```powershell
    $location = "West US"
    $vnetName = "myVnetName"
    $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    
            
## <a name="create-a-public-ip-address-and-nic"></a>Créer une adresse IP publique et carte réseau

Pour activer la communication avec la machine virtuelle dans le réseau virtuel, vous avez besoin d’une [adresse IP publique](../virtual-network/virtual-network-ip-addresses-overview-arm.md) et une interface réseau.

1. Créer l’adresse IP publique. Dans cet exemple, le nom d’adresses IP public est défini sur **myIP**.

    ```powershell
    $ipName = "myIP"
    $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location -AllocationMethod Dynamic
    ```       

2. Créer la carte réseau. Dans cet exemple, le nom de la carte réseau est défini sur **myNicName**.

    ```powershell
    $nicName = "myNicName"
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
    ```

## <a name="create-the-network-security-group-and-an-rdp-rule"></a>Créer une règle RDP et le groupe de sécurité réseau

Pour pouvoir se connecter à votre ordinateur virtuel à l’aide de RDP, vous devez avoir une règle de sécurité qui autorise l’accès RDP sur le port 3389. Étant donné que le disque dur virtuel pour la nouvelle machine virtuelle a été créé à partir d’un existant spécialisée machine virtuelle, une fois que la machine virtuelle est créée, vous pouvez utiliser un compte existant à partir de l’ordinateur virtuel source qui disposaient d’autorisations pour vous connecter à l’aide de RDP.

Cet exemple montre le nom NSG **myNsg** et le nom de la règle RDP à **myRdpRule**.

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389

$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
```

Pour plus d’informations sur les points de terminaison et règles NSG, voir [ouverture de ports pour une machine virtuelle dans Azure à l’aide de PowerShell](virtual-machines-windows-nsg-quickstart-powershell.md).

## <a name="create-the-vm-configuration"></a>Créer la configuration de la machine virtuelle

Définir la configuration de la machine virtuelle pour joindre le disque dur virtuel copié en tant que le disque dur virtuel du système d’exploitation.


```powershell
    # Set the URI for the VHD that you want to use. In this example, the VHD file named "myOsDisk.vhd" is kept in a storage account named "myStorageAccount" in a container named "myContainer".
    $osDiskUri = "https://myStorageAccount.blob.core.windows.net/myContainer/myOsDisk.vhd"
    
    #Set the VM name and size. This example sets the VM name to "myVM" and the VM size to "Standard_A2".
    $vmName = "myVM"
    $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A2"

    #Add the NIC
    $vm = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id

    #Add the OS disk by using the URL of the copied OS VHD. In this example, when the OS disk is created, the term "osDisk" is appened to the VM name to create the OS disk name. This example also specifies that this Windows-based VHD should be attached to the VM as the OS disk.
    $osDiskName = $vmName + "osDisk"
    $vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption attach -Windows
```


Si vous avez des disques de données qui doivent être associés à la machine virtuelle, vous devez également ajouter les éléments suivants : 

```powershell
    # Optional: Add data disks by using the URLs of the copied data VHDs at the appropriate Logical Unit Number (Lun).
    $dataDiskName = $vmName + "dataDisk"
    $vm = Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -VhdUri $dataDiskUri -Lun 0 -CreateOption attach
```

Les données et le système d’exploitation disque URL ressembler à ceci : `https://StorageAccountName.blob.core.windows.net/BlobContainerName/DiskName.vhd`. Vous pouvez trouver cette sur le portail en accédant au conteneur de stockage cible, en cliquant sur le système d’exploitation ou les données disque dur virtuel qui a été copié et puis copier le contenu de l’URL.


## <a name="create-the-vm"></a>Créer la machine virtuelle

Créer la machine virtuelle en utilisant les configurations que nous venez de créer.

```powershell
#Create the new VM
New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm
```

Si cette commande a réussi, vous verrez sortie comme suit :

```
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   
 
```
 
## <a name="verify-that-the-vm-was-created"></a>Vérifier que la machine virtuelle a été créée 
 
Vous devriez voir la machine virtuelle nouvellement créée dans le [portail Azure](https://portal.azure.com), sous **Parcourir** > **machines virtuelles**, ou en utilisant les commandes PowerShell suivantes :

```powershell
    $vmList = Get-AzureRmVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>Étapes suivantes

Pour vous connecter à votre nouvelle machine virtuelle, accédez à la machine virtuelle dans le [portail](https://portal.azure.com), cliquez sur **se connecter**et ouvrez le fichier RDP Remote Desktop. Utilisez les informations d’identification de compte de votre machine virtuelle d’origine pour se connecter à votre nouvelle machine virtuelle. Pour plus d’informations, voir [comment vous connecter et ouvrir une session sur une machine virtuelle Azure exécutant Windows](virtual-machines-windows-connect-logon.md).







