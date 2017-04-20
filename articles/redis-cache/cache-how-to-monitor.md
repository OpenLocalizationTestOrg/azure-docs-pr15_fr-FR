<properties 
    pageTitle="Comment faire pour surveiller Azure Redis Cache | Microsoft Azure" 
    description="Apprenez à surveiller l’intégrité et les performances vos instances de Cache Redis Azure" 
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
    ms.date="08/30/2016" 
    ms.author="sdanie"/>

# <a name="how-to-monitor-azure-redis-cache"></a>Comment faire pour surveiller Azure Redis Cache

Cache Redis Azure offre plusieurs options pour l’analyse des instances de votre cache. Vous pouvez afficher métriques, épingler graphiques mesures pour la Startboard, personnaliser la plage de date et l’heure de surveillance graphiques, ajouter et supprimer des mesures dans les graphiques et définir des alertes lorsque certaines conditions sont remplies. Ces outils permettent de contrôler l’état de vos instances Azure Redis Cache et vous aider à gérer vos applications de mise en cache.

Lorsque les diagnostics de cache sont activées, métrique d’instances de Cache Redis Azure est collectées environ toutes les 30 secondes et stockées afin qu’ils peuvent être affichés dans les graphiques mesures et évaluées par les règles d’alerte.

Indicateurs de cache sont collectées à l’aide de la Redis commande [Infos](http://redis.io/commands/info) . Pour plus d’informations sur les informations de différentes valeurs utilisées pour chaque cache métrique, voir [statistiques disponibles et création de rapports intervalles](#available-metrics-and-reporting-intervals).

Pour afficher les mesures de cache, [accédez](cache-configure.md#configure-redis-cache-settings) à votre instance de cache dans le [portail Azure](https://portal.azure.com). Métrique d’instances de Cache Redis Azure est accessibles sur la carte **Redis indicateurs** .

![Redis indicateurs][redis-cache-redis-metrics-blade]

>[AZURE.IMPORTANT] Si le message suivant s’affiche dans la carte **Redis indicateurs** , suivez les étapes décrites dans la section [activer cache des diagnostics](#enable-cache-diagnostics) pour activer les diagnostics de cache.
>
>`Monitoring may not be enabled. Click here to turn on Diagnostics.`

La carte **Redis métriques** dispose de **surveillance** graphiques qui affichent des indicateurs de cache. Chaque graphique peut être personnalisée en ajoutant ou suppression d’indicateurs et modification de l’intervalle de création de rapports. Pour afficher et configurer opérations et alertes, la carte de **Cache Redis** comporte une section **opérations** affichant cache **événements** et les **règles d’alerte**.

## <a name="enable-cache-diagnostics"></a>Activer les diagnostics de cache

Azure Cache Redis vous offre la possibilité que diagnostics données stockées dans un compte de stockage afin d’utiliser des outils que vous voulez accéder et traiter les données directement. Dans l’ordre des diagnostics cache à collecter, stocké et affiché dans le portail Azure, un compte de stockage doit être configuré. Cache dans la même région et d’abonnement partage le même compte de stockage de diagnostics et lorsque la configuration est modifiée s’applique à tous les cache dans l’abonnement qui se trouvent dans cette zone.

Pour activer et configurer les diagnostics de cache, accédez à la carte de **Cache Redis** pour votre instance de cache. Si diagnostics ne sont pas encore activés, un message s’affiche au lieu d’un graphique diagnostics.

![Activer les diagnostics de cache][redis-cache-enable-diagnostics]

Cliquez sur le message afin d’afficher la carte **métrique** et cliquez sur **Paramètres des diagnostics** pour activer et configurer les paramètres de diagnostic pour l’instance de service de cache.

![Paramètres des Diagnostics][redis-cache-diagnostic-settings]

![Configurer des diagnostics][redis-cache-configure-diagnostics]

Cliquez **sur** le bouton pour activer les diagnostics de cache et afficher la configuration des diagnostics.

Cliquez sur la flèche située à droite du **Compte de stockage** pour sélectionner un compte de stockage pour stocker les données de diagnostic. Pour de meilleures performances, sélectionnez un compte de stockage dans la même région en tant que le cache de résolution.

Une fois que les paramètres des diagnostics sont configurés, cliquez sur **Enregistrer** pour enregistrer la configuration. Notez qu’il peut prendre quelques instants pour que les modifications prennent effet.

>[AZURE.IMPORTANT] Cache dans la même région et l’abonnement partage les mêmes paramètres de stockage de diagnostics et lorsque la configuration est modifiés (diagnostics activé/désactivé ou en modifiant le compte de stockage) s’applique à tous les cache dans l’abonnement qui se trouvent dans cette zone.

Pour afficher les mesures stockées, examinez les tables dans votre compte de stockage des noms qui commencent par `WADMetrics`. Pour plus d’informations sur l’accès à la métrique stockée en dehors du portail Azure, consultez l’exemple de [contrôle d’accès Redis du Cache de données](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) .

>[AZURE.NOTE] Uniquement les indicateurs qui sont stockées dans le compte de stockage sélectionné sont affichées dans le portail Azure. Si vous changez les comptes de stockage, les données dans le compte de stockage précédemment configuré restent disponibles au téléchargement, mais elle n’est pas affiché dans le portail Azure.  

## <a name="available-metrics-and-reporting-intervals"></a>Statistiques disponibles et intervalles de création de rapports

Indicateurs de cache sont signalés à l’aide de plusieurs intervalles de création de rapports, notamment les **heures passées**, **aujourd'hui**, **semaine précédente**et **personnalisé**. La carte **métrique** pour chaque graphique indicateurs affiche les valeurs de moyennes, minimales et maximales pour chaque métrique dans le graphique, et certaines mesures affichent un total pour l’intervalle de création de rapports. 

Chaque métrique inclut deux versions. Une métrique mesure des performances pour le cache dans son intégralité et cache qui utilisent le [cluster](cache-how-to-premium-clustering.md), une version secondaire de la métrique incluant `(Shard 0-9)` dans les performances de mesures de nom pour une seule partagé dans un cache. Par exemple, si un cache a 4 milieu des fragments, `Cache Hits` est le montant total d’accès pour le cache dans son intégralité, et `Cache Hits (Shard 3)` est simplement le nombre d’accès pour cette éclater du cache.

>[AZURE.NOTE] Même lorsque le cache est inactif avec aucune application cliente active connecté, vous pouvez constater une activité de cache, tels que des clients connectés, utilisation de la mémoire et opérations en cours d’exécution. Cette activité est normale pendant l’opération d’une instance de Cache Redis Azure.

| Métrique            | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
|-------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Accès au cache        | Le nombre de clés trouvées dans l’intervalle de création de rapports spécifié. Correspond à `keyspace_hits` à partir de la Redis [informations](http://redis.io/commands/info) de commande.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| Échecs du cache      | Le nombre de recherches clés a échoué lors de l’intervalle de création de rapports spécifié. Correspond à `keyspace_misses` à partir de la commande Redis Infos. Échecs du cache pas nécessairement signifient qu'actuellement un problème avec le cache. Par exemple, lorsque vous utilisez le modèle de programmation latérale cache, une application recherche première dans le cache d’un élément. Si l’élément n’est pas visible (absence de cache), l’élément est extraite de la base de données et ajouté au cache pour une utilisation ultérieure. Échecs du cache sont pour le modèle de programmation cache latérale se déroule correctement. Si le nombre d’échecs du cache est plus élevé que prévu, examinez la logique d’application qui remplit et lit à partir du cache. Si sont en cours de suppression des éléments à partir du cache en raison de la pression de mémoire puis il peut y avoir des absences dans le cache, mais une mesure meilleure pour surveiller la pression de mémoire serait `Used Memory` ou `Evicted Keys`. |
| Clients connectés | Le nombre de connexions client dans le cache lors de l’intervalle de création de rapports spécifié. Correspond à `connected_clients` à partir de la commande Redis Infos. Une fois que la [limite de connexion](cache-configure.md#default-redis-server-configuration) est atteinte tentatives de connexion ultérieures au cache échouera. Notez que même s’il n’y a aucune application cliente active, il peut toujours être plusieurs instances de clients liés en raison des connexions et des processus internes.                                                                                                                                                                                                                                                                                                                                                                                                                          |
| Touches supprimés      | Le nombre d’éléments supprimé à partir du cache pendant l’intervalle de création de rapports spécifiée devant le `maxmemory` limite. Correspond à `evicted_keys` à partir de la commande Redis Infos.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| Touches qui a expiré      | Le nombre d’éléments arrivés à expiration dans le cache pendant l’intervalle de création de rapports spécifié. Cette valeur correspond à `expired_keys` à partir de la commande Redis Infos.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| Obtient              | Le nombre d’opérations get à partir du cache pendant l’intervalle de création de rapports spécifié. Cette valeur est la somme de ces valeurs à partir des informations Redis commande toutes les : `cmdstat_get`, `cmdstat_hget`, `cmdstat_hgetall`, `cmdstat_hmget`, `cmdstat_mget`, `cmdstat_getbit`, et `cmdstat_getrange`et équivaut à la somme du nombre de réponses au cours de l’intervalle de rapport.                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Redis charge du serveur | Le pourcentage de cycles dans lequel le serveur Redis est occupé de traitement et non en attente inactif pour les messages. Si ce compteur atteint 100, cela signifie que le serveur Redis a atteint un plafond de performances et de l’UC ne peut plus traiter travailler les plus rapidement. Si vous voyez une charge serveur élevée Redis exceptions d’expiration dans le client ne s’affiche. Dans ce cas, vous devez envisager l’évolution verticale ou partition vos données dans plusieurs cache.                                                                                                                                                                                                                                                                                                                                                                                                                                |
| Jeux              | Le nombre d’opérations de jeu dans le cache pendant l’intervalle de création de rapports spécifié. Cette valeur est la somme de ces valeurs à partir des informations Redis commande toutes les : `cmdstat_set`, `cmdstat_hset`, `cmdstat_hmset`, `cmdstat_hsetnx`, `cmdstat_lset`, `cmdstat_mset`, `cmdstat_msetnx`, `cmdstat_setbit`, `cmdstat_setex`, `cmdstat_setrange`, et `cmdstat_setnx`.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| Total des opérations  | Le nombre total de commandes traitées par le serveur de cache lors de l’intervalle de création de rapports spécifié. Cette valeur correspond à `total_commands_processed` à partir de la commande Redis Infos. Notez que lorsque Azure Redis Cache est utilisé uniquement pour la publication/sub il ne sera aucune métrique pour `Cache Hits`, `Cache Misses`, `Gets`, ou `Sets`, mais il y ait `Total Operations` indicateurs qui reflètent l’utilisation du cache pour les opérations de publication/sub.                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Mémoire utilisée       | La quantité de mémoire cache utilisée des paires clé/valeur dans le cache en Mo pendant l’intervalle de création de rapports spécifié. Cette valeur correspond à `used_memory` à partir de la commande Redis Infos. Cela n’inclut pas de métadonnées ou fragmentation.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| Mémoire utilisée RSS   | La quantité de mémoire cache utilisée en Mo pendant l’intervalle de création de rapports spécifiée, y compris la fragmentation et les métadonnées. Cette valeur correspond à `used_memory_rss` à partir de la commande Redis Infos.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| PROCESSEUR               | L’utilisation du processeur du serveur Azure Redis Cache sous forme de pourcentage dans l’intervalle de création de rapports spécifié. Cette valeur correspond au système d’exploitation `\Processor(_Total)\% Processor Time` compteur de performance.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| Lecture du cache        | La quantité de données de lire à partir du cache en mégaoctets par seconde (Mo/s) au cours de l’intervalle de création de rapports spécifié. Cette valeur est dérivée de cartes d’interface réseau qui prennent en charge de la machine virtuelle qui héberge la mémoire cache et n’est pas Redis spécifique. **Cette valeur correspond à la bande passante réseau utilisée par ce cache. Si vous voulez définir des alertes pour les limites de la bande passante du réseau côté serveur, puis créez à l’aide de ce `Cache Read` compteur. Consultez [ce tableau](cache-faq.md#cache-performance) pour les limites de bande passante observée pour cache différents niveaux et les tailles de tarification.**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| Cache d’écriture       | La quantité de données écrites dans le cache en mégaoctets par seconde (Mo/s) au cours de l’intervalle de rapport. Cette valeur est dérivée de cartes d’interface réseau qui prennent en charge de la machine virtuelle qui héberge la mémoire cache et n’est pas Redis spécifique. Cette valeur correspond à la bande passante réseau de données envoyées dans le cache du client.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |


## <a name="how-to-view-metrics-and-customize-charts"></a>Comment afficher les mesures et personnaliser les graphiques

Vous pouvez afficher une vue d’ensemble des mesures pour le cache sur la carte **Redis indicateurs** . Pour accéder à la **Redis métriques** carte choisir **tous les paramètres** > **Redis indicateurs**.

![Redis indicateurs][redis-cache-redis-metrics]


La carte **Redis indicateurs** contient les graphiques suivants.

| Redis graphique indicateurs | Métrique affichée     |
|------------------|-------------------|
| Écrivent du cache en lecture et en Cache | Lecture du cache |
|                            | Cache d’écriture |
| Clients connectés      | Clients connectés |
| Nombre de réponses  | Accès au cache        |
|                  | Échecs du cache      |
| Total des commandes   | Total des opérations  |
| Obtient et jeux    | Obtient              |
|                  | Jeux              |
| Utilisation de l’UC         | PROCESSEUR           |
| Utilisation de la mémoire      | Mémoire utilisée   |
|                   | Mémoire utilisée RSS |
| Redis charge du serveur | Charge du serveur   |
| Nombre de clés | Nombre total de clés |
|           | Touches supprimés |
|           | Touches qui a expiré |


Pour plus d’afficher les mesures dans un graphique spécifique et de personnaliser le graphique, cliquez sur le graphique souhaité à partir de la carte **Redis mesures** pour afficher la carte **métrique** de ce graphique.

![Carte métrique][redis-cache-metric-blade]

Toutes les alertes qui sont définis sur les mesures affichées par un graphique apparaissent en bas de la cuillère **métrique** de ce graphique.

Pour ajouter ou supprimer des mesures ou modifier l’intervalle de rapport, choisissez **Modifier le graphique**.

Pour ajouter ou supprimer des indicateurs dans le graphique, cliquez sur la case à cocher en regard du nom de la métrique. Pour modifier l’intervalle de rapport, cliquez sur l’intervalle souhaité. Pour modifier le **type de graphique**, cliquez sur le style souhaité. Une fois les modifications souhaitées, cliquez sur **Enregistrer**. 

![Modifier le graphique][redis-cache-edit-chart]

Lorsque vous cliquez sur **Enregistrer** vos modifications persiste jusqu'à ce que vous laissez la carte **métrique** . Lorsque vous revenez plus tard, vous verrez la métrique d’origine et la plage horaire à nouveau. Pour plus d’informations sur la personnalisation des graphiques, voir [métriques de service Moniteur](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md).

Pour afficher les mesures pour une période spécifique dans un graphique, pointez la souris sur un des barres spécifiques ou des points sur le graphique qui correspond à l’heure de votre choix, et les mesures pour cet intervalle sont affichées.

![Afficher les détails du graphique][redis-cache-view-chart-details]

## <a name="how-to-monitor-a-premium-cache-with-clustering"></a>Comment faire pour surveiller un cache premium avec cluster

Cache Premium ayant [cluster](cache-how-to-premium-clustering.md) activé peut avoir jusqu'à 10 milieu des fragments. Chaque éclater possède son propre métriques et les mesures sont agrégées afin de fournir des métriques pour le cache entier. Chaque métrique inclut deux versions. Une métrique mesure des performances pour le cache dans son intégralité et une autre version de la métrique qui inclut `(Shard 0-9)` dans les performances de mesures de nom pour une seule partagé dans un cache. Par exemple, si un cache comporte 3 milieu des fragments, `Cache Hits` est le montant total d’accès pour le cache dans son intégralité, et `Cache Hits (Shard 2)` est simplement le nombre d’accès pour cette éclater du cache.

Chaque graphique surveillance affiche les mesures de niveau supérieur pour le cache ainsi que les mesures pour chaque éclater cache.

![Moniteur][redis-cache-premium-monitor]

Vous pointez la souris sur les points de données affiche les détails de ce point dans le temps. 

![Moniteur][redis-cache-premium-point-summary]

Les valeurs plus grandes sont généralement les valeurs agrégées pour le cache tandis que les plus petites valeurs sont les mesures individuels pour le partagé. Notez que dans cet exemple, il existe trois milieu des fragments et la présence dans le cache est réparties de manière égale sur au milieu des fragments.

![Moniteur][redis-cache-premium-point-shard]

Pour afficher plus en détail cliquez sur le graphique pour afficher une vue développée sur la carte **métrique** .

![Moniteur][redis-cache-premium-chart-detail]

Par défaut chaque graphique inclut le compteur de performance de cache de niveau supérieur, ainsi que les compteurs de performance au milieu des fragments individuels. Vous pouvez personnaliser ces derniers sur la carte de **Modifier le graphique** .

![Moniteur][redis-cache-premium-edit]

Pour plus d’informations sur les compteurs de performance disponibles, voir [audits disponibles et des intervalles de création de rapports](#available-metrics-and-reporting-intervals).


## <a name="operations-and-alerts"></a>Opérations et les alertes

La section **opérations** sur la carte de **Cache Redis** comporte des sections **événements** et **règles d’alerte** .

![Oeprations][redis-cache-operations-events]

Pour afficher la liste récent d’opérations de cache, cliquez sur le graphique **d’événements** pour afficher la carte **d’événements** . Exemples d’opérations sont récupérer et régénérer les touches d’accès et l’activation et la résolution des règles d’alerte. Pour plus d’informations sur chaque événement, cliquez sur l’événement dans la carte **d’événements** .

Pour plus d’informations sur les événements, voir [Afficher les événements et les journaux d’audit](../monitoring-and-diagnostics/insights-debugging-with-events.md).

La section **règles d’alerte** affiche le nombre d’alertes relatives à l’instance de cache. Une règle d’alerte permet d’analyser votre instance de cache et de recevoir un message électronique chaque fois qu’une certaine valeur métrique atteint le seuil défini dans la règle. 

Règles d’alerte sont évaluées environ toutes les cinq minutes, et lorsqu’une règle d’alerte est activée, les notifications configurées sont envoyées. Notifications et activations de règle d’alerte ne sont pas traitées instantanément ; Il peut y avoir un délai de plusieurs minutes avant d’une règle d’alerte est activée et les notifications envoyées.

Règles d’alerte peuvent être consultées et définies à partir de la carte **métrique** pour un graphique de surveillance spécifique ou la carte de **règles d’alerte** .

Règles d’alerte ont les propriétés suivantes.

| Propriété de règle d’alerte                 | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
|-------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ressource                            | La ressource est évaluée par la règle d’alerte. Lorsque vous créez une règle d’alerte à partir d’un cache Redis, le cache est la ressource.                                                                                                                                                                                                                                                                                                                                                  |
| Nom                                | Nom qui identifie de la règle d’alerte dans l’instance actuelle du cache.                                                                                                                                                                                                                                                                                                                                                                                       |
| Description                         | Description facultative de la règle d’alerte.                                                                                                                                                                                                                                                                                                                                                                                                                               |
| Métrique                              | Métrique soit contrôlé par la règle d’alerte. Pour une liste des indicateurs de cache, voir statistiques disponibles et création de rapports intervalles.                                                                                                                                                                                                                                                                                                                                             |
| Condition                           | L’opérateur de condition pour la règle d’alerte. Choix possibles est : supérieure, supérieur ou égal à, inférieur à, inférieur ou égal à                                                                                                                                                                                                                                                                                                                             |
| Seuil                           | La valeur utilisée pour comparer avec la mesure de l’utilisation de l’opérateur spécifié par la propriété condition. Selon la métrique, cette valeur peut être dans octets/seconde, octets, % ou count.                                                                                                                                                                                                                                                                                     |
| Période                              | Spécifie la période pendant laquelle la valeur moyenne de la métrique est utilisée pour la comparaison de règle d’alerte. Par exemple, si la période a dépassé la dernière heure, la valeur moyenne de la métrique sur l’intervalle heure précédente est utilisée pour la comparaison. Si vous souhaitez recevoir une notification lorsque le seuil est atteint en raison d’un pic d’activité, un délai plus court est approprié. Pour être prévenu lorsqu’il y a activité prolongée au-dessus du seuil, utilisez une durée plus longue. |
| Service de messagerie et coadministrateurs | La valeur True, l’administrateur de service et l’administrateur Co-création sont envoyés par e-mail lorsque l’alerte est activée.                                                                                                                                                                                                                                                                                                                                                                    |
| Messagerie de l’administrateur supplémentaires      | Adresse de messagerie facultatif pour administrateur supplémentaire pour recevoir une notification lorsque l’alerte est activée.                                                                                                                                                                                                                                                                                                                                                                    |

Une notification est envoyée par l’activation de règle d’alerte. Une fois que le seuil d’une règle est dépassé et une notification est envoyée, la règle n’est pas ré-évaluée jusqu'à ce que la métrique inférieure au seuil. Si la métrique par la suite le seuil, l’alerte est réactivé et une nouvelle notification est envoyée.

Pour afficher toutes les règles d’alerte pour votre instance de cache, cliquez sur le composant de **règles d’alerte** dans la carte de **Cache Redis** . Pour afficher uniquement les règles d’alerte qui utilisent une métrique spécifique, accédez à la carte **métrique** pour le graphique contenant cette métrique.

![Règles d’alerte][redis-cache-alert-rules]

Pour ajouter une règle d’alerte, cliquez sur **Add alerte** à partir de la carte **métrique** ou la carte de **règles d’alerte** . 

Entrez les critères de la règle souhaitée dans la carte de règle **Ajouter une alerte** , puis cliquez sur **OK**. 

![Ajouter une règle d’alerte][redis-cache-add-alert]

>[AZURE.NOTE] Lorsqu’une règle d’alerte est créée en cliquant sur **Ajouter une alerte** à partir de la carte **métrique** , seules les mesures relatives affichées sur le graphique dans cette carte s’affichent dans le menu déroulant **métrique** . Lorsqu’une règle d’alerte est créée en cliquant sur **Ajouter une alerte** à partir de la carte de **règles d’alerte** , tous les paramètres du cache sont disponibles dans le menu déroulant **métrique** .

Une fois qu’une règle d’alerte est enregistrée s’affiche sur la carte de **règles d’alerte** ainsi que sur la carte **métrique** pour les graphiques qui s’affichent la métrique utilisée dans la règle d’alerte. Pour modifier une règle d’alerte, cliquez sur le nom de la règle d’alerte pour afficher la carte de **Modifier la règle** . À partir de la carte de **Modifier la règle** que vous pouvez modifier les propriétés de la règle, supprimer ou désactiver la règle d’alerte ou réactiver la règle si elle a été précédemment désactivée.

>[AZURE.NOTE] Toute modification apportée aux propriétés de la règle peut prendre un moment avant qu’elles apparaissent sur la carte de **règles d’alerte** ou la carte **métrique** .

Lorsqu’une règle d’alerte est activée, un message électronique est envoyé en fonction de la configuration de la règle d’alerte et une icône d’alerte s’affiche dans la partie de **règles d’alerte** sur la carte de **Cache Redis** .

Une règle d’alerte est censée être résolus lorsque la condition d’alerte n’est plus est vrai. Une fois que la condition de règle d’alerte est résolue, l’icône d’alerte est remplacée par une coche. Pour plus d’informations sur alertes activations et solutions, cliquez sur le composant **d’événements** sur la carte de **Cache Redis** pour afficher les événements sur la carte **d’événements** .

Pour plus d’informations sur les alertes dans Azure, voir [notifications d’alerte réception](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

## <a name="metrics-on-the-redis-cache-blade"></a>Indicateurs sur la carte de Cache Redis

La carte de **Cache Redis** affiche les catégories suivantes d’indicateurs.

-   [Graphiques de surveillance](#monitoring-charts)
-   [Graphiques de l’utilisation](#usage-charts)

### <a name="monitoring-charts"></a>Graphiques de surveillance

La section **surveillance** a **accès et échecs**, **Obtient et définit**, **connexions**et **Total commandes** graphiques.

![Graphiques de surveillance][redis-cache-monitoring-part]

Les graphiques de **surveillance** affichent les mesures suivantes.

| Graphique de surveillance | Indicateurs de cache     |
|------------------|-------------------|
| Nombre de réponses  | Accès au cache        |
|                  | Échecs du cache      |
| Obtient et jeux    | Obtient              |
|                  | Jeux              |
| Connexions      | Clients connectés |
| Total des commandes   | Total des opérations  |

Pour plus d’informations sur l’affichage les mesures et personnaliser les graphiques individuels dans cette section, voir la section suivante [comment afficher les mesures et personnaliser les indicateurs graphiques](#how-to-view-metrics-and-customize-charts) .

### <a name="usage-charts"></a>Graphiques de l’utilisation

La section **utilisation** a **Charge du serveur Redis**, **Utilisation de la mémoire**, **Bande passante réseau**et graphiques **De l’UC** et affiche également la **couche tarification** pour l’instance de cache.

![Graphiques de l’utilisation][redis-cache-usage-part]

Le **niveau de tarification** s’affiche le cache tarifs hiérarchiser et peut être utilisé à [l’échelle](cache-how-to-scale.md) le cache vers un autre niveau de tarification.

Les graphiques de **l’utilisation** affichent les mesures suivantes.

| Graphique de l’utilisation       | Indicateurs de cache |
|-------------------|---------------|
| Redis charge du serveur | Charge du serveur   |
| Utilisation de la mémoire      | Mémoire utilisée   |
| Bande passante réseau | Cache d’écriture   |
| Utilisation de l’UC         | PROCESSEUR           |

Pour plus d’informations sur l’affichage les mesures et personnaliser les graphiques individuels dans cette section, voir la section suivante [comment afficher les mesures et personnaliser les indicateurs graphiques](#how-to-view-metrics-and-customize-charts) .
  
<!-- IMAGES -->

[redis-cache-enable-diagnostics]: ./media/cache-how-to-monitor/redis-cache-enable-diagnostics.png

[redis-cache-diagnostic-settings]: ./media/cache-how-to-monitor/redis-cache-diagnostic-settings.png

[redis-cache-configure-diagnostics]: ./media/cache-how-to-monitor/redis-cache-configure-diagnostics.png

[redis-cache-monitoring-part]: ./media/cache-how-to-monitor/redis-cache-monitoring-part.png

[redis-cache-usage-part]: ./media/cache-how-to-monitor/redis-cache-usage-part.png

[redis-cache-metric-blade]: ./media/cache-how-to-monitor/redis-cache-metric-blade.png

[redis-cache-edit-chart]: ./media/cache-how-to-monitor/redis-cache-edit-chart.png

[redis-cache-view-chart-details]: ./media/cache-how-to-monitor/redis-cache-view-chart-details.png

[redis-cache-operations-events]: ./media/cache-how-to-monitor/redis-cache-operations-events.png

[redis-cache-alert-rules]: ./media/cache-how-to-monitor/redis-cache-alert-rules.png

[redis-cache-add-alert]: ./media/cache-how-to-monitor/redis-cache-add-alert.png

[redis-cache-premium-monitor]: ./media/cache-how-to-monitor/redis-cache-premium-monitor.png

[redis-cache-premium-edit]: ./media/cache-how-to-monitor/redis-cache-premium-edit.png

[redis-cache-premium-chart-detail]: ./media/cache-how-to-monitor/redis-cache-premium-chart-detail.png

[redis-cache-premium-point-summary]: ./media/cache-how-to-monitor/redis-cache-premium-point-summary.png

[redis-cache-premium-point-shard]: ./media/cache-how-to-monitor/redis-cache-premium-point-shard.png

[redis-cache-redis-metrics]: ./media/cache-how-to-monitor/redis-cache-redis-metrics.png

[redis-cache-redis-metrics-blade]: ./media/cache-how-to-monitor/redis-cache-redis-metrics-blade.png



