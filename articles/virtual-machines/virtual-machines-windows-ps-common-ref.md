<properties 
   pageTitle="Commandes PowerShell courantes pour les machines virtuelles | Microsoft Azure"
   description="Commandes PowerShell courantes pour vous aider à créer et gérer vos ordinateurs virtuels dans Azure sur Windows"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="davidmu1" 
   manager="timlt" 
   editor="tysonn" 
   tags="azure-resource-manager"/>
   
<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="09/27/2016"
   ms.author="davidmu" />

# <a name="common-powershell-commands-for-creating-and-managing-vms"></a>Commandes PowerShell courantes pour créer et gérer des machines virtuelles

Cet article décrit certaines des commandes PowerShell Azure que vous pouvez utiliser pour créer et gérer des machines virtuelles dans votre abonnement Azure.  Pour plus d’aide spécifiques commutateurs de ligne de commande et options, vous pouvez utiliser **Get-Help** *commande*.

Découvrez [comment installer et configurer Azure PowerShell](../powershell-install-configure.md) pour plus d’informations sur l’installation de la dernière version d’Azure PowerShell, en sélectionnant votre abonnement et se connecter à votre compte.

## <a name="create-a-vm"></a>Créer une machine virtuelle

Tâche | Commande
-------------- | -------------------------
Créer une configuration machine virtuelle | $vm = [New-AzureRmVMConfig](https://msdn.microsoft.com/library/mt603727.aspx) - VMName « vm_name » - VMSize « vm_size »<BR></BR><BR></BR>La configuration de la machine virtuelle est utilisée pour définir ou mettre à jour les paramètres de la machine virtuelle. La configuration est initialisée avec le nom de la machine virtuelle et sa [taille](virtual-machines-windows-sizes.md).
Ajouter des paramètres de configuration | $vm = [Set-AzureRmVMOperatingSystem](https://msdn.microsoft.com/library/mt603843.aspx) - machine virtuelle $vm-Windows - ComputerName « Nom_Ordinateur »-$cred - ProvisionVMAgent des informations d’identification - EnableAutoUpdate<BR></BR><BR></BR>Paramètres de système d’exploitation, y compris des [informations d’identification](https://technet.microsoft.com/library/hh849815.aspx) sont ajoutés à l’objet de configuration que vous avez précédemment créé à l’aide du nouveau AzureRmVMConfig.
Ajoutez une interface réseau | $vm = [Ajouter AzureRmVMNetworkInterface](https://msdn.microsoft.com/library/mt619351.aspx) - machine virtuelle $vm-Id $ carte. ID<BR></BR><BR></BR>Une machine virtuelle doit avoir une [interface de réseau](virtual-machines-windows-ps-create.md) pour communiquer dans un réseau virtuel. Vous pouvez également utiliser [Get-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619434.aspx) pour récupérer un objet interface réseau existant.
Spécifier une image de la plateforme | $vm = publisher_name » [Set-AzureRmVMSourceImage](https://msdn.microsoft.com/library/mt619344.aspx) - machine virtuelle $vm - éditeur »-proposent des références SKU-« product_sku » « publisher_offer »-Version « plus »<BR></BR><BR></BR>[Informations sur l’image](virtual-machines-windows-cli-ps-findimage.md) est ajouté à l’objet de configuration que vous avez précédemment créé à l’aide du nouveau AzureRmVMConfig. L’objet retourné par cette commande est utilisé uniquement lorsque vous définissez le disque du système d’exploitation d’utiliser une image de la plateforme.
Définir le disque du système d’exploitation d’utiliser une image de la plateforme | $vm = [Set-AzureRmVMOSDisk](https://msdn.microsoft.com/library/mt603746.aspx) - machine virtuelle $vm-http://mystore1.blob.core.windows.net/vhds/disk_name.vhd « nom « nom_disque » - VhdUri » - CreateOption FromImage<BR></BR><BR></BR>Le nom du disque système d’exploitation et de son emplacement de [stockage](../storage/storage-powershell-guide-full.md) est ajouté à l’objet de configuration que vous avez créé précédemment.
Définir le disque du système d’exploitation d’utiliser une image GRG | $vm = set-AzureRmVMOSDisk - machine virtuelle $vm-https://mystore1.blob.core.windows.net/system/Microsoft.Compute/Images/myimages/myprefix-osDisk « nom « nom_disque » - SourceImageUri. -VhdUri {guid} .vhd » « https://mystore1.blob.core.windows.net/vhds/disk_name.vhd » - CreateOption FromImage-Windows<BR></BR><BR></BR>Le nom du disque système d’exploitation, l’emplacement de l’image source et emplacement du disque dans le [stockage](../storage/storage-powershell-guide-full.md) est ajouté à l’objet de configuration.
Définir le disque du système d’exploitation d’utiliser une image spécialisée | $vm = set-AzureRmVMOSDisk - machine virtuelle $vm-http://mystore1.blob.core.windows.net/vhds/ « nom « name_of_disk » - VhdUri » - CreateOption joindre - Windows
Créer une machine virtuelle | « Nom_groupe_ressource » [New-AzureRmVM]() - ResourceGroupName-emplacement « location_name » - machine virtuelle $vm<BR></BR><BR></BR>Toutes les ressources sont créées dans un [groupe de ressources](../powershell-azure-resource-manager.md). La machine virtuelle doit être créée dans le même [emplacement](https://msdn.microsoft.com/library/azure/dn495177.aspx) que le groupe de ressources. Avant d’exécuter cette commande, exécuter nouveau AzureRmVMConfig, jeu AzureRmVMOperatingSystem, jeu AzureRmVMSourceImage, ajouter AzureRmVMNetworkInterface et jeu AzureRmVMOSDisk.

## <a name="get-information-about-vms"></a>Obtenir des informations sur les ordinateurs virtuels

Tâche | Commande
-------------- | -------------------------
Machines virtuelles liste dans un abonnement| [Get-AzureRmVM](https://msdn.microsoft.com/library/mt603718.aspx)
Machines virtuelles liste dans un groupe de ressources | Get-AzureRmVM - ResourceGroupName « nom_groupe_ressource »<BR></BR><BR></BR>Pour obtenir une liste des groupes de ressources dans votre abonnement, utilisez [Get-AzureRmResourceGroup](https://msdn.microsoft.com/library/mt679016.aspx).
Obtenir des informations sur une machine virtuelle | « Nom_groupe_ressource » Get-AzureRmVM - ResourceGroupName-vm_name « nom »

## <a name="manage-vms"></a>Gérer des ordinateurs virtuels

Tâche | Commande
-------------- | -------------------------
Démarrer une machine virtuelle | [Démarrage AzureRmVM](https://msdn.microsoft.com/library/mt603453.aspx) - ResourceGroupName « nom_groupe_ressource »-vm_name « nom »
Arrêter une machine virtuelle | [Stop AzureRmVM](https://msdn.microsoft.com/library/mt603483.aspx) - ResourceGroupName « nom_groupe_ressource »-vm_name « nom »
Redémarrer une machine virtuelle en cours d’exécution | [Redémarrer-AzureRmVM](https://msdn.microsoft.com/library/mt603775.aspx) - ResourceGroupName « nom_groupe_ressource »-vm_name « nom »
Supprimer une machine virtuelle | « Nom_groupe_ressource » [Supprimer AzureRmVM](https://msdn.microsoft.com/library/mt603641.aspx) - ResourceGroupName-vm_name « nom »
Généralisez une machine virtuelle | [Jeu-AzureRmVm](https://msdn.microsoft.com/library/mt603688.aspx) - ResourceGroupName YourResourceGroup-vm_name « nom »-Generalized<BR></BR><BR></BR>Exécutez cette commande avant d’exécuter enregistrer AzureRmVMImage.
Capturer une machine virtuelle | [Enregistrer AzureRmVMImage](https://msdn.microsoft.com/library/mt619423.aspx) - ResourceGroupName « nom_groupe_ressource » - VMName « vm_name » - DestinationContainerName « image_container » - VHDNamePrefix « image_name_prefix »-chemin d’accès « C:\filepath\filename.json »<BR></BR><BR></BR>Une machine virtuelle doit être [arrêté et généralisée](virtual-machines-windows-generalize-vhd.md) à utiliser pour créer une image. Avant d’exécuter cette commande, exécutez Set-AzureRmVm.
Mettre à jour une machine virtuelle | [Mise à jour AzureRmVM](https://msdn.microsoft.com/library/mt603662.aspx) - ResourceGroupName « nom_groupe_ressource » machine virtuelle - $vm<BR></BR><BR></BR>Obtenir la configuration machine virtuelle actuelle à l’aide de Get-AzureRmVM, modifier les paramètres de configuration de l’objet de la mémoire virtuelle et puis exécutez la commande suivante.
Ajout d’un disque de données pour une machine virtuelle | [Ajouter AzureRmVMDataDisk](https://msdn.microsoft.com/library/mt603673.aspx) - machine virtuelle $vm-https://mystore1.blob.core.windows.net/vhds/disk_name.vhd « nom « nom_disque » - VhdUri » LUN-#-mise en cache ReadWrite - DiskSizeinGB # - CreateOption vide<BR></BR><BR></BR>Get-AzureRmVM permet d’obtenir l’objet de la mémoire virtuelle. Spécifier le nombre LUN et la taille du disque. Exécuter la mise à jour-AzureRmVM pour appliquer les modifications de configuration de la machine virtuelle. Le disque que vous ajoutez n’est pas initialisé. Pour plus d’informations sur l’initialisation disques lorsqu’elles sont ajoutées, voir [Gérer les Machines d’Azure virtuelles à l’aide de gestionnaire de ressources et PowerShell](virtual-machines-windows-ps-manage.md).
Supprimer un disque de données à partir d’une machine virtuelle | [Supprimer AzureRmVMDataDisk](https://msdn.microsoft.com/library/mt603614.aspx) - machine virtuelle $vm-nom_disque « nom »<BR></BR><BR></BR>Get-AzureRmVM permet d’obtenir l’objet de la mémoire virtuelle. Exécuter la mise à jour-AzureRmVM pour appliquer les modifications de configuration de la machine virtuelle.
Ajouter une extension à une machine virtuelle | « Nom_groupe_ressource » [Set-AzureRmVMExtension](https://msdn.microsoft.com/library/mt603745.aspx) - ResourceGroupName-vm_name « emplacement « azure_location » - VMName »-nom_extension « nom »-Publisher « publisher_name »-Type « type_extension » - TypeHandlerVersion « #. # «-paramètres $Settings - ProtectedSettings $ProtectedSettings<BR></BR><BR></BR>Exécutez cette commande avec les [informations de configuration](virtual-machines-windows-extensions-configuration-samples.md) appropriées pour l’extension que vous voulez installer.
Supprimer une extension de machine virtuelle | « Nom_groupe_ressource » [Supprimer AzureRmVMExtension](https://msdn.microsoft.com/library/mt603782.aspx) - ResourceGroupName-vm_name « nom « nom_extension » - VMName »

## <a name="next-steps"></a>Étapes suivantes

- Consultez les étapes de base pour la création d’une machine virtuelle dans [créer une machine virtuelle Windows à l’aide du Gestionnaire de ressources et PowerShell](virtual-machines-windows-ps-create.md).

