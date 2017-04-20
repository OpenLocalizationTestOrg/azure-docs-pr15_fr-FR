<properties
    pageTitle="Configurer des points de terminaison sur une machine virtuelle Windows classique | Microsoft Azure"
    description="Apprenez à configurer les points de terminaison pour une machine virtuelle Windows dans le portail classique Azure pour autoriser la communication avec une machine virtuelle Windows dans Azure."
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

# <a name="how-to-set-up-endpoints-on-a-classic-windows-virtual-machine-in-azure"></a>Comment configurer des points de terminaison sur une machine virtuelle Windows classique dans Azure


Machines virtuelles que vous créez dans Azure à l’aide du modèle de déploiement classique peut automatiquement toutes les fenêtres de communiquent sur un privé canal réseau avec d’autres machines virtuelles dans le même service cloud ou le réseau virtuel. Toutefois, des ordinateurs sur Internet ou d’autres réseaux virtuels nécessitent des points de terminaison afin de rediriger le trafic réseau entrant pour une machine virtuelle. Cet article est également disponible pour les [machines virtuelles Linux](virtual-machines-linux-classic-setup-endpoints.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Dans le modèle de déploiement **Gestionnaire de ressources** , les points de terminaison sont configurés à l’aide de **Groupes de sécurité réseau (NSGs)**. Pour plus d’informations, voir [autoriser l’accès externe à votre ordinateur virtuel à l’aide du portail Azure] (virtual-machines-windows-nsg-quickstart-portal.md).

Lorsque vous créez une machine virtuelle Windows dans le portail classique Azure, points de terminaison courants tels que ceux de bureau à distance et Windows PowerShell distante sont généralement automatiquement créées pour vous. Vous pouvez configurer des points de terminaison supplémentaires lors de la création de la machine virtuelle ou par la suite, selon vos besoins.



[AZURE.INCLUDE [virtual-machines-common-classic-setup-endpoints](../../includes/virtual-machines-common-classic-setup-endpoints.md)]

## <a name="next-steps"></a>Étapes suivantes

* Pour utiliser une applet de commande PowerShell Azure pour configurer un point de terminaison machine virtuelle, voir [Ajouter AzureEndpoint](https://msdn.microsoft.com/library/azure/dn495300.aspx).

* Pour utiliser une applet de commande PowerShell Azure pour gérer un et sur un point de terminaison, voir [Gestion des accès aux listes de contrôle () pour les points de terminaison à l’aide de PowerShell](../virtual-network/virtual-networks-acl-powershell.md).

* Si vous avez créé une machine virtuelle dans le modèle de déploiement du Gestionnaire de ressources, vous pouvez utiliser PowerShell Azure pour [créer des groupes de sécurité de réseau](../virtual-network/virtual-networks-create-nsg-arm-ps.md) pour contrôler le trafic de la machine virtuelle.
