<properties
    pageTitle="Abonnement Microsoft Azure et limites de Service, les Quotas et les contraintes"
    description="Fournit une liste d’abonnement Azure courantes et limites de service, les quotas et les contraintes. Cela inclut des informations sur la façon d’augmenter les limites ainsi que des valeurs maximales."
    services=""
    documentationCenter=""
    authors="rothja"
    manager="jeffreyg"
    editor=""
    tags="billing"
    />

<tags
    ms.service="billing"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/29/2016"
    ms.author="btardif"/>

# <a name="azure-subscription-and-service-limits-quotas-and-constraints"></a>Abonnement Azure et limites de service, les quotas et les contraintes

## <a name="overview"></a>Vue d’ensemble

Ce document spécifie certaines limites Microsoft Azure plus courants. Cela ne comprend pas actuellement tous les services Azure. Dans le temps, ces limites sont développés et mis à jour pour couvrir plus de la plateforme.

Visitez le site [Azure tarifs présentation](https://azure.microsoft.com/pricing/) pour en savoir plus sur les tarifs Azure. Là, vous pouvez estimer les coûts à l’aide de la [Calculatrice de tarification](https://azure.microsoft.com/pricing/calculator/) ou en accédant à la page Détails de la tarification d’un service (par exemple, [Machines virtuelles Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows)).

> [AZURE.NOTE] Si vous souhaitez augmenter la limite au-dessus de la **Limite par défaut**, vous pouvez [Ouvrir une demande de support client en ligne sans frais](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). Les limites ne peut pas être élevés au-dessus de la valeur **Limite maximale** dans les tableaux ci-dessous. S’il n’existe aucune colonne **Limite maximale** , la ressource spécifiée ne comporte pas les limites de variables.

## <a name="limits-and-the-azure-resource-manager"></a>Spécifications et limites du Gestionnaire de ressources Azure

Il est désormais possible combiner plusieurs ressources Azure dans un seul groupe de ressources Azure. Lorsque vous utilisez des groupes de ressources, limites auparavant globales gérés localement au niveau régional avec le Gestionnaire de ressources Azure. Pour plus d’informations sur les groupes de ressources Azure, voir [vue d’ensemble du Gestionnaire de ressources Azure](azure-resource-manager/resource-group-overview.md).

Dans les limites ci-dessous, une nouvelle table a été ajoutée pour refléter les différences dans les limites lorsque vous utilisez le Gestionnaire de ressources Azure. Par exemple, est une table de **La limite d’abonnements** et une table de **La limite d’abonnements - Azure le Gestionnaire de ressources** . Lorsqu’une limite s’applique à ces deux scénarios, il ne s’affiche dans la première table. Sauf indication contraire, les limites sont globales dans toutes les régions.

> [AZURE.NOTE] Il est important de souligner que les quotas de ressources de groupes de ressources Azure sont accessibles par votre abonnement par région et pas par abonnement, comme les quotas de gestion du service. Nous allons utiliser les quotas de core par exemple. Si vous avez besoin demander une augmentation des quotas avec prise en charge des cœurs, vous devez décider combien cœurs que vous voulez utiliser dans les régions et apportez une demande des quotas de groupe de ressources Azure core spécifique pour les montants et les zones de votre choix. Par conséquent, si vous devez utiliser 30 cœurs en Europe ouest pour lancer votre application ; Vous devez demander spécifiquement 30 cœurs en Europe ouest. Mais vous n’aurez pas un quota core augmenter dans n’importe quelle autre région--uniquement ouest Europe disposeront du quota de 30 cœur.
<!-- -->
Par conséquent, vous devez peut s’avérer utile de prendre en compte vous décidez que vos quotas de groupe de ressources Azure doivent être pour votre charge de travail dans n’importe quelle une région et demander montant correspondant dans chaque région dans laquelle vous envisagez de déploiement. Voir [résolution des problèmes de déploiement](./resource-manager-common-deployment-errors.md) de l’aide expériences vos quotas en cours pour des régions spécifiques.

## <a name="service-specific-limits"></a>Limites spécifiques au service

- [Active Directory](#active-directory-limits)
- [Gestion des API](#api-management-limits)
- [Service d’application](#app-service-limits)
- [Passerelle d’application](#application-gateway-limits)
- [Analyse de l’application](#application-insights-limits)
- [Automatisation](#automation-limits)
- [Cache Redis Azure](#azure-redis-cache-limits)
- [RemoteApp Azure](#azure-remoteapp-limits)
- [Sauvegarde](#backup-limits)
- [Traitement par lots](#batch-limits)
- [Services BizTalk](#biztalk-services-limits)
- [CDN](#cdn-limits)
- [Services en nuage](#cloud-services-limits)
- [Données par défaut](#data-factory-limits)
- [Données Lake Analytique](#data-lake-analytics-limits)
- [DNS](#dns-limits)
- [DocumentDB](#documentdb-limits)
- [Événement Hubs](#event-hubs-limits)
- [Concentrateur IoT](#iot-hub-limits)
- [Archivage sécurisé clé](#key-vault-limits)
- [Services de support](#media-services-limits)
- [Engagement mobile](#mobile-engagement-limits)
- [Services mobiles](#mobile-services-limits)
- [Surveillance des mots clés](#monitoring-limits)
- [Authentification multifacteur](#multi-factor-authentication)
- [Mise en réseau](#networking-limits)
- [Service de notification de concentrateur](#notification-hub-service-limits)
- [Perspectives opérationnels](#operational-insights-limits)
- [Groupe de ressources](#resource-group-limits)
- [Planificateur](#scheduler-limits)
- [Recherche](#search-limits)
- [Bus des services](#service-bus-limits)
- [Récupération de site](#site-recovery-limits)
- [Base de données SQL](#sql-database-limits)
- [Espace de stockage](#storage-limits)
- [StorSimple système](#storsimple-system-limits)
- [Flux Analytique](#stream-analytics-limits)
- [Abonnement](#subscription-limits)
- [Gestionnaire de trafic](#traffic-manager-limits)
- [Machines virtuelles](#virtual-machines-limits)
- [Machine virtuelle échelle jeux](#virtual-machine-scale-sets-limits)


### <a name="subscription-limits"></a>Limites de l’abonnement
#### <a name="subscription-limits"></a>Limites de l’abonnement
[AZURE.INCLUDE [azure-subscription-limits](../includes/azure-subscription-limits.md)]

#### <a name="subscription-limits---azure-resource-manager"></a>Limite d’abonnements - Azure le Gestionnaire de ressources

Les limites suivantes s’appliquent lorsque vous utilisez le Gestionnaire de ressources Azure et groupes de ressources Azure. Limites qui n’ont pas été modifiées avec le Gestionnaire de ressources Azure ne sont pas indiquées ci-dessous. Reportez-vous au tableau ci-dessus pour ces limites.

Pour plus d’informations sur la gestion des limites sur les demandes de gestionnaire de ressources, afficher [les demandes de la limitation le Gestionnaire de ressources](resource-manager-request-limits.md).

[AZURE.INCLUDE [azure-subscription-limits-azure-resource-manager](../includes/azure-subscription-limits-azure-resource-manager.md)]


### <a name="resource-group-limits"></a>Limites de groupe de ressources

[AZURE.INCLUDE [azure-resource-groups-limits](../includes/azure-resource-groups-limits.md)]

### <a name="virtual-machines-limits"></a>Machines virtuelles limites
#### <a name="virtual-machine-limits"></a>Limites de Machine virtuelle
[AZURE.INCLUDE [azure-virtual-machines-limits](../includes/azure-virtual-machines-limits.md)]


#### <a name="virtual-machines-limits---azure-resource-manager"></a>Machines virtuelles limites - Azure le Gestionnaire de ressources

Les limites suivantes s’appliquent lorsque vous utilisez le Gestionnaire de ressources Azure et groupes de ressources Azure. Limites qui n’ont pas été modifiées avec le Gestionnaire de ressources Azure ne sont pas indiquées ci-dessous. Reportez-vous au tableau ci-dessus pour ces limites.

[AZURE.INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../includes/azure-virtual-machines-limits-azure-resource-manager.md)]

### <a name="virtual-machine-scale-sets-limits"></a>Machine virtuelle échelle définit les limites

[AZURE.INCLUDE [virtual-machine-scale-sets-limits](../includes/azure-virtual-machine-scale-sets-limits.md)]

### <a name="networking-limits"></a>Limites de mise en réseau

[AZURE.INCLUDE [expressroute-limits](../includes/expressroute-limits.md)]

#### <a name="networking-limits"></a>Limites de mise en réseau
[AZURE.INCLUDE [azure-virtual-network-limits](../includes/azure-virtual-network-limits.md)]

#### <a name="application-gateway-limits"></a>Les limites de passerelle d’application

[AZURE.INCLUDE [application-gateway-limits](../includes/application-gateway-limits.md)]

#### <a name="traffic-manager-limits"></a>Limites du Gestionnaire de trafic

[AZURE.INCLUDE [traffic-manager-limits](../includes/traffic-manager-limits.md)]

#### <a name="dns-limits"></a>Limites DNS

[AZURE.INCLUDE [dns-limits](../includes/dns-limits.md)]

### <a name="storage-limits"></a>Limites de stockage

Pour plus d’informations sur les limites de compte de stockage, voir [extensibilité du stockage Azure et des objectifs de Performance](../articles/storage/storage-scalability-targets.md).

#### <a name="storage-service-limits"></a>Limites de stockage de Service

[AZURE.INCLUDE [azure-storage-limits](../includes/azure-storage-limits.md)]

#### <a name="virtual-machine-disk-limits"></a>Limites de disque Machine virtuelle

[AZURE.INCLUDE [azure-storage-limits-vm-disks](../includes/azure-storage-limits-vm-disks.md)]

Pour plus d’informations, consultez [tailles machine virtuelle](../articles/virtual-machines/virtual-machines-linux-sizes.md) .

**Comptes de stockage standard**

[AZURE.INCLUDE [azure-storage-limits-vm-disks-standard](../includes/azure-storage-limits-vm-disks-standard.md)]

**Comptes de stockage Premium**

[AZURE.INCLUDE [azure-storage-limits-vm-disks-premium](../includes/azure-storage-limits-vm-disks-premium.md)]

#### <a name="storage-resource-provider-limits"></a>Limites de stockage de fournisseur de ressources

[AZURE.INCLUDE [azure-storage-limits-azure-resource-manager](../includes/azure-storage-limits-azure-resource-manager.md)]


### <a name="cloud-services-limits"></a>Limites de Services de cloud

[AZURE.INCLUDE [azure-cloud-services-limits](../includes/azure-cloud-services-limits.md)]


### <a name="app-service-limits"></a>Limites de Service d’application
Les limites de Service d’application suivantes incluent des limites pour les applications Web, applications Mobile, API applications et applications logique.

[AZURE.INCLUDE [azure-websites-limits](../includes/azure-websites-limits.md)]

### <a name="scheduler-limits"></a>Limites de planificateur

[AZURE.INCLUDE [scheduler-limits-table](../includes/scheduler-limits-table.md)]

### <a name="batch-limits"></a>Limites de traitement par lots

[AZURE.INCLUDE [azure-batch-limits](../includes/azure-batch-limits.md)]

###<a name="biztalk-services-limits"></a>Limites de Services BizTalk
Le tableau suivant indique les limites pour les Services Biztalk Azure.

[AZURE.INCLUDE [biztalk-services-service-limits](../includes/biztalk-services-service-limits.md)]


### <a name="documentdb-limits"></a>Limites de DocumentDB

[AZURE.INCLUDE [azure-documentdb-limits](../includes/azure-documentdb-limits.md)]

Quotas répertoriés avec un astérisque (*) [peuvent être ajustés en contactant le support Azure](./documentdb/documentdb-increase-limits.md).

### <a name="mobile-engagement-limits"></a>Limites d’Engagement mobile

[AZURE.INCLUDE [azure-mobile-engagement-limits](../includes/azure-mobile-engagement-limits.md)]


### <a name="search-limits"></a>Limites de recherche

Niveaux de tarification déterminer la capacité et les limites de votre service de recherche. Les niveaux comprennent :

- Service de multi-client *Free* , partagé avec d’autres abonnés Azure, destinés d’évaluation et des projets de développement de petite taille.
- *Base* fournit des ressources informatiques dédiés pour charges de travail à une échelle inférieure, avec jusqu'à trois réplicas pour charges de travail requête hautement disponible.
- *Standard (S1, S2, S3, S3 haute densité)* est plus grandes charges de travail. Plusieurs niveaux existent au sein de la couche standard afin que vous puissiez choisir une configuration des ressources pour des scénarios spécifiques.

**Limites par abonnement**

[AZURE.INCLUDE [azure-search-limits-per-subscription](../includes/azure-search-limits-per-subscription.md)]

**Limites par le service de recherche**

[AZURE.INCLUDE [azure-search-limits-per-service](../includes/azure-search-limits-per-service.md)]

Pour des informations plus granulaires sur les autres limites, y compris la taille du document, requêtes par seconde, les clés, les demandes et les réponses, voir [limites de Service de recherche Azure](search/search-limits-quotas-capacity.md).

### <a name="media-services-limits"></a>Limites de Media Services

[AZURE.INCLUDE [azure-mediaservices-limits](../includes/azure-mediaservices-limits.md)]

### <a name="cdn-limits"></a>Limites CDN

[AZURE.INCLUDE [cdn-limits](../includes/cdn-limits.md)]

### <a name="mobile-services-limits"></a>Limites de Services mobiles

[AZURE.INCLUDE [mobile-services-limits](../includes/mobile-services-limits.md)]

### <a name="monitoring-limits"></a>Limites de surveillance

[AZURE.INCLUDE [monitoring-limits](../includes/monitoring-limits.md)]

### <a name="notification-hub-service-limits"></a>Limites de Service de notification de concentrateur

[AZURE.INCLUDE [notification-hub-limits](../includes/notification-hub-limits.md)]

### <a name="event-hubs-limits"></a>Limites de Hubs d’événement

[AZURE.INCLUDE [azure-servicebus-limits](../includes/event-hubs-limits.md)]

### <a name="service-bus-limits"></a>Limites de Bus des services

[AZURE.INCLUDE [azure-servicebus-limits](../includes/service-bus-quotas-table.md)]

### <a name="iot-hub-limits"></a>Limites IoT concentrateur

[AZURE.INCLUDE [azure-iothub-limits](../includes/iot-hub-limits.md)]

### <a name="data-factory-limits"></a>Limites pour les données par défaut

[AZURE.INCLUDE [azure-data-factory-limits](../includes/azure-data-factory-limits.md)]

### <a name="data-lake-analytics-limits"></a>Les limites de données Lake Analytique
[AZURE.INCLUDE [azure-data-lake-analytics-limits](../includes/azure-data-lake-analytics-limits.md)]

### <a name="stream-analytics-limits"></a>Limites de flux Analytique
[AZURE.INCLUDE [stream-analytics-limits-table](../includes/stream-analytics-limits-table.md)]

### <a name="active-directory-limits"></a>Active Directory limite

[AZURE.INCLUDE [AAD-service-limits](../includes/active-directory-service-limits-include.md)]


### <a name="azure-remoteapp-limits"></a>Limites de RemoteApp Azure

[AZURE.INCLUDE [azure-remoteapp-limits](../includes/azure-remoteapp-limits.md)]

### <a name="storsimple-system-limits"></a>Limites du système StorSimple

[AZURE.INCLUDE [storsimple-limits-table](../includes/storsimple-limits-table.md)]


### <a name="operational-insights-limits"></a>Limites de perspectives opérationnelles

[AZURE.INCLUDE [operational-insights-limits](../includes/operational-insights-limits.md)]

### <a name="backup-limits"></a>Limites de sauvegarde

[AZURE.INCLUDE [azure-backup-limits](../includes/azure-backup-limits.md)]

### <a name="site-recovery-limits"></a>Limites de récupération de sites

[AZURE.INCLUDE [site-recovery-limits](../includes/site-recovery-limits.md)]

### <a name="application-insights-limits"></a>Limites d’analyse des applications

[AZURE.INCLUDE [application-insights-limits](../includes/application-insights-limits.md)]

### <a name="api-management-limits"></a>Limites de gestion de l’API

[AZURE.INCLUDE [api-management-service-limits](../includes/api-management-service-limits.md)]

### <a name="azure-redis-cache-limits"></a>Limites de Cache Redis Azure

[AZURE.INCLUDE [redis-cache-service-limits](../includes/redis-cache-service-limits.md)]

### <a name="key-vault-limits"></a>Limites de l’archivage sécurisé clé

[AZURE.INCLUDE [key-vault-limits](../includes/key-vault-limits.md)]

### <a name="multi-factor-authentication"></a>Authentification multifacteur
[AZURE.INCLUDE [azure-mfa-service-limits](../includes/azure-mfa-service-limits.md)]

### <a name="automation-limits"></a>Limites d’Automation
[AZURE.INCLUDE [automation-limits](../includes/azure-automation-service-limits.md)]

### <a name="sql-database-limits"></a>Limites de la base de données SQL

Limites de la base de données SQL, voir [Limites de ressources de base de données SQL](sql-database/sql-database-resource-limits.md).

## <a name="see-also"></a>Voir aussi

[Présentation des augmentations et limites Azure](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)

[Machine virtuelle et les tailles de Service Cloud pour Azure](virtual-machines/virtual-machines-linux-sizes.md)

[Taille des Services en nuage](cloud-services/cloud-services-sizes-specs.md)
