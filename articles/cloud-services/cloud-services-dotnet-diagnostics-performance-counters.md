<properties
   pageTitle="Utiliser des compteurs de Performance dans les Diagnostics de Windows Azure | Microsoft Azure"
   description="Compteurs de performance dans les services en nuage Azure ou machine virtuelle permet de rechercher des engorgements et optimiser les performances."
   services="cloud-services"
   documentationCenter=".net"
   authors="rboucher"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/29/2016"
   ms.author="robb" />

# <a name="create-and-use-performance-counters-in-an-azure-application"></a>Créer et utiliser des compteurs de performance dans une application Azure

Cet article décrit les avantages des et comment placer des compteurs de performance dans votre application Azure. Vous pouvez les utiliser pour collecter les données, rechercher des engorgements et optimiser les performances du système et d’application.

Compteurs de performance disponibles pour Windows Server, IIS et ASP.NET peuvent également être collectées et utilisées pour déterminer l’état de vos rôles web Azure, les rôles de travail et les Machines virtuelles. Vous pouvez également créer et utiliser des compteurs de performance personnalisés.  

Vous pouvez examiner les données de compteur de performance
1. Directement sur l’hôte d’application avec l’outil Analyseur de performances accédé à l’aide de bureau à distance
2. System Center Operations Manager à l’aide de Azure Management Pack
3. Avec les autres outils d’analyse qui accèdent aux données de diagnostic transféré vers le stockage Azure. Pour plus d’informations, consultez [stocker et afficher les données de Diagnostic dans le stockage Azure](https://msdn.microsoft.com/library/azure/hh411534.aspx) .  

Pour plus d’informations sur l’analyse des performances de votre application dans le [portail classique Azure](http://manage.azure.com/), Découvrez [comment les Services en nuage moniteur](https://www.azure.com/manage/services/cloud-services/how-to-monitor-a-cloud-service/).

Pour plus d’informations détaillées sur la création d’un enregistrement et le suivi de stratégie et l’utilisation de diagnostics et autres techniques pour résoudre les problèmes et optimiser les applications Azure, voir [Résolution des problèmes de meilleures pratiques pour le développement d’Applications Azure](https://msdn.microsoft.com/library/azure/hh771389.aspx).


## <a name="enable-performance-counter-monitoring"></a>Activer le contrôle de compteur de performances

Compteurs de performance ne sont pas activés par défaut. Votre application ou une tâche de démarrage devez modifier la configuration de l’agent de diagnostics par défaut pour inclure les compteurs de performance spécifiques que vous souhaitez surveiller pour chaque instance de rôle.

### <a name="performance-counters-available-for-microsoft-azure"></a>Compteurs de performance disponibles pour Microsoft Azure

Azure fournit un sous-ensemble des compteurs de performance disponibles pour Windows Server, IIS et la pile ASP.NET. Le tableau suivant répertorie certains des compteurs de performance d’un intérêt particulier pour les applications Azure.

|Catégorie de compteur : Objet (Instance)|Nom du compteur      |Référence|
|---|---|---|
|Exceptions de CLR .NET (_Général_)|# Exceptions levées / sec   |Compteurs d’exception|
|Mémoire CLR .NET (_Général_)    |% Temps dans le catalogue global            |Compteurs de Performance de mémoire|
|ASP.NET                      |Redémarrage de l’application    |Compteurs de performance pour ASP.NET|
|ASP.NET                      |Durée de l’exécution de la requête  |Compteurs de performance pour ASP.NET|
|ASP.NET                      |Demandes déconnectées   |Compteurs de performance pour ASP.NET|
|ASP.NET                      |Redémarrage de processus de travail |Compteurs de performance pour ASP.NET|
|Applications ASP.NET (__Total__)|Total des demandes        |Compteurs de performance pour ASP.NET|
|Applications ASP.NET (__Total__)|Demandes/s          |Compteurs de performance pour ASP.NET|
|V4.0.30319 ASP.NET           |Durée de l’exécution de la requête  |Compteurs de performance pour ASP.NET|
|V4.0.30319 ASP.NET           |Demander le délai d’attente       |Compteurs de performance pour ASP.NET|
|V4.0.30319 ASP.NET           |Demandes actuelles        |Compteurs de performance pour ASP.NET|
|V4.0.30319 ASP.NET           |Demandes en file d’attente         |Compteurs de performance pour ASP.NET|
|V4.0.30319 ASP.NET           |Demandes refusées       |Compteurs de performance pour ASP.NET|
|Mémoire                       |Mégaoctets disponibles        |Compteurs de Performance de mémoire|
|Mémoire                       |Octets validées         |Compteurs de Performance de mémoire|
|Processeur (_Total)            |Temps du processeur        |Compteurs de performance pour ASP.NET|
|TCPv4                        |Échecs de connexion     |Objet TCP|
|TCPv4                        |Connexions établies |Objet TCP|
|TCPv4                        |Réinitialisation de connexions       |Objet TCP|
|TCPv4                        |Segments envoyés/s       |Objet TCP|
|Interface(*) réseau         |Octets reçus/s      |Objet Interface réseau|
|Interface(*) réseau         |Octets envoyés/s          |Objet Interface réseau|
|Interface réseau (Microsoft Machine virtuelle Bus réseau carte _2)|Octets reçus/s|Objet Interface réseau|
|Interface réseau (Microsoft Machine virtuelle Bus réseau carte _2)|Octets envoyés/s|Objet Interface réseau|
|Interface réseau (Microsoft Machine virtuelle Bus réseau carte _2)|Total des octets/s|Objet Interface réseau|

## <a name="create-and-add-custom-performance-counters-to-your-application"></a>Créer et ajouter des compteurs de performance personnalisés à votre application

Azure prend en charge pour créer et modifier des compteurs de performance personnalisés pour les rôles web et les rôles de travail. Les compteurs peuvent être utilisés pour suivre et analyser comportement spécifiques à l’application. Vous pouvez créer et supprimer des catégories de compteurs de performance personnalisés et des spécificateurs d’une tâche au démarrage, le rôle web ou le rôle de travail avec des autorisations élevées.

>[AZURE.NOTE] Code qui apporte des modifications aux compteurs de performance personnalisés doit ont des autorisations pour exécuter élevées. Si le code est dans un rôle web ou de travail, le rôle doit inclure la balise <Runtime executionContext="elevated" /> dans le fichier ServiceDefinition.csdef pour le rôle initialisé correctement.

Vous pouvez envoyer des données compteur de performance personnalisé au stockage Azure à l’aide de l’agent de diagnostics.

Les données du compteur performances standard sont générées par les processus Azure. Données compteur de performance personnalisé doivent être créées par votre application web rôle ou collaborateur rôle. Pour plus d’informations sur les types de données pouvant être stockés dans des compteurs de performance personnalisés, voir [Types de compteur de Performance](https://msdn.microsoft.com/library/z573042h.aspx) . Voir des [Exemples de compteurs de performance](http://code.msdn.microsoft.com/azure/) pour obtenir un exemple qui crée et ensembles de données compteur de performance personnalisé dans un rôle web.

## <a name="store-and-view-performance-counter-data"></a>Stocker et afficher des données compteur de performance

Azure met en cache les données de compteur de performance avec d’autres informations de diagnostic. Ces données sont disponibles pour le contrôle à distance pendant l’exécution de l’instance de rôles à l’aide d’accès Bureau à distance pour afficher les outils tels que l’Analyseur de performances. Pour conserver les données en dehors de l’instance de rôle, l’agent de diagnostics doit transférer les données vers le stockage Azure. La limite de taille des données de compteur de performances mis en cache peut être configurée dans l’agent de diagnostics, ou il peut être configuré pour faire partie d’une limite partagée pour toutes les données de Diagnostics. Pour plus d’informations sur la définition de la taille de la mémoire tampon, voir [OverallQuotaInMB](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.diagnostics.diagnosticmonitorconfiguration.overallquotainmb.aspx) et [DirectoriesBufferConfiguration](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.diagnostics.directoriesbufferconfiguration.aspx). Consultez [stocker et afficher les données de Diagnostic dans le stockage Azure](https://msdn.microsoft.com/library/azure/hh411534.aspx) offre un aperçu de la configuration de l’agent de diagnostic pour transférer des données à un compte de stockage.

Chaque instance de compteur de performance configurée est enregistrée à un taux d’échantillonnage spécifié, et les données échantillonnées sont transférées vers le compte de stockage par une demande de transfert planifiée ou une demande de transfert à la demande. Transferts automatiques peuvent être planifiées aussi souvent qu’une fois par minute. Données de compteur de performance transférées par l’agent de diagnostics sont stockées dans une table, WADPerformanceCountersTable, dans le compte de stockage. Ce tableau peut être accessible et interrogé avec les méthodes de stockage Azure standard API. Consultez [Microsoft Azure compteurs de performance exemple](http://code.msdn.microsoft.com/Windows-Azure-PerformanceCo-7d80ebf9) pour obtenir un exemple d’interroger et d’afficher des données de compteur de performance de la table WADPerformanceCountersTable.

>[AZURE.NOTE] Selon la fréquence de transfert de l’agent de diagnostics et de latence file d’attente, les données les plus récentes compteur des performances dans le compte de stockage peuvent être obsolète après quelques minutes.

## <a name="enable-performance-counters-using-diagnostics-configuration-file"></a>Activer les compteurs de performance à l’aide du fichier de configuration des diagnostics

Utilisez la procédure suivante pour activer les compteurs de performances dans votre application Azure.

## <a name="prerequisites"></a>Conditions préalables

Cette section suppose que vous avez importé le moniteur Diagnostics dans votre application et ajouté le fichier de configuration diagnostics à votre solution Visual Studio (diagnostics.wadcfg SDK 2.4 et en dessous ou diagnostics.wadcfgx dans SDK 2.5 et version ultérieure). Consultez les étapes 1 et 2 de [L’activation des Diagnostics dans Azure Cloud Services et Machines virtuelles](./cloud-services-dotnet-diagnostics.md)) pour plus d’informations.

## <a name="step-1-collect-and-store-data-from-performance-counters"></a>Étape 1 : Collecter et stocker les données des compteurs de performance

Une fois que vous avez ajouté le fichier diagnostics à votre solution Visual Studio, vous pouvez configurer la collection de sites et le stockage de données compteur de performance dans une application Azure. Pour cela, vous devez ajouter des compteurs de performance au fichier diagnostics. Données Diagnostics, y compris les compteurs de performances, recueillies tout d’abord sur l’instance. Les données sont conservées puis dans la table WADPerformanceCountersTable dans le service de Table Azure, vous devez également spécifier le compte de stockage dans votre application. Si vous testez votre application localement dans l’émulateur de calcul, vous pouvez également stocker localement les données de diagnostic dans l’émulateur de stockage. Avant d’enregistrer des données de diagnostic vous devez tout d’abord accédez au [portail classique Azure](http://manage.windowsazure.com/) et créer un compte de stockage. Une meilleure solution consiste à trouver votre compte de stockage au même emplacement géographique que votre application Azure afin d’éviter les coûts de bande passante externes d’un abonnement payant et pour réduire la latence.

### <a name="add-performance-counters-to-the-diagnostics-file"></a>Ajouter des compteurs de performance au fichier diagnostics

Il existe de nombreux compteurs, que vous pouvez utiliser. L’exemple suivant illustre plusieurs compteurs de performance qui sont recommandées pour web et contrôle de rôle de travail.

Ouvrez le fichier diagnostics (diagnostics.wadcfg SDK 2.4 et en dessous ou diagnostics.wadcfgx dans SDK 2.5 et versions ultérieures) et ajoutez les éléments suivants à l’élément DiagnosticMonitorConfiguration :

```
    <PerformanceCounters bufferQuotaInMB="0" scheduledTransferPeriod="PT30M">
       <PerformanceCounterConfiguration counterSpecifier="\Memory\Available Bytes" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT30S" />

    <!-- Use the Process(w3wp) category counters in a web role -->

       <PerformanceCounterConfiguration counterSpecifier="\Process(w3wp)\% Processor Time" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\Process(w3wp)\Private Bytes" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\Process(w3wp)\Thread Count" sampleRate="PT30S" />

    <!-- Use the Process(WaWorkerHost) category counters in a worker role.
       <PerformanceCounterConfiguration counterSpecifier="\Process(WaWorkerHost)\% Processor Time" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\Process(WaWorkerHost)\Private Bytes" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\Process(WaWorkerHost)\Thread Count" sampleRate="PT30S" />
    -->

       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Interop(_Global_)\# of marshalling" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Loading(_Global_)\% Time Loading" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR LocksAndThreads(_Global_)\Contention Rate / sec" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Memory(_Global_)\# Bytes in all Heaps" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Networking(_Global_)\Connections Established" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Remoting(_Global_)\Remote Calls/sec" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Jit(_Global_)\% Time in Jit" sampleRate="PT30S" />
    </PerformanceCounters>
```

L’attribut bufferQuotaInMB, qui indique la quantité maximale de stockage de système de fichiers n’est disponible pour le type de collection de données (journaux Azure, journaux IIS, etc.). La valeur par défaut est égal à 0. Lorsque le quota est atteint, les données du plus ancien sont supprimées l’ajout de nouvelles données. La somme de toutes les propriétés bufferQuotaInMB doit être supérieure à la valeur de l’attribut OverallQuotaInMB. Pour une description plus détaillée de déterminer la quantité de stockage seront requis pour la collecte de données de diagnostic, consultez la section Configuration WAD de [Résolution des problèmes de meilleures pratiques pour le développement d’Applications Azure](https://msdn.microsoft.com/library/windowsazure/hh771389.aspx).

L’attribut scheduledTransferPeriod, qui spécifie l’intervalle entre les transferts planifiées des données, arrondi à la minute la plus proche. Dans les exemples suivants, il est défini sur PT30M (30 minutes). Si vous définissez la période de transfert pour une petite valeur, comme une minute, impact négatif sur les performances de votre application en production mais peut être utile pour voir diagnostics utilisation rapidement lorsque vous testez. La période de transfert planifiée doit être ajustée pour vous assurer que les données de Diagnostics ne sont pas remplacées sur l’instance, mais assez grande pour qu’il aura aucun impact sur les performances de votre application.

L’attribut counterSpecifier Spécifie le compteur de performance pour recueillir. L’attribut sampleRate spécifie la fréquence à laquelle le compteur de performance doit être échantillonné, dans ce cas 30 secondes.

Une fois que vous avez ajouté les compteurs de performance que vous souhaitez collecter, enregistrer vos modifications dans le fichier diagnostics. Ensuite, vous devez spécifier le compte de stockage que les données de diagnostic seront rendue persistante à.

### <a name="specify-the-storage-account"></a>Spécifier le compte de stockage

Pour conserver vos informations de diagnostic à votre compte de stockage Azure, vous devez spécifier une chaîne de connexion dans votre fichier de configuration (ServiceConfiguration.cscfg) service.

Pour Azure SDK 2.5 du compte de stockage peuvent être spécifié dans le fichier diagnostics.wadcfgx.

>[AZURE.NOTE] Ces instructions s’appliquent uniquement à Azure SDK 2.4 et en dessous. Pour Azure SDK 2.5 du compte de stockage peuvent être spécifié dans le fichier diagnostics.wadcfgx.

Pour définir les chaînes de connexion :

1. Ouvrez le fichier ServiceConfiguration.Cloud.cscfg à l’aide de votre éditeur de texte et définissez la chaîne de connexion pour votre espace de stockage. Les valeurs de *nom de compte* et *AccountKey* sont trouvent dans le portail Azure classique dans le tableau de bord stockage compte sous Gérer les clés.

    ```
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<name>;AccountKey=<key>"/>
    </ConfigurationSettings>
    ```
2. Enregistrez le fichier ServiceConfiguration.Cloud.cscfg.

3. Ouvrez le fichier ServiceConfiguration.Local.cscfg et vérifiez que UseDevelopmentStorage est défini sur true.

    ```
    <ConfigurationSettings>
      <Settingname="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true"/>
    </ConfigurationSettings>
    ```
À présent que les chaînes de connexion sont définis, votre application sera persistante à votre compte de stockage des données de diagnostic lorsque votre application est déployée.
4. Enregistrer et générer votre projet, puis déployer votre application.

## <a name="step-2-optional-create-custom-performance-counters"></a>Étape 2 : Compteurs de performance personnalisés créer (facultatif)

Outre les compteurs de performance prédéfinis, vous pouvez ajouter vos propres compteurs de performance personnalisés pour surveiller les rôles web ou collaborateur. Compteurs de performance personnalisés peuvent servir à suivre et surveiller comportement spécifiques à l’application et peuvent être créés ou supprimés dans une tâche au démarrage, le rôle web ou le rôle de travail avec des autorisations élevées.

L’agent de diagnostics de Windows Azure actualise la configuration de compteur de performance à partir du fichier .wadcfg une minute après avoir démarré.  Si vous avez créé des compteurs de performance personnalisés dans la méthode OnStart et vos tâches démarrage durent une minute à exécuter, votre compteurs de performance personnalisés n’ont été créées lors de l’agent de Diagnostics Azure essaie de charger les.  Dans ce scénario, vous verrez qu’Azure Diagnostics capture correctement toutes les données de diagnostics à l’exception de vos compteurs de performance personnalisés.  Pour résoudre ce problème, créez des compteurs de performance dans une tâche de démarrage ou déplacer certaines des estimations démarrage fonctionnent à la méthode OnStart après la création des compteurs de performance.

Procédez comme suit pour créer une simple performance personnalisés compteur nommée « \MyCustomCounterCategory\MyButton1Counter » :

1. Ouvrez le fichier de définition de service (CSDEF) pour votre application.
2. Ajouter l’élément Runtime à la WebRole ou l’élément WorkerRole pour autoriser l’exécution avec des privilèges élevés :

    ```
    <runtime executioncontext="elevated"/>
    ```
3. Enregistrez le fichier.
4. Ouvrez le fichier diagnostics (diagnostics.wadcfg SDK 2.4 et en dessous ou diagnostics.wadcfgx dans SDK 2.5 et versions ultérieures) et ajoutez le code suivant la DiagnosticMonitorConfiguration 

    ```
    <PerformanceCounters bufferQuotaInMB="0" scheduledTransferPeriod="PT30M">
     <PerformanceCounterConfiguration counterSpecifier="\MyCustomCounterCategory\MyButton1Counter" sampleRate="PT30S"/>
    </PerformanceCounters>
    ```
5. Enregistrez le fichier.
6. Créer la catégorie de compteur de performance personnalisé dans la méthode OnStart de votre rôle, avant l’appel de base. OnStart. L’exemple c# suivant crée une catégorie personnalisée, s’il n’existe pas déjà :

    ```
    public override bool OnStart()
    {
    if (!PerformanceCounterCategory.Exists("MyCustomCounterCategory"))
    {
       CounterCreationDataCollection counterCollection = new CounterCreationDataCollection();

       // add a counter tracking user button1 clicks
       CounterCreationData operationTotal1 = new CounterCreationData();
       operationTotal1.CounterName = "MyButton1Counter";
       operationTotal1.CounterHelp = "My Custom Counter for Button1";
       operationTotal1.CounterType = PerformanceCounterType.NumberOfItems32;
       counterCollection.Add(operationTotal1);

       PerformanceCounterCategory.Create(
         "MyCustomCounterCategory",
         "My Custom Counter Category",
         PerformanceCounterCategoryType.SingleInstance, counterCollection);

       Trace.WriteLine("Custom counter category created.");
    }
    else{
       Trace.WriteLine("Custom counter category already exists.");
    }

    return base.OnStart();
    }
    ```
7. Mettre à jour les compteurs au sein de votre application. L’exemple suivant met à jour un compteur de performance personnalisé sur les événements Button1_Click :

    ```
    protected void Button1_Click(object sender, EventArgs e)
        {
         PerformanceCounter button1Counter = new PerformanceCounter(
           "MyCustomCounterCategory",
           "MyButton1Counter",
           string.Empty,
           false);
         button1Counter.Increment();
        this.Button1.Text = "Button 1 count: " +
           button1Counter.RawValue.ToString();
        }
    ```
8. Enregistrez le fichier.  

Données compteur de performance personnalisé seront désormais collectées par le moniteur diagnostics de Windows Azure.

## <a name="step-3-query-performance-counter-data"></a>Étape 3 : Données compteur de performance de la requête

Une fois que votre application est déployée et en cours d’exécution l’écran de Diagnostics commence la collecte des compteurs de performance et conserver ces données au stockage Azure. Outils tels qu’Explorateur de serveurs dans Visual Studio, [Explorateur de stockage Azure](http://azurestorageexplorer.codeplex.com/)ou [Azure Diagnostics Gestionnaire](http://www.cerebrata.com/Products/AzureDiagnosticsManager/Default.aspx) par Cerebrata vous permet d’afficher les données de compteurs de performance dans la table WADPerformanceCountersTable. Vous pouvez également par programmation interroger le service de tableau à l’aide de [c#](../storage/storage-dotnet-how-to-use-tables.d), [Java](../storage/storage-java-how-to-use-table-storage.md), [Node.js](../storage/storage-nodejs-how-to-use-table-storage.md), [Python](../storage/storage-python-how-to-use-table-storage.md), [Ruby](../storage/storage-ruby-how-to-use-table-storage.md)ou [PHP](../storage/storage-php-how-to-use-table-storage.md).

L’exemple c# suivant montre une requête simple à la table WADPerformanceCountersTable et enregistre les données de diagnostic dans un fichier CSV. Une fois que les compteurs de performance sont enregistrés dans un fichier CSV, vous pouvez utiliser les fonctionnalités graphiques dans Microsoft Excel ou un autre outil pour visualiser les données. Veillez à ajouter une référence à Microsoft.WindowsAzure.Storage.dll, ce qui est inclus dans le Kit de développement Azure pour .NET octobre 2012 et versions ultérieures. L’assembly est installé à l’annuaire de programme Files%\Microsoft SDKs\Microsoft Azure.NET SDK\version-num\ref\ %.

```
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Table;
    ...

    // Get the connection string. When using Microsoft Azure Cloud Services, it is recommended
    // you store your connection string using the Microsoft Azure service configuration
    // system (*.csdef and *.cscfg files). You can you use the CloudConfigurationManager type
    // to retrieve your storage connection string.  If you're not using Cloud Services, it's
    // recommended that you store the connection string in your web.config or app.config file.
    // Use the ConfigurationManager type to retrieve your storage connection string.

    string connectionString = Microsoft.WindowsAzure.CloudConfigurationManager.GetSetting("StorageConnectionString");
    //string connectionString = System.Configuration.ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString;

    // Get a reference to the storage account using the connection string.  You can also use the development
    // storage account (Storage Emulator) for local debugging.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
    //CloudStorageAccount storageAccount = CloudStorageAccount.DevelopmentStorageAccount;

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "WADPerformanceCountersTable" table.
    CloudTable table = tableClient.GetTableReference("WADPerformanceCountersTable");

    // Create the table query, filter on a specific CounterName, DeploymentId and RoleInstance.
    TableQuery<PerformanceCountersEntity> query = new TableQuery<PerformanceCountersEntity>()
       .Where(
          TableQuery.CombineFilters(
          TableQuery.GenerateFilterCondition("CounterName", QueryComparisons.Equal, @"\Processor(_Total)\% Processor Time"),
          TableOperators.And,
          TableQuery.CombineFilters(
          TableQuery.GenerateFilterCondition("DeploymentId", QueryComparisons.Equal, "ec26b7a1720447e1bcdeefc41c4892a3"),
          TableOperators.And,
          TableQuery.GenerateFilterCondition("RoleInstance", QueryComparisons.Equal, "WebRole1_IN_0")
          )
       )
    );

    // Execute the table query.
    IEnumerable<PerformanceCountersEntity> result = table.ExecuteQuery(query);

    // Process the query results and build a CSV file.
    StringBuilder sb = new StringBuilder("TimeStamp,EventTickCount,DeploymentId,Role,RoleInstance,CounterName,CounterValue\n");

    foreach (PerformanceCountersEntity entity in result)
    {
       sb.Append(entity.Timestamp + "," + entity.EventTickCount + "," + entity.DeploymentId + ","
          + entity.Role + "," + entity.RoleInstance + "," + entity.CounterName + "," + entity.CounterValue+"\n");
    }

    StreamWriter sw = File.CreateText(@"C:\temp\PerfCounters.csv");
    sw.Write(sb.ToString());
    sw.Close();
```

Entités établir une correspondance entre les objets c# à l’aide d’une classe personnalisée dérivée de **TableEntity**. Le code suivant définit une classe d’entité qui représente un compteur de performance dans la table **WADPerformanceCountersTable** .


    public class PerformanceCountersEntity : TableEntity
    {
       public long EventTickCount { get; set; }
       public string DeploymentId { get; set; }
       public string Role { get; set; }
       public string RoleInstance { get; set; }
       public string CounterName { get; set; }
       public double CounterValue { get; set; }
    }


## <a name="next-steps"></a>Étapes suivantes
[Afficher des articles supplémentaires sur les Diagnostics Azure] (.. / azure diagnostics.md)
