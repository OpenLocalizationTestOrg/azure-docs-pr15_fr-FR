<properties
    pageTitle="Détacher un disque de données à partir d’un ordinateur Windows virtuel | Microsoft Azure"
    description="Découvrez comment détacher un disque de données à partir d’une machine virtuelle dans Azure à l’aide du modèle de déploiement Gestionnaire de ressources."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="cynthn"/>



# <a name="how-to-detach-a-data-disk-from-a-windows-virtual-machine"></a>La dissociation d’un disque de données à partir d’une machine virtuelle Windows


Lorsque vous n’avez plus besoin d’un disque de données est associé à une machine virtuelle, vous pouvez facilement la détacher. Cela supprime le disque de la machine virtuelle, mais ne supprime pas celui-ci de stockage. 

> [AZURE.WARNING] Si vous détachez un disque, qu'il n’est pas automatiquement supprimé. Si vous avez souscrit au stockage Premium, vous continuent à subir des frais de stockage pour le disque. Pour plus d’informations, reportez-vous à [tarification et facturation lors de l’utilisation de stockage Premium](../storage/storage-premium-storage.md#pricing-and-billing). 

Si vous souhaitez réutiliser les données existantes sur le disque, vous pouvez joindre à nouveau à la même machine virtuelle, ou une autre.  


## <a name="detach-a-data-disk-using-the-portal"></a>Détacher un disque de données à l’aide du portail

1. Dans le hub portail, sélectionnez **Machines virtuelles**.

2. Sélectionnez l’ordinateur virtuel qui contient le disque de données que vous souhaitez détacher, puis cliquez sur **tous les paramètres**.

3. Dans la carte de **paramètres** , sélectionnez **disques**.

4. Dans la carte de **disques** , sélectionnez le disque de données que vous voulez dissocier.

5. Dans la carte pour le disque de données, cliquez sur **Détacher**.


    ![Capture d’écran montrant le bouton détacher.](./media/virtual-machines-windows-detach-disk/detach-disk.png)

Le disque est conservé dans le stockage mais n’est plus attaché à une machine virtuelle.


## <a name="detach-a-data-disk-using-powershell"></a>Détacher un disque de données à l’aide de PowerShell

Dans cet exemple, la commande première Obtient la machine virtuelle nommée **MyVM07** dans le groupe de ressources **RG11** à l’aide de l’applet de commande Get-AzureRmVM. La commande stocke la machine virtuelle dans la variable **$VirtualMachine** . 

La deuxième commande supprime le disque de données nommé DataDisk3 à partir de la machine virtuelle. 

La commande finale met à jour l’état de la machine virtuelle pour terminer le processus de suppression du disque de données.

    $VirtualMachine = Get-AzureRmVM -ResourceGroupName "RG11" -Name "MyVM07" 
    Remove-AzureRmVMDataDisk -VM $VirtualMachine -Name "DataDisk3"
    Update-AzureRmVM -ResourceGroupName "RG11" -Name "MyVM07" -VM $VirtualMachine


Pour plus d’informations, voir [Supprimer AzureRmVMDataDisk](https://msdn.microsoft.com/library/mt603614.aspx)

## <a name="next-steps"></a>Étapes suivantes

Si vous voulez réutiliser le disque de données, vous pouvez simplement [joignez-le à un autre machine virtuelle](virtual-machines-windows-attach-disk-portal.md)
