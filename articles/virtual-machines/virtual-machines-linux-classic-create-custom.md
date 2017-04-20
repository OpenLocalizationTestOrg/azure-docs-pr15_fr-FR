<properties
    pageTitle="Créer une machine virtuelle Linux | Microsoft Azure"
    description="Apprenez à créer une machine virtuelle personnalisée avec le modèle de déploiement classique exécutant le système d’exploitation Linux."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/23/2016"
    ms.author="iainfou"/>

# <a name="how-to-create-a-custom-linux-vm"></a>Comment créer une Linux personnalisée machine virtuelle

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Pour la version du Gestionnaire de ressources, consultez [ici](virtual-machines-linux-create-cli-complete.md).

Cette rubrique décrit comment créer une personnalisé machine virtuelle () avec l’infrastructure du langage commun Azure à l’aide du modèle de déploiement classique. Nous utilisons une image Linux à partir des **IMAGES** disponibles sur Azure. Les commandes de l’infrastructure du langage commun Azure proposent les options de configuration suivantes, entre autres :

- Connexion de la machine virtuelle à un réseau virtuel
- Ajout de la machine virtuelle à un service cloud existant
- Ajout de la machine virtuelle à un compte de stockage existant
- Ajout de la machine virtuelle à un ensemble de disponibilité ou un emplacement

> [AZURE.IMPORTANT] Si vous souhaitez que votre machine virtuelle pour utiliser un réseau virtuel afin que vous pouvez vous connecter directement par nom d’hôte ou configurer des connexions entre local, vérifiez que vous spécifiez le réseau virtuel lorsque vous créez la machine virtuelle. Une machine virtuelle peut être configurée pour participer à un réseau virtuel uniquement lorsque vous créez la machine virtuelle. Pour plus d’informations sur les réseaux virtuels, voir [Vue d’ensemble du réseau virtuel Azure](http://go.microsoft.com/fwlink/p/?LinkID=294063).


## <a name="how-to-create-a-linux-virtual-machine-using-the-classic-deployment-model"></a>Comment créer une machine virtuelle Linux à l’aide du modèle de déploiement classique

[AZURE.INCLUDE [virtual-machines-create-LinuxVM](../../includes/virtual-machines-create-linuxvm.md)]
