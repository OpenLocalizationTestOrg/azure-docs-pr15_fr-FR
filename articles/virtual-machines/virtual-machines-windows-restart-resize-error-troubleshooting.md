<properties
   pageTitle="Machine virtuelle redémarrer ou en redimensionnant problèmes | Microsoft Azure"
   description="Résoudre les problèmes de déploiement de gestionnaire de ressources avec le redémarrage ou en redimensionnant une Machine virtuelle Windows existante dans Azure"
   services="virtual-machines-windows, azure-resource-manager"
   documentationCenter=""
   authors="Deland-Han"
   manager="felixwu"
   editor=""
   tags="top-support-issue"/>

<tags
   ms.service="virtual-machines-windows"
   ms.topic="support-article"
   ms.tgt_pltfrm="vm-windows"
   ms.devlang="na"
   ms.workload="required"
   ms.date="09/09/2016"
   ms.author="delhan"/>

# <a name="troubleshoot-resource-manager-deployment-issues-with-restarting-or-resizing-an-existing-windows-virtual-machine-in-azure"></a>Résoudre les problèmes de déploiement de gestionnaire de ressources avec le redémarrage ou en redimensionnant une Machine virtuelle Windows existante dans Azure

Lorsque vous essayez de démarrer une Machine virtuelle machine (arrêté Azure virtuelle), ou redimensionner un machine virtuelle Azure existant, l’erreur courant que vous rencontrez est un échec d’allocation. Cette erreur se produit lorsque le cluster ou la région n’a pas de ressources disponibles ou ne reconnaît pas la taille de mémoire virtuelle demandée.

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="collect-audit-logs"></a>Journaux d’audit collecte

Pour démarrer la résolution des problèmes, collecte des journaux d’audit afin d’identifier l’erreur associée à ce problème. Les liens suivants contiennent des informations détaillées sur le processus :

[Dépannage des déploiements de groupe de ressources avec Azure portail](../resource-manager-troubleshoot-deployments-portal.md)

[Opérations d’audit avec le Gestionnaire de ressources](../resource-group-audit.md)

## <a name="issue-error-when-starting-a-stopped-vm"></a>Problème : Erreur lors du démarrage d’une machine virtuelle arrêtée

Vous essayez de démarrer une machine virtuelle arrêtée mais obtenez un échec d’allocation.

### <a name="cause"></a>Cause

La demande pour démarrer la machine virtuelle arrêtée doit être démarrée au cluster d’origine qui héberge le service cloud. Toutefois, le cluster n’a pas d’espace disque disponible pour répondre à la demande.

### <a name="resolution"></a>Résolution

*   Arrêter tous les ordinateurs virtuels dans l’ensemble de disponibilité, puis redémarrez chaque machine virtuelle.

  1. Cliquez sur **groupes de ressources** > _votre groupe de ressources_ > **ressources** > _définie votre disponibilité_ > **Machines virtuelles** > _votre machine virtuelle_ > **Arrêter**.

  2. Une fois tous les ordinateurs virtuels arrêter, sélectionnez chacune des ordinateurs virtuels arrêtés et cliquez sur Démarrer.

*   Relancer la demande de redémarrer ultérieurement.

## <a name="issue-error-when-resizing-an-existing-vm"></a>Problème : Erreur lors du redimensionnement d’un ordinateur virtuel existant

Vous essayez de redimensionner une machine virtuelle existante mais obtenez un échec d’allocation.

### <a name="cause"></a>Cause

La demande pour redimensionner la machine virtuelle doit être démarrée au cluster d’origine qui héberge le service cloud. Toutefois, le cluster ne prend pas en charge la taille de mémoire virtuelle demandée.

### <a name="resolution"></a>Résolution

* Relancer la demande à l’aide d’une plus petite taille de la mémoire virtuelle.

* Si la taille de la machine virtuelle demandée ne peut pas être modifiée :

  1. Arrêter tous les ordinateurs virtuels dans l’ensemble de disponibilité.

    * Cliquez sur **groupes de ressources** > _votre groupe de ressources_ > **ressources** > _définie votre disponibilité_ > **Machines virtuelles** > _votre machine virtuelle_ > **Arrêter**.

  2. Une fois que tous les ordinateurs virtuels arrêté, redimensionnez la machine virtuelle souhaitée pour une plus grande taille.
  3. Sélectionnez la machine virtuelle redimensionnée et cliquez sur **Démarrer**, puis démarrer et chacun des ordinateurs virtuels arrêtés.

## <a name="next-steps"></a>Étapes suivantes

Si vous rencontrez des problèmes lorsque vous créez une nouvelle machine virtuelle Windows dans Azure, consultez [résolution des problèmes de déploiement aide pour créer une machine virtuelle Windows dans Azure](../virtual-machines/virtual-machines-windows-troubleshoot-deployment-new-vm.md).
