<properties
   pageTitle="Analyse de l’échec en mode | Microsoft Azure"
   description="Instructions pour effectuer une analyse de mode échec pour les solutions de cloud basées sur Azure."
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="christb"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="mwasson"/>

# <a name="azure-resiliency-guidance-failure-mode-analysis"></a>Conseils supplémentaires sur la résilience Azure : analyse de mode de défaillance

Échec du mode analyse (FMA) est un processus pour la création de la résilience dans un système, en identifiant les points de défaillance possibles dans le système. FMA doit faire partie des phases architecture et la conception, afin que vous puissiez générer défaillance dans le système à partir du début.

Voici la procédure générale à diriger un FMA : 

1. Identifier tous les composants du système. Inclure des dépendances externes, tel que fournisseurs d’identité, les services tiers et ainsi de suite.   

2. Pour chaque composant, identifier les défaillances potentielles susceptibles de se produire. Un seul composant peut avoir plusieurs l’échec du mode. Par exemple, vous devez prendre en compte les échecs de lecture et échecs d’écriture séparément, car les réductions impact et possibles sera différentes.

3. Évaluer chaque mode de défaillance en fonction de son risque global. Tenez compte des facteurs suivants :  

    - Quelle est la probabilité de l’échec. Est relativement courante ? Très rares ? Vous n’avez pas besoin des chiffres exacts ; l’objectif est d’aider à classer les la priorité.

    - Quel est l’impact sur l’application, en termes de disponibilité, la perte de données, coût monétaire et interruption de service ? 

4. Pour chaque mode de défaillance, déterminez comment l’application répondre et récupérer. Prendre en compte compromis dans la complexité de coût et de l’application.   

Comme point de départ pour votre processus FMA, cet article contient un catalogue des modes échec potentiels et leur minimisation. Le catalogue est organisé par technologie ou service Azure, plus une catégorie générale pour une conception de niveau de l’application. Le catalogue n’est pas exhaustif, mais traite la plupart des principales Azure services. 


## <a name="app-service"></a>Service d’application

### <a name="app-service-app-shuts-down"></a>Application de Service d’application s’arrête.

**Détection**. Causes possibles :

- Arrêt prévu
    
    - Un opérateur arrête de l’application ; par exemple, à l’aide du portail Azure.

    - L’application a été décharger car il n’était inactif. (Uniquement si la `Always On` paramètre est désactivé.)

- Arrêt inattendu

    - L’application se bloque.

    - Une instance de machine virtuelle application Service n’est plus disponible.


Journalisation Application_End détecte la fermeture du domaine d’application (arrêt de processus bordures) et est le seul moyen pour intercepter les arrêts de domaine d’application.

**Récupération**

- Si l’arrêt a été prévu, utilisez événement d’arrêt de l’application pour s’arrête. Par exemple, dans ASP.NET, utilisez la `Application_End` méthode.

- Si l’application a été décharger lorsqu’il est inactif, il est automatiquement redémarré sur la requête suivante. Toutefois, vous entraînera le « début à froid « coût. 

- Pour empêcher l’application en cours de chargement lorsqu’il est inactif, activer le `Always On` définition dans l’application web. Voir [configurer des applications web dans le Service d’application Azure][app-service-configure].

- Pour empêcher un opérateur d’arrêter l’application, définir un verrou ressource avec `ReadOnly` niveau. Consultez [ressources de verrouillage avec le Gestionnaire de ressources Azure][rm-locks].

- Si l’application se bloque ou une application Service machine virtuelle n’est pas disponible, application Service redémarre automatiquement l’application. 


**Diagnostics**. Les journaux applications et les journaux de serveur web. Consultez [Activer l’enregistrement des diagnostics pour les applications web dans le Service d’application Azure][app-service-logging].


### <a name="a-particular-user-repeatedly-makes-bad-requests-or-overloads-the-system"></a>Un utilisateur particulier rend requêtes incorrectes à plusieurs reprises ou surcharge du système. 

**Détection**. Authentification des utilisateurs et inclure les ID d’utilisateur dans les journaux d’application.

**Récupération**

- Utiliser la [Gestion des API Azure] [ api-management] aux demandes de limitation de l’utilisateur. Voir [limitation avec la gestion des API Azure de requêtes avancées][api-management-throttling]

- Bloquer l’utilisateur.

**Diagnostics**. Ouvrez une session toutes les demandes d’authentification.


### <a name="a-bad-update-was-deployed"></a>Une mise à jour incorrecte a été déployé.

**Détection**. Surveiller l’intégrité de l’application via le portail Azure (voir les [performances de l’application web moniteur Azure][app-insights-web-apps]) ou implémenter le [motif de surveillance état du point de terminaison][health-endpoint-monitoring-pattern].

**Récupération**. Utiliser plusieurs [emplacements de déploiement] [ app-service-slots] et revenir au déploiement dernier vérifié. Pour plus d’informations, voir [architecture de référence d’application web base][ra-web-apps-basic].


