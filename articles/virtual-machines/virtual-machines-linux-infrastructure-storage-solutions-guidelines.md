<properties
    pageTitle="Instructions de Solutions de stockage | Microsoft Azure"
    description="En savoir plus sur les instructions de conception et implémentation clées pour déployer des solutions de stockage dans les services d’infrastructure Azure."
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

# <a name="storage-infrastructure-guidelines"></a>Instructions d’infrastructure de stockage

[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-intro](../../includes/virtual-machines-linux-infrastructure-guidelines-intro.md)] 

Cet article se concentre sur la compréhension des besoins de stockage et éléments de conception pour obtenir des performances optimales machine virtuelle ().


## <a name="implementation-guidelines-for-storage"></a>Instructions d’implémentation pour le stockage

Décisions :

- Avez-vous besoin d’utiliser le stockage Standard ou Premium pour votre charge de travail ?
- Avez-vous besoin de répartition des disques pour créer des disques supérieure à 1023 Go ?
- Avez-vous besoin d’agrégat pour optimiser les performances d’e/s pour votre charge de travail ?
- Le jeu de comptes de stockage avez-vous besoin héberger votre charge de travail informatique ou infrastructure ?

Tâches :

- Passez en revue les demandes d’e/s des applications vous déployez et planifiez le numéro et le type de compte de stockage.
- Créer l’ensemble des comptes de stockage à l’aide de votre convention d’appellation. Vous pouvez utiliser l’infrastructure du langage commun Azure ou le portail.


## <a name="storage"></a>Espace de stockage

Stockage Azure est un élément essentiel de déploiement et la gestion des machines virtuelles (machines virtuelles) et les applications. Stockage Azure fournit des services pour le stockage des données de fichier, données non structurées et les messages, et il est également partie de l’infrastructure prenant en charge des machines virtuelles.

Il existe deux types de comptes de stockage disponible pour les ordinateurs virtuels de prise en charge :

- Stockage standard comptes vous permettent d’accéder au stockage d’objets blob (utilisé pour le stockage des disques machine virtuelle Azure), stockage de tables, de stockage de file d’attente et de stockage de fichiers.
- Comptes de [stockage Premium](../storage/storage-premium-storage.md) prise en charge de disque hautes performances et à faible latence pour les charges de travail intensives e/s, par exemple cluster MongoDB Sharded. Stockage Premium prend actuellement en charge des disques machine virtuelle Azure uniquement.

Azure crée des machines virtuelles avec un disque de système d’exploitation, un disque temporaire et zéro, un ou plusieurs disques de données facultatif. Le disque système d’exploitation et les données disques sont des objets BLOB Azure page, tandis que le disque temporaire est stocké localement sur le nœud où se trouve l’ordinateur. Veillez à lors de la conception d’applications d’utiliser uniquement cette disque temporaire pour les données non permanent comme la machine virtuelle peut être migrée entre hôtes pendant un événement de maintenance. Toutes les données stockées sur le disque temporaire sont perdues.

Durabilité et la disponibilité est fourni par l’environnement de stockage Azure sous-jacent pour vous assurer que vos données restent protégées contre les échecs de maintenance ou du matériel non planifiées. Lorsque vous créez votre environnement de stockage Azure, vous pouvez choisir de répliquer stockage machine virtuelle :

- localement dans un centre de données Azure donnée
- sur Azure centres de données au sein d’une région donnée
- sur centres de données Azure entre différentes régions.

Vous trouverez [plus d’informations sur les options de réplication de disponibilité](../storage/storage-introduction.md#replication-for-durability-and-high-availability).

Disques système d’exploitation et données ont une taille maximale de 1023 gigaoctets (Go). La taille maximale d’un objet blob est de 1 024 Go et qui doivent contenir les métadonnées (pied de page) du fichier de disque dur virtuel (un Go est 1024<sup>3</sup> octets). Vous pouvez utiliser la logique Volume Manager (Gestionnaire de volume logique) afin de dépasser cette limite en regroupant disques de données pour présenter des volumes logiques plus 1023 Go à votre ordinateur virtuel.

Il existe certaines limites extensibilité élevées lorsque vous concevez vos déploiements de stockage Azure : pour plus d’informations, consultez [limites d’abonnement et le service Microsoft Azure, les quotas et les contraintes](azure-subscription-service-limits.md#storage-limits) . Voir aussi [cibles de performances et extensibilité élevées stockage Azure](../storage/storage-scalability-targets.md).

Pour le stockage de l’application, vous pouvez stocker les données objet non structurées comme des documents, images, des sauvegardes, les données de configuration, les journaux, etc.. à l’aide de stockage d’objets blob. Plutôt que de votre application enregistrer sur un disque virtuel attaché à la machine virtuelle, l’application peut écrire directement au stockage d’objets blob Azure. Stockage d’objets BLOB offre également la possibilité de [différents niveaux de stockage actif et pouvant être ajoutées](../storage/storage-blob-storage-tiers.md) selon vos besoins de disponibilité et les contraintes de coût.


## <a name="striped-disks"></a>Disques rayées
Outre les qui vous permet de créer des disques supérieure à 1023 Go, dans de nombreux cas, à l’aide de la répartition des disques de données améliore les performances en autorisant plusieurs objets BLOB à l’arrière-plan de l’espace de stockage pour un seul volume. Répartition, des e/s requis pour écrire et lire les données d’un seul disque logique se déroule en parallèle.

Azure impose des limites sur le nombre de disques de données et la quantité de bande passante disponible, selon la taille de la mémoire virtuelle. Pour plus d’informations, voir [formats pour les machines virtuelles](virtual-machines-linux-sizes.md).

Si vous utilisez agrégat pour disques de données Azure, suivez les instructions suivantes :

- Disques de données doivent toujours être la taille maximale (1023 Go).
- Joindre les disques de données maximal autorisés pour la taille de la mémoire virtuelle.
- Utilisez le Gestionnaire de volume logique.
- Évitez d’utiliser des données Azure disque options de mise en cache (mise en cache de stratégie = None).

Pour plus d’informations, voir [Configuration Gestionnaire de volume logique sur un ordinateur virtuel Linux](virtual-machines-linux-configure-lvm.md).


## <a name="multiple-storage-accounts"></a>Plusieurs comptes de stockage

Lorsque vous créez votre environnement de stockage Azure, vous pouvez utiliser plusieurs comptes de stockage comme étant le nombre d’ordinateurs virtuels vous déployez augmente. Cette approche permet de distribuer des e/s sur l’infrastructure de stockage Azure sous-jacente pour maintenir des performances optimales pour vos machines virtuelles et les applications. Lorsque vous créez les applications que vous déployez, envisagez les exigences e/s que chaque machine virtuelle a et solde déconnecter ces machines virtuelles entre des comptes de stockage Azure. Tenter d’éviter de grouper toutes les haute e/s demande machines virtuelles dans à une ou deux comptes de stockage.

Pour plus d’informations sur les fonctionnalités d’e/s des différentes options de stockage Azure et d’autres recommandons maximales, voir [cibles de performances et extensibilité élevées stockage Azure](../storage/storage-scalability-targets.md).


## <a name="next-steps"></a>Étapes suivantes

[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-next-steps](../../includes/virtual-machines-linux-infrastructure-guidelines-next-steps.md)] 