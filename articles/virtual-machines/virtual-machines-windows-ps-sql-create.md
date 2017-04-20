<properties
    pageTitle="Créer un serveur Machine virtuelle SQL dans Azure PowerShell (responsable de ressources) | Microsoft Azure"
    description="Fournit les étapes et des scripts PowerShell pour créer une machine virtuelle Azure avec images de la galerie machine virtuelle SQL Server."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="rothja"
    manager="jhubbard"
    editor=""
    tags="azure-resource-manager" />
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="10/25/2016"
    ms.author="jroth"/>

# <a name="provision-a-sql-server-virtual-machine-using-azure-powershell-resource-manager"></a>Configurer une machine virtuelle SQL Server à l’aide de PowerShell Azure (responsable de ressources)

> [AZURE.SELECTOR]
- [Portail](virtual-machines-windows-portal-sql-server-provision.md)
- [PowerShell](virtual-machines-windows-ps-sql-create.md)

## <a name="overview"></a>Vue d’ensemble

Ce didacticiel montre comment créer une seule machine virtuelle Azure en utilisant le modèle de déploiement **d’Azure le Gestionnaire de ressources** à l’aide des applets de commande PowerShell Azure. Dans ce didacticiel, nous allons créer une seule machine virtuelle à l’aide d’un lecteur de disque unique à partir d’une image dans la galerie SQL. Nous allons créer de nouveaux fournisseurs pour le stockage, le réseau et les ressources de cluster qui seront utilisés par la machine virtuelle. Si vous avez des fournisseurs existants pour une de ces ressources, vous pouvez utiliser ces fournisseurs à la place.

Si vous avez besoin de la version classique de cette rubrique, voir [mise en service une machine virtuelle SQL Server à l’aide de Azure PowerShell classique](virtual-machines-windows-classic-ps-sql-create.md).

## <a name="prerequisites"></a>Conditions préalables

Pour ce didacticiel, vous devez :

- Un compte Azure et votre abonnement avant de commencer. Si vous n’en avez pas, inscrivez-vous pour une [version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/).
- [PowerShell azure)](../powershell-install-configure.md), version minimale de 1.4.0 ou version ultérieure (ce didacticiel écrite à l’aide de version1.5.0).
    - Pour obtenir votre version, tapez **Get-Module Azure ListAvailable**.

## <a name="configure-your-subscription"></a>Configurer votre abonnement

Ouvrez Windows PowerShell et établir un accès à votre compte Azure en exécutant l’applet de commande suivante. S’affiche avec un écran de connexion à entrer vos informations d’identification. Utiliser la même adresse de messagerie et mot de passe que vous utilisez pour vous connecter au portail Azure.

    Add-AzureRmAccount

Après la connexion avec succès, vous verrez certaines informations sur l’écran qui inclut le SubscriptionId avec laquelle vous vous connectez dans. Il s’agit de l’abonnement dans lequel les ressources de ce didacticiel seront créés, sauf si vous modifiez à un autre abonnement. Si vous avez plusieurs SubscriptionIds, exécutez l’applet de commande suivante pour renvoyer une liste de tous vos SubscriptionIds :

    Get-AzureRmSubscription

Pour passer à un autre SubscriptionID, exécutez l’applet avec votre SubscriptionId souhaité.

    Select-AzureRmSubscription -SubscriptionId xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx

## <a name="define-image-variables"></a>Définir des variables de l’image

Pour simplifier l’utilisation et la compréhension du script terminé à partir de ce didacticiel, nous allons commencer par définir un certain nombre de variables. Modifiez les valeurs de paramètre comme vous vos besoins, mais attention restrictions liées à la longueur des noms et des caractères spéciaux lorsque vous modifiez les valeurs fournies de dénomination.

### <a name="location-and-resource-group"></a>Emplacement et un groupe de ressources
Deux variables permet de définir la zone de données et le groupe de ressources dans lequel vous créerez les autres ressources de la machine virtuelle.

Modifier comme vous le souhaitez, puis exécutez les applets de commande suivantes pour initialisation ces variables.

    $Location = "SouthCentralUS"
    $ResourceGroupName = "sqlvm1"

### <a name="storage-properties"></a>Propriétés de stockage

Utilisez les variables suivantes pour définir le compte de stockage et le type de stockage devant être utilisé par la machine virtuelle.

