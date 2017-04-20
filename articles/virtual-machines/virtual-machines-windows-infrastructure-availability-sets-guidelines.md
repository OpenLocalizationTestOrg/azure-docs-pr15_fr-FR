<properties
    pageTitle="Disponibilité définie les indications | Microsoft Azure"
    description="En savoir plus sur les instructions de conception et implémentation clées pour le déploiement des ensembles de disponibilité dans les services d’infrastructure Azure."
    documentationCenter=""
    services="virtual-machines-windows"
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/08/2016"
    ms.author="iainfou"/>

# <a name="availability-sets-guidelines"></a>Disponibilité des jeux d’instructions

[AZURE.INCLUDE [virtual-machines-windows-infrastructure-guidelines-intro](../../includes/virtual-machines-windows-infrastructure-guidelines-intro.md)] 

Cet article se concentre sur comprendre les étapes de planification requis pour les jeux de disponibilité garantir la vos applications accessibles au cours des événements planifiées ou non.

## <a name="implementation-guidelines-for-availability-sets"></a>Instructions d’implémentation pour les jeux de disponibilité

Décisions :

- Jeux de disponibilité combien devez-vous pour les rôles et les niveaux différents dans votre infrastructure d’application ?

Tâches :

- Définir le nombre de machines virtuelles de chaque niveau d’application que vous avez besoin.
- Déterminer si vous avez besoin d’ajuster le nombre de pannes ou mettre à jour des domaines à utiliser pour votre application.
- Définir les ensembles de disponibilité requis à l’aide de votre convention d’appellation et quelles machines virtuelles elles contiennent. Une machine virtuelle ne peut résider dans une disponibilité ensemble. 

## <a name="availability-sets"></a>Jeux de disponibilité

Dans Azure, machines virtuelles (machines virtuelles) peuvent être placés dans un groupement logique appelé un jeu de disponibilité. Lorsque vous créez des machines virtuelles au sein d’un ensemble de disponibilité, la plateforme Azure distribue le positionnement de ces machines virtuelles sur l’infrastructure sous-jacente. Doit comporter un événement de maintenance planifiée pour la plateforme Azure ou un matériel sous-jacent / défaillance de l’infrastructure, l’utilisation des jeux de disponibilité garantit qu’au moins une machine virtuelle reste en cours d’exécution.

Pour obtenir les meilleurs résultats, les applications ne doivent pas résider sur un seul ordinateur virtuel. Un jeu de disponibilité qui contient une seule machine virtuelle ne bénéficier d’une protection depuis des événements planifiées ou la plateforme Azure. Le [SLA Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines) nécessite deux ou plusieurs ordinateurs virtuels au sein d’une disponibilité configuré pour autoriser la distribution des machines virtuelles au sein de l’infrastructure sous-jacente.

L’infrastructure sous-jacente dans Azure est divisé en mettre à jour les domaines et défaillance. Ces domaines sont définies par les hôtes partager un cycle de mises à jour courantes, ou partager similaires infrastructure physique telles que la mise en réseau et power. Azure distribue automatiquement vos ordinateurs virtuels au sein d’une disponibilité définie sur plusieurs domaines pour garantir disponibilité et tolérance de panne. Selon la taille de votre application et le nombre de machines virtuelles au sein d’un ensemble de disponibilité, vous pouvez ajuster le nombre de domaines que vous souhaitez utiliser. Vous pouvez en savoir plus sur la [Gestion des disponibilité et l’utilisation de domaines mise à jour et de pannes](virtual-machines-windows-manage-availability.md).

Lorsque vous créez votre infrastructure d’application, vous devez également planifier les niveaux de l’application que vous utilisez. Machines virtuelles de groupe qui remplissent les mêmes fonctions de disponibilité des jeux, par exemple une disponibilité définie pour vos ordinateurs virtuels frontaux exécutant IIS. Créer une disponibilité distincte définir pour vos ordinateurs virtuels principale qui exécute SQL Server. L’objectif consiste à s’assurer que chaque composant de votre application est protégé par un ensemble de disponibilité et au moins une fois instance reste toujours en cours d’exécution.

Programmes d’équilibrage de charge peuvent être utilisés devant chaque niveau de l’application de travailler en parallèle avec un ensemble de disponibilité et de s’assurer le trafic peut toujours être routé vers une instance en cours d’exécution. Sans un équilibrage de charge, vos ordinateurs virtuels peuvent continuer à s’exécuter dans l’ensemble des événements de maintenance planifiée et non planifiée, mais vos utilisateurs finaux peut ne pas être en mesure de les résoudre si la machine virtuelle principale n’est pas disponible.


## <a name="next-steps"></a>Étapes suivantes
[AZURE.INCLUDE [virtual-machines-windows-infrastructure-guidelines-next-steps](../../includes/virtual-machines-windows-infrastructure-guidelines-next-steps.md)] 