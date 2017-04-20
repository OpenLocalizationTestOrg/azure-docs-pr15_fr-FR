<properties
    pageTitle="À propos des disques et des disques durs virtuels pour Windows machines virtuelles | Microsoft Azure"
    description="Découvrez les concepts de base des disques et des disques durs virtuels pour Windows virtuelle machines dans Azure."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="cynthn"/>

# <a name="about-disks-and-vhds-for-azure-virtual-machines"></a>À propos des disques et des disques durs virtuels pour les machines virtuelles Azure

Comme n’importe quel autre ordinateur, machines virtuelles dans Azure utiliser disques comme emplacement pour stocker un système d’exploitation, des applications et des données. Toutes les machines virtuelles Azure disposez au moins deux disques – un disque de système d’exploitation Windows et un disque temporaire. Le disque système d’exploitation est créé à partir d’une image, puis le disque système d’exploitation et l’image sont des disques durs virtuels (disques durs virtuels) stockées dans un compte de stockage Azure. Machines virtuelles peut également avoir un ou plusieurs disques de données, qui sont également stockés en tant que disques durs virtuels. Cet article est également disponible pour les [machines virtuelles Linux](virtual-machines-linux-about-disks-vhds.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]



## <a name="operating-system-disk"></a>Disque système d’exploitation

Chaque machine virtuelle dispose d’un système d’exploitation attaché disque. Il est enregistré comme un disque et appelé le lecteur C: par défaut. Ce disque a une capacité maximale de 1023 gigaoctets (Go). 

##<a name="temporary-disk"></a>Disque temporaire

Le disque temporaire est automatiquement créé pour vous. Le disque temporaire est appelé le lecteur d par défaut et il est utilisé pour le stockage pagefile.sys. 

La taille du disque temporaire varie en fonction de la taille de la machine virtuelle. Pour plus d’informations, voir [machines virtuelles tailles pour Windows](virtual-machines-windows-sizes.md).

>[AZURE.WARNING] Ne pas stocker des données sur le disque temporaire. Il fournit un stockage temporaire pour les applications et processus et est destiné à stocker uniquement des données tels que des fichiers de page ou de remplacement. Pour remapper ce disque à une autre lettre, voir [Modifier la lettre de lecteur du disque temporaire Windows](virtual-machines-windows-classic-change-drive-letter.md).

Pour plus d’informations sur la façon Azure utilise le disque temporaire, voir [Présentation du lecteur temporaire Microsoft Azure machines virtuelles en fonctionnement](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

## <a name="data-disk"></a>Disque de données

Un disque de données est un disque dur virtuel est associé à une machine virtuelle pour stocker les données d’application, ou d’autres données qu'auxquelles vous souhaitez conserver. Disques de données sont enregistrés en tant que lecteurs SCSI et intitulées par une lettre que vous choisissez.  Chaque disque de données a une capacité maximale de 1023 go. La taille de la machine virtuelle détermine combien disques de données que vous pouvez joindre et le type de stockage que vous pouvez utiliser pour héberger les disques.

>[AZURE.NOTE] Pour plus d’informations sur les capacités de machines virtuelles, voir [machines virtuelles tailles pour Windows](virtual-machines-windows-sizes.md).

Azure crée un disque de système d’exploitation lorsque vous créez une machine virtuelle à partir d’une image. Si vous utilisez une image qui inclut des disques de données, Azure crée également les disques de données lorsqu’il crée la machine virtuelle. Dans le cas contraire, vous ajoutez des disques de données après avoir créé la machine virtuelle.

Vous pouvez ajouter des disques de données pour une machine virtuelle à tout moment, en **en joignant** le disque à la machine virtuelle. Vous pouvez utiliser un disque dur virtuel que vous avez téléchargé ou copiées à votre compte de stockage, ou qui Azure crée pour vous. Attacher un disque de données associe le fichier de disque dur virtuel avec la machine virtuelle en plaçant une « location » sur le disque dur virtuel afin qu’il ne peut pas être supprimé à partir du stockage lorsqu’il est toujours connecté.

## <a name="about-vhds"></a>À propos des disques durs virtuels

Les disques durs virtuels utilisés dans Azure sont des fichiers .vhd stockés en tant que des objets BLOB de page dans un compte de stockage standard ou premium dans Azure. Pour plus d’informations sur les objets BLOB de page, voir [objets BLOB bloc comprendre et des objets BLOB de page](https://msdn.microsoft.com/library/ee691964.aspx). Pour plus d’informations sur le stockage premium, voir [stockage Premium : stockage High performance pour charges de travail Azure machine virtuelle](../storage/storage-premium-storage.md).

Azure prend en charge le format de disque dur virtuel disque fixe. Le format fixe présente le disque logique linéaire dans le fichier, afin qu’offset disque X est stocké au décalage blob X. Un pied de page à la fin de l’objet blob décrit les propriétés de ce dernier. Souvent, les déchets format fixe espace le permet, car la plupart des disques ont grande plage de données non utilisés dans les. Cependant, Azure stocke les fichiers .vhd dans un format incomplet, vous recevez les avantages des deux disques fixes et dynamiques en même temps. Pour plus d’informations, voir [prise en main des disques durs virtuels](https://technet.microsoft.com/library/dd979539.aspx).

Tous les fichiers .vhd Azure que vous souhaitez utiliser comme source pour créer des disques ou des images sont en lecture seule. Lorsque vous créez un disque ou une image, Azure effectue des copies des fichiers .vhd. Ces copies peuvent être en lecture seule ou en lecture / écriture, selon la façon dont vous utilisez le disque dur virtuel.

Lorsque vous créez une machine virtuelle à partir d’une image, Azure crée un disque de la machine virtuelle qui est une copie du fichier .vhd source. Pour vous protéger contre toute suppression accidentelle, Azure place une location sur n’importe quel fichier .vhd source qui est utilisé pour créer une image, un disque de système d’exploitation ou un disque de données.

Avant de pouvoir supprimer un fichier .vhd source, vous devez supprimer la location en supprimant le disque ou l’image. Vous pouvez supprimer la machine virtuelle, le disque de système d’exploitation et le fichier .vhd source tous en même temps en supprimant la machine virtuelle et supprimer tous les disques associés. Toutefois, la suppression d’un fichier .vhd qui est une source d’un disque de données comporte plusieurs étapes dans un ordre ensemble. Tout d’abord vous détachez le disque à partir de la machine virtuelle, puis supprimez le disque et supprimez le fichier .vhd.

>[AZURE.WARNING] Si vous supprimez un fichier .vhd source à partir du stockage, ou supprimez votre compte de stockage, Microsoft ne peut pas récupérer des données pour vous.



## <a name="next-steps"></a>Étapes suivantes
-  [Joindre un disque](virtual-machines-windows-attach-disk-portal.md) pour ajouter un espace de stockage supplémentaire pour votre ordinateur virtuel.
-  [Télécharger une image machine virtuelle Windows Azure](virtual-machines-windows-upload-image.md) à utiliser lors de la création d’un nouvel ordinateur virtuel.
-  [Modifier la lettre de lecteur du disque temporaire Windows](virtual-machines-windows-classic-change-drive-letter.md) afin que votre application peut utiliser le lecteur d pour les données.
