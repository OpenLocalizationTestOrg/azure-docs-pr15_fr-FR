<properties 
    pageTitle="Comment utiliser AMQP 1.0 avec l’API Java Service Bus | Microsoft Azure" 
    description="Comment utiliser le Service de Message Java (JMS) avec Bus des services Azure et avancées Message Queuing Protodol (AMQP) 1.0." 
    services="service-bus" 
    documentationCenter="java" 
    authors="sethmanheim" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="service-bus" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="10/04/2016" 
    ms.author="sethm"/>

# <a name="how-to-use-the-java-message-service-jms-api-with-service-bus-and-amqp-10"></a>L’utilisation de l’API Java Message Service (JMS) avec Bus des services et AMQP 1.0

L’avancées Message Queuing Protocol (AMQP) 1.0 est un protocole de messagerie efficace, fiable au niveau du câble que vous pouvez utiliser pour créer des applications de messagerie robustes, disponibilité sur plusieurs plateformes.

Prise en charge pour AMQP 1.0 dans Service marché signifie que vous pouvez utiliser file attente comprenant et publication/abonnement traitées fonctionnalités de messagerie à partir d’une plage de plateformes à l’aide d’un protocole binaire efficace. Par ailleurs, vous pouvez créer des applications composées les composants créés à l’aide d’un mélange de langues, les cadres et les systèmes d’exploitation.

Cet article explique comment utiliser le Service Bus fonctionnalités (files d’attente et rubriques de publication/abonnement) de messagerie à partir d’applications Java à l’aide de la populaires Java Message Service (JMS) API standard. Il existe un [article companion](service-bus-dotnet-advanced-message-queuing.md) qui explique comment faire la même chose à l’aide de l’API .NET Bus du Service. Vous pouvez utiliser ces deux guides ensemble pour en savoir plus sur la messagerie entre plates-formes à l’aide de AMQP 1.0.

## <a name="get-started-with-service-bus"></a>Prise en main à Service

