<properties 
    pageTitle="Service Bus avec .NET et AMQP 1.0 | Microsoft Azure"
    description="À l’aide de Bus des services à partir de .NET avec AMQP"
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
    ms.date="10/03/2016"
    ms.author="sethm" />

# <a name="using-service-bus-from-net-with-amqp-10"></a>À l’aide de Bus des services à partir de .NET avec AMQP 1.0

[AZURE.INCLUDE [service-bus-selector-amqp](../../includes/service-bus-selector-amqp.md)]

## <a name="downloading-the-service-bus-sdk"></a>Télécharger le Kit de développement logiciel Bus des services

Prise en charge AMQP 1.0 est disponible dans le Kit de développement de Bus Service version 2.1 ou version ultérieure. Vous pouvez vous assurer que vous avez la version la plus récente en téléchargeant les bits Bus de Service à partir de [NuGet][].

## <a name="configuring-net-applications-to-use-amqp-10"></a>Configurer des applications .NET à utiliser AMQP 1.0

Par défaut, la bibliothèque cliente Service Bus .NET communique avec le service Bus des services à l’aide d’un protocole SOAP dédié. Pour utiliser AMQP 1.0 au lieu de la valeur par défaut protocole requiert une configuration explicite sur la chaîne de connexion Bus des services, comme décrit dans la section suivante. Différent de cette modification, code de l’application reste inchangé lorsque vous utilisez AMQP 1.0.

Dans la version actuelle, il existe quelques fonctionnalités API qui ne sont pas pris en charge lorsque vous utilisez AMQP. Ces fonctionnalités non prises en charge sont répertoriées ultérieurement dans la section [des fonctionnalités non prises en charge, les restrictions et des différences de comportement](#unsupported-features-restrictions-and-behavioral-differences). Certains paramètres de configuration avancés également ont une signification différente lors de l’utilisation de AMQP.

### <a name="configuration-using-appconfig"></a>Configuration à l’aide de App.config

Il est conseillé aux applications d’utiliser le fichier de configuration App.config pour stocker les paramètres. Pour les applications de Service Bus, vous pouvez utiliser App.config pour stocker les paramètres pour la valeur Service Bus **ConnectionString** . Un exemple de fichier App.config est la suivante :

    <?xml version="1.0" encoding="utf-8" ?>
    <configuration>
        <appSettings>
            <add key="Microsoft.ServiceBus.ConnectionString"
                 value="Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp" />
        </appSettings>
    </configuration>

La valeur de la `Microsoft.ServiceBus.ConnectionString` paramètre est la chaîne de connexion de Service Bus servant à configurer la connexion au Service Bus. Le format est comme suit :

    Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp

Où `[namespace]` et `SharedAccessKey` obtenu à partir du [portail Azure][]. Pour plus d’informations, voir [prise en main files d’attente Bus des services][].

Lorsque vous utilisez AMQP, ajoutez la chaîne de connexion avec `;TransportType=Amqp`. Cette notation vous informe de la bibliothèque de client pour rendre sa connexion au Service Bus à l’aide de AMQP 1.0.

## <a name="message-serialization"></a>Sérialisation des messages

Lorsque vous utilisez le protocole par défaut, le comportement de sérialisation par défaut de la bibliothèque cliente .NET consiste à utiliser le type de [DataContractSerializer][] pour sérialiser une instance de [BrokeredMessage][] de transport entre la bibliothèque de client et le service Bus des services. Lorsque vous utilisez le mode de transport AMQP, la bibliothèque cliente utilise le système de type AMQP pour la sérialisation du [message traité][BrokeredMessage] dans un message AMQP. Cette sérialisation active le message devant être reçus et interprétés par une application de réception qui s’exécute potentiellement sur une autre plate-forme, par exemple, une application Java qui utilise l’API JMS pour accéder aux Bus des services.

Lorsque vous créez une instance de [BrokeredMessage][] , vous pouvez fournir un objet .NET en tant que paramètre au constructeur à utiliser comme le corps du message. Pour les objets qui peuvent être mappés aux types primitifs AMQP, le corps est sérialisé en types de données AMQP. Si l’objet ne peut pas être mappé directement dans un type primitif AMQP ; Autrement dit, un type personnalisé défini par l’application, puis l’objet est sérialisé en utilisant le [DataContractSerializer][]et les octets séries sont envoyés dans un message de données AMQP.

Pour faciliter l’interopérabilité avec les clients non .NET, utilisent uniquement des types .NET qui peuvent être sérialisés directement dans les types AMQP pour le corps du message. Le tableau suivant répertorie les types et le mappage correspondant au système de type AMQP.

| Type d’objet .NET corps          | Type AMQP mappées                          | Type de Section de corps AMQP                                                                                                                                    |
|--------------------------------|-------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------|
| bool                           | valeur booléenne                                   | Valeur AMQP                                                                                                                                                |
| sur un octet                           | ubyte                                     | Valeur AMQP                                                                                                                                                |
| ushort                         | ushort                                    | Valeur AMQP                                                                                                                                                |
| uint                           | uint                                      | Valeur AMQP                                                                                                                                                |
| ulong                          | ulong                                     | Valeur AMQP                                                                                                                                                |
| sbyte                          | sur un octet                                      | Valeur AMQP                                                                                                                                                |
| courte                          | courte                                     | Valeur AMQP                                                                                                                                                |
| ent                            | ent                                       | Valeur AMQP                                                                                                                                                |
| long                           | long                                      | Valeur AMQP                                                                                                                                                |
| Flottement                          | Flottement                                     | Valeur AMQP                                                                                                                                                |
| Double                         | Double                                    | Valeur AMQP                                                                                                                                                |
| Decimal                        | decimal128                                | Valeur AMQP                                                                                                                                                |
| car                           | car                                      | Valeur AMQP                                                                                                                                                |
| Date/heure                       | horodatage                                 | Valeur AMQP                                                                                                                                                |
| GUID                           | UUID                                      | Valeur AMQP                                                                                                                                                |
| Byte]                         | en nombre binaire.                                    | Valeur AMQP                                                                                                                                                |
| chaîne                         | chaîne                                    | Valeur AMQP                                                                                                                                                |
| System.Collections.IList       | liste                                      | AMQP valeur : éléments contenus dans la collection de sites peuvent uniquement être ceux qui sont définis dans cette table.                                                             |
| System.Array                   | tableau                                     | AMQP valeur : éléments contenus dans la collection de sites peuvent uniquement être ceux qui sont définis dans cette table.                                                             |
| System.Collections.IDictionary | carte                                       | AMQP valeur : éléments contenus dans la collection de sites peuvent uniquement être ceux qui sont définis dans cette table. Remarque : seules les clés de chaîne sont pris en charge.                        |
| URI                            | Décrit chaîne (voir le tableau suivant) | Valeur AMQP                                                                                                                                                |
| DateTimeOffset                 | Décrit long (voir le tableau suivant)   | Valeur AMQP                                                                                                                                                |
| TimeSpan                       | Décrit long (voir ce qui suit)         | Valeur AMQP                                                                                                                                                |
| Flux de données                         | en nombre binaire.                                    | Données AMQP (peut être plusieurs). Les sections de données contiennent les octets brutes pour lire à partir de l’objet de flux.                                                           |
| Autre objet                   | en nombre binaire.                                    | Données AMQP (peut être plusieurs). Contient le fichier binaire série de l’objet qui utilise le DataContractSerializer ou un sérialiseur fournie par l’application. |

