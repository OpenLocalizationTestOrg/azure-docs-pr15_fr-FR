<properties
    pageTitle="Connecter des machines virtuelles Windows dans un service cloud | Microsoft Azure"
    description="Connecter une machine virtuelle Windows créée avec le modèle de déploiement classique à un service cloud Azure ou le réseau virtuel."
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

# <a name="connect-windows-virtual-machines-created-with-the-classic-deployment-model-with-a-virtual-network-or-cloud-service"></a>Connecter des machines virtuelles Windows créés avec le modèle de déploiement classique avec un service de réseau ou sur le cloud virtuel

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

Machines virtuelles Windows créés avec le modèle de déploiement classique sont toujours placés dans un service cloud. Le service cloud se comporte comme un conteneur et fournit un nom DNS public unique, une adresse IP publique et un jeu de points de terminaison pour accéder à la machine virtuelle via Internet. Le service en nuage peut se trouver dans un réseau virtuel, mais qui n’est pas obligatoire. Vous pouvez également [connecter des machines virtuelles de Linux avec un service de réseau ou sur le cloud virtuel](virtual-machines-linux-classic-connect-vms.md).

Si un service cloud ne figure pas dans un réseau virtuel, il s’agit d’un service cloud *autonome* . Les machines virtuelles dans un service cloud autonome peuvent uniquement communiquer avec d’autres machines virtuelles à l’aide de noms DNS publics des autres machines virtuelles et se déplace le trafic via Internet. Si un service cloud se trouve dans un réseau virtuel, les machines virtuelles dans ce service cloud peuvent communiquer avec tous les autres machines virtuelles dans le réseau virtuel sans l’envoyer tout le trafic via Internet.

Si vous placez vos machines virtuelles dans le même service cloud autonome, vous pouvez toujours utiliser l’équilibrage de charge et ensembles de disponibilité. Pour plus d’informations, voir [l’équilibrage de charge machines virtuelles](virtual-machines-windows-load-balance.md) et de [Gérer la disponibilité des machines virtuelles](virtual-machines-windows-manage-availability.md). Toutefois, vous ne pouvez pas organiser les machines virtuelles sur sous-réseaux ou connecter un service cloud autonome à votre réseau local. Voici un exemple :

[AZURE.INCLUDE [virtual-machines-common-classic-connect-vms](../../includes/virtual-machines-common-classic-connect-vms.md)]

## <a name="next-steps"></a>Étapes suivantes

Après avoir créé une machine virtuelle, il est judicieux [d’Ajouter un disque de données](virtual-machines-windows-classic-attach-disk.md) pour vos services et les charges de travail d’avoir un emplacement pour stocker les données. 