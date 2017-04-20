<properties
   pageTitle="Optimisation de votre code Azure dans Visual Studio | Microsoft Azure"
   description="Obtenir des informations à propos du code comment Azure outils d’optimisation dans Visual Studio rendre le code plus robuste et réaliser."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="optimizing-your-azure-code"></a>Optimisation de votre Code Azure

Lorsque vous effectuez la programmation applications qui utilisent Microsoft Azure, il existe certaines pratiques de codage que vous devez suivre pour aider à éviter des problèmes avec extensibilité élevées application, comportement et les performances dans un environnement cloud. Microsoft fournit un outil d’analyse de Code Azure qui reconnaît et identifie plusieurs de ces problèmes fréquemment rencontrés et vous permet de les résoudre. Vous pouvez télécharger l’outil dans Visual Studio via NuGet.

## <a name="azure-code-analysis-rules"></a>Règles d’analyse du Code Azure

L’outil d’analyse de Code Azure utilise les règles suivantes pour marquer automatiquement votre code Azure lorsqu’il détecte des problèmes connus ayant un impact sur les performances. Détecté problèmes s’affichent en tant qu’un avertissements ou les erreurs du compilateur. Correctifs de code ou des suggestions pour résoudre l’erreur ou avertissement sont souvent fournies par le biais d’une icône ampoule.

## <a name="avoid-using-default-in-process-session-state-mode"></a>Évitez d’utiliser le mode d’état de session (in-process) par défaut

### <a name="id"></a>ID

AP0000

### <a name="description"></a>Description

Si vous utilisez le mode d’état de session (in-process) par défaut pour les applications cloud, vous pouvez perdre l’état de session.

