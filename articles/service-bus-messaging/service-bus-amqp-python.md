<properties 
    pageTitle="Service Bus et Python avec AMQP 1.0 | Microsoft Azure"
    description="Utilisation de Bus des services à partir de Python avec AMQP."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" /> 
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="09/29/2016"
    ms.author="sethm" />

# <a name="using-service-bus-from-python-with-amqp-10"></a>À l’aide de Bus des services à partir de Python avec AMQP 1.0

[AZURE.INCLUDE [service-bus-selector-amqp](../../includes/service-bus-selector-amqp.md)]

PROTONS Python est une liaison de langage Python à PROTONS-C ; Autrement dit, PROTONS Python est implémenté comme une enveloppe autour d’un moteur implémenté dans C.

## <a name="download-the-proton-client-library"></a>Télécharger la bibliothèque de client PROTONS

Vous pouvez télécharger PROTONS-C et ses liaisons associées (y compris les Python) à partir de [http://qpid.apache.org/download.html](http://qpid.apache.org/download.html). Le téléchargement est sous forme de code source. Pour générer le code, suivez les instructions contenues dans le package téléchargé.

Notez qu’au moment de la rédaction de ce document, la prise en charge SSL dans PROTONS-C est uniquement disponible pour les systèmes d’exploitation Linux. Bus des services Azure exigeant l’utilisation de SSL, PROTONS-c (et les liaisons de langue) peuvent uniquement être permettant d’accéder aux Bus des services de Linux pour le moment. Travail pour activer PROTONS-C avec SSL sous Windows est lancé revenez précédent fréquemment des mises à jour.

## <a name="working-with-service-bus-queues-topics-and-subscriptions-from-python"></a>Utilisation des files d’attente, les rubriques et les abonnements à partir de Python Bus des services

Le code suivant montre comment envoyer et recevoir des messages à partir d’un Bus de Service de messagerie entité.

### <a name="send-messages-using-proton-python"></a>Envoyer des messages à l’aide de PROTONS Python

Le code suivant montre comment envoyer un message à un Bus des services de messagerie entité.

```
messenger = Messenger()
message = Message()
message.address = "amqps://[username]:[password]@[namespace].servicebus.windows.net/[entity]"

message.body = u"This is a text string"
messenger.put(message)
messenger.send()
```

### <a name="receive-messages-using-proton-python"></a>Recevoir des messages à l’aide de PROTONS Python

Le code suivant montre comment recevoir un message à partir d’un Bus de Service de messagerie entité.

```
messenger = Messenger()
address = "amqps://[username]:[password]@[namespace].servicebus.windows.net/[entity]"
messenger.subscribe(address)

messenger.start()
messenger.recv(1)
message = Message()
if messenger.incoming:
      messenger.get(message)
messenger.stop()
```

## <a name="messaging-between-net-and-proton-python"></a>Messagerie entre .NET et PROTONS Python

### <a name="application-properties"></a>Propriétés de l’application

#### <a name="proton-python-to-service-bus-net-apis"></a>PROTONS-Python à Bus .NET API de Service

Messages PROTONS Python prend en charge les propriétés de l’application des types suivants : **int**, **long**, **le flottement**, **uuid**, **bool**, **chaîne**. Le code Python suivant montre comment définir les propriétés d’un message à l’aide de chacun de ces types de propriété.

```
message.properties[u"TestString"] = u"This is a string"    
message.properties[u"TestInt"] = 1
message.properties[u"TestLong"] = 1000L
message.properties[u"TestFloat"] = 1.5    
message.properties[u"TestGuid"] = uuid.uuid1()    
```

Dans l’API .NET Bus du Service, les propriétés de l’application message sont effectuées dans la collection **Properties** de [BrokeredMessage][]. Le code suivant montre comment lire les propriétés de l’application d’un message provenant d’un client Python.

```
if (message.Properties.Keys.Count > 0)
{
  foreach (string name in message.Properties.Keys)
  {
    Object value = message.Properties[name];
    Console.WriteLine(name + ": " + value + " (" + value.GetType() + ")" );
  }
  Console.WriteLine();
}
```

Le tableau suivant mappe les types de propriétés Python aux types de propriété .NET.

| Type de propriété Python | Type de propriété .NET |
|----------------------|--------------------|
| ent                  | ent                |
| Flottement                | Double             |
| long                 | Int64              |
| UUID                 | GUID               |
| bool                 | bool               |
| chaîne               | chaîne             |

#### <a name="service-bus-net-apis-to-proton-python"></a>Service Bus API .NET PROTONS Python

Le type [BrokeredMessage][] prend en charge les propriétés de l’application des types suivants : **byte**, **sbyte**, **car**, **courte**, **ushort**, **int**, **uint**, **long**, **ulong**, **le flottement**, **double**, **decimal**, **bool**, **Guid**, **chaîne**, **Uri**, **DateTime**, **DateTimeOffset**et **TimeSpan**. Le code .NET suivant montre comment définir des propriétés sur un objet [BrokeredMessage][] à l’aide de chacun de ces types de propriété.

```
message.Properties["TestByte"] = (byte)128;
message.Properties["TestSbyte"] = (sbyte)-22;
message.Properties["TestChar"] = (char) 'X';
message.Properties["TestShort"] = (short)-12345;
message.Properties["TestUshort"] = (ushort)12345;
message.Properties["TestInt"] = (int)-100;
message.Properties["TestUint"] = (uint)100;
message.Properties["TestLong"] = (long)-12345;
message.Properties["TestUlong"] = (ulong)12345;
message.Properties["TestFloat"] = (float)3.14159;
message.Properties["TestDouble"] = (double)3.14159;
message.Properties["TestDecimal"] = (decimal)3.14159;
message.Properties["TestBoolean"] = true;
message.Properties["TestGuid"] = Guid.NewGuid();
message.Properties["TestString"] = "Service Bus";
message.Properties["TestUri"] = new Uri("http://www.bing.com");
message.Properties["TestDateTime"] = DateTime.Now;
message.Properties["TestDateTimeOffSet"] = DateTimeOffset.Now;
message.Properties["TestTimeSpan"] = TimeSpan.FromMinutes(60);
message.Properties["TestTimeSpan"] = TimeSpan.FromMinutes(60);
```

Le code Python suivant montre comment lire les propriétés de l’application d’un message provenant d’un client Service Bus .NET.

```
if message.properties != None:
   for k,v in message.properties.items():         
         print "--   %s : %s (%s)" % (k, str(v), type(v))         
```

Le tableau suivant mappe les types de propriétés .NET aux types de propriété Python.

| Type de propriété .NET | Type de propriété Python | Notes                                                                                                                                                               |
|--------------------|----------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| sur un octet               | ent                  | -                                                                                                                                                                     |
| sbyte              | ent                  | -                                                                                                                                                                     |
| car               | car                 | Cours de PROTONS Python                                                                                                                                                 |
| courte              | ent                  | -                                                                                                                                                                     |
| ushort             | ent                  | -                                                                                                                                                                     |
| ent                | ent                  | -                                                                                                                                                                     |
| uint               | ent                  | -                                                                                                                                                                     |
| long               | ent                  | -                                                                                                                                                                     |
| ulong              | long                 | Cours de PROTONS Python                                                                                                                                                 |
| Flottement              | Flottement                | -                                                                                                                                                                     |
| Double             | Flottement                | -                                                                                                                                                                     |
| Decimal            | Chaîne               | Décimale n’est pas pris en charge avec PROTONS.                                                                                                                     |
| bool               | bool                 | -                                                                                                                                                                     |
| GUID               | UUID                 | Cours de PROTONS Python                                                                                                                                                 |
| chaîne             | chaîne               | -                                                                                                                                                                     |
| Date/heure           | horodatage            | Cours de PROTONS Python                                                                                                                                                 |
| DateTimeOffset     | DescribedType        | DateTimeOffset.UtcTicks mappé au type de AMQP :<type name=”datetime-offset” class=restricted source=”long”><descriptor name=”com.microsoft:datetime-offset” /></type> |
| TimeSpan           | DescribedType        | Timespan.Ticks mappé au type de AMQP :<type name=”timespan” class=restricted source=”long”><descriptor name=”com.microsoft:timespan” /></type>                        |
| URI                | DescribedType        | Uri.AbsoluteUri mappé au type de AMQP :<type name=”uri” class=restricted source=”string”><descriptor name=”com.microsoft:uri” /></type>                               |

### <a name="standard-properties"></a>Propriétés standard

Les tableaux suivants illustrent le mappage entre les propriétés de message standard PROTONS Python et les propriétés de message standard [BrokeredMessage][] .

#### <a name="proton-python-to-service-bus-net-apis"></a>PROTONS-Python à Bus .NET API de Service

| PROTONS Python        | Bus des services .NET         | Notes                                                     |
|----------------------|--------------------------|-----------------------------------------------------------|
| résistant              | n/a                      | Bus des services prend uniquement en charge les messages résistants.               |
| priorité             | n/a                      | Service Bus prend uniquement en charge une seul message priorité élevée.      |
| Durée de vie                  | Message.TimeToLive       | Conversion, PROTONS Python TTL est définie en millisecondes. |
| première\_acquéreur      | n/a                      | -                                                           |
| remise\_count      | n/a                      | -                                                           |
| ID                   | Message.MessageID        | -                                                           |
| utilisateur\_id             | n/a                      | -                                                           |
| adresse              | Message.To               | -                                                           |
| Objet              | Message.Label            | -                                                           |
| réponse\_à            | Message.ReplyTo          | -                                                           |
| corrélation\_id      | Message.CorrelationID    | -                                                           |
| contenu\_type        | Message.ContentType      | -                                                           |
| contenu\_codage    | n/a                      | -                                                           |
| expiration\_heure         | n/a                      | -                                                           |
| Création d’une\_heure       | n/a                      | -                                                           |
| groupe\_id            | Message.SessionId        | -                                                           |
| groupe\_séquence      | n/a                      | -                                                           |
| réponse\_à\_groupe\_id | Message.ReplyToSessionId | -                                                           |
| mettre en forme               | n/a                      | -                                                           |

| Bus des services .NET        | PROTONS                       | Notes                                                     |
|-------------------------|------------------------------|-----------------------------------------------------------|
| ContentType             | Message.Content\_type        | -                                                           |
| ID de corrélation           | Message.Correlation\_id      | -                                                           |
| EnqueuedTimeUtc         | n/a                          | -                                                           |
| Étiquette                   | Message.Subject              | -                                                           |
| MessageId               | Message.ID                   | -                                                           |
| ReplyTo                 | Message.Reply\_à            | -                                                           |
| ReplyToSessionId        | Message.Reply\_à\_groupe\_id | -                                                           |
| ScheduledEnqueueTimeUtc | n/a                          | -                                                           |
| ID de session               | Message.Group\_id            | -                                                           |
| Propriété TimeToLive              | Message.TTL                  | Conversion, PROTONS Python TTL est définie en millisecondes. |
| À                      | Message.Address              | -                                                           |

## <a name="next-steps"></a>Étapes suivantes

Prêt pour en savoir plus ? Consultez les liens suivants :

- [Vue d’ensemble du service Bus AMQP]
- [AMQP dans Service marché pour Windows Server]

[BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx
[AMQP dans Service marché pour Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx

[Vue d’ensemble du service Bus AMQP]: service-bus-amqp-overview.md
