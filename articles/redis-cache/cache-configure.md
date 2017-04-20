<properties 
    pageTitle="Comment configurer Azure Redis Cache | Microsoft Azure"
    description="Comprendre la configuration de Redis par défaut pour le Cache Redis Azure et découvrez comment configurer vos instances de Cache Redis Azure"
    services="redis-cache"
    documentationCenter="na"
    authors="steved0x"
    manager="douge"
    editor="tysonn" />
<tags 
    ms.service="cache"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="cache-redis"
    ms.workload="tbd"
    ms.date="08/25/2016"
    ms.author="sdanie" />

# <a name="how-to-configure-azure-redis-cache"></a>Comment configurer Azure Redis Cache

Cette rubrique décrit comment consulter et mettre à jour la configuration pour vos instances de Cache Redis Azure et couvre la configuration du serveur Redis par défaut pour les instances de Cache Redis Azure.

>[AZURE.NOTE] Pour plus d’informations sur la configuration et l’utilisation des fonctionnalités de cache premium, voir [comment configurer persistance Premium Azure Redis cache](cache-how-to-premium-persistence.md), [Comment faire pour configurer le cluster Premium Azure Redis cache](cache-how-to-premium-clustering.md)et [comment configurer la prise en charge de réseau virtuel Premium Azure Redis cache](cache-how-to-premium-vnet.md).

## <a name="configure-redis-cache-settings"></a>Configurer les paramètres de cache Redis

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-browse.md)]

Cache Redis Azure fournit les paramètres suivants sur la carte de **paramètres** .

![Redis paramètres du Cache](./media/cache-configure/redis-cache-settings.png)