## <a name="azure-active-directory"></a>Azure Active Directory

### <a name="openid-connect-oidc-authentication-fails"></a>Se connecter OpenID (OIDC) l’authentification échoue.

**Détection**. Modes de panne possibles sont les suivantes :

1. Azure AD n’est pas disponible ou ne peuvent pas être atteintes en raison d’un problème de réseau. Redirection vers le point de terminaison d’authentification échoue et logiciels intermédiaires OIDC lève une exception.
2. Client AD Azure n’existe pas. Redirection vers le point de terminaison d’authentification renvoie un code d’erreur HTTP et logiciels intermédiaires OIDC lève une exception.
3. Utilisateur ne peut pas s’authentifier. Aucune stratégie de détection n’est nécessaire ; Azure AD gère les échecs de connexion.

**Récupération**

1. Intercepter exception non gérée de logiciels intermédiaires.
2. Gérer les `AuthenticationFailed` événements.
3. Redirigez l’utilisateur vers une page d’erreur.
4. Nouvelles tentatives d’utilisateur.


## <a name="azure-search"></a>Recherche Azure

### <a name="writing-data-to-azure-search-fails"></a>Écrire des données à la recherche Azure échouent.

**Détection**. Intercepter `Microsoft.Rest.Azure.CloudException` erreurs.

**Récupération**

Le [Kit de développement .NET recherche] [ search-sdk] tente automatiquement de renvoyer après incidents transitoires. Les exceptions levées par le Kit de développement logiciel client doivent être traitées comme non transitoires erreurs.

La stratégie de nouvelles tentatives par défaut utilise recul exponentielle. Pour utiliser une stratégie de nouvelles tentatives différents, appelez `SetRetryPolicy` sur la `SearchIndexClient` ou `SearchServiceClient` cours. Pour plus d’informations, voir [Effectue une nouvelle tentative automatique][auto-rest-client-retry].

**Diagnostics**. Utiliser la [recherche le trafic Analytique][search-analytics].


### <a name="reading-data-from-azure-search-fails"></a>Lecture de données de la recherche Azure échoue.

**Détection**. Intercepter `Microsoft.Rest.Azure.CloudException` erreurs.

**Récupération**

Le [Kit de développement .NET recherche] [ search-sdk] tente automatiquement de renvoyer après incidents transitoires. Les exceptions levées par le Kit de développement logiciel client doivent être traitées comme non transitoires erreurs.

La stratégie de nouvelles tentatives par défaut utilise recul exponentielle. Pour utiliser une stratégie de nouvelles tentatives différents, appelez `SetRetryPolicy` sur la `SearchIndexClient` ou `SearchServiceClient` cours. Pour plus d’informations, voir [Effectue une nouvelle tentative automatique][auto-rest-client-retry].

**Diagnostics**. Utiliser la [recherche le trafic Analytique][search-analytics].



## <a name="cassandra"></a>Cassandra


### <a name="reading-or-writing-to-a-node-fails"></a>Échec de lecture ou d’écriture dans un nœud.

