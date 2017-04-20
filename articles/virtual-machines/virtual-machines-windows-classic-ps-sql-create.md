<properties
    pageTitle="Créer un serveur Machine virtuelle SQL dans Azure PowerShell (standard) | Microsoft Azure"
    description="Fournit les étapes et des scripts PowerShell pour créer une machine virtuelle Azure avec images de la galerie machine virtuelle SQL Server. Cette rubrique utilise le mode de déploiement classique."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="rothja"
    manager="jhubbard"
    editor=""
    tags="azure-service-management" />
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="10/07/2016"
    ms.author="jroth" />

# <a name="provision-a-sql-server-virtual-machine-using-azure-powershell-classic"></a>Configurer une machine virtuelle SQL Server à l’aide de PowerShell Azure (classique)

## <a name="overview"></a>Vue d’ensemble

Cet article explique comment créer une machine virtuelle SQL Server dans Azure en utilisant les applets de commande PowerShell.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Pour la version de gestionnaire de ressources de cette rubrique, voir [mise en service une machine virtuelle SQL Server à l’aide du Gestionnaire de ressources de PowerShell Azure](virtual-machines-windows-ps-sql-create.md).

### <a name="install-and-configure-powershell"></a>Installez et configurez PowerShell :

1. Si vous n’avez pas un compte Azure, visitez [Azure essai gratuit](https://azure.microsoft.com/pricing/free-trial/).

2. [Télécharger et installer les dernières commandes PowerShell Azure](../powershell-install-configure.md).

3. Démarrez Windows PowerShell et se connecter à votre abonnement Azure avec la commande **Ajouter AzureAccount** .

        Add-AzureAccount

## <a name="determine-your-target-azure-region"></a>Déterminer votre région Azure cible

Votre serveur de Machine virtuelle SQL sera hébergé dans un service cloud qui se trouve une région Azure spécifique. Les étapes suivantes vous aident à déterminer votre région, le compte de stockage et le cloud service qui sera utilisé pour le reste de ce didacticiel.

1. Déterminer le centre de données que vous voulez utiliser pour héberger votre machine virtuelle de SQL Server. Les commandes PowerShell suivants affiche les régions disponibles dans le détail avec une liste de synthèse à la fin.

        Get-AzureLocation
        (Get-AzureLocation).Name

2.  Une fois que vous avez identifié votre emplacement par défaut, définissez une variable nommée **$dcLocation** dans cette zone.

        $dcLocation = "<region name>"

## <a name="set-your-subscription-and-storage-account"></a>Configurer votre compte de stockage et d’abonnement

1. Déterminer l’abonnement Azure que vous utiliserez pour le nouvel ordinateur virtuel.

        (Get-AzureSubscription).SubscriptionName

1. Affecter votre abonnement Azure cible dans la variable **$subscr** . Définissez en tant que votre abonnement actuel Azure.

        $subscr="<subscription name>"
        Select-AzureSubscription -SubscriptionName $subscr –Current

1. Puis vérifier la présence de comptes de stockage existants. Le script suivant affiche tous les comptes de stockage présents dans votre région choisie :

        (Get-AzureStorageAccount | where { $_.GeoPrimaryLocation -eq $dcLocation }).StorageAccountName

    >[AZURE.NOTE] Si vous avez besoin d’un nouveau compte de stockage, tout d’abord créer un nom de compte de stockage de toutes les minuscules avec la commande New-AzureStorageAccount comme dans l’exemple suivant : **New-AzureStorageAccount - StorageAccountName «<storage account name>»-emplacement $dcLocation**

1. Affecter le nom de compte de stockage cible à la **$staccount**. Utilisez puis **Set-AzureSubscription** pour définir l’abonnement et le compte de stockage en cours.

        $staccount="<storage account name>"
        Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

## <a name="select-a-sql-server-virtual-machine-image"></a>Sélectionner une image de machine virtuelle SQL Server

1. Découvrez la liste des images de machines virtuelles SQL Server disponibles dans la galerie. Ces toutes les images ont une propriété **ImageFamily** qui commence par « SQL ». La requête suivante affiche la famille image disponible à qui vous ont SQL Server est préinstallé.

        Get-AzureVMImage | where { $_.ImageFamily -like "SQL*" } | select ImageFamily -Unique | Sort-Object -Property ImageFamily

1. Lorsque vous avez trouvé la famille image machine virtuelle, vous pouvez rencontrer plusieurs images publiés dans cette famille. Utiliser le script suivant pour rechercher le nom d’image publiés machine virtuelle plus récent pour votre famille image sélectionnée (par exemple, **SQL Server 2016 RTM Enterprise sur Windows Server 2012 R2**) :

        $family="<ImageFamily value>"
        $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

        echo "Selected SQL Server image name:"
        echo "   $image"

## <a name="create-the-virtual-machine"></a>Créer la machine virtuelle

Enfin, créez la machine virtuelle avec PowerShell :

1. Créer un service cloud pour héberger la nouvelle machine virtuelle. Notez qu’il est également possible d’utiliser un service cloud existant à la place. Créer une nouvelle variable **$svcname** avec le nom court du service cloud.

        $svcname = "<cloud service name>"
        New-AzureService -ServiceName $svcname -Label $svcname -Location $dcLocation

2. Spécifiez le nom de la machine virtuelle et une taille. Pour plus d’informations sur les tailles de machine virtuelle, voir [Tailles Machine virtuelle pour Azure](virtual-machines-windows-sizes.md).

        $vmname="<machine name>"
        $vmsize="<Specify one: Large, ExtraLarge, A5, A6, A7, A8, A9, or see the link to the other VM sizes>"
        $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

3. Spécifier le compte d’administrateur local et le mot de passe.

        $cred=Get-Credential -Message "Type the name and password of the local administrator account."
        $vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

4. Exécutez le script suivant pour créer la machine virtuelle.

        New-AzureVM –ServiceName $svcname -VMs $vm1

>[AZURE.NOTE] Pour explication supplémentaire et options de configuration, consultez la section **créer votre jeu de commandes** dans [Utiliser Azure PowerShell pour créer et de préconfigurer Machines virtuelles fonctionnant sous Windows](virtual-machines-windows-classic-create-powershell.md).

## <a name="example-powershell-script"></a>Exemple de script PowerShell

Le script suivant fournit et exemple d’un script complet qui crée une machine virtuelle **SQL Server 2016 RTM Enterprise sur Windows Server 2012 R2** . Si vous utilisez ce script, vous devez personnaliser les variables initiales selon les étapes précédentes dans cette rubrique.

    # Customize these variables based on your settings and requirements:
    $dcLocation = "East US"
    $subscr="mysubscription"
    $staccount="mystorageaccount"
    $family="SQL Server 2016 RTM Enterprise on Windows Server 2012 R2"
    $svcname = "mycloudservice"
    $vmname="myvirtualmachine"
    $vmsize="A5"

    # Set the current subscription and storage account
    # Comment out the New-AzureStorageAccount line if the account already exists
    Select-AzureSubscription -SubscriptionName $subscr –Current
    New-AzureStorageAccount -StorageAccountName $staccount -Location $dcLocation
    Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

    # Select the most recent VM image in this image family:
    $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

    # Create the new cloud service; comment out this line if cloud service exists already:
    New-AzureService -ServiceName $svcname -Label $svcname -Location $dcLocation

    # Create the VM config:
    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

    # Set administrator credentials:
    $cred=Get-Credential -Message "Type the name and password of the local administrator account."
    $vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

    # Create the SQL Server VM:
    New-AzureVM –ServiceName $svcname -VMs $vm1


## <a name="connect-with-remote-desktop"></a>Connexion Bureau à distance

1. Créer la. Fichiers RDP dans le dossier de documents de l’utilisateur actuel pour lancer ces machines virtuelles pour terminer la configuration :

        $documentspath = [environment]::getfolderpath("mydocuments")
        Get-AzureRemoteDesktopFile -ServiceName $svcname -Name $vmname -LocalPath "$documentspath\vm1.rdp"

1. Dans le répertoire documents, lancez le fichier RDP. Se connecter avec le nom d’utilisateur administrateur et le mot de passe fourni précédemment (par exemple, si votre nom d’utilisateur a été VMAdmin, spécifiez « \VMAdmin » en tant que l’utilisateur et le mot de passe).

        cd $documentspath
        .\vm1.rdp

## <a name="complete-the-configuration-of-the-sql-server-machine-for-remote-access"></a>Terminer la configuration de l’ordinateur SQL Server pour l’accès à distance

Une fois la connexion sur l’ordinateur de bureau à distance, configurez SQL Server selon les instructions dans les [étapes de configuration de la connectivité de SQL Server dans une machine virtuelle Azure](virtual-machines-windows-classic-sql-connect.md#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).

## <a name="next-steps"></a>Étapes suivantes

Vous trouverez des instructions supplémentaires de mise en service des machines virtuelles avec PowerShell dans la [documentation de machines virtuelles](virtual-machines-windows-classic-create-powershell.md). Pour les scripts supplémentaires relatifs à SQL Server et stockage Premium, voir [Utiliser le stockage Azure Premium avec SQL Server sur des Machines virtuelles](virtual-machines-windows-classic-sql-server-premium-storage.md).

Dans de nombreux cas, l’étape suivante consiste à migrer vos bases de données pour cette nouvelle machine virtuelle SQL Server. Pour des instructions de migration de base de données, voir [migrer une base de données SQL Server sur un ordinateur virtuel Azure](virtual-machines-windows-migrate-sql.md).

Si vous souhaitez également dans l’aide du portail Azure pour créer des Machines virtuelles SQL, voir [mise en service d’un serveur SQL Machine virtuelle sur Azure](virtual-machines-windows-portal-sql-server-provision.md). Notez que le didacticiel qui vous guide dans le portail crée des machines virtuelles à l’aide du modèle de gestionnaire de ressources recommandé, plutôt que le modèle classique utilisé dans cette rubrique PowerShell.

Outre ces ressources, nous vous recommandons de consulter [d’autres rubriques liés à l’exécution de SQL Server dans le Machines virtuelles Azure](virtual-machines-windows-sql-server-iaas-overview.md).
