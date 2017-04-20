<properties 
    pageTitle="Comment configurer permanence des données pour un Cache de Redis Premium Azure" 
    description="Découvrez comment configurer et gérer des vos instances de Cache Redis Azure niveau Premium permanence des données" 
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
    ms.date="09/30/2016" 
    ms.author="sdanie"/>

# <a name="how-to-configure-data-persistence-for-a-premium-azure-redis-cache"></a>Comment configurer permanence des données pour un Cache de Redis Premium Azure

Azure Cache Redis comporte des offres cache différent flexibilité lors du choix de la taille de cache et les fonctionnalités, y compris la nouvelle couche Premium.

La couche premium Azure Redis Cache intègre des fonctionnalités telles que cluster, persistance et prise en charge réseau virtuel. Cet article décrit comment configurer persistance dans une instance de Cache Redis Azure premium.

Pour plus d’informations sur d’autres fonctionnalités de cache premium, voir [Introduction à la couche Azure Redis Cache Premium](cache-premium-tier-intro.md).

## <a name="what-is-data-persistence"></a>Qu’est permanence des données ?
Redis persistance permet de conserver les données stockées dans Redis. Vous pouvez également prendre des instantanés et sauvegarder les données, vous pouvez charger en cas d’échec matériel. Il s’agit d’un grand avantage niveau Basic ou Standard où toutes les données sont stockées en mémoire et il peut y avoir perdre des données en cas d’échec où se trouvent des nœuds de Cache vers le bas. 

