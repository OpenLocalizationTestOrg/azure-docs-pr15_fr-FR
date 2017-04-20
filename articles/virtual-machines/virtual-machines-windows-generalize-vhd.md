<properties
    pageTitle="Généralisez un disque dur virtuel Windows | Microsoft Azure"
    description="Découvrez comment utiliser Sysprep généraliser une machine virtuelle Windows à utiliser avec le modèle de déploiement du Gestionnaire de ressources."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/20/2016"
    ms.author="cynthn"/>
    
    
    
    
# <a name="generalize-a-windows-virtual-machine-using-sysprep"></a>Généralisez une machine virtuelle Windows à l’aide de Sysprep

Cette section vous montre comment generalize votre machine virtuelle Windows à utiliser en tant qu’image. Sysprep supprime toutes vos informations de compte personnel, entre autres et prépare l’ordinateur à utiliser en tant qu’image. Pour plus d’informations sur Sysprep, voir [comment utiliser Sysprep : An Introduction](http://technet.microsoft.com/library/bb457073.aspx).

Vérifiez que les rôles de serveur en cours d’exécution sur l’ordinateur sont prises en charge par Sysprep. Pour plus d’informations, voir [Prise en charge Sysprep des rôles de serveur](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

>[AZURE.IMPORTANT] Si vous exécutez Sysprep avant d’envoyer votre disque dur virtuel à Azure pour la première fois, vérifiez que vous avez [préparé votre machine virtuelle](virtual-machines-windows-prepare-for-upload-vhd-image.md) avant d’exécuter Sysprep. 

1. Se connecter à la machine virtuelle Windows.

2. Ouvrez la fenêtre d’invite de commandes en tant qu’administrateur. Accédez au répertoire **%windir%\system32\sysprep**, puis exécutez `sysprep.exe`.

3. Dans la boîte de dialogue **Outil de préparation système** , sélectionnez **système Entrez de prédéfinies Experience (OOBE)**et assurez-vous que la case à cocher **Generalize** est activée.

4. Dans les **Options d’arrêt**, sélectionnez **Arrêter le système**.

5. Cliquez sur **OK**.

    ![Démarrez Sysprep](./media/virtual-machines-windows-upload-image/sysprepgeneral.png)

6. Lorsque Sysprep s’achève, il arrête la machine virtuelle. 

## <a name="next-steps"></a>Étapes suivantes

- Si la machine virtuelle est en local, vous pouvez maintenant [Télécharger le disque dur virtuel sur Azure](virtual-machines-windows-upload-image.md).
- Si la machine virtuelle est déjà dans Azure, vous pouvez désormais [créer une image à partir de la machine virtuelle GRG](virtual-machines-windows-capture-image.md).