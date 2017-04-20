<properties 
    pageTitle="L’activation de métriques de stockage dans le portail Azure | Microsoft Azure" 
    description="Comment activer métriques de stockage pour les services Blob, file d’attente, Table et fichier" 
    services="storage" 
    documentationCenter="" 
    authors="robinsh" 
    manager="carmonm" 
    editor="tysonn"/>

<tags 
    ms.service="storage" 
    ms.workload="storage" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="08/03/2016" 
    ms.author="robinsh"/>

# <a name="enabling-storage-metrics-and-viewing-metrics-data"></a>Activation des métriques de stockage et l’affichage des données de métrique

[AZURE.INCLUDE [storage-selector-portal-enable-and-view-metrics](../../includes/storage-selector-portal-enable-and-view-metrics.md)]

## <a name="overview"></a>Vue d’ensemble

Par défaut, métriques de stockage n’est pas activée pour vos services de stockage. Vous pouvez activer le contrôle à l’aide du [Portail classique Azure](https://manage.windowsazure.com), Windows PowerShell, ou par programme via une API de stockage.

Lorsque vous activez métriques de stockage, vous devez choisir une période de rétention pour les données : cette période détermine la durée pendant laquelle l’espace de stockage service conserve les mesures et les frais de l’espace requis pour stocker les. En règle générale, vous devez utiliser une période de rétention plus courte pour minute indicateurs que métriques horaire en raison de l’espace supplémentaire significative requis pour les indicateurs minutes. Vous devez choisir une période de rétention, tels que vous avez suffisamment de temps pour analyser les données et télécharger les mesures que vous souhaitez conserver pour une analyse hors ligne ou des rapports. N’oubliez pas que vous serez également facturé pour le téléchargement des données de métrique à partir de votre compte de stockage.

## <a name="how-to-enable-storage-metrics-using-the-azure-classic-portal"></a>Comment activer métriques de stockage à l’aide du portail classique Azure

Dans le [Portail classique Azure](https://manage.windowsazure.com), utilisez la page Configurer pour un compte de stockage au contrôle métriques de stockage. Pour une analyse, vous pouvez définir un niveau et une période de rétention en jours pour chacun des objets BLOB, les tables et les files d’attente. Dans les deux cas, le niveau est une des opérations suivantes :

- Désactivé : Aucune métrique n’est collectées.

- Minimal — Métriques de stockage collecte un ensemble de base de mesures tels que pénétration/sortie, disponibilité, latence et des pourcentages de réussite, qui sont regroupées pour les services Blob, la Table et file d’attente.

- Commentaires — Métriques de stockage collecte un ensemble complet des indicateurs qui inclut les mêmes métriques de chaque opération API, outre les mesures de niveau de service de stockage. Métrique détaillée activer analyse plus approfondie des problèmes qui se produisent pendant le fonctionnement des applications.

Notez que le portail classique Azure ne pas actuellement vous autorise à configurer les mesures minutes dans votre compte de stockage ; Vous devez activer la minute indicateurs à l’aide de PowerShell ou par programme.


## <a name="how-to-enable-storage-metrics-using-powershell"></a>Comment activer métriques de stockage à l’aide de PowerShell

Vous pouvez utiliser PowerShell sur votre ordinateur local pour configurer métriques de stockage dans votre compte de stockage en utilisant l’applet de commande PowerShell Azure Get-AzureStorageServiceMetricsProperty pour récupérer les paramètres actuels et l’applet de commande Set-AzureStorageServiceMetricsProperty pour modifier les paramètres actuels.

Les applets de commande qui contrôlent métriques de stockage utilisent les paramètres suivants :

- Valeurs possibles MetricsType sont heures et minutes.

- Valeurs possibles ServiceType sont Blob, file d’attente et Table.

- MetricsLevel les valeurs possibles sont Aucun (équivalent à désactivé dans le portail classique Azure), le Service (équivalent au minimum dans le portail classique Azure) et ServiceAndApi (équivalent sur commentaires dans le portail classique Azure).

Par exemple, la commande suivante bascule minute métriques pour le service blob dans votre compte de stockage par défaut avec la période de rétention définie à cinq jours :

`Set-AzureStorageServiceMetricsProperty -MetricsType Minute -ServiceType Blob -MetricsLevel ServiceAndApi  -RetentionDays 5`

La commande suivante extrait les actuel horaire métriques niveau et la rétention des jours pour le service blob dans votre compte de stockage par défaut :

`Get-AzureStorageServiceMetricsProperty -MetricsType Hour -ServiceType Blob`

Pour plus d’informations sur la façon de configurer les applets de commande PowerShell Azure pour l’utiliser avec votre abonnement Azure et comment sélectionner le compte de stockage par défaut à utiliser, voir : [comment installer et configurer Azure PowerShell](../powershell-install-configure.md).

## <a name="how-to-enable-storage-metrics-programmatically"></a>Comment activer métriques de stockage par programme

L’extrait de code c# suivant montre comment activer les indicateurs et journalisation pour le service d’objets Blob à l’aide de la bibliothèque de stockage client pour .NET :

    //Parse the connection string for the storage account.
    const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);

    // Create service client for credentialed access to the Blob service.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Enable Storage Analytics logging and set retention policy to 10 days. 
    ServiceProperties properties = new ServiceProperties();
    properties.Logging.LoggingOperations = LoggingOperations.All;
    properties.Logging.RetentionDays = 10;
    properties.Logging.Version = "1.0";

    // Configure service properties for metrics. Both metrics and logging must be set at the same time.
    properties.HourMetrics.MetricsLevel = MetricsLevel.ServiceAndApi;
    properties.HourMetrics.RetentionDays = 10;
    properties.HourMetrics.Version = "1.0";

    properties.MinuteMetrics.MetricsLevel = MetricsLevel.ServiceAndApi;
    properties.MinuteMetrics.RetentionDays = 10;
    properties.MinuteMetrics.Version = "1.0";

    // Set the default service version to be used for anonymous requests.
    properties.DefaultServiceVersion = "2015-04-05";

    // Set the service properties.
    blobClient.SetServiceProperties(properties);
    
## <a name="viewing-storage-metrics"></a>Affichage des métriques de stockage

Lorsque vous avez configuré métriques de stockage pour surveiller votre compte de stockage, il enregistre les mesures dans un ensemble de tables connus dans votre compte de stockage. Vous pouvez utiliser la page Moniteur pour votre compte de stockage sur le portail classique Azure pour afficher les mesures horaires dès qu’elles sont disponibles dans un graphique. Dans cette page dans le portail classique Azure, vous pouvez :

- Sélectionner les mesures à tracer sur le graphique (le choix des statistiques disponibles varient selon que vous avez choisi de surveillance détaillée ou minimales pour le service sur la page Configurer).


- Sélectionnez la plage horaire pour les indicateurs affichée dans le graphique.


- Choisissez d’utiliser une échelle absolue ou relative à tracer les mesures.


- Configurer des alertes par courrier électronique pour vous avertir lorsqu’un indicateur spécifique atteint une certaine valeur.


Si vous voulez télécharger les métriques de stockage à long terme ou pour les analyser localement, vous devrez utiliser un outil ou écrire du code pour lire les tables. Vous devez télécharger les mesures minutes pour l’analyse. Les tables ne s’affichent pas si vous répertoriez toutes les tables dans votre compte de stockage, mais vous pouvez y accéder directement par leur nom. De nombreux outils de navigation sur le stockage de tiers connaissent l’existence de ces tables et vous permettent de les afficher directement (le billet affichera [Microsoft Azure stockage Explorers](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx) pour obtenir la liste des outils disponibles).

### <a name="hourly-metrics"></a>Métrique horaire
- $MetricsHourPrimaryTransactionsBlob
- $MetricsHourPrimaryTransactionsTable
- $MetricsHourPrimaryTransactionsQueue

### <a name="minute-metrics"></a>Métrique minute
- $MetricsMinutePrimaryTransactionsBlob
- $MetricsMinutePrimaryTransactionsTable
- $MetricsMinutePrimaryTransactionsQueue

### <a name="capacity"></a>Capacité
- $MetricsCapacityBlob

Vous trouverez des informations complètes sur les schémas de ces tables au [Schéma de la Table Analytique métriques de stockage](https://msdn.microsoft.com/library/azure/hh343264.aspx). Les exemples de lignes ci-dessous s’affichent uniquement un sous-ensemble des colonnes disponibles, mais illustrent certaines fonctionnalités importantes de la manière dont métriques de stockage enregistre les mesures :

| PartitionKey  |       RowKey       |                    Horodatage | TotalRequests | TotalBillableRequests | TotalIngress | TotalEgress | Disponibilité | AverageE2ELatency | AverageServerLatency | PercentSuccess |
|---------------|:------------------:|-----------------------------:|---------------|-----------------------|--------------|-------------|--------------|-------------------|----------------------|----------------|
| 20140522T1100 |      utilisateur ; Tous les      | 2014-05-22T11:01:16.7650250Z | 7             | 7                     | 4003         | 46801       | 100          | 104.4286          | 6.857143             | 100            |
| 20140522T1100 | utilisateur ; QueryEntities | 2014-05-22T11:01:16.7640250Z | 5             | 5                     | 2694         | 45951       | 100          | 143.8             | 7.8                  | 100            |
| 20140522T1100 |  utilisateur ; QueryEntity  | 2014-05-22T11:01:16.7650250Z | 1             | 1                     | 538          | 633         | 100          | 3                 | 3                    | 100            |
| 20140522T1100 | utilisateur ; UpdateEntity  | 2014-05-22T11:01:16.7650250Z | 1             | 1                     | 771          | 217         | 100          | 9                 | 6                    | 100               |

Dans ces données métriques minute exemple, la clé de partition utilise l’heure à résolution minute. La clé de ligne identifie le type d’informations qui sont stockées dans la ligne et il est composé de deux parties d’informations, le type d’accès et le type de requête :

- Le type d’accès est utilisateur ou système, où utilisateur fait référence à toutes les demandes de l’utilisateur au service de stockage et le système fait référence aux requêtes envoyées par stockage Analytique.

- Le type de requête est tout auquel cas elle est une ligne de synthèse, ou elle identifie l’API spécifique tel que QueryEntity ou UpdateEntity.


Les données d’exemple ci-dessus affiche tous les enregistrements d’une minute unique (en commençant à 11:00 AM), afin que le nombre de requêtes QueryEntities ainsi que le nombre de QueryEntity demande ainsi que le nombre de UpdateEntity demandes ajouter jusqu'à sept, c'est-à-dire le total apparaît dans la ligne de l’utilisateur : tous les. De même, vous pouvez obtenir la latence de bout en bout moyenne 104.4286 sur la ligne de l’utilisateur : tous les en calcul ((143.8 * 5) + 3 + 9) / 7.

Vous devez envisager paramètre des alertes dans le portail classique Azure dans la page Moniteur afin que métriques de stockage peut être informé automatiquement des modifications importantes apportées dans le comportement de vos services de stockage. Si vous utilisez un outil de l’Explorateur d’espace de stockage pour télécharger ces données métriques dans un format délimité par des tabulations, vous pouvez utiliser Microsoft Excel pour analyser les données. Consultez le blog [Microsoft Azure stockage Explorers](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx) pour obtenir la liste des outils de l’Explorateur d’espace de stockage disponible.



## <a name="accessing-metrics-data-programmatically"></a>Accès aux données de métrique par programme

La liste suivante répertorie des exemples c# de code qui accède à la minute métrique pour une plage de minutes et affiche les résultats dans une fenêtre de console. Il utilise la bibliothèque de stockage Azure version 4 qui inclut la classe CloudAnalyticsClient qui simplifie l’accès à des tables métriques de stockage.

    private static void PrintMinuteMetrics(CloudAnalyticsClient analyticsClient, DateTimeOffset startDateTime, DateTimeOffset endDateTime)
    {
    // Convert the dates to the format used in the PartitionKey
    var start = startDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");
    var end = endDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");
    
    var services = Enum.GetValues(typeof(StorageService));
    foreach (StorageService service in services)
    {
    Console.WriteLine("Minute Metrics for Service {0} from {1} to {2} UTC", service, start, end);
    var metricsQuery = analyticsClient.CreateMinuteMetricsQuery(service, StorageLocation.Primary);
    var t = analyticsClient.GetMinuteMetricsTable(service);
    var opContext = new OperationContext();
    var query =
    from entity in metricsQuery
    // Note, you can't filter using the entity properties Time, AccessType, or TransactionType
    // because they are calculated fields in the MetricsEntity class.
    // The PartitionKey identifies the DataTime of the metrics.
    where entity.PartitionKey.CompareTo(start) >= 0 && entity.PartitionKey.CompareTo(end) <= 0 
    select entity;
    
    // Filter on "user" transactions after fetching the metrics from Table Storage.
    // (StartsWith is not supported using LINQ with Azure table storage)
    var results = query.ToList().Where(m => m.RowKey.StartsWith("user"));
    var resultString = results.Aggregate(new StringBuilder(), (builder, metrics) => builder.AppendLine(MetricsString(metrics, opContext))).ToString();
    Console.WriteLine(resultString);
    }
    }
    
    private static string MetricsString(MetricsEntity entity, OperationContext opContext)
    {
    var entityProperties = entity.WriteEntity(opContext);
    var entityString =
    string.Format("Time: {0}, ", entity.Time) +
    string.Format("AccessType: {0}, ", entity.AccessType) +
    string.Format("TransactionType: {0}, ", entity.TransactionType) +
    string.Join(",", entityProperties.Select(e => new KeyValuePair<string, string>(e.Key.ToString(), e.Value.PropertyAsObject.ToString())));
    return entityString;
    
    }




## <a name="what-charges-do-you-incur-when-you-enable-storage-metrics"></a>Frais entraînent lorsque vous activez métriques de stockage ?

Écrire des demandes de création d’entités de table pour indicateurs sont facturées au taux standard applicables à toutes les opérations de stockage Azure.

Lire et supprimer les requêtes un client aux données de métrique sont également facturables au taux standards. Si vous avez configuré une stratégie de rétention des données, vous ne seront pas imputés lorsque le stockage Azure supprime des données de mesures de l’ancien. Toutefois, si vous supprimez des données analytique, votre compte est facturé pour les opérations de suppression.

La capacité utilisée par les tables métriques est également facturable : vous pouvez utiliser les éléments suivants pour évaluer la quantité de la capacité utilisé pour le stockage des données de métrique :

- Si chaque heure un service utilise chaque API dans chaque service, environ 148 Ko de données sont stockée toutes les heures dans les tables des transactions métriques si vous avez activé le service et le niveau de l’API résumé.

- Si chaque heure un service utilise chaque API dans chaque service, environ 12 Ko de données sont stockée toutes les heures dans les tables des transactions métriques si vous avez activé uniquement les niveaux de service résumé.

- La table capacité des objets BLOB comporte deux lignes ajoutées chaque jour (à condition qu’utilisateur a choisi de participer pour les journaux) : cela signifie que chaque jour la taille de ce tableau augmente par jusqu'à environ 300 octets.

## <a name="next-steps"></a>Étapes suivantes :
[L’activation de stockage Analytique journalisation et accéder aux données du journal](https://msdn.microsoft.com/library/dn782840.aspx)
 