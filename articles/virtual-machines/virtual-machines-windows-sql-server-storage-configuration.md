<properties
    pageTitle="Configuration du stockage pour les machines virtuelles SQL Server | Microsoft Azure"
    description="Cette rubrique décrit comment Azure configure le stockage pour les machines virtuelles SQL Server pendant la mise en service (modèle de déploiement Gestionnaire de ressources). Il explique également comment vous pouvez configurer le stockage pour vos ordinateurs existants SQL Server virtuels."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="ninarn"
    manager="jhubbard"    
    tags="azure-resource-manager"/>
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="08/04/2016"
    ms.author="ninarn" />

# <a name="storage-configuration-for-sql-server-vms"></a>Configuration du stockage pour les machines virtuelles SQL Server

Lorsque vous configurez une image de machine virtuelle SQL Server dans Azure, le portail vous permet d’automatiser la configuration de votre espace de stockage. Cela inclut joindre stockage de la machine virtuelle, rendre ce stockage accessible à SQL Server et à le configurer pour optimiser vos exigences de performances spécifiques.

Cette rubrique explique comment Azure configure stockage pour vos ordinateurs virtuels serveur SQL pendant la mise en service et pour machines virtuelles existantes. Cette configuration est basée sur les [meilleures pratiques de performances](virtual-machines-windows-sql-performance.md) pour les machines virtuelles Azure qui exécute SQL Server.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modèle de déploiement classique.

## <a name="prerequisites"></a>Conditions préalables
Pour utiliser les paramètres de configuration de stockage automatisé, votre machine virtuelle requiert les caractéristiques suivantes :

