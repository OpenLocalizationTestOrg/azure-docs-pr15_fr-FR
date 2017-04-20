<properties 
    pageTitle="Comment utiliser AMQP 1.0 avec l’API .NET Service Bus | Microsoft Azure" 
    description="Découvrez comment utiliser avancées Message Queuing Protodol (AMQP) 1.0 avec l’API Azure .NET Service Bus." 
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
    ms.topic="article" 
    ms.date="09/29/2016" 
    ms.author="sethm"/>

# <a name="how-to-use-amqp-10-with-the-service-bus-net-api"></a>Comment utiliser AMQP 1.0 avec l’API .NET Bus du Service

Les avancées Message Queuing Protocol (AMQP) 1.0 est un protocole de messagerie efficace, fiable au niveau du câble que vous pouvez utiliser pour générer robuste, multi-plateforme, applications de messagerie.

Prise en charge pour AMQP 1.0 dans Service marché signifie que vous pouvez utiliser file attente comprenant et publication/abonnement traitées fonctionnalités de messagerie à partir d’une plage de plateformes à l’aide d’un protocole binaire efficace. Par ailleurs, vous pouvez créer des applications composées les composants créés à l’aide d’un mélange de langues, les cadres et les systèmes d’exploitation.

Cet article explique comment utiliser les fonctionnalités de messagerie Bus des services demandée (files d’attente et rubriques de publication/abonnement) à partir d’applications .NET à l’aide de l’API .NET Bus du Service. Il existe un [article companion](service-bus-java-how-to-use-jms-api-amqp.md) qui explique comment effectuer la même à l’aide de l’API Java Message Service (JMS) standard. Vous pouvez utiliser ces deux guides ensemble pour en savoir plus sur la messagerie entre plates-formes à l’aide de AMQP 1.0.

## <a name="get-started-with-service-bus"></a>Prise en main à Service