Modifier comme vous le souhaitez, puis exécutez l’applet de commande suivante pour initialisation ces variables. Notez que dans cet exemple, nous utilisons [Premium stockage](../storage/storage-premium-storage.md)recommandé pour les charges de travail. Pour plus d’informations sur ce guide et les autres recommandations, voir [les procédures recommandées pour SQL Server dans le Machines virtuelles Azure](virtual-machines-windows-sql-performance.md).

    $StorageName = $ResourceGroupName + "storage"
    $StorageSku = "Premium_LRS"

### <a name="network-properties"></a>Propriétés du réseau

Utilisez les variables suivantes pour définir l’interface réseau, la méthode d’allocation TCP/IP, le nom du réseau virtuel, le nom de sous-réseau virtuel, la plage d’adresses IP pour le réseau virtuel, la plage d’adresses IP pour le sous-réseau et l’étiquette de nom de domaine public devant être utilisé par le réseau de la machine virtuelle.

Modifier comme vous le souhaitez, puis exécutez l’applet de commande suivante pour initialisation ces variables.

    $InterfaceName = $ResourceGroupName + "ServerInterface"
    $TCPIPAllocationMethod = "Dynamic"
    $VNetName = $ResourceGroupName + "VNet"
    $SubnetName = "Default"
    $VNetAddressPrefix = "10.0.0.0/16"
    $VNetSubnetAddressPrefix = "10.0.0.0/24"
    $DomainName = "sqlvm1"   

### <a name="virtual-machine-properties"></a>Propriétés de la machine virtuelle

Utiliser les variables suivantes pour définir le nom de la machine virtuelle, le nom de l’ordinateur, la taille de la machine virtuelle et le nom du disque système d’exploitation pour la machine virtuelle.

Modifier comme vous le souhaitez, puis exécutez l’applet de commande suivante pour initialisation ces variables.

    $VMName = $ResourceGroupName + "VM"
    $ComputerName = $ResourceGroupName + "Server"
    $VMSize = "Standard_DS13"
    $OSDiskName = $VMName + "OSDisk"

### <a name="image-properties"></a>Propriétés de l’image

Utilisez les variables suivantes pour définir l’image à utiliser pour la machine virtuelle. Dans cet exemple, l’image de SQL Server 2016 Enterprise est utilisée.

Modifier comme vous le souhaitez, puis exécutez l’applet de commande suivante pour initialisation ces variables.

    $PublisherName = "MicrosoftSQLServer"
    $OfferName = "SQL2016-WS2016"
    $Sku = "Enterprise"
    $Version = "latest"

Notez que vous pouvez obtenir une liste complète des offres d’image de SQL Server avec la commande Get-AzureRmVMImageOffer :

    Get-AzureRmVMImageOffer -Location 'East US' -Publisher 'MicrosoftSQLServer'