**Détection**. Intercepter l’exception. Pour les clients .NET, il s’agit généralement `System.Web.HttpException`. Autres client peut avoir d’autres types d’exception.  Pour plus d’informations, voir [Gestion des erreurs de Cassandra terminé vers la droite](http://www.datastax.com/dev/blog/cassandra-error-handling-done-right).

**Récupération**

- Chaque [client Cassandra](https://wiki.apache.org/cassandra/ClientOptions) possède son propre réessayer stratégies et ses fonctionnalités. Pour plus d’informations, voir [Gestion des erreurs de Cassandra terminé droite][cassandra-error-handling].
- Utilisez un déploiement prenant en charge en rack, avec des nœuds de données répartis sur les domaines d’erreur.
- Déployer plusieurs parties avec la cohérence quorum local. Si une panne non transitoires, basculer vers une autre région.

**Diagnostics**. Journaux d’application


## <a name="cloud-service"></a>Service de nuage

### <a name="web-or-worker-roles-are-unexpectedlybeing-shut-down"></a>Rôles Web ou collaborateur sont arrêtés de façon inattendue.

**Détection**. La [RoleEnvironment.Stopping] [ RoleEnvironment.Stopping] de l’événement.

**Récupération**. Remplacer le [RoleEntryPoint.OnStop] [ RoleEntryPoint.OnStop] méthode pour nettoyer normalement. Pour plus d’informations, voir [La bonne manière pour gérer les événements d’Azure OnStop] [ onstop-events] (blog).


## <a name="documentdb"></a>DocumentDB

### <a name="reading-data-from-documentdb-fails"></a>Lecture de données à partir de DocumentDB échoue.

**Détection**. Intercepter `System.Net.Http.HttpRequestException` ou `Microsoft.Azure.Documents.DocumentClientException`. 

**Récupération**

- Le Kit de développement effectue automatiquement une nouvelle tentative échecs. Pour définir le nombre de tentatives et la durée maximale, configurez `ConnectionPolicy.RetryOptions`. Exceptions génère le client sont au-delà de la stratégie de nouvelle tentative ou ne sont pas des erreurs transitoires. 

- Si le service DocumentDB limite du client, elle retourne une erreur 429 HTTP. Vérifier le code d’état le `DocumentClientException`. Si vous rencontrez l’erreur 429 régulièrement, envisagez d’augmenter la valeur de débit de la collection DocumentDB.

- Répliquer la base de données DocumentDB sur deux ou plusieurs zones. Tous les doubles sont lisibles. À l’aide du client SDK, spécifiez la `PreferredLocations` paramètre. Il s’agit d’une liste des régions Azure. Toutes les lectures seront envoyées à la première zone disponible dans la liste. Si la requête échoue, le client essaiera les autres régions dans la liste, dans l’ordre. Pour plus d’informations, voir [développement avec des comptes de DocumentDB plusieurs régions][docdb-multi-region].

**Diagnostics**. Enregistrer toutes les erreurs du côté client.


### <a name="writing-data-to-documentdb-fails"></a>Écrire des données vers DocumentDB échoue.

**Détection**. Intercepter `System.Net.Http.HttpRequestException` ou `Microsoft.Azure.Documents.DocumentClientException`. 

**Récupération**

- Le Kit de développement effectue automatiquement une nouvelle tentative échecs. Pour définir le nombre de tentatives et la durée maximale, configurez `ConnectionPolicy.RetryOptions`. Exceptions génère le client sont au-delà de la stratégie de nouvelle tentative ou ne sont pas des erreurs transitoires. 

- Si le service DocumentDB limite du client, elle retourne une erreur 429 HTTP. Vérifier le code d’état le `DocumentClientException`. Si vous rencontrez l’erreur 429 régulièrement, envisagez d’augmenter la valeur de débit de la collection DocumentDB.

- Répliquer la base de données DocumentDB sur deux ou plusieurs zones. Si la région primaire échoue, une autre région sera promue pour écrire. Vous pouvez également déclencher manuellement un basculement. Le Kit de développement effectue la découverte automatique et le routage, par conséquent, code de l’application continue de fonctionner après un basculement. Pendant la période de basculement (généralement minutes), les opérations d’écriture ont latence plus élevée, que le Kit de développement détecte la nouvelle zone d’écriture. Pour plus d’informations, voir [développement avec des comptes de DocumentDB plusieurs régions][docdb-multi-region].

- Comme un secours, conserver le document à une sauvegarde file d’attente et traiter la file d’attente ultérieurement.

**Diagnostics**. Enregistrer toutes les erreurs du côté client.


## <a name="elasticsearch"></a>Elasticsearch

### <a name="reading-data-from-elasticsearch-fails"></a>Lecture de données à partir de Elasticsearch échoue.

**Détection**. Intercepter l’exception appropriée pour le [client Elasticsearch] particulier[ elasticsearch-client] en cours d’utilisation. 

**Récupération**

- Utiliser un mécanisme de réessayer. Chaque client dispose de sa propre stratégies de réessayer. 

- Déployer plusieurs nœuds Elasticsearch et utiliser la réplication de disponibilité.

Pour plus d’informations, voir [Elasticsearch en cours d’exécution sur Azure][elasticsearch-azure].

**Diagnostics**. Vous pouvez utiliser les outils d’analyse pour Elasticsearch ou enregistrer toutes les erreurs côté client avec la charge utile. Consultez la section « Suivi » dans [Elasticsearch en cours d’exécution sur Azure][elasticsearch-azure].


### <a name="writing-data-to-elasticsearch-fails"></a>Écrire des données vers Elasticsearch échoue.

**Détection**. Intercepter l’exception appropriée pour le [client Elasticsearch] particulier[ elasticsearch-client] en cours d’utilisation.  

**Récupération**

- Utiliser un mécanisme de réessayer. Chaque client dispose de sa propre stratégies de réessayer. 
 
- Si l’application peut tolérer un niveau de cohérence réduite, envisagez d’écriture avec `write_consistency` définition de `quorum`.

Pour plus d’informations, voir [Elasticsearch en cours d’exécution sur Azure][elasticsearch-azure].

**Diagnostics**. Vous pouvez utiliser les outils d’analyse pour Elasticsearch ou enregistrer toutes les erreurs côté client avec la charge utile. Consultez la section « Suivi » dans [Elasticsearch en cours d’exécution sur Azure][elasticsearch-azure].


## <a name="queue-storage"></a>Stockage de file d’attente

### <a name="writing-a-message-to-azure-queue-storage-fails-consistently"></a>Écriture d’un message vers Azure file d’attente stockage échoue régulièrement.

**Détection**. Après *N* tentatives, l’opération d’écriture échoue.

**Récupération**

- Stocker les données dans un cache local et les transférer l’écriture sur stockage plus tard, quand le service devient disponible.
- Créer une file d’attente secondaire et écrire dans cette file d’attente si la file d’attente principal n’est pas disponible.

**Diagnostics**. Utiliser les [mesures de stockage][storage-metrics].


### <a name="the-application-cannot-process-a-particular-message-from-the-queue"></a>L’application ne peut pas traiter un message spécifique dans la file d’attente. 

**Détection**. Spécifique à l’application. Par exemple, le message contient des données non valides, ou la logique métier échoue pour une raison quelconque. 

**Récupération**

Déplacer le message vers une file d’attente distincte. Exécuter un processus distinct pour examiner les messages dans cette file d’attente.

Envisagez d’utiliser Azure Service Bus messagerie files d’attente, qui fournit une [file d’attente suspendue] [ sb-dead-letter-queue] fonctionnalité à cet effet.

Remarque : Si vous utilisez des files d’attente de stockage avec WebJobs, le WebJobs SDK fournit des messages poison intégrés. Apprendre [à utiliser le stockage Azure file d’attente avec le Kit de développement WebJobs][sb-poison-message].

**Diagnostics**. Utiliser la journalisation de l’application.


## <a name="redis-cache"></a>Redis Cache

### <a name="reading-from-the-cache-fails"></a>Échec de lecture à partir du cache.

**Détection**. Intercepter `StackExchange.Redis.RedisConnectionException`.

**Récupération**

1. Recommencez concernant les échecs transitoires. Azure Redis cache prend en charge relance intégrée à voir les [instructions de réessayer de Cache Redis][redis-retry].
2. Traiter les échecs non transitoires comme une absence de cache et revenir à la source de données d’origine.

**Diagnostics**. Utiliser les [diagnostics de Cache Redis][redis-monitor].


### <a name="writing-to-the-cache-fails"></a>Échec de l’écriture dans le cache.

**Détection**. Intercepter `StackExchange.Redis.RedisConnectionException`.

**Récupération**

1. Recommencez concernant les échecs transitoires. Azure Redis cache prend en charge relance intégrée à voir les [instructions de réessayer de Cache Redis][redis-retry].
2. Si l’erreur est non transitoires, ignorez-la et laisser les autres transactions écrire plus loin dans le cache.

**Diagnostics**. Utiliser les [diagnostics de Cache Redis][redis-monitor].


## <a name="sql-database"></a>Base de données SQL

### <a name="cannot-connect-to-the-database-in-the-primary-region"></a>Impossible de se connecter à la base de données dans la région principale.

**Détection**. Échec de la connexion.

**Récupération**

Au préalable : La base de données doit être configuré pour la réplication de geo active. Consultez [SQL de base de données Active Geo réplication][sql-db-replication].

- Pour les requêtes, lisez à partir d’un réplica secondaire. 
- Pour les insertions et les mises à jour, manuellement basculer vers un réplica secondaire. Voir [initier un basculement planifié ou de base de données SQL Azure][sql-db-failover].

Le réplica utilise une chaîne de connexion différente, vous devez mettre à jour la chaîne de connexion dans votre application.


### <a name="client-runs-out-of-connections-in-the-connection-pool"></a>Client manque de connexions dans le regroupement de connexion.

**Détection**. Intercepter `System.InvalidOperationException` erreurs. 

**Récupération**

- Recommencez l’opération.
- Comme un plan d’atténuation, isoler les pools de connexion pour chaque cas d’utilisation, afin qu’un cas d’utilisation ne peut pas monopoliser toutes les connexions.
- Augmenter les pools maximale de connexion.

**Diagnostics**. Journaux d’application.


### <a name="database-connection-limit-is-reached"></a>Limite de connexion de base de données est atteint. 

**Détection**. Base de données SQL Azure limite le nombre des travailleurs simultanées, des connexions et des sessions. Les limites dépendent du niveau de service. Pour plus d’informations, voir [limites de ressources de base de données SQL Azure][sql-db-limits].

Pour détecter ces erreurs, intercepter `System.Data.SqlClient.SqlException` et vérifiez la valeur de `SqlException.Number` pour le code d’erreur SQL. Pour obtenir la liste des codes d’erreur appropriés, voir [codes d’erreur SQL pour les applications clientes de base de données SQL : erreur de connexion et d’autres problèmes de base de données][sql-db-errors].

**Récupération**. Ces erreurs sont considérées transitoires, et une nouvelle tentative peut résoudre le problème. Si vous avez atteint manière cohérente à travers ces erreurs, vous pouvez mise à l’échelle de la base de données.

**Diagnostics**. - [Sys.event_log] [ sys.event_log] requête renvoie les connexions de base de données réussies, échecs de connexion et des blocages.

- Créer une [règle d’alerte] [ azure-alerts] pour a échoué connexions.

- Activer [l’audit de base de données SQL] [ sql-db-audit] et rechercher les échecs de connexion.


## <a name="service-bus-messaging"></a>Service de messagerie Bus

### <a name="reading-a-message-from-a-service-bus-queue-fails"></a>Échec de la lecture d’un message à partir d’une file d’attente Bus des services.

**Détection**. Intercepter des exceptions à partir du Kit de développement logiciel client. La classe de base pour les exceptions Bus des services est [MessagingException][sb-messagingexception-class]. Si l’erreur est transitoire, la `IsTransient` propriété est vraie. 

Pour plus d’informations, voir [Bus des services de messagerie exceptions][sb-messaging-exceptions].

**Récupération**

1. Recommencez concernant les échecs transitoires. Voir [Bus des services réessayer instructions][sb-retry].

2. Les messages qui ne peuvent pas être remis à n’importe quel récepteur sont placés dans une *file d’attente suspendue*. Utilisez cette file d’attente pour afficher les messages qui n’a pas peuvent être reçues. Il n’existe aucun nettoyage automatique de la file d’attente suspendue. Messages y restent jusqu'à ce que vous avez explicitement la récupérer. Voir [files cachées vue d’ensemble du Service Bus][sb-dead-letter-queue].


### <a name="writing-a-message-to-a-service-bus-queue-fails"></a>Écriture d’un message à un Service Bus file d’attente échoue.

**Détection**. Intercepter des exceptions à partir du Kit de développement logiciel client. La classe de base pour les exceptions Bus des services est [MessagingException][sb-messagingexception-class]. Si l’erreur est transitoire, la `IsTransient` propriété est vraie. 

Pour plus d’informations, voir [Bus des services de messagerie exceptions][sb-messaging-exceptions].

**Récupération**

1. Le Service Bus tentatives automatiquement après erreurs transitoires. Par défaut, elle utilise recul exponentielle. Après le nombre maximal de nouvelles tentatives ou le délai d’attente maximal, le client lève une exception. Pour plus d’informations, voir [Service Bus réessayer instructions][sb-retry].

2. Si le quota de file d’attente est dépassé, le client lève [QuotaExceededException][QuotaExceededException]. Le message d’exception donne plus de détails. Décharger certains messages à partir de la file d’attente avant de réessayer et envisagez d’utiliser le modèle Circuit séparateur pour éviter de nouvelles tentatives continues alors que le quota est dépassé. En outre, vérifiez que la propriété [BrokeredMessage.TimeToLive] n’est pas définie trop élevée. 

3. Au sein d’une région, la résilience peut être améliorée à l’aide de [files d’attente partitionnées ou rubriques][sb-partition]. Un non partitions file d’attente ou une rubrique est affecté à une seule banque de messagerie. Si cette banque de messagerie n’est pas disponible, toutes les opérations sur cette file d’attente ou une rubrique échouera. Un partitionnée file d’attente ou une rubrique est partition entre plusieurs banques de messagerie. 

4. Pour plus de fiabilité, créez deux espaces de noms Bus des services dans différentes régions et répliquer les messages. Vous pouvez utiliser soit réplication active ou passive.

    - La réplication Active : le client envoie tous les messages à des files d’attente. Le combiné est à l’écoute sur les deux files d’attente. Baliser les messages avec un identificateur unique pour le client pouvez ignorer les messages en double.

    - Réplication passive : le client envoie le message à une file d’attente. Si une erreur est générée, le client revient à la file d’attente. Le combiné est à l’écoute sur les deux files d’attente. Cette approche réduit le nombre de messages en double sont envoyées. Toutefois, le destinataire doit toujours gérer des messages en double.

    Pour plus d’informations, voir [GeoReplication exemple] [ sb-georeplication-sample] et des [pratiques recommandées pour les applications isolantes contre défaillances Bus des services et d’urgence][sb-outages].


### <a name="duplicate-message"></a>Message dupliqué.

**Détection**. Examinez la `MessageId` et `DeliveryCount` propriétés du message.

**Récupération**

- Si possible, concevez votre message opérations de transformation doivent être idempotents. Dans le cas contraire, stocker un ID de message des messages qui sont déjà traitées et vérifier votre nom avant de traiter un message.

- Activer la détection des doublons en créant la file d’attente avec `RequiresDuplicateDetection` défini sur true. Avec ce paramètre, Bus des services supprime automatiquement les messages envoyés avec la même `MessageId` en tant que message précédent.  Notez les points suivants :

    -  Ce paramètre empêche les messages en double d’être placés dans la file d’attente. Il n’empêche pas un récepteur de traiter le même message plusieurs fois.

    - Détection des doublons comporte une fenêtre de temps. Si un doublon est envoyé au-delà de cette fenêtre, ne pas être détecté.

**Diagnostics**. Ouvrez une session messages en double.


### <a name="the-application-cannot-process-a-particular-message-from-the-queue"></a>L’application ne peut pas traiter un message spécifique dans la file d’attente. 

**Détection**. Spécifique à l’application. Par exemple, le message contient des données non valides, ou la logique métier échoue pour une raison quelconque. 

**Récupération**

Il existe deux modes d’échec à prendre en compte. 

- Le combiné détecte la panne. Dans ce cas, déplacer le message vers la file d’attente suspendue. Exécuter une version ultérieure, un processus distinct pour examiner les messages dans la file d’attente suspendue.

- Le combiné échoue au milieu de traitement du message &mdash; par exemple, en raison d’une exception non gérée. Pour gérer le cas, utilisez `PeekLock` mode. Dans ce mode, si le verrou arrive à expiration, le message devient disponible pour les autres destinataires. Si le message dépasse le nombre maximal de remise ou le temps de vie, le message est déplacé automatiquement vers la file d’attente suspendue.

Pour plus d’informations, voir [files cachées vue d’ensemble du Service Bus][sb-dead-letter-queue].

**Diagnostics**. Chaque fois que l’application déplace un message dans la file d’attente suspendue, écrire un événement dans les journaux des applications.


## <a name="service-fabric"></a>TISSU de service

### <a name="a-request-to-a-service-fails"></a>Une requête vers un service échoue.

**Détection**. Le service retourne une erreur.

**Récupération**

- Recherchez à nouveau un proxy (`ServiceProxy` ou `ActorProxy`) et appelez la méthode service/acteur à nouveau. 

- **Service dynamique**. Renvoyer à la ligne opérations sur les collections fiables dans une transaction. S’il existe une erreur, la transaction sera annulée. La demande, si extraites d’une file d’attente, est traitée à nouveau. 
 
- **Service sans état**. Si le service conserve les données dans un magasin externe, toutes les opérations doivent être idempotents.


**Diagnostics**. Journal des applications

### <a name="service-fabric-node-is-shut-down"></a>Nœud tissu de service est arrêté.

**Détection**. Un jeton d’annulation est passé au service `RunAsync` méthode. Service TISSU annule la tâche avant d’arrêter le nœud.

**Récupération**. Utiliser le jeton d’annulation pour détecter arrêt. Lorsque les demandes de Service TISSU annulation, terminez votre travail, puis quittez `RunAsync` aussi rapidement que possible.

**Diagnostics**. Journaux d’application


## <a name="storage"></a>Espace de stockage

### <a name="writing-data-to-azure-storage-fails"></a>Écrire des données au stockage Azure échoue

**Détection**. Le client reçoit des erreurs lors de l’écriture.

**Récupération**

1. Recommencez l’opération, pour récupérer des défaillances temporaires. La [stratégie de nouvelles] [ Storage.RetryPolicies] dans le client SDK gère cette valeur automatiquement.
2. Implémentez le modèle Circuit séparateur pour éviter de stockage écrasant.
3. Si N tentatives échouent, effectuez un secours ordonné. Par exemple :

    - Stocker les données dans un cache local et les transférer l’écriture sur stockage plus tard, quand le service devient disponible.
    - Si l’action écriture se trouvait dans une étendue transactions, compense la transaction.

**Diagnostics**. Utiliser les [mesures de stockage][storage-metrics].


### <a name="reading-data-from-azure-storage-fails"></a>Lecture de données à partir du stockage Azure échoue.

**Détection**. Le client reçoit des erreurs lors de la lecture.

**Récupération**

1. Recommencez l’opération, pour récupérer des défaillances temporaires. La [stratégie de nouvelles] [ Storage.RetryPolicies] dans le client SDK gère cette valeur automatiquement.
2. Espace de stockage RA GRS, cas d’échec de lecture du point de terminaison principal, essayez de lire le point de terminaison secondaire. Le Kit de développement logiciel client peut gérer cela automatiquement. Voir [la réplication du stockage Azure][storage-replication].
3. Si *N* tentatives échouent, prendre une action secours dégradé. Par exemple, si une image du produit ne sont pas accessibles à partir du stockage, afficher une image d’espace réservé générique.

**Diagnostics**. Utiliser les [mesures de stockage][storage-metrics].


## <a name="virtual-machine"></a>Machine virtuelle

### <a name="connection-to-a-backend-vm-fails"></a>Échec de la connexion à un serveur principal machine virtuelle.

**Détection**. Erreurs de connexion réseau.

**Récupération**

- Déployer des machines virtuelles au moins deux principaux dans un jeu de disponibilité, derrière un équilibrage de charge.

- Si l’erreur de connexion est transitoire, parfois TCP va correctement réessayer d’envoyer le message. 

- Mise en œuvre d’une stratégie de nouvelles tentatives dans l’application. 

- Pour les erreurs permanentes ou non transitoires, implémenter le [séparateur de Circuit] [ circuit-breaker] motif.

- Si la machine virtuelle appelante dépasse sa limite de sortie de réseau, la file d’attente sortante rempliront. Si la file d’attente sortante est régulièrement plein, envisagez l’évolution horizontale. 

**Diagnostics**. Journal des événements au niveau des limites de service.

### <a name="vm-instance-becomes-unavailable-or-unhealthy"></a>Machine virtuelle instance devient indisponible ou incorrecte.

**Détection**. Configurer un équilibrage de charge [sonde santé] [ lb-probe] qui indique si l’instance machine virtuelle est correct. La sonde doit vérifier si les fonctions critiques sont répond correctement. 

**Récupération**. Pour chaque niveau de l’application, placé plusieurs instances machine virtuelle dans le même jeu de disponibilité, puis placez un équilibrage de charge devant les ordinateurs virtuels. Si la sonde d’intégrité échoue, l’équilibrage de charge cesse d’envoyer des nouvelles connexions à l’instance mauvais état.

**Diagnostics**. -Utiliser l’équilibrage de charge [journal analytique][lb-monitor].
- Configurer votre système de surveillance pour surveiller tous le points de terminaison de contrôle d’état.


### <a name="operator-accidentally-shuts-down-a-vm"></a>Opérateur arrête accidentellement un ordinateur virtuel.

**Détection**. N/A

**Récupération**. Définir un verrou ressource avec `ReadOnly` niveau. Consultez [ressources de verrouillage avec le Gestionnaire de ressources Azure][rm-locks].

**Diagnostics**. Utiliser les [journaux d’activité Azure][azure-activity-logs].


## <a name="webjobs"></a>WebJobs

### <a name="continuous-job-stops-running-when-the-scm-host-is-idle"></a>Tâche continue s’arrête en cours d’exécution lorsque l’hôte SCM est inactif.

**Détection**. Passer un jeton d’annulation à la fonction WebJob. Pour plus d’informations, voir [arrêt][web-jobs-shutdown]. 

**Récupération**. Activer la `Always On` définition dans l’application web. Pour plus d’informations, voir [tâches de s’exécuter en arrière-plan avec WebJobs][web-jobs].


## <a name="application-design"></a>Conception de l’application

### <a name="application-cant-handle-a-spike-in-incoming-requests"></a>Application ne peuvent pas gérer un pic dans les demandes entrantes.

**Détection**. Dépend de l’application. Symptômes courants :

- Le site Web démarre retour de codes d’erreur HTTP 5xx.
- Services dépendants, tels que la base de données ou du stockage, commencer à limiter les demandes. Recherchez des erreurs HTTP comme 429 HTTP (trop nombreuses requêtes), en fonction du service.
- HTTP file d’attente grandit.

**Récupération**

- Évoluer pour gérer la charge accrue. 

- Minimiser les échecs pour éviter de devoir pannes en cascade interrompre l’ensemble de l’application. Stratégies d’atténuation sont les suivantes :

    - Mettre en œuvre la [Limitation de motif] [ throttling-pattern] afin d’éviter les systèmes principaux écrasant.
    - Utiliser le [chargement file d’attente nivellement] [ queue-based-load-leveling] demandes de la mémoire tampon et les traiter à un rythme approprié.
    - La priorité à certains clients. Par exemple, si l’application a niveaux gratuites et payantes, limiter les clients sur la couche gratuite, mais pas payants clients. [File d’attente]de priorité voient[priority-queue-pattern].

**Diagnostics**. Utiliser la [fonctionnalité journalisation application Service diagnostic][app-service-logging]. Utiliser un service tel que [Azure journal Analytique][azure-log-analytics], [Application Insights][app-insights], ou [Nouvelle Relic] [ new-relic] pour aider à comprendre les journaux de diagnostic.


### <a name="one-of-the-operations-in-a-workflow-or-distributed-transaction-fails"></a>L’une des opérations dans un flux de travail ou d’une transaction distribuée échoue.

**Détection**. Après *N* tentatives, elle échoue toujours.

**Récupération**

- En tant qu’un plan d’atténuation, implémentez le [Planificateur Agent responsable] [ scheduler-agent-supervisor] motif pour gérer le flux de travail entier. 
- Ne pas réessayer sur des délais d’expiration. Il existe un taux de réussite faible pour corriger cette erreur. 
- Travail de file d’attente, afin de réessayez plus tard.

**Diagnostics**. Ouvrez une session toutes les opérations (réussies et échouées), y compris les actions de compensation. Utilisez les ID de corrélation afin que vous pouvez effectuer le suivi de toutes les opérations dans la même transaction.


### <a name="a-call-to-a-remote-service-fails"></a>Un appel à un service distant échoue.

**Détection**. Code d’erreur HTTP.

**Récupération**

1. Recommencez concernant les échecs transitoires. 
2. Si l’appel échoue après *N* tentatives, effectuer une action de secours. (Application spécifique).
3. Mettre en œuvre le [séparateur de Circuit motif] [ circuit-breaker] pour éviter les échecs en cascade. 

**Diagnostics**. Ouvrez une session tous les échecs d’appel distant.


## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le processus de FMA, voir [résilience par la conception des services en nuage] [ resilience-by-design-pdf] (téléchargement PDF).

<!-- links -->

[api-management]: https://azure.microsoft.com/documentation/services/api-management/
[api-management-throttling]: ../api-management/api-management-sample-flexible-throttling.md
[app-insights]: ../application-insights/app-insights-overview.md
[app-insights-web-apps]: ../application-insights/app-insights-azure-web-apps.md
[app-service-configure]: ../app-service-web/web-sites-configure.md
[app-service-logging]: ../app-service-web/web-sites-enable-diagnostic-log.md
[app-service-slots]: ../app-service-web/web-sites-staged-publishing.md
[auto-rest-client-retry]: https://github.com/Azure/autorest/blob/master/Documentation/clients-retry.md
[azure-activity-logs]: ../monitoring-and-diagnostics/monitoring-overview-activity-logs.md
[azure-alerts]: ../monitoring-and-diagnostics/insights-alerts-portal.md
[azure-log-analytics]: ../log-analytics/log-analytics-overview.md
[BrokeredMessage.TimeToLive]: https://msdn.microsoft.com/library/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx
[cassandra-error-handling]: http://www.datastax.com/dev/blog/cassandra-error-handling-done-right
[circuit-breaker]: https://msdn.microsoft.com/library/dn589784.aspx
[docdb-multi-region]: ../documentdb/documentdb-developing-with-multiple-regions.md
[elasticsearch-azure]: guidance-elasticsearch-running-on-azure.md
[elasticsearch-client]: https://www.elastic.co/guide/en/elasticsearch/client/index.html
[health-endpoint-monitoring-pattern]: https://msdn.microsoft.com/library/dn589789.aspx
[onstop-events]: https://azure.microsoft.com/blog/the-right-way-to-handle-azure-onstop-events/
[lb-monitor]: ../load-balancer/load-balancer-monitor-log.md
[lb-probe]: ../load-balancer/load-balancer-custom-probe-overview.md#learn-about-the-types-of-probes
[new-relic]: https://newrelic.com/
[priority-queue-pattern]: https://msdn.microsoft.com/library/dn589794.aspx
[queue-based-load-leveling]: https://msdn.microsoft.com/library/dn589783.aspx
[QuotaExceededException]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.quotaexceededexception.aspx
[ra-web-apps-basic]: guidance-web-apps-basic.md
[redis-monitor]: ../redis-cache/cache-how-to-monitor.md
[redis-retry]: ../best-practices-retry-service-specific.md#cache-redis-retry-guidelines
[resilience-by-design-pdf]: http://download.microsoft.com/download/D/8/C/D8C599A4-4E8A-49BF-80EE-FE35F49B914D/Resilience_by_Design_for_Cloud_Services_White_Paper.pdf
[RoleEntryPoint.OnStop]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstop.aspx
[RoleEnvironment.Stopping]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.stopping.aspx
[rm-locks]: ../resource-group-lock-resources.md
[sb-dead-letter-queue]: ../service-bus-messaging/service-bus-dead-letter-queues.md
[sb-georeplication-sample]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/GeoReplication
[sb-messagingexception-class]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingexception.aspx
[sb-messaging-exceptions]: ../service-bus-messaging/service-bus-messaging-exceptions.md
[sb-outages]: ../service-bus/service-bus-outages-disasters.md#protecting-queues-and-topics-against-datacenter-outages-or-disasters
[sb-partition]: ../service-bus-messaging/service-bus-partitioning.md
[sb-poison-message]: ../app-service-web/websites-dotnet-webjobs-sdk-storage-queues-how-to.md#poison
[sb-retry]: ../best-practices-retry-service-specific.md#service-bus-retry-guidelines
[search-sdk]: https://msdn.microsoft.com/library/dn951165.aspx
[scheduler-agent-supervisor]: https://msdn.microsoft.com/library/dn589780.aspx
[search-analytics]: ../search/search-traffic-analytics.md
[sql-db-audit]: ../sql-database/sql-database-auditing-get-started.md
[sql-db-errors]: ../sql-database/sql-database-develop-error-messages.md#resource-governanance-errors
[sql-db-failover]: ../sql-database/sql-database-geo-replication-failover-portal.md
[sql-db-limits]: ../sql-database/sql-database-resource-limits.md
[sql-db-replication]: ../sql-database/sql-database-geo-replication-overview.md
[storage-metrics]: https://msdn.microsoft.com/library/dn782843.aspx
[storage-replication]: ../storage/storage-redundancy.md
[Storage.RetryPolicies]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.retrypolicies.aspx
[sys.event_log]: https://msdn.microsoft.com/library/dn270018.aspx
[throttling-pattern]: https://msdn.microsoft.com/library/dn589798.aspx
[web-jobs]: ../app-service-web/web-sites-create-web-jobs.md
[web-jobs-shutdown]: ../app-service-web/websites-dotnet-webjobs-sdk-storage-queues-how-to.md#graceful

