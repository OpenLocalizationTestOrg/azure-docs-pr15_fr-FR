<properties
    pageTitle="Instructions de Machines virtuelles Linux | Microsoft Azure"
    description="En savoir plus sur les instructions de conception et implémentation clées de déploiement d’ordinateurs virtuels Linux dans Azure"
    documentationCenter=""
    services="virtual-machines-linux"
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/08/2016"
    ms.author="iainfou"/>

# <a name="virtual-machines-guidelines"></a>Machines virtuelles instructions

[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-intro](../../includes/virtual-machines-linux-infrastructure-guidelines-intro.md)] 

Cet article se concentre sur la compréhension de planification étapes requises pour créer et gérer des machines virtuelles (machines virtuelles) au sein de votre environnement Azure.

## <a name="implementation-guidelines-for-vms"></a>Instructions d’implémentation pour les machines virtuelles
Décisions :

- Machines virtuelles combien vous avez besoin pour divers niveaux de l’application et les composants de votre infrastructure ?
- Les ressources du processeur et la mémoire doit-il chaque machine virtuelle, et quelles sont les conditions de stockage ?

Tâches :

- Définir les charges de travail pour votre application et les ressources que requièrent les ordinateurs virtuels.
- Aligner les besoins en ressources pour chaque machine virtuelle avec le type de stockage et de taille de la mémoire virtuelle approprié.
- Définissez vos groupes de ressources pour les différents niveaux et les composants de votre infrastructure.
- Définir votre convention d’appellation machine virtuelle.
- Créer vos ordinateurs virtuels à l’aide de la Azure infrastructure du langage commun, web portail, ou avec les modèles de gestionnaire de ressources.

## <a name="virtual-machines"></a>Machines virtuelles

Un des principaux composants au sein de votre environnement Azure est susceptible de machines virtuelles. Il s’agit de l’endroit où vous exécutez vos applications, les bases de données, les services d’authentification, les etc..

Il est important de comprendre les [différentes tailles de machine virtuelle](virtual-machines-linux-sizes.md) à dimensionner correctement votre environnement de performances et de la perspective de coût. Si vos ordinateurs virtuels n’ont pas suffisamment cœurs de processeur ou la mémoire, les performances de votre application sont affectées quelle que soit la manière dont il est conçu et développé. Passez en revue les charges de travail suggérés pour chaque série machine virtuelle comme point de départ lorsque vous décidez de la taille machine virtuelle à utiliser pour chaque composant dans votre infrastructure. Vous pouvez [Modifier la taille d’un ordinateur virtuel](virtual-machines-linux-change-vm-size.md) après le déploiement.

Stockage joue un rôle clé performances machine virtuelle. Vous pouvez utiliser stockage Standard, qui utilise des disques en rotation régulière, ou stockage Premium pour charges e/s élevées et des performances optimales, qui utilisent des disques SSD. Comme avec la taille de la mémoire virtuelle, des coûts sont considérations à sélectionner le support de stockage. Vous pouvez consulter l' [article de stockage infrastructure instructions](virtual-machines-linux-infrastructure-storage-solutions-guidelines.md) pour mieux comprendre comment concevoir stockage approprié pour optimiser les performances de vos ordinateurs virtuels.


## <a name="resource-groups"></a>Groupes de ressources
Composants tels que des machines virtuelles sont logiquement regroupés pour faciliter la gestion et la maintenance à l’aide de [Groupes de ressources Azure](../azure-resource-manager/resource-group-overview.md). À l’aide de groupes de ressources, vous pouvez créer, gérer et surveiller toutes les ressources qui composent une application donnée. Vous pouvez également implémenter [des contrôles d’accès basé sur un rôle](../active-directory/role-based-access-control-what-is.md) pour accorder l’accès à d’autres personnes au sein de votre équipe pour que les ressources que dont ils ont besoin. Prendre un certain temps planifiez vos groupes de ressources et les attributions de rôle. Il existe différentes approches réellement de conception et mise en œuvre de groupes de ressources, veillez donc à lire [l’article instructions de groupes de ressources](virtual-machines-linux-infrastructure-resource-groups-guidelines.md) pour mieux comprendre la meilleure façon pour générer vos ordinateurs virtuels.


## <a name="templates"></a>Modèles 
Vous pouvez créer des modèles, défini par les fichiers JSON déclaratives, pour créer vos ordinateurs virtuels. Modèles généralement également créer le stockage requis, mise en réseau, interfaces réseau, adresses IP, etc. ainsi que les ordinateurs virtuels eux-mêmes. Vous pouvez utiliser des modèles pour créer des environnements cohérentes et reproduire pour le développement et test à des fins de répliquer facilement les environnements de production et vice versa. Vous pouvez en savoir plus sur [Création et l’utilisation des modèles](../azure-resource-manager/resource-group-overview.md#template-deployment) pour comprendre comment vous pouvez les utiliser pour créer et déployer vos ordinateurs virtuels.


## <a name="next-steps"></a>Étapes suivantes
[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-next-steps](../../includes/virtual-machines-linux-infrastructure-guidelines-next-steps.md)] 