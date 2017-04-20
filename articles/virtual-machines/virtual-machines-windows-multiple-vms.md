<properties
    pageTitle="Créer plusieurs machines virtuelles | Microsoft Azure"
    description="Options permettant de créer plusieurs machines virtuelles sur Windows"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="gbowerman"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="guybo"/>

# <a name="create-multiple-azure-virtual-machines"></a>Créer plusieurs machines virtuelles Azure

Il existe de nombreux scénarios où vous devez créer un grand nombre de machines virtuelles similaires (machines virtuelles). Voici quelques exemples informatique High performance (HPC), analyse de données de grande taille, scalable et souvent sans état serveurs intermédiaire ou serveur principal (par exemple, de serveurs Web) et bases de données distribuées.

Cet article décrit les options disponibles pour créer plusieurs ordinateurs virtuels dans Azure. Ces options dépassent les cas simples dans lequel vous créez manuellement une série de machines virtuelles. Pour créer de machines virtuelles, les processus que vous utilisez habituellement n’échelle bien si vous avez besoin créer plus de quelques machines virtuelles.

Pour créer de machines virtuelles similaires consiste à utiliser la construction Azure le Gestionnaire de ressources de la _ressource effectue une boucle_.

## <a name="resource-loops"></a>Ressource boucles

Ressource boucles sont une forme abrégée syntaxique dans le Gestionnaire de ressources Azure modèles. Ressource boucles peuvent créer un jeu de ressources configurées de façon similaire d’une boucle. Vous pouvez utiliser les ressources boucles pour créer plusieurs comptes de stockage, interfaces réseau ou machines virtuelles. Pour plus d’informations sur les ressources boucles, consultez [créer des machines virtuelles dans disponibilité définit à l’aide de la ressource boucles](https://azure.microsoft.com/documentation/templates/201-vm-copy-index-loops/).

## <a name="challenges-of-scale"></a>Défis d’échelle

Bien que les ressources boucles facilement générer une infrastructure cloud à l’échelle et produire des modèles plus concises, certains défis restent. Par exemple, si vous utilisez une boucle de ressources pour créer des 100 machines virtuelles, vous devez mettre en corrélation réseau interface (NIC) avec des comptes de stockage et machines virtuelles correspondantes. Étant donné que le nombre de machines virtuelles est susceptible d’être différent du nombre de comptes de stockage, vous devrez traiter tailles de boucle ressource différente, trop. Il s’agit aux problèmes, mais la complexité augmente considérablement avec échelle.

Un autre défi se produit lorsque vous avez besoin d’une infrastructure nuances ELASTIQUEMENT. Par exemple, vous souhaiterez une infrastructure échelle automatiquement augmente ou diminue le nombre de machines virtuelles en réponse à la charge de travail. Machines virtuelles ne fournissent un mécanisme intégré pour nombre (horizontale et échelle dans) varie. Si vous redimensionnez dans en supprimant des machines virtuelles, il est difficile de garantir la disponibilité en vérifiant que machines virtuelles sont réparties sur domaines de mise à jour et de pannes.

Enfin, lorsque vous utilisez une boucle de ressource, plusieurs appels à créer des ressources atteindre du tissu sous-jacent. Lorsque plusieurs appels créent des ressources similaires, Azure a une opportunité implicite à améliorer cette conception et optimiser les performances et la fiabilité de déploiement. Il s’agit là _échelle machine virtuelle définit_ se.

## <a name="virtual-machine-scale-sets"></a>Machine virtuelle échelle jeux

Machine virtuelle échelle jeux sont une ressource Azure calculer à déployer et gérer un ensemble de machines virtuelles identiques. Avec tous les ordinateurs virtuels configuré la même, échelle machine virtuelle jeux sont faciles à mettre à l’échelle dans et évoluer. Vous modifiez simplement le nombre de machines virtuelles dans l’ensemble. Vous pouvez également configurer des jeux d’échelle machine virtuelle à l’échelle basée sur les demandes de la charge de travail.

Pour les applications qui doivent s’adapter les ressources de calcul, puis dans, les opérations d’échelle sont implicitement réparties sur domaines défaillance et mise à jour.

Au lieu de corrélation plusieurs ressources telles que des cartes réseau et des ordinateurs virtuels, un ensemble d’échelle machine virtuelle a réseau, stockage, machine virtuelle et propriétés de l’extension que vous pouvez configurer de manière centralisée.

Pour une introduction aux ensembles d’échelle machine virtuelle, reportez-vous à l' [échelle de machine virtuelle définit la page produit](https://azure.microsoft.com/services/virtual-machine-scale-sets/). Pour plus d’informations, accédez à l' [échelle de machines virtuelles définit documentation](https://azure.microsoft.com/documentation/services/virtual-machine-scale-sets/).
