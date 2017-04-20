<properties
    pageTitle="Utiliser le stockage Azure Premium avec SQL Server | Microsoft Azure"
    description="Cet article utilise les ressources créées avec le modèle de déploiement classique et fournit des conseils sur l’utilisation de stockage Premium Azure avec SQL Server Azure machines virtuelles en fonctionnement en cours d’exécution."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="danielsollondon"
    manager="jhubbard"
    editor="monicar"    
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="08/19/2016"
    ms.author="jroth"/>

# <a name="use-azure-premium-storage-with-sql-server-on-virtual-machines"></a>Utiliser le stockage Azure Premium avec SQL Server sur des Machines virtuelles


## <a name="overview"></a>Vue d’ensemble

[Le stockage Azure Premium](../storage/storage-premium-storage.md) est la nouvelle génération de stockage qui fournit une latence faible et haut débit IO. Il convient le mieux pour clés IO intensives charges de travail, telles que SQL Server sur IaaS [Machines virtuelles](https://azure.microsoft.com/services/virtual-machines/).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


Cet article fournit des conseils de planification et de la migration une Machine virtuelle SQL Server à utiliser le stockage Premium en cours d’exécution. Cela inclut Azure infrastructure (mise en réseau, stockage) et les étapes de machine virtuelle Windows invité. L’exemple de l' [appendice](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage) montre une migration de bout en bout complète comment déplacer des machines virtuelles plus grande pour tirer parti de stockage SSD local amélioré avec PowerShell.

Il est important de comprendre le processus de bout en bout d’utilisant le stockage Azure Premium avec SQL Server sur machines virtuelles IAAS. Cela inclut :

- Identification des conditions requises pour utiliser le stockage Premium.
- Exemples de déploiement de SQL Server sur IaaS au stockage Premium pour les nouveaux déploiements.
- Exemples de migration déploiements existants, les déploiements à l’aide de SQL toujours sur les groupes de disponibilité et les serveurs autonomes.
- Approches de migration possibles.
- Exemple de complète de bout en bout illustrant les étapes Azure, Windows et SQL Server pour la migration d’implémentation toujours sur existante.

Pour plus d’informations sur SQL Server dans le Machines virtuelles Azure, voir [SQL Server dans le Machines virtuelles Azure](virtual-machines-windows-sql-server-iaas-overview.md).

**Auteur :** Sol Michel **réviseurs techniques :** Luis Carlos Vargas hareng, Sanjay Mishra, Pravin Mital, Juergen Thomas, Gonzalo Lefaure.

## <a name="prerequisites-for-premium-storage"></a>Conditions préalables pour le stockage Premium

Il existe plusieurs conditions préalables pour l’utilisation du stockage Premium.

### <a name="machine-size"></a>Taille de l’ordinateur

Pour utiliser le stockage Premium, vous devrez utiliser série DS Machines virtuelles (machine virtuelle). Si vous n’avez pas utilisé machines série DS dans votre service de nuage avant, vous devez supprimer la machine virtuelle existante, conserver les disques joints et puis créer un nouveau service cloud avant de recréer la machine virtuelle comme taille de rôle DS *. Pour plus d’informations sur la taille de la Machine virtuelle, voir [Machine virtuelle et les tailles de Service Cloud pour Azure](virtual-machines-linux-sizes.md).

### <a name="cloud-services"></a>Services en nuage

Vous ne pouvez utiliser DS * machines virtuelles avec stockage Premium lorsqu’ils sont créés dans un nouveau service cloud. Si vous utilisez SQL Server toujours dans Azure, toujours sur récepteur sera reporter à l’adresse Azure internes ou externes IP d’équilibrage de charge associé à un service cloud. Cet article explique comment migrer tout en conservant la disponibilité dans ce scénario.

> [AZURE.NOTE] Une série DS * doit être la première machine virtuelle qui est déployée sur le nouveau Service Cloud.

### <a name="regional-vnets"></a>VNETS régionaux

Pour les machines virtuelles DS *, vous devez configurer le réseau (VNET virtuel) qui héberge vos ordinateurs virtuels pour être régionaux. Cela « élargit « la VNET consiste à autoriser les plus grande machines virtuelles à être mis en service dans autre cluster et autoriser les communications entre eux. Dans l’écran suivant, l’emplacement mis en surbrillance indique VNETs régionaux, alors que le premier résultat affiche un VNET « étroite ».

![RegionalVNET][1]

Vous pouvez augmenter un tickets de support technique Microsoft pour migrer vers un VNET régionaux, Microsoft apportez une modification, puis pour effectuer la migration vers VNETs régionaux, modifiez la propriété AffinityGroup dans la configuration du réseau. Tout d’abord exporter la Configuration du réseau dans PowerShell, puis remplacez la propriété **AffinityGroup** dans l’élément **VirtualNetworkSite** avec une propriété **emplacement** . Spécifier `Location = XXXX` où `XXXX` est une région Azure. Puis importer la configuration de nouveau.

Par exemple, compte tenu de la configuration VNET suivante :

    <VirtualNetworkSite name="danAzureSQLnet" AffinityGroup="AzureSQLNetwork">
    <AddressSpace>
      <AddressPrefix>10.0.0.0/8</AddressPrefix>
      <AddressPrefix>172.16.0.0/12</AddressPrefix>
    </AddressSpace>
    <Subnets>
    ...
    </VirtualNetworkSite>

Pour déplacer ce vers un VNET régionaux en Europe ouest, modifiez la configuration à ce qui suit :

    <VirtualNetworkSite name="danAzureSQLnet" Location="West Europe">
    <AddressSpace>
      <AddressPrefix>10.0.0.0/8</AddressPrefix>
      <AddressPrefix>172.16.0.0/12</AddressPrefix>
    </AddressSpace>
    <Subnets>
    ...
    </VirtualNetworkSite>

### <a name="storage-accounts"></a>Comptes de stockage

Vous avez besoin créer un nouveau compte de stockage est configuré pour le stockage Premium. Notez que l’utilisation du stockage Premium est réglée sur le compte de stockage, pas sur les disques durs virtuels individuels, mais lorsque vous utilisez une machine virtuelle DS * série vous pouvez joindre du disque dur virtuel à partir de comptes Premium et stockage Standard. Vous pouvez prendre en compte si vous ne souhaitez pas placer le disque dur virtuel OS une session sur le compte de stockage Premium.

La commande **New-AzureStorageAccountPowerShell** suivante avec le **Type** de « Premium_LRS » crée un compte de stockage Premium :

    $newstorageaccountname = "danpremstor"
    New-AzureStorageAccount -StorageAccountName $newstorageaccountname -Location "West Europe" -Type "Premium_LRS"   

### <a name="vhds-cache-settings"></a>Paramètres du Cache de disques durs virtuels

La différence entre la création des disques qui font partie d’un compte de stockage Premium principale est le paramètre cache du disque. Pour disques du volume de données SQL Server, il est recommandé que vous utilisez «**La mise en cache en lecture**». Pour les volumes de journal des transactions, le paramètre cache du disque doit être défini sur «**Aucun**». Ceci est différent des recommandations pour les comptes de stockage Standard.

Une fois les disques durs virtuels ont été attachées, le paramètre de cache ne peut pas être modifié. Vous devrez détacher et reconnectez le disque dur virtuel avec un facteur de cache mis à jour.

### <a name="windows-storage-spaces"></a>Espaces de stockage Windows

Vous pouvez utiliser les [Espaces de stockage Windows](https://technet.microsoft.com/library/hh831739.aspx) comme vous le faisiez avec stockage Standard précédent, cela vous permettra de migrer un ordinateur virtuel qui est déjà utilisant des espaces de stockage. L’exemple [l’appendice](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage) (étape 9 et transfert) montre le code Powershell pour extraire et importer une machine virtuelle avec plusieurs disques durs virtuels joints.

Pools de stockage ont été utilisés avec un compte de stockage Azure Standard pour améliorer la débit et réduire la latence. Vous pouvez trouver valeur à tester des Pools de stockage avec stockage Premium pour les nouveaux déploiements, mais ils ajoutent complexité supplémentaire avec le programme d’installation de stockage.

#### <a name="how-to-find-which-azure-virtual-disks-map-to-storage-pools"></a>Comment trouver des disques virtuels Azure carte à pools de stockage

Étant donné les recommandations de paramètre cache différent pour joints disques durs virtuels, vous pouvez décider de copier les disques durs virtuels à un compte de stockage Premium. Toutefois, lorsque vous les relier à la nouvelle série de service d’annuaire machine virtuelle, vous devrez peut-être modifier les paramètres du cache. Il est plus simple d’appliquer le stockage Premium recommandé de paramètres de cache lorsque vous avez des disques durs virtuels distincts pour les fichiers de données SQL et les fichiers journaux (plutôt qu’un seul disque dur virtuel qui contient les deux).

> [AZURE.NOTE] Si vous avez des fichiers journaux et de données SQL Server sur le même volume, la mise en cache vous choisirez sur les modèles d’accès IO pour vos charges de travail de base de données. Test uniquement peut montrer quelle option de mise en cache le mieux adapté à ce scénario.

Toutefois, si vous utilisez des espaces de stockage de Windows qui sont composés de plusieurs disques durs virtuels vous devrez examiner vos scripts d’origine afin d’identifier qui joint les disques durs virtuels sont dans quel pool spécifique, donc vous pouvez ensuite définir les paramètres de cache en conséquence pour chaque disque.

Si vous ne disposez pas de script d’origine disponible pour vous indiquer quels disques durs virtuels mapper au pool de stockage, vous pouvez utiliser les étapes suivantes pour déterminer le mappage de pool de stockage/disque.

Pour chaque disque, procédez comme suit :

1. Obtenir la liste des disques associés à la machine virtuelle avec la commande **Get-AzureVM** :

    Get-AzureVM - ServiceName <servicename> -nom <vmname> | Get-AzureDataDisk

1. Notez les Diskname et LUN.

    ![DisknameAndLUN][2]

1. Bureau à distance la machine virtuelle. Atteindre la **Gestion de l’ordinateur** | **Le Gestionnaire de périphériques** | **disques**. Examinez les propriétés de chacun de la « disques virtuels Microsoft »

    ![VirtualDiskProperties][3]

1. Le numéro de LUN ici est une référence au nombre LUN que vous spécifiez lorsque vous connectez le disque dur virtuel de la machine virtuelle.
1. Pour le « disque virtuel Microsoft » accédez à l’onglet **Détails** , puis dans la liste des **Propriétés** , accédez à **Clé pilote**. Dans la **valeur**, notez le **décalage**, c'est-à-dire 0002 dans l’écran suivant. La 0002 indique la PhysicalDisk2 qui fait référence au pool de stockage.

    ![VirtualDiskPropertyDetails][4]

2. Pour chaque pool de stockage, vider les disques associées :

    Get-StoragePool - FriendlyName AMS1pooldata | Get-disque physique

    ![GetStoragePool][5]

Vous pouvez désormais utiliser ces informations à associer associés disques durs virtuels à disques physiques dans des Pools d’espace de stockage.

Une fois que vous avez mappé durs aux disques physiques dans Pools de stockage, vous pouvez détacher et copiez-les sur un compte de stockage Premium, associez-les avec le paramètre cache correcte. Voir l’exemple dans l' [appendice](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage), les étapes 8 à 12. Ces étapes montrent comment extraire une configuration de disque dur joint machine virtuelle dans un fichier CSV, copiez les disques durs virtuels, modifiez les paramètres de cache de configuration de disque et enfin redéployez la machine virtuelle comme une série de service d’annuaire machine virtuelle avec tous les disques joints.

### <a name="vm-storage-bandwidth-and-vhd-storage-throughput"></a>La bande passante de stockage mémoire virtuelle et débit du disque dur virtuel de stockage

Le montant des performances de stockage dépend de la taille du service d’annuaire * VM spécifiée et les tailles de disque dur virtuel. Les ordinateurs virtuels ont allocation différentes pour le nombre de disques durs virtuels qui peut être attaché et la bande passante maximale ils prend en charge (Mo/s). Pour les numéros de bande passante spécifique, voir [Machine virtuelle et les tailles de Service Cloud pour Azure](virtual-machines-linux-sizes.md).

Sorties par accrue sont réalisés avec une plus grande taille de disque. Vous devez prendre en compte lorsque vous élaborez votre méthode de migration. Pour plus d’informations, [reportez-vous à la table pour les Types de disque et sorties par](../storage-premium-storage.md#scalability-and-performance-targets-when-using-premium-storage).

Enfin, vous pouvez envisager que machines virtuelles ont bande passante autre disque maximal qu’ils prend en charge pour tous les disques joints. Élevée, vous pouvez saturation de la bande passante disque maximale disponible pour la taille spécifiée rôle machine virtuelle. Par exemple un Standard_DS14 prend en charge jusqu'à 512 Mo/s ; Par conséquent, avec trois disques P30 vous pouvez saturation de la bande passante disque de la machine virtuelle. Mais dans cet exemple, la limite de débit pourrait être dépassée selon la combinaison de lecture et d’écriture IOs.

## <a name="new-deployments"></a>Nouveaux déploiements

Les deux sections suivantes montrent comment déployer des machines virtuelles SQL Server au stockage Premium. Comme mentionné précédemment, vous n’avez forcément pas besoin de placer le disque du système d’exploitation sur un stockage Premium. Vous pouvez choisir de le faire si vous souhaitez placer les charges de travail IO intensives sur le disque dur virtuel du système d’exploitation.

Le premier exemple illustre l’utilisation d’Images de la galerie Azure existant. Le second exemple montre comment utiliser une image personnalisée machine virtuelle dont vous disposez d’un compte de stockage Standard existant.

> [AZURE.NOTE] Ces exemples part du principe que vous avez déjà créé un VNET régionaux.

### <a name="create-a-new-vm-with-premium-storage-with-gallery-image"></a>Créer une nouvelle machine virtuelle avec stockage Premium avec Image de la galerie

L’exemple ci-dessous montre comment placer le disque dur virtuel du système d’exploitation sur un stockage premium et joindre des disques durs virtuels de stockage Premium. Toutefois, vous pouvez également placer le disque du système d’exploitation dans un compte de stockage Standard, puis joignez durs qui résident dans un compte de stockage Premium. Vous trouverez les deux scénarios.

    $mysubscription = "DansSubscription"
    $location = "West Europe"

    #Set up subscription
    Set-AzureSubscription -SubscriptionName $mysubscription
    Select-AzureSubscription -SubscriptionName $mysubscription -Current  

#### <a name="step-1-create-a-premium-storage-account"></a>Étape 1 : Créer un compte de stockage Premium


    #Create Premium Storage account, note Type
    $newxiostorageaccountname = "danspremsams"
    New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  


#### <a name="step-2-create-a-new-cloud-service"></a>Étape 2 : Créer un nouveau Service Cloud

    $destcloudsvc = "danNewSvcAms"
    New-AzureService $destcloudsvc -Location $location


#### <a name="step-3-reserve-a-cloud-service-vip-optional"></a>Étape 3 : Réserver VIP d’un Service Cloud (facultatif)
    #check exisitng reserved VIP
    Get-AzureReservedIP

    $reservedVIPName = “sqlcloudVIP”
    New-AzureReservedIP –ReservedIPName $reservedVIPName –Label $reservedVIPName –Location $location

#### <a name="step-4-create-a-vm-container"></a>Étape 4 : Créer un conteneur machine virtuelle
    #Generate storage keys for later
    $xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

    ##Generate storage acc contexts
    $xioContext = New-AzureStorageContext –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary   

    #Create container
    $containerName = 'vhds'
    New-AzureStorageContainer -Name $containerName -Context $xioContext

#### <a name="step-5-placing-os-vhd-on-standard-or-premium-storage"></a>Étape 5 : Mise OS dur sur Standard ou stockage Premium
    #NOTE: Set up subscription and default storage account which will be used to place the OS VHD in

    #If you want to place the OS VHD Premium Storage Account
    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount  $newxiostorageaccountname  

    #If you wanted to place the OS VHD Standard Storage Account but attach Premium Storage VHDs then you would run this instead:
    $standardstorageaccountname = "danstdams"

    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount  $standardstorageaccountname

#### <a name="step-6-create-vm"></a>Étape 6 : Créer machine virtuelle
    #Get list of available SQL Server Images from the Azure Image Gallery.
    $galleryImage = Get-AzureVMImage | where-object {$_.ImageName -like "*SQL*2014*Enterprise*"}
    $image = $galleryImage.ImageName

    #Set up Machine Specific Information
    $vmName = "dsDan1"
    $vnet = "dansvnetwesteur"
    $subnet = "SQL"
    $ipaddr = "192.168.0.8"

    #Remember to change to DS series VM
    $newInstanceSize = "Standard_DS1"

    #create new Avaiability Set
    $availabilitySet = "cloudmigAVAMS"

    #Machine User Credentials
    $userName = "myadmin"
    $pass = "mycomplexpwd4*"

    #Create VM Config
    $vmConfigsl = New-AzureVMConfig -Name $vmName -InstanceSize $newInstanceSize -ImageName $image  -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` -AdminUserName $userName -Password $pass | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

    #Add Data and Log Disks to VM Config
    #Note the size specified ‘-DiskSizeInGB 1023’, this will attach 2 x P30 Premium Storage Disk Type
    #Utilising the Premium Storage enabled Storage account

    $vmConfigsl | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 0 -HostCaching "ReadOnly"  -DiskLabel "DataDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-data1.vhd"
    $vmConfigsl | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 1 -HostCaching "None"  -DiskLabel "logDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-log1.vhd"

    #Create VM
    $vmConfigsl  | New-AzureVM –ServiceName $destcloudsvc -VNetName $vnet ## Optional (-ReservedIPName $reservedVIPName)  

    #Add RDP Endpoint
    $EndpointNameRDPInt = "3389"
    Get-AzureVM -ServiceName $destcloudsvc -Name $vmName | Add-AzureEndpoint -Name "EndpointNameRDP" -Protocol "TCP" -PublicPort "53385" -LocalPort $EndpointNameRDPInt  | Update-AzureVM

    #Check VHD storage account, these should be in $newxiostorageaccountname
    Get-AzureVM -ServiceName $destcloudsvc -Name $vmName | Get-AzureDataDisk
    Get-AzureVM -ServiceName $destcloudsvc -Name $vmName |Get-AzureOSDisk


### <a name="create-a-new-vm-to-use-premium-storage-with-a-custom-image"></a>Créer un nouvel ordinateur virtuel pour utiliser le stockage Premium avec une image personnalisée

Ce scénario montre l’endroit où vous avez des images personnalisées existantes qui se trouvent dans un compte de stockage Standard. Comme indiqué si vous voulez placer le disque dur virtuel du système d’exploitation sur le stockage de Premium vous devez copier l’image qui existe dans le compte de stockage Standard et les transférer vers un stockage Premium avant de pouvoir être utilisée. Si vous disposez d’une image local, vous pouvez également utiliser cette méthode pour copier qui directement sur le compte de stockage Premium.

#### <a name="step-1-create-storage-account"></a>Étape 1 : Créer le compte de stockage
    $mysubscription = "DansSubscription"
    $location = "West Europe"

    #Create Premium Storage account
    $newxiostorageaccountname = "danspremsams"
    New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  

    #Standard Storage account
    $origstorageaccountname = "danstdams"

#### <a name="step-2-create-cloud-service"></a>Étape 2 créer Service Cloud
    $destcloudsvc = "danNewSvcAms"
    New-AzureService $destcloudsvc -Location $location


#### <a name="step-3-use-existing-image"></a>Étape 3 : Utiliser une image existante
Vous pouvez utiliser une image existante. Ou bien, vous pouvez [prendre une image d’un ordinateur existant](virtual-machines-windows-classic-capture-image.md). Notez l’ordinateur vous image ne doit pas être DS* machine. Une fois que l’image, les étapes suivantes montrent comment copier dans le compte de stockage Premium avec la * *Démarrer AzureStorageBlobCopy** applet de commande PowerShell.

    #Get storage account keys:
    #Standard Storage account
    $originalstorage =  Get-AzureStorageKey -StorageAccountName $origstorageaccountname
    #Premium Storage account
    $xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

    #Set up contexts for the storage accounts:
    $origContext = New-AzureStorageContext  –StorageAccountName $origstorageaccountname -StorageAccountKey $originalstorage.Primary
    $destContext = New-AzureStorageContext  –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary  

#### <a name="step-4-copy-blob-between-storage-accounts"></a>Étape 4 : Copie Blob entre des comptes de stockage
    #Get Image VHD
    $myImageVHD = "dansoldonorsql2k14-os-2015-04-15.vhd"
    $containerName = 'vhds'

    #Copy Blob between accounts
    $blob = Start-AzureStorageBlobCopy -SrcBlob $myImageVHD -SrcContainer $containerName `
    -DestContainer vhds -Destblob "prem-$myImageVHD" `
    -Context $origContext -DestContext $destContext  

#### <a name="step-5-regularly-check-copy-status"></a>Étape 5 : Vérifier régulièrement copier l’état :
    $blob | Get-AzureStorageBlobCopyState

#### <a name="step-6-add-image-disk-to-azure-disk-repository-in-subscription"></a>Étape 6 : Ajouter le disque d’images sur le disque Azure référentiel abonnement
    $imageMediaLocation = $destContext.BlobEndPoint+"/"+$myImageVHD
    $newimageName = "prem"+"dansoldonorsql2k14"

    Add-AzureVMImage -ImageName $newimageName -MediaLocation $imageMediaLocation

> [AZURE.NOTE] Il est possible que même si les rapports d’état en tant que succès, vous pouvez toujours obtenir une erreur location disque. Dans ce cas, attendez environ 10 minutes.

#### <a name="step-7--build-the-vm"></a>Étape 7 : Créer la machine virtuelle
Vous créez ici la machine virtuelle à partir de votre image et en joignant deux disques durs virtuels stockage Premium :

    $newimageName = "prem"+"dansoldonorsql2k14"
    #Set up Machine Specific Information
    $vmName = "dansolchild"
    $vnet = "westeur"
    $subnet = "Clients"
    $ipaddr = "192.168.0.41"

    #This will need to be a new cloud service
    $destcloudsvc = "danregsvcamsxio2"

    #Use to DS Series VM
    $newInstanceSize = "Standard_DS1"

    #create new Avaiability Set
    $availabilitySet = "cloudmigAVAMS3"

    #Machine User Credentials
    $userName = "myadmin"
    $pass = "theM)stC0mplexP@ssw0rd!”


    #Create VM Config
    $vmConfigsl2 = New-AzureVMConfig -Name $vmName -InstanceSize $newInstanceSize -ImageName $newimageName  -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` -AdminUserName $userName -Password $pass | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

    $vmConfigsl2 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 0 -HostCaching "ReadOnly"  -DiskLabel "DataDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-Datadisk-1.vhd"
    $vmConfigsl2 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 1 -HostCaching "None"  -DiskLabel "LogDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-logdisk-1.vhd"



    $vmConfigsl2 | New-AzureVM –ServiceName $destcloudsvc -VNetName $vnet

## <a name="existing-deployments-that-do-not-use-always-on-availability-groups"></a>Déploiements existants qui n’utilisent pas toujours sur les groupes de disponibilité

> [AZURE.NOTE] Pour les déploiements existants, voir la section [conditions préalables](#prerequisites-for-premium-storage) de cette rubrique.

Il existe différentes considérations pour les déploiements SQL Server qui n’utilisent pas toujours sur les groupes de disponibilité et celles qui. Si vous n’utilisez pas toujours sur et que vous avez un serveur SQL Server autonome existant, vous pouvez mettre à niveau vers le stockage Premium à l’aide d’un nouveau compte de service et de stockage cloud. Tenez compte des options suivantes :

- **Créer une nouvelle machine virtuelle SQL Server**. Vous pouvez créer un nouvel ordinateur du serveur virtuel SQL qui utilise un compte de stockage Premium, comme indiqué dans les déploiements de nouveau. Puis sauvegarder et restaurer vos bases de données utilisateur et de configuration de SQL Server. L’application devra être mis à jour pour faire référence à la nouvelle SQL Server si elle est effectué en interne ou à l’extérieur. Vous devez copier tous les objets 'déconnecter db' comme si vous effectuiez une migration (côte à côte) SQL Server côte à côte. Cela inclut les objets tels que des connexions, les certificats et les serveurs liés.
- **Migrer une machine virtuelle d’existant SQL Server**. Il faut mettre la machine virtuelle de SQL Server hors connexion, puis transférer sur un service en nuage, qui inclut toutes ses durs joints copie vers le compte de stockage Premium. Lorsque la machine virtuelle est en ligne, l’application font référence le nom d’hôte serveur comme avant. N’oubliez pas que la taille du disque existant affecte les caractéristiques de performances. Par exemple, un disque 400 Go est arrondi par excès à un P20. Si vous savez que vous ne nécessitent pas qui les performances du disque, puis vous pouvez recréer la machine virtuelle comme une machine virtuelle série DS et joindre des disques durs virtuels de stockage Premium de la spécification de taille/performance que vous avez besoin. Vous pouvez ensuite détacher et reconnectez les fichiers de base de données SQL.

> [AZURE.NOTE] Lorsque copier les disques dur vous devez tenir compte de la taille, selon la taille signifie que le type de disque de stockage Premium elles répondent aux, ce paramètre détermine spécification de performance de disque. Arrondit Azure jusqu'à la plus proche disque taille importante, afin que si vous avez un disque 400 Go, il sera arrondie à un P20. Selon vos besoins IO existants de ce dernier système d’exploitation, vous devrez pas migration de ces dernières à un compte de stockage Premium.

Si votre serveur SQL Server est accessible en externe, l’adresse VIP service cloud changera. Vous devez également points de terminaison de mise à jour, les utilisateurs et les DNS paramètres.

## <a name="existing-deployments-that-use-always-on-availability-groups"></a>Déploiements existants qui utilisent toujours sur les groupes de disponibilité

> [AZURE.NOTE] Pour les déploiements existants, voir la section [conditions préalables](#prerequisites-for-premium-storage) de cette rubrique.

Initiale dans cette section nous allons étudier comment toujours sur interagit avec Azure mise en réseau. Nous échouera puis migrations dans deux scénarios : migrations où une interruption de service peut être tolérée et migrations où vous devez obtenir très peu de temps.

En local SQL Server toujours sur disponibilité groupes utilisent un récepteur local qui enregistre un nom DNS virtuel ainsi que d’une adresse IP qui est partagée entre un ou plusieurs serveurs SQL. Lorsque les clients se connectent-ils qu’ils sont routés via l’adresse IP écoute sur le serveur SQL principal. Il s’agit du serveur auquel appartient la ressource toujours IP à cet horaire.

![DeploymentsUseAlways sur][6]

Dans Microsoft Azure qu'avoir qu’une seule adresse IP affectée à une carte réseau sur l’ordinateur virtuel, donc afin de parvenir à la même couche d’abstraction comme local, Azure utilise l’adresse IP affectée à l’équilibrage de charge interne/externe (ILB/ELB). La ressource IP qui est partagée entre les serveurs est définie sur la même adresse IP en tant que la ILB/ELB. Ceci est publié dans le système DNS et le trafic client est passé par le biais du ILB/ELB au réplica principal SQL Server. La ILB/ELB sait qui SQL Server est primaire car il utilise sondes pour détecter la ressource toujours IP. Dans l’exemple précédent, il teste chaque nœud qui a un point de terminaison référencée par la ELB/ILB, selon ce qui répond est le principal SQL Server.

> [AZURE.NOTE] La ILB et ELB sont attribuées à un service cloud Azure particulier, par conséquent, toute migration cloud dans Azure probablement signifie que l’adresse IP équilibrage de charge changera.

### <a name="migrating-always-on-deployments-that-can-allow-some-downtime"></a>Migration toujours sur les déploiements acceptant une interruption de service

Il existe deux stratégies pour migrer toujours sur les déploiements permettant de détecter une interruption de service :

1. **Ajouter davantage de réplicas secondaire à un toujours sur Cluster existant**
1. **Migrer vers un nouveau toujours sur Cluster**

#### <a name="1-add-more-secondary-replicas-to-an-existing-always-on-cluster"></a>1. ajouter davantage de réplicas secondaire à un toujours sur Cluster existant

Une stratégie consiste à ajouter au groupe toujours sur disponibilité plus secondaires. Vous devez ajouter ces éléments dans un nouveau service cloud et mettre à jour le récepteur avec la nouvelle période d’équilibrage de charge.

##### <a name="points-of-downtime"></a>Points d’interruption :

- Validation de cluster.
- Test toujours à basculement de nouveaux secondaires.

Si vous utilisez des Pools de stockage Windows au sein de la machine virtuelle débit IO, puis ils seront disponibles hors pendant une Validation Cluster complète. Le test de validation est requis lorsque vous ajoutez des nœuds au cluster. Le temps que nécessaire pour exécuter le test peut varier, afin que vous devez le tester dans votre environnement de test représentatives pour obtenir une durée approximative de la durée de cette opération.

Mettre en service moment où vous pouvez effectuer basculement manuel et chaos test sur les nœuds récemment ajoutés pour garantir toujours sur haute disponibilité fonctionne comme prévu.

![DeploymentUseAlways On2][7]

> [AZURE.NOTE] Vous devez arrêter toutes les instances de SQL Server dans lequel les Pools de stockage sont utilisés avant l’exécution de Validation.
##### <a name="high-level-steps"></a>Principales étapes à suivre

1. Créez deux nouveaux serveurs SQL dans nouveau service cloud avec stockage Premium.
1. Copier les sauvegardes complètes et de restauration avec **NORECOVERY**.
1. Copier les objets dépendants, telles que des connexions etc. via « déconnecter utilisateur DB ».
1. Créer un nouveau interne charge Équilibrage (ILB) ou utiliser un programme d’équilibrage de charge externe (ELB) et puis configurer les points de terminaison équilibrage de charge sur les deux nouveaux nœuds.
> [AZURE.NOTE] Vérifier que tous les nœuds ont la configuration correcte de point de terminaison avant de poursuivre

1. Bloquer l’accès utilisateur/Application à SQL Server (si vous utilisez des Pools de stockage).
1. Arrêtez SQL Server moteur Services sur tous les nœuds (si vous utilisez des Pools de stockage).
1. Ajouter de nouveaux nœuds pour cluster et exécuter une validation complète.
1. Une fois la Validation terminée, démarrez tous les Services de SQL Server.
1. Sauvegarder les journaux des transactions et restaurer des bases de données utilisateur.
1. Ajouter de nouveaux nœuds dans le groupe toujours sur la disponibilité et placez la réplication **synchrone**.
1. Ajoutez la ressource d’adresse IP de la nouvelle Cloud Service ILB/ELB via PowerShell pour le toujours en fonction de l’exemple de plusieurs sites dans l' [appendice](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage). Dans Windows cluster, définissez les **propriétaires possibles** de la ressource **Adresse IP** pour les nouveaux nœuds anciens. Consultez la section « Ajout de ressource d’adresse IP sur le même sous-réseau » de l' [appendice](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage).
1. Basculer vers un des nœuds du nouveau.
1. Vérifiez les nouveaux nœuds basculement automatique basculement partenaires et test.
1. Supprimer des nœuds d’origine de groupe de disponibilité.

##### <a name="advantages"></a>Avantages

- Nouveaux serveurs SQL peut être testé (SQL Server et Application) avant d’être ajoutés sur toujours actif.
- Vous pouvez modifier la taille de la mémoire virtuelle et personnaliser le stockage selon vos besoins. Toutefois, il est utile de conserver toutes les chemins d’accès de fichier SQL même.
- Vous pouvez contrôler quand le transfert des sauvegardes DB aux réplicas secondaire sont démarrés. Cela est différent d’utiliser l’applet de commande Azure **Démarrer AzureStorageBlobCopy** pour copier des disques durs virtuels, car il s’agit d’une copie asynchrone.

##### <a name="disadvantages"></a>Inconvénients liés
- Lorsque vous utilisez des Pools de stockage Windows, il est temps d’arrêt Cluster lors de la Validation de Cluster complète pour les nouveaux nœuds supplémentaires.
- En fonction de la Version de SQL Server et le nombre existant de réplicas secondaires, vous ne serez peut-être pas en mesure d’ajouter davantage de réplicas secondaire sans supprimer secondaires existants.
- Vous pouvez rencontrer SQL données transfert longtemps lors de la configuration secondaires.
- Il est coût supplémentaire lors de la migration alors que vous avez nouvelles machines en cours d’exécution en parallèle.

#### <a name="2-migrate-to-a-new-always-on-cluster"></a>2. migrer vers un nouveau toujours sur Cluster

Une autre stratégie consiste à créer un tout nouveau toujours sur Cluster avec tout nouveau nœuds dans un nouveau service cloud et puis rediriger les clients pour l’utiliser.

##### <a name="points-of-downtime"></a>Points d’arrêt

Il est temps d’arrêt lorsque vous transférez des applications et les utilisateurs vers le récepteur toujours sur Nouveau. Le temps d’arrêt suivant :

- Le temps nécessaire pour restaurer les sauvegardes du journal des transactions final bases de données sur les nouveaux serveurs.
- Le temps nécessaire pour mettre à jour des applications clientes d’utiliser écoute toujours sur Nouveau.

##### <a name="advantages"></a>Avantages

- Vous pouvez tester l’environnement de production réel, SQL Server, et OS générer les modifications.
- Vous avez la possibilité de personnaliser le stockage et potentiellement réduire la taille de la machine virtuelle. Cela peut entraîner une réduction des coûts.
- Vous pouvez mettre à jour votre version de SQL Server ou une version au cours du processus. Vous pouvez également mettre à niveau le système d’exploitation.
- La précédente toujours sur Cluster peuvent agir en tant qu’annulation unie cible.

##### <a name="disadvantages"></a>Inconvénients liés

- Vous devez renommer le DNS que l’auditeur si vous souhaitez que les deux clusters toujours en cours d’exécution simultanément. Cette opération ajoute administration aérienne lors de la migration comme chaînes de l’application client doivent refléter le nouveau nom de récepteur.
- Vous devez implémenter un mécanisme de synchronisation entre les deux environnements pour les garder aussi près que possible de réduire les exigences de synchronisation finale avant la migration.
- Il figure coût lors de la migration alors que vous avez le nouvel environnement d’exécution.

### <a name="migrating-always-on-deployments-for-minimal-downtime"></a>Migration toujours sur hybrides pour très peu de temps

Il existe deux stratégies pour les déploiements toujours sur la migration pour très peu de temps :

1. **Utiliser un secondaire existant : Site unique**
1. **Utiliser les réplicas secondaire existant : plusieurs sites**

#### <a name="1-utilize-an-existing-secondary-single-site"></a>1. utiliser un secondaire existant : Site unique

Une stratégie de temps d’arrêt minimal consiste à prendre un nuage existant secondaire et supprimer du service cloud en cours. Copier les disques durs virtuels vers le nouveau compte de stockage Premium, puis créer la machine virtuelle dans le nouveau service cloud. Mettre à jour le récepteur dans cluster et basculement.

##### <a name="points-of-downtime"></a>Points d’arrêt

- Il est temps d’arrêt lorsque vous mettez à jour le nœud final avec le point de terminaison équilibrage de charge.
- Votre reconnexion du client peut être retardée selon la configuration de votre client/DNS.
- Il est temps d’arrêt supplémentaires si vous choisissez d’utiliser le groupe toujours Cluster en mode hors connexion à remplacer les adresses IP. Vous pouvez éviter cela en utilisant une dépendance OR et des propriétaires possibles pour la ressource adresse IP ajoutée. Consultez la section « Ajout de ressource d’adresse IP sur le même sous-réseau » de l' [appendice](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage).

> [AZURE.NOTE] Lorsque vous souhaitez que le nœud ajouté à partake dans comme un partenaire toujours à basculement, vous devez ajouter un point de terminaison Azure avec une référence à l’ensemble d’équilibrage de charge. Lorsque vous exécutez la commande **Ajouter AzureEndpoint** pour ce faire, les connexions en cours doit rester ouvertes, mais nouvelle connexions au récepteur ne sera pas en mesure d’établir jusqu'à ce que l’équilibrage de charge a mis à jour. Dans ce test a été considéré 90 derniers-120 secondes, cela doit être testé.

##### <a name="advantages"></a>Avantages

- Sans supplémentaire des coûts encourus lors de la migration.
- Une migration individuelle.
- Réduction de la complexité.
- Autorise sorties par une augmentation du Premium stockage références SKU. Lorsque les disques sont détachés de la machine virtuelle et copié dans le nouveau service cloud, un 3e partie outil peut être utilisé pour augmenter la taille du disque dur virtuel qui fournit débit plus élevé. Pour augmenter la taille du disque dur virtuel, consultez ce [forum de discussion](https://social.msdn.microsoft.com/Forums/azure/4a9bcc9e-e5bf-4125-9994-7c154c9b0d52/resizing-azure-data-disk?forum=WAVirtualMachinesforWindows).

##### <a name="disadvantages"></a>Inconvénients liés

- Il existe une perte temporaire de HA et DR lors de la migration.
- Comme il s’agit d’une migration de 1:1, vous devrez utiliser une taille de mémoire virtuelle minimale qui prend en charge le nombre de disques durs virtuels, que vous ne puissiez pas en mesure de versements vos ordinateurs virtuels.
- Ce scénario utiliseriez l’applet de commande Azure **Démarrer AzureStorageBlobCopy** , qui est asynchrone. Il n’existe pas de SLA à la fin de la copie. La durée des copies varie, cela dépend attendre dans la file d’attente qu'elle dépend également la quantité de données à transférer. Le temps de copie augmente si le transfert est destiné à un autre centre de données Azure qui prend en charge le stockage Premium dans une autre région. Si vous avez simplement 2 nœuds, envisagez une atténuation possibles au cas où la copie prend plue en phase de test. Il peut s’agir idées suivantes.
    - Ajoutez un nœud SQL Server 3e temporaire pour HA avant la migration convenu interruption de service.
    - Exécuter la migration en dehors d’Azure maintenance planifiée.
    - Vérifiez que vous avez configuré votre quorum cluster correctement.  

##### <a name="high-level-steps"></a>Principales étapes à suivre

Ce document ne présente pas un exemple de bout en bout complet, alors que l' [appendice](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage) fournit des détails qui peuvent être exploitées pour effectuer ceci.

![MinimalDowntime][8]

- Rassembler configuration du disque et supprimez le nœud (ne supprimez pas durs joints).
- Créer un compte de stockage Premium et copier des disques durs virtuels à partir du compte de stockage Standard
- Créer le nouveau service cloud et redéployez la machine virtuelle SQL2 dans ce service cloud. Créer la machine virtuelle à l’aide du disque dur virtuel d’origine du système d’exploitation copiée et en joignant les disques durs virtuels copiées.
- Configurer ILB / ELB et ajouter des points de terminaison.
- Récepteur de mise à jour par des options :
    - Déconnecter le toujours sur groupe et mise à jour de toujours sur récepteur ILB nouveau / adresse IP ELB.
    - Ou l’ajout de la ressource d’adresse IP de nouveau Cloud Service ILB/ELB via PowerShell dans Windows cluster. Puis définir les propriétaires possibles de la ressource adresse IP vers le nœud a été déplacée, SQL2 et définir cette valeur en tant que dépendance ou dans le nom du réseau. Consultez la section « Ajout de ressource d’adresse IP sur le même sous-réseau » de l' [appendice](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage).
- Vérifier la configuration de DNS/propagation aux clients.
- Migrer SQL1 machine virtuelle, puis accédez aux étapes 2 à 4.
- Si vous utilisez les étapes 5ii, puis ajouter SQL1 en tant que propriétaire Possible la ressource adresse IP ajoutée
- Tester le basculement.

#### <a name="2-utilize-existing-secondary-replicas-multi-site"></a>2. utiliser réplicas secondaire existant : plusieurs sites

Si vous avez des nœuds dans le centre de données Azure plusieurs (DC) ou si vous disposez d’un environnement hybride, vous pouvez utiliser une configuration toujours dans cet environnement pour réduire les temps d’arrêt.

L’approche consiste à changer la synchronisation toujours en synchrone pour les locaux ou DC Azure secondaire, puis basculement sur vers que SQL Server. Copier les disques durs virtuels à un compte de stockage Premium, puis redéployez l’ordinateur dans un nouveau service cloud. Mettre à jour le récepteur, puis puis restauré.

##### <a name="points-of-downtime"></a>Points d’arrêt

Le temps d’arrêt se compose de l’heure à basculement pour le remplacement DC et inversement. Il dépend également de la configuration de votre client/DNS et votre reconnexion du client peut être retardée.
Prenons l’exemple suivant d’une configuration hybride toujours sur :

![MultiSite1][9]

##### <a name="advantages"></a>Avantages

- Vous pouvez utiliser l’infrastructure existante.
- Vous avez la possibilité de pré-mise à niveau le stockage sur le contrôleur de domaine DR Azure Azure tout d’abord.
- Le stockage Azure DR DC peut être reconfiguré.
- Il existe un minimum de deux basculement lors de la migration, à l’exclusion de basculement test.
- Vous n’avez pas besoin de déplacer des données SQL Server avec la sauvegarde et de restauration.

##### <a name="disadvantages"></a>Inconvénients liés

- En fonction de l’accès client à SQL Server, il peut être latence accrue lorsque SQL Server est en cours d’exécution dans un autre contrôleur de domaine à l’application.
- La durée de copie des disques durs virtuels au stockage Premium peut être long. Cela peut affecter votre décision si vous souhaitez conserver le nœud dans le groupe de disponibilité. Point à considérer pour lorsque travail intensive journal charges en cours d’exécution lors de la migration est requise, car le nœud principal obligé de conserver les transactions non répliquées dans son journal des transactions. Par conséquent, cela peut augmenter considérablement la taille.
- Ce scénario utiliseriez l’applet de commande Azure **Démarrer AzureStorageBlobCopy** , qui est asynchrone. Il n’y a pas de SLA sur saisie semi-automatique. La durée des copies varie, cela dépend attendre dans la file d’attente, elle dépend également la quantité de données à transférer. Par conséquent, vous avez simplement un nœud dans votre centre de données 2ème, vous devez prendre des mesures d’atténuation au cas où la copie prend plue en phase de test. Il peut s’agir idées suivantes.
    - Ajoutez un nœud SQL 2ème temporaire pour HA avant la migration convenu interruption de service.
    - Exécuter la migration en dehors d’Azure maintenance planifiée.
    - Vérifiez que vous avez configuré votre quorum cluster correctement.

Ce scénario part du principe que vous avez présentées lors de l’installation et savez comment le stockage est mappé pour apporter des modifications pour les paramètres de cache de disque optimales.

##### <a name="high-level-steps"></a>Principales étapes à suivre
![Multisite2][10]

- Rendre les locaux / alterne DC Azure SQL Server principale et rendent les autres automatique basculement partenaire (AFP).
- Collectez les informations de configuration de disque SQL2 et supprimez le nœud (ne supprimez pas durs joints).
- Créer un compte de stockage Premium et copiez des disques durs virtuels à partir du compte de stockage Standard.
- Créer un nouveau service cloud et créez la machine virtuelle SQL2 avec ses disques prime stockage joints.
- Configurer ILB / ELB et ajouter des points de terminaison.
- Mettre à jour le toujours sur récepteur avec ILB nouveau / ELB IP adresse et testez le basculement.
- Vérifiez la configuration de DNS.
- Modifier la AFP à SQL2, puis migrer SQL1 et accédez aux étapes 2 à 5.
- Tester le basculement.
- Activez la AFP SQL1 et SQL2

## <a name="appendix-migrating-a-multisite-always-on-cluster-to-premium-storage"></a>Appendice : Migration un Multisite toujours sur Cluster au stockage Premium

Le reste de cette rubrique fournit un exemple détaillé de la conversion d’un cluster de toujours sur plusieurs sites au stockage Premium. Il convertit également que l’auditeur d’à l’aide d’un programme d’équilibrage de charge externe (ELB) à un équilibrage de charge interne (ILB).

### <a name="environment"></a>Environnement

- Windows 2k 12 / SQL 2k 12
- Fichiers de base de données 1 sur SP
- 2 x Pools de stockage par nœud

![Appendix1][11]

### <a name="vm"></a>MACHINE VIRTUELLE :

Dans cet exemple, nous allons vous montrer déplacement d’un ELB à ILB. ELB était disponible avant ILB, voici comment changer cela lors de la migration.

![Appendix2][12]

### <a name="pre-steps-connect-to-subscription"></a>Étapes de pré : Se connecter à l’abonnement

    Add-AzureAccount

    #Set up subscription
    Get-AzureSubscription

#### <a name="step-1-create-new-storage-account-and-cloud-service"></a>Étape 1 : Créer le nouveau compte de stockage et le Service en nuage
    $mysubscription = "DansSubscription"
    $location = "West Europe"

    #Storage accounts
    #current storage account where the vm to migrate resides
    $origstorageaccountname = "danstdams"

    #Create Premium Storage account
    $newxiostorageaccountname = "danspremsams"
    New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  

    #Generate storage keys for later
    $originalstorage =  Get-AzureStorageKey -StorageAccountName $origstorageaccountname
    $xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

    #Generate storage acc contexts
    $origContext = New-AzureStorageContext  –StorageAccountName $origstorageaccountname -StorageAccountKey $originalstorage.Primary
    $xioContext = New-AzureStorageContext  –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary  

    #Set up subscription and default storage account
    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $origstorageaccountname
    Select-AzureSubscription -SubscriptionName $mysubscription -Current

    #CREATE NEW CLOUD SVC
    $vnet = "dansvnetwesteur"

    ##Existing cloud service
    $sourceSvc="dansolSrcAms"

    ##Create new cloud service
    $destcloudsvc = "danNewSvcAms"
    New-AzureService $destcloudsvc -Location $location

#### <a name="step-2-increase-the-permitted-failures-on-resources-optional"></a>Étape 2 : Augmenter les échecs autorisées sur les ressources<Optional>
Dans certaines ressources qui appartiennent à votre groupe toujours sur disponibilité existe limites sur le nombre d’erreurs pouvant se produire dans une période, où le service de cluster tentera de redémarrer le groupe de ressources. Il est recommandé de que vous augmentez tandis que vous parcourez cette procédure, depuis si vous n’est pas manuellement basculement basculement et le déclencheur en fermant machines que pouvez-vous près de cette limite.

Il est prudent doubler l’allocation de panne, pour effectuer cette opération dans le Gestionnaire de Cluster de basculement, accédez aux propriétés du groupe de ressources toujours sur :

![Appendix3][13]

Modifier le nombre maximal d’échecs à 6.

#### <a name="step-3-addition-ip-address-resource-for-cluster-group-optional"></a>Étape 3 : Adresse IP Addition ressource de Cluster groupe<Optional>

Si vous avez une seule adresse IP pour le groupe Cluster et il est aligné sur le sous-réseau cloud, veillez à ce, si vous prenez accidentellement en mode hors connexion tous les nœuds dans le cloud sur ce réseau puis la ressource adresse IP du Cluster et nom de réseau Cluster ne sera pas mise en ligne. En cas de ce qu’il bloquera mises à jour vers d’autres ressources de cluster.

#### <a name="step-4-dns-configuration"></a>Étape 4 : Configuration de DNS

Pour mettre en œuvre une lisse transition dépend comment DNS est utilisée et mises à jour.
Lorsque toujours sur est installé, il crée un groupe de ressources de Cluster de Windows, si vous ouvrez le Gestionnaire de Cluster de basculement, vous verrez qu’au minimum celui-ci possède trois ressources, les deux le document fait référence à :

- Nom du réseau virtuel (VNN) – il s’agit du nom DNS que le client se connecter à lorsque vous souhaitez vous connecter aux serveurs SQL via le toujours.
- Ressource d’adresse IP – il s’agit de l’adresse IP associé à la VNN, vous pouvez avoir plusieurs, et dans une configuration multisite vous dispose d’une adresse IP par site/sous-réseau.

Lors de la connexion à SQL Server, le Client SQL Server pilote est récupérer les enregistrements DNS associés aux que l’auditeur et essayez de vous connecter à chaque toujours sur associé adresse IP, ci-dessous abordent dépend de plusieurs facteurs peut influencer ceci.

Le nombre d’enregistrements DNS simultanées qui sont associés au nom du récepteur dépend non seulement le nombre d’adresses IP associé, mais le « RegisterAllIpProviders'setting dans basculement de la ressource toujours ON VNN.

Lorsque vous déployez toujours sur dans Azure il existe différentes étapes pour créer le récepteur et adresses IP, vous devez configurer manuellement le 'RegisterAllIpProviders' 1, il s’agit différent pour un déploiement toujours sur site dont il est déjà défini sur 1.

Si 'RegisterAllIpProviders' est égal à 0, puis vous verrez uniquement un enregistrement DNS dans DNS associé à l’écoute :

![Appendix4][14]

Si 'RegisterAllIpProviders' est 1 :

![Appendix5][15]

Le code ci-dessous vider les paramètres VNN et le configurer pour vous, veuillez noter, pour que la modification prenne effet, vous devez déconnecter le VNN et mettez-le en ligne, cette prise le récepteur en mode hors connexion à l’origine d’interruption de connectivité client.

    ##Always On Listener Name
    $ListenerName = "Mylistener"
    ##Get AlwaysOn Network Name Settings
    Get-ClusterResource $ListenerName| Get-ClusterParameter
    ##Set RegisterAllProvidersIP
    Get-ClusterResource $ListenerName| Set-ClusterParameter RegisterAllProvidersIP  1

Dans une étape de migration ultérieure, vous devez mettre à jour le récepteur toujours sur avec une adresse IP mis à jour qui font référence à un équilibrage de charge, qui comprend un la suppression de ressources adresse IP et ajout. Après la mise à jour IP, vous devez vous assurer la nouvelle adresse IP a été mis à jour dans la Zone DNS et que les clients sont à jour leur cache DNS local.

Si vos clients résident dans un segment de réseau et faire référence à un autre serveur DNS, vous devez prendre en considération ce qui arrive à propos du transfert de Zone DNS lors de la migration, comme l’application vous reconnecter temps sera limité par au moins l’heure Zone transfert de toutes les nouvelles adresses IP que l’auditeur. Si vous êtes sous contrainte heure ici, vous devez discuter et tester forcer un transfert de zone incrémentiel avec les équipes de Windows, et également placer l’enregistrement d’hôte DNS à un inférieur Time To Live (TTL), les clients met à jour. Pour plus d’informations, voir [Transferts de Zone incrémentiels](https://technet.microsoft.com/library/cc958973.aspx) et [Démarrer DnsServerZoneTransfer](https://technet.microsoft.com/library/jj649917.aspx).

Par défaut la valeur TTL enregistrement DNS associé à l’écoute toujours sur dans Azure est 1200 secondes. Vous souhaiterez peut-être réduire le nombre si vous êtes sous temps de contrainte pendant la migration afin des clients mettre à jour leurs DNS avec l’adresse IP mis à jour que l’auditeur. Vous pouvez voir et modifier la configuration en vidant la configuration de la VNN :

    $AGName = "myProductionAG"
    $ListenerName = "Mylistener"
    #Look at HostRecordTTL
    Get-ClusterResource $ListenerName| Get-ClusterParameter

    #Set HostRecordTTL Examples
    Get-ClusterResource $ListenerName| Set-ClusterParameter -Name "HostRecordTTL" 120

Veuillez noter, faible la « HostRecordTTL », un montant plus élevé du trafic DNS se produit.

##### <a name="client-application-settings"></a>Paramètres des applications clientes

Si votre application cliente SQL prend en charge le .net 4.5 SQLClient, puis vous pouvez utiliser « MULTISUBNETFAILOVER = TRUE' mot clé, il est recommandé pour être appliqués car elle permet de connexion plus rapide SQL toujours dans le groupe de disponibilité pendant le basculement. Il énumère toutes les adresses IP associées à l’écoute toujours en parallèle et effectue une vitesse de nouvelle tentative de connexion TCP plus agressif lors d’un basculement.

Pour plus d’informations sur les paramètres ci-dessus, voir [MultiSubnetFailover mot-clé et fonctionnalités associées](https://msdn.microsoft.com/library/hh213080.aspx#MultiSubnetFailover). Voir également [Prise en charge SqlClient de disponibilité, sinistre](https://msdn.microsoft.com/library/hh205662(v=vs.110).aspx).

#### <a name="step-5-cluster-quorum-settings"></a>Étape 5 : Paramètres de quorum Cluster

Lorsque vous vous apprêtez à être tenu au moins un SQL Server vers le bas à la fois, vous devez modifier le paramètre de quorum cluster, si vous utilisez le fichier partage témoin avec 2 nœuds, vous devez définir le quorum autoriser majorité nœud et d’utiliser vote dynamique et il s’agit afin de permettre un seul nœud reste permanent.


    Set-ClusterQuorum -NodeMajority  

Pour plus d’informations sur la gestion et la configuration du quorum du cluster, voir [configurer et gérer le Quorum dans un Cluster de basculement Windows Server 2012](https://technet.microsoft.com/library/jj612870.aspx).

#### <a name="step-6-extract-existing-endpoints-and-acls"></a>Étape 6 : Extraire les utilisateurs et les points de terminaison existants
    #GET Endpoint info
    Get-AzureVM -ServiceName $destcloudsvc -Name $vmNameToMigrate | Get-AzureEndpoint
    #GET ACL Rules for Each EP, this example is for the Always On Endpoint
    Get-AzureVM -ServiceName $destcloudsvc -Name $vmNameToMigrate | Get-AzureAclConfig -EndpointName "myAOEndPoint-LB"  

Enregistrer les éléments suivants vers un fichier texte.

#### <a name="step-7-change-failover-partners-and-replication-modes"></a>Étape 7 : Modifier basculement partenaires et des Modes de réplication

Si vous avez plus de 2 serveurs SQL Server, vous devez modifier le basculement d’une autre secondaire dans un autre CD ou local à « Synchrone » et rendez-le un partenaire de basculement automatique (AFP), il s’agit donc mettre à jour HA tandis que vous apportez des modifications. Vous pouvez le faire par le biais TSQL de modifier les SSMS bien que :

![Appendix6][16]

#### <a name="step-8-remove-secondary-vm-from-cloud-service"></a>Étape 8 : Supprimer les machine virtuelle secondaire d’un service cloud

Vous devez être envisagez de migrer un nœud secondaire cloud tout d’abord, s’il s’agit actuellement principal, vous devez lancer un basculement manuel.

    $vmNameToMigrate="dansqlams2"

    #Check machine status
    Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate

    #Shutdown secondary VM
    Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | stop-AzureVM


    #Extract disk configuration

    ##Building Existing Data Disk Configuration
    $file = "C:\Azure Storage Testing\mydiskconfig_$vmNameToMigrate.csv"
    $datadisks = @(Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureDataDisk )
    Add-Content $file “lun, vhdname, hostcaching, disklabel, diskName”
    foreach ($disk in $datadisks)
    {
      $vhdname = $disk.MediaLink.AbsolutePath -creplace  "/vhds/"
      $disk.Lun, , $disk.HostCaching, $vhdname, $disk.DiskLabel,$disks.DiskName
    # Write-Host "copying disk $disk"
    $adddisk = "{0},{1},{2},{3},{4}" -f $disk.Lun,$vhdname, $disk.HostCaching, $disk.DiskLabel, $disk.DiskName
    $adddisk | add-content -path $file
    }

    #Get OS Disk
    $osdisks = Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureOSDisk ## | select -ExpandProperty MediaLink
    $osvhdname = $osdisks.MediaLink.AbsolutePath -creplace  "/vhds/"
    $osdisks.OS, $osdisks.HostCaching, $osvhdname, $osdisks.DiskLabel, $osdisks.DiskName
    $addosdisk = "{0},{1},{2},{3},{4}" -f $osdisks.OS,$osvhdname, $osdisks.HostCaching, $osdisks.Disklabel , $osdisks.DiskName
    $addosdisk | add-content -path $file

    #Import disk config
    $diskobjects  = Import-CSV $file

    #Check disk config, make sure below returns the disks associated with the VM
    $diskobjects

    #Identify OS Disk
    $osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
    $osdiskforbuild = $osdiskimport.diskName

    #Check machibe is off
    Get-AzureVM -ServiceName $sourceSvc -Name  $vmNameToMigrate

    #Drop machine and rebuild to new cls
    Remove-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate

#### <a name="step-9-change-disk-caching-settings-in-csv-file-and-save"></a>Étape 9 : Modifier la mise en cache des paramètres dans un fichier CSV et enregistrer

Pour les volumes de données suivants doivent être définis sur en lecture seule.

Pour les volumes de journal des transactions ces doivent être définies sur aucun.

![Appendix7][17]

#### <a name="step-10-copy-vhds"></a>Étape 10 : Copie disques durs virtuels
    #Ensure you have created the container for these:
    $containerName = 'vhds'

    #Create container
    New-AzureStorageContainer -Name $containerName -Context $xioContext

    ####DISK COPYING####
    #Get disks from csv, get settings for each VHDs and copy to Premium Storage accoun
    ForEach ($disk in $diskobjects)
       {
       $lun = $disk.Lun
       $vhdname = $disk.vhdname
       $cacheoption = $disk.HostCaching
       $disklabel = $disk.DiskLabel
       $diskName = $disk.DiskName
       Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname has cache setting : $cacheoption"

       #Start async copy
       Start-AzureStorageBlobCopy -srcUri "https://$origstorageaccountname.blob.core.windows.net/vhds/$vhdname" `
    -SrcContext $origContext `
    -DestContainer $containerName `
    -DestBlob $vhdname `
    -DestContext $xioContext
       }



Vous pouvez vérifier l’état de la copie des disques durs virtuels sur le compte de stockage Premium :

    ForEach ($disk in $diskobjects)
       {
       $lun = $disk.Lun
       $vhdname = $disk.vhdname
       $cacheoption = $disk.HostCaching
       $disklabel = $disk.DiskLabel
       $diskName = $disk.DiskName

       $copystate = Get-AzureStorageBlobCopyState -Blob $vhdname -Container $containerName -Context $xioContext
    Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname, STATUS = " $copystate.Status
       }

![Appendix8][18]

Attendez que tous ces sont validées avec succès.

Pour plus d’informations pour les objets BLOB individuels :

    Get-AzureStorageBlobCopyState -Blob "blobname.vhd" -Container $containerName -Context $xioContext

#### <a name="step-11-register-os-disk"></a>Étape 11 : Disque de Registre du système d’exploitation

    #Change storage account
    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $newxiostorageaccountname
    Select-AzureSubscription -SubscriptionName $mysubscription -Current

    #Register OS disk
    $osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
    $osvhd = $osdiskimport.vhdname
    $osdiskforbuild = $osdiskimport.diskName

    #Registering OS disk, but as XIO disk
    $xioDiskName = $osdiskforbuild + "xio"
    Add-AzureDisk -DiskName $xioDiskName -MediaLocation  "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$osvhd"  -Label "BootDisk" -OS "Windows"

#### <a name="step-12-import-secondary-into-new-cloud-service"></a>Étape 12 : Importer secondaire dans le nouveau service en nuage

Le code ci-dessous utilise également l’ajout de l’option ici vous pouvez importer l’ordinateur et utiliser l’adresse IP virtuelle conservables.

    #Build VM Config
    $ipaddr = "192.168.0.5"
    #Remember to change to XIO
    $newInstanceSize = "Standard_DS13"
    $subnet = "SQL"

    #Create new Avaiability Set
    $availabilitySet = "cloudmigAVAMS"

    #build machine config into object
    $vmConfig = New-AzureVMConfig -Name $vmNameToMigrate -InstanceSize $newInstanceSize -DiskName $xioDiskName -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

    #Reload disk config
    $diskobjects  = Import-CSV $file
    $datadiskimport = $diskobjects | where {$_.lun -ne "Windows"}

    ForEach ( $attachdatadisk in $datadiskimport)
       {
    $label = $attachdatadisk.disklabel
    $lunNo = $attachdatadisk.lun
    $hostcach = $attachdatadisk.hostcaching
    $datadiskforbuild = $attachdatadisk.diskName
    $vhdname = $attachdatadisk.vhdname

    ###Attaching disks to a VM during a deploy to a new cloud service and new storage account is different from just attaching VHDs to just a redeploy in a new cloud service
    $vmConfig | Add-AzureDataDisk -ImportFrom -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vhdname" -LUN $lunNo -HostCaching $hostcach -DiskLabel $label

    }

    #Create VM
    $vmConfig  | New-AzureVM –ServiceName $destcloudsvc –Location $location -VNetName $vnet ## Optional (-ReservedIPName $reservedVIPName)

#### <a name="step-13-create-ilb-on-new-cloud-svc-add-load-balanced-endpoints-and-acls"></a>Étape 13 : Créer des ILB sur Nouveau Cloud Svc, ajouter charge équilibrée des points de terminaison et les utilisateurs
    #Check for existing ILB
    GET-AzureInternalLoadBalancer -ServiceName $destcloudsvc

    $ilb="sqlIntIlbDest"
    $subnet = "SQL"
    $IP="192.168.0.25"
    Add-AzureInternalLoadBalancer -ServiceName $destcloudsvc -InternalLoadBalancerName $ilb –SubnetName $subnet –StaticVNetIPAddress $IP

    #Endpoints
    $epname="sqlIntEP"
    $prot="tcp"
    $locport=1433
    $pubport=1433
    Get-AzureVM –ServiceName $destcloudsvc –Name $vmNameToMigrate  | Add-AzureEndpoint -Name $epname -Protocol $prot -LocalPort $locport -PublicPort $pubport -ProbePort 59999 -ProbeIntervalInSeconds 5 -ProbeTimeoutInSeconds 11  -ProbeProtocol "TCP" -InternalLoadBalancerName $ilb -LBSetName $ilb -DirectServerReturn $true | Update-AzureVM

    #SET Azure ACLs or Network Security Groups & Windows FWs

    #http://msdn.microsoft.com/library/azure/dn495192.aspx

    ####WAIT FOR FULL AlwaysOn RESYNCRONISATION!!!!!!!!!#####

####<a name="step-14-update-always-on"></a>Étape 14 : Mettre à jour toujours sur
    #Code to be executed on a Cluster Node
    $ClusterNetworkNameAmsterdam = "Cluster Network 2" # the azure cluster subnet network name
    $newCloudServiceIPAmsterdam = "192.168.0.25" # IP address of your cloud service

    $AGName = "myProductionAG"
    $ListenerName = "Mylistener"


    Add-ClusterResource "IP Address $newCloudServiceIPAmsterdam" -ResourceType "IP Address" -Group $AGName -ErrorAction Stop |  Set-ClusterParameter -Multiple @{"Address"="$newCloudServiceIPAmsterdam";"ProbePort"="59999";SubnetMask="255.255.255.255";"Network"=$ClusterNetworkNameAmsterdam;"OverrideAddressMatch"=1;"EnableDhcp"=0} -ErrorAction Stop

    #set dependancy and NETBIOS, then remove old IP address

    #set NETBIOS, then remove old IP address
    Get-ClusterGroup $AGName | Get-ClusterResource -Name "IP Address $newCloudServiceIPAmsterdam" | Set-ClusterParameter -Name EnableNetBIOS -Value 0

    #set dependency to Listener (OR Dependency) and delete previous IP Address resource that references:

    #Make sure no static records in DNS

![Appendix9][19]

Supprimer l’ancien service cloud adresse IP.

![Appendix10][20]

#### <a name="step-15-dns-update-check"></a>Étape 15 : Mise à jour DNS vérification

Vous devez maintenant informé des serveurs DNS vos réseaux de client SQL Server et vérifiez que le cluster a ajouté l’enregistrement hôte supplémentaire pour l’adresse IP ajoutée. Si ces serveurs DNS n’ont pas été mises à jour, vous pouvez forcer un transfert de Zone DNS et vous assurer que les clients d’il sous-réseau sont en mesure de résoudre les deux adresses toujours sur IP, il s’agit donc vous n’avez pas besoin d’attendre la réplication DNS automatique.

#### <a name="step-16-reconfigure-always-on"></a>Étape 16 : Reconfigurer toujours

À ce stade vous attendez secondaire ce nœud qui ont été migré pour entièrement resynchroniser avec le nœud locales et basculer vers le nœud de réplication synchrone et le rendre la AFP.  

#### <a name="step-17-migrate-second-node"></a>Étape 17 : Migrer deuxième nœud
    $vmNameToMigrate="dansqlams1"

    Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate

    #Get endpoint information
    $endpoint = Get-AzureVM -ServiceName $sourceSvc  -Name $vmNameToMigrate | Get-AzureEndpoint

    #Shutdown VM
    Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | stop-AzureVM

    #Get disk config

    #Building Existing Data Disk Configuration
    $file = "C:\Azure Storage Testing\mydiskconfig_$vmNameToMigrate.csv"
    $datadisks = @(Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureDataDisk )
    Add-Content $file “lun, vhdname, hostcaching, disklabel, diskName”
    foreach ($disk in $datadisks)
    {
      $vhdname = $disk.MediaLink.AbsolutePath -creplace  "/vhds/"
      $disk.Lun, , $disk.HostCaching, $vhdname, $disk.DiskLabel,$disks.DiskName
    # Write-Host "copying disk $disk"
    $adddisk = "{0},{1},{2},{3},{4}" -f $disk.Lun,$vhdname, $disk.HostCaching, $disk.DiskLabel, $disk.DiskName
    $adddisk | add-content -path $file
    }

    #Get OS Disk
    $osdisks = Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureOSDisk ## | select -ExpandProperty MediaLink
    $osvhdname = $osdisks.MediaLink.AbsolutePath -creplace  "/vhds/"
    $osdisks.OS, $osdisks.HostCaching, $osvhdname, $osdisks.DiskLabel, $osdisks.DiskName
    $addosdisk = "{0},{1},{2},{3},{4}" -f $osdisks.OS,$osvhdname, $osdisks.HostCaching, $osdisks.Disklabel , $osdisks.DiskName
    $addosdisk | add-content -path $file

    #Import disk config
    $diskobjects  = Import-CSV $file

    #Check disk configuration
    $diskobjects

    #Identify OS Disk
    $osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
    $osdiskforbuild = $osdiskimport.diskName

    #Check machine is off
    Get-AzureVM -ServiceName $sourceSvc -Name  $vmNameToMigrate

    #Drop machine and rebuild to new cls
    Remove-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate

#### <a name="step-18-change-disk-caching-settings-in-csv-file-and-save"></a>Étape 18 : Modifier la mise en cache des paramètres dans un fichier CSV et enregistrer

Pour les volumes de données suivants doivent être définis sur en lecture seule.

Pour les volumes de journal des transactions ces doivent être définies sur aucun.

![Appendix11][21]

#### <a name="step-19-create-new-independent-storage-account-for-secondary-node"></a>Étape 19 : Créer le compte de stockage indépendante pour nœud secondaire
    $newxiostorageaccountnamenode2 = "danspremsams2"
    New-AzureStorageAccount -StorageAccountName $newxiostorageaccountnamenode2 -Location $location -Type "Premium_LRS"  

    #Reset the storage account src if node 1 in a different storage account
    $origstorageaccountname2nd = "danstdams2"

    #Generate storage keys for later
    $xiostoragenode2 = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountnamenode2

    #Generate storage acc contexts
    $xioContextnode2 = New-AzureStorageContext  –StorageAccountName $newxiostorageaccountnamenode2 -StorageAccountKey $xiostoragenode2.Primary  

    #Set up subscription and default storage account
    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $newxiostorageaccountnamenode2
    Select-AzureSubscription -SubscriptionName $mysubscription -Current

#### <a name="step-20-copy-vhds"></a>Étape 20 : Copie disques durs virtuels
    #Ensure you have created the container for these:
    $containerName = 'vhds'

    #Create container
    New-AzureStorageContainer -Name $containerName -Context $xioContextnode2  

    ####DISK COPYING####
    ##get disks from csv, get settings for each VHDs and copy to Premium Storage accoun
    ForEach ($disk in $diskobjects)
       {
       $lun = $disk.Lun
       $vhdname = $disk.vhdname
       $cacheoption = $disk.HostCaching
       $disklabel = $disk.DiskLabel
       $diskName = $disk.DiskName
       Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname has cache setting : $cacheoption"

       #Start async copy
       Start-AzureStorageBlobCopy -srcUri "https://$origstorageaccountname2nd.blob.core.windows.net/vhds/$vhdname" `
        -SrcContext $origContext `
        -DestContainer $containerName `
        -DestBlob $vhdname `
        -DestContext $xioContextnode2
       }

    #Check for copy progress

    #check induvidual blob status
    Get-AzureStorageBlobCopyState -Blob "danRegSvcAms-dansqlams1-2014-07-03.vhd" -Container $containerName -Context $xioContext


Vous pouvez vérifier l’état de copie de disque dur virtuel pour tous les disques durs virtuels : ForEach ($disk dans $diskobjects) {$lun = $disk. LUN $vhdname = $disk.vhdname $cacheoption = $disk. HostCaching $disklabel = $disk. Étiquette disquette $diskName = $disk. DiskName

       $copystate = Get-AzureStorageBlobCopyState -Blob $vhdname -Container $containerName -Context $xioContextnode2
    Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname, STATUS = " $copystate.Status
       }

![Appendix12][22]

Attendez que tous ces sont validées avec succès.

Pour plus d’informations pour les objets BLOB individuels : état de blob induvidual #Check Get-AzureStorageBlobCopyState-Blob « danRegSvcAms-dansqlams1-2014-07-03.vhd »-conteneur $containerName-contexte $xioContextnode2

#### <a name="step-21-register-os-disk"></a>Étape 21 : Disque de Registre du système d’exploitation
    #change storage account to the new XIO storage account
    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $newxiostorageaccountnamenode2
    Select-AzureSubscription -SubscriptionName $mysubscription -Current

    #Register OS disk
    $osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
    $osvhd = $osdiskimport.vhdname
    $osdiskforbuild = $osdiskimport.diskName

    #Registering OS disk, but as XIO disk
    $xioDiskName = $osdiskforbuild + "xio"
    Add-AzureDisk -DiskName $xioDiskName -MediaLocation  "https://$newxiostorageaccountnamenode2.blob.core.windows.net/vhds/$osvhd"  -Label "BootDisk" -OS "Windows"

    #Build VM Config
    $ipaddr = "192.168.0.4"
    $newInstanceSize = "Standard_DS13"

    #Join to existing Avaiability Set

    #Build machine config into object
    $vmConfig = New-AzureVMConfig -Name $vmNameToMigrate -InstanceSize $newInstanceSize -DiskName $xioDiskName -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

    #Reload disk config
    $diskobjects  = Import-CSV $file
    $datadiskimport = $diskobjects | where {$_.lun -ne "Windows"}

    ForEach ( $attachdatadisk in $datadiskimport)
       {
    $label = $attachdatadisk.disklabel
    $lunNo = $attachdatadisk.lun
    $hostcach = $attachdatadisk.hostcaching
    $datadiskforbuild = $attachdatadisk.diskName
    $vhdname = $attachdatadisk.vhdname

    ###This is different to just a straight cloud service change
    #note if you do not have a disk label the command below will fail, populate as required.
    $vmConfig | Add-AzureDataDisk -ImportFrom -MediaLocation "https://$newxiostorageaccountnamenode2.blob.core.windows.net/vhds/$vhdname" -LUN $lunNo -HostCaching $hostcach -DiskLabel $label

    }

    #Create VM
    $vmConfig  | New-AzureVM –ServiceName $destcloudsvc –Location $location -VNetName $vnet -Verbose

#### <a name="step-22-add-load-balanced-endpoints-and-acls"></a>Étape 22 : Ajouter charge équilibrée des points de terminaison et les utilisateurs
    #Endpoints
    $epname="sqlIntEP"
    $prot="tcp"
    $locport=1433
    $pubport=1433
    Get-AzureVM –ServiceName $destcloudsvc –Name $vmNameToMigrate  | Add-AzureEndpoint -Name $epname -Protocol $prot -LocalPort $locport -PublicPort $pubport -ProbePort 59999 -ProbeIntervalInSeconds 5 -ProbeTimeoutInSeconds 11  -ProbeProtocol "TCP" -InternalLoadBalancerName $ilb -LBSetName $ilb -DirectServerReturn $true | Update-AzureVM


    #STOP!!! CHECK in the Azure classic portal or Machine Endpoints through powershell that these Endpoints are created!

    #SET ACLs or Azure Network Security Groups & Windows FWs

    #http://msdn.microsoft.com/library/azure/dn495192.aspx

#### <a name="step-23-test-failover"></a>Étape 23 : Basculement de Test

Laissez le nœud migré maintenant synchroniser avec le nœud de toujours sur locales, placez-le en mode de réplication synchrone et patienter jusqu'à ce qu’il est synchronisé. Basculement de local vers le premier nœud migrées, qui est le AFP. Une fois que qui a travaillé, modifier le dernier nœud migré vers le AFP.

Vous devez tester basculement entre tous les nœuds et fonctionnent bien que tests chaos afin de travail basculement comme prévu et dans un mode rapide.

#### <a name="step-24-put-back-cluster-quorum-settings--dns-ttl--failover-pntrs--sync-settings"></a>Étape 24 : Réinsérer les paramètres du quorum cluster / DNS TTL / Failover Pntrs / paramètres de synchronisation
##### <a name="adding-ip-address-resource-on-same-subnet"></a>Ajouter des ressources d’adresse IP même sous-réseau

Si vous avez seulement 2 serveurs SQL et à les migrer vers un service en nuage, mais tout en conservant sur le même sous-réseau, vous pouvez éviter de prendre le récepteur en mode hors connexion pour supprimer la toujours sous adresse IP d’origine et ajouter la nouvelle adresse IP. Si vous migrez les ordinateurs virtuels vers un autre sous-réseau que vous n’aurez pas à effectuer cette opération comme un réseau cluster supplémentaires qui font référence à ce sous-réseau sera.

Après avoir mis l’objet d’une migration secondaire et ajouté dans la nouvelle ressource adresse IP pour le nouveau service cloud avant le basculement primaire existante, vous devez prendre comme suit dans le Gestionnaire de basculement Cluster :

Pour ajouter dans la zone adresse IP, voir l' [appendice](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage), étape 14.

1. Pour la ressource adresse IP actuelle, modifier le propriétaire possible à « Existant primaire SQL Server », dans l’exemple ci-dessous, « dansqlams4 » :

    ![Appendix13][23]

1. Pour la nouvelle ressource adresse IP, modifier le propriétaire possible à 'Migrée secondaire SQL Server », dans l’exemple ci-dessous, « dansqlams5 » :

    ![Appendix14][24]

1. Une fois que cette option est définie vous pouvez basculer, et lorsque le dernier nœud est migré les propriétaires possibles doivent être modifiées et ce nœud est ajouté en tant que propriétaire Possible :

    ![Appendix15][25]

## <a name="additional-resources"></a>Ressources supplémentaires
- [Stockage Azure Premium](../storage/storage-premium-storage.md)
- [Machines virtuelles](https://azure.microsoft.com/services/virtual-machines/)
- [Machines virtuelles Azure SQL Server dans](virtual-machines-windows-sql-server-iaas-overview.md)

<!-- IMAGES -->
[1]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/1_VNET_Portal.png
[2]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/2_Diskname_Lun.png
[3]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/3_Virtual_Disk_Properties.png
[4]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/4_Virtual_Disk_Properties_Details.png
[5]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/5_Get_Storage_Pool.png
[6]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/6_Deployments_Always_On.png
[7]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/7_Add_More_Secondaries.png
[8]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/8_Use_Existing_Secondary_Single_Site.png
[9]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/9_Use_Existing_Secondary_Multi_Site.png
[10]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/9_Use_Existing_Secondary_Multi_Site_b.png
[11]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_01.png
[12]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_02.png
[13]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_03.png
[14]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_04.png
[15]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_05.png
[16]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_06.png
[17]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_07.png
[18]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_08.png
[19]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_09.png
[20]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_10.png
[21]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_11.png
[22]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_12.png
[23]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_13.png
[24]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_14.png
[25]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_15.png
