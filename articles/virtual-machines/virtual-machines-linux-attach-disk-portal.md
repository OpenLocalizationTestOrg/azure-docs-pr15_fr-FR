<properties
    pageTitle="Joindre un disque de données à une VM Linux | Microsoft Azure"
    description="Comment joindre disque de données existante ou nouvelle à une VM Linux dans le portail Azure à l’aide du modèle de déploiement Gestionnaire de ressources."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/06/2016"
    ms.author="cynthn"/>

# <a name="how-to-attach-a-data-disk-to-a-linux-vm-in-the-azure-portal"></a>L’insertion d’un disque de données à une VM Linux dans le portail Azure

Cet article vous explique comment joindre disques nouveaux et existants à une machine virtuelle Linux via le portail d’Azure. Vous pouvez également [joindre un disque de données pour une machine virtuelle Windows dans le portail Azure](virtual-machines-windows-attach-disk-portal.md). Avant de le faire, passez en revue les conseils suivants :

- La taille de la machine virtuelle contrôle combien disques de données que vous pouvez joindre. Pour plus d’informations, voir [formats pour les machines virtuelles](virtual-machines-linux-sizes.md).
- Pour utiliser le stockage Premium, vous devez une machine virtuelle DS ou GS séries. Vous pouvez utiliser des disques à partir des comptes de stockage Premium et Standard avec ces machines virtuelles. Stockage Premium est disponible dans certaines zones. Pour plus d’informations, voir [stockage Premium : stockage High Performance pour charges de travail Azure Machine virtuelle](../storage/storage-premium-storage.md).
- Disques associés aux machines virtuelles sont en réalité des fichiers .vhd dans un compte de stockage Azure. Pour plus d’informations, voir [à propos des disques et des disques durs virtuels pour les machines virtuelles](virtual-machines-linux-about-disks-vhds.md).
- Pour un nouveau disque, vous n’avez pas besoin créer tout d’abord, car Azure crée lorsque vous joignez.
- Pour un disque existant, le fichier .vhd doit être disponible dans un compte de stockage Azure. Vous pouvez utiliser un .vhd qui se trouve déjà, si elle n’est pas reliée à une autre machine virtuelle, ou téléchargement de fichiers votre propre .vhd pour le compte de stockage.


[AZURE.INCLUDE [virtual-machines-common-attach-disk-portal](../../includes/virtual-machines-common-attach-disk-portal.md)]

## <a name="next-steps"></a>Étapes suivantes

Une fois que le disque est ajouté, vous devez préparer à utiliser. Consultez la section dans le système d’exploitation de la machine virtuelle : « Comment : initialisation d’un disque de données nouveau dans Linux » dans cet [article](virtual-machines-linux-classic-attach-disk.md#how-to-initialize-a-new-data-disk-in-linux).
