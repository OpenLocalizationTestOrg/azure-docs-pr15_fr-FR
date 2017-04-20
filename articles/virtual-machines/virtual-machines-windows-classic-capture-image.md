<properties
    pageTitle="Capturer une image d’une machine virtuelle Windows Azure | Microsoft Azure"
    description="Capturer une image d’un ordinateur virtuel Windows Azure créé avec le modèle de déploiement classique."
    services="virtual-machines-windows"
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
    ms.date="09/27/2016"
    ms.author="cynthn"/>

#<a name="capture-an-image-of-an-azure-windows-virtual-machine-created-with-the-classic-deployment-model"></a>Capturer une image d’un ordinateur virtuel Windows Azure créé avec le modèle de déploiement classique.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Pour plus d’informations de modèle de gestionnaire de ressources, voir [créer une copie machine virtuelle Windows en cours d’exécution dans Azure](virtual-machines-windows-vhd-copy.md).


Cet article vous explique comment faire pour capturer une machine virtuelle Azure exécutant Windows afin d’utiliser en tant qu’image pour créer d’autres machines virtuelles. Cette image inclut le disque système d’exploitation et des disques de données qui sont joints à la machine virtuelle. Il n’inclut pas les configurations de mise en réseau, vous devez configurer ceux lorsque vous créez les autres machines virtuelles qui utilisent l’image.

Azure stocke l’image sous **Mes Images**. Il s’agit au même endroit où sont stockées les images que vous avez téléchargée. Pour plus d’informations sur les images, voir [à propos des images des machines virtuelles](virtual-machines-linux-classic-about-images.md).

##<a name="before-you-begin"></a>Avant de commencer##

Cette procédure suppose que vous avez déjà créé une machine virtuelle Azure et configuré le système d’exploitation, notamment en joignant les disques de données. Si vous n’avez pas défini ces encore, consultez ces instructions :

- [Créer une machine virtuelle à partir d’une image](virtual-machines-windows-classic-createportal.md)
- [L’insertion d’un disque de données pour une machine virtuelle](virtual-machines-windows-classic-attach-disk.md)
- Vérifiez que les rôles de serveur sont pris en charge avec Sysprep. Pour plus d’informations, voir [Prise en charge Sysprep des rôles de serveur](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

> [AZURE.WARNING] Ce processus supprime la machine virtuelle d’origine une fois qu’il est capturé. 

Avant caputuring une image d’un ordinateur virtuel Azure, il est recommandé de sauvegarder la machine virtuelle cible. Machines virtuelles Azure peuvent être sauvegardés à l’aide de sauvegarde Azure. Pour plus d’informations, voir [sauvegarder des machines virtuelles Azure](../backup/backup-azure-vms.md). Autres solutions sont disponibles à partir de partenaires certifiés. Pour savoir ce qui est actuellement disponible, recherchez la Azure Marketplace.


##<a name="capture-the-virtual-machine"></a>Capture de la machine virtuelle

1. Dans le [portail classique Azure](http://manage.windowsazure.com), **se connecter** à la machine virtuelle. Pour plus d’informations, voir [comment se connecter à une machine virtuelle exécutant Windows Server] [].

2.  Ouvrez une fenêtre d’invite de commandes en tant qu’administrateur.

3.  Basculez vers le répertoire `%windir%\system32\sysprep`, puis exécutez sysprep.exe.

4.  La boîte de dialogue **Outil de préparation système** s’affiche. Procédez comme suit :

    - Dans l' **Action de nettoyage du système**, sélectionnez **système Entrez de prédéfinies Experience (OOBE)** et assurez-vous que **Generalize** est activée. Pour plus d’informations sur l’utilisation de Sysprep, voir [comment utiliser Sysprep : An Introduction][].

    - Dans les **Options d’arrêt**, sélectionnez **Arrêter le système**.

    - Cliquez sur **OK**.

    ![Exécutez Sysprep](./media/virtual-machines-windows-classic-capture-image/SysprepGeneral.png)

7.  Sysprep s’arrête la machine virtuelle, qui détermine l’état de la machine virtuelle dans le portail classique Azure **arrêté**.

8.  Dans le portail Azure classique, cliquez sur **Machines virtuelles** et sélectionnez la machine virtuelle que vous voulez capturer.

9.  Dans la barre de commandes, cliquez sur **capturer**.

    ![Capturer machine virtuelle](./media/virtual-machines-windows-classic-capture-image/CaptureVM.png)

    La boîte de dialogue **capturer la Machine virtuelle** s’affiche.

10. Dans la zone **Nom de l’Image**, tapez un nom pour la nouvelle image.

11. Avant d’ajouter une image de Windows Server à votre ensemble d’images personnalisés, il doit être contient en exécutant Sysprep comme indiqué dans les étapes précédentes. Cliquez sur **J’ai exécute Sysprep sur l’ordinateur virtuel** pour indiquer que vous l’avez fait.

12. Activez la case à cocher pour capturer l’image. La nouvelle image est désormais disponible sous **les Images**.

    ![Capture de l’image réussie](./media/virtual-machines-windows-classic-capture-image/VMCapturedImageAvailable.png)

##<a name="next-steps"></a>Étapes suivantes

L’image est prêt à être utilisé pour créer des machines virtuelles. Pour ce faire, vous devez créer une machine virtuelle à l’aide de l’élément de menu **De la galerie** et sélectionnez l’image que vous venez de créer. Pour plus d’informations, voir [créer une machine virtuelle à partir d’une image](virtual-machines-windows-classic-createportal.md).



[Comment se connecter à une machine virtuelle exécutant Windows Server]: virtual-machines-windows-classic-connect-logon.md
[Comment utiliser Sysprep : Introduction]: http://technet.microsoft.com/library/bb457073.aspx
[Run Sysprep.exe]: ./media/virtual-machines-capture-image-windows-server/SysprepCommand.png
[Enter Sysprep.exe options]: ./media/virtual-machines-windows-classic-capture-image/SysprepGeneral.png
[The virtual machine is stopped]: ./media/virtual-machines-capture-image-windows-server/SysprepStopped.png
[Capture an image of the virtual machine]: ./media/virtual-machines-windows-classic-capture-image/CaptureVM.png
[Enter the image name]: ./media/virtual-machines-capture-image-windows-server/Capture.png
[Image capture successful]: ./media/virtual-machines-capture-image-windows-server/CaptureSuccess.png
[Use the captured image]: ./media/virtual-machines-capture-image-windows-server/MyImagesWindows.png
