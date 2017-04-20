<properties
    pageTitle="Azure IoT concentrateur pour Java prise en main | Microsoft Azure"
    description="Concentrateur IoT Azure avec Java prise en main didacticiel. Utilisez Azure IoT concentrateur et Java avec les kits de développement Microsoft Azure IoT à mettre en œuvre une solution Internet des objets."
    services="iot-hub"
    documentationCenter="java"
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="java"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/11/2016"
     ms.author="dobett"/>

# <a name="get-started-with-azure-iot-hub-for-java"></a>Prise en main Azure IoT concentrateur pour Java

[AZURE.INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

À la fin de ce didacticiel, vous avez trois applications console Java :

* **identité appareil créer**, ce qui crée une identité de l’appareil et une clé de sécurité associées pour connecter votre périphérique simulé.
* **messages lus-d2c**, qui affiche la télémétrie envoyé par votre périphérique simulé.
* **appareil simulé**, qui se connecte à votre concentrateur IoT à l’identité de l’appareil créée précédemment et envoie un message de télémétrie chaque deuxième à l’aide du protocole AMQP.

> [AZURE.NOTE] L’article [IoT concentrateur SDK] [ lnk-hub-sdks] fournit des informations sur les différents kits de développement que vous pouvez utiliser pour créer les deux applications à s’exécuter sur des appareils mobiles et votre solution back end.

Pour effectuer ce didacticiel, vous devez les éléments suivants :

+ Java SE 8. <br/> [Préparer votre environnement de développement] [ lnk-dev-setup] décrit comment installer Java pour ce didacticiel sur Windows ou Linux.

+ Maven 3.  <br/> [Préparer votre environnement de développement] [ lnk-dev-setup] décrit comment installer Maven pour ce didacticiel sur Windows ou Linux.

+ Un compte Azure actif. (Si vous n’avez pas un compte, vous pouvez créer un [compte gratuit] [ lnk-free-trial] en quelques minutes.)

[AZURE.INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

En tant que dernière étape, prenez note de la valeur de **clé primaire** , puis cliquez sur **messagerie**. Sur la carte de **messagerie** , prenez note de l' **événement concentrateur compatible avec le nom** et l' **événement concentrateur compatible avec le point de terminaison**. Vous avez besoin de ces trois valeurs lorsque vous créez votre application **d2c-lire les messages** .

![Carte de la messagerie concentrateur IoT portail Azure][6]

Vous venez de créer votre concentrateur IoT, et vous la IoT concentrateur hostname, chaîne de connexion IoT concentrateur, IoT concentrateur de clé primaire, nom événement concentrateur compatible et point de terminaison compatible concentrateur événement que vous devez effectuer ce didacticiel.

## <a name="create-a-device-identity"></a>Création d’une identité de l’appareil

Dans cette section, vous créez une application console Java qui crée une nouvelle identité de périphérique dans le Registre identité dans votre hub IoT. Un périphérique ne peut pas se connecter à IoT concentrateur, à moins qu’une entrée dans le Registre identité appareil. Reportez-vous à la section **Périphérique identité Registre** du [Guide du développeur IoT concentrateur] [ lnk-devguide-identity] pour plus d’informations. Lorsque vous exécutez cette application console, il génère un ID d’appareil unique et une clé que votre appareil peut utiliser pour identifier lorsqu’il envoie des messages de l’appareil-nuage à IoT concentrateur.

1. Créer un nouveau dossier vide nommé iot java-Démarrer. Dans le dossier iot java-Démarrer, créez un nouveau projet Maven appelé **identité appareil créer** à l’aide de la commande suivante à votre invite de commandes. Remarque : il s’agit d’une commande unique, longue :

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=create-device-identity -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. À votre invite de commandes, accédez au dossier identité appareil créer nouveau.

3. À l’aide d’un éditeur de texte, ouvrez le fichier pom.xml dans le dossier identité créer de l’appareil et ajoutez les dépendances suivantes pour le nœud **dépendances** . Cela vous permet d’utiliser le package SDK du service iothub de votre application :

    ```
    <dependency>
      <groupId>com.microsoft.azure.iothub-java-client</groupId>
      <artifactId>iothub-java-service-client</artifactId>
      <version>1.0.9</version>
    </dependency>
    ```
    
4. Enregistrez et fermez le fichier pom.xml.

5. À l’aide d’un éditeur de texte, ouvrez le fichier create-device-identity\src\main\java\com\mycompany\app\App.java.

6. Ajoutez les instructions suivantes **Importer** le fichier :

    ```
    import com.microsoft.azure.iot.service.exceptions.IotHubException;
    import com.microsoft.azure.iot.service.sdk.Device;
    import com.microsoft.azure.iot.service.sdk.RegistryManager;

    import java.io.IOException;
    import java.net.URISyntaxException;
    ```

7. Ajoutez les variables de niveau classe suivantes à la classe **application** , remplaçant **{yourhubconnectionstring}** par la valeur votre indiqué précédemment :

    ```
    private static final String connectionString = "{yourhubconnectionstring}";
    private static final String deviceId = "myFirstJavaDevice";
    
    ```
    
8. Modifier la signature de la méthode **principale** pour inclure les exceptions comme suit :

    ```
    public static void main( String[] args ) throws IOException, URISyntaxException, Exception
    ```
    
9. Ajoutez le code suivant dans le corps de la méthode **principale** . Ce code crée une unité appelée *javadevice* dans le Registre identité IoT concentrateur si n’existe pas déjà. Il affiche ensuite l’id de périphérique et clé dont vous avez besoin plus tard :

    ```
    RegistryManager registryManager = RegistryManager.createFromConnectionString(connectionString);

    Device device = Device.createFromId(deviceId, null, null);
    try {
      device = registryManager.addDevice(device);
    } catch (IotHubException iote) {
      try {
        device = registryManager.getDevice(deviceId);
      } catch (IotHubException iotf) {
        iotf.printStackTrace();
      }
    }
    System.out.println("Device id: " + device.getDeviceId());
    System.out.println("Device key: " + device.getPrimaryKey());
    ```

10. Enregistrez et fermez le fichier App.java.

11. Pour créer l’application **d’identité de l’appareil créer** en utilisant Maven, exécutez la commande suivante à l’invite de commandes dans le dossier identité créer de l’appareil :

    ```
    mvn clean package -DskipTests
    ```

12. Pour exécuter l’application **d’identité de l’appareil créer** en utilisant Maven, exécutez la commande suivante à l’invite de commandes dans le dossier identité créer de l’appareil :

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

13. Prenez note de **l’id de périphérique** et la **clé du périphérique**. Vous aurez besoin ultérieurement lorsque vous créez une application qui se connecte à concentrateur IoT en tant que périphérique.

> [AZURE.NOTE] Le Registre d’identité IoT concentrateur stocke uniquement des identités des appareils pour permettre un accès sécurisé au concentrateur. Il stocke les ID de l’équipement et clés à utiliser en tant que les informations d’identification de sécurité et un indicateur activé ou désactivé que vous pouvez utiliser pour désactiver l’accès pour un périphérique individuel. Si votre application a besoin stocker les autres métadonnées spécifiques à l’appareil, elle doit utiliser un magasin spécifique à l’application. Reportez-vous au [Guide de développeur IoT concentrateur] [ lnk-devguide-identity] pour plus d’informations.

## <a name="receive-device-to-cloud-messages"></a>Recevoir des messages de l’appareil-nuage

Dans cette section, vous créez une application console Java qui lit les messages de l’appareil-nuage de IoT concentrateur. Un concentrateur IoT expose un [Événement concentrateur][lnk-event-hubs-overview]-point de terminaison compatible pour pouvoir lire les messages de l’appareil-nuage. Pour simplifier les choses, ce didacticiel crée un lecteur de base qui n’est pas adapté à un déploiement haut débit. Le [traitement des messages appareil-nuage] [ lnk-process-d2c-tutorial] didacticiel vous montre comment traiter les messages appareil-nuage à l’échelle. La [Prise en main événement Hubs] [ lnk-eventhubs-tutorial] didacticiel fournit des informations plus sur la façon de traiter les messages à partir de l’événement Hubs et ne s’applique aux points de terminaison compatibles IoT concentrateur événement concentrateur.

> [AZURE.NOTE] Le point de terminaison compatible événement concentrateur pour la lecture des messages de l’appareil-nuage toujours utilise le protocole AMQP.

1. Dans le dossier iot java-démarrer que vous avez créé dans la section *Création d’une identité de l’appareil* , créez un nouveau projet Maven appelé **d2c-lire les messages** à l’aide de la commande suivante à votre invite de commandes. Remarque : il s’agit d’une commande unique, longue :

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=read-d2c-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. À votre invite de commandes, naviguez vers le nouveau dossier d2c-lire les messages.

3. À l’aide d’un éditeur de texte, ouvrez le fichier pom.xml dans le dossier en lecture-d2c-messages et ajoutez les dépendances suivantes pour le nœud **dépendances** . Cela vous permet de vous permet d’utiliser le package eventhubs client dans votre application de lire le point de terminaison compatible concentrateur événement :

    ```
    <dependency> 
        <groupId>com.microsoft.azure</groupId> 
        <artifactId>azure-eventhubs</artifactId> 
        <version>0.8.2</version> 
    </dependency>
    ```

4. Enregistrez et fermez le fichier pom.xml.

5. À l’aide d’un éditeur de texte, ouvrez le fichier read-d2c-messages\src\main\java\com\mycompany\app\App.java.

6. Ajoutez les instructions suivantes **Importer** le fichier :

    ```
    import java.io.IOException;
    import com.microsoft.azure.eventhubs.*;
    import com.microsoft.azure.servicebus.*;
    
    import java.io.IOException;
    import java.nio.charset.Charset;
    import java.time.*;
    import java.util.Collection;
    import java.util.concurrent.ExecutionException;
    import java.util.function.*;
    import java.util.logging.*;
    ```

7. Ajoutez les variables de niveau classe suivantes à la classe **d’application** . Remplacez **{youriothubkey}**, **{youreventhubcompatibleendpoint}**et **{youreventhubcompatiblename}** par les valeurs que vous avez noté précédemment :

    ```
    private static String connStr = "Endpoint={youreventhubcompatibleendpoint};EntityPath={youreventhubcompatiblename};SharedAccessKeyName=iothubowner;SharedAccessKey={youriothubkey}";
    ```

8. Ajoutez la méthode **receiveMessages** suivante à la classe **d’application** . Cette méthode crée une instance **EventHubClient** pour vous connecter au point de terminaison compatible événement concentrateur, puis asynchrone crée une instance **PartitionReceiver** lire à partir d’une partition d’événement concentrateur. Il en continu et permet d’imprimer les détails du message jusqu'à ce que l’application est fermée.

    ```
    private static EventHubClient receiveMessages(final String partitionId)
    {
      EventHubClient client = null;
      try {
        client = EventHubClient.createFromConnectionStringSync(connStr);
      }
      catch(Exception e) {
        System.out.println("Failed to create client: " + e.getMessage());
        System.exit(1);
      }
      try {
        client.createReceiver( 
          EventHubClient.DEFAULT_CONSUMER_GROUP_NAME,  
          partitionId,  
          Instant.now()).thenAccept(new Consumer<PartitionReceiver>()
        {
          public void accept(PartitionReceiver receiver)
          {
            System.out.println("** Created receiver on partition " + partitionId);
            try {
              while (true) {
                Iterable<EventData> receivedEvents = receiver.receive(100).get();
                int batchSize = 0;
                if (receivedEvents != null)
                {
                  for(EventData receivedEvent: receivedEvents)
                  {
                    System.out.println(String.format("Offset: %s, SeqNo: %s, EnqueueTime: %s", 
                      receivedEvent.getSystemProperties().getOffset(), 
                      receivedEvent.getSystemProperties().getSequenceNumber(), 
                      receivedEvent.getSystemProperties().getEnqueuedTime()));
                    System.out.println(String.format("| Device ID: %s", receivedEvent.getProperties().get("iothub-connection-device-id")));
                    System.out.println(String.format("| Message Payload: %s", new String(receivedEvent.getBody(),
                      Charset.defaultCharset())));
                    batchSize++;
                  }
                }
                System.out.println(String.format("Partition: %s, ReceivedBatch Size: %s", partitionId,batchSize));
              }
            }
            catch (Exception e)
            {
              System.out.println("Failed to receive messages: " + e.getMessage());
            }
          }
        });
      }
      catch (Exception e)
      {
        System.out.println("Failed to create receiver: " + e.getMessage());
      }
      return client;
    }
    ```

    > [AZURE.NOTE] Cette méthode utilise un filtre lorsqu’il crée le destinataire afin que le destinataire lit uniquement les messages envoyés à concentrateur IoT après que le récepteur démarre en cours d’exécution. Ceci est utile dans un environnement de test afin que vous puissiez voir l’ensemble actuel des messages. Dans un environnement de production votre code devez vous assurer qu’il traite tous les messages - voir [comment traiter les messages d’appareil-nuage IoT concentrateur] [ lnk-process-d2c-tutorial] didacticiels pour plus d’informations.

9. Modifier la signature de la méthode **principale** pour inclure l’exception comme suit :

    ```
    public static void main( String[] args ) throws IOException
    ```

10. Ajoutez le code suivant à la méthode de la classe **application** **principale** . Ce code crée les deux instances **EventHubClient** et **PartitionReceiver** et vous permet de fermer l’application lorsque vous avez terminé le traitement des messages :

    ```
    EventHubClient client0 = receiveMessages("0");
    EventHubClient client1 = receiveMessages("1");
    System.out.println("Press ENTER to exit.");
    System.in.read();
    try
    {
      client0.closeSync();
      client1.closeSync();
      System.exit(0);
    }
    catch (ServiceBusException sbe)
    {
      System.exit(1);
    }
    ```

    > [AZURE.NOTE] Ce code suppose que vous avez créé votre concentrateur IoT dans le niveau (gratuit) F1. Un concentrateur IoT gratuit comporte deux partitions nommées « 0 » et « 1 ».

11. Enregistrez et fermez le fichier App.java.

12. Pour créer l’application **d2c-lire les messages** à l’aide de Maven, exécutez la commande suivante à l’invite de commandes dans le dossier en lecture-d2c-messages :

    ```
    mvn clean package -DskipTests
    ```

## <a name="create-a-simulated-device-app"></a>Créer une application de périphérique simulé

Dans cette section, vous créez une application console Java qui simule un appareil qui envoie des messages de l’appareil-nuage à un concentrateur IoT.

1. Dans le dossier iot java-démarrer que vous avez créé dans la section *Création d’une identité de l’appareil* , créez un projet Maven appelé **périphérique simulé** à l’aide de la commande suivante à votre invite de commandes. Remarque : il s’agit d’une commande unique, longue :

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. À votre invite de commandes, accédez au dossier appareil simulé à nouveau.

3. À l’aide d’un éditeur de texte, ouvrez le fichier pom.xml dans le dossier simulé à l’appareil et ajoutez les dépendances suivantes pour le nœud **dépendances** . Cela vous permet d’utiliser le package client iothub-java dans votre application pour communiquer avec votre concentrateur IoT et à sérialiser des objets Java en JSON :

    ```
    <dependency>
      <groupId>com.microsoft.azure.iothub-java-client</groupId>
      <artifactId>iothub-java-device-client</artifactId>
      <version>1.0.14</version>
    </dependency>
    <dependency>
      <groupId>com.google.code.gson</groupId>
      <artifactId>gson</artifactId>
      <version>2.3.1</version>
    </dependency>
    ```

4. Enregistrez et fermez le fichier pom.xml.

5. À l’aide d’un éditeur de texte, ouvrez le fichier simulated-device\src\main\java\com\mycompany\app\App.java.

6. Ajoutez les instructions suivantes **Importer** le fichier :

    ```
    import com.microsoft.azure.iothub.DeviceClient;
    import com.microsoft.azure.iothub.IotHubClientProtocol;
    import com.microsoft.azure.iothub.Message;
    import com.microsoft.azure.iothub.IotHubStatusCode;
    import com.microsoft.azure.iothub.IotHubEventCallback;
    import com.microsoft.azure.iothub.IotHubMessageResult;
    import com.google.gson.Gson;
    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.Random;
    import java.util.concurrent.Executors;
    import java.util.concurrent.ExecutorService;
    ```

7. Ajoutez les variables de niveau classe suivantes à la classe **application** , remplaçant **{youriothubname}** avec votre nom de concentrateur IoT et **{yourdevicekey}** avec la valeur de clé appareil générées dans la section *Création d’une identité de l’appareil* :

    ```
    private static String connString = "HostName={youriothubname}.azure-devices.net;DeviceId=myFirstJavaDevice;SharedAccessKey={yourdevicekey}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.AMQP;
    private static String deviceId = "myFirstJavaDevice";
    private static DeviceClient client;
    ```

    Cet exemple d’application utilise la variable de **protocole** lorsqu’il fait appel à un objet **DeviceClient** . Vous pouvez utiliser le protocole HTTP ou AMQP pour communiquer avec IoT concentrateur.

8. Ajoutez que suit imbriqués **TelemetryDataPoint** classe à l’intérieur de la classe **application** pour spécifier les données de télémétrie qu'envoie votre appareil à votre concentrateur IoT :

    ```
    private static class TelemetryDataPoint {
      public String deviceId;
      public double windSpeed;
        
      public String serialize() {
        Gson gson = new Gson();
        return gson.toJson(this);
      }
    }
    ```

9. Ajoutez la classe **EventCallback** imbriquée suivante à l’intérieur de la classe **application** pour afficher l’état d’accusé de réception renvoyant le hub IoT lorsqu’il traite un message à partir du périphérique simulé. Cette méthode avertit également le thread principal dans l’application lorsque le message a été traité :

    ```
    private static class EventCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to message with status: " + status.name());
      
        if (context != null) {
          synchronized (context) {
            context.notify();
          }
        }
      }
    }
    ```

10. Ajoutez la classe **MessageSender** imbriquée suivante à l’intérieur de la classe **App** . La méthode **run** dans cette catégorie génère des exemples de données de télémétrie pour envoyer à votre plateforme IoT et attend un accusé de réception avant d’envoyer le message suivant :

    ```
    private static class MessageSender implements Runnable {
      public volatile boolean stopThread = false;
      
      public void run()  {
        try {
          double avgWindSpeed = 10; // m/s
          Random rand = new Random();
          
          while (!stopThread) {
            double currentWindSpeed = avgWindSpeed + rand.nextDouble() * 4 - 2;
            TelemetryDataPoint telemetryDataPoint = new TelemetryDataPoint();
            telemetryDataPoint.deviceId = deviceId;
            telemetryDataPoint.windSpeed = currentWindSpeed;
            
            String msgStr = telemetryDataPoint.serialize();
            Message msg = new Message(msgStr);
            System.out.println("Sending: " + msgStr);
            
            Object lockobj = new Object();
            EventCallback callback = new EventCallback();
            client.sendEventAsync(msg, callback, lockobj);
            
            synchronized (lockobj) {
              lockobj.wait();
            }
            Thread.sleep(1000);
          }
        } catch (InterruptedException e) {
          System.out.println("Finished.");
        }
      }
    }
    ```

    Cette méthode envoie un nouveau message appareil-nuage une seconde après que le hub IoT reconnaît le message précédent. Le message contient un objet JSON numéro de série avec l’ID de périphérique et un numéro généré aléatoirement pour simuler un capteur de vitesse vent.

11. Remplacez la méthode **principale** par le code suivant qui crée un thread pour envoyer des messages de l’appareil-nuage à votre concentrateur IoT :

    ```
    public static void main( String[] args ) throws IOException, URISyntaxException {
      client = new DeviceClient(connString, protocol);
      client.open();

      MessageSender sender = new MessageSender();

      ExecutorService executor = Executors.newFixedThreadPool(1);
      executor.execute(sender);

      System.out.println("Press ENTER to exit.");
      System.in.read();
      executor.shutdownNow();
      client.close();
    }
    ```

12. Enregistrez et fermez le fichier App.java.

13. Pour générer l’application **d’appareil simulé** à l’aide de Maven, exécutez la commande suivante à l’invite de commandes dans le dossier simulé à l’appareil :

    ```
    mvn clean package -DskipTests
    ```

> [AZURE.NOTE] Pour simplifier les choses, ce didacticiel ne pas implémenter une stratégie de réessayer. Dans le code de production, vous devez implémenter des stratégies de nouvelles tentatives (par exemple, un intervalle exponentielle), comme indiqué dans l’article MSDN [Transitoires de gestion des pannes][lnk-transient-faults].

## <a name="run-the-applications"></a>Exécuter les applications

Vous êtes maintenant prêt à exécuter les applications.

1. À une invite de commande dans le dossier en lecture d2c, exécutez la commande suivante pour commencer l’analyse de la première partition dans votre hub IoT :

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Application cliente de service Java IoT concentrateur pour contrôler les messages de l’appareil-nuage][7]

2. À une invite de commande dans le dossier appareil simulé, exécutez la commande suivante pour commencer à envoyer des données de télémétrie à votre concentrateur IoT :

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App" 
    ```

    ![Application de client smart device Java IoT concentrateur pour envoyer des messages de l’appareil-nuage][8]

3. La vignette de **l’utilisation** du [portail Azure] [ lnk-portal] affiche le nombre de messages envoyés au concentrateur :

    ![Azure portail l’utilisation vignette nombre de messages envoyés à IoT concentrateur][43]

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous configuré un nouveau concentrateur IoT dans le portail Azure et puis créée une identité appareil dans identité Registre son. Vous avez utilisé cette identité appareil pour activer l’application périphérique simulé envoyer des messages appareil-nuage au concentrateur. Vous avez également créé une application qui affiche les messages reçus par le hub. 

Pour continuer la prise en main IoT concentrateur et Explorer d’autres scénarios IoT voir :

- [Connecter votre périphérique][lnk-connect-device]
- [Mise en route de la gestion des appareils][lnk-device-management]
- [Prise en main du Kit de développement de passerelle IoT][lnk-gateway-SDK]

Pour découvrir comment étendre votre solution IoT et traiter des messages de périphérique-nuage à l’échelle, consultez le [traitement des messages appareil-nuage] [ lnk-process-d2c-tutorial] didacticiel.

<!-- Images. -->
[6]: ./media/iot-hub-java-java-getstarted/create-iot-hub6.png
[7]: ./media/iot-hub-java-java-getstarted/runapp1.png
[8]: ./media/iot-hub-java-java-getstarted/runapp2.png
[43]: ./media/iot-hub-java-java-getstarted/usage.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide-identity-registry.md
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/java-devbox-setup.md
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-device-management]: iot-hub-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/