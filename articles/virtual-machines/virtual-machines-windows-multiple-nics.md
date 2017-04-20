<properties
   pageTitle="Créer une machine virtuelle Windows avec plusieurs cartes réseau | Microsoft Azure"
   description="Apprenez à créer une machine virtuelle Windows avec plusieurs cartes réseau liée à l’aide de PowerShell Azure ou le Gestionnaire de ressources de modèles."
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
   ms.workload="infrastructure"
   ms.date="10/27/2016"
   ms.author="iainfou"/>

# <a name="creating-a-windows-vm-with-multiple-nics"></a>Création d’une machine virtuelle Windows avec plusieurs cartes réseau
Vous pouvez créer une machine virtuelle () dans Azure qui possède plusieurs interfaces réseau virtuel (cartes réseau) est jointes à. Un scénario courant serait d’avoir différents sous-réseaux pour la connectivité frontale et principale, ou sur un réseau dédié à une solution de surveillance ou de sauvegarde. Cet article fournit des commandes rapides permettant de créer une machine virtuelle avec plusieurs cartes réseau est jointe à. Pour plus d’informations, notamment comment créer plusieurs cartes réseau dans vos propres scripts PowerShell, en savoir plus sur le [déploiement des machines virtuelles plusieurs cartes](../virtual-network/virtual-network-deploy-multinic-arm-ps.md). Différentes [tailles machine virtuelle](virtual-machines-windows-sizes.md) prise en charge d’un nombre variable de cartes réseau, donc dimensionner votre machine virtuelle en conséquence.

>[AZURE.WARNING] Vous devez joindre plusieurs cartes réseau lorsque vous créez une machine virtuelle, vous ne pouvez pas ajouter des cartes réseau pour une machine virtuelle existante. Vous pouvez [créer une machine virtuelle basée sur les disques virtuels d’origine](virtual-machines-windows-vhd-copy.md) et créer plusieurs cartes réseau lorsque vous déployez la machine virtuelle.

## <a name="create-core-resources"></a>Créer des ressources de base
Vérifiez que vous disposez des [dernières Azure PowerShell installé et configuré](../powershell-install-configure.md). Connectez-vous à votre compte Azure :

```powershell
Login-AzureRmAccount
```

Dans les exemples suivants, remplacez les noms de paramètres exemple avec vos propres valeurs. Exemples de noms de paramètre inclus `myResourceGroup`, `mystorageaccount`, et `myVM`.

Tout d’abord, créez un groupe de ressources. L’exemple suivant crée un groupe de ressources nommé `myResourceGroup` dans les `WestUs` emplacement :

```powershell
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "WestUS"
```

Créer un compte de stockage pour mettre en attente de vos ordinateurs virtuels. L’exemple suivant crée un compte de stockage nommé `mystorageaccount`:

```powershell
$storageAcc = New-AzureRmStorageAccount -ResourceGroupName "myResourceGroup" `
    -Location "WestUS" -Name "mystorageaccount" `
    -Kind "Storage" -SkuName "Premium_LRS" 
```

## <a name="create-virtual-network-and-subnets"></a>Créer des sous-réseaux et réseau virtuel
Définir les deux sous-réseaux réseau virtuel - un pour le trafic frontal et un pour le trafic principale. L’exemple suivant définit deux sous-réseaux, nommés `mySubnetFrontEnd` et `mySubnetBackEnd`:

```powershell
$mySubnetFrontEnd = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnetFrontEnd" `
    -AddressPrefix "192.168.1.0/24"
$mySubnetBackEnd = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnetBackEnd" `
    -AddressPrefix "192.168.2.0/24"
```

Créer votre réseau virtuel et sous-réseaux. L’exemple suivant crée un réseau virtuel nommé `myVnet`:

```powershell
$myVnet = New-AzureRmVirtualNetwork -ResourceGroupName "myResourceGroup" `
    -Location "WestUS" -Name "myVnet" -AddressPrefix "192.168.0.0/16" `
    -Subnet $mySubnetFrontEnd,$mySubnetBackEnd
```


## <a name="create-multiple-nics"></a>Créer plusieurs cartes réseau
Créez deux cartes réseau, joindre une carte réseau au sous-réseau frontal et une carte réseau au sous-réseau principale. L’exemple suivant crée deux cartes réseau, nommé `myNic1` et `myNic2`:

```powershell
$frontEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetFrontEnd'}
$myNic1 = New-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Location "WestUS" -Name "myNic1" -SubnetId $frontEnd.Id

$backEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetBackEnd'}
$myNic2 = New-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Location "WestUS" -Name "myNic2" -SubnetId $backEnd.Id
```

Vous créez habituellement également un [groupe de sécurité réseau](../virtual-network/virtual-networks-nsg.md) ou [l’équilibrage de charge](../load-balancer/load-balancer-overview.md) pour aider à gérer et distribuer le trafic sur vos ordinateurs virtuels. L’article [machine virtuelle plusieurs cartes plus détaillée](../virtual-network/virtual-network-deploy-multinic-arm-ps.md) la procédure de création d’un groupe de sécurité réseau et en affectant des cartes réseau.


## <a name="create-the-virtual-machine"></a>Créer la machine virtuelle
Maintenant commencer à créer votre configuration machine virtuelle. Chaque taille de la mémoire virtuelle a une limite pour le nombre total de cartes réseau que vous pouvez ajouter à une machine virtuelle. En savoir plus sur les [tailles de machine virtuelle Windows](virtual-machines-windows-sizes.md). 

Tout d’abord, définissez vos informations d’identification machine virtuelle sur le `$cred` variable comme suit :

```powershell
$cred = Get-Credential
```

L’exemple suivant définit une machine virtuelle nommée `myVM` et utilise une taille de mémoire virtuelle qui prend en charge jusqu'à deux cartes réseau (`Standard_DS2_v2`) :

```powershell
$vmConfig = New-AzureRmVMConfig -VMName "myVM" -VMSize "Standard_DS2_v2"
```

Créez le reste de votre configuration de la machine virtuelle. L’exemple suivant crée un ordinateur Windows Server 2012 R2 virtuel :

```powershell
$vmConfig = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName Te"MyVM" `
    -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
$vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig -PublisherName "MicrosoftWindowsServer" `
    -Offer "WindowsServer" -Skus "2012-R2-Datacenter" -Version "latest"
```

Joindre deux cartes réseau que vous avez créé précédemment :

```powershell
$vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $myNic1.Id -Primary
$vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $myNic2.Id
```

Configurer le stockage et le disque virtuel pour votre nouvelle machine virtuelle :

```powershell
$blobPath = "vhds/WindowsVMosDisk.vhd"
$osDiskUri = $storageAcc.PrimaryEndpoints.Blob.ToString() + $blobPath
$diskName = "windowsvmosdisk"
$vmConfig = Set-AzureRmVMOSDisk -VM $vmConfig -Name $diskName -VhdUri $osDiskUri `
    -CreateOption "fromImage"
```

Enfin, créez une machine virtuelle :

```powershell
New-AzureRmVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "WestUS"
```

## <a name="creating-multiple-nics-using-resource-manager-templates"></a>Création de plusieurs cartes réseau à l’aide de modèles de gestionnaire de ressources
Azure modèles Gestionnaire de ressources permet de définir votre environnement déclaratives fichiers JSON. Vous pouvez lire une [vue d’ensemble du Gestionnaire de ressources Azure](../azure-resource-manager/resource-group-overview.md). Gestionnaire de ressources modèles permettent de créer plusieurs instances d’une ressource au cours du déploiement, telles que la création de plusieurs cartes réseau. *Copie* vous permet de spécifier le nombre d’instances à créer :

```bash
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

En savoir plus sur la [Création de plusieurs instances à l’aide de *copie*](../resource-group-create-multiple.md). 

Vous pouvez également utiliser un `copyIndex()` puis ajouter un nombre à un nom de la ressource, qui permet de créer `myNic1`, `MyNic2`, etc.. La figure suivante montre un exemple d’ajout de la valeur d’index :

```bash
"name": "[concat('myNic', copyIndex())]", 
```

Vous pouvez lire un exemple complet de la [Création de plusieurs cartes réseau à l’aide de modèles de gestionnaire de ressources](../virtual-network/virtual-network-deploy-multinic-arm-template.md).

## <a name="next-steps"></a>Étapes suivantes
Veillez à examiner les [tailles machine virtuelle Windows](virtual-machines-windows-sizes.md) lorsque vous tentez de créer une machine virtuelle avec plusieurs cartes réseau. Soyez attentif au nombre maximal de cartes réseau prend en charge la taille de chaque machine virtuelle. 

N’oubliez pas que vous ne pouvez pas ajouter des cartes réseau pour une machine virtuelle existante, vous devez créer toutes les cartes réseau lorsque vous déployez la machine virtuelle. Lorsque vous planifiez vos déploiements pour vous assurer que vous disposez de tous les la connectivité réseau requis dès le début intégrées.