- Configuration à l’aide d’une [image de la galerie SQL Server](virtual-machines-windows-sql-server-iaas-overview.md#option-1-deploy-a-sql-vm-per-minute-licensing).
- Utilise le [modèle de déploiement Gestionnaire de ressources](../resource-manager-deployment-model.md).
- Utilise [le stockage Premium](../storage/storage-premium-storage.md).

## <a name="new-vms"></a>Nouvelles machines virtuelles
Les sections suivantes décrivent comment configurer le stockage de nouvelles machines virtuelles SQL Server.

### <a name="azure-portal"></a>Portail Azure
Lors de la configuration d’une machine virtuelle Azure à l’aide d’une photo de la galerie de SQL Server, vous pouvez choisir de configurer automatiquement l’espace de stockage pour votre nouvelle machine virtuelle. Vous spécifiez la taille de stockage, les limites de performances et type de charge de travail. La capture d’écran suivante montre la carte de configuration de stockage utilisé dans le cadre SQL VM mise en service.

![Configuration du stockage machine virtuelle SQL Server pendant la mise en service](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-provisioning.png)

En fonction de votre choix, Azure effectue les tâches de configuration de stockage suivantes après la création de la machine virtuelle :

- Crée et attache disques de données de stockage premium sur l’ordinateur virtuel.
- Configure les disques de données pour être accessibles à SQL Server.
- Configure les disques de données dans un pool de stockage en fonction des taille et performances (sorties par et débit) critères spécifiés.
- Associe le pool de stockage à un nouveau lecteur sur l’ordinateur virtuel.
- Optimise ce nouveau lecteur selon votre type de charge de travail spécifiée (Data Warehouse, Transactional traitement ou général).

Pour plus d’informations sur la façon dont Azure configure les paramètres de stockage, voir la [section configuration de stockage](#storage-configuration). Pour une procédure pas à pas complète de la création d’une machine virtuelle de SQL Server dans le portail Azure, consultez [le didacticiel mise en service](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="resource-manage-templates"></a>Gérer les modèles de ressources
Si vous utilisez les modèles de gestionnaire de ressources suivants, deux disques de données premium sont joints par défaut, sans aucune configuration de pool de stockage. Toutefois, vous pouvez personnaliser ces modèles pour modifier le nombre de disques de données premium liés à la machine virtuelle.

- [Créer des machines virtuelles avec des sauvegardes automatiques](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autobackup)
- [Créer des machines virtuelles avec correction automatique](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autopatching)
- [Créer des machines virtuelles avec l’intégration AKV](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-keyvault)

## <a name="existing-vms"></a>Machines virtuelles existantes
Pour le machines virtuelles existantes SQL Server, vous pouvez modifier certains paramètres de stockage dans le portail Azure. Sélectionnez votre machine virtuelle, accédez à la zone Paramètres, puis de Configuration SQL Server. La carte de Configuration SQL Server affiche l’utilisation actuelle du stockage de votre ordinateur virtuel. Tous les lecteurs qui existent sur votre ordinateur virtuel sont affichés dans ce graphique. Pour chaque lecteur, l’espace de stockage affiche de quatre sections :

- Données SQL
- Journal SQL
- Autres (stockage non SQL)
- Disponible

![Configurer le stockage pour machine virtuelle SQL Server existante](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-existing.png)

Pour configurer le stockage pour ajouter un nouveau lecteur ou étendre un lecteur existant, cliquez sur le lien Modifier au-dessus du graphique.

Les options de configuration que vous voyez varie selon que vous avez utilisé cette fonctionnalité avant. Lorsque vous utilisez pour la première fois, vous pouvez spécifier vos besoins de stockage pour un nouveau lecteur. Si vous avez utilisé cette fonctionnalité pour créer un lecteur, vous pouvez choisir d’étendre le stockage de ses.

### <a name="use-for-the-first-time"></a>Utiliser pour la première fois
Si elle est la première fois à l’aide de cette fonctionnalité, vous pouvez spécifier les limites de taille et performances de stockage pour un nouveau lecteur. Cette expérience est semblable à ce que vous voyez au moment de la mise en service. La différence principale est que vous n’êtes pas autorisé à spécifier le type de charge de travail. Cette restriction empêche interrompre toute configuration SQL Server existante sur l’ordinateur virtuel.

![Configurer les curseurs de stockage SQL Server](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-usage-sliders.png)

Azure crée un nouveau lecteur selon vos propres critères. Dans ce scénario, Azure effectue les tâches de configuration de stockage suivantes :

- Crée et attache disques de données de stockage premium sur l’ordinateur virtuel.
- Configure les disques de données pour être accessibles à SQL Server.
- Configure les disques de données dans un pool de stockage en fonction des taille et performances (sorties par et débit) critères spécifiés.
- Associe le pool de stockage à un nouveau lecteur sur l’ordinateur virtuel.

Pour plus d’informations sur la façon dont Azure configure les paramètres de stockage, voir la [section configuration de stockage](#storage-configuration).

### <a name="add-a-new-drive"></a>Ajouter un nouveau lecteur
Si vous avez déjà configuré le stockage sur votre machine virtuelle de SQL Server, augmentation du stockage affiche deux nouvelles options. La première option consiste à ajouter un nouveau lecteur, ce qui peut augmenter le niveau de performance de votre ordinateur virtuel.

![Ajouter un nouveau lecteur à une VM SQL](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-add-new-drive.png)

Toutefois, après avoir ajouté le lecteur, vous devez effectuer une configuration manuelle complémentaire pour atteindre l’amélioration des performances.

### <a name="extend-the-drive"></a>Étendre le lecteur
L’option autres pour développer stockage est d’étendre le lecteur existant. Cette option permet d’augmenter la capacité de stockage pour votre lecteur, mais elle n’améliore pas les performances. Avec des pools de stockage, vous ne pouvez pas modifier le nombre de colonnes après avoir créé le pool de stockage. Le nombre de colonnes détermine le nombre de d’écriture en parallèle, lesquelles peuvent être réparties sur les disques de données. Par conséquent, les disques de données ajoutée ne peut pas améliorer les performances. Ils ne peuvent fournir davantage de stockage pour les données en cours d’écriture. Cette limitation signifie également que, lors de l’extension du lecteur, le nombre de colonnes détermine le nombre minimal de disques de données que vous pouvez ajouter. Donc si vous créez un pool de stockage avec des quatre données disques, le nombre de colonnes est également quatre. Chaque fois que vous étirez le stockage, vous devez ajouter des disques de données au moins quatre.

![Étendre un lecteur pour un VM SQL](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-extend-a-drive.png)

## <a name="storage-configuration"></a>Configuration de stockage
Cette section fournit une référence afin que les modifications de configuration de stockage Azure effectue automatiquement pendant la configuration dans le portail Azure ou SQL VM mise en service.

- Si vous avez sélectionné moins de deux to de stockage pour votre machine virtuelle, Azure ne crée pas un pool de stockage.
- Si vous avez sélectionné au moins deux to de stockage pour votre machine virtuelle, Azure configure un pool de stockage. La section suivante de cette rubrique fournit les informations de la configuration du pool de stockage.
- Configuration automatique du stockage toujours utilise des disques de données de [stockage premium](../storage/storage-premium-storage.md) P30. Par conséquent, il existe un mappage 1:1 entre votre nombre de to sélectionné et le nombre de données des disques connectés à votre ordinateur virtuel.

Informations de tarification, consultez la page [tarifs de stockage](https://azure.microsoft.com/pricing/details/storage) sous l’onglet **Stockage de disque** .

### <a name="creation-of-the-storage-pool"></a>Création du pool de stockage
Azure utilise les paramètres suivants pour créer le pool de stockage sur machines virtuelles SQL Server.

| Paramètre | Valeur |
|-----|-----|
| Taille de répartition  | 256 Ko (Data Warehouse) ; 64 Ko (transactions) |
| Tailles de disque | 1 to |
| Cache | Lecture |
| Taille d’allocation | Taille d’unité d’allocation NTFS 64 Ko |
| Initialisation instantanée des fichiers | Activé |
| Verrouiller des pages en mémoire | Activé |
| Récupération | Récupération simple (sans résilience) |
| Nombre de colonnes | Nombre de données disques<sup>1</sup> |
| Emplacement TempDB | Stockées sur disques de données<sup>2</sup> |

<sup>1</sup> une fois le pool de stockage est créé, vous ne pouvez pas modifier le nombre de colonnes dans le pool de stockage.

<sup>2</sup> ce paramètre s’applique uniquement à la première lecteur que vous créez à l’aide de la fonctionnalité de configuration de stockage.

## <a name="workload-optimization-settings"></a>Paramètres d’optimisation de la charge de travail
Le tableau suivant décrit les options de type de la charge de trois travail disponibles et leurs optimisations correspondantes :

| Type de charge de travail | Description | Optimisations |
|-----|-----|-----|
| **Général** | Paramètre par défaut qui prend en charge la plupart des charges de travail | Aucun |
| **Traitement des transactions** | Optimise le stockage de charges de travail OLTP classique de la base de données | Indicateur de suivi 1117<br/>Indicateur de suivi 1118 |
| **Stockage des données** | Optimise le stockage de charges de travail d’analyse et de création de rapports | Indicateur de suivi 610<br/>Indicateur de suivi 1117 |

>[AZURE.NOTE] Vous ne pouvez spécifier le type de charge de travail lorsque vous configurez une machine virtuelle SQL en le sélectionnant dans l’étape de configuration de stockage.

## <a name="next-steps"></a>Étapes suivantes
D’autres rubriques liés à l’exécution de SQL Server dans Azure machines virtuelles, consultez [SQL Server Azure machines virtuelles en fonctionnement](virtual-machines-windows-sql-server-iaas-overview.md).
