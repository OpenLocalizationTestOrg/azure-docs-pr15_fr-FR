<properties
    pageTitle="Résolution des problèmes d’échecs d’allocation de mémoire virtuelle Windows | Microsoft Azure"
    description="Résoudre les problèmes de répartition lorsque vous créez, redémarrez ou redimensionnez un ordinateur virtuel Windows Azure"
    services="virtual-machines-windows, azure-resource-manager"
    documentationCenter=""
    authors="JiangChen79"
    manager="felixwu"
    editor=""
    tags="top-support-issue,azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="na"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/02/2016"
    ms.author="cjiang"/>

# <a name="troubleshoot-allocation-failures-when-you-create-restart-or-resize-windows-vms-in-azure"></a>Résoudre les problèmes de répartition lorsque vous créez, redémarrez ou redimensionnez machines virtuelles Windows dans Azure

Lorsque vous créez une machine virtuelle, redémarrez arrêtés machines virtuelles (désallouées) ou redimensionnez une machine virtuelle, Microsoft Azure attribue des ressources de calcul à votre abonnement. Que vous receviez erreurs lors de l’exécution de ces opérations--même avant d’atteindre les limites de l’abonnement Azure. Cet article explique les causes de certaines des échecs d’allocation courantes et suggère remediation possible. Les informations peuvent également être utiles lorsque vous planifiez le déploiement de vos services. Vous pouvez également [résoudre les problèmes de répartition lorsque vous créez, redémarrez ou redimensionnez machines virtuelles Linux dans Azure](virtual-machines-linux-allocation-failure.md).

[AZURE.INCLUDE [virtual-machines-common-allocation-failure](../../includes/virtual-machines-common-allocation-failure.md)]
