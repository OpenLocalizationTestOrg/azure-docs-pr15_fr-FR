<properties
    pageTitle="Opération de maintenance planifiée pour les machines virtuelles Windows | Microsoft Azure"
    description="Comprendre la maintenance planifiée quels Azure est et comment il affecte votre machines virtuelles Windows Azure"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="drewm"
    manager="timlt"
    editor=""
    tags="azure-service-management,azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="04/26/2016"
    ms.author="drewm"/>

# <a name="planned-maintenance-for-virtual-machines-in-azure"></a>Maintenance planifiée pour les machines virtuelles dans Azure


Comprendre la maintenance planifiée quels Azure est et comment il peut affecter la disponibilité de vos machines virtuelles Windows. Cet article est également disponible pour les [machines virtuelles Linux](virtual-machines-linux-planned-maintenance.md). 

Cet article fournit un arrière-plan en ce qui concerne le processus de maintenance planifiée Azure. Si vous souhaitez résoudre les problèmes liés redémarré votre ordinateur virtuel, vous pouvez [lire ce billet de blog billet détaillant l’affichage machine virtuelle redémarre les journaux](https://azure.microsoft.com/blog/viewing-vm-reboot-logs/).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


## <a name="why-azure-performs-planned-maintenance"></a>Pourquoi Azure effectue la maintenance planifiée

Microsoft Azure exécute régulièrement mises à jour dans le monde pour améliorer la fiabilité, de performances et sécurité de l’infrastructure hôte qui se trouve sous machines virtuelles. La plupart de ces mises à jour sont effectuées sans impact à votre machines virtuelles ou les Services en nuage, y compris les mises à jour en conservant la quantité de mémoire.

Toutefois, certaines mises à jour nécessitent un redémarrage à vos machines virtuelles pour appliquer les mises à jour requises pour l’infrastructure. Machines virtuelles sont arrêtés pendant que nous correctif de l’infrastructure, puis redémarrage des machines virtuelles.

Notez qu’il existe deux types de maintenance qui peuvent avoir un impact sur la disponibilité de vos machines virtuelles : planifiées et. Cette page décrit la façon dont Microsoft Azure exécute la maintenance planifiée. Pour plus d’informations sur la maintenance planifiée, voir [comprendre planifiée et maintenance planifiée](virtual-machines-windows-manage-availability.md).

[AZURE.INCLUDE [virtual-machines-common-planned-maintenance](../../includes/virtual-machines-common-planned-maintenance.md)]