Ce guide suppose que vous disposez déjà d’un espace de noms de Service Bus contenant une file d’attente nommée **file d’attente 1**. Si vous n’effectuez pas le cas, vous pouvez [créer l’espace de noms et file d’attente](service-bus-create-namespace-portal.md) à l’aide du [portail Azure](https://portal.azure.com). Pour plus d’informations sur la création des files d’attente et espaces de noms Bus des services, Découvrez [comment files d’attente de Bus des services](service-bus-dotnet-get-started-with-queues.md).

> [AZURE.NOTE] Rubriques et files d’attente partitionnées prennent également en charge AMQP. Pour plus d’informations, voir [partitionnées messagerie entités](service-bus-partitioning.md) et [prise en charge de AMQP 1.0 Bus des services partition files d’attente et les rubriques](service-bus-partitioned-queues-and-topics-amqp-overview.md).

## <a name="downloading-the-amqp-10-jms-client-library"></a>Le téléchargement de la bibliothèque de client AMQP 1.0 JMS

Pour plus d’informations sur l’emplacement où télécharger la version la plus récente de la bibliothèque de client Apache Qpid JMS AMQP 1.0, visitez [https://qpid.apache.org/download.html](https://qpid.apache.org/download.html).

Vous devez ajouter les quatre fichiers JAR suivants à partir de l’archive de distribution Apache Qpid JMS AMQP 1.0 au chemin de classe Java lors de la création et l’exécution des applications JMS à Service :

- geronimo jms\_1.1\_spécifications 1.0.jar
- qpid-amqp-1-0-client-[version].jar
- qpid-amqp-1-0-client-JMS-[version].jar
- qpid-amqp-1-0-Common-[version].jar

## <a name="coding-java-applications"></a>Codage d’applications Java

### <a name="java-naming-and-directory-interface-jndi"></a>Affectation des noms Java et Interface d’annuaire (JNDI)

JMS utilise la façon de nommer Java et le répertoire Interface JNDI () pour créer une séparation entre les noms logiques et physiques. Deux types d’objets JMS sont résolus à l’aide de JNDI : ConnectionFactory et Destination. JNDI utilise un modèle de fournisseur dans lequel vous pouvez connecter les différents services d’annuaire pour gérer les droits de résolution de nom. Mettre en forme la 1.0 AMQP Apache Qpid JMS bibliothèque est fourni avec les propriétés d’un simple fournisseur JNDI basée sur le fichier qui est configuré à l’aide d’un fichier de propriétés des éléments suivants :

```
# servicebus.properties - sample JNDI configuration
    
# Register a ConnectionFactory in JNDI using the form:
# connectionfactory.[jndi_name] = [ConnectionURL]
connectionfactory.SBCF = amqps://[SASPolicyName]:[SASPolicyKey]@[namespace].servicebus.windows.net
    
# Register some queues in JNDI using the form
# queue.[jndi_name] = [physical_name]
# topic.[jndi_name] = [physical_name]
queue.QUEUE = queue1
```

#### <a name="configure-the-connectionfactory"></a>Configurer la ConnectionFactory

L’entrée permettant de définir un **ConnectionFactory** dans le fournisseur de JNDI Qpid propriétés de fichier est le format suivant :

```
connectionfactory.[jndi_name] = [ConnectionURL]
```

Où **[jndi_name]** et **[ConnectionURL]** ont la signification suivante :

- **[jndi_name]**: le nom de la ConnectionFactory logique. C’est le nom qui sera résolu dans l’application Java à l’aide de la méthode IntialContext.lookup() JNDI.
- **[ConnectionURL]**: une URL qui fournit la bibliothèque JMS avec les informations requises pour l’intermédiaire AMQP.

Le format de la **ConnectionURL** est la suivante :

```
amqps://[SASPolicyName]:[SASPolicyKey]@[namespace].servicebus.windows.net
```
Où **[espace de noms]**, **[SASPolicyName]** et **[SASPolicyKey]** ont la signification suivante :

- **[espace de noms]**: espace de noms de Service Bus.
- **[SASPolicyName]**: nom de la stratégie la Signature de Access partagés file d’attente.
- **[SASPolicyKey]**: clé de stratégie de la Signature de Access partagés file d’attente.

> [AZURE.NOTE] Vous devez coder URL le mot de passe manuellement. Un utilitaire de codage URL utile est disponible en [http://www.w3schools.com/tags/ref_urlencode.asp](http://www.w3schools.com/tags/ref_urlencode.asp).

#### <a name="configure-destinations"></a>Configurer les destinations

L’entrée utilisée pour définir une destination dans le fournisseur de JNDI Qpid propriétés de fichier est au format suivant :

```
queue.[jndi_name] = [physical_name]
```

ou

```
topic.[jndi_name] = [physical_name]
```

Où **[jndi\_nom]** et **[physique\_nom]** ont la signification suivante :

- **[jndi_name]**: le nom de la destination logique. C’est le nom qui sera résolu dans l’application Java à l’aide de la méthode IntialContext.lookup() JNDI.
- **[nom_physique]**: le nom de l’entité Bus de Service dans lequel l’application envoie ou reçoit des messages.

> [AZURE.NOTE] Lors de la réception d’un abonnement rubrique Bus des services, le nom physique spécifié dans JNDI doit être le nom de la rubrique. Le nom de l’abonnement est fourni lorsque l’abonnement résistant est créé dans le code d’application JMS. Le [Guide du développeur 1.0 Service Bus AMQP](service-bus-amqp-dotnet.md) fournit davantage de détails sur l’utilisation de l’abonnement à une rubrique Bus des services de JMS.

### <a name="write-the-jms-application"></a>Écrire l’application JMS

Il n’existe aucune API ou options requises lorsque vous utilisez JMS à Service spécial. Il existe cependant certaines restrictions à prendre en compte ultérieurement. Comme avec n’importe quelle application JMS, la première chose que nécessaire est la configuration de l’environnement JNDI, pour être en mesure de résoudre un **ConnectionFactory** et les destinations.

#### <a name="configure-the-jndi-initialcontext"></a>Configurer la InitialContext JNDI

L’environnement JNDI en passant d’une table de hachage des informations de configuration dans le constructeur de la classe javax.naming.InitialContext. Les deux requis éléments dans la table de hachage sont le nom du cours de l’usine de contexte Initial et l’URL du fournisseur. Le code suivant montre comment configurer l’environnement JNDI pour utiliser le fichier de propriétés Qpid basée JNDI fournisseur avec un fichier de propriétés nommé **servicebus.properties**.

```
Hashtable<String, String> env = new Hashtable<String, String>(); 
env.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory"); 
env.put(Context.PROVIDER_URL, "servicebus.properties"); 
InitialContext context = new InitialContext(env);
``` 

### <a name="a-simple-jms-application-using-a-service-bus-queue"></a>Une application JMS simple à l’aide d’une file d’attente Bus des services

L’exemple de programme suivant envoie JMS TextMessages à une file d’attente de Bus des services avec le nom logique JNDI de file d’attente et reçoit le message précédent.

```
// SimpleSenderReceiver.java
    
import javax.jms.*;
import javax.naming.Context;
import javax.naming.InitialContext;
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.util.Hashtable;
import java.util.Random;
    
public class SimpleSenderReceiver implements MessageListener {
    private static boolean runReceiver = true;
    private Connection connection;
    private Session sendSession;
    private Session receiveSession;
    private MessageProducer sender;
    private MessageConsumer receiver;
    private static Random randomGenerator = new Random();
    
    public SimpleSenderReceiver() throws Exception {
        // Configure JNDI environment
        Hashtable<String, String> env = new Hashtable<String, String>();
        env.put(Context.INITIAL_CONTEXT_FACTORY, 
                   "org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory");
        env.put(Context.PROVIDER_URL, "servicebus.properties");
        Context context = new InitialContext(env);
    
        // Look up ConnectionFactory and Queue
        ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
        Destination queue = (Destination) context.lookup("QUEUE");
    
        // Create Connection
        connection = cf.createConnection();
    
        // Create sender-side Session and MessageProducer
        sendSession = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);
        sender = sendSession.createProducer(queue);
    
        if (runReceiver) {
            // Create receiver-side Session, MessageConsumer,and MessageListener
            receiveSession = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);
            receiver = receiveSession.createConsumer(queue);
            receiver.setMessageListener(this);
            connection.start();
        }
    }
    
    public static void main(String[] args) {
        try {
    
            if ((args.length > 0) && args[0].equalsIgnoreCase("sendonly")) {
                runReceiver = false;
            }
    
            SimpleSenderReceiver simpleSenderReceiver = new SimpleSenderReceiver();
            System.out.println("Press [enter] to send a message. Type 'exit' + [enter] to quit.");
            BufferedReader commandLine = new java.io.BufferedReader(new InputStreamReader(System.in));
    
            while (true) {
                String s = commandLine.readLine();
                if (s.equalsIgnoreCase("exit")) {
                    simpleSenderReceiver.close();
                    System.exit(0);
                } else {
                    simpleSenderReceiver.sendMessage();
                }
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
    
    private void sendMessage() throws JMSException {
        TextMessage message = sendSession.createTextMessage();
        message.setText("Test AMQP message from JMS");
        long randomMessageID = randomGenerator.nextLong() >>>1;
        message.setJMSMessageID("ID:" + randomMessageID);
        sender.send(message);
        System.out.println("Sent message with JMSMessageID = " + message.getJMSMessageID());
    }
    
    public void close() throws JMSException {
        connection.close();
    }
    
    public void onMessage(Message message) {
        try {
            System.out.println("Received message with JMSMessageID = " + message.getJMSMessageID());
            message.acknowledge();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}   
```

### <a name="run-the-application"></a>Exécuter l’application

Exécution de l’application produit une sortie de l’écran :

```
> java SimpleSenderReceiver
Press [enter] to send a message. Type 'exit' + [enter] to quit.
    
Sent message with JMSMessageID = ID:2867600614942270318
Received message with JMSMessageID = ID:2867600614942270318
    
Sent message with JMSMessageID = ID:7578408152750301483
Received message with JMSMessageID = ID:7578408152750301483
    
Sent message with JMSMessageID = ID:956102171969368961
Received message with JMSMessageID = ID:956102171969368961
exit
```

## <a name="cross-platform-messaging-between-jms-and-net"></a>Disponibilité sur plusieurs plateformes messagerie entre JMS et .NET

Ce guide a montré comment envoyer et recevoir des messages vers et depuis Bus des services à l’aide de JMS. Toutefois, un des principaux avantages de AMQP 1.0 est qu’il permet aux applications basées sur des composants écrits dans d’autres langues, avec les messages échangés fiable et fidélité complète.

À l’aide de l’application de JMS exemple ci-dessus et une application .NET similaire du guide de [l’utilisation de AMQP 1.0 avec l’API .NET Service Bus .NET](service-bus-dotnet-advanced-message-queuing.md), vous pouvez échanger des messages entre .NET et Java. 

Pour plus d’informations sur les détails de messagerie à l’aide du Service Bus et AMQP 1.0 entre plates-formes, consultez le [Guide du développeur 1.0 Service Bus AMQP](service-bus-amqp-dotnet.md).

### <a name="jms-to-net"></a>JMS pour .NET

Pour illustrer JMS à la messagerie .NET :

* Démarrer l’application .NET sans des arguments de ligne de commande.
* Démarrer l’application Java avec l’argument de ligne de commande « sendonly ». Dans ce mode, l’application ne reçoit pas de messages à partir de la file d’attente, il envoie uniquement.
* Appuyez plusieurs fois sur **entrée** dans la console d’application Java, qui va entraîner l’envoi de messages.
* Ces messages sont reçus par l’application .NET.

#### <a name="output-from-jms-application"></a>Sortie d’application JMS

```
> java SimpleSenderReceiver sendonly
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Sent message with JMSMessageID = ID:4364096528752411591
Sent message with JMSMessageID = ID:459252991689389983
Sent message with JMSMessageID = ID:1565011046230456854
exit
```

#### <a name="output-from-net-application"></a>Sortie d’application .NET

```
> SimpleSenderReceiver.exe  
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Received message with MessageID = 4364096528752411591
Received message with MessageID = 459252991689389983
Received message with MessageID = 1565011046230456854
exit
```

### <a name="net-to-jms"></a>.NET à JMS

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

Les restrictions suivantes s’appliquent lorsque vous utilisez JMS sur AMQP 1.0 à Service, à savoir :

* Seule **MessageProducer** ou **MessageConsumer** est autorisé par **Session**. Si vous avez besoin créer plusieurs **MessageProducers** ou **MessageConsumers** dans une application, créer une **Session** dédié pour chacun d’eux.
* Abonnement à une rubrique volatiles n’est actuellement pas pris en charge.
* **MessageSelectors** ne sont pas actuellement pris en charge.
* Destinations temporaires, par exemple, **TemporaryQueue**, **TemporaryTopic** ne sont pas pris en charge, ainsi que le **QueueRequestor** et **TopicRequestor** API qui les utilisent.
* Sessions traitées et les transactions distribuées ne sont pas pris en charge.

## <a name="summary"></a>Résumé

Cette procédure a montré comment utiliser Bus des services dans fonctionnalités de messagerie (files d’attente et rubriques de publication/abonnement) à partir de Java à l’aide de l’API JMS populaires et AMQP 1.0.

Vous pouvez également utiliser le Service Bus AMQP 1.0 à partir d’autres langues, y compris .NET, C, Python et PHP. Échanger des messages fiable et au fidélité complète à l’aide de la version 1.0 AMQP prennent en charge dans Service marché composants créés à l’aide de ces différentes langues. Pour plus d’informations, voir le [Guide du développeur 1.0 Service Bus AMQP](service-bus-amqp-dotnet.md).

## <a name="next-steps"></a>Étapes suivantes

* [Prise en charge de AMQP 1.0 dans Azure Service marché](service-bus-amqp-overview.md)
* [Comment utiliser AMQP 1.0 avec l’API .NET Bus du Service](service-bus-dotnet-advanced-message-queuing.md)
* [Service Bus AMQP 1.0 Guide du développeur](service-bus-amqp-dotnet.md)
* [Comment utiliser les files d’attente Bus des services](service-bus-dotnet-get-started-with-queues.md)
* [Centre de développement Java](/develop/java/).



 
