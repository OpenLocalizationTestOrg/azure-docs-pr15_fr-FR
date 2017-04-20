<properties
   pageTitle="Résoudre les problèmes de déploiement Linux VM-RM | Microsoft Azure"
   description="Résoudre les problèmes de déploiement de directeur des ressources lorsque vous créez une nouvelle machine virtuelle Linux dans Azure"
   services="virtual-machines-linux, azure-resource-manager"
   documentationCenter=""
   authors="JiangChen79"
   manager="felixwu"
   editor=""
   tags="top-support-issue, azure-resource-manager"/>

<tags
  ms.service="virtual-machines-linux"
  ms.workload="na"
  ms.tgt_pltfrm="vm-linux"
  ms.devlang="na"
  ms.topic="article"
  ms.date="09/09/2016"
  ms.author="cjiang"/>

# <a name="troubleshoot-resource-manager-deployment-issues-with-creating-a-new-linux-virtual-machine-in-azure"></a>Résoudre les problèmes de déploiement de gestionnaire de ressources à la création d’une nouvelle machine virtuelle Linux dans Azure

[AZURE.INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="collect-audit-logs"></a>Journaux d’audit collecte

Pour démarrer la résolution des problèmes, collecte des journaux d’audit afin d’identifier l’erreur associée à ce problème. Les liens suivants contiennent des informations détaillées sur le processus à suivre.

[Résoudre les déploiements de groupe de ressources avec Azure portail](../resource-manager-troubleshoot-deployments-portal.md)

[Opérations d’audit avec le Gestionnaire de ressources](../resource-group-audit.md)

[AZURE.INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[AZURE.INCLUDE [virtual-machines-linux-troubleshoot-deployment-new-vm-table](../../includes/virtual-machines-linux-troubleshoot-deployment-new-vm-table.md)]

**Y:** Si le système d’exploitation est Linux generalized, et il est téléchargée et/ou capturé avec le paramètre GRG, il n’y avoir des erreurs. De même, si le système d’exploitation est Linux spécialisés, il est téléchargé et/ou capturé avec le paramètre spécialisé, puis ne se trouve des erreurs.

**Télécharger les erreurs :**

**N<sup>1</sup>:** Si le système d’exploitation est Linux generalized, et il est téléchargé comme spécialisés, vous obtenez une erreur délai de mise à disponibilité, car la machine virtuelle est bloquée au niveau de la mise en service de partage.

**N<sup>2</sup>:** Si le système d’exploitation est Linux spécialisées, et il est téléchargé comme generalized, vous obtenez une erreur d’échec mise à disponibilité parce que la nouvelle machine virtuelle s’exécute avec le nom de l’ordinateur d’origine, nom d’utilisateur et mot de passe.

**Résolution :**

Pour résoudre les deux ces erreurs, téléchargez le disque dur virtuel d’origine, disponible local, avec le même paramètre que pour le système d’exploitation (generalized/spécialisé). Pour télécharger que generalized, n’oubliez pas d’exécuter - deprovision tout d’abord.

**Capturer des erreurs :**

**N<sup>3</sup>:** Si le système d’exploitation est Linux generalized, et il est capturé en tant que spécialisés, vous obtenez une erreur délai de mise à disponibilité, car la machine virtuelle d’origine n’est pas utilisable car il est marqué comme generalized.

**N<sup>4</sup>:** Si le système d’exploitation est Linux spécialisées, et il est capturé comme generalized, vous obtenez une erreur d’échec mise à disponibilité parce que la nouvelle machine virtuelle s’exécute avec le nom de l’ordinateur d’origine, nom d’utilisateur et mot de passe. En outre, la cellule d’origine machine virtuelle n’est pas utilisable car il est marqué comme spécialisées.

**Résolution :**

Pour résoudre les deux ces erreurs, supprimez l’image en cours à partir du portail et [capturer de nouveau à partir des durs actuels](virtual-machines-linux-capture-image.md) avec le paramètre de même que celle du système d’exploitation (generalized/spécialisé).

## <a name="issue-custom-gallery-marketplace-image-allocation-failure"></a>Problème : Personnalisé / bibliothèque / image marketplace ; Échec d’allocation
Cette erreur se produit dans les situations lors de la nouvelle demande de machine virtuelle est épinglée à un cluster qui ne reconnaît pas la taille de la mémoire virtuelle demandée, ou qui n’a pas d’espace libre pour s’adapter à la demande.

**Entraîner 1 :** Le cluster ne reconnaît pas la taille de mémoire virtuelle demandée.

**Solution 1 :**

- Relancer la demande à l’aide d’une plus petite taille de la mémoire virtuelle.
- Si la taille de la machine virtuelle demandée ne peut pas être modifiée :
  - Arrêter tous les ordinateurs virtuels dans l’ensemble de disponibilité.
  Cliquez sur **groupes de ressources** > *votre groupe de ressources* > **ressources** > *définie votre disponibilité* > **Machines virtuelles** > *votre machine virtuelle* > **Arrêter**.
  - Une fois que tous les ordinateurs virtuels arrêté, créez la nouvelle machine virtuelle dans la taille souhaitée.
  - Démarrer la nouvelle machine virtuelle tout d’abord, puis sélectionnez chacune des ordinateurs virtuels arrêtés et cliquez sur **Démarrer**.

**Entraîner 2 :** Le cluster n’a pas de libérer des ressources.

**Solution 2 :**

- Relancer la demande ultérieurement.
- Si la nouvelle machine virtuelle peut faire partie d’un ensemble de disponibilité différentes
  - Créer un nouvel ordinateur virtuel dans une autre disponibilité configurée (dans la même région).
  - Ajouter la nouvelle machine virtuelle au même réseau virtuel.

## <a name="next-steps"></a>Étapes suivantes
Si vous rencontrez des problèmes lorsque vous démarrez une VM Linux arrêté ou redimensionnez un VM Linux existant dans Azure, voir [problèmes de déploiement Gestionnaire de ressources de résoudre les problèmes avec le redémarrage ou en redimensionnant une Machine virtuelle Linux existante dans Azure](virtual-machines-linux-restart-resize-error-troubleshooting.md).