Et vous pouvez voir les références SKU disponibles pour une offre avec la commande Get-AzureRmVMImageSku. La commande suivante affiche que toutes les références SKU disponibles pour le **SQL2014SP1 WS2012R2** proposent.

    Get-AzureRmVMImageSku -Location 'East US' -Publisher 'MicrosoftSQLServer' -Offer 'SQL2014SP1-WS2012R2' | Select Skus

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Avec le modèle de déploiement Gestionnaire de ressources, le premier objet que vous créez est le groupe de ressources. Nous allons utiliser l’applet de commande [New-AzureRmResourceGroup](https://msdn.microsoft.com/library/mt759837.aspx) pour créer un groupe de ressources Azure et ses ressources avec le nom de groupe de ressources et l’emplacement défini par les variables que vous avez déjà initialisé.

Exécutez l’applet de commande suivante pour créer votre nouveau groupe de ressources.

    New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location

## <a name="create-a-storage-account"></a>Créer un compte de stockage

La machine virtuelle nécessite des ressources de stockage pour le disque système d’exploitation et pour les fichiers journaux et de données SQL Server. Pour simplifier, nous allons créer un seul disque pour les deux. Vous pouvez joindre des disques supplémentaires ultérieurement à l’aide de l’applet de commande [Ajouter Azure disque](https://msdn.microsoft.com/library/azure/dn495252.aspx) afin de placer vos données SQL Server et les journaux sur des disques dédiés. Nous allons utiliser l’applet de commande [New-AzureRmStorageAccount](https://msdn.microsoft.com/library/mt607148.aspx) pour créer un compte de stockage standard dans votre nouveau groupe de ressources et avec le nom de compte de stockage, le nom de référence (SKU) de stockage et l’emplacement défini à l’aide de variables que vous avez déjà initialisé.

Exécutez l’applet de commande suivante pour créer votre nouveau compte de stockage.  

    $StorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageName -SkuName $StorageSku -Kind "Storage" -Location $Location

## <a name="create-network-resources"></a>Créer des ressources de réseau

La machine virtuelle nécessite un certain nombre de ressources réseau pour la connectivité réseau.

- Chaque machine virtuelle nécessite un réseau virtuel.
- Un réseau virtuel doit avoir au moins un sous-réseau défini.
- Une interface réseau doit être définie avec un public ou une adresse IP privée.

### <a name="create-a-virtual-network-subnet-configuration"></a>Créer une configuration sous-réseau réseau virtuel

Nous allons commencer par créer une configuration sous-réseau pour notre réseau virtuel. Pour notre didacticiel, nous allons créer un sous-réseau par défaut à l’aide de l’applet de commande [New-AzureRmVirtualNetworkSubnetConfig](https://msdn.microsoft.com/library/mt619412.aspx) . Nous allons créer notre configuration sous-réseau réseau virtuel avec le préfixe de nom et l’adresse sous-réseau défini en utilisant les variables que vous avez déjà initialisé.

>[AZURE.NOTE] Vous pouvez définir d’autres propriétés de la configuration de sous-réseau réseau virtuel à l’aide de cette applet de commande, mais qui est dépasse le cadre de ce didacticiel.

Exécutez l’applet de commande suivante pour créer la configuration de votre sous-réseau virtuel.

    $SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix

### <a name="create-a-virtual-network"></a>Créer un réseau virtuel

Ensuite, nous allons créer notre réseau virtuel à l’aide de l’applet de commande [New-AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt603657.aspx) . Nous allons créer notre réseau virtuel dans votre nouveau groupe de ressources, avec le nom, emplacement et l’adresse, le préfixe défini en utilisant les variables que vous avez déjà initialisé et l’utilisation de la configuration de sous-réseau que vous avez défini à l’étape précédente.

Exécutez l’applet de commande suivante pour créer votre réseau virtuel.

    $VNet = New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName -Location $Location -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig

### <a name="create-the-public-ip-address"></a>Créer l’adresse IP publique

Maintenant que nous avons notre réseau virtuel défini, nous avons besoin de configurer une adresse IP pour la connectivité à la machine virtuelle. Pour ce didacticiel, nous allons créer une adresse IP publique à l’aide d’adressage pour prendre en charge de la connectivité Internet IP dynamique. Nous allons utiliser l’applet de commande [New-AzureRmPublicIpAddress](https://msdn.microsoft.com/library/mt603620.aspx) pour créer l’adresse IP dans le groupe de ressources créé prevously, avec le nom, un emplacement, une méthode d’allocation et une étiquette de nom de domaine DNS définie en utilisant les variables que vous avez déjà initialisé.

>[AZURE.NOTE] Vous pouvez définir d’autres propriétés de l’adresse IP à l’aide de cette applet de commande, mais qui est dépasse le cadre de ce didacticiel initial. Vous pouvez également créer une adresse privée ou une adresse avec une adresse statique, mais qui est également dépasse le cadre de ce didacticiel.

Exécutez l’applet de commande suivante pour créer votre adresse IP publique.

    $PublicIp = New-AzureRmPublicIpAddress -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName

### <a name="create-the-network-interface"></a>Créer l’interface de réseau

Nous sommes maintenant prêts à créer l’interface réseau proposant à notre machine virtuelle. Nous allons utiliser l’applet de commande [New-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619370.aspx) pour créer notre interface réseau dans le groupe de ressources créé précédemment et avec le nom, l’emplacement, la sous-réseau et l’adresse IP publique précédemment définie.

Exécutez l’applet de commande suivante pour créer votre interface réseau.

    $Interface = New-AzureRmNetworkInterface -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id

## <a name="configure-a-vm-object"></a>Configurer un objet de la mémoire virtuelle

Maintenant que nous avons des ressources de stockage et de réseau définies, nous sommes prêts à définir des ressources de cluster pour la machine virtuelle. Pour notre didacticiel, nous sera spécifier la taille de la machine virtuelle et différentes propriétés du système d’exploitation, spécifiez l’interface réseau que nous avons créée précédemment, définir le stockage blob et spécifiez le disque système d’exploitation.

### <a name="create-the-vm-object"></a>Créer l’objet machine virtuelle

Nous allons commencer en spécifiant la taille de la machine virtuelle. Pour ce didacticiel, nous devons spécifier un DS13. Nous allons utiliser l’applet de commande [New-AzureRmVMConfig](https://msdn.microsoft.com/library/mt603727.aspx) pour créer un objet configurable machine virtuelle avec le nom et la taille définie en utilisant les variables que vous avez déjà initialisé.

Exécutez l’applet de commande suivante pour créer l’objet ordinateur virtuel.

    $VirtualMachine = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize

### <a name="create-a-credential-object-to-hold-the-name-and-password-for-the-local-administrator-credentials"></a>Créer un objet d’informations d’identification pour mettre en attente le nom et le mot de passe pour les informations d’identification d’administrateur local

Avant que nous puissions définir les propriétés de système d’exploitation pour la machine virtuelle, nous avons besoin fournir les informations d’identification du compte d’administrateur local sous forme de chaîne sécurisée. Pour ce faire, nous allons utiliser l’applet de commande [Get-Credential](https://technet.microsoft.com/library/hh849815.aspx) .

Exécutez l’applet de commande suivante et, dans la fenêtre de demande d’informations d’identification Windows PowerShell, tapez le nom et le mot de passe à utiliser pour le compte d’administrateur local dans la machine virtuelle Windows.

    $Credential = Get-Credential -Message "Type the name and password of the local administrator account."

### <a name="set-the-operating-system-properties-for-the-virtual-machine"></a>Définir les propriétés du système d’exploitation pour la machine virtuelle

Nous sommes maintenant prêts définir les propriétés de système d’exploitation de la machine virtuelle. Nous allons utiliser l’applet de commande [Set-AzureRmVMOperatingSystem](https://msdn.microsoft.com/library/mt603843.aspx) pour définir le type de système d’exploitation Windows, nécessitent l' [agent de machine virtuelle](virtual-machines-windows-classic-agents-and-extensions.md) pour être installé, spécifier que l’applet de commande permet de mise à jour automatique et définir le nom de la machine virtuelle, le nom d’ordinateur et les informations d’identification en utilisant les variables que vous avez déjà initialisé.

Exécutez l’applet de commande suivante pour définir les propriétés du système d’exploitation de votre machine virtuelle.

    $VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate

### <a name="add-the-network-interface-to-the-virtual-machine"></a>Ajouter l’interface du réseau de la machine virtuelle

Ensuite, nous allons ajouter l’interface réseau que nous avons créé précédemment à la machine virtuelle. Nous allons utiliser l’applet de commande [Add AzureRmVMNetworkInterface](https://msdn.microsoft.com/library/mt619351.aspx) pour ajouter l’interface réseau à l’aide de la variable d’interface réseau que vous avez défini précédemment.

Exécutez l’applet de commande suivante pour définir l’interface du réseau de votre machine virtuelle.

    $VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id

### <a name="set-the-blob-storage-location-for-the-disk-to-be-used-by-the-virtual-machine"></a>Définir l’emplacement de stockage blob pour le disque devant être utilisé par la machine virtuelle

Ensuite, nous allons définir l’emplacement de stockage blob pour le disque devant être utilisé par la machine virtuelle en utilisant les variables que vous avez défini précédemment.

Exécutez l’applet de commande suivante pour définir l’emplacement de stockage blob.

    $OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"

### <a name="set-the-operating-system-disk-properties-for-the-virtual-machine"></a>Définir les propriétés d’un disque de la machine virtuelle du système d’exploitation

Ensuite, nous allons définir le système d’exploitation propriétés d’un disque de la machine virtuelle. Nous allons utiliser l’applet de commande [Set-AzureRmVMOSDisk](https://msdn.microsoft.com/library/mt603746.aspx) pour spécifier que le système d’exploitation pour la machine virtuelle seront proviennent d’une image, pour définir la mise en cache en lecture seule (car SQL Server est installé sur le même disque) et définissez le nom de la machine virtuelle et le disque système d’exploitation définie en utilisant les variables que nous avons définie précédemment.

Exécutez l’applet de commande suivante pour définir les propriétés d’un disque de votre machine virtuelle du système d’exploitation.

    $VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage

### <a name="specify-the-platform-image-for-the-virtual-machine"></a>Spécifier l’image de la plateforme de la machine virtuelle

Notre dernière étape de configuration consiste à spécifier l’image de la plateforme de notre machine virtuelle. Pour notre didacticiel, nous utilisons la dernière image CTP SQL Server 2016. Nous allons utiliser l’applet de commande [Set-AzureRmVMSourceImage](https://msdn.microsoft.com/library/mt619344.aspx) à utiliser cette image, telle que définie par les variables que vous avez défini précédemment.

Exécutez l’applet de commande suivante pour spécifier l’image de la plateforme de votre machine virtuelle.

    $VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName $PublisherName -Offer $OfferName -Skus $Sku -Version $Version

## <a name="create-the-sql-vm"></a>Créer la machine virtuelle SQL

Maintenant que vous avez terminé les étapes de configuration, vous êtes prêt à créer la machine virtuelle. Nous allons utiliser l’applet de commande [New-AzureRmVM](https://msdn.microsoft.com/library/mt603754.aspx) pour créer la machine virtuelle en utilisant les variables que nous avons définie.

Exécutez l’applet de commande suivante pour créer votre machine virtuelle.

    New-AzureRmVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine

La machine virtuelle est créée. Notez qu’un compte de stockage standard est créé pour les diagnostics de démarrage, car le compte de stockage spécifié de disque de la machine virtuelle est un compte de stockage premium.

Vous pouvez à présent afficher cet ordinateur dans le portail Azure pour afficher [son adresse IP publique et son nom de domaine complet](virtual-machines-windows-portal-sql-server-provision.md#Connect).  

## <a name="example-script"></a>Exemple de script

Le script suivant contient le script PowerShell complet pour ce didacticiel. Il suppose que vous avez déjà le programme d’installation l’abonnement Azure à utiliser avec les commandes **Ajouter AzureRmAccount** et **Sélectionnez AzureRmSubscription** .


    # Variables
    ## Global
    $Location = "SouthCentralUS"
    $ResourceGroupName = "sqlvm1"
    ## Storage
    $StorageName = $ResourceGroupName + "storage"
    $StorageSku = "Premium_LRS"

    ## Network
    $InterfaceName = $ResourceGroupName + "ServerInterface"
    $VNetName = $ResourceGroupName + "VNet"
    $SubnetName = "Default"
    $VNetAddressPrefix = "10.0.0.0/16"
    $VNetSubnetAddressPrefix = "10.0.0.0/24"
    $TCPIPAllocationMethod = "Dynamic"
    $DomainName = "sqlvm1"

    ##Compute
    $VMName = $ResourceGroupName + "VM"
    $ComputerName = $ResourceGroupName + "Server"
    $VMSize = "Standard_DS13"
    $OSDiskName = $VMName + "OSDisk"

    ##Image
    $PublisherName = "MicrosoftSQLServer"
    $OfferName = "SQL2016-WS2016"
    $Sku = "Enterprise"
    $Version = "latest"

    # Resource Group
    New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location

    # Storage
    $StorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageName -SkuName $StorageSku -Kind "Storage" -Location $Location

    # Network
    $SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
    $VNet = New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName -Location $Location -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
    $PublicIp = New-AzureRmPublicIpAddress -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
    $Interface = New-AzureRmNetworkInterface -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id

    # Compute
    $VirtualMachine = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize
    $Credential = Get-Credential -Message "Type the name and password of the local administrator account."
    $VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate #-TimeZone = $TimeZone
    $VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
    $OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
    $VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage

    # Image
    $VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName $PublisherName -Offer $OfferName -Skus $Sku -Version $Version

    ## Create the VM in Azure
    New-AzureRmVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine

## <a name="next-steps"></a>Étapes suivantes
Une fois que la machine virtuelle est créée, vous êtes prêt à vous connecter à la machine virtuelle en utilisant la connectivité RDP et le programme d’installation. Pour plus d’informations, voir [se connecter à un ordinateur de virtuelle SQL Server dans Azure (responsable de ressources)](virtual-machines-windows-sql-connect.md).
