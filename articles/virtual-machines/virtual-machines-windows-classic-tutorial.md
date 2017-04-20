<properties
    pageTitle="Créer une machine virtuelle dans le portail classique | Microsoft Azure"
    description="Créer une machine virtuelle Windows dans le portail classique Azure."
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
    ms.date="10/18/2016"
    ms.author="cynthn"/>

# <a name="create-a-virtual-machine-running-windows-in-the-azure-classic-portal"></a>Créer une machine virtuelle exécutant Windows dans le portail classique Azure

> [AZURE.SELECTOR]
- [Portail classique Azure](virtual-machines-windows-classic-tutorial.md)
- [PowerShell : Déploiement classique](virtual-machines-windows-classic-create-powershell.md)

<br>

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Découvrez comment [effectuer ces étapes en utilisant le modèle de déploiement du Gestionnaire de ressources](virtual-machines-windows-hero-tutorial.md) à l’aide du **nouveau portail Azure**. 

Ce didacticiel montre comment créer une machine virtuelle Azure (machine virtuelle) exécutant Windows dans le portail classique Azure. Nous allons utiliser une image de Windows Server par exemple, mais qui est une des images de nombreux Qu'azure offre. Notez que vos choix image dépendent de votre abonnement. Par exemple, les images de bureau de Windows peuvent être disponibles pour les abonnés MSDN.

Cette section vous montre comment utiliser l’option **De la galerie** dans le portail classique Azure pour créer la machine virtuelle. Cette option offre davantage d’options configuration que l’option **Création rapide** . Par exemple, si vous souhaitez participer à une machine virtuelle à un réseau virtuel, vous devez utiliser l’option **De la galerie** .

Vous pouvez également créer des machines virtuelles à l’aide de [vos propres images](virtual-machines-windows-classic-createupload-vhd.md). Pour en savoir plus sur cette et d’autres méthodes, voir les [différentes façons de créer une machine virtuelle Windows](virtual-machines-windows-creation-choices.md).



## <a name="video-walkthrough"></a>Procédure pas à pas vidéo

Voici une procédure pas à pas de ce didacticiel.

[AZURE.VIDEO creating-a-windows-vm-on-microsoft-azure-classic-portal]

## <a id="createvirtualmachine"> </a>Créer la machine virtuelle

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-windowsvm.md)]

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [créer une machine virtuelle à l’aide du modèle de déploiement Gestionnaire de ressources](virtual-machines-windows-hero-tutorial.md) dans le nouveau portail Azure. 

- Ouvrez une session sur l’ordinateur virtuel. Pour plus d’informations, voir [Ouvrir une session sur un ordinateur virtuel exécutant Windows Server](virtual-machines-windows-classic-connect-logon.md).

- Connectez un disque pour stocker les données. Vous pouvez joindre à la fois vides disques et qui contiennent des données. Pour plus d’informations, voir [joindre un disque de données pour une machine virtuelle Windows créé avec le modèle de déploiement classique](virtual-machines-windows-classic-attach-disk.md).