Cache Redis Azure offre persistance Redis à l’aide du [modèle relationnelle](http://redis.io/topics/persistence), où les données sont stockées dans un compte de stockage Azure. Lors de la persistance est configuré, Azure Redis Cache persiste un instantané du cache Redis dans un format binaire Redis sur le disque basé sur une fréquence de sauvegarde configurable. Si un sinistre se produit qui désactive le cache principal et réplica, le cache est reconstruit à l’aide de l’instantané le plus récent.

Persistance peut être configuré à partir de la carte de **Nouveau Cache Redis** lors de la création de cache et sur la carte de **paramètres** de cache premium existant.

## <a name="create-a-premium-cache"></a>Créer un cache premium

Pour créer un cache et configurer persistance, se connecter au [portail Azure](https://portal.azure.com) et cliquez sur **Nouveau**->**données + stockage**>**Redis Cache**.

![Créer un Cache Redis][redis-cache-new-cache-menu]

Pour configurer persistance, tout d’abord le choix entre le cache **Premium** dans la carte de **Choisir votre niveau de tarification** .

![Choisissez votre niveau de tarification][redis-cache-premium-pricing-tier]

Une fois une prime tarifs couche est sélectionnée, cliquez sur **Redis persistance**.

![Redis persistance][redis-cache-persistence]

Les étapes décrites dans la section suivante décrivent comment configurer persistance Redis sur votre nouveau cache premium. Une fois que Redis persistance est configuré, cliquez sur **créer** pour créer votre cache premium avec une connexion permanente Redis.

## <a name="configure-redis-persistence"></a>Configurer persistance Redis

Redis persistance est configuré sur la carte **Redis permanence des données** . Pour le nouveau cache, cette carte est accessible pendant le processus de création de cache, comme décrit dans la section précédente. Pour cache existant, la carte **Redis permanence des données** est accessible à partir de la carte de **paramètres** pour le cache.

![Redis paramètres][redis-cache-settings]

Pour activer la Redis permanence, cliquez sur **activé** pour activer la sauvegarde relationnelle (base de données Redis). Pour désactiver la persistance Redis sur un cache premium précédemment activés, cliquez sur **désactivé**.

Pour configurer l’intervalle de sauvegarde, sélectionnez une **Fréquence de sauvegarde** dans la liste déroulante. Choix incluent ** **15 Minutes**, **30 minutes**, 60 minutes**, **6 heures**, **12 heures**et **24 heures**. Cet intervalle commence à compter vers le bas une fois l’opération de sauvegarde précédente terminée et lorsqu’il s’écoule une nouvelle sauvegarde est lancée.

Cliquez sur le **Compte de stockage** pour sélectionner le compte de stockage à utiliser, puis sélectionnez soit la **clé primaire** ou **secondaire** à utiliser dans la liste déroulante **Clé de stockage** . Vous devez choisir un compte de stockage dans la même région en tant que le cache, et un compte de **Stockage Premium** est recommandé, car le stockage premium a un débit plus élevé. 

>[AZURE.IMPORTANT] Si la clé de stockage pour votre compte persistance régénérer, vous devez rechoose la clé de votre choix dans la liste déroulante **Clé de stockage** .

![Redis persistance][redis-cache-persistence-selected]

Cliquez sur **OK** pour enregistrer la configuration de persistance.

La prochaine sauvegarde (ou première sauvegarde pour nouveau cache) est lancée une fois que l’intervalle de fréquence de sauvegarde s’écoule.



## <a name="persistence-faq"></a>Persistance Forum aux questions

La liste suivante contient des réponses aux questions fréquemment posées sur persistance Azure Redis Cache.

-   [Puis-je activer persistance sur un cache précédemment créé ?](#can-i-enable-persistence-on-a-previously-created-cache)
-   [Puis-je modifier la fréquence de sauvegarde après avoir créé le cache ?](#can-i-change-the-backup-frequency-after-i-create-the-cache)
-   [Pourquoi si j’utilise une fréquence de sauvegarde de 60 minutes est supérieure à 60 minutes entre les sauvegardes ?](#why-if-i-have-a-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups)
-   [Que se passe-t-il à l’anciens sauvegardes lors d’une nouvelle sauvegarde ?](#what-happens-to-the-old-backups-when-a-new-backup-is-made)
-   [Que se passe-t-il si j’ai mise à l’échelle sur une autre taille et la restauration d’une sauvegarde qui a été effectuée avant l’opération de mise à l’échelle ?](#what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)

### <a name="can-i-enable-persistence-on-a-previously-created-cache"></a>Puis-je activer persistance sur un cache précédemment créé ?

Oui, Redis persistance peut être configuré à la fois lors de la création du cache et cache premium existant.

### <a name="can-i-change-the-backup-frequency-after-i-create-the-cache"></a>Puis-je modifier la fréquence de sauvegarde après avoir créé le cache ?

Oui, vous pouvez modifier la fréquence de sauvegarde sur la carte **Redis permanence des données** . Pour plus d’informations, voir [configurer Redis persistance](#configure-redis-persistence).

### <a name="why-if-i-have-a-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups"></a>Pourquoi si j’utilise une fréquence de sauvegarde de 60 minutes est supérieure à 60 minutes entre les sauvegardes ?

L’intervalle de fréquence de sauvegarde ne démarre pas jusqu'à ce que le processus de sauvegarde précédent est terminée. Si la fréquence de sauvegarde est 60 minutes et dure un processus de sauvegarde 15 minutes pour mener à bien, la sauvegarde suivante ne démarre pas avant 75 minutes après l’heure de début de la sauvegarde précédente.

### <a name="what-happens-to-the-old-backups-when-a-new-backup-is-made"></a>Que se passe-t-il à l’anciens sauvegardes lors d’une nouvelle sauvegarde ?

Toutes les sauvegardes à l’exception de l’option la plus récente sont supprimés automatiquement. Cette suppression n’ait pas lieu immédiatement, mais les sauvegardes plus anciennes ne sont pas conservées indéfiniment.

### <a name="what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation"></a>Que se passe-t-il si j’ai mise à l’échelle sur une autre taille et la restauration d’une sauvegarde qui a été effectuée avant l’opération de mise à l’échelle ?

-   Si vous avez redimensionnée à une plus grande taille, il n’existe aucun impact.
-   Si vous avez mise à l’échelle sur une taille inférieure, et que vous avez un personnalisé [bases de données](cache-configure.md#databases) qui est supérieure à la [limite de bases de données](cache-configure.md#databases) pour votre nouvelle taille, dans les bases de données n’est pas être restauré. Pour plus d’informations, voir [est mon bases de données personnalisées paramètre affecté pendant le dimensionnement ?](cache-how-to-scale.md#is-my-custom-databases-setting-affected-during-scaling)
-   Si vous avez mise à l’échelle sur une taille inférieure et qu’il n’est pas suffisamment d’espace la plus petite taille pour contenir toutes les données à partir de la dernière sauvegarde, les clés de suppression de pendant le processus de restauration, généralement à l’aide de la stratégie de suppression [allkeys lru](http://redis.io/topics/lru-cache) .

## <a name="next-steps"></a>Étapes suivantes
Découvrez comment utiliser d’autres fonctionnalités de cache premium.

-   [Introduction à la couche Azure Redis Cache Premium](cache-premium-tier-intro.md)
  
<!-- IMAGES -->

[redis-cache-new-cache-menu]: ./media/cache-how-to-premium-persistence/redis-cache-new-cache-menu.png

[redis-cache-premium-pricing-tier]: ./media/cache-how-to-premium-persistence/redis-cache-premium-pricing-tier.png

[redis-cache-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-persistence.png

[redis-cache-persistence-selected]: ./media/cache-how-to-premium-persistence/redis-cache-persistence-selected.png

[redis-cache-settings]: ./media/cache-how-to-premium-persistence/redis-cache-settings.png
