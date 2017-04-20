<properties 
    pageTitle="Comment utiliser Bus des services Azure avec WebJobs SDK" 
    description="Découvrez comment utiliser les files d’attente de Bus des services Azure et rubriques avec WebJobs SDK." 
    services="app-service\web, service-bus" 
    documentationCenter=".net" 
    authors="tdykstra" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="06/01/2016" 
    ms.author="tdykstra"/>

# <a name="how-to-use-azure-service-bus-with-the-webjobs-sdk"></a>Comment utiliser Bus des services Azure avec WebJobs SDK

## <a name="overview"></a>Vue d’ensemble

Ce guide fournit des exemples de code c# qui montrent comment déclencher un processus lorsque je reçois un message Bus des services Azure. Les exemples de code utilisent [WebJobs SDK](websites-dotnet-webjobs-sdk.md) version 1.x.

Part du principe que vous savez [comment créer un projet WebJob dans Visual Studio avec les chaînes de connexion qui pointent vers votre compte de stockage](websites-dotnet-webjobs-sdk-get-started.md).

Extraits de code affichent uniquement les fonctions, pas le code qui crée le `JobHost` objet comme dans cet exemple :

```
public class Program
{
   public static void Main()
   {
      JobHostConfiguration config = new JobHostConfiguration();
      config.UseServiceBus();
      JobHost host = new JobHost(config);
      host.RunAndBlock();
   }
}
```