-   [Prise en charge et résolution des problèmes de paramètres](#support-amp-troubleshooting-settings)
-   [Paramètres généraux](#general-settings)
    -   [Propriétés](#properties)
    -   [Touches d’accès rapide](#access-keys)
    -   [Paramètres avancés](#advanced-settings)
    -   [Redis Cache Advisor](#redis-cache-advisor)
-   [Paramètres d’échelle](#scale-settings)
    -   [Niveau de tarification](#pricing-tier)
    -   [Redis taille de cluster](#cluster-size)
-   [Paramètres de gestion des données](#data-management-settings)
    -   [Redis permanence des données](#redis-data-persistence)
    -   [Importer/exporter](#importexport)
-   [Paramètres d’administration](#administration-settings)
    -   [Redémarrage](#reboot)
    -   [Mises à jour de la planification](#schedule-updates)
-   [Paramètres des Diagnostics](#diagnostics-settings)
-   [Paramètres du réseau](#network-settings)
-   [Paramètres de gestion des ressources](#resource-management-settings)

## <a name="support--troubleshooting-settings"></a>Prise en charge et résolution des problèmes de paramètres

Les paramètres de la section **Support + résolution des problèmes** vous fournissent avec les options de résolution des problèmes avec le cache.

![Prise en charge + résolution des problèmes](./media/cache-configure/redis-cache-support-troubleshooting.png)

Cliquez sur **diagnostiquer et résoudre les problèmes** fournis avec les stratégies et problèmes courants permettant de les résoudre.

Cliquez sur **journal d’activité** pour afficher les actions exécutées dans le cache. Vous pouvez également utiliser le filtrage pour développer cette vue pour inclure d’autres ressources. Pour plus d’informations sur l’utilisation des journaux d’audit, voir [Afficher les événements et des journaux d’audit](../monitoring-and-diagnostics/insights-debugging-with-events.md) et [Audit opérations avec le Gestionnaire de ressources](../resource-group-audit.md). Pour plus d’informations sur l’analyse des événements de Cache Redis Azure, voir [opérations et les alertes](cache-how-to-monitor.md#operations-and-alerts).

**Santé des ressources** espions de la ressource et vous indique s’il s’exécute comme prévu. Pour plus d’informations sur le service de santé ressource Azure, voir [vue d’ensemble d’intégrité de ressource Azure](../resource-health/resource-health-overview.md).

>[AZURE.NOTE] Santé des ressources ne parvient pas à créer des rapports sur l’état du Cache Redis Azure instances hébergé dans un réseau virtuel. Pour plus d’informations, voir [toutes les fonctionnalités de cache fonctionnent lors de l’hébergement d’un cache dans un VNET ?](cache-how-to-premium-vnet.md#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet)

Cliquez sur **nouvelle demande d’assistance** pour ouvrir une demande de support pour le cache.

## <a name="general-settings"></a>Paramètres généraux

Les paramètres dans la section **Général** permettent d’accéder et de configurer les paramètres suivants pour votre cache.

![Paramètres généraux](./media/cache-configure/redis-cache-general-settings.png)

-   [Propriétés](#properties)
-   [Touches d’accès rapide](#access-keys)
-   [Paramètres avancés](#advanced-settings)
-   [Redis Cache Advisor](#redis-cache-advisor)

### <a name="properties"></a>Propriétés

Cliquez sur **Propriétés** pour afficher des informations sur le cache, y compris les ports et le point de terminaison du cache.

![Redis des propriétés de Cache](./media/cache-configure/redis-cache-properties.png)

### <a name="access-keys"></a>Touches d’accès rapide

Cliquez sur les **touches d’accès rapide** pour afficher ou régénérer les touches d’accès rapide pour le cache. Ces touches sont utilisés par les clients se connectant à votre cache ainsi que le nom d’hôte et les ports à partir de la carte de **Propriétés** .

![Redis Cache les touches d’accès](./media/cache-configure/redis-cache-manage-keys.png)






### <a name="advanced-settings"></a>Paramètres avancés

Les paramètres suivants sont configurer sur la carte **des paramètres avancés** .

-   [Ports d’accès](#access-ports)
-   [Stratégie de MaxMemory et réservé maxmemory](#maxmemory-policy-and-maxmemory-reserved)
-   [Notifications de Keyspace (paramètres avancées)](#keyspace-notifications-advanced-settings)


### <a name="access-ports"></a>Ports d’accès

Par défaut, accès non SSL est désactivé pour le nouveau cache. Pour activer le port non SSL, cliquez sur **non** pour **Autoriser l’accès uniquement via SSL** sur la **carte de paramètres avancés** , puis sur **Enregistrer**.

![Ports d’accès Cache redis](./media/cache-configure/redis-cache-access-ports.png)

### <a name="maxmemory-policy-and-maxmemory-reserved"></a>Stratégie de MaxMemory et réservé maxmemory

Les paramètres de **stratégie Maxmemory** et **réservé maxmemory** sur la carte **des paramètres avancés** configurent les stratégies de mémoire pour le cache. Le paramètre de **stratégie de maxmemory** configure la stratégie de suppression pour le cache et **réservé maxmemory** configure la mémoire réservée aux processus non cache.

![Redis Cache Maxmemory stratégie](./media/cache-configure/redis-cache-maxmemory-policy.png)

**MaxMemory stratégie** permet de choisir parmi les stratégies suivantes ait.

-   volatiles-lru - il s’agit de la valeur par défaut.
-   AllKeys lru
-   volatiles aléatoire
-   AllKeys aléatoire
-   volatiles ttl
-   noeviction

Pour plus d’informations sur les stratégies maxmemory, voir [stratégies de suppression](http://redis.io/topics/lru-cache#eviction-policies).

Le paramètre **réservé maxmemory** configure la quantité de mémoire en Mo réservé pour les opérations non cache telles que la réplication pendant le basculement. Il peut également être utilisé lorsque vous avez un ratio fragmentation élevée. Ce paramètre vous permet d’avoir une expérience de serveur Redis plus cohérente lorsque votre charge varie. Cette valeur doit être définie supérieure pour les charges de travail qui sont en écriture lourds. Lors de la mémoire est réservée aux opérations telles qu’il n’est pas disponible pour le stockage des données mises en cache.

>[AZURE.IMPORTANT] Le paramètre **réservé maxmemory** est disponible uniquement pour Standard et Premium met en cache.

### <a name="keyspace-notifications-advanced-settings"></a>Notifications de Keyspace (paramètres avancées)

Redis keyspace notifications sont configurées sur la carte **des paramètres avancés** . Notifications de Keyspace permettent aux clients de recevoir des notifications lorsque certains événements se produisent.

![Redis Cache des paramètres avancé](./media/cache-configure/redis-cache-advanced-settings.png)

>[AZURE.IMPORTANT] Notifications de Keyspace et le paramètre **événements keyspace avertir** sont uniquement disponibles pour Standard et Premium met en cache.

Pour plus d’informations, voir [Redis Keyspace Notifications](http://redis.io/topics/notifications). Pour les exemples de code, voir le fichier [KeySpaceNotifications.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/KeySpaceNotifications.cs) dans l’échantillon [Bonjour tout le monde](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) .

<a name="recommendations"></a>
## <a name="redis-cache-advisor"></a>Redis Cache Advisor

La carte de **recommandations** affiche des recommandations pour le cache. Tout se déroule normalement, aucune recommandation ne s’affichent. 

![Recommandations](./media/cache-configure/redis-cache-no-recommendations.png)

Si toutes les conditions se produisent durant les opérations de cache tels que l’utilisation de mémoire haute, la bande passante réseau ou charge du serveur, une alerte s’affiche dans la carte de **Cache Redis** .

![Recommandations](./media/cache-configure/redis-cache-recommendations-alert.png)

Vous trouverez plus d’informations sur la carte de **recommandations** .

![Recommandations](./media/cache-configure/redis-cache-recommendations.png)

Vous pouvez surveiller les mesures dans les sections de [surveillance](cache-how-to-monitor.md#monitoring-charts) et les [graphiques de l’utilisation](cache-how-to-monitor.md#usage-charts) de la cuillère **Redis Cache** .

Chaque niveau de tarification comporte différentes limites pour les connexions client, la mémoire et la bande passante. Si le cache approche la capacité maximale pour ces indicateurs sur une période prolongée, une recommandation est créée. Pour plus d’informations sur les mesures et les limites a été relues par l’outil de **recommandations** , voir le tableau suivant.

| Redis métrique Cache      | Pour plus d’informations, voir                                                  |
|-------------------------|---------------------------------------------------------------------------|
| Utilisation de la bande passante réseau | [Performances du cache - la bande passante disponible](cache-faq.md#cache-performance) |
| Clients connectés       | [Configuration du serveur par défaut Redis - maxclients](#maxclients)            |
| Charge du serveur             | [Graphiques de l’utilisation - Redis charge du serveur](cache-how-to-monitor.md#usage-charts)  |
| Utilisation de la mémoire            | [Performances du cache - taille](cache-faq.md#cache-performance)                |

Pour mettre à niveau le cache, cliquez sur **mettre à jour maintenant** pour modifier le [niveau de tarification](#pricing-tier) et mettre à l’échelle le cache de résolution. Pour plus d’informations sur le choix d’un niveau de tarification, voir [quelle offre Redis Cache et la taille dois-je utiliser ?](cache-faq.md#what-redis-cache-offering-and-size-should-i-use).

## <a name="scale-settings"></a>Paramètres d’échelle

Les paramètres dans la section **échelle** permettent d’accéder et de configurer les paramètres suivants pour votre cache.

![Réseau](./media/cache-configure/redis-cache-scale.png)

-   [Niveau de tarification](#pricing-tier)
-   [Redis taille de cluster](#cluster-size)

### <a name="pricing-tier"></a>Niveau de tarification

Cliquez sur **couche de tarification** pour afficher ou modifier le niveau de tarification pour le cache. Pour plus d’informations sur la mise à l’échelle, voir [comment échelle Azure Redis Cache](cache-how-to-scale.md).

![Redis Cache tarifs niveau](./media/cache-configure/pricing-tier.png)

<a name="cluster-size"></a>
### <a name="redis-cluster-size"></a>Redis Cluster taille

Cliquez sur **Taille de Cluster Redis (PREVIEW)** pour modifier la taille de cluster pour cumulé cache premium avec cluster activé.

>[AZURE.NOTE] Notez que si la couche Azure Redis Cache Premium a été publiée officielle, la fonction Redis la taille de Cluster est actuellement en mode Aperçu avant.

![Redis taille de cluster](./media/cache-configure/redis-cache-redis-cluster-size.png)

Pour modifier la taille de cluster, utilisez le curseur ou tapez un nombre compris entre 1 et 10 dans la zone de texte **nombre partagé** et cliquez sur **OK** pour enregistrer.

>[AZURE.IMPORTANT] Redis cluster est disponible uniquement pour cache Premium. Pour plus d’informations, voir [comment configurer cluster Premium Azure Redis cache](cache-how-to-premium-clustering.md).


## <a name="data-management-settings"></a>Paramètres de gestion des données

Les paramètres dans la section **Gestion des données** permettent d’accéder et de configurer les paramètres suivants pour votre cache.

![Gestion des données](./media/cache-configure/redis-cache-data-management.png)

-   [Redis permanence des données](#redis-data-persistence)
-   [Importer/exporter](#importexport)

### <a name="redis-data-persistence"></a>Redis permanence des données

Cliquez sur **Redis permanence des données** pour activer, désactiver ou configurer permanence des données pour le cache de résolution premium.

![Redis permanence des données](./media/cache-configure/redis-cache-persistence-settings.png)

Pour activer la Redis permanence, cliquez sur **activé** pour activer la sauvegarde relationnelle (base de données Redis). Pour désactiver la persistance Redis, cliquez sur **désactivé**.

Pour configurer l’intervalle de sauvegarde, sélectionnez une **Fréquence de sauvegarde** dans la liste déroulante. Choix incluent ** **15 Minutes**, **30 minutes**, 60 minutes**, **6 heures**, **12 heures**et **24 heures**. Cet intervalle commence à compter vers le bas une fois l’opération de sauvegarde précédente terminée et lorsqu’il s’écoule une nouvelle sauvegarde est lancée.

Cliquez sur le **Compte de stockage** pour sélectionner le compte de stockage à utiliser, puis sélectionnez soit la **clé primaire** ou **secondaire** à utiliser dans la liste déroulante **Clé de stockage** . Vous devez choisir un compte de stockage dans la même région en tant que le cache, et un compte de **Stockage Premium** est recommandé, car le stockage premium a un débit plus élevé. Chaque fois que la clé de stockage pour votre compte persistance régénérer, vous devez ré-choisir la clé souhaitée dans la liste déroulante **Clé de stockage** .

Cliquez sur **OK** pour enregistrer la configuration de persistance.

>[AZURE.IMPORTANT] Permanence des données redis est disponible uniquement pour cache Premium. Pour plus d’informations, voir [comment configurer persistance Premium Azure Redis cache](cache-how-to-premium-persistence.md).

### <a name="importexport"></a>Importer/exporter

Importation/exportation est une opération de gestion des données Azure Redis Cache qui vous permet d’importer des données dans le Cache Redis Azure ou exporter des données à partir d’Azure Redis Cache par importer et exporter un instantané de base de données de Cache Redis (relationnelle) à partir d’un cache premium vers un blob de page dans un compte de stockage Azure. Cela vous permet de migrer entre différentes instances de Cache Redis Azure ou remplir le cache de données avant des utiliser.

Importation peut servir à transférer Redis compatible relationnelle ou les fichiers à partir de n’importe quel serveur Redis en cours d’exécution dans n’importe quel nuage ou l’environnement, y compris Redis s’exécutant sur n’importe quel fournisseur de nuage tel que Amazon Web Services et d’autres personnes, Windows ou Linux. Importer des données est un moyen facile de créer un cache de données prédéfinie. Pendant le processus d’importation Azure Redis Cache charge les fichiers relationnelle à partir du stockage Azure en mémoire, puis insère les touches dans le cache.

Exporter vous permet d’exporter les données stockées dans le Cache de Redis Azure à Redis compatible relationnelle ou les fichiers. Vous pouvez utiliser cette fonctionnalité pour déplacer des données à partir d’une instance de Cache Redis Azure vers un autre ou vers un autre serveur Redis. Pendant le processus d’exportation qu'un fichier temporaire est créé sur l’ordinateur virtuel qui héberge l’instance du serveur Azure Redis Cache et le fichier est téléchargé sur le compte de stockage désigné. Une fois l’opération d’exportation terminée avec soit un état de réussite ou l’échec, le fichier temporaire est supprimé.

>[AZURE.IMPORTANT] Importation/exportation est disponible uniquement pour cache de niveau Premium. Pour plus d’informations et des instructions, voir [Importer et exporter des données dans le Cache Redis Azure](cache-how-to-import-export-data.md).


## <a name="administration-settings"></a>Paramètres d’administration

Les paramètres dans la section **Administration** vous permettent d’effectuer les tâches administratives suivantes pour le cache de résolution premium. 

![Administration](./media/cache-configure/redis-cache-administration.png)

-   [Redémarrage](#reboot)
-   [Mises à jour de la planification](#schedule-updates)

>[AZURE.IMPORTANT] Les paramètres de cette section ne sont disponibles pour le cache de niveau Premium.

### <a name="reboot"></a>Redémarrage

La carte **redémarrer** vous permet de redémarrer une ou plusieurs des nœuds du cache. Cela vous permet de tester votre application pour la résilience en cas de panne.

![Redémarrage](./media/cache-configure/redis-cache-reboot.png)

Si vous avez un cache premium avec cluster activé, vous pouvez sélectionner quels milieu des fragments du cache de redémarrer.

![Redémarrage](./media/cache-configure/redis-cache-reboot-cluster.png)

Pour redémarrer un ou plusieurs nœuds de cache, sélectionnez les nœuds souhaités et cliquez sur **redémarrer**. Si vous avez un cache premium avec cluster activé, sélectionnez le shard(s) redémarrer, puis cliquez sur **redémarrer**. Après quelques minutes, le redémarrage ou les nœuds sélectionné et sont reconnecter quelques minutes plus tard.

>[AZURE.IMPORTANT] Redémarrage est disponible uniquement pour cache de niveau Premium. Pour plus d’informations et des instructions, voir [administration Azure Redis Cache - redémarrer](cache-administration.md#reboot).

### <a name="schedule-updates"></a>Mises à jour de la planification

La carte de **mises à jour de la planification** vous permet de désigner une fenêtre de gestion des mises à jour de serveur Redis pour le cache de résolution. 

>[AZURE.IMPORTANT] Notez que la fenêtre de maintenance s’applique uniquement aux Redis mises à jour du serveur et pas de n’importe quel Azure mises à jour ou mises à jour du système d’exploitation des ordinateurs virtuels hébergeant le cache.

![Mises à jour de la planification](./media/cache-configure/redis-schedule-updates.png)

Pour spécifier une période de maintenance, vérifiez vos jours et spécifier l’heure de début de fenêtre de maintenance pour chaque jour, puis cliquez sur **OK**. Notez que l’horaire de la fenêtre maintenance est au format UTC. 

>[AZURE.IMPORTANT] Mises à jour du calendrier est disponible uniquement pour cache de niveau Premium. Pour plus d’informations et des instructions, voir [administration Azure Redis Cache - mises à jour de la planification](cache-administration.md#schedule-updates).

## <a name="diagnostics-settings"></a>Paramètres des Diagnostics

La section **Diagnostics** permet de configurer des diagnostics pour le Cache de résolution Redis.

![Diagnostics](./media/cache-configure/redis-cache-diagnostics.png)

Pour [configurer le compte de stockage](cache-how-to-monitor.md#enable-cache-diagnostics) utilisé pour stocker les diagnostics de cache, cliquez sur **Diagnostics** .

![Redis Diagnostics Cache](./media/cache-configure/redis-cache-diagnostics-settings.png)

Pour [Afficher les mesures](cache-how-to-monitor.md#how-to-view-metrics-and-customize-charts) pour votre cache et **règles d’alerte** pour [définir des règles d’alerte](cache-how-to-monitor.md#operations-and-alerts), cliquez sur **Redis indicateurs** .

Pour plus d’informations sur les diagnostics de Cache Redis Azure, Découvrez [comment contrôler Azure Redis Cache](cache-how-to-monitor.md).


## <a name="network-settings"></a>Paramètres du réseau

Les paramètres de la section **réseau** permettent d’accéder à configurer les paramètres suivants pour votre cache.

![Réseau](./media/cache-configure/redis-cache-network.png)

>[AZURE.IMPORTANT] Paramètres réseau virtuel ne sont disponibles pour le cache premium qui ont été configurés avec prise en charge VNET lors de la création du cache. Pour plus d’informations sur la création d’un cache premium avec VNET prennent en charge et mise à jour ses paramètres, voir [comment configurer virtuel réseau prend en charge pour un Cache de Redis Premium Azure](cache-how-to-premium-vnet.md).

## <a name="resource-management-settings"></a>Paramètres de gestion des ressources

![Gestion des ressources](./media/cache-configure/redis-cache-resource-management.png)

La section **balises** vous permet d’organiser vos ressources. Pour plus d’informations, voir [en utilisant des balises pour organiser vos ressources Azure](../resource-group-using-tags.md).

La section **verrouille** permet de verrouiller un abonnement, groupe de ressources ou des ressources pour empêcher d’autres utilisateurs de votre organisation à partir d’accidentellement en supprimant ou modifiant les ressources critiques. Pour plus d’informations, consultez [ressources de verrouillage avec le Gestionnaire de ressources Azure](../resource-group-lock-resources.md).

La section **utilisateurs** prend en charge le contrôle d’accès basé sur un rôle (RBAC) dans le portail Azure pour aider les organisations à répondre aux exigences de gestion des accès simple et précise. Pour plus d’informations, voir [contrôle d’accès basé sur un rôle dans le portail Azure](../active-directory/role-based-access-control-configure.md).

Cliquez sur **Exporter le modèle** pour créer et exporter un modèle de vos ressources déployées pour les déploiements à venir. Pour plus d’informations sur l’utilisation des modèles, consultez [ressources de déploiement avec les modèles Azure le Gestionnaire de ressources](../resource-group-template-deploy.md).

## <a name="default-redis-server-configuration"></a>Configuration du serveur Redis par défaut

Nouvelle instance de Cache Redis Azure est configurés avec les valeurs de configuration de Redis par défaut suivantes.

>[AZURE.NOTE] Les paramètres de cette section ne peuvent pas être modifiés à l’aide de la `StackExchange.Redis.IServer.ConfigSet` méthode. Si cette méthode est appelée avec l’une des commandes dans cette section, une exception semblable au suivant est levée :  
>
>`StackExchange.Redis.RedisServerException: ERR unknown command 'CONFIG'`
>  
>Toutes les valeurs qui peuvent être configurées, telles que **politique de mémoire max**, sont configurables via les outils de gestion portail ou de ligne de commande Azure comme Azure infrastructure du langage commun ou PowerShell.

|Paramètre|Valeur par défaut|Description|
|---|---|---|
|bases de données|16|Le nombre de bases de données par défaut est de 16, mais vous pouvez configurer un nombre différent en fonction du niveau de prix. <sup>1</sup> DB 0 correspond à la base de données par défaut, vous pouvez sélectionner une autre dans une base de chaque connexion à l’aide `connection.GetDatabase(dbid)` où dbid est un nombre compris entre `0` et `databases - 1`.|
|MaxClients|Dépend de la tarification niveau<sup>2</sup>|Il s’agit le nombre maximal de clients connectés autorisé en même temps. Une fois que la limite est atteinte Redis se ferme toutes les nouvelles connexions envoyer un message d’erreur « nombre maximal de clients atteint ».|
|stratégie de MaxMemory|volatiles lru|Stratégie MaxMemory est le paramètre pour comment Redis sélectionne les éléments à supprimer lorsque maxmemory (la taille du cache offrant que vous avez sélectionné lorsque vous avez créé le cache) est atteint. Le paramètre par défaut est Azure Redis Cache lru volatiles, ce qui permet de supprimer les touches avec une expiration définie à l’aide d’un algorithme LRU. Ce paramètre peut être configuré dans le portail Azure. Pour plus d’informations, voir [Maxmemory stratégie et réservé maxmemory](#maxmemory-policy-and-maxmemory-reserved).|
|exemples de MaxMemory|3|LRU et algorithmes TTL minimales ne sont pas algorithmes précis mais rapprocher algorithmes (afin d’économiser de la mémoire), afin de pouvoir sélectionner ainsi que la taille de l’échantillon à vérifier. Par exemple pour par défaut Redis vérifie trois clés et sélectionnez celui qui a été moins récemment utilisé.|
|LUA délai|5 000|Max exécution d’un script Lua en millisecondes. Si la durée d’exécution maximale est atteint Redis se connecte qu’un script est encore en exécution une fois que la valeur maximale autorisée heure et commence à répondre aux requêtes avec un message d’erreur.|
|limite de l’événement LUA|500|Il s’agit de la taille maximale de file d’attente des événements de script.|
|limite client-sortie-tampon limite normalclient-sortie-tampon pubsub|0 0 032 Mo 8 60|Les limites de mémoire tampon de sortie client peuvent servir à forcer la déconnexion des clients qui ne sont pas la lecture de données à partir du serveur assez rapidement pour une raison quelconque (une raison courante est qu’un client publication/Sub ne peut pas consommer rapidement l’éditeur peut générer les messages). Pour plus d’informations, voir [http://redis.io/topics/clients](http://redis.io/topics/clients).|

<a name="databases"></a>
<sup>1</sup> La limite de `databases` est différente pour chaque Cache Azure Redis tarifs couche et peut être définie lors de la création du cache. Si aucune `databases` paramètre est spécifié lors de la création de cache, la valeur par défaut est de 16.

-   Base et Standard
    -   C0 cache (250 Mo) - jusqu'à 16 bases de données
    -   C1 cache (1 Go) - jusqu'à 16 bases de données
    -   C2 cache (2,5 Go) - jusqu'à 16 bases de données
    -   C3 (6 Go) cache - jusqu'à 16 bases de données
    -   C4 cache (13 Go) - jusqu'à 32 bases de données
    -   C5 cache (26 Go) - jusqu'à 48 bases de données
    -   C6 cache (Go 53) - jusqu'à 64 bases de données
-   Met en cache Premium
    -   P1 (6 à 60 Go) - jusqu'à 16 bases de données
    -   P2 (13 à 130 Go) - jusqu'à 32 bases de données
    -   P3 (26 à 260 Go) - jusqu'à 48 bases de données
    -   P4 (53 à 530 Go) - jusqu'à 64 bases de données
    -   Tous les cache premium avec cluster Redis activé - Redis cluster prend uniquement en charge de la base de données 0 afin que la `databases` limiter pour n’importe quel cache premium avec cluster Redis activé est efficacement 1 et la commande [Sélectionner](http://redis.io/commands/select) n’est pas autorisée. Pour plus d’informations, voir [dois-je apporter des modifications à mon application cliente utilise cluster ?](#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)


>[AZURE.NOTE] La `databases` paramètre peut être configurées uniquement pendant la création du cache et uniquement à l’aide de PowerShell, infrastructure du langage commun ou autres clients de gestion. Pour obtenir un exemple de la configuration `databases` lors de la création de cache à l’aide de PowerShell, voir [AzureRmRedisCache de nouveau](cache-howto-manage-redis-cache-powershell.md#databases).


<a name="maxclients"></a>
<sup>2</sup> `maxclients` est différente pour chaque Cache Azure Redis tarifs niveau.

-   Base et Standard
    -   C0 cache (250 Mo) - jusqu'à 256 connexions
    -   C1 cache (1 Go) - jusqu'à 1 000 connexions
    -   C2 cache (2,5 Go) - jusqu'à 2 000 connexions
    -   C3 (6 Go) cache - jusqu'à 5 000 connexions
    -   C4 cache (13 Go) - jusqu'à 10 000 connexions
    -   C5 cache (26 Go) - jusqu'à 15 000 connexions
    -   C6 cache (Go 53) - jusqu'à 20 000 connexions
-   Met en cache Premium
    -   P1 (6 à 60 Go) - jusqu'à 7 500 connexions
    -   P2 (13 à 130 Go) - jusqu'à 15 000 connexions
    -   P3 (26 à 260 Go) - jusqu'à 30 000 connexions
    -   P4 (53 à 530 Go) - jusqu'à 40 000 connexions

## <a name="redis-commands-not-supported-in-azure-redis-cache"></a>Redis commandes non prises en charge dans le Cache Redis Azure

>[AZURE.IMPORTANT] Étant donné que la configuration et la gestion des instances de Cache Redis Azure est gérée par Microsoft les commandes suivantes sont désactivées. Si vous essayez d’appeler vous recevrez un message d’erreur semblable à `"(error) ERR unknown command"`.
>
>-  BGREWRITEAOF
>-  BGSAVE
>-  CONFIGURATION DE LA
>-  DÉBOGUER
>-  MIGRER
>-  ENREGISTRER
>-  ARRÊT
>-  SLAVEOF
>-  CLUSTER - Cluster écriture sont désactivées, mais en lecture seule Cluster commandes sont autorisées.

Pour plus d’informations sur les commandes Redis, voir [http://redis.io/commands](http://redis.io/commands).

## <a name="redis-console"></a>Redis console

Vous pouvez en toute sécurité émettre des commandes à vos instances de Cache Redis Azure à l’aide de la **Console Redis**, qui est disponible pour Standard et Premium met en cache.

>[AZURE.IMPORTANT] La Console Redis ne fonctionne pas avec VNET, cluster et les bases de données autres que 0. 
>
>-  [VNET](cache-how-to-premium-vnet.md) - lorsque le cache fait partie d’un VNET, seuls les clients dans le VNET peuvent accéder au cache. Car la Console Redis utilise le client redis cli.exe hébergé sur des ordinateurs virtuels qui ne font pas partie de votre VNET, il ne peut pas se connecter à votre cache.
>-  [Cluster](cache-how-to-premium-clustering.md) - Redis de la Console utilise le client redis cli.exe qui ne prend pas en charge le cluster pour le moment. L’utilitaire redis-infrastructure du langage commun dans la branche [instable](http://redis.io/download) du référentiel Redis en GitHub mettent en œuvre, prise en charge de base lors de la mise en route avec la `-c` basculer. Pour plus d’informations voir [jouer avec le cluster](http://redis.io/topics/cluster-tutorial#playing-with-the-cluster) sur [http://redis.io](http://redis.io) dans la [Redis didacticiel cluster](http://redis.io/topics/cluster-tutorial).
>-  La Console Redis établit une nouvelle connexion à la base de données 0 chaque fois que vous envoyez une commande. Vous ne pouvez pas utiliser le `SELECT` commande permettant de sélectionner une autre base de données, car la base de données est réinitialisé à 0 avec chaque commande. Pour plus d’informations sur l’exécution de commandes Redis, y compris à une autre base de données, voir [Comment puis-je exécuter Redis commandes ?](cache-faq.md#how-can-i-run-redis-commands)

Pour accéder à la Console Redis, cliquez sur **Console** de la carte de **Cache Redis** .

![Redis console](./media/cache-configure/redis-console-menu.png)

Pour exécuter des commandes sur votre instance de cache, tapez simplement la commande souhaitée dans la console.

![Redis console](./media/cache-configure/redis-console.png)

Pour la liste des commandes Redis sont désactivées pour Azure Redis Cache, voir la section précédente [Redis commandes non prises en charge dans Azure Redis Cache](#redis-commands-not-supported-in-azure-redis-cache) . Pour plus d’informations sur les commandes Redis, voir [http://redis.io/commands](http://redis.io/commands). 

## <a name="move-your-cache-to-a-new-subscription"></a>Déplacer le cache vers un nouvel abonnement

Vous pouvez déplacer le cache vers un nouvel abonnement en cliquant sur **déplacer**.

![Déplacer Redis Cache](./media/cache-configure/redis-cache-move.png)

Pour plus d’informations sur le déplacement des ressources à partir d’un groupe de ressources à l’autre et à partir d’un abonnement à un autre, voir [déplacer des ressources à nouveau groupe de ressources ou d’abonnement](../resource-group-move-resources.md).

## <a name="next-steps"></a>Étapes suivantes
-   Pour plus d’informations sur l’utilisation des commandes Redis, voir [Comment puis-je exécuter Redis commandes ?](cache-faq.md#how-can-i-run-redis-commands).