Veuillez partager vos idées et vos commentaires à [l’analyse du Code Azure commentaires](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Raison n °

Par défaut, le mode d’état de session spécifié dans le fichier web.config est en cours. En outre, si aucune entrée spécifiée dans le fichier de configuration, le mode d’état de Session par défaut dans le processus. Le mode in-process stocke l’état de session en mémoire sur le serveur web. Lorsqu’une instance est redémarrée ou une nouvelle instance est utilisée pour l’équilibrage de charge ou en charge le basculement, l’état de session stockée en mémoire sur le serveur web n’est pas enregistré. Cette situation empêche l’application ne soient scalable sur le nuage.

État de session ASP.NET prend en charge plusieurs options de stockage différentes pour les données de session : InProc, StateServer, SQL Server, personnalisé et désactivé. Il est recommandé d’utiliser le mode personnalisé pour héberger des données dans un magasin d’état de Session externes, telles que [l’état de Session Azure fournisseur pour Redis](http://go.microsoft.com/fwlink/?LinkId=401521).

### <a name="solution"></a>Solution

Une solution recommandée consiste à stocker l’état de session sur un service de cache gérées. Découvrez comment utiliser [l’état de Session Azure fournisseur pour Redis](http://go.microsoft.com/fwlink/?LinkId=401521) pour stocker votre état de session. Vous pouvez également stocker l’état de session dans d’autres emplacements pour vérifier que votre application est scalable sur le nuage. Pour en savoir plus sur les autres solutions lisent [Modes d’état de Session](https://msdn.microsoft.com/library/ms178586).

## <a name="run-method-should-not-be-async"></a>Exécuter method ne doit pas être asynchrone

### <a name="id"></a>ID

AP1000

### <a name="description"></a>Description

Créer des méthodes asynchrones (par exemple, [attendre](https://msdn.microsoft.com/library/hh156528.aspx)) en dehors de la méthode [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) , puis appelez les méthodes asynchrone à partir de [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx). Déclaration de la méthode [[Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) asynchrones provoque le rôle de collaborateur entrer une boucle de redémarrage.

Veuillez partager vos idées et vos commentaires à [l’analyse du Code Azure commentaires](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Raison n °

Appel de méthodes asynchrone à l’intérieur de la méthode [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) entraîne l’exécution du service cloud recycler le rôle de travail. Lorsqu’un rôle de travail démarre, tous les exécution du programme a lieu à l’intérieur de la méthode [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) . Quitter la méthode [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) provoque le rôle de collaborateur redémarrer. Lorsque le runtime de rôle de collaborateur accède à la méthode asynchrone, elle distribue toutes les opérations après la méthode asynchrone, puis renvoie. Dans ce cas le rôle de collaborateur quitter à partir de la méthode [[[[Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) et redémarrer. Dans la prochaine itération d’exécution, le rôle de collaborateur accède à nouveau la méthode asynchrone et redémarre, à l’origine du rôle de collaborateur pour Corbeille ainsi que.

### <a name="solution"></a>Solution

Placez toutes les opérations asynchrones en dehors de la méthode [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) . Ensuite, appelez la méthode asynchrone refactorisé à partir d’à l’intérieur de la méthode [[Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) , tels que RunAsync () .wait. L’outil d’analyse de Code Azure peut vous aider à résoudre ce problème.

L’extrait de code suivant montre le correctif de code pour ce problème :

```
public override void Run()
{
    RunAsync().Wait();
}

public async Task RunAsync()
{
    //Asynchronous operations code logic
    // This is a sample worker implementation. Replace with your logic.

    Trace.TraceInformation("WorkerRole1 entry point called");

    HttpClient client = new HttpClient();

    Task<string> urlString = client.GetStringAsync("http://msdn.microsoft.com");

    while (true)
    {
        Thread.Sleep(10000);
        Trace.TraceInformation("Working");

        string stream = await urlString;
    }

}
```

## <a name="use-service-bus-shared-access-signature-authentication"></a>Utilisez l’authentification Service Bus partagés accès Signature

### <a name="id"></a>ID

AP2000

### <a name="description"></a>Description

Utiliser partagés accès Signature (sa) pour l’authentification. Service de contrôle d’accès (ACS) est en cours déconseillée pour l’authentification bus du service.

Veuillez partager vos idées et vos commentaires à [l’analyse du Code Azure commentaires](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Raison n °

Pour renforcer la sécurité, Azure Active Directory consiste à remplacer l’authentification ACS avec l’authentification associations de sécurité. Pour plus d’informations sur le plan de transition, voir [Azure Active Directory y a-t-il de ACS](http://blogs.technet.com/b/ad/archive/2013/06/22/azure-active-directory-is-the-future-of-acs.aspx) .

### <a name="solution"></a>Solution

Utilisez l’authentification associations de sécurité dans vos applications. L’exemple suivant montre comment utiliser un jeton de sa existant pour accéder à un espace de noms de service bus ou entité.

```
MessagingFactory listenMF = MessagingFactory.Create(endpoints, new StaticSASTokenProvider(subscriptionToken));
SubscriptionClient sc = listenMF.CreateSubscriptionClient(topicPath, subscriptionName);
BrokeredMessage receivedMessage = sc.Receive();
```

Consultez les rubriques suivantes pour plus d’informations.

- Pour une vue d’ensemble, voir [Partagés Signature authentification de l’accès à Service](https://msdn.microsoft.com/library/dn170477.aspx)

- [L’utilisation d’authentification de Signature accès partagé à Service](https://msdn.microsoft.com/library/dn205161.aspx)

- Pour un exemple de projet, voir [l’authentification à l’aide de partagés accès Signature (sa) avec les services Bus récurrents](http://code.msdn.microsoft.com/windowsazure/Using-Shared-Access-e605b37c)

## <a name="consider-using-onmessage-method-to-avoid-receive-loop"></a>Envisagez d’utiliser la méthode OnMessage afin d’éviter « recevoir boucle »

### <a name="id"></a>ID

AP2002

### <a name="description"></a>Description

Pour éviter d’être placés dans une « boucle de réception », l’appel de la méthode **OnMessage** est une meilleure solution pour recevoir des messages que l’appel de la méthode de **réception** . Toutefois, si vous devez utiliser la méthode de **réception** et que vous spécifiez un serveur de ceux définis par défaut délai d’attente, vérifiez que le délai d’attente serveur est plus d’une minute.

Veuillez partager vos idées et vos commentaires à [l’analyse du Code Azure commentaires](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Raison n °

Lorsque vous appelez **OnMessage**, le client démarre une pompe de messages interne qui consulte en permanence la file d’attente ou d’abonnement. Cette pompe de messages contient une boucle qui émet un appel à recevoir des messages. Si l’appel arrive à expiration, il émet un nouvel appel. Le délai d’expiration est déterminé par la valeur de la propriété [OperationTimeout](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout.aspx) de la [MessagingFactory](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.messagingfactory.aspx)qui est utilisé.

L’avantage de l’utilisation **OnMessage** par rapport à la **réception** est que les utilisateurs ne rencontrent vérifier les messages, gérer les exceptions, traiter plusieurs messages en parallèle et compléter les messages manuellement.

Si vous appelez **réception** sans utiliser de sa valeur par défaut, assurez-vous que la valeur *ServerWaitTime* est plus d’une minute. Paramétrer le *ServerWaitTime* plus d’une minute empêche le serveur de délai d’expiration avant que le message a été reçu entièrement.

### <a name="solution"></a>Solution

Voir les exemples de code suivants pour les utilisations recommandées. Pour plus d’informations, voir [QueueClient.OnMessage méthode (Microsoft.ServiceBus.Messaging)](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.queueclient.onmessage.aspx)et [QueueClient.Receive méthode (Microsoft.ServiceBus.Messaging)](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.queueclient.receive.aspx).

Pour améliorer les performances de l’infrastructure de messagerie Azure, voir le modèle de conception [Asynchrone Introduction à la messagerie](https://msdn.microsoft.com/library/dn589781.aspx).

Voici un exemple d’utilisation **OnMessage** pour recevoir des messages.

```
void ReceiveMessages()
{
    // Initialize message pump options.
    OnMessageOptions options = new OnMessageOptions();
    options.AutoComplete = true; // Indicates if the message-pump should call complete on messages after the callback has completed processing.
    options.MaxConcurrentCalls = 1; // Indicates the maximum number of concurrent calls to the callback the pump should initiate.
    options.ExceptionReceived += LogErrors; // Enables you to get notified of any errors encountered by the message pump.

    // Start receiving messages.
    QueueClient client = QueueClient.Create("myQueue");
    client.OnMessage((receivedMessage) => // Initiates the message pump and callback is invoked for each message that is recieved, calling close on the client will stop the pump.
    {
        // Process the message.
    }, options);
    Console.WriteLine("Press any key to exit.");
    Console.ReadKey();
```

Voici un exemple d’utilisation de **réception** avec le délai d’attente serveur par défaut.

```
string connectionString =  
CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

QueueClient Client =  
    QueueClient.CreateFromConnectionString(connectionString, "TestQueue");

while (true)  
{   
   BrokeredMessage message = Client.Receive();
   if (message != null)
   {
      try  
      {
         Console.WriteLine("Body: " + message.GetBody<string>());
         Console.WriteLine("MessageID: " + message.MessageId);
         Console.WriteLine("Test Property: " +  
            message.Properties["TestProperty"]);

         // Remove message from queue
         message.Complete();
      }

      catch (Exception)
      {
         // Indicate a problem, unlock message in queue
         message.Abandon();
      }
   }
```

Voici un exemple d’utilisation de **réception** avec un délai d’attente server ceux définis par défaut.

```
while (true)  
{   
   BrokeredMessage message = Client.Receive(new TimeSpan(0,1,0));

   if (message != null)
   {
      try  
      {
         Console.WriteLine("Body: " + message.GetBody<string>());
         Console.WriteLine("MessageID: " + message.MessageId);
         Console.WriteLine("Test Property: " +  
            message.Properties["TestProperty"]);

         // Remove message from queue
         message.Complete();
      }

      catch (Exception)
      {
         // Indicate a problem, unlock message in queue
         message.Abandon();
      }
   }
}
```
## <a name="consider-using-asynchronous-service-bus-methods"></a>Envisagez d’utiliser les méthodes asynchrones Bus des services

### <a name="id"></a>ID

AP2003

### <a name="description"></a>Description

Utilisez asynchrone Bus des services pour améliorer les performances avec la messagerie traité.

Veuillez partager vos idées et vos commentaires à [l’analyse du Code Azure commentaires](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Raison n °

Méthodes asynchrones permettent de concurrence de programme d’application, car chaque appel en cours d’exécution ne bloquer le thread principal. Lorsque vous utilisez le Service Bus méthodes de messagerie, l’exécution d’une opération (envoyer, recevoir, supprimer, etc.) prend du temps. Ce temps inclut le traitement de l’opération par le service Bus des services en plus de la latence de la demande et la réponse. Pour augmenter le nombre d’opérations par heure, opérations doivent exécuter simultanément. Pour plus d’informations, veuillez consulter [Meilleures pratiques pour la Performance améliorations apportées à l’aide de Service Bus demandé de messagerie](https://msdn.microsoft.com/library/azure/hh528527.aspx).

### <a name="solution"></a>Solution

Pour plus d’informations sur l’utilisation de la méthode asynchrone recommandée, consultez [QueueClient cours (Microsoft.ServiceBus.Messaging)](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.queueclient.aspx) .

Pour améliorer les performances de l’infrastructure de messagerie Azure, voir le modèle de conception [Asynchrone Introduction à la messagerie](https://msdn.microsoft.com/library/dn589781.aspx).

## <a name="consider-partitioning-service-bus-queues-and-topics"></a>Prendre en considération partition files d’attente de Bus des services et rubriques

### <a name="id"></a>ID

AP2004

### <a name="description"></a>Description

Files d’attente de partition Bus des services et des rubriques pour de meilleures performances avec Bus des services de messagerie.

Veuillez partager vos idées et vos commentaires à [l’analyse du Code Azure commentaires](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Raison n °

Partition files d’attente de Bus des services et rubriques performances débit disponibilité accrue et service, car la vitesse globale d’un partitionnée file d’attente ou une rubrique n’est plus limitée par les performances d’un intermédiaire seul message ou de la banque d’informations. En outre, une panne temporaire d’une banque de messagerie ne fait pas appel un partitionnée file d’attente ou une rubrique indisponible. Pour plus d’informations, voir [Entités de messagerie partition](https://msdn.microsoft.com/library/azure/dn520246.aspx).

### <a name="solution"></a>Solution

L’extrait de code suivant montre comment partition entités de messagerie.

```
// Create partitioned topic.
NamespaceManager ns = NamespaceManager.CreateFromConnectionString(myConnectionString);
TopicDescription td = new TopicDescription(TopicName);
td.EnablePartitioning = true;
ns.CreateTopic(td);
```

Pour plus d’informations, consultez les rubriques et les files d’attente de Bus de Service de [partition | Microsoft Azure Blog](https://azure.microsoft.com/blog/2013/10/29/partitioned-service-bus-queues-and-topics/) et consultez la [File d’attente de Microsoft Azure Service Bus partition](https://code.msdn.microsoft.com/windowsazure/Service-Bus-Partitioned-7dfd3f1f) exemple.

## <a name="do-not-set-sharedaccessstarttime"></a>Ne définissez pas SharedAccessStartTime

### <a name="id"></a>ID

AP3001

### <a name="description"></a>Description

Évitez d’utiliser SharedAccessStartTimeset à l’heure actuelle pour démarrer immédiatement la stratégie d’accès partagé. Vous devez uniquement définir cette propriété si vous voulez commencer la stratégie d’accès partagé ultérieurement.

Veuillez partager vos idées et vos commentaires à [l’analyse du Code Azure commentaires](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Raison n °

Synchronisation de l’horloge entraîne une différence de temps faible entre les centres de données. Par exemple, penser logiquement définissant l’heure de début une stratégie de stockage associations de sécurité en tant que l’heure actuelle à l’aide de DateTime.Now ou une méthode similaire entraînera la stratégie associations de sécurité entre en vigueur immédiatement. Toutefois, les différences légères heure des centres de données peuvent entraîner des problèmes avec les marques dans la mesure où parfois centre de données peuvent être légèrement postérieure à l’heure de début, tandis que d’autres une longueur d’avance. Par conséquent, la stratégie de sa peut expirer rapidement (ou même immédiatement) si la durée de vie de stratégie est trop faible.

Pour obtenir des instructions sur l’utilisation de la Signature de Access partagés sur stockage Azure, voir [Présentation de tableau associations de sécurité (partagés accès Signature), sa file d’attente et mettre à jour pour les associations de sécurité Blob - Blog de l’équipe de stockage de Microsoft Azure - Site accueil - MSDN Blogs](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx).

### <a name="solution"></a>Solution

Supprimez l’instruction qui définit l’heure de début de la stratégie d’accès partagé. L’outil d’analyse de Code Azure fournit un correctif pour ce problème. Pour plus d’informations sur la gestion de la sécurité, voir le modèle de conception [Avec voiturier clé motif](https://msdn.microsoft.com/library/dn568102.aspx).

L’extrait de code suivant montre le correctif de code pour ce problème.

```
// The shared access policy provides  
// read/write access to the container for 10 hours.
blobPermissions.SharedAccessPolicies.Add("mypolicy", new SharedAccessBlobPolicy()
{
   // To ensure SAS is valid immediately, don’t set start time.
   // This way, you can avoid failures caused by small clock differences.
   SharedAccessExpiryTime = DateTime.UtcNow.AddHours(10),
   Permissions = SharedAccessBlobPermissions.Write |
      SharedAccessBlobPermissions.Read
});
```

## <a name="shared-access-policy-expiry-time-must-be-more-than-five-minutes"></a>Partage de stratégie d’accès délai d’expiration doit être plus de cinq minutes

### <a name="id"></a>ID

AP3002

### <a name="description"></a>Description

Il peut y avoir autant que différence par rapport à une minute cinq dans horloge entre les centres de données à différents emplacements en raison d’une condition appelée « temporelle. » Pour éviter que les associations de sécurité jeton de stratégie d’expiration défini plus tôt que prévu, le délai d’expiration soit plus de cinq minutes.

Veuillez partager vos idées et vos commentaires à [l’analyse du Code Azure commentaires](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Raison n °

Synchronisent des centres de données à différents emplacements dans le monde entier par un signal d’horloge. Étant donné que le temps requis pour signal d’horloge à différents emplacements, il est possible un écart de temps entre les centres de données à différents emplacements géographiques bien que tout soit censé synchronisée. Différence de temps peut affecter l’accès partagé Démarrer heure et date d’expiration intervalle la stratégie. Par conséquent, pour vous assurer que la stratégie d’accès partagés entre en vigueur immédiatement, ne spécifiez pas l’heure de début. En outre, vérifiez que le délai d’expiration est plus de 5 minutes pour empêcher le délai d’expiration au plus tôt.

Pour plus d’informations sur l’utilisation de la Signature de Access partagés sur stockage Azure, voir [Présentation de tableau associations de sécurité (partagés accès Signature), sa file d’attente et mettre à jour pour les associations de sécurité Blob - Blog de l’équipe de stockage de Microsoft Azure - Site accueil - MSDN Blogs](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx).

### <a name="solution"></a>Solution

Pour plus d’informations sur la gestion de la sécurité, consultez le modèle de conception [Avec voiturier clé motif](https://msdn.microsoft.com/library/dn568102.aspx).

Voici un exemple de ne pas spécifier une heure de début de stratégie accès partagé.

```
// The shared access policy provides  
// read/write access to the container for 10 hours.
blobPermissions.SharedAccessPolicies.Add("mypolicy", new SharedAccessBlobPolicy()
{
   // To ensure SAS is valid immediately, don’t set start time.
   // This way, you can avoid failures caused by small clock differences.
   SharedAccessExpiryTime = DateTime.UtcNow.AddHours(10),
   Permissions = SharedAccessBlobPermissions.Write |
      SharedAccessBlobPermissions.Read
});
```

Voici un exemple de spécifier une heure de début de stratégie accès partagé avec une durée d’expiration de stratégie plue de cinq minutes.

```
// The shared access policy provides  
// read/write access to the container for 10 hours.
blobPermissions.SharedAccessPolicies.Add("mypolicy", new SharedAccessBlobPolicy()
{
   // To ensure SAS is valid immediately, don’t set start time.
   // This way, you can avoid failures caused by small clock differences.
  SharedAccessStartTime = new DateTime(2014,1,20),   
 SharedAccessExpiryTime = new DateTime(2014, 1, 21),
   Permissions = SharedAccessBlobPermissions.Write |
      SharedAccessBlobPermissions.Read
});
```

Pour plus d’informations, voir [créer et utiliser une Signature accès partagé](https://msdn.microsoft.com/library/azure/jj721951.aspx).

## <a name="use-cloudconfigurationmanager"></a>Utiliser CloudConfigurationManager

### <a name="id"></a>ID

AP4000

### <a name="description"></a>Description

À l’aide de la classe [ConfigurationManager](https://msdn.microsoft.com/library/system.configuration.configurationmanager(v=vs.110).aspx) pour les projets comme site Web Azure et les services mobiles Azure ne présentent des problèmes d’exécution. Toutefois, pour obtenir les meilleurs résultats, il est judicieux de sélectionner utiliser Cloud[ConfigurationManager](https://msdn.microsoft.com/library/system.configuration.configurationmanager(v=vs.110).aspx) comme une pratique unifiée de gestion des configurations pour toutes les applications Azure Cloud.

Veuillez partager vos idées et vos commentaires à [l’analyse du Code Azure commentaires](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Raison n °

CloudConfigurationManager lit le fichier de configuration correspondant à l’environnement d’application.

[CloudConfigurationManager](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager.aspx)

### <a name="solution"></a>Solution

Refactorisez votre code pour utiliser la [Classe CloudConfigurationManager](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager.aspx). Un correctif de code pour ce problème est fourni par l’outil d’analyse de Code Azure.

L’extrait de code suivant montre le correctif de code pour ce problème. Remplacer

`var settings = ConfigurationManager.AppSettings["mySettings"];`

avec

`var settings = CloudConfigurationManager.GetSetting("mySettings");`

Voici un exemple de la façon de stocker le paramètre de configuration dans un fichier App.config ou Web.config. Ajoutez les paramètres dans la section appSettings du fichier de configuration. Voici le fichier Web.config de l’exemple ci-dessus.

```
<appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="mySettings" value="[put_your_setting_here]"/>
  </appSettings>  
```

## <a name="avoid-using-hard-coded-connection-strings"></a>Évitez d’utiliser des chaînes de connexion codé en dur

### <a name="id"></a>ID

AP4001

### <a name="description"></a>Description

Si vous utilisez des chaînes de connexion codé en dur et vous devez mettre à jour les plus tard, vous devrez apporter des modifications à votre code source et la recompilation de l’application. Toutefois, si vous stockez vos chaînes de connexion dans un fichier de configuration, vous pouvez modifier les plus tard en mettant simplement à jour le fichier de configuration.

Veuillez partager vos idées et vos commentaires à [l’analyse du Code Azure commentaires](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Raison n °

Irréversible chaînes de connexion est une mauvaise pratique, car il présente des problèmes lorsque les chaînes de connexion doivent être modifiées rapidement. En outre, si le projet doit être archivé au contrôle de source, chaînes de connexion codé en dur sécurité différents dans la mesure où les chaînes peuvent être affichées dans le code source.

### <a name="solution"></a>Solution

Stocker des chaînes de connexion dans les environnements Azure ou les fichiers de configuration.

- Pour les applications autonomes, utilisez app.config pour stocker des paramètres de chaîne de connexion.

- Pour les applications web IIS hébergeant, utilisez web.config pour stocker des chaînes de connexion.

- Pour les applications ASP.NET vNext, utilisez configuration.json pour stocker les chaînes de connexion.

Pour plus d’informations sur l’utilisation de fichiers de configurations tels que web.config ou app.config, voir [Instructions de Configuration Web ASP.NET](https://msdn.microsoft.com/library/vstudio/ff400235(v=vs.100).aspx). Pour plus d’informations sur le travail de variables environnement comment Azure, voir [Azure des Sites Web : comment chaînes de l’Application et utiliser des chaînes de connexion](https://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/). Pour plus d’informations sur le stockage de la chaîne de connexion dans le contrôle de code source, voir [pour éviter d’enregistrer des informations sensibles telles que les chaînes de connexion dans les fichiers qui sont stockées dans un référentiel de code source](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control).

## <a name="use-diagnostics-configuration-file"></a>Utiliser le fichier de configuration des diagnostics

### <a name="id"></a>ID

AP5000

### <a name="description"></a>Description

Au lieu de configurer les paramètres des diagnostics dans votre code par exemple à l’aide de la Microsoft.WindowsAzure.Diagnostics API de programmation, vous devez configurer les paramètres des diagnostics dans le fichier diagnostics.wadcfg. (Ou diagnostics.wadcfgx si vous utilisez Azure SDK 2.5). En procédant ainsi, vous pouvez modifier les paramètres des diagnostics sans avoir à recompilation votre code.

Veuillez partager vos idées et vos commentaires à [l’analyse du Code Azure commentaires](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Raison n °

Avant d’Azure SDK 2.5 (qui utilise les diagnostics de Windows Azure 1.3), Azure Diagnostics (WAD) peut être configuré à l’aide de plusieurs méthodes : ajout au stockage blob configuration à l’aide de code impératif, la configuration déclarative ou la configuration par défaut. Toutefois, la meilleure façon de configurer des diagnostics consiste à utiliser un fichier de configuration XML (diagnostics.wadcfg ou diagnositcs.wadcfgx pour SDK 2.5 et versions ultérieures) dans le projet d’application. Dans cette approche, le fichier diagnostics.wadcfg complètement définit la configuration et peut être mis à jour et redéployé au se. Mélange de l’utilisation du fichier de configuration diagnostics.wadcfg avec les méthodes de définition de configurations de programmation en utilisant les classes [DiagnosticMonitor](https://msdn.microsoft.com/library/microsoft.windowsazure.diagnostics.diagnosticmonitor.aspx)ou [RoleInstanceDiagnosticManager](https://msdn.microsoft.com/library/microsoft.windowsazure.diagnostics.management.roleinstancediagnosticmanager.aspx)peut entraîner une confusion. Pour plus d’informations, consultez [initialisation ou modifier la Configuration Diagnostics Azure](https://msdn.microsoft.com/library/azure/hh411537.aspx) .

À partir de 1.3 WAD (inclus avec Azure SDK 2.5), il n’est plus possible d’utiliser le code pour configurer les diagnostics. Par conséquent, vous pouvez fournir uniquement la configuration lors de l’application ou mise à jour de l’extension diagnostics.

### <a name="solution"></a>Solution

Utiliser le Concepteur de configuration diagnostics pour atteindre les paramètres de diagnostic le fichier de configuration diagnostics (diagnositcs.wadcfg ou diagnositcs.wadcfgx pour SDK 2.5 et versions ultérieures). Il est également recommandé que vous installez [Azure SDK 2.5](http://go.microsoft.com/fwlink/?LinkId=513188) et utilisez la fonctionnalité diagnostics plus récente.

1. Dans le menu contextuel pour le rôle que vous souhaitez configurer, sélectionnez Propriétés, puis l’onglet Configuration.

1. Dans la section **Diagnostics** , vérifiez que la case à cocher **Activer les Diagnostics** est sélectionnée.

1. Cliquez sur le bouton **configurer** .

  ![Accès à l’option Activer les Diagnostics](./media/vs-azure-tools-optimizing-azure-code-in-visual-studio/IC796660.png)

  Pour plus d’informations, voir [Configuration des Diagnostics pour les Services en nuage Azure et Machines virtuelles](vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md) .


## <a name="avoid-declaring-dbcontext-objects-as-static"></a>Éviter de déclarer des objets DbContext comme statique

### <a name="id"></a>ID

AP6000

### <a name="description"></a>Description

Pour enregistrer la mémoire, évitez de déclarer des objets DBContext comme statique.

Veuillez partager vos idées et vos commentaires à [l’analyse du Code Azure commentaires](http://go.microsoft.com/fwlink/?LinkId=403771).

### <a name="reason"></a>Raison n °

Objets DBContext stocker les résultats de requête de chaque appel. Objets DBContext statiques ne sont pas supprimées jusqu'à ce que le domaine d’application est décharger. Par conséquent, un objet DBContext statique peut consommer de grandes quantités de mémoire.

### <a name="solution"></a>Solution

Déclarer DBContext comme une variable locale ou un champ d’instance non statique et utilisez-le pour une tâche puis laissez-le être supprimées après utilisation.

L’exemple suivant classe de contrôleur MVC montre comment utiliser l’objet DBContext.

```
public class BlogsController : Controller
    {
        //BloggingContext is a subclass to DbContext        
        private BloggingContext db = new BloggingContext();
        // GET: Blogs
        public ActionResult Index()
        {
            //business logics…
            return View();
        }
        protected override void Dispose(bool disposing)
        {
            if (disposing)
            {
                db.Dispose();
            }
            base.Dispose(disposing);
        }
    }
```

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur optimzing et résolution des problèmes d’applications Azure, consultez [résoudre les problèmes d’une application web dans le Service d’application Azure à l’aide de Visual Studio](./app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md).
