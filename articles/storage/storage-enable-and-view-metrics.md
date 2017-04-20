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

# <a name="enabling-azure-storage-metrics-and-viewing-metrics-data"></a>Activation des métriques de stockage Azure et affichage des données de mesures

[AZURE.INCLUDE [storage-selector-portal-enable-and-view-metrics](../../includes/storage-selector-portal-enable-and-view-metrics.md)]

## <a name="overview"></a>Vue d’ensemble

Par défaut, métriques de stockage n’est pas activée pour vos services de stockage. Vous pouvez activer la surveillance via le [Portail Azure](https://portal.azure.com) ou Windows PowerShell, ou par programme via la bibliothèque de stockage client.

Lorsque vous activez métriques de stockage, vous devez choisir une période de rétention pour les données : cette période détermine la durée pendant laquelle l’espace de stockage service conserve les mesures et les frais de l’espace requis pour stocker les. En règle générale, vous devez utiliser une période de rétention plus courte pour minute indicateurs que métriques horaire en raison de l’espace supplémentaire significative requis pour les indicateurs minutes. Vous devez choisir une période de rétention, tels que vous avez suffisamment de temps pour analyser les données et télécharger les mesures que vous souhaitez conserver pour une analyse hors ligne ou des rapports. N’oubliez pas que vous serez également facturé pour le téléchargement des données de métrique à partir de votre compte de stockage.

## <a name="how-to-enable-metrics-using-the-azure-portal"></a>Comment activer les indicateurs à l’aide du portail Azure

Procédez comme suit pour activer les indicateurs dans le [Portail Azure](https://portal.azure.com):

1. Accédez à votre compte de stockage.
1. Ouvrez la carte de **paramètres** et sélectionnez **Diagnostics**.
1. Assurez-vous que **l’état** est défini sur **activé**.
1. Sélectionnez les mesures pour les services que vous souhaitez analyser.
2. Spécifier une stratégie de rétention pour indiquer la durée de conserver les mesures et les données du journal.

Notez que le [Portail Azure](https://portal.azure.com) ne pas actuellement vous autorise à configurer les mesures minutes dans votre compte de stockage ; Vous devez activer la minute indicateurs à l’aide de PowerShell ou par programme.

## <a name="how-to-enable-metrics-using-powershell"></a>Comment activer les indicateurs à l’aide de PowerShell

Vous pouvez utiliser PowerShell sur votre ordinateur local pour configurer métriques de stockage dans votre compte de stockage en utilisant l’applet de commande PowerShell Azure Get-AzureStorageServiceMetricsProperty pour récupérer les paramètres actuels et l’applet de commande Set-AzureStorageServiceMetricsProperty pour modifier les paramètres actuels.

Les applets de commande qui contrôlent métriques de stockage utilisent les paramètres suivants :

- MetricsType : valeurs possibles sont les heures et minutes.

- ServiceType : valeurs possibles sont Blob, file d’attente et Table.

- MetricsLevel : valeurs possibles sont Aucun, le Service et ServiceAndApi.

Par exemple, la commande suivante bascule minute métriques pour le service Blob dans votre compte de stockage par défaut avec la période de rétention définie à cinq jours :

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

Après avoir configuré métriques de stockage Analytique pour surveiller votre compte de stockage, stockage Analytique enregistre les mesures dans un ensemble de tables connus dans votre compte de stockage. Vous pouvez configurer les graphiques pour afficher les mesures de toutes les heures dans le [Portail Azure](https://portal.azure.com):

1. Accédez à votre compte de stockage sur le [Portail Azure](https://portal.azure.com).
2. Dans la section **analyse** , cliquez sur **Ajouter des vignettes** pour ajouter un nouveau graphique. Dans la **Galerie de mosaïque**, sélectionnez la mesure que vous souhaitez afficher et faites-le glisser vers la section de **surveillance** .
3. Pour modifier les mesures sont affichées dans un graphique, cliquez sur le lien **Modifier** . Vous pouvez ajouter ou supprimer des mesures individuelles en sélectionnant ou les désélectionner.
4. Lorsque vous avez terminé de modifier les indicateurs, cliquez sur **Enregistrer** .

Si vous voulez télécharger les métriques de stockage à long terme ou pour les analyser localement, vous devez :

- Utilisez un outil qui est informé de ces tables et vous permet d’afficher et les télécharger.
- Écrire une application personnalisée ou un script pour lire et stocker les tables.

De nombreux outils de navigation sur le stockage de tiers connaissent l’existence de ces tables et vous permettent de les afficher directement.
Voir [Azure stockage Explorers](storage-explorers.md) pour obtenir la liste des outils disponibles.

> [AZURE.NOTE] Depuis la version 0.8.0 de [Microsoft Azure stockage Explorer] (http://storageexplorer.com/), vous maintenant seront en mesure d’afficher et télécharger les tables de mesures analytique.

Pour accéder aux tables analytique par programme, notez que les tables analytique n’apparaissent pas si vous répertoriez toutes les tables dans votre compte de stockage. Vous pouvez soit y accéder directement par leur nom, ou utilisez l' [API CloudAnalyticsClient](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.analytics.cloudanalyticsclient.aspx) dans la bibliothèque cliente .NET pour les noms des tables de requête.

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

Vous devez envisager paramètre des alertes dans le [Portail Azure](https://portal.azure.com) dans la page Moniteur afin que métriques de stockage peut être informé automatiquement des modifications importantes apportées dans le comportement de vos services de stockage. Si vous utilisez un outil de l’Explorateur d’espace de stockage pour télécharger ces données métriques dans un format délimité par des tabulations, vous pouvez utiliser Microsoft Excel pour analyser les données. Consultez le blog [Microsoft Azure stockage Explorers](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx) pour obtenir la liste des outils de l’Explorateur d’espace de stockage disponible.



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

## <a name="next-steps"></a>Étapes de la suivant :
[L’activation de stockage journalisation et accéder aux données du journal](https://msdn.microsoft.com/library/dn782840.aspx)
