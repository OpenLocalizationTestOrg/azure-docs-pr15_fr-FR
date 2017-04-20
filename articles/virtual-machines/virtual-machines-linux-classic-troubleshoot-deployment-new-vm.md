<properties
   pageTitle="Résoudre les problèmes de déploiement Linux VM classique | Microsoft Azure"
   description="Résoudre les problèmes de déploiement classique lorsque vous créez une nouvelle machine virtuelle Linux dans Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="JiangChen79"
   manager="felixwu"
   editor=""
   tags="top-support-issue"/>

<tags
  ms.service="virtual-machines-linux"
  ms.workload="na"
  ms.tgt_pltfrm="vm-linux"
  ms.devlang="na"
  ms.topic="article"
  ms.date="09/06/2016"
  ms.author="cjiang"/>

# <a name="troubleshoot-classic-deployment-issues-with-creating-a-new-linux-virtual-machine-in-azure"></a>Résoudre les problèmes de déploiement classique de création d’une nouvelle machine virtuelle Linux dans Azure

[AZURE.INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-selectors](../../includes/virtual-machines-linux-troubleshoot-deployment-new-vm-selectors-include.md)]

[AZURE.INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="collect-audit-logs"></a>Journaux d’audit collecte

Pour démarrer la résolution des problèmes, collecte des journaux d’audit afin d’identifier l’erreur associée à ce problème.

Dans le portail Azure, cliquez sur **Parcourir** > **machines virtuelles** > *votre machine virtuelle Windows* > **paramètres** > **journaux d’Audit**.

[AZURE.INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[AZURE.INCLUDE [virtual-machines-linux-troubleshoot-deployment-new-vm-table](../../includes/virtual-machines-linux-troubleshoot-deployment-new-vm-table.md)]

**Y:** Si le système d’exploitation est Linux generalized, et il est téléchargée et/ou capturé avec le paramètre GRG, il n’y avoir des erreurs. De même, si le système d’exploitation est Linux spécialisés, il est téléchargé et/ou capturé avec le paramètre spécialisé, puis ne se trouve des erreurs.

**Télécharger les erreurs :**

**N<sup>1</sup>:** Si le système d’exploitation est Linux generalized, et il est téléchargé comme spécialisés, vous obtenez une erreur délai de mise à disponibilité, car la machine virtuelle est bloquée au niveau de la mise en service de partage.

**N<sup>2</sup>:** Si le système d’exploitation est Linux spécialisées, et il est téléchargé comme generalized, vous obtenez une erreur d’échec mise à disponibilité parce que la nouvelle machine virtuelle s’exécute avec le nom de l’ordinateur d’origine, nom d’utilisateur et mot de passe.

**Résolution :**

Pour résoudre les deux ces erreurs, téléchargez le disque dur virtuel d’origine, disponible local, avec le même paramètre que pour le système d’exploitation (generalized/spécialisé). Pour télécharger que generalized, n’oubliez pas d’exécuter - deprovision tout d’abord. Pour plus d’informations, voir [créer et télécharger un disque dur virtuel qui contient le système d’exploitation Linux](virtual-machines-linux-classic-create-upload-vhd.md) .

**Capturer des erreurs :**

**N<sup>3</sup>:** Si le système d’exploitation est Linux generalized, et il est capturé en tant que spécialisés, vous obtenez une erreur délai de mise à disponibilité, car la machine virtuelle d’origine n’est pas utilisable car il est marqué comme generalized.

**N<sup>4</sup>:** Si le système d’exploitation est Linux spécialisées, et il est capturé comme generalized, vous obtenez une erreur d’échec mise à disponibilité parce que la nouvelle machine virtuelle s’exécute avec le nom de l’ordinateur d’origine, nom d’utilisateur et mot de passe. En outre, la cellule d’origine machine virtuelle n’est pas utilisable car il est marqué comme spécialisées.

**Résolution :**

Pour résoudre les deux ces erreurs, supprimez l’image en cours à partir du portail et [capturer de nouveau à partir des durs actuels](virtual-machines-linux-classic-capture-image.md) avec le paramètre de même que celle du système d’exploitation (generalized/spécialisé).

## <a name="issue-custom-gallery-marketplace-image-allocation-failure"></a>Problème : Personnalisé / bibliothèque / image marketplace ; Échec d’allocation
Cette erreur se produit dans les situations lors de la nouvelle demande de machine virtuelle est envoyée à un cluster qui n’a pas d’espace libre pour s’adapter à la demande, ou ne reconnaît pas la taille de la mémoire virtuelle demandée. Il n’est pas possible de combiner différentes séries de machines virtuelles dans le même service cloud. Donc si vous souhaitez créer un nouvel ordinateur virtuel d’une taille différente de celle que votre service cloud peut prendre en charge, la demande cluster échouera.

Selon les contraintes du service cloud que vous permet de créer la nouvelle machine virtuelle, vous pouvez rencontrer une erreur provoquée par une des deux situations.

**Entraîner 1 :** Le service en nuage est épinglé à un cluster spécifique, ou il est lié à un groupe affinité et donc épinglée à un cluster spécifique par la conception. Fait nouvelle ressource cluster demande dans la mesure où affinité du groupe sont traités dans le même cluster où se trouvent les ressources existantes. Toutefois, le même cluster peut prendre en charge la taille de mémoire virtuelle demandée ou non ont manque d’espace, ce qui entraîne une erreur d’allocation. C’est vrai que les nouvelles ressources sont créées via un service en nuage ou un service cloud existant.

**Solution 1 :**

- Créer un nouveau service cloud et l’associer à une région ou un réseau virtuel région.
- Créer une nouvelle machine virtuelle dans le nouveau service cloud.
  Si vous obtenez une erreur lorsque vous tentez de créer un nouveau service cloud, recommencez ultérieurement ou modifier une région pour le service cloud.

> [AZURE.IMPORTANT] Si vous avez essayé de créer un nouvel ordinateur virtuel dans un service cloud existant, mais n’a pas pu et deviez créer un nouveau service cloud pour votre nouvelle machine virtuelle, vous pouvez choisir de consolider vos ordinateurs virtuels dans le même service cloud. Pour ce faire, supprimez les ordinateurs virtuels dans le service cloud existant et les capturer de nouveau à partir de leurs disques dans le nouveau service cloud. Toutefois, il est important n’oubliez pas que le nouveau service cloud aura un nouveau nom et VIP, vous devez mettre à jour ces pour toutes les dépendances qui l’utilisent ces informations pour le service cloud existant.

**Entraîner 2 :** Le service en nuage est associé à un réseau virtuel qui est lié à un groupe affinité, afin qu’il est épinglée à un cluster spécifique par la conception. Nouvelle ressource de cluster toutes les demandes dans la mesure où affinité du groupe sont donc essayé dans le même cluster où se trouvent les ressources existantes. Toutefois, le même cluster peut prendre en charge la taille de mémoire virtuelle demandée ou non ont manque d’espace, ce qui entraîne une erreur d’allocation. C’est vrai que les nouvelles ressources sont créées via un service en nuage ou un service cloud existant.

**Solution 2 :**

- Créer un nouveau réseau virtuel régionaux.
- Créer la nouvelle machine virtuelle dans le réseau virtuel.
- [Se connecter votre réseau virtuel existant](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/) vers le nouveau réseau virtuel. Consultez plus d’informations sur les [réseaux régionaux](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/). Vous pouvez également [migrer votre réseau virtuel basés sur affinité du groupe à un réseau virtuel régionaux](https://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/), puis créez la nouvelle machine virtuelle.

## <a name="next-steps"></a>Étapes suivantes
Si vous rencontrez des problèmes lorsque vous démarrez une VM Linux arrêté ou redimensionnez un VM Linux existant dans Azure, consultez [résolution des problèmes de déploiement classique avec le redémarrage ou en redimensionnant une Machine virtuelle Linux existante dans Azure](virtual-machines-linux-classic-restart-resize-error-troubleshooting.md).