Un [exemple de code complet Bus des services](https://github.com/Azure/azure-webjobs-sdk-samples/blob/master/BasicSamples/ServiceBus/Program.cs) se trouve dans le référentiel webjobs azure-exemples sdk GitHub.com.

## <a id="prerequisites"></a>Conditions préalables

Pour travailler de manière à Service, vous devez installer le package NuGet [Microsoft.Azure.WebJobs.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus/) outre les autres packages WebJobs SDK. 

Vous devez également définir la chaîne de connexion AzureWebJobsServiceBus outre les chaînes de connexion de stockage.  Vous pouvez le faire dans la `connectionStrings` section du fichier App.config, comme illustré dans l’exemple suivant :

        <connectionStrings>
            <add name="AzureWebJobsDashboard" connectionString="DefaultEndpointsProtocol=https;AccountName=[accountname];AccountKey=[accesskey]"/>
            <add name="AzureWebJobsStorage" connectionString="DefaultEndpointsProtocol=https;AccountName=[accountname];AccountKey=[accesskey]"/>
            <add name="AzureWebJobsServiceBus" connectionString="Endpoint=sb://[yourServiceNamespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[yourKey]"/>
        </connectionStrings>

Pour obtenir un exemple de projet qui inclut le paramètre de chaîne de connexion de Service Bus dans le fichier App.config, consultez [exemple Bus des services](https://github.com/Azure/azure-webjobs-sdk-samples/tree/master/BasicSamples/ServiceBus). 

Les chaînes de connexion peuvent également être définies dans l’environnement d’exécution Azure, qui remplace ensuite les paramètres App.config lorsque la WebJob s’exécute dans Azure ; Pour plus d’informations, voir [Prise en main du Kit de développement WebJobs](websites-dotnet-webjobs-sdk-get-started.md#configure-the-web-app-to-use-your-azure-sql-database-and-storage-account).

## <a id="trigger"></a>Comment faire pour déclencher la lecture d’une fonction lors de la réception d’un message de file d’attente Bus des services

Pour écrire une fonction qui le WebJobs SDK appelle lors de la réception d’un message file d’attente, utilisez la `ServiceBusTrigger` attribut. Constructeur de l’attribut prend un paramètre qui spécifie le nom de la file d’attente d’interrogation.

### <a name="how-servicebustrigger-works"></a>Fonctionnement de ServiceBusTrigger

Le Kit de développement reçoit un message dans `PeekLock` des appels et mode `Complete` sur le message si la fonction est terminée avec succès, ni d’appels `Abandon` si la fonction échoue. Si la fonction s’exécute plue de la `PeekLock` délai d’attente, le verrou est renouvelé automatiquement.

Bus des services gère sa propre file d’attente poison qui ne peut pas être contrôlée ou configurée par le SDK WebJobs. 

### <a name="string-queue-message"></a>Message de file d’attente de chaîne

L’exemple de code suivant lit un message file d’attente qui contient une chaîne et écrit la chaîne dans le tableau de bord WebJobs SDK.

        public static void ProcessQueueMessage([ServiceBusTrigger("inputqueue")] string message, 
            TextWriter logger)
        {
            logger.WriteLine(message);
        }

**Remarque :** Si vous créez les file d’attente messages dans une application qui n’utilise pas le SDK WebJobs, vérifiez que la valeur [BrokeredMessage.ContentType](http://msdn.microsoft.com/library/microsoft.servicebus.messaging.brokeredmessage.contenttype.aspx) « texte/brut ».

### <a name="poco-queue-message"></a>Message file d’attente POCO

Le Kit de développement désérialisera automatiquement un message file d’attente contenant JSON pour un POCO [(simple objet CLR ancien](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object)) type. L’exemple de code suivant lit un message file d’attente contenant un `BlobInformation` objet qui compte un `BlobName` propriété :

        public static void WriteLogPOCO([ServiceBusTrigger("inputqueue")] BlobInformation blobInfo,
            TextWriter logger)
        {
            logger.WriteLine("Queue message refers to blob: " + blobInfo.BlobName);
        }

Pour des exemples de code montrant comment utiliser les propriétés de la POCO pour travailler avec des objets BLOB et des tables dans la même fonction, voir la [version de files d’attente de stockage de cet article](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#pocoblobs).

Si votre code qui crée le message file d’attente n’utilise pas le WebJobs SDK, utilisez code similaire à l’exemple suivant :

        var client = QueueClient.CreateFromConnectionString(ConfigurationManager.ConnectionStrings["AzureWebJobsServiceBus"].ConnectionString, "blobadded");
        BlobInformation blobInformation = new BlobInformation () ;
        var message = new BrokeredMessage(blobInformation);
        client.Send(message);

### <a name="types-servicebustrigger-works-with"></a>Types ServiceBusTrigger fonctionne avec

En outre `string` et types de POCO, vous pouvez utiliser la `ServiceBusTrigger` attribut avec un tableau d’octets ou un `BrokeredMessage` objet.

## <a id="create"></a>Comment créer des messages de file d’attente Bus des services

Pour écrire une fonction qui crée une nouvelle utilisation de message file d’attente les `ServiceBus` attribut et passez le nom de la file d’attente au constructeur d’attribut. 


### <a name="create-a-single-queue-message-in-a-non-async-function"></a>Créer un message file d’attente unique dans une fonction non asynchrone

L’exemple de code suivant utilise un paramètre de sortie pour créer un nouveau message dans la file d’attente nommée « outputqueue » avec le même contenu que le message reçu dans la file d’attente nommée « inputqueue ».

        public static void CreateQueueMessage(
            [ServiceBusTrigger("inputqueue")] string queueMessage,
            [ServiceBus("outputqueue")] out string outputQueueMessage)
        {
            outputQueueMessage = queueMessage;
        }

Le paramètre de sortie pour la création d’un message file d’attente unique peut être un des types suivants :

* `string`
* `byte[]`
* `BrokeredMessage`
* Type POCO sérialisable que vous définissez. Automatiquement sérialisée au format JSON.

Pour les paramètres de type POCO, un message file d’attente est toujours créé lors de la fonction se termine ; Si le paramètre est null, le Kit de développement crée un message file d’attente qui retourne une valeur null lorsque le message est reçu et désérialisé. Pour les autres types, si le paramètre est null aucun message file d’attente n’est créée.

### <a name="create-multiple-queue-messages-or-in-async-functions"></a>Créer plusieurs messages file d’attente ou dans des fonctions asynchrones

Pour créer plusieurs messages, utilisez le `ServiceBus` attribut avec `ICollector<T>` ou `IAsyncCollector<T>`, comme illustré dans l’exemple suivant :

        public static void CreateQueueMessages(
            [ServiceBusTrigger("inputqueue")] string queueMessage,
            [ServiceBus("outputqueue")] ICollector<string> outputQueueMessage,
            TextWriter logger)
        {
            logger.WriteLine("Creating 2 messages in outputqueue");
            outputQueueMessage.Add(queueMessage + "1");
            outputQueueMessage.Add(queueMessage + "2");
        }

Chaque message file d’attente est créée immédiatement lors de la `Add` méthode est appelée.

## <a id="topics"></a>L’utilisation des rubriques Bus des services

Pour écrire une fonction qui le Kit de développement appelle lorsqu’un message est reçu sur un sujet Bus des services, utilisez la `ServiceBusTrigger` attribut avec le constructeur qui prend nom de la rubrique et le nom de l’abonnement, comme illustré dans l’exemple de code suivant :

        public static void WriteLog([ServiceBusTrigger("outputtopic","subscription1")] string message,
            TextWriter logger)
        {
            logger.WriteLine("Topic message: " + message);
        }

Pour créer un message sur un sujet, utilisez la `ServiceBus` attribut avec un nom de la rubrique la même façon que vous l’utilisez avec un nom de file d’attente.

## <a name="features-added-in-release-11"></a>Fonctionnalités ajoutées à la version 1.1

Les fonctionnalités suivantes ont été ajoutées dans la version 1.1 :

* Autoriser la personnalisation complète de traitement par le biais du message `ServiceBusConfiguration.MessagingProvider`.
* `MessagingProvider`prend en charge la personnalisation du Service Bus `MessagingFactory` et `NamespaceManager`.
* A `MessageProcessor` modèle de stratégie vous permet de spécifier un processeur par file d’attente/rubrique.
* Accès concurrentiels au traitement de message est pris en charge par défaut. 
* Facilite la personnalisation de `OnMessageOptions` via `ServiceBusConfiguration.MessageOptions`.
* Autoriser [AccessRights](https://github.com/Azure/azure-webjobs-sdk-samples/blob/master/BasicSamples/ServiceBus/Functions.cs#L71) être indiquée dans `ServiceBusTriggerAttribute` / `ServiceBusAttribute` (pour les scénarios où vous ne disposez pas des droits de gestion). 

## <a id="queues"></a>Rubriques connexes couverts par l’article sur les procédures de stockage files d’attente

Pour plus d’informations sur les scénarios WebJobs SDK non spécifiques aux Bus des services, voir [comment utiliser le stockage Azure file d’attente avec le Kit de développement WebJobs](websites-dotnet-webjobs-sdk-storage-queues-how-to.md). 

Rubriques traitées dans cet article sont les suivants :

* Fonctions asynchrones
* Plusieurs instances
* Arrêt
* Utiliser les attributs WebJobs SDK dans le corps d’une fonction
* Définir les chaînes de connexion SDK dans le code
* Définir les valeurs pour WebJobs SDK paramètres constructeur dans le code
* Déclencher manuellement une fonction
* Écrire des journaux

## <a id="nextsteps"></a>Étapes suivantes

Ce guide a fourni des exemples de code qui montrent comment gérer des scénarios courants d’utilisation à Azure Service. Pour plus d’informations sur l’utilisation d’Azure WebJobs et WebJobs SDK, voir [Azure WebJobs recommandé de ressources](http://go.microsoft.com/fwlink/?linkid=390226).
 
