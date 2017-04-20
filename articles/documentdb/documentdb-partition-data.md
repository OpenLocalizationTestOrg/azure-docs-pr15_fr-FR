<properties 
    pageTitle="Partition et la même échelle dans Azure DocumentDB | Microsoft Azure"      
    description="Découvrez comment partition works DocumentDB Azure, comment configurer partition et partition clés et comment choisir la clé partition appropriée pour votre application."         
    services="documentdb" 
    authors="arramac" 
    manager="jhubbard" 
    editor="monicar" 
    documentationCenter=""/> 

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/20/2016" 
    ms.author="arramac"/> 

# <a name="partitioning-and-scaling-in-azure-documentdb"></a>Partition et la même échelle dans Azure DocumentDB
[Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) est conçu pour vous aider à atteindre les performances rapides et prévisibles et les proportions en toute transparence avec votre application qu’elle se développe. Cet article fournit une vue d’ensemble de partition comment fonctionne dans DocumentDB et explique comment vous pouvez configurer collections DocumentDB pour redimensionner efficacement vos applications.

Après la lecture de cet article, vous ne pourrez pas répondre aux questions suivantes :   

- Comment fonctionne partition dans DocumentDB Azure ?
- Comment configurer partition dans DocumentDB
- Quelles sont les clés, et comment sélectionner la clé de partition droite pour mon application ?

Pour commencer avec un code, téléchargez le projet à partir [d’Échantillon pilote de test de Performance DocumentDB](https://github.com/Azure/azure-documentdb-dotnet/tree/a2d61ddb53f8ab2a23d3ce323c77afcf5a608f52/samples/documentdb-benchmark). 

## <a name="partitioning-in-documentdb"></a>Partition dans DocumentDB

Dans DocumentDB, vous pouvez stocker et interroger des documents JSON sans schéma avec les heures de réponse ordre des millisecondes sur n’importe quelle échelle. DocumentDB fournit des conteneurs pour le stockage de données appelées **collections de sites**. Collections de sites sont ressources logiques et peuvent s’étendre sur une ou plusieurs partitions physiques ou serveurs. Le nombre de partitions est déterminé par DocumentDB en fonction de la taille de stockage et le débit généré de la collection de sites. Toutes les partitions dans DocumentDB a une taille fixe de stockage de secours SSD associé et répliquées de disponibilité. Gestion des partitions est entièrement gérée par DocumentDB Azure, et vous n’avez pas à écrire du code complexe ou gérer vos partitions. Collections DocumentDB sont **pratiquement illimité** en termes de stockage et de débit. 

Procédure est complètement transparente à votre application. DocumentDB prend en charge lectures rapides et écrit des requêtes SQL et LINQ, JavaScript, en fonction logique transactions, niveaux de cohérence et contrôle d’accès précis via des appels API REST à une ressource de collection unique. Le service gère les données de distribution dans plusieurs partitions et requêtes routage vers la droite partition. 

Comment cela fonctionne ? Lorsque vous créez une collection de sites dans DocumentDB, vous pouvez constater qu’il existe une valeur de **propriété de clé partition** configuration que vous pouvez spécifier. Il s’agit de la propriété JSON (ou le chemin d’accès) dans vos documents qui peuvent être utilisées par DocumentDB distribution de vos données sur plusieurs serveurs ou partitions. DocumentDB sera la valeur de clé partition de hachage et le résultat haché pour déterminer la partition dans laquelle le document JSON sera stocké. Tous les documents avec la même clé partition seront stockés dans la même partition. 

Par exemple, considérez une application qui stocke les données sur les employés et leurs services dans DocumentDB. Nous allons choisir `"department"` en tant que la propriété de clé partition, afin d’adapter les données par département. Tous les documents dans DocumentDB doivent contenir un obligatoire `"id"` propriété qui doit être unique pour chaque document avec la même valeur clé partition, par exemple, `"Marketing`». Tous les documents stockés dans une collection de sites peut comporter qu’une combinaison unique de clé de partition et l’id, par exemple, `{ "Department": "Marketing", "id": "0001" }`, `{ "Department": "Marketing", "id": "0002" }`, et `{ "Department": "Sales", "id": "0001" }`. En d’autres termes, la propriété composée de (id clé, partition) est la clé primaire pour votre collection de sites.

### <a name="partition-keys"></a>Touches de partition
Le choix de la clé de partition est une décision importante que vous devrez effectuer au moment de la conception. Vous devez choisir un nom de la propriété JSON qui comporte un large éventail de valeurs et est susceptible d’avoir répartis de modèles d’accès. Clé de partition est spécifiée comme un chemin d’accès JSON, par exemple, `/department` représente le service de propriété. 

Le tableau suivant répertorie des exemples de définitions de clé partition et les valeurs JSON correspondant à chacun.

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p><strong>Chemin d’accès clé partition</strong></p></td>
            <td valign="top"><p><strong>Description</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>/Department</p></td>
            <td valign="top"><p>Correspond à la valeur JSON de doc.department où le document est le document.</p></td>
        </tr>
        <tr>
            <td valign="top"><p>nom/propriétés /</p></td>
            <td valign="top"><p>Correspond à la valeur JSON de doc.properties.name où le document est le document (propriété imbriquée).</p></td>
        </tr>
        <tr>
            <td valign="top"><p>/ID</p></td>
            <td valign="top"><p>Correspond à la valeur JSON de doc.id (clé id et partition sont la même propriété).</p></td>
        </tr>
        <tr>
            <td valign="top"><p>/ « nom de service »</p></td>
            <td valign="top"><p>Correspond à la valeur JSON de document [« nom de service »] où le document est le document.</p></td>
        </tr>
    </tbody>
</table>

> [AZURE.NOTE] La syntaxe de chemin d’accès clé partition est similaire à la spécification de chemin d’accès de l’indexation de chemins de stratégie à la différence de clés que le chemin d’accès correspond à la propriété au lieu de la valeur, il n’y a aucun caractère générique à la fin. Par exemple, vous devez spécifier/département / ? pour indexer les valeurs sous service, mais spécifiez /department comme la définition de clé partition. Le chemin d’accès clé partition implicitement indexé et ne peuvent pas être exclue de l’indexation à l’aide de remplacements de la stratégie d’indexation.

Examinons comment le choix de la clé de partition affecte les performances de votre application.

### <a name="partitioning-and-provisioned-throughput"></a>Débit partition et mis en service
DocumentDB est conçu pour des performances prévisibles. Lorsque vous créez une collection de sites, vous réservez débit en termes d' ** [unités demande](documentdb-request-units.md) (demandeur) par seconde**. Chaque demande est affectée à un prix unitaire demande qui est proportionnelle à la quantité de ressources système comme processeur et IO consommées par l’opération. Lecture d’un document 1 Ko grâce à la cohérence Session consomme 1 unité demande. Un accès en lecture est 1 demandeur quel que soit le nombre d’éléments stockés ou le nombre de demandes simultanées en cours d’exécution simultanément. Documents volumineux requièrent une version ultérieure demande unités selon la taille. Si vous connaissez la taille de vos entités et le nombre de lectures, que vous devez prendre en charge pour votre application, vous pouvez prévoir le nombre exact de débit requis pour les besoins en matière de lecture de votre application. 

Quand DocumentDB stocke des documents, il les répartit uniformément entre partitions en fonction de la valeur de clé partition. Le débit est également réparti de manière disponibles partitions c'est-à-dire le débit par partition = (total débit par collection de sites) / (nombre de partitions). 

>[AZURE.NOTE] Pour atteindre le débit complè de la collection de sites, vous devez choisir une clé de partition qui permet de vous permettent de distribuer uniformément demandes par rapport à un certain nombre de valeurs de clé partition distincte.

## <a name="single-partition-and-partitioned-collections"></a>Partition unique et des Collections partitionnées
DocumentDB prend en charge la création de collections de partition unique et partitionnées. 

- **Collections partitionnées** peuvent s’étalent sur plusieurs partitions et prend en charge de très grandes quantités de stockage et de débit. Vous devez spécifier une clé de partition pour la collection de sites.
- **Partition unique collections** ont des options de prix inférieures et la possibilité de la requête et effectuer des transactions pour toutes les données de la collection de sites. Ils disposent les limites de stockage et extensibilité élevées d’une seule partition. Vous n’êtes pas obligé de spécifier une clé de partition pour ces collections. 

![Collections partitionnées dans DocumentDB][2] 

Pour les scénarios qui n’est pas nécessaire de grands volumes de stockage ou débit, partition unique collections sont une bonne cible. Notez que seule partition collections disposent extensibilité élevées et des limites de stockage d’une seule partition, c'est-à-dire jusqu'à 10 Go de stockage et jusqu'à 10 000 unités demande par seconde. 

Collections partitionnées peuvent prendre en charge de très grandes quantités de stockage et de débit. Toutefois les offres par défaut sont configurés pour stocker jusqu'à 250 Go de stockage et mettre à l’échelle jusqu'à 250 000 unités demande par seconde. Si vous avez besoin de plus de stockage ou de débit par collection de sites, contactez le [Support Azure](documentdb-increase-limits.md) pour que ces accrue pour votre compte.

Le tableau suivant répertorie les différences de travailler avec une seule partition et collections partitionnées :

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p></p></td>
            <td valign="top"><p><strong>Collection de sites Partition unique</strong></p></td>
            <td valign="top"><p><strong>Collection de sites partitionnées</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>Clé de partition</p></td>
            <td valign="top"><p>Aucun</p></td>
            <td valign="top"><p>Obligatoire</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Clé primaire de Document</p></td>
            <td valign="top"><p>« id »</p></td>
            <td valign="top"><p>clé composée &lt;clé de partition&gt; et « id »</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Stockage minimum</p></td>
            <td valign="top"><p>0 GO</p></td>
            <td valign="top"><p>0 GO</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Capacité maximale de stockage</p></td>
            <td valign="top"><p>10 GO</p></td>
            <td valign="top"><p>Illimité (250 Go par défaut)</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Débit minimum</p></td>
            <td valign="top"><p>400 unités demande par seconde</p></td>
            <td valign="top"><p>10 000 unités demande par seconde</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Débit maximal</p></td>
            <td valign="top"><p>10 000 unités demande par seconde</p></td>
            <td valign="top"><p>Illimité (unités demande 250 000 par seconde par défaut)</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Versions de l’API</p></td>
            <td valign="top"><p>Tous les</p></td>
            <td valign="top"><p>API 2015-12-16 et versions ultérieures</p></td>
        </tr>
    </tbody>
</table>

## <a name="working-with-the-sdks"></a>Travailler avec les kits de développement

Azure DocumentDB désormais en charge partition automatique avec [L’API REST version 2015-12-16](https://msdn.microsoft.com/library/azure/dn781481.aspx). Afin de créer des collections partitionnées, vous devez télécharger versions SDK 1.6.0 ou version ultérieure dans une des plates-formes prises en charge Kit de développement logiciel (.NET, Node.js, Java, Python). 

### <a name="creating-partitioned-collections"></a>Création de collections de partitionnées

L’exemple suivant montre un extrait de code .NET pour créer une collection de sites pour stocker des données de télémétrie appareil de 20 000 unités de demande de débit par seconde. Le Kit de développement définit la valeur OfferThroughput (qui à son tour définit la `x-ms-offer-throughput` en-tête de la demande dans l’API REST). Ici, nous positionner le `/deviceId` comme clé partition. Le choix de la clé de partition est enregistré en même temps que le reste des métadonnées, telles que nom et stratégie d’indexation de collection de sites.

Dans cet exemple, nous avons choisi `deviceId` étant donné que nous sauront pas que (a) dans la mesure où il existe un grand nombre d’appareils, écrit peut être distribuée sur partitions de manière égale et permet de mettre à l’échelle la base de données pour des volumes importants de données d’acquisition et (b) la plupart des requêtes comme l’extraction de la dernière lecture pour un appareil sont limitées à un identificateur unique et peuvent être récupérées à partir d’une seule partition.

    DocumentClient client = new DocumentClient(new Uri(endpoint), authKey);
    await client.CreateDatabaseAsync(new Database { Id = "db" });

    // Collection for device telemetry. Here the JSON property deviceId will be used as the partition key to 
    // spread across partitions. Configured for 10K RU/s throughput and an indexing policy that supports 
    // sorting against any number or string property.
    DocumentCollection myCollection = new DocumentCollection();
    myCollection.Id = "coll";
    myCollection.PartitionKey.Paths.Add("/deviceId");

    await client.CreateDocumentCollectionAsync(
        UriFactory.CreateDatabaseUri("db"),
        myCollection,
        new RequestOptions { OfferThroughput = 20000 });
        

> [AZURE.NOTE] Afin de créer des collections partitionnées, vous devez spécifier une valeur de débit d’unités de demande > 10 000 par seconde. Dans la mesure où débit est d’un multiple de 100, cela doit être 10,100 ou une version ultérieure.

Cette méthode effectue une API REST appel vers DocumentDB et le service configurera un certain nombre de partitions en fonction du débit demandé. Vous pouvez modifier le débit d’une collection selon les besoins de vos performances évoluer. Pour plus d’informations, voir [Niveaux de Performance](documentdb-performance-levels.md) .

### <a name="reading-and-writing-documents"></a>Lire et écrire des documents

À présent, nous allons insérer des données dans DocumentDB. Voici un exemple de classe contenant un appareil de lecture et un appel à CreateDocumentAsync pour insérer un nouvel appareil en train de lire dans une collection de sites.

    public class DeviceReading
    {
        [JsonProperty("id")]
        public string Id;

        [JsonProperty("deviceId")]
        public string DeviceId;

        [JsonConverter(typeof(IsoDateTimeConverter))]
        [JsonProperty("readingTime")]
        public DateTime ReadingTime;

        [JsonProperty("metricType")]
        public string MetricType;

        [JsonProperty("unit")]
        public string Unit;

        [JsonProperty("metricValue")]
        public double MetricValue;
      }

    // Create a document. Here the partition key is extracted as "XMS-0001" based on the collection definition
    await client.CreateDocumentAsync(
        UriFactory.CreateDocumentCollectionUri("db", "coll"),
        new DeviceReading
        {
            Id = "XMS-001-FE24C",
            DeviceId = "XMS-0001",
            MetricType = "Temperature",
            MetricValue = 105.00,
            Unit = "Fahrenheit",
            ReadingTime = DateTime.UtcNow
        });


Nous allons lire le document id, et il est la clé partition mettre à jour et finir, supprimez-le par id et clé de partition. Notez que les lectures incluent une valeur PartitionKey (correspondant à la `x-ms-documentdb-partitionkey` en-tête de la demande dans l’API REST).

    // Read document. Needs the partition key and the ID to be specified
    Document result = await client.ReadDocumentAsync(
      UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
      new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });

    DeviceReading reading = (DeviceReading)(dynamic)result;

    // Update the document. Partition key is not required, again extracted from the document
    reading.MetricValue = 104;
    reading.ReadingTime = DateTime.UtcNow;

    await client.ReplaceDocumentAsync(
      UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
      reading);

    // Delete document. Needs partition key
    await client.DeleteDocumentAsync(
      UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
      new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });



### <a name="querying-partitioned-collections"></a>Interroger des collections partitionnées

Lorsque vous recherchez des données dans des collections partitionnées, DocumentDB achemine automatiquement la requête vers les partitions correspondant aux valeurs clés partition spécifiés dans le filtre (le cas échéant). Par exemple, cette requête est acheminée vers simplement la partition contenant la clé de partition « XMS-0001 ».

    // Query using partition key
    IQueryable<DeviceReading> query = client.CreateDocumentQuery<DeviceReading>(
        UriFactory.CreateDocumentCollectionUri("db", "coll"))
        .Where(m => m.MetricType == "Temperature" && m.DeviceId == "XMS-0001");

La requête suivante n’est pas un filtre sur la touche partition (ID de périphérique) et est serveur à toutes les partitions où il est exécuté sur index de la partition. Notez que vous devez spécifier la EnableCrossPartitionQuery (`x-ms-documentdb-query-enablecrosspartition` dans l’API REST) pour que le Kit de développement pour exécuter une requête sur les partitions.

    // Query across partition keys
    IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
        UriFactory.CreateDocumentCollectionUri("db", "coll"), 
        new FeedOptions { EnableCrossPartitionQuery = true })
        .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100);

### <a name="parallel-query-execution"></a>Exécution des requêtes en parallèle

Le SDK DocumentDB 1.9.0 et au-dessus des options de l’exécution des requêtes en parallèle prise en charge, qui vous permet d’exécuter des requêtes de faible latence sur collections partitionnées, même lorsqu’ils souhaitent tactile un grand nombre de partitions. Par exemple, la requête suivante est configurée pour s’exécuter en parallèle entre partitions.

    // Cross-partition Order By Queries
    IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
        UriFactory.CreateDocumentCollectionUri("db", "coll"), 
        new FeedOptions { EnableCrossPartitionQuery = true, MaxDegreeOfParallelism = 10, MaxBufferedItemCount = 100})
        .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100)
        .OrderBy(m => m.MetricValue);

Vous pouvez gérer l’exécution des requêtes en parallèle en ajustant les paramètres suivants :

- En définissant `MaxDegreeOfParallelism`, vous pouvez contrôler le degré de parallélisme c'est-à-dire le nombre maximal de connexions réseau simultanées à partitions de la collection de sites. Si vous définissez cette valeur-1, le degré de parallélisme est géré par le Kit de développement. Si la `MaxDegreeOfParallelism` n’est pas spécifiée ou défini à 0, ce qui correspond à la valeur par défaut, il y ait une connexion réseau unique à partitions de la collection de sites.
- En définissant `MaxBufferedItemCount`, vous pouvez échanger des désactiver requête latence et client côté utilisation de la mémoire. Si vous omettez ce paramètre ou que vous définissez cette valeur-1, le nombre d’éléments mis en mémoire tampon pendant l’exécution des requêtes en parallèle est géré par le Kit de développement.

Étant donné le même état de la collection de sites, une requête en parallèle retourner des résultats dans le même ordre que dans l’exécution de série. Lorsque vous effectuez une requête entre partitions qui comporte le tri (ORDER BY et/ou haut), le SDK DocumentDB émet la requête en parallèle entre partitions et fusionne les résultats partiellement triés dans le côté client pour produire des résultats classés globalement.

### <a name="executing-stored-procedures"></a>Procédures stockées en cours d’exécution

Vous pouvez également exécuter des transactions atomiques par rapport à vos documents avec la même ID de périphérique, par exemple, si vous gérez agrégats ou le dernier état d’un périphérique dans un seul document. 

    await client.ExecuteStoredProcedureAsync<DeviceReading>(
        UriFactory.CreateStoredProcedureUri("db", "coll", "SetLatestStateAcrossReadings"),
        new RequestOptions { PartitionKey = new PartitionKey("XMS-001") }, 
        "XMS-001-FE24C");

Dans la section suivante, nous examinons comment vous pouvez déplacer aux collections partitionnées à partir de collections de partition unique.

<a name="migrating-from-single-partition"></a>
### <a name="migrating-from-single-partition-to-partitioned-collections"></a>Migration de partition unique aux collections partitionnées
Lorsqu’une application à l’aide d’une collection de sites partition unique doit débit supérieur (> 10 000 demandeur/s) ou le stockage de données plus grande (> 10 Go), vous pouvez utiliser l' [Outil de Migration de données DocumentDB](http://www.microsoft.com/downloads/details.aspx?FamilyID=cda7703a-2774-4c07-adcc-ad02ddc1a44d) pour migrer les données à partir de la collection de sites à partition unique sur une collection de sites partitionnées. 

Pour migrer à partir d’une collection de sites partition unique pour une collection de sites partitionnées

1. Exporter des données à partir de la collection de sites à partition unique vers JSON. Pour plus d’informations, voir [Exporter vers un fichier JSON](documentdb-import-data.md#export-to-json-file) .
2. Importer les données dans une collection partitionnée créée avec une définition de clé partition et plus de 10 000 unités demande débit par seconde, comme illustré dans l’exemple ci-dessous. Pour plus d’informations, voir [importation DocumentDB](documentdb-import-data.md#DocumentDBSeqTarget) .

![Migration des données à une collection partitionnées dans DocumentDB][3]  

>[AZURE.TIP] Pour importer courts, envisagez d’augmenter le nombre de parallèle demandes à 100 ou une version ultérieure pour tirer parti de la débit disponible pour les collections partitionnées. 

Maintenant que nous avons terminé les notions de base, examinons quelques considérations relatives à la conception importante lorsque vous travaillez avec des clés de partition dans DocumentDB.

## <a name="designing-for-partitioning"></a>Conception de division
Le choix de la clé de partition est une décision importante que vous devrez effectuer au moment de la conception. Cette section décrit certaines des compromis lors de la sélection d’une clé de partition pour votre collection de sites.

### <a name="partition-key-as-the-transaction-boundary"></a>Clé de partition en tant que la limite de transaction
Votre choix de clé de partition doit s’équilibrer la nécessité d’activer l’utilisation des transactions par rapport à la configuration minimale requise pour répartir vos entités sur plusieurs touches partition pour garantir une solution scalable. À une extrême, vous pouvez définir la même clé partition pour tous vos documents, mais cela peut limiter l’extensibilité élevées de votre solution. À l’autre extrême, vous pourriez affecter une clé de partition unique pour chaque document, ce qui serait hautement scalable, mais vous empêchent l’utilisation de transactions croisée document via les procédures stockées et des déclencheurs. Une clé de partition idéale est un qui vous permet d’utiliser des requêtes efficaces et qui a cardinalité suffisante pour garantir que votre solution s’adapte. 

### <a name="avoiding-storage-and-performance-bottlenecks"></a>Pour éviter d’obtenir des engorgements de stockage et de performances 
Il est également important de choisir une propriété qui permet d’écriture réparti sur un certain nombre de valeurs distinctes. Demandes de la même clé partition ne peut pas dépasser le débit d’une seule partition et seront limitées. Il est donc important sélectionner une clé de partition n’entraîne pas **« points sensibles »** au sein de votre application. La taille de stockage totale pour les documents avec la même clé partition peut également pas dépasser 10 Go de stockage. 

### <a name="examples-of-good-partition-keys"></a>Exemples de bonne partition clés
Voici quelques exemples sur la façon de choisir la clé de partition pour votre application :

* Si vous êtes la mise en œuvre d’un système principal de profil utilisateur, l’ID utilisateur est un bon choix pour la clé de partition.
* Si vous stockez IoT données état du périphérique, par exemple, un ID de périphérique est un bon choix pour la clé de partition.
* Si vous utilisez DocumentDB pour la connexion de données de la série chronologique, l’ID de nom d’hôte ou un processus est un bon choix pour la clé de partition.
* Si vous avez une architecture de cliente multiples, l’ID de client est un bon choix pour la clé de partition.

Notez que dans certains cas d’utilisation (par exemple, les profils utilisateur et IoT ci-dessus), la clé de partition peut l’être identique à votre id (document clé). Dans d’autres tels que les données de série de temps, vous pouvez avoir une clé de partition qui est différente de l’id.

### <a name="partitioning-and-loggingtime-series-data"></a>Partition et journalisation/heure-série de données
Un des scénarios d’utilisation plus courants de DocumentDB est pour la journalisation et télémétrie. Il est important de choisir une clé de lecteur correct dans la mesure où vous devrez peut-être en lecture/écriture importants volumes de données. Le choix sera dépendent de votre lecture et d’écriture taux et les types de requêtes que vous souhaitez exécuter. Voici quelques conseils sur la façon de choisir une clé de lecteur correct.

- Si votre cas d’utilisation implique un taux de petite écrit acculumating sur une longue période de temps, et qu’il soit nécessaire à la requête en plages de horodatages et autres filtres, puis en utilisant un report de l’horodatage par exemple, date comme une clé de partition est une bonne approche. Cela vous permet de requête sur toutes les données pour une date à partir d’une seule partition. 
- Si votre charge de travail est lourds écriture, qui est généralement plus courant, vous devez utiliser une clé de partition qui n’est pas basée sur horodatage afin que DocumentDB pouvez uniformiser la largeur écrit sur plusieurs partitions. Ici un nom d’hôte, ID de processus, ID de l’activité ou une autre propriété présentant une cardinalité élevée est un bon choix. 
- Une troisième approche est un hybride une si vous avez plusieurs collections, une pour chaque jour/mois et la clé de partition est une propriété granulaire comme nom d’hôte. Cela a l’avantage que vous pouvez définir les niveaux de performances différentes en fonction de la fenêtre de temps, par exemple, la collection du mois en cours est configurée avec un débit plus élevé dans la mesure où il sert de lecture et écriture, alors que les mois précédents avec une diminution débit car ils servent uniquement lectures.

### <a name="partitioning-and-multi-tenancy"></a>Partition et location multiples
Si vous implémentez une application cliente multiples à l’aide de DocumentDB, il existe deux principaux modèles d’implémentation de location avec DocumentDB – clé une partition par client et une collection de sites par client. Voici les avantages et inconvénients pour chacune d’elles :

* Une clé de Partition par client : dans ce modèle, les clients sont colocalisés au sein d’une collection unique. Mais requêtes et les insertions pour les documents dans un seul client peuvent être effectuées par rapport à une seule partition. Vous pouvez également implémenter logique transactions entre tous les documents dans un client. Dans la mesure où plusieurs clients partagent une collection de sites, vous pouvez enregistrer des coûts de stockage et de débit par le regroupement de ressources pour les clients au sein d’une collection unique plutôt que de mise en service de marge supplémentaire pour chaque client. L’inconvénient est que vous n’avez pas d’isolement performances par client. Performances/débit provenant d’Internet s’appliquent à l’ensemble de la collection vs augmente ciblées pour les clients.
* Une collection de sites par client : chaque client dispose de sa propre collection. Dans ce modèle, vous pouvez réserver des performances par client. Avec nouveau la consommation en fonction de tarification modèle de DocumentDB, ce modèle est plus économique pour les applications clients à plusieurs avec un petit nombre de clients.

Vous pouvez également utiliser une approche combinaison/niveaux qui collocates clients petites et migre des clients plus grande vers leur propre collection de sites.

## <a name="next-steps"></a>Étapes suivantes
Dans cet article, nous avons décrit comment partition fonctionne dans Azure DocumentDB, comment vous pouvez créer des collections partitionnées et comment vous pouvez choisir une clé de partition appropriée pour votre application. 

-   Effectuer une échelle et des tests de performances avec DocumentDB. Consultez [performances et échelle réalisés avec Azure DocumentDB](documentdb-performance-testing.md) pour un échantillon.
-   Prise en main de codage avec le [SDK](documentdb-sdk-dotnet.md) ou de l' [API REST](https://msdn.microsoft.com/library/azure/dn781481.aspx)
-   En savoir plus sur [débit généré dans DocumentDB](documentdb-performance-levels.md)
-   Si vous souhaitez personnaliser la façon dont votre application effectue partition, vous pouvez brancher dans votre propre implémentation partition côté client. Voir [côté Client partition prise en charge](documentdb-sharding.md).

[1]: ./media/documentdb-partition-data/partitioning.png
[2]: ./media/documentdb-partition-data/single-and-partitioned.png
[3]: ./media/documentdb-partition-data/documentdb-migration-partitioned-collection.png  

 
