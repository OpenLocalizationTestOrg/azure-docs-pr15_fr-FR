<properties 
    pageTitle="Introduction à la couche Azure Redis Cache Premium | Microsoft Azure" 
    description="Découvrez comment créer et gérer persistance Redis, Redis cluster et support VNET pour vos instances de Cache Redis Azure niveau Premium" 
    services="redis-cache" 
    documentationCenter="" 
    authors="steved0x" 
    manager="douge" 
    editor=""/>

<tags 
    ms.service="cache" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="cache-redis" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/15/2016" 
    ms.author="sdanie"/>

# <a name="introduction-to-the-azure-redis-cache-premium-tier"></a>Introduction à la couche Azure Redis Cache Premium
Azure Cache Redis est un cache distribué, géré qui permet de créer des applications hautement scalable et injoignable en fournissant un accès très rapide à vos données. 

Le nouveau niveau Premium est un niveau de prêt l’entreprise, qui inclut toutes les fonctionnalités Standard de couche et plus d’informations, tels que meilleures performances charges de travail plus grandes, sinistre, importer/exporter et sécurité renforcée. Poursuivre la lecture pour en savoir plus sur les fonctionnalités supplémentaires du niveau de cache Premium.

## <a name="better-performance-compared-to-standard-or-basic-tier"></a>Meilleures performances par rapport au niveau Standard ou de base
**Meilleures performances par rapport à norme ou base niveau.** Cache dans la couche Premium est déployé sur du matériel qui a processeurs plus rapides et permet d’améliorer les performances par rapport à la Basic ou couche Standard. Cache de niveau Premium ont un débit supérieur et inférieur latence. 

**Débit pour le même Cache dimensionné est plus élevé dans Premium par rapport à niveau Standard.** Par exemple, le débit un 53 P4 Go (Premium) cache est 250K requêtes par seconde par rapport à 150 K pour C6 (Standard).

