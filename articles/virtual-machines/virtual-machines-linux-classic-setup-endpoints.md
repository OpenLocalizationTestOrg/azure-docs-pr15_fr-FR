<properties
    pageTitle="Configurer des points de terminaison sur un VM Linux classique | Microsoft Azure"
    description="Découvrez comment configurer des points de terminaison pour un Linux VM dans le portail classique Azure pour autoriser la communication avec une machine virtuelle Linux dans Azure"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/13/2016"
    ms.author="cynthn"/>

# <a name="how-to-set-up-endpoints-on-a-linux-classic-virtual-machine-in-azure"></a>Comment configurer des points de terminaison sur une machine virtuelle classique Linux dans Azure

Tous les ordinateurs virtuels Linux que vous créez dans Azure à l’aide du modèle de déploiement classique peuvent communiquer automatiquement sur un canal de réseau privé avec d’autres machines virtuelles dans le même service cloud ou le réseau virtuel. Toutefois, des ordinateurs sur Internet ou d’autres réseaux virtuels nécessitent des points de terminaison afin de rediriger le trafic réseau entrant pour une machine virtuelle. Cet article est également disponible pour les [machines virtuelles Windows](virtual-machines-windows-classic-setup-endpoints.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Dans le modèle de déploiement **Gestionnaire de ressources** , les points de terminaison sont configurés à l’aide de **Groupes de sécurité réseau (NSGs)**. Pour plus d’informations, voir [ouverture de ports et points de terminaison] (virtuel-machines-linux-nsg-quickstart.md).

Lorsque vous créez une machine virtuelle Linux dans le portail classique Azure, un point de terminaison pour SSH (Secure Shell) est généralement automatiquement créé pour vous. Vous pouvez configurer des points de terminaison supplémentaires lors de la création de la machine virtuelle ou par la suite, selon vos besoins.
 

[AZURE.INCLUDE [virtual-machines-common-classic-setup-endpoints](../../includes/virtual-machines-common-classic-setup-endpoints.md)]

## <a name="next-steps"></a>Étapes suivantes

* Vous pouvez également créer un point de terminaison machine virtuelle à l’aide de l' [Interface de ligne Azure](../virtual-machines-command-line-tools.md). Exécutez la commande **créer de point de terminaison machine virtuelle azure** .

* Si vous avez créé une machine virtuelle dans le modèle de déploiement du Gestionnaire de ressources, vous pouvez utiliser l’infrastructure du langage commun Azure en mode de gestionnaire de ressources pour [créer des groupes de sécurité de réseau](../virtual-network/virtual-networks-create-nsg-arm-cli.md) pour contrôler le trafic de la machine virtuelle.