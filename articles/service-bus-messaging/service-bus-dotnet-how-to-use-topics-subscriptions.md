<properties
    pageTitle="Utilisez les rubriques de Service Bus avec .NET | Microsoft Azure"
    description="Découvrez comment utiliser des rubriques Bus des services et abonnements avec .NET dans Azure. Exemples de code sont écrits pour les applications .NET."
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="get-started-article"
    ms.date="09/16/2016"
    ms.author="sethm"/>

# <a name="how-to-use-service-bus-topics-and-subscriptions"></a>Comment utiliser des abonnements et les rubriques de Bus des services

[AZURE.INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Cet article décrit comment utiliser des abonnements et les rubriques de Bus des services. Les exemples sont écrits en c# et utilisent les API .NET. Les scénarios présentés incluent la création d’abonnements, création de filtres de l’abonnement, envoyant des messages à un sujet, recevoir des messages à partir d’un abonnement et suppression des abonnements et les rubriques et rubriques. Pour plus d’informations sur les abonnements et les rubriques, consultez la section [étapes suivantes](#next-steps) .

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

[AZURE.INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

## <a name="configure-the-application-to-use-service-bus"></a>Configurer l’application à utiliser Bus des services

Lorsque vous créez une application qui utilise le Bus des services, vous devez ajouter une référence à l’ensemble de Service Bus et inclure les espaces de noms correspondants. Le plus simple consiste à télécharger le package [NuGet](https://www.nuget.org) approprié.

## <a name="get-the-service-bus-nuget-package"></a>Obtenir le package NuGet Bus de Service

Le [package Service Bus NuGet](https://www.nuget.org/packages/WindowsAzure.ServiceBus) est le moyen le plus simple pour obtenir l’API Bus de Service et configurez votre application avec toutes les dépendances Bus des services nécessaires. Pour installer le package Service Bus NuGet dans votre projet, procédez comme suit :

1.  Dans l’Explorateur de solutions, cliquez sur **références**, puis cliquez sur **Gérer les Packages NuGet**.
2.  Recherchez « Bus des services » et sélectionnez l’élément **Bus des services Microsoft Azure** . Cliquez sur **installer** pour terminer l’installation, puis fermez la boîte de dialogue suivante :

    ![][7]

Vous êtes maintenant prêt à écrire du code Bus des services.

## <a name="create-a-service-bus-connection-string"></a>Créer une chaîne de connexion Bus des services

Bus des services utilise une chaîne de connexion pour stocker les informations d’identification et les points de terminaison. Vous pouvez placer votre chaîne de connexion dans un fichier de configuration, plutôt que de manière irréversible il :

- Lorsque vous utilisez les services Azure, il est recommandé que vous stockez votre chaîne de connexion en utilisant le système de configuration du service Azure (fichiers .csdef et .cscfg).
- Lorsque vous utilisez les sites Web Azure ou Machines virtuelles Azure, il est recommandé que vous stockez votre chaîne de connexion en utilisant le système de configuration .NET (par exemple, le fichier Web.config).

Dans les deux cas, vous pouvez récupérer votre chaîne de connexion à l’aide de la `CloudConfigurationManager.GetSetting` méthode, comme illustré plus loin dans cet article.

### <a name="configure-your-connection-string"></a>Configurer votre chaîne de connexion

Le mécanisme de configuration de service vous permet de modifier dynamiquement les paramètres de configuration à partir du [portail Azure][] sans redéployez votre application. Par exemple, ajoutez une `Setting` des étiquettes à votre fichier de définition (**.csdef**) service, comme illustré dans l’exemple suivant.

```
<ServiceDefinition name="Azure1">
...
    <WebRole name="MyRole" vmsize="Small">
        <ConfigurationSettings>
            <Setting name="Microsoft.ServiceBus.ConnectionString" />
        </ConfigurationSettings>
    </WebRole>
...
</ServiceDefinition>
```

Puis, vous spécifiez des valeurs dans le fichier de configuration (.cscfg) service.

```
<ServiceConfiguration serviceName="Azure1">
...
    <Role name="MyRole">
        <ConfigurationSettings>
            <Setting name="Microsoft.ServiceBus.ConnectionString"
                     value="Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey" />
        </ConfigurationSettings>
    </Role>
...
</ServiceConfiguration>
```

Utilisez le nom de la clé Signature accès partagé (sa) et les valeurs clés récupérées à partir du portail comme décrit plus haut.

### <a name="configure-your-connection-string-when-using-azure-websites-or-azure-virtual-machines"></a>Configurer votre chaîne de connexion lors de l’utilisation des sites Web Azure ou Machines virtuelles Azure

Lorsque vous utilisez les sites Web ou Machines virtuelles, il est recommandé d’utiliser le système de configuration .NET (par exemple, Web.config). Stocker la chaîne de connexion à l’aide de la `<appSettings>` élément.

```
<configuration>
    <appSettings>
        <add key="Microsoft.ServiceBus.ConnectionString"
             value="Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey" />
    </appSettings>
</configuration>
```

Utilisez le nom de sa et les valeurs de clé que vous avez extraites à partir du [portail Azure][], comme décrit plus haut.

## <a name="create-a-topic"></a>Créer un sujet

Vous pouvez effectuer des opérations de gestion pour les abonnements à l’aide de la classe [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) et rubriques Bus des services. Ce cours fournit des méthodes pour créer, énumérer et supprimer des rubriques.

L’exemple suivant crée un `NamespaceManager` objet à l’aide de la Azure `CloudConfigurationManager` classe avec une chaîne de connexion composé de l’adresse de base d’un espace de noms Bus de Service et les associations de sécurité appropriées les informations d’identification avec les autorisations pour gérer. Cette chaîne de connexion est sous la forme suivante :

```
Endpoint=sb://<yourNamespace>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<yourKey>
```

Utilisez l’exemple suivant, selon les paramètres de configuration dans la section précédente.

```
// Create the topic if it does not exist already.
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

var namespaceManager =
    NamespaceManager.CreateFromConnectionString(connectionString);

if (!namespaceManager.TopicExists("TestTopic"))
{
    namespaceManager.CreateTopic("TestTopic");
}
```

Surcharge de la méthode [CreateTopic](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.createtopic.aspx) qui vous permettent de définir les propriétés de la rubrique ; par exemple, pour définir la valeur par défaut de valeur time to live (TTL) pour être appliquées aux messages envoyés à la rubrique. Ces paramètres sont appliqués à l’aide de la classe [TopicDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicdescription.aspx) . L’exemple suivant montre comment créer une rubrique nommée **TestTopic** avec une taille maximale de 5 Go et un message par défaut TTL d’une minute.

```
// Configure Topic Settings.
TopicDescription td = new TopicDescription("TestTopic");
td.MaxSizeInMegabytes = 5120;
td.DefaultMessageTimeToLive = new TimeSpan(0, 1, 0);

// Create a new Topic with custom settings.
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

var namespaceManager =
    NamespaceManager.CreateFromConnectionString(connectionString);

if (!namespaceManager.TopicExists("TestTopic"))
{
    namespaceManager.CreateTopic(td);
}
```

> [AZURE.NOTE] Vous pouvez utiliser la méthode [TopicExists](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.topicexists.aspx) sur les objets [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) pour vérifier si une rubrique avec un nom spécifié existe déjà dans un espace de noms.

## <a name="create-a-subscription"></a>Créer un abonnement

Vous pouvez également créer des abonnements rubrique à l’aide de la classe [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) . Abonnements sont nommées et peuvent avoir un filtre facultatif qui limite l’ensemble des messages transmis à la file d’attente virtuelle de l’abonnement.

> [AZURE.IMPORTANT] Dans l’ordre des messages d’être reçue par un abonnement, vous devez créer cet abonnement avant d’envoyer des messages à la rubrique. S’il n’y a aucun abonnement à un sujet, la rubrique ignore ces messages.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Créer un abonnement avec le filtre par défaut (MatchAll)

Si aucun filtre n’est spécifié lors de la création d’un nouvel abonnement, le filtre **MatchAll** est le filtre par défaut qui est utilisé. Lorsque vous utilisez le filtre **MatchAll** , tous les messages publiés dans la rubrique sont placées dans la file d’attente virtuelle de l’abonnement. L’exemple suivant crée un abonnement nommé « AllMessages » et utilise le filtre de **MatchAll** par défaut.

```
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

var namespaceManager =
    NamespaceManager.CreateFromConnectionString(connectionString);

if (!namespaceManager.SubscriptionExists("TestTopic", "AllMessages"))
{
    namespaceManager.CreateSubscription("TestTopic", "AllMessages");
}
```

### <a name="create-subscriptions-with-filters"></a>Créer des abonnements avec les filtres

Vous pouvez également définir des filtres qui vous permettent de spécifier lesquelles messages envoyés à un sujet doivent apparaître un abonnement sujet spécifique.

Le type de filtre pris en charge par les abonnements plus souple est la classe [SqlFilter][] , qui met en œuvre un sous-ensemble de SQL92. Filtres SQL fonctionnent sur les propriétés des messages qui sont publiés dans la rubrique. Pour plus d’informations sur les expressions qui peuvent être utilisés avec un filtre SQL, consultez la syntaxe [SqlFilter.SqlExpression][] .

L’exemple suivant crée un abonnement nommé **HighMessages** avec un objet [SqlFilter][] qui sélectionne uniquement les messages qui ont une propriété **format** personnalisée égal à 3.

```
// Create a "HighMessages" filtered subscription.
SqlFilter highMessagesFilter =
   new SqlFilter("MessageNumber > 3");

namespaceManager.CreateSubscription("TestTopic",
   "HighMessages",
   highMessagesFilter);
```

De même, l’exemple suivant crée un abonnement nommé **LowMessages** avec un [SqlFilter][] qui sélectionne uniquement les messages qui ont une propriété **format** inférieur ou égal à 3.

```
// Create a "LowMessages" filtered subscription.
SqlFilter lowMessagesFilter =
   new SqlFilter("MessageNumber <= 3");

namespaceManager.CreateSubscription("TestTopic",
   "LowMessages",
   lowMessagesFilter);
```

Maintenant lorsqu’un message est envoyé à `TestTopic`, il est toujours livré aux récepteurs d’abonné à l’abonnement de rubrique **AllMessages** et sélectivement remis aux récepteurs d’abonné à l’abonnement à une rubrique **HighMessages** et **LowMessages** (selon le contenu du message).

## <a name="send-messages-to-a-topic"></a>Envoyer des messages à un sujet

Pour envoyer un message à un sujet de Bus des services, votre application crée un objet [TopicClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx) à l’aide de la chaîne de connexion.

Le code suivant montre comment créer un objet [TopicClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx) correspondant à la rubrique **TestTopic** créé précédemment à l’aide de la [`CreateFromConnectionString`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.createfromconnectionstring.aspx) appel API.

```
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

TopicClient Client =
    TopicClient.CreateFromConnectionString(connectionString, "TestTopic");

Client.Send(new BrokeredMessage());
```

Les messages envoyés vers des rubriques Bus des services sont des instances de la classe [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx) . Objets [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx) comportent un ensemble de propriétés standard (par exemple, [étiquette](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx) et [TimeToLive](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx)), un dictionnaire est utilisé pour inclure des propriétés personnalisées spécifiques à l’application et un corps de données d’application arbitraire. Une application peut définir le corps du message en passant tout objet sérialisable au constructeur de l’objet [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx) , et le **DataContractSerializer** approprié est alors utilisée pour sérialiser l’objet. Vous pouvez également **System.IO.Stream** peut être fourni.

L’exemple suivant montre comment envoyer des messages de test cinq à l’objet de [TopicClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx) **TestTopic** obtenu dans l’exemple ci-dessus. Notez que la valeur de propriété [format](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.properties.aspx) de chaque message varie selon l’itération de la boucle (détermine les abonnements reçoivent).

```
for (int i=0; i<5; i++)
{
  // Create message, passing a string message for the body.
  BrokeredMessage message = new BrokeredMessage("Test message " + i);

  // Set additional custom app-specific property.
  message.Properties["MessageNumber"] = i;

  // Send message to the topic.
  Client.Send(message);
}
```

Rubriques Bus des services prend en charge une taille maximale des messages de la [couche Standard](service-bus-premium-messaging.md) 256 Ko et 1 Mo dans la [couche Premium](service-bus-premium-messaging.md). L’en-tête, qui inclut les propriétés d’application standard et personnalisées, peut avoir une taille maximale de 64 Ko. Il n’existe aucune limite du nombre de messages conservés dans une rubrique mais il existe une lettrine sur la taille totale des messages émanant d’une rubrique. La taille de cette rubrique est définie au moment de la création, avec un maximum de 5 Go. Si la partition est activée, la limite supérieure est plus élevée. Pour plus d’informations, consultez [partitionnées messagerie entités](service-bus-partitioning.md).

## <a name="how-to-receive-messages-from-a-subscription"></a>Comment faire pour recevoir des messages à partir d’un abonnement

Les procédures recommandées pour recevoir des messages à partir d’un abonnement consiste à utiliser un objet [SubscriptionClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.aspx) . Objets [SubscriptionClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.aspx) peuvent travailler dans deux modes : [ *ReceiveAndDelete* et *PeekLock*](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx).

Lorsque l’utilisation du mode **ReceiveAndDelete** , recevez est une opération de capture d’unique ; Autrement dit, quand Bus des services reçoit une demande de lecture d’un message dans un abonnement, il marque le message comme étant consommées et retourne à l’application. Le mode **ReceiveAndDelete** très simple et convient le mieux pour les scénarios dans lesquels une application tolèrent ne pas traitement d’un message en cas de panne. Pour mieux comprendre cela, envisagez un scénario dans lequel le consommateur envoie la demande de réception, puis se bloque avant de le traiter. Étant donné que Service Bus a marqué le message comme consommées, lors de l’application redémarre et commence à lire des messages à nouveau, il sera ai manqués le message a été consommé avant l’incident.

En mode **PeekLock** (qui est le mode par défaut), le processus de réception devient une opération de deux étages, qui permet aux applications de prise en charge qui ne peut pas tolérer messages manquants. Lorsque Service Bus reçoit une demande, il recherche le message suivant à consommer, verrous pour empêcher les autres consommateurs reçoit et renvoie à l’application. Une fois l’application fini de traiter le message (ou qu’il stocke fiable d’un traitement ultérieur), il termine la deuxième étape du processus de réception en appelant [achevé](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) sur le message reçu. Lorsque Service Bus voit la **achevé** appeler, il marque le message comme étant consommées et supprime de l’abonnement.

L’exemple suivant montre comment les messages pouvant être reçus et traitement à l’aide du mode **PeekLock** par défaut. Pour spécifier une valeur différente [ReceiveMode](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx) , vous pouvez utiliser une autre surcharge pour [CreateFromConnectionString](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.createfromconnectionstring.aspx). Cet exemple utilise le rappel [OnMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.onmessage.aspx) pour traiter les messages entrants dans l’abonnement **HighMessages** .

```
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

SubscriptionClient Client =
    SubscriptionClient.CreateFromConnectionString
            (connectionString, "TestTopic", "HighMessages");

// Configure the callback options.
OnMessageOptions options = new OnMessageOptions();
options.AutoComplete = false;
options.AutoRenewTimeout = TimeSpan.FromMinutes(1);

Client.OnMessage((message) =>
{
    try
    {
        // Process message from subscription.
        Console.WriteLine("\n**High Messages**");
        Console.WriteLine("Body: " + message.GetBody<string>());
        Console.WriteLine("MessageID: " + message.MessageId);
        Console.WriteLine("Message Number: " +
            message.Properties["MessageNumber"]);

        // Remove message from subscription.
        message.Complete();
    }
    catch (Exception)
    {
        // Indicates a problem, unlock message in subscription.
        message.Abandon();
    }
}, options);
```

Cet exemple configure le rappel [OnMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.onmessage.aspx) à l’aide d’un objet [OnMessageOptions](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.aspx) . [La saisie semi-automatique](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.autocomplete.aspx) est définie sur **false** pour activer le contrôle manuel de quand il doit appeler [achevé](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) sur le message reçu. [AutoRenewTimeout](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.autorenewtimeout.aspx) est définie sur 1 minute, ce qui provoque le client Patientez jusqu'à une minute avant de mettre fin à la fonctionnalité de renouvellement automatique et le client effectue un nouvel appel pour vérifier les messages. Cette valeur de propriété réduit le nombre de fois où que le client effectue des appels facturables ne récupérant pas de messages.

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Comment gérer le blocage d’applications et messages illisibles

Bus des services fournit les fonctionnalités pour vous aider à récupérer normalement les erreurs dans votre application ou difficultés traitement d’un message. Si une application de réception est impossible de traiter le message pour une raison quelconque, il peut appeler la méthode [Abandon](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.abandon.aspx) sur le message reçu (au lieu de la méthode [achevé](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) ). Dans ce cas Bus des services déverrouiller le message au sein de l’abonnement et le rendre disponible pour être à nouveau, reçus par l’application prend beaucoup de même ou par une autre application prend beaucoup de.

Il est également un délai d’expiration associée à un message verrouillé au sein de l’abonnement, et si l’application ne parvient pas à traiter le message avant du verrou délai d’expiration (par exemple, si l’application se bloque), puis Service Bus déverrouiller automatiquement le message et rend disponible pour être reçus à nouveau.

Dans le cas où l’application se bloque après traitement du message, mais avant que la requête [achevé](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) est émise, le message sera remis à nouveau à l’application lorsqu’il redémarre. Cette option est souvent appelée *traitement au moins une fois*; Autrement dit, chaque message est traité au moins une fois, mais dans certains cas le même message peut être redistribué. Si le scénario ne peut pas tolérer traitement en double, les développeurs d’applications doivent ajouter une logique supplémentaire à son application pour gérer la remise des messages en double. Ceci est souvent obtenue à l’aide de la propriété [MessageId](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx) du message, ce qui reste constante pour tous les tentatives de remise.

## <a name="delete-topics-and-subscriptions"></a>Supprimer des rubriques et des abonnements

L’exemple suivant montre comment supprimer la rubrique **TestTopic** à partir de l’espace de noms de service **HowToSample** .

```
// Delete Topic.
namespaceManager.DeleteTopic("TestTopic");
```

Suppression d’un sujet entraîne celle de tous les abonnements qui sont enregistrés avec la rubrique. Abonnements peuvent également être supprimés séparément. Le code suivant montre comment supprimer un abonnement nommé **HighMessages** à partir de la rubrique **TestTopic** .

```
namespaceManager.DeleteSubscription("TestTopic", "HighMessages");
```

## <a name="next-steps"></a>Étapes suivantes

À présent que vous avez appris les notions de base des rubriques Bus des services et des abonnements, suivez ces liens pour en savoir plus.

-   [Files d’attente, de sujets et abonnements][].
-   [Exemple de filtres rubrique][]
-   Référence des API pour [SqlFilter][].
-   Créer une application de travail qui envoie et reçoit des messages vers et depuis une file d’attente Bus de Service : [Service Bus demandée messagerie didacticiel .NET][].
-   Exemples de Bus de service : télécharger à partir [d’Azure échantillons][] ou reportez-vous à la [vue d’ensemble](service-bus-samples.md).

  [Portail Azure]: https://portal.azure.com

  [7]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/getting-started-multi-tier-13.png

  [Abonnements, rubriques et files d’attente]: service-bus-queues-topics-subscriptions.md
  [Exemple de filtres rubrique]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/TopicFilters
  [SqlFilter]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.aspx
  [SqlFilter.SqlExpression]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
  [Bus des services demandé de messagerie .NET didacticiel]: service-bus-brokered-tutorial-dotnet.md
  [Exemples d’Azure]: https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=2