Cet article suppose que vous disposez déjà d’un espace de noms de Service Bus contenant une file d’attente nommée « file d’attente 1 ». Si vous n’effectuez pas le cas, vous pouvez créer l’espace de noms et file d’attente à l’aide du [portail Azure][]. Pour plus d’informations sur la création des files d’attente et espaces de noms Bus des services, voir [prise en main files d’attente Bus des services](service-bus-dotnet-get-started-with-queues.md#1-create-a-namespace-using-the-Azure-portal).

## <a name="download-the-service-bus-sdk"></a>Téléchargez le Kit de développement logiciel Bus des services

Prise en charge AMQP 1.0 est disponible dans le Service Bus SDK version 2.1 ou version ultérieure. Vous pouvez télécharger les bits dernières NuGet en [http://nuget.org/packages/WindowsAzure.ServiceBus/](http://nuget.org/packages/WindowsAzure.ServiceBus/).

## <a name="code-net-applications"></a>Applications .NET code

Par défaut, la bibliothèque cliente Service Bus .NET communique avec le service Bus des services à l’aide d’un protocole SOAP dédié. Pour utiliser AMQP 1.0 au lieu de la valeur par défaut protocole requiert la configuration explicite de la chaîne de connexion de Service Bus comme décrit dans la section suivante. Différent de cette modification, code de l’application reste en fait ne change pas lorsque vous utilisez AMQP 1.0.

Dans la version actuelle, il existe quelques fonctionnalités API qui ne sont pas pris en charge lorsque vous utilisez AMQP. Ces fonctionnalités non prises en charge sont répertoriées ultérieurement dans la section [restrictions et les fonctionnalités non prises en charge](#unsupported-features-and-restrictions). Certains paramètres de configuration avancés également ont une signification différente lors de l’utilisation de AMQP. Aucune de ces paramètres sont utilisés dans cet article, mais plus de détails sont disponibles dans [vue d’ensemble du Service Bus AMQP](service-bus-amqp-dotnet.md#unsupported-features-restrictions-and-behavioral-differences).

### <a name="configure-via-appconfig"></a>Configurer via App.config

Il est recommandé que les applications utilisent le fichier de configuration App.config pour stocker les paramètres. Pour les applications de Service Bus, vous pouvez utiliser App.config pour stocker le Service Bus **ConnectionString**. Cet exemple d’application utilise également App.config pour stocker le nom du Service Bus entité qu’il utilise la messagerie.

Voici un exemple de fichier App.config :

```
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <appSettings>
        <add key="Microsoft.ServiceBus.ConnectionString"
             value="Endpoint=sb://[namespace].servicebus.windows.net;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp" />
            <add key="EntityName" value="queue1" />
    </appSettings>
</configuration>
```

### <a name="configure-the-service-bus-connection-string"></a>Configurer la chaîne de connexion Bus des services

La valeur du paramètre **Microsoft.ServiceBus.ConnectionString** est la chaîne de connexion de Service Bus servant à configurer la connexion au Service Bus. Le format est comme suit :

```
Endpoint=sb://[namespace].servicebus.windows.net;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp
```

Où `[namespace]` et `[SAS key]` obtenu à partir du [portail Azure][]. Pour plus d’informations, voir [comment utiliser des files d’attente Service Bus] [].

Lorsque vous utilisez AMQP, la chaîne de connexion est ajoutée avec `;TransportType=Amqp`, qui indique à la bibliothèque de client pour établir la connexion à Bus des services à l’aide de AMQP 1.0.

### <a name="configure-the-entity-name"></a>Configurer le nom de l’entité

Cet exemple d’application utilise le `EntityName` définition dans la section **appSettings** du fichier App.config pour configurer le nom de la file d’attente avec laquelle l’application échange des messages.

### <a name="a-simple-net-application-using-a-service-bus-queue"></a>Une application .NET simple à l’aide d’une file d’attente Bus des services

L’exemple suivant envoie et reçoit des messages vers et depuis une file d’attente Bus des services.

```
// SimpleSenderReceiver.cs
    
using System;
using System.Configuration;
using System.Threading;
using Microsoft.ServiceBus.Messaging;
    
namespace SimpleSenderReceiver
{
    class SimpleSenderReceiver
    {
        private static string connectionString;
        private static string entityName;
        private static Boolean runReceiver = true;
        private MessagingFactory factory;
        private MessageSender sender;
        private MessageReceiver receiver;
        private MessageListener messageListener;
        private Thread listenerThread;
    
        static void Main(string[] args)
        {
            try
            {
                if ((args.Length > 0) && args[0].ToLower().Equals("sendonly"))
                    runReceiver = false;
                    
                string ConnectionStringKey = "Microsoft.ServiceBus.ConnectionString";
                string entityNameKey = "EntityName";
                entityName = ConfigurationManager.AppSettings[entityNameKey];
                connectionString = ConfigurationManager.AppSettings[ConnectionStringKey];
                SimpleSenderReceiver simpleSenderReceiver = new SimpleSenderReceiver();
    
                Console.WriteLine("Press [enter] to send a message. " +
                    "Type 'exit' + [enter] to quit.");
    
                while (true)
                {
                    string s = Console.ReadLine();
                    if (s.ToLower().Equals("exit"))
                    {
                        simpleSenderReceiver.Close();
                        System.Environment.Exit(0);
                    }
                    else
                        simpleSenderReceiver.SendMessage();
                }
            }
            catch (Exception e)
            {
                Console.WriteLine("Caught exception: " + e.Message);
            }
        }
    
        public SimpleSenderReceiver()
        {
            factory = MessagingFactory.CreateFromConnectionString(connectionString);
            sender = factory.CreateMessageSender(entityName);
    
            if (runReceiver)
            {
                receiver = factory.CreateMessageReceiver(entityName);
                messageListener = new MessageListener(receiver);
                listenerThread = new Thread(messageListener.Listen);
                listenerThread.Start();
            }
        }
    
        public void Close()
        {
            messageListener.RequestStop();
            listenerThread.Join();
            factory.Close();
        }
    
        private void SendMessage()
        {
            BrokeredMessage message = new BrokeredMessage("Test AMQP message from .NET");
            sender.Send(message);
            Console.WriteLine("Sent message with MessageID = " 
                + message.MessageId);
        }

    }
    
    public class MessageListener
    {
        private MessageReceiver messageReceiver;
        public MessageListener(MessageReceiver receiver)
        {
            messageReceiver = receiver;
        }
    
        public void Listen()
        {
            while (!_shouldStop)
            {
                try
                {
                    BrokeredMessage message = 
                        messageReceiver.Receive(new TimeSpan(0, 0, 10));
                    if (message != null)
                    {
                        Console.WriteLine("Received message with MessageID = " + 
                            message.MessageId);
                        message.Complete();
                    }
                }
                catch (Exception e)
                {
                    Console.WriteLine("Caught exception: " + e.Message);
                    break;
                }
            }
        }
    
        public void RequestStop()
        {
            _shouldStop = true;
        }
    
        private volatile bool _shouldStop;
    }
}
```

### <a name="run-the-application"></a>Exécuter l’application

Exécution de l’application produit une sortie de l’écran :

```
> SimpleSenderReceiver.exe
Press [enter] to send a message. Type 'exit' + [enter] to quit.
    
Sent message with MessageID = fb7f5d3733704e4ba4bd55f759d9d7cf
Received message with MessageID = fb7f5d3733704e4ba4bd55f759d9d7cf
    
Sent message with MessageID = d00e2e088f06416da7956b58310f7a06
Received message with MessageID = d00e2e088f06416da7956b58310f7a06
    
Received message with MessageID = f27f79ec124548c196fd0db8544bca49
Sent message with MessageID = f27f79ec124548c196fd0db8544bca49
exit
```

## <a name="cross-platform-messaging-between-jms-and-net"></a>Disponibilité sur plusieurs plateformes messagerie entre JMS et .NET

Cette rubrique a montré comment envoyer des messages à Bus des services à l’aide de .NET et également à recevoir les messages à l’aide de .NET. Toutefois, un des principaux avantages de AMQP 1.0 est qu’il permet aux applications basées sur des composants écrits dans d’autres langues, avec les messages échangés fiable et fidélité complète.

À l’aide de l’exemple d’application .NET ci-dessus et une application Java similaire du guide de [l’utilisation de l’API avec Service di & AMQP 1.0 Java Message Service (JMS)](service-bus-java-how-to-use-jms-api-amqp.md), il est possible d’échanger des messages entre .NET et Java. 

Pour plus d’informations sur les détails de messagerie à l’aide du Service Bus et AMQP 1.0 entre plates-formes, consultez la [vue d’ensemble du Service Bus AMQP 1.0](service-bus-amqp-overview.md).

### <a name="jms-to-net"></a>JMS pour .NET

Pour illustrer JMS à la messagerie .NET :

* Démarrer l’application .NET sans des arguments de ligne de commande.
* Démarrer l’application Java avec l’argument de ligne de commande « sendonly ». Dans ce mode, l’application ne reçoit pas de messages à partir de la file d’attente, il envoie uniquement.
* Appuyez plusieurs fois sur **entrée** dans la console d’application Java, qui va entraîner l’envoi de messages.
* Ces messages sont reçus par l’application .NET.

### <a name="output-from-jms-application"></a>Sortie d’application JMS

```
> java SimpleSenderReceiver sendonly
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Sent message with JMSMessageID = ID:4364096528752411591
Sent message with JMSMessageID = ID:459252991689389983
Sent message with JMSMessageID = ID:1565011046230456854
exit
```

### <a name="output-from-net-application"></a>Sortie d’application .NET

```
> SimpleSenderReceiver.exe  
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Received message with MessageID = 4364096528752411591
Received message with MessageID = 459252991689389983
Received message with MessageID = 1565011046230456854
exit
```

## <a name="net-to-jms"></a>.NET à JMS

Pour illustrer .NET à la messagerie JMS :

* Démarrer l’application .NET avec l’argument de ligne de commande « sendonly ». Dans ce mode, l’application ne reçoit pas de messages à partir de la file d’attente, il envoie uniquement.
* Démarrer l’application Java sans des arguments de ligne de commande.
* Appuyez plusieurs fois sur **entrée** dans la console d’application .NET, qui va entraîner l’envoi de messages.
* Ces messages sont reçus par l’application Java.

#### <a name="output-from-net-application"></a>Sortie d’application .NET

```
> SimpleSenderReceiver.exe sendonly
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Sent message with MessageID = d64e681a310a48a1ae0ce7b017bf1cf3  
Sent message with MessageID = 98a39664995b4f74b32e2a0ecccc46bb
Sent message with MessageID = acbca67f03c346de9b7893026f97ddeb
exit
```

#### <a name="output-from-jms-application"></a>Sortie d’application JMS

```
> java SimpleSenderReceiver 
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Received message with JMSMessageID = ID:d64e681a310a48a1ae0ce7b017bf1cf3
Received message with JMSMessageID = ID:98a39664995b4f74b32e2a0ecccc46bb
Received message with JMSMessageID = ID:acbca67f03c346de9b7893026f97ddeb
exit
```

## <a name="unsupported-features-and-restrictions"></a>Restrictions et les fonctionnalités non prises en charge

Les fonctionnalités suivantes de l’API .NET Service Bus ne sont pas actuellement pris en charge lorsque vous utilisez AMQP :

 * Transactions
 * Envoyer par la destination du transfert

Pour plus d’informations, voir [fonctionnalités non prises en charge, les restrictions et des différences de comportement](service-bus-amqp-dotnet.md#unsupported-features-restrictions-and-behavioral-differences).

## <a name="summary"></a>Résumé

Cet article a montré comment accéder aux fonctionnalités de messagerie Bus des services demandée (files d’attente et rubriques de publication/abonnement) à partir de .NET à l’aide de AMQP 1.0 et l’API .NET Bus du Service.

Vous pouvez également utiliser le Service Bus AMQP 1.0 à partir d’autres langues, y compris Java, C, Python et PHP. Les composants créés à l’aide de ces langues peuvent échanger des messages fiable et fidélité complète à l’aide de AMQP 1.0 dans Service marché. Pour plus d’informations, consultez la [vue d’ensemble du Service Bus AMQP](service-bus-amqp-dotnet.md).

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez lu une vue d’ensemble du Service Bus et AMQP avec .NET, consultez les liens suivants pour plus d’informations.

* [Prise en charge de AMQP 1.0 dans Azure Service marché](service-bus-amqp-overview.md)
* [Comment utiliser l’API Java Message Service (JMS) avec Service di & AMQP 1.0](service-bus-java-how-to-use-jms-api-amqp.md)
* [Comment utiliser les files d’attente Bus des services](service-bus-dotnet-get-started-with-queues.md)
 
[Portail Azure]: https://portal.azure.com
