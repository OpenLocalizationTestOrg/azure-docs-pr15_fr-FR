<properties 
    pageTitle="Cache Redis Azure FAQ | Microsoft Azure" 
    description="Découvrez les réponses aux questions les plus fréquentes, les modèles et les meilleures pratiques pour le Cache Redis Azure" 
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
    ms.date="10/18/2016" 
    ms.author="sdanie"/>

# <a name="azure-redis-cache-faq"></a>Cache Redis Azure Forum aux questions

Découvrez les réponses aux questions les plus courantes, des modèles et des pratiques recommandées pour Azure Redis Cache. 


## <a name="what-if-my-question-isnt-answered-here"></a>Que se passe-t-il si ma question n’est pas une réponse ici ?

Si votre question n’est pas répertoriée ici, n’hésitez pas et nous allons vous aider à trouver une réponse.

-   Vous pouvez publier une question dans le [thread Disqus](#comments) à la fin de ce forum aux questions et faites appel à l’équipe de Cache Azure et d’autres membres de la Communauté de cet article.
-   Pour atteindre un public plus large, vous pouvez publier une question sur le [Forum MSDN de Cache Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=azurecache) et faites appel à l’équipe de Cache Azure et d’autres membres de la Communauté.
-   Si vous souhaitez créer une demande de fonctionnalité, vous pouvez envoyer vos demandes et des idées à [Azure Redis Cache utilisateur voix](https://feedback.azure.com/forums/169382-cache).
-   Vous pouvez également envoyer un message électronique pour nous évaluations [externes Azure Cache](mailto:azurecache@microsoft.com).

## <a name="azure-redis-cache-basics"></a>Concepts de base du Cache Redis Azure

Le Forum aux questions de cette section connaître certains des concepts de base du Cache Redis Azure.

-    [Quel est le Cache Redis Azure ?](#what-is-azure-redis-cache)
-    [Comment puis-je prendre en main Cache Redis Azure ?](#how-can-i-get-started-with-azure-redis-cache)

Le Forum aux questions suivantes traitent de concepts de base et des questions concernant Azure Redis Cache et réponses dans une des autres sections du Forum aux questions.

-   [Quelle offre Redis Cache et la taille dois-je utiliser ?](#what-redis-cache-offering-and-size-should-i-use)
-   [Les clients de cache Redis puis-je utiliser ?](#what-redis-cache-clients-can-i-use)
-   [Existe-t-il un émulateur pour Azure Redis Cache local ?](#is-there-a-local-emulator-for-azure-redis-cache)
-   [Comment surveiller l’intégrité et les performances de mon cache ?](#how-do-i-monitor-the-health-and-performance-of-my-cache)



## <a name="planning-faqs"></a>Planification des questions fréquentes

-   [Quelle offre Redis Cache et la taille dois-je utiliser ?](#what-redis-cache-offering-and-size-should-i-use)
-   [Performances Redis Cache Azure](#azure-redis-cache-performance)
-   [Dans quelle région dois-je trouver mon cache ?](#in-what-region-should-i-locate-my-cache)
-   [Comment de facturation pour le Cache Redis Azure ?](#how-am-i-billed-for-azure-redis-cache)
-   [Puis-je utiliser Azure Redis Cache avec Azure pour le gouvernement Cloud ou Azure Chine Cloud ?](#can-i-use-azure-redis-cache-with-azure-government-cloud-or-azure-china-cloud)



## <a name="development-faqs"></a>Forum aux questions de développement

-   [En quoi consistent les options de configuration StackExchange.Redis ?](#what-do-the-stackexchangeredis-configuration-options-do)
-   [Les clients de cache Redis puis-je utiliser ?](#what-redis-cache-clients-can-i-use)
-   [Existe-t-il un émulateur pour Azure Redis Cache local ?](#is-there-a-local-emulator-for-azure-redis-cache)
-   [Comment puis-je exécuter Redis commandes ?](#how-can-i-run-redis-commands)
-   [Pourquoi ne Cache Redis Azure pas une référence de bibliothèque de classes MSDN tels que les autres services Azure ?](#why-doesnt-azure-redis-cache-have-an-msdn-class-library-reference-like-some-of-the-other-azure-services)
-   [Puis-je utiliser Azure Redis Cache comme un cache de session PHP ?](#can-i-use-azure-redis-cache-as-a-php-session-cache)


## <a name="security-faqs"></a>FAQ sur la sécurité

-   [Quand dois-je activer le port non SSL pour se connecter à Redis ?](#when-should-i-enable-the-non-ssl-port-for-connecting-to-redis)


## <a name="production-faqs"></a>Forum aux questions de production

-   [Quelles sont certaines meilleures pratiques de production ?](#what-are-some-production-best-practices)
-   [Quelles sont les considérations lors de l’utilisation des commandes courantes Redis ?](#what-are-some-of-the-considerations-when-using-common-redis-commands)
-   [Comment puis-je évaluer et tester les performances de mon cache ?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
-   [Informations importantes sur pool croissance](#important-details-about-threadpool-growth)
-   [Activer le catalogue global serveur obtenir plus de débit sur le client lors de l’utilisation de StackExchange.Redis](#enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis)


## <a name="monitoring-and-troubleshooting-faqs"></a>Analyse et dépannage des questions fréquentes

Le Forum aux questions de cette section garde courantes de surveillance et de dépannage des questions. Pour plus d’informations sur la surveillance et la résolution des problèmes de vos instances de Cache Redis Azure, voir [comment contrôler Azure Redis Cache](cache-how-to-monitor.md) et [comment résoudre les problèmes de Cache Redis Azure](cache-how-to-troubleshoot.md).

-   [Comment surveiller l’intégrité et les performances de mon cache ?](#how-do-i-monitor-the-health-and-performance-of-my-cache)
-   [Cache diagnostics stockage paramètres de mon compte modifiés, où est passée ?](#my-cache-diagnostics-storage-account-settings-changed-what-happened)
-   [Pourquoi les diagnostics est activées pour certains cache nouveau mais pas d’autres personnes ?](#why-is-diagnostics-enabled-for-some-new-caches-but-not-others)
-   [Pourquoi est-ce que je vois des délais d’expiration ?](#why-am-i-seeing-timeouts)
-   [Pourquoi mon client a été déconnecté à partir du cache ?](#why-was-my-client-disconnected-from-the-cache)


## <a name="prior-cache-offering-faqs"></a>Forum aux questions préalable offre de Cache

-   [L’offre de Cache Azure correspond à mes besoins ?](#which-azure-cache-offering-is-right-for-me)


### <a name="what-is-azure-redis-cache"></a>Quel est le Cache Redis Azure ?

Cache Redis Azure est basé sur la source d’ouvrir populaires [Redis cache](http://redis.io). Il permet d’accéder à un cache de Redis sûr et dédié, géré par Microsoft et accessible à partir de n’importe quelle application dans Azure. Pour obtenir une présentation plus détaillée, consultez la page de produit [Azure Redis Cache](https://azure.microsoft.com/services/cache/) sur Azure.com.


### <a name="how-can-i-get-started-with-azure-redis-cache"></a>Comment puis-je prendre en main Cache Redis Azure ?

Il existe plusieurs façons, que vous pouvez commencer avec Azure Redis Cache.

-    Vous pouvez consulter un de nos didacticiels disponibles pour [.NET](cache-dotnet-how-to-use-azure-redis-cache.md), [ASP.NET](cache-web-app-howto.md), [Java](cache-java-get-started.md), [Node.js](cache-nodejs-get-started.md)et [Python](cache-python-get-started.md). 
-    Vous pouvez regarder [comment créer haute Performance à l’aide de Microsoft Azure Redis Cache Apps](https://azure.microsoft.com/documentation/videos/how-to-build-high-performance-apps-using-microsoft-azure-cache/).
-    Vous pouvez consulter la documentation du client pour les clients qui correspondent à la langue de développement de votre projet pour voir comment utiliser Redis. Il existe de nombreux clients Redis qui peuvent être utilisés avec Azure Redis Cache. Pour obtenir la liste de clients Redis, voir [http://redis.io/clients](http://redis.io/clients).


Si vous n’avez pas déjà un compte Azure, vous pouvez :

-    [Ouvrir un compte Azure gratuitement](/pricing/free-trial/?WT.mc_id=redis_cache_hero). Vous obtenez crédits pouvant être utilisées pour essayer services Azure payants. Même une fois que les crédits sont utilisés vers le haut, vous pouvez conserver le compte et utiliser les fonctionnalités et services Azure gratuits.
-    [Avantages d’abonné activer Visual Studio](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=redis_cache_hero). Votre abonnement MSDN vous donne crédits chaque mois que vous pouvez utiliser pour les services Azure payants.


<a name="cache-size"></a>
### <a name="what-redis-cache-offering-and-size-should-i-use"></a>Quelle offre Redis Cache et la taille dois-je utiliser ?
Chaque Cache Redis Azure offre différents niveaux de **taille**, **la bande passante**, **haute disponibilité**et options **SLA** .

Voici les considérations sur la sélection d’une offre de Cache.

-   **Mémoire**: couches Basic et Standard offrent 250 Mo – 53 go. La couche Premium offre jusqu'à 530 Go avec plus disponible [à la demande](mailto:wapteams@microsoft.com?subject=Redis%20Cache%20quota%20increase). Pour plus d’informations, voir [Azure Redis Cache tarifs](https://azure.microsoft.com/pricing/details/cache/).
-   **Les performances du réseau**: Si vous avez une charge de travail qui requiert haut débit la couche Premium offre davantage de bande passante par rapport à Standard ou de base. Au sein de chaque niveau cache tailles plus grandes comporter plus de bande passante en raison de la machine virtuelle sous-jacente qui héberge le cache. Reportez-vous au [tableau suivant](#cache-performance) pour plus d’informations.
-   **Débit**: niveau Premium l’offre le débit disponible maximal. Si le serveur de cache ou le client atteint la limite de bande passante, vous recevrez des délais d’expiration du côté client. Pour plus d’informations, voir le tableau suivant.
-   **Haute disponibilité/SLA**: Azure Redis Cache garantit qu’un cache Standard/Premium sera disponible au moins 99,9 % du temps. Pour en savoir plus sur notre délai d’intervention, voir [Azure Redis Cache tarifs](https://azure.microsoft.com/support/legal/sla/cache/v1_0/). Le contrat SLA couvre uniquement la connectivité aux points de terminaison du Cache. Le contrat SLA n’aborde pas la protection contre la perte de données. Nous recommandons d’utiliser la fonctionnalité persistance Redis des données dans la couche Premium pour accroître la résilience contre la perte de données.
-   **Permanence des données redis**: Premium le montage en cascade permet vous permettent de conserver les cache des données dans un compte de stockage Azure. Toutes les données se trouve dans un cache Basic/Standard uniquement en mémoire. En cas d’infrastructure sous-jacente problèmes il peuvent être perte de données potentielle. Nous recommandons d’utiliser la fonctionnalité persistance Redis des données dans la couche Premium pour accroître la résilience contre la perte de données. Cache Redis Azure propose relationnelle et UNEDE (bientôt disponible) des options dans la persistance Redis. Pour plus d’informations, voir [comment configurer persistance Premium Azure Redis cache](cache-how-to-premium-persistence.md).
-   **Cluster Redis**: créer met en le cache supérieure à 53 Go ou aux données partagé entre plusieurs nœuds Redis, vous pouvez utiliser Redis cluster, qui est disponible dans le niveau Premium. Chaque nœud se compose d’une paire de cache principal/réplica de disponibilité. Pour plus d’informations, voir [comment configurer cluster Premium Azure Redis cache](cache-how-to-premium-clustering.md).
-   **Sécurité amélioré et isolement réseau**: déploiement d’Azure virtuel réseau (VNET) fournit une sécurité renforcée et isolement pour votre Cache Redis Azure, ainsi que les sous-réseaux, stratégies de contrôle d’accès, et d’autres fonctionnalités supplémentaires pour limitent l’accès. Pour plus d’informations, voir [comment configurer la prise en charge de réseau virtuel Premium Azure Redis cache](cache-how-to-premium-vnet.md).
-   **Configurer Redis**: dans les niveaux Standard et Premium, vous pouvez configurer Redis pour les notifications de Keyspace.
-   **Nombre maximal de connexions client**: niveau Premium l’offre le nombre maximal de clients qui peuvent se connecter à Redis, avec un nombre élevé de connexions de cache de taille supérieure. [Veuillez reportez-vous à la page tarification pour plus d’informations](https://azure.microsoft.com/pricing/details/cache/).
-   **Core dédié pour serveur Redis**: dans le niveau Premium toutes les tailles de cache ont un cœur dédié pour Redis. Dans la norme/Basic niveaux la C1 dimensionner et ci-dessus ont un cœur dédié pour Redis server.
-   **Redis est à thread unique** pour avoir plus de deux cœurs ne fournit pas d’avantages supplémentaires par rapport aux deux cœurs, mais volumineux machine virtuelle ont généralement plus de bande passante que de plus petite taille. Si le serveur de cache ou le client atteint la limite de bande passante, vous recevrez des délais d’expiration du côté client.
-   **Améliorations des performances**: cache dans la couche Premium est déployés sur matériel qui comporte des processeurs plus rapides et vous permet de meilleures performances par rapport à la couche Basic ou Standard. Cache de niveau Premium ont un débit supérieur et inférieur latence.

<a name="cache-performance"></a>
### <a name="azure-redis-cache-performance"></a>Performances Redis Cache Azure

Le tableau suivant indique les valeurs de bande passante maximale observées pendant la vérification des différentes tailles de Standard et Premium met en cache à l’aide de `redis-benchmark.exe` à partir d’un VM Iaas contre le point de terminaison Azure Redis Cache. Notez que ces valeurs ne sont pas garanties et il y a pas de SLA pour ces nombres mais doivent être classique. Vous devez charger tester votre propre application pour déterminer la taille de cache appropriée pour votre application.

À partir de ce tableau, nous pouvons dessiner conclusions suivantes.

-   Débit pour le cache ont la même taille est supérieur au niveau Premium par rapport à la couche Standard. Par exemple, avec un Cache Go 6, débit de P1 est 140K RPS par rapport aux 49 K pour C3.
-   Avec Redis cluster, débit augmente de manière linéaire à mesure que vous augmentez le nombre de milieu des fragments (nœuds) dans le cluster. Par exemple, si vous créez un cluster P4 de 10 milieu des fragments, le débit disponible est 250K * 10 = 2,5 millions de RPS.
-   Débit pour les tailles de clé plus grandes est plus élevé dans le niveau Premium par rapport à la couche Standard.

| Niveau de tarification             | Taille   | Cœurs de processeur | Bande passante disponible                                    | Taille de la clé 1 Ko                            |
|--------------------------|--------|-----------|--------------------------------------------------------|------------------------------------------|
| **Taille de cache standard** |        |           | **Mégabits par seconde (Mo/s) / mégaoctets par seconde (Mo/s)** | **Nombre de requêtes par seconde**            |
| C0                       | 250 MO | Partagé    | 5 / 0.625                                              | 600                                      |
| C1                       | 1 GO   | 1         | 100 / 12,5                                             | 12200                                    |
| C2                       | 2,5 GO | 2         | 200 / 25                                               | 24000                                    |
| C3                       | 6 GO   | 4         | 400 / 50                                               | 49000                                    |
| C4                       | 13 GO  | 2         | 500 / 62,5                                             | 61000                                    |
| C5                       | GO 26  | 4         | 1000 / 125                                             | 115000                                   |
| C6                       | GO 53  | 8         | 2000 / 250                                             | 150000                                   |
| **Taille de cache Premium**  |        | **Cœurs d’UC par partagé**  |                                         | **Nombre de requêtes par seconde, par partagé** |
| P1                       | 6 GO   | 2         | 1000 / 125                                             | 140000                                   |
| P2                       | 13 GO  | 4         | 2000 / 250                                             | 220000                                   |
| P3                       | GO 26  | 4         | 2000 / 250                                             | 220000                                   |
| P4                       | GO 53  | 8         | et 4 000 / 500                                             | 250000                                   |


Pour plus d’informations sur le téléchargement tels que les outils Redis `redis-benchmark.exe`, voir la [Comment puis-je exécuter Redis commandes ?](#cache-commands) section.

<a name="cache-region"></a>
### <a name="in-what-region-should-i-locate-my-cache"></a>Dans quelle région dois-je trouver mon cache ?

Pour optimiser les performances et la latence les plus faibles, recherchez le Cache Redis Azure dans la même région que votre application cliente cache.

<a name="cache-billing"></a>
### <a name="how-am-i-billed-for-azure-redis-cache"></a>Comment de facturation pour le Cache Redis Azure ?

Tarifs de Cache Redis Azure est [ici](https://azure.microsoft.com/pricing/details/cache/). La page tarification répertorie les tarifs à un taux horaire. Cache est facturés sur une base par minute entre le moment où le cache est créé jusqu’au moment où un cache est supprimé. Il n’existe aucune option pour arrêter ou suspendre la facturation d’un cache.


## <a name="can-i-use-azure-redis-cache-with-azure-government-cloud-or-azure-china-cloud"></a>Puis-je utiliser Azure Redis Cache avec Azure pour le gouvernement Cloud ou Azure Chine Cloud ?

Oui, Azure Redis Cache est disponible dans Azure pour le gouvernement Cloud et Azure Chine Cloud. Notez que les URL d’accès et de gestion du Cache de Redis Azure sont différents dans Azure pour le gouvernement Cloud et Azure Chine Cloud par rapport à nuage Public Azure. Pour plus d’informations sur les considérations relatives à l’aide de Cache Redis Azure avec Azure pour le gouvernement Cloud et Azure Chine Cloud, voir [Bases de données pour le gouvernement de Azure - Azure Redis Cache](../azure-government/documentation-government-services-database.md#azure-redis-cache) et [Azure Chine Cloud - Azure Redis Cache](https://www.azure.cn/documentation/services/redis-cache/).

Pour plus d’informations sur l’utilisation de Cache Redis Azure avec PowerShell dans Azure pour le gouvernement Cloud et Azure Chine Cloud, Découvrez [comment vous connecter à Azure pour le gouvernement Cloud ou Azure Chine Cloud](cache-howto-manage-redis-cache-powershell.md#how-to-connect-to-azure-government-cloud-or-azure-china-cloud).


<a name="cache-configuration"></a>
### <a name="what-do-the-stackexchangeredis-configuration-options-do"></a>En quoi consistent les options de configuration StackExchange.Redis ?

StackExchange.Redis dispose de nombreuses options. Cette section présente une partie des paramètres courants. Pour plus d’informations sur StackExchange.Redis options, voir [configuration StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Configuration.md).

ConfigurationOptions|Description|Recommandation
---|---|---
AbortOnConnectFail|Lorsque la valeur true, la connexion se reconnectera pas après une défaillance réseau.|La valeur false et laisser StackExchange.Redis vous reconnecter automatiquement.
ConnectRetry|Le nombre de répétitions de tentatives de connexion pendant initiale se connecter.| Consultez les remarques ci-dessous pour obtenir des instructions. |
ConnectTimeout|Délai d’attente en ms pour les opérations de connexion.| Consultez les remarques ci-dessous pour obtenir des instructions. |

Dans la plupart des cas, les valeurs par défaut du client suffisent. Vous pouvez régler les options en fonction de votre charge de travail.

-   **Nouvelles tentatives**
    -   Pour ConnectRetry et ConnectTimeout les instructions générales fournies pour consiste à échouer rapidement, puis réessayez. Ceci est basé sur votre charge de travail et combien de temps sur moyenne prend votre client exécuter une commande Redis et recevoir une réponse.
    -   Laisser StackExchange.Redis vous reconnecter automatiquement au lieu de vérifier le statut de connexion et que vous vous reconnectez vous-même. **Évitez d’utiliser la propriété ConnectionMultiplexer.IsConnected**.
    -   Snowballing - parfois que vous pouvez rencontrer un problème lorsque vous réessayer et cette snowballs et fait jamais. Dans ce cas, vous devez envisager en utilisant un algorithme de réessayer exponentielle intervalle comme décrit dans les [instructions générales fournies pour de nouvelles](best-practices-retry-general.md) publiés par le groupe Microsoft Patterns et pratiques.
-   **Valeurs de délai d’attente**
    -   Envisagez de votre charge de travail, puis définissez les valeurs en conséquence. Si vous stockez des valeurs élevées, définissez le délai d’expiration sur une valeur supérieure.
    -   Définir `AbortOnConnectFail` à false et permettre aux StackExchange.Redis vous reconnecter à votre place.
    -   Utiliser une seule instance ConnectionMultiplexer pour l’application. Vous pouvez utiliser une LazyConnection pour créer une instance unique qui est retournée par une propriété de connexion, comme indiqué dans [se connecter au cache à l’aide de la classe ConnectionMultiplexer](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache).
    -   Définir la `ConnectionMultiplexer.ClientName` propriété pour un nom unique de l’instance application fins de diagnostic.
    -   Utiliser plusieurs `ConnectionMultiplexer` instances de charges de travail personnalisées.
    -   Vous pouvez suivre ce modèle si vous avez variation de charge dans votre application. Par exemple :
    -   Vous pouvez avoir une multiplexage en matière de gestion clés étendues.
    -   Vous pouvez avoir une multiplexage en matière de gestion petites clés.
    -   Vous pouvez définir différentes valeurs pour les délais de connexion et réessayer de logique pour chaque ConnectionMultiplexer que vous utilisez.
    -   Définir la `ClientName` propriété sur chaque multiplexage pour vous aider à diagnostics.
    -   Cela vous aboutissez à plus rationalisée latence par `ConnectionMultiplexer`.

### <a name="what-redis-cache-clients-can-i-use"></a>Les clients de cache Redis puis-je utiliser ?

Un des avantages de Redis est qu’il existe de nombreux clients prenant en charge plusieurs langues de développement différents. Pour connaître la liste de clients, voir [Redis clients](http://redis.io/clients). Pour des didacticiels couvrant plusieurs clients et des langues différentes, voir [comment utiliser Azure Redis Cache](cache-dotnet-how-to-use-azure-redis-cache.md) , puis cliquez sur la langue souhaitée dans le sélecteur de langue dans la partie supérieure de l’article.

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

<a name="cache-emulator"></a>
### <a name="is-there-a-local-emulator-for-azure-redis-cache"></a>Existe-t-il un émulateur pour Azure Redis Cache local ?

Il n’existe aucun émulateur pour Azure Redis Cache local, mais vous pouvez exécuter la version MSOpenTech de redis server.exe à partir de la [Redis des outils de ligne de commande](https://github.com/MSOpenTech/redis/releases/) sur votre ordinateur local et s’y connecter pour obtenir une expérience similaire à un émulateur cache local, comme le montre l’exemple suivant.


    private static Lazy<ConnectionMultiplexer>
        lazyConnection = new Lazy<ConnectionMultiplexer>
        (() =>
        {
            // Connect to a locally running instance of Redis to simulate a local cache emulator experience.
            return ConnectionMultiplexer.Connect("127.0.0.1:6379");
        });
    
        public static ConnectionMultiplexer Connection
        {
            get
            {
                return lazyConnection.Value;
            }
        }


Vous pouvez éventuellement configurer un fichier [redis.conf](http://redis.io/topics/config) pour mieux correspondent aux [paramètres de cache par défaut](cache-configure.md#default-redis-server-configuration) pour le Cache de Redis Azure en ligne si vous le souhaitez.

<a name="cache-commands"></a>
### <a name="how-can-i-run-redis-commands"></a>Comment puis-je exécuter Redis commandes ?

Vous pouvez utiliser les commandes répertoriés au [Redis des commandes](http://redis.io/commands#) à l’exception des commandes répertoriées en [Redis commandes non prises en charge dans Azure Redis Cache](cache-configure.md#redis-commands-not-supported-in-azure-redis-cache). Pour exécuter les commandes Redis vous disposez de plusieurs options.

-   Si vous avez un cache Standard ou Premium, vous pouvez exécuter les commandes Redis à l’aide de la [Console Redis](cache-configure.md#redis-console). Cette offre un moyen sécurisé pour exécuter des commandes Redis dans le portail Azure.
-   Vous pouvez également utiliser les outils de ligne de commande Redis. Pour les utiliser, procédez comme suit.
-   Téléchargez les [Outils de ligne de commande de Redis](https://github.com/MSOpenTech/redis/releases/).
-   Se connecter à l’aide de cache `redis-cli.exe`. Passez le point de terminaison cache à l’aide de que basculer la -h et la clé en utilisant - un comme le montre l’exemple suivant.
-   `redis-cli -h <your cache="" name="">
.redis.cache.windows.net -a <key>
  `
  - Notez que les outils de ligne de commande Redis ne fonctionnent pas avec le port SSL, mais vous pouvez utiliser un utilitaire tel que `stunnel` connecter en toute sécurité les outils pour le port SSL en suivant les instructions dans le billet de blog [Annonce ASP.NET Session State Provider pour Redis (version préliminaire)](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx) .

<a name="cache-reference"></a>
### <a name="why-doesnt-azure-redis-cache-have-an-msdn-class-library-reference-like-some-of-the-other-azure-services"></a>Pourquoi ne Cache Redis Azure pas une référence de bibliothèque de classes MSDN tels que les autres services Azure ?

Microsoft Azure Redis Cache est basé sur la source ouverte populaires Redis Cache et sont accessibles par un large éventail de [Redis clients](http://redis.io/clients) qui sont disponibles pour de nombreuses langues de programmation. Chaque client dispose de sa propre API qui effectue des appels à l’instance de cache Redis à l’aide de [Redis commandes](http://redis.io/commands).

Étant donné que chaque client est différent, il n’est pas une référence de classe centralisé sur MSDN ; à la place chaque client conserve sa propre documentation de référence. Outre la documentation de référence, il existe plusieurs didacticiels montrant comment prise en main Cache Redis Azure à l’aide de différentes langues et clients du cache. Pour accéder à ces didacticiels, voir [comment utiliser Azure Redis Cache](cache-dotnet-how-to-use-azure-redis-cache.md) et cliquez sur la langue souhaitée dans le sélecteur de langue dans la partie supérieure de l’article.


### <a name="can-i-use-azure-redis-cache-as-a-php-session-cache"></a>Puis-je utiliser Azure Redis Cache comme un cache de session PHP ?

Oui, pour utiliser Azure Redis Cache comme un cache de session PHP, spécifiez la chaîne de connexion à votre instance Azure Redis Cache dans `session.save_path`.

>[AZURE.IMPORTANT] Lorsque vous utilisez Azure Redis Cache comme un cache de session PHP, vous devez URL coder la clé de sécurité utilisée pour la connexion dans le cache, comme illustré dans l’exemple suivant.
>
>`session.save_path = "tcp://mycache.redis.cache.windows.net:6379?auth=<url encoded primary or secondary key here>";`
>
>Si la clé n’est pas encoder au format URL, vous pouvez recevoir une exception semblable à ce qui suit :`Failed to parse session.save_path`

Pour plus d’informations sur l’utilisation de Cache Redis comme un cache de session PHP avec le client PhpRedis, voir [gestionnaire PHP Session](https://github.com/phpredis/phpredis#php-session-handler).



<a name="cache-ssl"></a>
### <a name="when-should-i-enable-the-non-ssl-port-for-connecting-to-redis"></a>Quand dois-je activer le port non SSL pour se connecter à Redis ?

Redis serveur ne prend pas en charge SSL prêts à l’emploi, contrairement à Azure Redis Cache. Si vous vous connectez à Azure Redis Cache et que votre client prend en charge SSL, comme StackExchange.Redis, vous devez utiliser SSL.

Notez que le port non SSL est désactivé par défaut pour les nouvelles instances de Cache Redis Azure. Si votre client ne prend pas en charge SSL, vous devez activer le port non SSL en suivant les instructions de la section [ports d’accès](cache-configure.md#access-ports) de l’article [configurer un cache dans Azure Redis Cache](cache-configure.md) .

Redis outils tels que `redis-cli` ne fonctionnent pas avec le port SSL, mais vous pouvez utiliser un utilitaire tel que `stunnel` connecter en toute sécurité les outils pour le port SSL en suivant les instructions dans le billet de blog [Annonce ASP.NET Session State Provider pour Redis (version préliminaire)](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx) .

Pour obtenir des instructions sur le téléchargement les outils Redis, consultez la [Comment puis-je exécuter Redis commandes ?](#cache-commands) section.



### <a name="what-are-some-production-best-practices"></a>Quelles sont certaines meilleures pratiques de production ?

-   [Meilleures pratiques StackExchange.Redis](#stackexchangeredis-best-practices)
-   [Concepts de configuration et](#configuration-and-concepts)
-   [Tests de performances](#performance-testing)

#### <a name="stackexchangeredis-best-practices"></a>Meilleures pratiques StackExchange.Redis

-   Définir `AbortConnect` sur false, puis laissez la ConnectionMultiplexer vous reconnecter automatiquement. [Pour plus d’informations, voir ici](https://gist.github.com/JonCole/36ba6f60c274e89014dd#file-se-redis-setabortconnecttofalse-md).
-   Réutiliser la ConnectionMultiplexer : ne créez pas un pour chaque demande. La `Lazy<ConnectionMultiplexer>` modèle [illustré ici](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache) est fortement recommandé.
-   Redis fonctionne mieux avec des valeurs plus petites, donc envisager hachage des données plus grandes en plusieurs clés. Dans [cette Redis discussion](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ), 100 Ko est considéré comme « étendu ». Lisez [cet article](https://gist.github.com/JonCole/db0e90bedeb3fc4823c2#large-requestresponse-size) pour un problème d’exemple qui peut être dû à des valeurs élevées.
-   Configurer vos [paramètres de pool](#important-details-about-threadpool-growth) pour éviter des délais d’expiration.
-   Utilisez au moins le connectTimeout par défaut de 5 secondes. Ceci lui StackExchange.Redis suffisamment de temps pour rétablir la connexion, dans le cas d’un spots réseau.
-   N’oubliez pas des coûts de performances associés à différentes opérations que vous exécutez. Par exemple, le `KEYS` commande est une opération o (n) et doit être évitée. Le [site redis.io](http://redis.io/commands/) contient des détails autour de la complexité du temps pour chaque opération pris en charge. Cliquez sur chaque commande pour afficher la complexité pour chaque opération.

#### <a name="configuration-and-concepts"></a>Concepts de configuration et

-   Utilisez Standard ou niveau Premium pour les systèmes de Production. Le niveau de base est un système de nœud unique avec aucune réplication de données et aucune SLA. Par ailleurs, utilisez au moins un cache C1. Cache C0 est vraiment destinées aux scénarios de développement/test simple.
-   N’oubliez pas que Redis est un magasin de données **En mémoire** . Lisez [cet article](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) afin que vous ayez connaissance des scénarios où la perte de données peut se produire.
-   Développer votre système tel qu’il peut gérer [en raison de correctifs et basculement](https://gist.github.com/JonCole/317fe03805d5802e31cfa37e646e419d#file-azureredis-patchingexplained-md)l’option connexion les blips.

#### <a name="performance-testing"></a>Tests de performances

-   Démarrer à l’aide de `redis-benchmark.exe` pour avoir une idée de débit possible avant d’écrire votre propre performance tests. Remarque Ce point de référence redis ne prend pas en charge SSL, afin que vous avez pour [Activer le port Non-SSL via le portail Azure](cache-configure.md#access-ports) avant de vous exécuterez le test. Pour obtenir des exemples, voir [Comment puis-je évaluer et tester les performances de mon cache ?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
-   Le client utilisé pour le test de mémoire virtuelle doit être dans la même région en tant que votre instance de cache Redis.
-   Nous recommandons d’utiliser Dv2 machine virtuelle série pour votre client, car elles ont une meilleure matériel et donnent de meilleurs résultats.
-   Assurez-vous que votre client machine virtuelle vous choisissez comporte au moins autant computing et fonctionnalité de bande passante en tant que le cache de test. 
-   Activer VRSS sur l’ordinateur client si vous êtes sous Windows. [Pour plus d’informations, voir ici](https://technet.microsoft.com/library/dn383582.aspx).
-   Instances de Redis niveau Premium seront ont mieux réseau latence et un débit, car ils exécutent le meilleur matériel pour processeur et réseau.

<a name="cache-redis-commands"></a>
### <a name="what-are-some-of-the-considerations-when-using-common-redis-commands"></a>Quelles sont les considérations lors de l’utilisation des commandes courantes Redis ?

-   Vous ne devez pas exécuter certaines commandes Redis qui prendre beaucoup de temps pour terminer sans comprendre l’impact de ces commandes.
    -   Par exemple, ne sont pas exécutées la commande [clés](http://redis.io/commands/keys) en production comme cela peut prendre beaucoup de temps pour renvoyer selon le nombre de clés. Redis est un serveur seul thread et il traite les commandes une à la fois. Si vous avez d’autres commandes émises après clés, ils pas soient traités jusqu'à ce que Redis traite la commande clés. Le [site redis.io](http://redis.io/commands/) contient des détails autour de la complexité du temps pour chaque opération pris en charge. Cliquez sur chaque commande pour afficher la complexité pour chaque opération.
-   Les tailles de clé - dois-je utiliser clé/valeurs petites ou grandes clé/valeurs ? En règle générale, il dépend du scénario. Si votre scénario exige grandes clés puis vous pouvez ajuster le ConnectionTimeout et réessayer de valeurs et ajuster votre logique de nouvelle tentative. Du point de vue Redis server, avec des valeurs inférieures sont respectées pour de meilleures performances.
-   Cela ne signifie pas que vous ne pouvez pas stocker des valeurs plus grandes dans Redis ; Vous devez tenir compte les considérations suivantes. Latence sera plus élevé. Si vous avez un ensemble de données sont plus grandes et celui qui est plus petit, vous pouvez utiliser plusieurs instances ConnectionMultiplexer, chacun configuré avec un autre jeu de valeurs de délai d’expiration et réessayer, comme décrit dans la section précédente [que faire les options de configuration StackExchange.Redis](#cache-configuration) .



<a name="cache-benchmarking"></a>
### <a name="how-can-i-benchmark-and-test-the-performance-of-my-cache"></a>Comment puis-je évaluer et tester les performances de mon cache ?

-   [Activer les diagnostics de cache](cache-how-to-monitor.md#enable-cache-diagnostics) afin que vous pouvez [surveiller](cache-how-to-monitor.md) l’état du cache. Vous pouvez afficher les mesures dans le portail Azure et vous pouvez également [télécharger et passez en revue](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) les aide des outils de votre choix.
-   Vous pouvez utiliser redis benchmark.exe pour charger le test de votre serveur Redis.
-   Assurez-vous que la charge test client et le cache Redis se trouvent dans la même région.
-   Utiliser les redis cli.exe et surveiller le cache à l’aide de la commande Infos.
-   Si votre charge à l’origine de la fragmentation de mémoire haute vous devez évoluer vers une plus grande taille de cache.
-   Pour obtenir des instructions sur le téléchargement les outils Redis, consultez la [Comment puis-je exécuter Redis commandes ?](#cache-commands) section.

Voici un exemple d’utilisation redis benchmark.exe. Pour des résultats précis, exécutez cette commande à partir d’une machine virtuelle dans la même région en tant que le cache de résolution.

-   Test pipeline définir les demandes à l’aide d’une charge utile k 1

    redis benchmark.exe - h **yourcache**. redis.cache.windows.net- **yourAccesskey** -t définir - n 1000000 -d 1 024 - P 50
    
-   Les requêtes à l’aide d’une charge utile k 1 test pipeline GET. 
    Remarque : Exécuter l’ensemble de test indiqué ci-dessus tout d’abord à remplir cache
    
    redis benchmark.exe - h **yourcache**. redis.cache.windows.net- **yourAccesskey** -t GET -n 1000000 - d 1 024 - P 50

<a name="threadpool"></a>
### <a name="important-details-about-threadpool-growth"></a>Informations importantes sur pool croissance

Le pool de threads CLR inclut deux types de threads - « Collaborateur » et « Port de terminaison d’e/s » (également appelé IOCP) threads. 

-   Threads de travail sont utilisés lorsque pour des éléments tels que traitement `Task.Run(…)` ou `ThreadPool.QueueUserWorkItem(…)` méthodes. Ces threads sont également utilisés par divers composants du CLR lorsque travail doit se produisent sur un thread d’arrière-plan.
-   Threads IOCP sont utilisés lorsque IO asynchrone se produit (par exemple, lecture à partir du réseau). 

Le pool de threads fournit des nouveaux threads de travail ou threads de terminaison d’e/s à la demande (sans toute régulation) jusqu'à ce qu’il atteigne le paramètre « Minimale » pour chaque type de thread. Par défaut, le nombre minimal de threads est défini sur le nombre de processeurs sur un système. 

Une fois que le nombre d’existant (occupé) threads correspondances que le nombre « minimal » de threads, le pool sera limitation est la fréquence à laquelle injecte nouveaux threads à un thread par 500 millisecondes. Cela signifie que si votre système reçoit une rupture de travail nécessitant un thread IOCP, celle-ci traite acceptés très rapidement. Toutefois, si la rupture de travail n’est plus que le paramètre « Minimale » configuré, il sera certaines retard dans la partie du travail de traitement du pool attend d’une des deux situations suivantes se produit.

1. Un thread existant devient libre de traiter le travail.
1. Aucun thread existant ne devient gratuit pour 500 ms, pour un nouveau thread est créé.

En fait, cela signifie que lorsque le nombre de threads occupé (e) est supérieur à threads Min, vous probablement payez un délai de 500 ms avant que le trafic réseau est traité par l’application. En outre, il est important de noter que lorsqu’une thread existante reste inactive pendant plus de 15 secondes (selon ce que j’ai oublié), il sera nettoyé et ce cycle de croissance et retrait pouvez répéter.

Si nous examiner un exemple de message d’erreur de StackExchange.Redis (build 1.0.450 ou version ultérieure), vous verrez qu’il imprime maintenant les statistiques de pool (voir IOCP et collaborateur détails ci-dessous).

    System.TimeoutException: Timeout performing GET MyKey, inst: 2, mgr: Inactive, 
    queue: 6, qu: 0, qs: 6, qc: 0, wr: 0, wq: 0, in: 0, ar: 0, 
    IOCP: (Busy=6,Free=994,Min=4,Max=1000), 
    WORKER: (Busy=3,Free=997,Min=4,Max=1000)

Dans l’exemple ci-dessus, vous constatez que thread IOCP il existe 6 threads occupé (e) et le système est configuré pour autoriser 4 threads minimales. Dans ce cas, le client auriez probablement vu deux 500 ms retards car 6 > 4.

Notez que StackExchange.Redis pouvez atteinte délais si croissance d’IOCP ou collaborateur threads est limitée.

### <a name="recommendation"></a>Recommandation

Par conséquent, nous vous recommandons vivement que clients définir la valeur de configuration minimale des threads IOCP et de travail pour qu’elle soit supérieure à la valeur par défaut. Nous ne pouvons pas donner des indications unique sur ce que cette valeur doit être parce que la valeur correcte pour une application sera trop haut/bas pour une autre application. Ce paramètre peut également affecter les performances des autres composants d’applications complexes, pour chaque client a besoin affiner ce paramètre à leurs besoins spécifiques. Un bon point de départ est 200 ou 300, puis effectuez un test et redéfinir certains selon vos besoins.

Comment faire pour configurer ces paramètres :

-   Dans ASP.NET, utilisez le [paramètre de configuration « minIoThreads »][] sous la `<processModel>` élément de configuration dans web.config. Si vous exécutez à l’intérieur des sites Web Azure, ce paramètre n’est pas exposé via les options de configuration. Toutefois, vous devriez pouvoir toujours configuré ces par programme (voir ci-dessous) à partir de votre méthode Application_Start dans global.asax.cs.

> **Note importante :** la valeur spécifiée dans cet élément de configuration est un paramètre *par cœur* . Par exemple, si vous avez une machine 4 core et que vous souhaitez votre paramètre minIOThreads 200 en cours d’exécution, vous utiliseriez `<processModel minIoThreads="50"/>`.

-   En dehors d’ASP.NET, utilisez la [ThreadPool.SetMinThreads(...)](https://msdn.microsoft.com/library/system.threading.threadpool.setminthreads.aspx) API.

<a name="server-gc"></a>
### <a name="enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis"></a>Activer le catalogue global serveur obtenir plus de débit sur le client lors de l’utilisation de StackExchange.Redis

L’activation de catalogue global server pouvez optimiser le client et offrent de meilleures performances et débit lors de l’utilisation de StackExchange.Redis. Pour plus d’informations sur le serveur catalogue global et comment l’activer, consultez les articles suivants.

-   [Pour activer le catalogue global du serveur](https://msdn.microsoft.com/library/ms229357.aspx)
-   [Principes de base du nettoyage](https://msdn.microsoft.com/library/ee787088.aspx)
-   [Performances et le nettoyage](https://msdn.microsoft.com/library/ee851764.aspx)







<a name="cache-monitor"></a>
### <a name="how-do-i-monitor-the-health-and-performance-of-my-cache"></a>Comment surveiller l’intégrité et les performances de mon cache ?

Instances de Microsoft Azure Redis Cache peuvent être contrôlés dans le [portail Azure](https://portal.azure.com). Vous pouvez afficher métriques, épingler graphiques mesures pour la Startboard, personnaliser la plage de date et l’heure de surveillance graphiques, ajouter et supprimer des mesures dans les graphiques et définir des alertes lorsque certaines conditions sont remplies. Pour plus d’informations, voir [Moniteur Azure Redis Cache](cache-how-to-monitor.md).

La section **prise en charge + résolution des problèmes** de la cuillère Cache Redis **paramètres** contienne également plusieurs outils pour surveiller et dépanner votre cache. 

-   **Résoudre les problèmes** fournit des informations sur les stratégies pour résoudre les erreurs et problèmes courants.
-   **Journaux d’audit** fournit des informations sur les actions exécutées dans le cache. Vous pouvez également utiliser le filtrage pour développer cette vue pour inclure d’autres ressources.
-   **Santé des ressources** espions de la ressource et vous indique s’il s’exécute comme prévu. Pour plus d’informations sur le service de santé ressource Azure, voir [vue d’ensemble d’intégrité de ressource Azure](../resource-health/resource-health-overview.md).
-   **Nouvelle demande d’assistance** propose des options pour ouvrir une demande de support pour le cache.

Ces outils permettent de contrôler l’état de vos instances Azure Redis Cache et vous aider à gérer vos applications de mise en cache. Pour plus d’informations, voir [prise en charge et résolution des problèmes de paramètres](cache-configure.md#support-amp-troubleshooting-settings).

### <a name="my-cache-diagnostics-storage-account-settings-changed-what-happened"></a>Cache diagnostics stockage paramètres de mon compte modifiés, où est passée ?

Cache dans la même région et l’abonnement partage les mêmes paramètres de stockage de diagnostics et si la configuration est modifiés (diagnostics activé/désactivé ou en modifiant le compte de stockage) s’applique à tous les cache dans l’abonnement qui se trouvent dans cette zone. Si les paramètres des diagnostics pour le cache ont changé, vérifiez si les paramètres des diagnostics pour un autre cache dans le même abonnement et région ont changé. Vérifier consiste à afficher les journaux d’audit pour le cache de résolution pour un `Write DiagnosticSettings` événement. Pour plus d’informations sur l’utilisation des journaux d’audit, voir [Afficher les événements et des journaux d’audit](../monitoring-and-diagnostics/insights-debugging-with-events.md) et [Audit opérations avec le Gestionnaire de ressources](../resource-group-audit.md). Pour plus d’informations sur l’analyse des événements de Cache Redis Azure, voir [opérations et les alertes](cache-how-to-monitor.md#operations-and-alerts).

### <a name="why-is-diagnostics-enabled-for-some-new-caches-but-not-others"></a>Pourquoi les diagnostics est activées pour certains cache nouveau mais pas d’autres personnes ?

Cache dans la région et l’abonnement même partage les mêmes paramètres de stockage des diagnostics. Si vous créez un cache dans la même région et l’abonnement comme cache une autre contenant des diagnostics activé, diagnostics est activé sur le nouveau cache en utilisant les mêmes paramètres.


<a name="cache-timeouts"></a>
### <a name="why-am-i-seeing-timeouts"></a>Pourquoi est-ce que je vois des délais d’expiration ?

Délais de se produisent dans le client qui vous permet de communiquer avec Redis. Pour l’essentiel Redis server n’expire pas. Lorsqu’une commande est envoyée au serveur Redis, la commande est la file d’attente et Redis server finalement capte la commande et l’exécute. Toutefois le client pouvez délai d’expiration au cours du processus et si c’est le cas une exception est élevé sur le côté appelant. Pour plus d’informations sur la résolution des problèmes de délai d’attente, voir [résolution des problèmes de côté Client](cache-how-to-troubleshoot.md#client-side-troubleshooting) et [exceptions d’expiration StackExchange.Redis](cache-how-to-troubleshoot.md#stackexchangeredis-timeout-exceptions).


<a name="cache-disconnect"></a>
### <a name="why-was-my-client-disconnected-from-the-cache"></a>Pourquoi mon client a été déconnecté à partir du cache ?

Voici quelques souvent, déconnexion d’un cache.

-   Causes côté client
    -   L’application cliente a été redéployée.
    -   L’application cliente effectué une opération de mise à l’échelle.
        -   Dans le cas des Services Cloud ou Web Apps, cela peut être dû à la mise à l’échelle.
    -   La couche réseau côté client est modifié.
    -   Erreurs transitoires s’est produite dans le client ou dans les nœuds du réseau entre le client et le serveur.
    -   Les limites de seuil de bande passante ont été atteintes.
    -   Processeur de manière intensive opérations opération était trop longue pour terminer.
-   Causes côté serveur
    -   Dans le cache standard offrant, le service de Cache Redis Azure initié un basculement à partir du nœud principal vers le nœud secondaire.
    -   Azure a été correction de l’instance où le cache a été déployé
        -   Il peut s’agir des mises à jour du serveur Redis ou de maintenance machine virtuelle général.







### <a name="which-azure-cache-offering-is-right-for-me"></a>L’offre de Cache Azure correspond à mes besoins ?

>[AZURE.IMPORTANT]Selon l’année dernière [annonce](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/), service Service de Cache géré Azure et Azure dans le rôle Cache sera supprimée sur 30 novembre 2016. Nous vous recommandons consiste à utiliser [Azure Redis Cache](https://azure.microsoft.com/services/cache/). Pour plus d’informations sur la migration, voir [migrer à partir de Service de Cache gérées pour mettre en Cache Redis Azure](cache-migrate-to-redis.md).

### <a name="azure-redis-cache"></a>Cache Redis Azure
Cache Redis Azure est disponible à partir de la taille maximale de 53 Go et a un SLA de 99,9 % de disponibilité. La nouvelle [couche premium](cache-premium-tier-intro.md) propose des tailles jusqu'à 530 Go et prise en charge pour effectuer ces regroupements, VNET et persistance, avec un SLA 99,9 %.

Cache Redis Azure permet aux clients pour utiliser un cache Redis sûr et dédié, géré par Microsoft. Avec cette offre, vous obtenez tirer parti des riches fonctionnalités et réseau fournies par Redis et fiable d’hébergement et de contrôle de Microsoft.

Contrairement aux cache traditionnel qui traite uniquement des paires clé-valeur, Redis est connu pour ses types de données hautement performant. Redis également prend en charge en cours d’exécution atomiques opérations sur ces types, tels que l’ajout à une chaîne ; incrémenter la valeur d’un hachage ; pousser à une liste ; Calculez l’intersection définie, union et différence ; ou prise le membre avec un classement plus élevé dans un jeu trié. Autres fonctionnalités incluent la prise en charge des transactions, publication/sub, Lua l’écriture de script, touches avec une limité temps de vie et les paramètres de configuration pour rendre Redis se comportent davantage comme un cache traditionnel.

Un autre aspect clé Redis succès est le réseau correct, percutants libres intégré autour de celle-ci. Ceci est pris en compte dans l’ensemble centralisée de clients Redis disponibles dans plusieurs langues. Ainsi, qu’il soit utilisé par pratiquement n’importe quel charge de travail que vous deviez générer à l’intérieur d’Azure. 

Pour plus d’informations sur la prise en main Azure Redis Cache, voir [comment utiliser Azure Redis Cache](cache-dotnet-how-to-use-azure-redis-cache.md) et à la [documentation de Cache Redis Azure](https://azure.microsoft.com/documentation/services/redis-cache/).

### <a name="managed-cache-service"></a>Service de Cache gérée
[Gèrent Cache service est configuré pour être supprimée 30 novembre 2016.](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)

### <a name="in-role-cache"></a>Dans le rôle Cache
[Dans le rôle Cache est défini pour être supprimée 30 novembre 2016.](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)





[paramètre de configuration « minIoThreads »]: https://msdn.microsoft.com/library/vstudio/7w2sway1(v=vs.100).aspx
