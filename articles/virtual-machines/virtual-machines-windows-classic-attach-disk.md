<properties
    pageTitle="Connectez un disque à une machine virtuelle | Microsoft Azure"
    description="Joindre un disque de données pour une machine virtuelle Windows créée avec le modèle de déploiement classique et initialisation."
    services="virtual-machines-windows, storage"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/27/2016"
    ms.author="cynthn"/>

# <a name="attach-a-data-disk-to-a-windows-virtual-machine-created-with-the-classic-deployment-model"></a>Joindre un disque de données pour une machine virtuelle Windows créée avec le modèle de déploiement classique

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Si vous souhaitez utiliser le nouveau portail, voir [l’insertion d’un disque de données pour une machine virtuelle Windows dans le portail Azure](virtual-machines-windows-attach-disk-portal.md).

Si vous avez besoin d’un disque de données supplémentaires, vous pouvez joindre un disque vide ou un disque existant avec les données à une machine virtuelle. Dans les deux cas, les disques sont fichiers .vhd qui résident dans un compte de stockage Azure. Dans le cas d’un nouveau disque, une fois que vous connectez le disque, vous devez également il initialisation afin qu’elle soit prêt à être utilisé par une machine virtuelle Windows.

Pour plus d’informations sur les disques, voir [à propos de disques et des disques durs virtuels pour les Machines virtuelles](virtual-machines-windows-about-disks-vhds.md).


[AZURE.INCLUDE [howto-attach-disk-windows-linux](../../includes/howto-attach-disk-windows-linux.md)]

## <a name="initialize-the-disk"></a>Initialisation le disque

1. Se connecter à la machine virtuelle. Pour plus d’informations, voir [comment se connecter à une machine virtuelle exécutant Windows Server][logon].

2. Après que vous être connecté à la machine virtuelle, ouvrez le **Gestionnaire de serveur**. Dans le volet gauche, sélectionnez **les Services de stockage de fichiers et**.

    ![Ouvrir le gestionnaire serveur](./media/virtual-machines-windows-classic-attach-disk/fileandstorageservices.png)

3. Développer le menu et sélectionnez **disques**.

4. La section **disques** répertorie les disques. Dans la plupart des cas, il aura disque 0, disques 1 et 2. Disque 0 est le disque système d’exploitation et le disque 1 est le disque temporaire disque 2 est le disque de données que vous venez d’attacher de la machine virtuelle. Le nouveau disque de données permet de répertorier la Partition **inconnu**. Cliquez sur le disque et sélectionnez **initialisation**.

5.  Vous êtes averti que toutes les données seront supprimées lorsque le disque est initialisé. Cliquez sur **Oui** pour valider l’avertissement et initialisation le disque. Une fois terminé, la partition est répertoriée comme **GPT**. Le disque avec le bouton droit à nouveau, puis sélectionnez **Nouveau Volume**.

6.  Exécuter l’Assistant en utilisant les valeurs par défaut. Une fois l’Assistant terminé, la section **Volumes** répertorie le nouveau volume. Le disque est maintenant prêt à stocker des données et en ligne.

    ![Volume initialisé avec succès](./media/virtual-machines-windows-classic-attach-disk/newvolumecreated.png)

> [AZURE.NOTE] La taille de la machine virtuelle détermine combien de disques vous pouvez joindre à celui-ci. Pour plus d’informations, voir [formats pour les machines virtuelles](virtual-machines-linux-sizes.md).

## <a name="additional-resources"></a>Ressources supplémentaires

[La dissociation d’un disque à partir d’une machine virtuelle Windows](virtual-machines-windows-classic-detach-disk.md)

[À propos des disques et des disques durs virtuels pour les machines virtuelles](virtual-machines-linux-about-disks-vhds.md)

[logon]: virtual-machines-windows-classic-connect-logon.md
