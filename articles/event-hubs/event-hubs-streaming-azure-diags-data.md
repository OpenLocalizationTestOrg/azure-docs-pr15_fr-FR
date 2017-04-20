<properties
    pageTitle="Diffusion en continu de données Azure Diagnostics dans le chemin d’accès rapide utilisant centraux et événements | Microsoft Azure"
    description="Montre comment configurer des Diagnostics Azure avec Hubs événement de bout en bout, y compris les conseils pour des scénarios courants."
    services="event-hubs"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags
    ms.service="event-hubs"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="07/14/2016"
    ms.author="sethm" />

# <a name="streaming-azure-diagnostics-data-in-the-hot-path-by-using-event-hubs"></a>Diffusion en continu de données Azure Diagnostics dans le chemin d’accès rapide à l’aide de Hubs d’événement

Diagnostics de Windows Azure propose des méthodes flexibles pour collecter les mesures et les journaux des machines virtuelles de services cloud (machines virtuelles) et transférer les résultats au stockage Azure. À partir de l’intervalle de temps mars 2016 (2,9 SDK), vous pouvez recevoir Diagnostics aux sources de données entièrement personnalisé et transférer des données de chemin réactif en secondes à l’aide de [Azure événement Hubs](https://azure.microsoft.com/services/event-hubs/).

Types de données pris en charge sont les suivantes :

- Événements de suivi pour Windows (ETW)
- Compteurs de performance
- Journaux d’événements Windows
- Journaux d’application
- Journaux d’infrastructure Diagnostics Azure

Cet article vous explique comment configurer des Diagnostics Azure avec Hubs événement de bout en bout. Conseils sont également fournis pour les scénarios courants suivants :

- Comment personnaliser les journaux et les mesures obtenir sinked aux Hubs d’événement
- Comment modifier les configurations dans chaque environnement
- Comment afficher les données de flux Hubs d’événement
- Comment résoudre les problèmes de la connexion  

## <a name="prerequisites"></a>Conditions préalables

Événement Hubs couler dans Azure Diagnostics est pris en charge dans les Services en nuage, machines virtuelles, jeux d’échelle Machine virtuelle et tissu de Service depuis le 2,9 SDK Azure et les outils Azure correspondant pour Visual Studio.

- Extension de Diagnostics Azure 1,6 ([Azure SDK pour .NET 2,9 ou version ultérieure](https://azure.microsoft.com/downloads/) cible cela par défaut)
- [Visual Studio 2013 ou version ultérieure](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
- Configurations existantes des Diagnostics Azure dans une application à l’aide d’un fichier *.wadcfgx* et une des méthodes suivantes :
    - Visual Studio : [Configuration des Diagnostics pour les Services de Cloud Azure et Machines virtuelles](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md)
    - Windows PowerShell : [Activer les diagnostics de Services de Cloud Azure à l’aide de PowerShell](../cloud-services/cloud-services-diagnostics-powershell.md)
- Espace de noms de Hubs événement mis en service par l’article de la [prise en main avec Hubs d’événement](./event-hubs-csharp-ephcs-getstarted.md)

## <a name="connect-azure-diagnostics-to-event-hubs-sink"></a>Se connecter Azure Diagnostics récepteur de Hubs d’événement

Diagnostics de Windows Azure récepteurs toujours les journaux et les mesures, par défaut, à un compte de stockage Azure. Une application peut en outre récepteur aux événements Hubs en ajoutant une nouvelle section **récepteurs** à l’élément **WadCfg** dans la section **PublicConfig** du fichier *.wadcfgx* . Dans Visual Studio, il est stocké le fichier *.wadcfgx* à l’emplacement suivant : **Projet de Service Cloud** > **rôles** >  **(RoleName)** > **diagnostics.wadcfgx** fichier.

```
<SinksConfig>
  <Sink name="HotPath">
    <EventHub Url="https://diags-mycompany-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" />
  </Sink>
</SinksConfig>
```

Dans cet exemple, l’URL du concentrateur événement est défini sur l’espace de noms complet du concentrateur événement : espace de noms événement Hubs + « / » + nom Hub de l’événement.  

L’URL du concentrateur événement s’affiche dans le [portail Azure](http://go.microsoft.com/fwlink/?LinkID=213885) dans le tableau de bord Hubs événement.  

Le nom de **récepteur** peut être défini à n’importe quelle chaîne valide tant que la même valeur est utilisée tout au long du fichier de configuration.

> [AZURE.NOTE]  Il peut y avoir récepteurs supplémentaires, tels *applicationInsights* configuré dans cette section. Diagnostics de Windows Azure permet à un ou plusieurs récepteurs à définir si chaque récepteur est également déclaré dans la section **PrivateConfig** .  

Le récepteur d’événement Hubs doit également être déclaré et défini dans la section **PrivateConfig** du fichier de configuration de la *.wadcfgx* .

```
<PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <StorageAccount name="" key="" endpoint="" />
  <EventHub Url="https://diags-mycompany-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" SharedAccessKey="9B3SwghJOGEUvXigc6zHPInl2iYxrgsKHZoy4nm9CUI=" />
</PrivateConfig>
```

La `SharedAccessKeyName` valeur doit correspondre à une clé de Signature accès partagé (sa) et la stratégie a été défini dans l’espace de noms **Hubs événement** . Accédez au tableau de bord Hubs événement dans le [portail Azure](https://manage.windowsazure.com), cliquez sur l’onglet **configurer** et configurer une stratégie nommée (par exemple, « SendRule ») qui dispose des autorisations *Envoyer* . Le **StorageAccount** est également déclaré dans **PrivateConfig**. Il est inutile de modifier des valeurs ici s’il sont agit. Dans cet exemple, nous laisser les valeurs vides, qui est un signe qu’un actif en aval définira les valeurs. Par exemple, le fichier de configuration d’environnement *ServiceConfiguration.Cloud.cscfg* définit les noms appropriés environnement et les clés.  

> [AZURE.WARNING] La clé d’événement Hubs sa est stockée en texte brut dans le fichier *.wadcfgx* . Souvent, cette touche archivée au contrôle de code source ou est disponible en tant qu’actif dans votre serveur de génération, afin que vous devez le protéger le cas échéant. Nous vous recommandons d’utiliser une clé de sa ici avec des autorisations *Envoyer uniquement* afin qu’un utilisateur malveillant peut écrire dans le Hub de l’événement, mais pas écouter ni gérer.

## <a name="configure-azure-diagnostics-logs-and-metrics-to-sink-with-event-hubs"></a>Configurer les journaux de Diagnostics Azure et les mesures pour recevoir avec Hubs d’événement

Comme indiqué précédemment, toutes les données de diagnostic personnalisés et par défaut, c'est-à-dire, indicateurs et des journaux, est automatiquement sinked au stockage Azure dans les intervalles configurés. Dans l’événement Hubs ou tout récepteur supplémentaire, vous pouvez spécifier n’importe quel nœud racine ou feuille dans la hiérarchie pour être sinked avec le Hub de l’événement. Cela inclut les événements ETW, compteurs de performance, les journaux d’événements Windows et les journaux d’application.   

Il est important de prendre en compte le nombre de points de données doit être transféré réellement aux événements Hubs. En règle générale, les développeurs transfèrent des données de chemin d’accès à chaud faible latence qui doivent être consommées et interprétées rapidement. Systèmes de surveiller des alertes ou des règles d’échelle automatique sont des exemples. Un développeur peut également configurer une banque de substitution analyse ou recherchez store--par exemple, Azure flux Analytique, Elasticsearch, un système de surveillance personnalisé ou un système de surveillance favori à partir d’autres personnes.

Voici quelques exemples de configuration.

```
<PerformanceCounters scheduledTransferPeriod="PT1M" sinks="HotPath">
  <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Requests/Sec" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Errors Total/Sec" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Queued" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Rejected" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" />
</PerformanceCounters>
```

Dans l’exemple suivant, le récepteur est appliqué vers le nœud **compteurs de performance** parent dans la hiérarchie, ce qui signifie que tous les enfants **compteurs de performance** seront envoyé aux événements Hubs.  

```
<PerformanceCounters scheduledTransferPeriod="PT1M">
  <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Requests/Sec" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Errors Total/Sec" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Queued" sampleRate="PT3M" sinks="HotPath" />
  <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Rejected" sampleRate="PT3M" sinks="HotPath"/>
  <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" sinks="HotPath"/>
</PerformanceCounters>
```

Dans l’exemple précédent, le récepteur est appliqué à uniquement trois compteurs : **Demandes en file d’attente**, **Demandes refusées**et **temps du processeur**.  

L’exemple suivant montre comment un développeur peut limiter la quantité de données envoyées à des valeurs critiques servent d’intégrité de ce service.  

```
<Logs scheduledTransferPeriod="PT1M" sinks="HotPath" scheduledTransferLogLevelFilter="Error" />
```

Dans cet exemple, le récepteur est appliqué aux journaux et est filtré uniquement aux trace au niveau de l’erreur.

## <a name="deploy-and-update-a-cloud-services-application-and-diagnostics-config"></a>Déployer et mettre à jour une configuration d’application et les diagnostics de Services Cloud

Visual Studio fournit la plus simple pour déployer l’application et la configuration de récepteur Hubs événement. Pour afficher et modifier le fichier, ouvrez le fichier *.wadcfgx* dans Visual Studio, modifier et enregistrer. Le chemin d’accès est le **Projet de Service Cloud** > **rôles** > **(RoleName)** > **diagnostics.wadcfgx**.  

À ce stade, tous les déploiement déploiement mise à jour et actions dans Visual Studio, Visual Studio Team System et toutes les commandes ou scripts basés sur MSBuild et l’utilisation du **/t : publier** cible inclure la *.wadcfgx* dans le processus de création d’un package. En outre, les mises à jour et les déploiements déploiement le fichier vers Azure à l’aide de l’extension de l’agent Azure Diagnostics appropriée sur vos ordinateurs virtuels.

Une fois que vous déployez l’application et la configuration de Diagnostics Azure, vous pouvez immédiatement voir activité dans le tableau de bord du concentrateur événement. Cela signifie que vous êtes prêt à passer à l’affichage des données du tracé hot dans l’outil récepteur client ou l’analyse de votre choix.  

Dans l’illustration suivante, le tableau de bord événement Hubs affiche envoi exact de données de diagnostic pour le Hub événement commençant un certain temps après 11 PM. Qui est lorsque l’application a été déployée avec un fichier mis à jour *.wadcfgx* et le récepteur a été configuré correctement.

![][0]  

> [AZURE.NOTE] Lorsque vous effectuez des mises à jour le fichier de configuration de la Azure Diagnostics (.wadcfgx), il est recommandé de distribuer les mises à jour à l’ensemble de l’application, ainsi que la configuration à l’aide de la publication Visual Studio, ou un script Windows PowerShell.  

## <a name="view-hot-path-data"></a>Afficher les données de chemin d’accès à chaud

Comme indiqué précédemment, il existe de nombreux cas d’utilisation pour écouter et traitement des données de l’événement Hubs.

Une approche simple consiste à créer une application console de test petite pour écouter le Hub de l’événement et imprimer le flux de sortie. Vous pouvez placer le code suivant, qui est expliqué plus en détail dans [prise en main événement Hubs](./event-hubs-csharp-ephcs-getstarted.md)), dans une application console.  

Notez que l’application console doit inclure le [package événement processeur hôte Nuget](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/).  

N’oubliez pas de remplacer les valeurs crochets dans la fonction **Main** avec des valeurs pour vos ressources.   

```
//Console application code for EventHub test client
using System;
using System.Collections.Generic;
using System.Diagnostics;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using Microsoft.ServiceBus.Messaging;

namespace EventHubListener
{
    class SimpleEventProcessor : IEventProcessor
    {
        Stopwatch checkpointStopWatch;

        async Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
        {
            Console.WriteLine("Processor Shutting Down. Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason);
            if (reason == CloseReason.Shutdown)
            {
                await context.CheckpointAsync();
            }
        }

        Task IEventProcessor.OpenAsync(PartitionContext context)
        {
            Console.WriteLine("SimpleEventProcessor initialized.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset);
            this.checkpointStopWatch = new Stopwatch();
            this.checkpointStopWatch.Start();
            return Task.FromResult<object>(null);
        }

        async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
        {
            foreach (EventData eventData in messages)
            {
                string data = Encoding.UTF8.GetString(eventData.GetBytes());
                    Console.WriteLine(string.Format("Message received.  Partition: '{0}', Data: '{1}'",
                        context.Lease.PartitionId, data));

                foreach (var x in eventData.Properties)
                {
                    Console.WriteLine(string.Format("    {0} = {1}", x.Key, x.Value));
                }
            }

            //Call checkpoint every 5 minutes, so that worker can resume processing from 5 minutes back if it restarts.
            if (this.checkpointStopWatch.Elapsed > TimeSpan.FromMinutes(5))
            {
                await context.CheckpointAsync();
                this.checkpointStopWatch.Restart();
            }
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            string eventHubConnectionString = "Endpoint= <your connection string>”
            string eventHubName = "<Event Hub name>";
            string storageAccountName = "<Storage account name>";
            string storageAccountKey = "<Storage account key>”;
            string storageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", storageAccountName, storageAccountKey);

            string eventProcessorHostName = Guid.NewGuid().ToString();
            EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, eventHubName, EventHubConsumerGroup.DefaultGroupName, eventHubConnectionString, storageConnectionString);
            Console.WriteLine("Registering EventProcessor...");
            var options = new EventProcessorOptions();
            options.ExceptionReceived += (sender, e) => { Console.WriteLine(e.Exception); };
            eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>(options).Wait();

            Console.WriteLine("Receiving. Press enter key to stop worker.");
            Console.ReadLine();
            eventProcessorHost.UnregisterEventProcessorAsync().Wait();
        }
    }
}
```

## <a name="troubleshoot-event-hubs-sink"></a>Résoudre les problèmes d’événement Hubs récepteur

- Le Hub événement n’affiche pas d’événement entrants ou sortants activité comme prévu.

    Vérifiez que votre plateforme d’événement est correctement mis en service. Toutes les informations de connexion dans la section **PrivateConfig** de *.wadcfgx* doivent correspondre aux valeurs de la ressource comme indiqué dans le portail. Vérifiez que vous disposez d’une stratégie de sa définie (« SendRule » dans l’exemple) dans le portail et que l’autorisation *Envoyer* est accordée.  

- Après une mise à jour, le Hub de l’événement ne s’affiche plus activité événement entrants ou sortants.

    Tout d’abord, vérifiez que les informations d’événement concentrateur et la configuration sont correcte, comme expliqué précédemment. Parfois, le **PrivateConfig** est réinitialisé dans une mise à jour du déploiement. La solution recommandée est apporter toutes les modifications apportées aux *.wadcfgx* dans le projet, puis appuyez sur une mise à jour de l’application complète. Si ce n’est pas possible, assurez-vous que la mise à jour diagnostics pousse un complète **PrivateConfig** qui comporte la touche associations de sécurité.  

- J’ai tenté les suggestions, puis le Hub de l’événement ne fonctionne toujours pas.

    Essayez de rechercher dans la table de stockage Azure qui contient les journaux et les erreurs des Diagnostics Azure lui-même : **WADDiagnosticInfrastructureLogsTable**. Une seule option consiste à utiliser un outil comme [Explorateur de stockage Azure](http://www.storageexplorer.com) pour vous connecter à ce compte de stockage, afficher ce tableau et ajouter une requête pour horodatage dans les dernières 24 heures. Vous pouvez utiliser l’outil pour exporter un fichier .csv, ouvrez-le dans une application telle que Microsoft Excel. Excel facilite rechercher des chaînes de carte d’appel, par exemple **EventHubs**, pour voir quels erreur est signalée.  

## <a name="next-steps"></a>Étapes suivantes

[En savoir plus sur l’événement Hubs](https://azure.microsoft.com/services/event-hubs/) •

## <a name="appendix-complete-azure-diagnostics-configuration-file-wadcfgx-example"></a>Appendice : Terminer l’exemple de fichier (.wadcfgx) de configuration Diagnostics Azure

```
<?xml version="1.0" encoding="utf-8"?>
<DiagnosticsConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <WadCfg>
      <DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="applicationInsights.errors">
        <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error" />
        <Directories scheduledTransferPeriod="PT1M">
          <IISLogs containerName="wad-iis-logfiles" />
          <FailedRequestLogs containerName="wad-failedrequestlogs" />
        </Directories>
        <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="HotPath">
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Requests/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Errors Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Queued" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Rejected" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" />
        </PerformanceCounters>
        <WindowsEventLog scheduledTransferPeriod="PT1M">
          <DataSource name="Application!*" />
        </WindowsEventLog>
        <CrashDumps>
          <CrashDumpConfiguration processName="WaIISHost.exe" />
          <CrashDumpConfiguration processName="WaWorkerHost.exe" />
          <CrashDumpConfiguration processName="w3wp.exe" />
        </CrashDumps>
        <Logs scheduledTransferPeriod="PT1M" sinks="HotPath" scheduledTransferLogLevelFilter="Error" />
      </DiagnosticMonitorConfiguration>
      <SinksConfig>
        <Sink name="HotPath">
          <EventHub Url="https://diageventhub-py-ns.servicebus.windows.net/diageventhub-py" SharedAccessKeyName="SendRule" />
        </Sink>
        <Sink name="applicationInsights">
          <ApplicationInsights />
          <Channels>
            <Channel logLevel="Error" name="errors" />
          </Channels>
        </Sink>
      </SinksConfig>
    </WadCfg>
    <StorageAccount />
  </PublicConfig>
  <PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <StorageAccount name="" key="" endpoint="" />
    <EventHub Url="https://diageventhub-py-ns.servicebus.windows.net/diageventhub-py" SharedAccessKeyName="SendRule" SharedAccessKey="YOUR_KEY_HERE" />
  </PrivateConfig>
  <IsEnabled>true</IsEnabled>
</DiagnosticsConfiguration>
```

La *ServiceConfiguration.Cloud.cscfg* complémentaire pour que cet exemple ressemble à ceci.

```
<?xml version="1.0" encoding="utf-8"?>
<ServiceConfiguration serviceName="MyFixItCloudService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="3" osVersion="*" schemaVersion="2015-04.2.6">
  <Role name="MyFixIt.WorkerRole">
    <Instances count="1" />
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="YOUR_CONNECTION_STRING" />
    </ConfigurationSettings>
  </Role>
</ServiceConfiguration>
```

<!-- Images. -->
[0]: ./media/event-hubs-streaming-azure-diags-data/dashboard.png