| Type .NET      | AMQP mappée décrites Type                                                                                              | Notes                   |
|----------------|-------------------------------------------------------------------------------------------------------------------------|-------------------------|
| URI            | `<type name=”uri” class=restricted source=”string”> <descriptor name=”com.microsoft:uri” /></type>`                       | Uri.AbsoluteUri         |
| DateTimeOffset | `<type name=”datetime-offset” class=restricted source=”long”> <descriptor name=”com.microsoft:datetime-offset” /></type>` | DateTimeOffset.UtcTicks |
| TimeSpan       | `<type name=”timespan” class=restricted source=”long”> <descriptor name=”com.microsoft:timespan” /></type> `              | TimeSpan.Ticks          |

## <a name="unsupported-features-restrictions-and-behavioral-differences"></a>Fonctionnalités non prises en charge, les restrictions et les différences

Les fonctionnalités suivantes de l’API Service Bus .NET ne sont pas actuellement pris en charge lorsque vous utilisez AMQP :

-   Transactions

-   Envoyer par la destination du transfert

Il existe également certaines différences dans le comportement de l’API Service Bus .NET petites lorsque vous utilisez AMQP, par rapport au protocole par défaut :

-   La propriété [OperationTimeout][] est ignorée.

-   `MessageReceiver.Receive(TimeSpan.Zero)`fonctionne comme `MessageReceiver.Receive(TimeSpan.FromSeconds(10))`.

-   Fin de messages par jetons verrouiller possible uniquement par les destinataires de messages reçus initialement les messages.

## <a name="controlling-amqp-protocol-settings"></a>Contrôler les paramètres de protocole AMQP

API .NET exposent plusieurs paramètres pour contrôler le comportement du protocole AMQP :

-   **MessageReceiver.PrefetchCount**: détermine le crédit initial appliqué à un lien. La valeur par défaut est égal à 0.

-   **MessagingFactorySettings.AmqpTransportSettings.MaxFrameSize**: ouverture des contrôles de la taille d’image maximale AMQP offert pendant la négociation lors de la connexion. La valeur par défaut est de 65 536 octets.

-   **MessagingFactorySettings.AmqpTransportSettings.BatchFlushInterval**: si les transferts sont batchable, cette valeur détermine le délai maximal pour l’envoi de dispositions. Hérité par expéditeurs/récepteurs par défaut. Expéditeur/destinataire individuel peuvent substituer la valeur par défaut, ce qui correspond à 20 millisecondes.

-   **MessagingFactorySettings.AmqpTransportSettings.UseSslStreamSecurity**: détermine si AMQP connexions sont établies via une connexion SSL. La valeur par défaut est **Vrai**.

## <a name="next-steps"></a>Étapes suivantes

Prêt pour en savoir plus ? Consultez les liens suivants :

- [Vue d’ensemble du service Bus AMQP]
- [Prise en charge de AMQP 1.0 Bus des services partition files d’attente et rubriques]
- [AMQP dans Service marché pour Windows Server]

  [Prise en main files d’attente Bus des services]: service-bus-dotnet-get-started-with-queues.md
  [DataContractSerializer]: https://msdn.microsoft.com/library/azure/system.runtime.serialization.datacontractserializer.aspx
  [BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx
  [Microsoft.ServiceBus.Messaging.MessagingFactory.AcceptMessageSession]: https://msdn.microsoft.com/library/azure/jj657638.aspx
  [Microsoft.ServiceBus.Messaging.MessagingFactory.CreateMessageSender(System.String,System.String)]: https://msdn.microsoft.com/library/azure/jj657703.aspx
  [OperationTimeout]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout.aspx
[NuGet]: http://nuget.org/packages/WindowsAzure.ServiceBus/
[Portail Azure]: https://portal.azure.com
[Vue d’ensemble du service Bus AMQP]: service-bus-amqp-overview.md
[Prise en charge de AMQP 1.0 Bus des services partition files d’attente et rubriques]: service-bus-partitioned-queues-and-topics-amqp-overview.md
[AMQP dans Service marché pour Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx
