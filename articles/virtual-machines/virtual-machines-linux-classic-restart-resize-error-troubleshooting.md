<properties
   pageTitle="Machine virtuelle redémarrer ou en redimensionnant problèmes | Microsoft Azure"
   description="Résoudre les problèmes de déploiement classique de redémarrer ou en redimensionnant une Machine virtuelle Linux existante dans Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="Deland-Han"
   manager="felixwu"
   editor=""
   tags="top-support-issue"/>

<tags
   ms.service="virtual-machines-linux"
   ms.topic="support-article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="required"
   ms.date="09/20/2016"
   ms.devlang="na"
   ms.author="delhan"/>

# <a name="troubleshoot-classic-deployment-issues-with-restarting-or-resizing-an-existing-linux-virtual-machine-in-azure"></a>Résoudre les problèmes de déploiement classique de redémarrer ou en redimensionnant une Machine virtuelle Linux existante dans Azure

> [AZURE.SELECTOR]
- [Classique](../articles/virtual-machines/virtual-machines-linux-classic-restart-resize-error-troubleshooting.md)
- [Gestionnaire de ressources](../articles/virtual-machines/virtual-machines-linux-restart-resize-error-troubleshooting.md)

Lorsque vous essayez de démarrer une Machine virtuelle machine (arrêté Azure virtuelle), ou redimensionner un machine virtuelle Azure existant, l’erreur courant que vous rencontrez est un échec d’allocation. Cette erreur se produit lorsque le cluster ou la région n’a pas de ressources disponibles ou ne reconnaît pas la taille de mémoire virtuelle demandée.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="collect-audit-logs"></a>Journaux d’audit collecte

Pour démarrer la résolution des problèmes, collecte des journaux d’audit afin d’identifier l’erreur associée à ce problème.

Dans le portail Azure, cliquez sur **Parcourir** > **machines virtuelles** > _votre machine virtuelle Linux_ > **paramètres** > **journaux d’Audit**.

## <a name="issue-error-when-starting-a-stopped-vm"></a>Problème : Erreur lors du démarrage d’une machine virtuelle arrêtée

Vous essayez de démarrer une machine virtuelle arrêtée mais obtenez un échec d’allocation.

### <a name="cause"></a>Cause

La demande pour démarrer la machine virtuelle arrêtée doit être démarrée au cluster d’origine qui héberge le service cloud. Toutefois, le cluster n’a pas d’espace disque disponible pour répondre à la demande.

### <a name="resolution"></a>Résolution

* Créer un nouveau service cloud et l’associer à des options une région ou un réseau virtuel région, mais pas un groupe affinité.

* Supprimer la machine virtuelle arrêtée.

* Recréez la machine virtuelle dans le nouveau service cloud à l’aide de disques.

* Démarrer la machine virtuelle recréée.

Si vous obtenez une erreur lorsque vous tentez de créer un nouveau service cloud, recommencez ultérieurement ou modifier une région pour le service cloud.

> [AZURE.IMPORTANT] Le nouveau service cloud aura un nouveau nom et VIP, vous devez modifier ces informations pour toutes les dépendances qui utilisent ces informations pour le service cloud existant.

## <a name="issue-error-when-resizing-an-existing-vm"></a>Problème : Erreur lors du redimensionnement d’un ordinateur virtuel existant

Vous essayez de redimensionner une machine virtuelle existante mais obtenez un échec d’allocation.

### <a name="cause"></a>Cause

La demande pour redimensionner la machine virtuelle doit être démarrée au cluster d’origine qui héberge le service cloud. Toutefois, le cluster ne prend pas en charge la taille de mémoire virtuelle demandée.

### <a name="resolution"></a>Résolution

Réduire la taille de mémoire virtuelle demandée, puis retentez l’opération redimensionnement.

* Cliquez sur **Parcourir toutes les** > **machines virtuelles (classique)** > _votre machine virtuelle_ > **paramètres** > **taille**. Pour plus d’informations, voir [redimensionner la machine virtuelle](https://msdn.microsoft.com/library/dn168976.aspx).

Si elle n’est pas possible de réduire la taille de la mémoire virtuelle, procédez comme suit :

  * Créer un nouveau service cloud, garantissant il est pas lié à un groupe affinité et non associé à un réseau virtuel qui est lié à un groupe affinité.

  * Créer une nouveau, grande taille virtuelle qu’il contient.

Vous pouvez consolider vos ordinateurs virtuels dans le même service cloud. Si votre service cloud existant est associé à un réseau virtuel région, vous pouvez connecter le nouveau service cloud au réseau virtuel existant.

Si le service cloud existant n’est pas associé à un réseau virtuel région, vous devez supprimer les ordinateurs virtuels dans le service cloud existant et recréez-les dans le nouveau service cloud de leurs disques. Toutefois, il est important n’oubliez pas que le nouveau service cloud aura un nouveau nom et VIP, vous devez mettre à jour ces pour toutes les dépendances qui l’utilisent ces informations pour le service cloud existant.

## <a name="next-steps"></a>Étapes suivantes

Si vous rencontrez des problèmes lorsque vous créez une nouvelle VM Linux dans Azure, consultez [résolution des problèmes de déploiement aide pour créer une machine virtuelle Linux dans Azure](../virtual-machines/virtual-machines-linux-troubleshoot-deployment-new-vm.md).
