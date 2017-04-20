<properties
    pageTitle="Surveiller votre API de gestion des API Azure, concentrateur d’événement et Runscope"
    description="Exemple d’application montrant la stratégie de journal-eventhub par connexion Azure API gestion, Azure événement Hubs et Runscope pour le protocole HTTP journalisation et la surveillance"
    services="api-management"
    documentationCenter=""
    authors="darrelmiller"
    manager="erikre"
    editor=""/>

<tags
    ms.service="api-management"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="darrmi"/>

# <a name="monitor-your-apis-with-azure-api-management-event-hubs-and-runscope"></a>Surveiller votre API de gestion des API Azure, concentrateur d’événement et Runscope

Le [service de gestion des API](api-management-key-concepts.md) fournit de nombreuses fonctionnalités afin d’améliorer le traitement des demandes HTTP envoyées à votre API HTTP. Toutefois, l’existence des demandes et les réponses sont transitoires. La demande est effectuée et le texte passe via le service de gestion de l’API à la principale API. Votre API traite la demande et une réponse passe par le biais du consommateur API. Le service de gestion de l’API conserve certaines statistiques importantes sur les API pour l’affichage dans le tableau de bord de portail de Publisher, mais au-delà, les détails ont disparu.

À l’aide de la [stratégie](api-management-howto-policies.md) de [journal-eventhub](https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub) dans le service de gestion de l’API, vous pouvez envoyer tous les détails de la demande et réponse à un [Concentrateur d’événement Azure](../event-hubs/event-hubs-what-is-event-hubs.md). Il existe de nombreuses raisons raison pour laquelle vous souhaitez générer des événements à partir des messages HTTP envoyés à votre API. Voici quelques exemples de piste d’audit des mises à jour, analytique de l’utilisation, d’alerte exception 3e intégrations tierces.   

Cet article explique comment faire pour capturer le message de demande et réponse HTTP entière, envoyez-le à un concentrateur de l’événement et puis transmettre ce message à un service fournissant HTTP journalisation et la surveillance de services tiers.

## <a name="why-send-from-api-management-service"></a>Pourquoi envoyer à partir de Service de gestion des API ?
Il est possible d’écrire des logiciels intermédiaires HTTP qui peut s’intégrer à structures d’API HTTP pour capturer les requêtes HTTP et les réponses et les flux à la journalisation et la surveillance des systèmes. L’inconvénient de cette approche est logiciels intermédiaires HTTP doit être intégré à l’API serveur principal et doit correspondre à la plateforme de l’API. S’il existe plusieurs API chacun d’eux doit déployer logiciels intermédiaires. Il existe souvent raisons pourquoi principal API ne peut pas être mis à jour.

À l’aide du service de gestion de l’API Azure intégrer avec infrastructure de journalisation fournit une solution centralisée et indépendante de la plate-forme. Il est également scalable, dans la partie en raison des fonctionnalités [geo réplication](api-management-howto-deploy-multi-region.md) de gestion de l’API Azure.

## <a name="why-send-to-an-azure-event-hub"></a>Pourquoi envoyer à un concentrateur événement Azure ?
Il est une judicieux de demander, pourquoi créer une stratégie qui est propre à Azure événement Hubs ? Il existe différents endroits où j’Imaginons que vous voulez ouvrir une session Mes requêtes. Pourquoi ne pas simplement envoyer les demandes directement à la destination finale ?  Il s’agit d’une option. Toutefois, lorsque vous effectuez des demandes de journalisation à partir d’un service de gestion des API, il est nécessaire prendre en compte l’impact de journalisation des messages sur les performances de l’API. Progressive augmentations de charge peuvent être résolues en augmentant instances des composants du système disponibles ou en tirant parti de la réplication de geo. Toutefois, courte pointes de trafic peuvent entraîner des demandes d’être retardée considérablement si les demandes à l’infrastructure de journalisation commencent à ralentir sous charge.