Pour plus d’informations sur la taille, le débit et la bande passante avec cache premium, voir [Forum aux questions Azure Redis Cache](cache-faq.md#what-redis-cache-offering-and-size-should-i-use)

## <a name="redis-data-persistence"></a>Redis permanence des données
La couche Premium permet de vous permettent de conserver les cache des données dans un compte de stockage Azure. Toutes les données se trouve dans un cache Basic/Standard uniquement en mémoire. En cas d’infrastructure sous-jacente problèmes il peuvent être perte de données potentielle. Nous recommandons d’utiliser la fonctionnalité persistance Redis des données dans la couche Premium pour accroître la résilience contre la perte de données. Cache Redis Azure propose relationnelle et UNEDE (bientôt disponible) des options dans [Redis persistance](http://redis.io/topics/persistence). 

Pour obtenir des instructions sur la configuration de persistance, voir [comment configurer persistance Premium Azure Redis cache](cache-how-to-premium-persistence.md).

##<a name="redis-cluster"></a>Redis cluster
Si vous souhaitez créer le cache supérieure à 53 Go ou aux données partagé entre plusieurs nœuds Redis, vous pouvez utiliser Redis cluster qui est disponible dans le niveau Premium. Chaque nœud se compose d’une paire de cache principal/réplica gérée par Azure de disponibilité. 

**Redis cluster vous permet de débit et échelle maximale.** Débit augmente de façon linéaire à mesure que vous augmentez le nombre de milieu des fragments (nœuds) dans le cluster. Par exemple. Si vous créez un cluster P4 de 10 milieu des fragments, puis le débit disponible est de 250 Ko * 10 = 2,5 millions de requêtes par seconde. Consultez le [Forum aux questions Azure Redis Cache](cache-faq.md#what-redis-cache-offering-and-size-should-i-use) pour plus d’informations sur la taille, le débit et la bande passante avec cache premium.

Pour commencer à utiliser la mise en cluster, voir [comment configurer cluster Premium Azure Redis cache](cache-how-to-premium-clustering.md).

##<a name="enhanced-security-and-isolation"></a>Isolement et une sécurité renforcée

Cache créé dans la couche Basic ou Standard est accessibles sur internet. Accès au Cache est restreint en fonction de la touche d’accès rapide. Avec la couche Premium vous pouvez davantage vous assurer que seuls les clients au sein d’un réseau spécifié peuvent accéder au Cache. Vous pouvez déployer Redis Cache dans un [Réseau virtuel Azure (VNet)](https://azure.microsoft.com/services/virtual-network/). Vous pouvez utiliser les fonctionnalités des VNet comme sous-réseaux, stratégies de contrôle d’accès, et d’autres fonctionnalités supplémentaires pour limiter l’accès à Redis.

Pour plus d’informations, voir [comment configurer la prise en charge de réseau virtuel Premium Azure Redis cache](cache-how-to-premium-vnet.md).

## <a name="importexport"></a>Importer/exporter

Importation/exportation est une opération de gestion des données Azure Redis Cache qui vous permet d’importer des données dans le Cache Redis Azure ou exporter des données à partir d’Azure Redis Cache par importer et exporter un instantané de base de données de Cache Redis (relationnelle) à partir d’un cache premium vers un blob de page dans un compte de stockage Azure. Cela vous permet de migrer entre différentes instances de Cache Redis Azure ou remplir le cache de données avant des utiliser.

Importation peut servir à transférer Redis compatible relationnelle ou les fichiers à partir de n’importe quel serveur Redis en cours d’exécution dans n’importe quel nuage ou l’environnement, y compris Redis s’exécutant sur n’importe quel fournisseur de nuage tel que Amazon Web Services et d’autres personnes, Windows ou Linux. Importer des données est un moyen facile de créer un cache de données prédéfinie. Pendant le processus d’importation, Azure Redis Cache charge les fichiers relationnelle à partir du stockage Azure en mémoire, puis insère les touches dans le cache.

Exporter vous permet d’exporter les données stockées dans le Cache de Redis Azure à Redis compatible relationnelle ou les fichiers. Vous pouvez utiliser cette fonctionnalité pour déplacer des données à partir d’une instance de Cache Redis Azure vers un autre ou vers un autre serveur Redis. Pendant le processus d’exportation, un fichier temporaire est créé sur l’ordinateur virtuel qui héberge l’instance du serveur Azure Redis Cache et le fichier est téléchargé sur le compte de stockage désigné. Une fois l’opération d’exportation terminée avec soit un état de réussite ou l’échec, le fichier temporaire est supprimé.

Pour plus d’informations, voir [comment importer des données et exporter des données à partir d’Azure Redis Cache](cache-how-to-import-export-data.md).

## <a name="reboot"></a>Redémarrage

La couche premium vous permet de redémarrer un ou plusieurs des nœuds de votre cache à la demande. Cela vous permet de tester votre application pour la résilience en cas de panne. Vous pouvez redémarrer les nœuds suivants.

-   Nœud maître de cache
-   Nœud esclave du cache
-   Nœuds maître et esclave du cache
-   Lorsque vous utilisez un cache premium avec cluster, vous pouvez redémarrer le maître, esclave ou les deux nœuds pour milieu des fragments individuels dans le cache

Pour plus d’informations, voir [redémarrer](cache-administration.md#reboot) et [Forum aux questions sur Redémarrer](cache-administration.md#reboot-faq).

## <a name="schedule-updates"></a>Mises à jour de la planification

La fonctionnalité de mises à jour planifiées vous permet de désigner une période de maintenance pour le cache. Lorsque la fenêtre de maintenance est spécifiée, des mises à jour du serveur Redis sont apportées au cours de cette fenêtre. Pour désigner une période de maintenance, sélectionnez les jours de votre choix, puis spécifiez la maintenance fenêtre Démarrer heure pour chaque jour. Notez que l’horaire de la fenêtre maintenance est au format UTC. 

Pour plus d’informations, voir [planification](cache-administration.md#schedule-updates) et [mises à jour de planification Forum aux questions](cache-administration.md#schedule-updates-faq).

>[AZURE.NOTE] Uniquement Redis serveur mises à jour apportées au cours de la fenêtre de maintenance planifiée. La fenêtre Gestion ne s’applique pas aux mises à jour Azure ou les mises à jour pour le système d’exploitation machine virtuelle.

## <a name="to-scale-to-the-premium-tier"></a>Pour s’adapter à la couche premium

Pour mettre à l’échelle à la couche premium, simplement choisissez une des niveaux premium dans la carte **Modifier tarifs niveau** . Vous pouvez également mettre à l’échelle le cache de résolution au niveau premium à l’aide de PowerShell et infrastructure du langage commun. Pour obtenir des instructions étape par étape, voir [comment échelle Azure Redis Cache](cache-how-to-scale.md) et [Comment faire pour automatiser une opération de mise à l’échelle](cache-how-to-scale.md#how-to-automate-a-scaling-operation).

## <a name="next-steps"></a>Étapes suivantes

Créer un cache et d’Explorer les nouvelles fonctionnalités de niveau premium.

-   [Comment configurer persistance Premium Azure Redis cache](cache-how-to-premium-persistence.md)
-   [Comment configurer la prise en charge de réseau virtuel pour un Cache de Redis Premium Azure](cache-how-to-premium-vnet.md)
-   [Comment faire pour configurer le cluster Premium Azure Redis cache](cache-how-to-premium-clustering.md)
-   [Comment importer et exporter des données à partir d’Azure Redis Cache](cache-how-to-import-export-data.md)
-   [Comment administrer Azure Redis Cache](cache-administration.md)
  

