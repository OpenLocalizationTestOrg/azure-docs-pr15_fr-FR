<properties 
    pageTitle="Comment résoudre les problèmes de Cache Redis Azure | Microsoft Azure" 
    description="Découvrez comment résoudre les problèmes courants avec Azure Redis Cache." 
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
    ms.date="10/25/2016" 
    ms.author="sdanie"/>

# <a name="how-to-troubleshoot-azure-redis-cache"></a>Comment résoudre les problèmes de Cache Redis Azure

Cet article fournit des conseils pour les catégories suivantes des problèmes Azure Redis Cache de résolution des problèmes.

-   [Résolution des problèmes de côté client](#client-side-troubleshooting) - cette section fournit des instructions sur l’identification et résolution des problèmes créés par l’application qui se connecte à mettre en Cache Redis Azure.
-   [Résolution des problèmes de côté serveur](#server-side-troubleshooting) - cette section fournit des instructions sur l’identification et résolution des problèmes dus côté serveur Azure Redis Cache.
-   [Exceptions de délai d’expiration StackExchange.Redis](#stackexchangeredis-timeout-exceptions) - cette section fournit des informations sur la résolution des problèmes lors de l’utilisation du client StackExchange.Redis.


>[AZURE.NOTE] Plusieurs étapes de dépannage dans ce guide comprennent les instructions pour exécuter les commandes Redis et surveiller diverses métriques de performances. Pour plus d’informations et des instructions, voir les articles dans la section [informations supplémentaires](#additional-information) .

## <a name="client-side-troubleshooting"></a>Résolution des problèmes de côté client


Cette section présente la résolution des problèmes qui se produisent en raison d’une condition sur l’application cliente.

-   [Sollicitation de la mémoire sur le client](#memory-pressure-on-the-client)
-   [Rupture de trafic](#burst-of-traffic)
-   [Client élevé de l’UC](#high-client-cpu-usage)
-   [Bande passante du côté client dépassée](#client-side-bandwidth-exceeded)
-   [Demande/réponse de grande taille](#large-requestresponse-size)
-   [Qu’est-il advenu mes données dans Redis ?](#what-happened-to-my-data-in-redis)

### <a name="memory-pressure-on-the-client"></a>Sollicitation de la mémoire sur le client

#### <a name="problem"></a>Problème

Sollicitation de la mémoire de l’ordinateur client permet d’accéder à tous les types de problèmes de performances qui peuvent retarder de traitement de données qui a été envoyées par l’instance Redis sans délai. Lorsque la pression de mémoire atteint, le système doit généralement données de la page de la mémoire physique de mémoire virtuelle qui se trouve sur le disque. Cette *page cas de défaillance* , le système ralentir considérablement.

#### <a name="measurement"></a>Mesure 

1.  Surveiller l’utilisation de la mémoire sur ordinateur pour vous assurer qu’elle ne dépasse pas la quantité de mémoire disponible. 
2.  Surveiller les `Page Faults/Sec` compteur de performance. La plupart des systèmes sera que certains défauts de page même normales, observez les pointes dans ce compteur de performance des erreurs de page qui correspondent à des délais d’expiration.

#### <a name="resolution"></a>Résolution

Mise à niveau de votre client pour un client plus grande taille de la mémoire virtuelle avec plus de mémoire ou attentivement vos habitudes de mémoire pour réduire la quantité de mémoire consuption.


### <a name="burst-of-traffic"></a>Rupture de trafic

#### <a name="problem"></a>Problème

Pointes de trafic combiné avec une mauvaise `ThreadPool` paramètres peuvent entraîner des retards de transformation des données déjà envoyé par le serveur Redis mais pas encore consommées sur le côté client.

#### <a name="measurement"></a>Mesure 

Moniteur comment votre `ThreadPool` statistiques changent au fil du temps à l’aide de code [comme suit](https://github.com/JonCole/SampleCode/blob/master/ThreadPoolMonitor/ThreadPoolLogger.cs). Vous pouvez également consulter la `TimeoutException` message de StackExchange.Redis. Voici un exemple :

    System.TimeoutException: Timeout performing EVAL, inst: 8, mgr: Inactive, queue: 0, qu: 0, qs: 0, qc: 0, wr: 0, wq: 0, in: 64221, ar: 0, 
    IOCP: (Busy=6,Free=999,Min=2,Max=1000), WORKER: (Busy=7,Free=8184,Min=2,Max=8191)

Dans le message ci-dessus, il existe plusieurs problèmes qui sont intéressants :

 1. Notez que dans la `IOCP` section et la `WORKER` section que vous avez un `Busy` valeur qui est supérieure à la `Min` valeur. Cela signifie que votre `ThreadPool` paramètres doivent ajuster.
 2. Vous pouvez également voir `in: 64221`. Cela signifie que 64211 octets ont été reçues au niveau de la couche de socket noyau mais n’avez pas encore été lu par l’application (par exemple, StackExchange.Redis). En règle générale, cela signifie que votre application n’est pas la lecture de données à partir du réseau aussi rapidement que le serveur est l’envoi à votre intention.

#### <a name="resolution"></a>Résolution

Configurer vos [Paramètres de pool](https://gist.github.com/JonCole/e65411214030f0d823cb) pour vous assurer que votre pool de threads mettra à l’échelle rapidement sous scénarios rupture.


### <a name="high-client-cpu-usage"></a>Client élevé de l’UC

#### <a name="problem"></a>Problème

Utilisation intensive du processeur sur le client indique que le système ne peut effectuer le travail qu’il a été invité à effectuer. Cela signifie que le client ne peut pas traiter une réponse Redis au moment opportun même si Redis a envoyé la réponse très rapidement.

#### <a name="measurement"></a>Mesure

Surveiller l’utilisation de l’UC large système par le biais du portail Azure ou le compteur de performance associé. En veillant à ne pas contrôler l’UC du *processus* , car un processus unique peut avoir faible utilisation de l’UC en même temps que global du système du processeur peut être élevé. Regardez les pointes de l’UC qui correspondent aux délais d’expiration. Résulte élevée du processeur, vous pouvez également voir haute `in: XXX` les valeurs dans `TimeoutException` comme décrit dans la section [rupture du trafic](#burst-of-traffic) des messages d’erreur.

>[AZURE.NOTE] StackExchange.Redis 1.1.603 et versions ultérieures incluent la `local-cpu` métrique dans `TimeoutException` messages d’erreur. Vérifiez que vous utilisez la dernière version du [package StackExchange.Redis NuGet](https://www.nuget.org/packages/StackExchange.Redis/). Il existe corrigés en permanence en cours dans le code pour le rendre plus performantes grâce aux délais d’expiration pour que la dernière version est important.

#### <a name="resolution"></a>Résolution

Mettre à niveau vers une plus grande taille machine virtuelle avec capacité de l’UC plus ou examiner la cause de l’UC. 



### <a name="client-side-bandwidth-exceeded"></a>Bande passante du côté client dépassée

#### <a name="problem"></a>Problème

Ordinateurs clients dimensionné différentes ont des limitations sur la bande passante réseau qu’ils ont disponibles. Si le client dépasse la bande passante disponible, puis données ne seront pas traitées sur le côté client aussi rapidement que le serveur est l’envoi. Cela peut entraîner des délais d’expiration.

#### <a name="measurement"></a>Mesure

Surveiller comment votre utilisation de la bande passante changent au fil du temps à l’aide de code [comme suit](https://github.com/JonCole/SampleCode/blob/master/BandWidthMonitor/BandwidthLogger.cs). Notez que ce code peut s’exécutent pas correctement dans certains environnements avec des autorisations restreintes (par exemple, les sites web Azure).

#### <a name="resolution"></a>Résolution 

Augmenter la taille de mémoire virtuelle Client ou réduire la consommation de bande passante réseau.


### <a name="large-requestresponse-size"></a>Demande/réponse de grande taille

#### <a name="problem"></a>Problème

Une demande/réponse volumineuse peuvent entraîner des délais d’expiration. Par exemple, supposons que votre valeur du délai d’attente configurée sur votre client est de 1 seconde. Votre application demande deux touches (par exemple, « A » et « B ») en même temps (à l’aide de la même connexion réseau physique). La plupart des clients prennent en charge « Pipelining » de requêtes, telles que les deux demandes « A » et « B » sont envoyées sur la connexion au serveur une après l’autre sans attendre que les réponses. Le serveur envoie les réponses dans le même ordre. Si la réponse « A » est grande suffisamment il peut occuper la plupart du délai d’expiration pour les demandes suivantes. 

L’exemple suivant illustre ce scénario. Dans ce scénario, demande « A » et « B » sont envoyés rapidement, le serveur commence à envoyer des réponses « A » et « B » rapidement, mais en raison de durées de transfert de données, « B » êtes bloqué derrière la demande et l’expire même si le serveur a répondu rapidement.

  	|-------- 1 Second Timeout (A)----------|
  	|-Request A-|
         |-------- 1 Second Timeout (B) ----------|
         |-Request B-|
                |- Read Response A --------|
                                           |- Read Response B-| (**TIMEOUT**)



#### <a name="measurement"></a>Mesure

Il s’agit d’un identifiant difficile à mesurer. Vous devez en fait instrument votre code client pour effectuer le suivi des réponses et les demandes de grande taille. 

#### <a name="resolution"></a>Résolution

1.  Redis est optimisé pour un grand nombre de petites valeurs, plutôt que quelques valeurs de grande taille. La meilleure solution consiste à décomposer vos données connexes plus petites valeurs. Voir la [Quelle est la plage de taille de la valeur idéale pour redis ? Est 100 Ko trop volumineux ?](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ) publier des détails autour pourquoi plus petites valeurs sont recommandés.
2.  Augmenter la taille de votre machine virtuelle (pour client et serveur de Cache Redis) pour obtenir une version ultérieure capacités de bande passante, réduire les temps de transfert de données pour les réponses plus grandes. Notez que l’obtention davantage de bande passante sur le serveur uniquement ou seulement sur le client ne peut pas être suffisamment. Mesurer votre utilisation de la bande passante et comparer les capacités de la taille de la machine virtuelle que vous avez actuellement.
3.  Augmenter le nombre de `ConnectionMultiplexer` objets utilisation et demandes cyclique via des connexions différentes.


### <a name="what-happened-to-my-data-in-redis"></a>Qu’est-il advenu mes données dans Redis ?

#### <a name="problem"></a>Problème

Je prévu pour certains dans mon instance Azure Redis Cache de données, mais il n’a pas été parviens habituel fait.

##### <a name="resolution"></a>Résolution

Voir [Mes données dans Redis est passée ?](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) pour causes et solutions possibles.


## <a name="server-side-troubleshooting"></a>Résolution des problèmes de côté serveur

Cette section présente la résolution des problèmes qui se produisent en raison d’une condition sur le serveur de cache.

-   [Sollicitation de la mémoire sur le serveur](#memory-pressure-on-the-server)
-   [Utilisation intensive du processeur / serveur charger](#high-cpu-usage-server-load)
-   [Bande passante du côté serveur dépassée](#server-side-bandwidth-exceeded)

### <a name="memory-pressure-on-the-server"></a>Sollicitation de la mémoire sur le serveur

#### <a name="problem"></a>Problème

Sollicitation de la mémoire côté serveur permet d’accéder à tous les types de problèmes de performances qui peuvent retarder le traitement des demandes. Lorsque la pression de mémoire atteint, le système doit généralement données de la page de la mémoire physique de mémoire virtuelle qui se trouve sur le disque. Cette *page cas de défaillance* , le système ralentir considérablement. Il existe plusieurs causes possibles de cette sollicitation de mémoire : 

1.  Vous avez rempli le cache à pleine capacité de données. 
2.  Redis voit fragmentation mémoire haute - souvent due au stockage d’objets de grande taille (Redis est optimisé pour une petite objets - voir les [Quelle est la plage de taille de la valeur idéale pour redis ? Est 100 Ko trop volumineux ?](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ) publier pour plus d’informations). 

#### <a name="measurement"></a>Mesure

Redis expose deux mesures qui peuvent vous aider à identifier ce problème. La première est `used_memory` et l’autre est `used_memory_rss`. [Les mesures](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) sont disponibles dans le portail Azure ou via la commande [Redis Infos](http://redis.io/commands/info) .

#### <a name="resolution"></a>Résolution

Il existe plusieurs éventuelles modifications que vous pouvez apporter afin de garder de l’utilisation de la mémoire :

1. [Configurer une stratégie de mémoire](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) et de définir des délais d’expiration sur vos clés. Notez que cela peut ne pas être suffisant si vous avez fragmentation.
2. [Configurer une valeur réservée maxmemory](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) est assez grande pour compense fragmentation de mémoire.
3. Divisez vos objets mis en cache volumineux dans le plus petit des objets associés.
4. [Échelle](cache-how-to-scale.md) à l' augmentation de la taille du cache.
5. Si vous utilisez un [cache premium avec cluster Redis activé](cache-how-to-premium-clustering.md) , vous pouvez [augmenter le nombre de milieu des fragments](cache-how-to-premium-clustering.md#change-the-cluster-size-on-a-running-premium-cache).

### <a name="high-cpu-usage--server-load"></a>Utilisation intensive du processeur / serveur charger

#### <a name="problem"></a>Problème

Utilisation intensive du processeur signifie que le côté client peut échouer traiter une réponse Redis au moment opportun même si Redis a envoyé la réponse très rapidement.

#### <a name="measurement"></a>Mesure

Surveiller l’utilisation de l’UC large système par le biais du portail Azure ou le compteur de performance associé. En veillant à ne pas contrôler l’UC du *processus* , car un processus unique peut avoir faible utilisation de l’UC en même temps que global du système du processeur peut être élevé. Regardez les pointes de l’UC qui correspondent aux délais d’expiration.

#### <a name="resolution"></a>Résolution

[Échelle](cache-how-to-scale.md) à un plus grand cache niveau avec capacité de l’UC plus ou examiner la cause de l’UC. 

### <a name="server-side-bandwidth-exceeded"></a>Bande passante du côté serveur dépassée

#### <a name="problem"></a>Problème

Instances de cache dimensionné différentes ont des limitations sur la bande passante réseau qu’ils ont disponibles. Si le serveur dépasse la bande passante disponible, données pas envoyées au client aussi rapidement. Cela peut entraîner des délais d’expiration.

#### <a name="measurement"></a>Mesure

Vous pouvez surveiller les `Cache Read` métrique, ce qui correspond à la quantité de données lues dans le cache en mégaoctets par seconde (Mo/s) au cours de l’intervalle de création de rapports spécifié. Cette valeur correspond à la bande passante réseau utilisée par ce cache. Si vous voulez définir des alertes pour les limites de la bande passante du réseau côté serveur, vous pouvez créer à l’aide de ce `Cache Read` compteur. Comparer vos lectures avec les valeurs de [ce tableau](cache-faq.md#cache-performance) pour les limites de bande passante observée pour cache différents niveaux et les tailles de tarification.

#### <a name="resolution"></a>Résolution

Si vous êtes toujours près de la bande passante maximale observée pour la couche et cache la taille tarification, envisagez [mise à l’échelle](cache-how-to-scale.md) sur une tarification niveau ou taille de bande passante réseau supérieure, en utilisant les valeurs dans [ce tableau](cache-faq.md#cache-performance) comme guide.


## <a name="stackexchangeredis-timeout-exceptions"></a>Exceptions de délai d’attente StackExchange.Redis

StackExchange.Redis utilise un paramètre nommé de configuration `synctimeout` pour les opérations synchrones qui a une valeur par défaut de 1 000 ms. Si un appel synchrone ne s’exécute dans le temps imparti, le client StackExchange.Redis génère une erreur de délai d’expiration similaire à l’exemple suivant.

    System.TimeoutException: Timeout performing MGET 2728cc84-58ae-406b-8ec8-3f962419f641, inst: 1,mgr: Inactive, queue: 73, qu=6, qs=67, qc=0, wr=1/1, in=0/0 IOCP: (Busy=6, Free=999, Min=2,Max=1000), WORKER (Busy=7,Free=8184,Min=2,Max=8191)


Ce message d’erreur contient des indicateurs qui peuvent vous aider à vous pointez sur la cause et trouver résolution du problème. Le tableau suivant contient des informations sur les métriques de message d’erreur.

| Métrique de message d’erreur | Plus d’informations                                                                                                                                                                                                                                          |
|------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| inst       | Dans la dernière tranche horaire : 0 commandes ont été émis                                                                                                                                                                                              |
| Directeur        | Le Gestionnaire de socket effectue `socket.select` qui signifie qu’il est demandant le système d’exploitation pour indiquer un socket qui a quelque chose à faire ; fondamentalement : le lecteur n’est pas lecture activement à partir du réseau, car il ne pensez il est lié à |
| file d’attente      | Il existe 73 total des opérations en cours                                                                                                                                                                                                        |
| qu         | 6 les opérations en cours dans la file d’attente non envoyé et n’ont pas encore été écrites au réseau sortant                                                                                                                                                           |
| QS         | 67 d’opérations d’en cours he ont été envoyés vers le serveur, mais une réponse n’est pas disponible. La réponse peut être `Not yet sent by the server` ou`sent by the server but not yet processed by the client.`                                                   |
| QC         | 0 des opérations en cours avez vu réponses mais n’ont pas encore été marqué comme étant terminée en raison de se trouvent sur la boucle de saisie semi-automatique                                                                                                                                      |
| wR         | Il y a un octets/activewriters writer actif (ce qui signifie que les demandes non envoyés 6 ne sont pas ignorés)                                                                                                                                                   |
| dans         | Il n’y a aucune lecteurs actives et zéro octet est disponibles pour la lecture sur la carte réseau octets/activereaders                                                                                                                                               |


### <a name="steps-to-investigate"></a>Étapes à suivre pour déterminer l’origine

1. Comme il est recommandé Assurez-vous que vous utilisez le modèle suivant pour vous connecter lorsque vous utilisez le client StackExchange.Redis.


        private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
        {
            return ConnectionMultiplexer.Connect("cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");
    
        });
    
        public static ConnectionMultiplexer Connection
        {
            get
            {
                return lazyConnection.Value;
            }
        }


    Pour plus d’informations, voir [se connecter au cache à l’aide de StackExchange.Redis](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache).

2. Assurez-vous que le Cache Redis Azure et l’application cliente sont dans la même région dans Azure. Par exemple, vous pouvez obtenir des délais d’expiration lorsque le cache est Extrême-Orient aux États-Unis, mais le client est aux États-Unis Ouest et la demande ne se termine dans le `synctimeout` intervalle ou vous pouvez obtenir des délais d’expiration lorsque vous effectuez le débogage à partir de votre ordinateur de développement local. 

    Il est recommandé de disposer du cache et dans le client dans la même région Azure. Si vous avez un scénario qui comprend des appels région croisée, vous devez définir la `synctimeout` intervalle sur une valeur supérieure à l’intervalle de 1 000 ms par défaut en incluant une `synctimeout` propriété dans la chaîne de connexion. L’exemple suivant montre un extrait de chaîne de connexion de cache StackExchange.Redis avec un `synctimeout` de ms 2000.

        synctimeout=2000,cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...

3. Vérifiez que vous utilisez la dernière version du [package StackExchange.Redis NuGet](https://www.nuget.org/packages/StackExchange.Redis/). Il existe corrigés en permanence en cours dans le code pour le rendre plus performantes grâce aux délais d’expiration pour que la dernière version est important.

4. S’il existe des demandes prise liées en limitations de bande passante sur le serveur ou le client, prendra plus de temps pour que les conditions d’achèvement et ce qui provoque des délais d’expiration. Pour voir si votre délai est en raison de la bande passante réseau sur le serveur, consultez [la bande passante du côté serveur dépassée](#server-side-bandwidth-exceeded). Pour voir si votre délai est en raison de la bande passante réseau de client, voir [la bande passante du côté Client dépassée](#client-side-bandwidth-exceeded).

6. Vous prise UC lié sur le serveur ou sur le client
    -   Vérifier si vous êtes prise lié par UC sur votre client qui peut entraîner la requête ne pas être traitées au sein du `synctimeout` intervalle, ce qui provoque un délai d’expiration. Déplacement vers une plus grande taille client ou répartir la charge peut vous aider à contrôler cette situation. 
    -   Vérifier si vous obtenez UC lié sur le serveur en analysant la `CPU` [métrique de performances du cache](cache-how-to-monitor.md#available-metrics-and-reporting-intervals). Requêtes entrantes tandis que Redis est lié au processeur peuvent entraîner ces demandes au délai d’attente. Pour contourner ce problème, vous pouvez répartir la charge sur plusieurs milieu des fragments dans un cache premium ou mettre à niveau vers une augmentation de la taille ou le niveau de tarification. Pour plus d’informations, voir [Dépassement de bande passante de côté serveur](#server-side-bandwidth-exceeded).

7. Existe-t-il des commandes prenant beaucoup de temps à traiter sur le serveur ? Commandes dont l’exécution prend beaucoup de temps à traiter sur le serveur redis la longue exécution peut entraîner des délais d’expiration. Voici quelques exemples de commandes longues `mget` avec un grand nombre de clés, `keys *` ou mal orthographié lua scripts. Vous pouvez vous connecter à votre instance Azure Redis Cache en utilisant le client redis-infrastructure du langage commun ou utiliser la [Console Redis](cache-configure.md#redis-console) et exécutez la commande [SlowLog](http://redis.io/commands/slowlog) pour déterminer s’il existe des demandes plus longue que prévu. Sur le serveur redis et StackExchange.Redis sont optimisés pour les nombreuses petites requêtes plutôt que moins grandes requêtes. Le fractionnement de vos données en petits ensembles peut améliorer les choses ici. 

    Pour plus d’informations sur la connexion au point de terminaison SSL en Cache Redis Azure à l’aide de redis-infrastructure du langage commun et stunnel, consultez le blog de [l’Annonce ASP.NET Session State Provider pour Redis (version préliminaire)](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx) . Pour plus d’informations, voir [SlowLog](http://redis.io/commands/slowlog).

8. Charge du serveur Redis haute peut entraîner des délais d’expiration. Vous pouvez contrôler la charge du serveur en analysant la `Redis Server Load` [métrique de performances du cache](cache-how-to-monitor.md#available-metrics-and-reporting-intervals). Un chargement de serveur de 100 (valeur maximale) indique que le serveur redis a été occupé (e), avec aucune durée d’inactivité, traitement des demandes. Pour voir si certaines requêtes occupent d’ensemble de la fonctionnalité de serveur, exécutez la commande SlowLog, comme décrit dans le paragraphe précédent. Pour plus d’informations, voir [de l’UC haute / serveur charger](#high-cpu-usage-server-load).

9. Était là ou tout autre événement côté client qui ont pu causer un spots réseau ? Vérifiez sur le client (web, rôle de collaborateur ou un Iaas VM) si un événement comme mise à l’échelle le nombre d’instances du client vers le haut ou vers le bas s’est produite, ou déployez une nouvelle version du client ou échelle automatique est activé ? Dans nos tests, que nous avons rencontré qu’échelle ou mise à l’échelle haut/bas peut entraîner la connectivité réseau sortant peut être perdue pendant quelques secondes. Code StackExchange.Redis est résistant à ces événements et se reconnectera. Pendant ce temps de reconnexion toutes les demandes dans la file d’attente peuvent expirer.

10. Était utilisé une demande volumineux précédant plusieurs requêtes petites au Cache Redis qui a expiré ? Le paramètre `qs` dans l’erreur du message vous indique le nombre de requêtes a été envoyé à partir du client sur le serveur, mais n’avez pas encore traité une réponse. Cette valeur peut cessent car StackExchange.Redis utilise une seule connexion TCP et peut en lecture seule une réponse à la fois. Même si la première opération a expiré, il n’empêche pas les données envoyées vers ou à partir du serveur et autres requêtes sont bloqués tant que cette opération terminée, à l’origine de délais. Une solution consiste à réduire les risques de délais d’expiration en garantissant que le cache est assez grand pour que votre charge de travail et fractionner des valeurs élevées en petits segments. Une autre solution possible consiste à utiliser un pool de `ConnectionMultiplexer` des objets dans votre client, puis choisissez le moins chargé `ConnectionMultiplexer` lors de l’envoi d’une nouvelle demande. Cela ne doit empêcher un seul délai à l’origine de délai d’expiration d’également d’autres requêtes.

11. Si vous utilisez `RedisSessionStateprovider`, vérifiez que vous avez correctement le délai d’expiration de réessayer. `retrytimeoutInMilliseconds`doit être supérieure à `operationTimeoutinMilliseonds`, sinon aucune nouvelle tentative se produit. Dans l’exemple suivant `retrytimeoutInMilliseconds` est définie sur 3000. Pour plus d’informations, consultez [Fournisseur d’état de Session ASP.NET pour Azure Redis Cache](cache-aspnet-session-state-provider.md) et [comment utiliser les paramètres de configuration du fournisseur d’état de Session et fournisseur de Cache de sortie](https://github.com/Azure/aspnet-redis-providers/wiki/Configuration).


    <add
      name="AFRedisCacheSessionStateProvider"
      type="Microsoft.Web.Redis.RedisSessionStateProvider"
      host="enbwcache.redis.cache.windows.net"
      port="6380"
      accessKey="…"
      ssl="true"
      databaseId="0"
      applicationName="AFRedisCacheSessionState"
      connectionTimeoutInMilliseconds = "5000"
      operationTimeoutInMilliseconds = "1000"
      retryTimeoutInMilliseconds="3000" />


12. Vérifier l’utilisation de la mémoire sur le serveur Azure Redis Cache en [analysant](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) `Used Memory RSS` et `Used Memory`. Si une stratégie de suppression est en place, Redis commence la suppression touches quand `Used_Memory` atteigne la taille du cache. Dans l’idéal, `Used Memory RSS` doivent être uniquement légèrement supérieur `Used memory`. Une différence importante signifie qu’il est fragmentation de la mémoire (internes ou externes. Lorsque `Used Memory RSS` est inférieure à `Used Memory`, cela signifie que la partie de la mémoire cache a été remplacé par le système d’exploitation. Dans ce cas, vous trouverez quelques latence significative. Car Redis n’a pas contrôler comment ses affectations sont mappées aux pages de mémoire, haute `Used Memory RSS` est souvent le résultat d’une pointe de l’utilisation de mémoire. Lorsque Redis libère de la mémoire, la mémoire est envoyée à l’allocation et l’allocation peut ou ne peut donner la mémoire revenir au système. Il peut y avoir une différence entre la `Used Memory` consommation de valeur et la mémoire comme indiqué par le système d’exploitation. Il peut s’avérer étant donné que la quantité de mémoire a été utilisée et publiée par Redis, mais pas donnée au système. Pour atténuer les problèmes de mémoire, vous pouvez effectuer les étapes suivantes.
    -   Mettre à niveau le cache vers une plus grande taille afin que vous exécutez pas respectivement limitations de la mémoire sur le système.
    -   Définir des délais d’expiration sur les clés afin qu’anciennes valeurs sont supprimées le fait.
    -   Surveiller les la `used_memory_rss` métrique en cache. Lorsque cette valeur est proche de la taille de leur cache, vous pouvez commencer à voir les problèmes de performances. Distribuer les données sur plusieurs milieu des fragments si vous utilisez un cache premium, ou mettre à niveau vers une augmentation de la taille du cache.

    Pour plus d’informations, voir [La pression de mémoire sur le serveur](#memory-pressure-on-the-server).

## <a name="additional-information"></a>Informations supplémentaires

-   [Quelle offre Redis Cache et la taille dois-je utiliser ?](cache-faq.md#what-redis-cache-offering-and-size-should-i-use)
-   [Comment puis-je évaluer et tester les performances de mon cache ?](cache-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
-   [Comment puis-je exécuter Redis commandes ?](cache-faq.md#how-can-i-run-redis-commands)
-   [Comment faire pour surveiller Azure Redis Cache](cache-how-to-monitor.md)