Les Hubs événement Azure est conçu pour très grands volumes de pénétration de données, avec capacité pour gérer un nombre d’événements beaucoup plus élevé que le nombre de requêtes HTTP la plupart des processus API. Le Hub de l’événement se comporte comme un type de mémoire tampon sophistiqué entre votre service de gestion des API et l’infrastructure qui stockera et traiter les messages. Cela garantit que les performances de l’API ne seront pas détérioré en raison de l’infrastructure de journalisation.  

Une fois que les données ont été transmis à un concentrateur événement, il est conservé et attend pour les consommateurs événement concentrateur pour le traiter. Le Hub de l’événement ne souhaite pas le mode de traitement, concernent uniquement en prenant soin du que message est remis correctement.     

Événement Hubs ont la possibilité pour les événements de flux de données à plusieurs groupes de consommateurs. Cela permet d’événements devant être traité par systèmes totalement différents. Cela permet de prise en charge de nombreux scénarios d’intégration sans plaçant retards addition sur la transformation de la demande de l’API au sein du service de gestion de l’API qu’un seul événement doit être générée.

## <a name="a-policy-to-send-applicationhttp-messages"></a>Une stratégie pour envoyer des messages de l’application/http
Un concentrateur événement accepte les données d’événement sous forme de chaîne simple. Le contenu de cette chaîne est complètement vous appartient. Pour pouvoir empaqueter une requête HTTP et envoyer aux événements Hubs nous avons besoin de mettre en forme la chaîne contenant les informations de demande ou de réponse. Dans les situations de cette façon, s’il existe une mise en forme existante nous pouvons réutiliser, puis nous pouvons avez pas à écrire notre propre l’analyse du code. J’ai considéré initialement à l’aide de la [QAR](http://www.softwareishard.com/blog/har-12-spec/) pour envoyer les demandes et réponses HTTP. Toutefois, ce format est optimisé pour stocker une séquence de requêtes HTTP dans un format JSON en fonction. Il contenue un nombre d’éléments obligatoires qui ajouté complexité inutile pour le scénario de passer le message HTTP sur le réseau.  

Une autre option était d’utiliser le `application/http` type d’élément multimédia comme décrit dans la spécification HTTP [RFC 7230](http://tools.ietf.org/html/rfc7230). Ce type de média utilise le même format que celui qui est utilisé pour envoyer des messages HTTP sur le réseau, mais l’intégralité du message peut être placé dans le corps d’une autre demande HTTP. Dans notre exemple, nous allons simplement à utiliser le corps comme notre message à envoyer aux événements Hubs. Pratique, il est un analyseur qui existe dans les bibliothèques [Microsoft ASP.NET 2.2 WebClient API](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/) qui peuvent analyser ce format et la convertir en natif `HttpRequestMessage` et `HttpResponseMessage` objets.

Pour pouvoir créer ce message que nous avons besoin de tirer parti de c# en fonction des [expressions de stratégie](https://msdn.microsoft.com/library/azure/dn910913.aspx) gestion des API Azure. Voici la stratégie qui envoie un message de demande HTTP à Azure événement Hubs.

       <log-to-eventhub logger-id="conferencelogger" partition-id="0">
       @{
           var requestLine = string.Format("{0} {1} HTTP/1.1\r\n",
                                                       context.Request.Method,
                                                       context.Request.Url.Path + context.Request.Url.QueryString);

           var body = context.Request.Body?.As<string>(true);
           if (body != null && body.Length > 1024)
           {
               body = body.Substring(0, 1024);
           }

           var headers = context.Request.Headers
                                  .Where(h => h.Key != "Authorization" && h.Key != "Ocp-Apim-Subscription-Key")
                                  .Select(h => string.Format("{0}: {1}", h.Key, String.Join(", ", h.Value)))
                                  .ToArray<string>();

           var headerString = (headers.Any()) ? string.Join("\r\n", headers) + "\r\n" : string.Empty;

           return "request:"   + context.Variables["message-id"] + "\n"
                               + requestLine + headerString + "\r\n" + body;
       }
       </log-to-eventhub>

### <a name="policy-declaration"></a>Déclaration de stratégie
Il quelques aspects particuliers aborder sur cette expression de stratégie. La stratégie de journal-eventhub possède un attribut appelé id journal qui fait référence au nom de journal qui a été créé dans le service de gestion de l’API. Vous trouverez les détails d’un journal événement concentrateur dans le service de gestion de l’API de configuration dans le document [comment enregistrer des événements dans Azure événement Hubs gestion des API Azure](api-management-howto-log-event-hubs.md). L’attribut deuxième est un paramètre facultatif qui indique Hubs événement qui partition pour stocker le message dans. Événement Hubs utilisent des partitions à activer expansion et d’exiger un minimum de deux. La livraison chronologique des messages est garantie uniquement au sein d’une partition. Si nous ne pas demander à concentrateur événement dans quelle partition pour placer le message, il utilise un algorithme cyclique pour répartir la charge. Toutefois, qui peut entraîner certains de nos messages soient traités de manière désordonnée.  

### <a name="partitions"></a>Partitions
Pour vous assurer que nos messages remis aux consommateurs dans l’ordre et tirer parti de la fonction de distribution de charge des partitions, j’ai choisi d’envoyer des messages de demande HTTP à une partition et les messages de réponse HTTP sur une deuxième partition. Cela permet de garantir une distribution charge pair et nous pouvons garantir que toutes les demandes seront consommées dans l’ordre et toutes les réponses ne seront utilisés dans l’ordre. Il est possible d’une réponse à consommer avant la demande correspondante, mais qui n’est pas un problème que nous avons les demandes d’un autre système de corrélation pour les réponses et nous sachent que demandes sont toujours avant les réponses.

### <a name="http-payloads"></a>Charges HTTP
Après la génération du `requestLine` nous vérifier si le corps de la requête doit être tronqué. Corps de la demande est tronqué à 1024 uniquement. Cela peut être augmentée, alors que les messages de l’événement concentrateur sont limités à 256 Ko, il est probable que certains HTTP corps ne tient ne pas dans un seul message. Lors de la journalisation et analytique un grand nombre d’informations peut être dérivé simplement la ligne de requête HTTP et les en-têtes. En outre, plusieurs requêtes API renvoient uniquement organismes petites et la perte de valeur des informations par troncature corps volumineux n’est minime par rapport à la réduction du transfert, de traitement et les coûts de stockage à conserver tout le contenu corps. Une dernière remarque sur le corps de traitement est que nous avons besoin de passer `true` à l’As<string>méthode () nous sommes en train de lire le contenu du corps, mais n’a pas été également souhaitez que le système principal API puissent lire le corps. En passant la valeur True pour cette méthode nous entraîner le corps en mémoire tampon afin qu’il puisse être lu une seconde fois. Il est important de tenir compte des si vous avez une API qui ne le téléchargement de fichiers volumineux ou utilise l’interrogation longue. Dans ce cas, il serait préférable de ne pas lire le corps du tout.   

### <a name="http-headers"></a>En-têtes HTTP
En-têtes HTTP peuvent être transférées simplement via dans le format du message dans un format de paire clé/valeur simple. Nous avons choisi à retirer certains champs sensibles de sécurité, afin d’éviter inutilement perd les informations d’identification. Il est probable que clés d’API et d’autres informations d’identification à utiliser à des fins analytique. Si nous souhaitons effectuer des analyses de l’utilisateur et le produit qu’ils utilisent puis nous pouvons obtenir qu’à partir de la `context` objet et ajoutez au message.     
### <a name="message-metadata"></a>Métadonnées de message
Lorsque vous créez le message complet à envoyer au hub événement, la première ligne n’est pas en fait partie de la `application/http` message. La première ligne est composée de si le message est une demande ou message de réponse et un id de message qui est utilisé pour faire correspondre les demandes de réponses des métadonnées supplémentaires. L’id du message est créé à l’aide d’une autre stratégie qui ressemble à ceci :

    <set-variable name="message-id" value="@(Guid.NewGuid())" />

Nous pourrions avoir créé le message de demande, qui stockés dans une variable jusqu'à ce que la réponse a été retournée et ensuite simplement envoyée la demande et réponse comme un seul message. Toutefois, en envoyant la demande et réponse séparément et à l’aide d’un id de message pour faire correspondre les deux, nous recevons un peu plus de souplesse dans la taille du message, la possibilité de tirer parti de plusieurs partitions, tout en conservant ordre des messages et la demande s’affichera plus tôt dans notre tableau de bord de journalisation. Il existe également peut-être certains scénarios où une réponse valide n’est jamais envoyée au concentrateur événement, éventuellement en raison d’une erreur de demande irrécupérables dans le service de gestion de l’API, mais nous pourront toujours être un enregistrement de la demande.

La stratégie pour envoyer le message de réponse HTTP est très similaire à la demande et donc la configuration de la stratégie terminée ressemble à ceci :

      <policies>
        <inbound>
            <set-variable name="message-id" value="@(Guid.NewGuid())" />
            <log-to-eventhub logger-id="conferencelogger" partition-id="0">
              @{
                  var requestLine = string.Format("{0} {1} HTTP/1.1\r\n",
                                                              context.Request.Method,
                                                              context.Request.Url.Path + context.Request.Url.QueryString);

                  var body = context.Request.Body?.As<string>(true);
                  if (body != null && body.Length > 1024)
                  {
                      body = body.Substring(0, 1024);
                  }

                  var headers = context.Request.Headers
                                       .Where(h => h.Key != "Authorization" && h.Key != "Ocp-Apim-Subscription-Key")
                                       .Select(h => string.Format("{0}: {1}", h.Key, String.Join(", ", h.Value)))
                                       .ToArray<string>();

                  var headerString = (headers.Any()) ? string.Join("\r\n", headers) + "\r\n" : string.Empty;

                  return "request:"   + context.Variables["message-id"] + "\n"
                                      + requestLine + headerString + "\r\n" + body;
              }
          </log-to-eventhub>
        </inbound>
        <backend>
            <forward-request follow-redirects="true" />
        </backend>
        <outbound>
            <log-to-eventhub logger-id="conferencelogger" partition-id="1">
              @{
                  var statusLine = string.Format("HTTP/1.1 {0} {1}\r\n",
                                                      context.Response.StatusCode,
                                                      context.Response.StatusReason);

                  var body = context.Response.Body?.As<string>(true);
                  if (body != null && body.Length > 1024)
                  {
                      body = body.Substring(0, 1024);
                  }

                  var headers = context.Response.Headers
                                                  .Select(h => string.Format("{0}: {1}", h.Key, String.Join(", ", h.Value)))
                                                  .ToArray<string>();

                  var headerString = (headers.Any()) ? string.Join("\r\n", headers) + "\r\n" : string.Empty;

                  return "response:"  + context.Variables["message-id"] + "\n"
                                      + statusLine + headerString + "\r\n" + body;
             }
          </log-to-eventhub>
        </outbound>
      </policies>

La `set-variable` stratégie crée une valeur qui n’est accessible par les deux le `log-to-eventhub` stratégie dans la `<inbound>` section et la `<outbound>` section.  

## <a name="receiving-events-from-event-hubs"></a>Recevoir des événements à partir de l’événement Hubs
Réception d’événements à partir d’Azure événement concentrateur en utilisant le [protocole AMQP](http://www.amqp.org/). L’équipe Microsoft Service Bus ont apportées client pour faciliter les événements beaucoup de bibliothèques. Il existe deux approches pris en charge, une est en cours d’un *Grand public Direct* et l’autre utilise la `EventProcessorHost` cours. Vous trouverez des exemples de ces deux approches de [l’Événement Hubs Programming Guide](../event-hubs/event-hubs-programming-guide.md). La version courte des différences est, `Direct Consumer` vous permet de contrôler et la `EventProcessorHost` effectue automatiquement partie du travail plomberie vous, mais émet certaines hypothèses sur la façon dont vous allez traiter les événements.  

### <a name="eventprocessorhost"></a>EventProcessorHost
Dans cet exemple, nous allons utiliser la `EventProcessorHost` pour simplifier, cependant il peut ne pas le meilleur choix pour ce scénario particulier. `EventProcessorHost`effectue le travail difficile de vérifier que vous n’avez pas à vous soucier de problèmes au sein d’un cours de processeur événement particulier de thread. Toutefois, dans notre scénario, nous allons simplement convertir le message vers un autre format, puis transmet à un autre service à l’aide d’une méthode asynchrone. Il est inutile de mise à jour d’état partagé et par conséquent aucun risque de problèmes liés aux threads. La plupart des scénarios, `EventProcessorHost` est probablement le meilleur choix ni certainement l’option plus facilement.     

### <a name="ieventprocessor"></a>IEventProcessor
Le concept central lors de l’utilisation `EventProcessorHost` consiste à créer une implémentation de la `IEventProcessor` interface qui contient la méthode `ProcessEventAsync`. L’essence même de cette méthode est indiquée ici :

  {Tâche IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages) asynchrone

           foreach (EventData eventData in messages)
           {
               _Logger.LogInfo(string.Format("Event received from partition: {0} - {1}", context.Lease.PartitionId,eventData.PartitionKey));

               try
               {
                   var httpMessage = HttpMessage.Parse(eventData.GetBodyStream());
                   await _MessageContentProcessor.ProcessHttpMessage(httpMessage);
               }
               catch (Exception ex)
               {
                   _Logger.LogError(ex.Message);
               }
           }
            ... checkpointing code snipped ...
        }

Une liste d’objets EventData sont transmis à la méthode et nous parcourir cette liste. Les octets de chaque méthode sont analysées dans un objet HttpMessage et cet objet est passé à une instance de IHttpMessageProcessor.

### <a name="httpmessage"></a>HttpMessage
La `HttpMessage` instance contienne trois séries de données :

      public class HttpMessage
       {
           public Guid MessageId { get; set; }
           public bool IsRequest { get; set; }
           public HttpRequestMessage HttpRequestMessage { get; set; }
           public HttpResponseMessage HttpResponseMessage { get; set; }

        ... parsing code snipped ...

      }

La `HttpMessage` instance contient un `MessageId` GUID qui nous permettent de vous connecter la requête HTTP à la réponse HTTP correspondante et une valeur booléenne qui indique si l’objet contient une instance de HttpRequestMessage et HttpResponseMessage. À l’aide d’intégré dans des classes HTTP de `System.Net.Http`, j’ai réussi à tirer parti de la `application/http` l’analyse du code est inclus dans `System.Net.Http.Formatting`.  

### <a name="ihttpmessageprocessor"></a>IHttpMessageProcessor
La `HttpMessage` instance est ensuite transmis à l’implémentation de `IHttpMessageProcessor` qui est une interface que j’ai créée pour détacher la réception et l’interprétation de l’événement à partir d’Azure événement concentrateur et le traitement réel de celle-ci.


## <a name="forwarding-the-http-message"></a>Transférer le message HTTP
Dans cet exemple, j’ai décidé qu’il serait intéressant de basculez la requête HTTP à [Runscope](http://www.runscope.com). Runscope est un service basé sur le cloud spécialisé dans HTTP débogage, la journalisation et la surveillance. Ils disposent d’un niveau gratuit, afin qu’il est facile à essayer et il permet de voir les requêtes HTTP dans s’étalant en temps réel via notre service de gestion de l’API.

La `IHttpMessageProcessor` implémentation ressemble à ceci,

      public class RunscopeHttpMessageProcessor : IHttpMessageProcessor
       {
           private HttpClient _HttpClient;
           private ILogger _Logger;
           private string _BucketKey;
           public RunscopeHttpMessageProcessor(HttpClient httpClient, ILogger logger)
           {
               _HttpClient = httpClient;
               var key = Environment.GetEnvironmentVariable("APIMEVENTS-RUNSCOPE-KEY", EnvironmentVariableTarget.User);
               _HttpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("bearer", key);
               _HttpClient.BaseAddress = new Uri("https://api.runscope.com");
               _BucketKey = Environment.GetEnvironmentVariable("APIMEVENTS-RUNSCOPE-BUCKET", EnvironmentVariableTarget.User);
               _Logger = logger;
           }

           public async Task ProcessHttpMessage(HttpMessage message)
           {
               var runscopeMessage = new RunscopeMessage()
               {
                   UniqueIdentifier = message.MessageId
               };

               if (message.IsRequest)
               {
                   _Logger.LogInfo("Sending HTTP request " + message.MessageId.ToString());
                   runscopeMessage.Request = await RunscopeRequest.CreateFromAsync(message.HttpRequestMessage);
               }
               else
               {
                   _Logger.LogInfo("Sending HTTP response " + message.MessageId.ToString());
                   runscopeMessage.Response = await RunscopeResponse.CreateFromAsync(message.HttpResponseMessage);
               }

               var messagesLink = new MessagesLink() { Method = HttpMethod.Post };
               messagesLink.BucketKey = _BucketKey;
               messagesLink.RunscopeMessage = runscopeMessage;
               var runscopeResponse = await _HttpClient.SendAsync(messagesLink.CreateRequest());
               _Logger.LogDebug("Request sent to Runscope");
           }
       }

J’ai bénéficier d’une [bibliothèque de client existante pour Runscope](http://www.nuget.org/packages/Runscope.net.hapikit/0.9.0-alpha) qui facilite push `HttpRequestMessage` et `HttpResponseMessage` instances vers le haut dans son service. Afin d’accéder à l’API Runscope vous avez besoin d’un compte et une clé de l’API. Vous trouverez des instructions pour obtenir une clé API dans l’enregistrement de la [Création d’Applications à accès Runscope API](http://blog.runscope.com/posts/creating-applications-to-access-the-runscope-api) .

## <a name="complete-sample"></a>Exemple complet
Le [code source](https://github.com/darrelmiller/ApimEventProcessor) et les tests pour l’échantillon sont Github. Vous avez besoin d’un [Service de gestion des API](api-management-get-started.md), [Un concentrateur événement connecté](api-management-howto-log-event-hubs.md)et un [Compte de stockage](../storage/storage-create-storage-account.md) pour exécuter l’exemple pour vous-même.   

L’exemple est simplement une application Console simple qui est à l’écoute des événements en provenance de Hub de l’événement, convertit les transformer une `HttpRequestMessage` et `HttpResponseMessage` objets, puis les transfère à l’API Runscope.

Dans l’image animée suivante, vous pouvez voir une demande sachant à une API dans le portail développeur, l’application Console affichant le message reçu, traitées et transmises puis la demande et réponse ne s’affiche dans l’inspecteur de trafic Runscope.

![Démonstration de demande transféré vers Runscope](./media/api-management-log-to-eventhub-sample/apim-eventhub-runscope.gif)

## <a name="summary"></a>Résumé
Service de gestion de l’API Azure fournit un endroit idéal pour capturer le trafic HTTP en déplacement vers et depuis votre API. Azure événement Hubs est une solution hautement scalable et de prix pour capturer le trafic et intégrer les systèmes de traitement secondaire pour la journalisation, surveillance et autres sophistiquées analytique. Connexion à 3e partie de surveillance du trafic systèmes comme Runscope est une simple en tant que quelques douzaines lignes de code.

## <a name="next-steps"></a>Étapes suivantes
-   En savoir plus sur Azure événement Hubs
    -   [Prise en main Azure événement Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
    -   [Recevoir des messages avec EventProcessorHost](../event-hubs/event-hubs-csharp-ephcs-getstarted.md#receive-messages-with-eventprocessorhost)
    -   [Événement Hubs guide de programmation](../event-hubs/event-hubs-programming-guide.md)
-   Pour plus d’informations sur l’intégration de la gestion de l’API et Hubs d’événement
    -   [Comment enregistrer des événements dans Azure événement Hubs gestion des API Azure](api-management-howto-log-event-hubs.md)
    -   [Référence d’entité journal](https://msdn.microsoft.com/library/azure/mt592020.aspx)
    -   [référence de la stratégie de journal-eventhub](https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub)
    