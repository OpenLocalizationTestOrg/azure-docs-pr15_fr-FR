<properties
    pageTitle="Envoyer des messages de cloud vers le périphérique avec concentrateur IoT | Microsoft Azure"
    description="Suivez ce didacticiel pour apprendre à envoyer des messages cloud vers le périphérique à l’aide d’Azure IoT concentrateur avec Java."
    services="iot-hub"
    documentationCenter="java"
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="java"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/13/2016"
     ms.author="dobett"/>

# <a name="tutorial-how-to-send-cloud-to-device-messages-with-iot-hub-and-java"></a>Didacticiel : Comment envoyer des messages de cloud vers le périphérique avec concentrateur IoT et Java

[AZURE.INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## <a name="introduction"></a>Introduction

Concentrateur IoT Azure est un service entièrement géré qui vous aide à activer la communication bidirectionnelle fiable et sécurisée entre millions d’appareils IoT et mettre fin à une application précédent. Le didacticiel [prise en main IoT concentrateur] montre comment créer un concentrateur IoT, mise en service d’une identité appareil qu’il contient et un périphérique simulé qui envoie des messages de l’appareil-nuage de code.

Ce didacticiel s’appuie sur la [prise en main IoT concentrateur]. Il vous explique comment procéder pour :

- À partir de votre application cloud principale, envoyer des messages de cloud vers le périphérique à un seul périphérique via IoT concentrateur.
- Recevoir des messages de cloud vers le périphérique sur un appareil.
- À partir de votre cloud application back-end, demander un accusé de réception (*commentaires*) pour les messages envoyés à un appareil IoT concentrateur.

Vous pouvez trouver plus d’informations sur les messages de cloud vers le périphérique dans le [Guide du développeur IoT concentrateur][IoT Hub Developer Guide - C2D].

À la fin de ce didacticiel, vous exécutez deux applications console Java :

* **appareil simulé**, une version modifiée de l’application créée dans [prise en main IoT concentrateur], qui se connecte à votre concentrateur IoT et reçoit des messages cloud vers le périphérique.
* **envoi de messages de c2d**, qui envoie un message cloud vers le périphérique à l’appareil simulé via IoT concentrateur et puis reçoit son accusé de réception.

> [AZURE.NOTE] Concentrateur IoT prend en charge SDK pour les nombreuses langues (y compris C, Java et Javascript) via Azure IoT appareil SDK et les plateformes d’appareil. Pour obtenir des instructions détaillées sur la connexion de votre appareil au code de ce didacticiel et généralement à Azure IoT concentrateur, voir le [Centre de développement IoT Azure].

Pour effectuer ce didacticiel, vous devez les éléments suivants :

+ Java SE 8. <br/> [Préparer votre environnement de développement] [ lnk-dev-setup] décrit comment installer Java pour ce didacticiel sur Windows ou Linux.

+ Maven 3.  <br/> [Préparer votre environnement de développement] [ lnk-dev-setup] décrit comment installer Maven pour ce didacticiel sur Windows ou Linux.

+ Un compte Azure actif. (Si vous n’avez pas un compte, vous pouvez créer un [compte gratuit] [ lnk-free-trial] en quelques minutes.)

## <a name="receive-messages-on-the-simulated-device"></a>Recevoir des messages sur le périphérique simulé

Dans cette section, vous modifiez l’application périphérique simulé vous avez créé dans [prise en main IoT concentrateur] pour recevoir des messages de cloud vers le périphérique à partir du hub IoT.

1. À l’aide d’un éditeur de texte, ouvrez le fichier simulated-device\src\main\java\com\mycompany\app\App.java.

2. Ajoutez la classe **MessageCallback** suivante comme une classe imbriquée à l’intérieur de la classe **App** . La méthode **execute** est appelée lorsque le périphérique reçoit un message IoT concentrateur. Dans cet exemple, le périphérique indique toujours le hub qu’il a terminé le message :

    ```
    private static class MessageCallback implements
    com.microsoft.azure.iothub.MessageCallback {
      public IotHubMessageResult execute(Message msg, Object context) {
        System.out.println("Received message from hub: "
          + new String(msg.getBytes(), Message.DEFAULT_IOTHUB_MESSAGE_CHARSET));

        return IotHubMessageResult.COMPLETE;
      }
    }
    ```

3. Modifier la méthode **principale** pour créer une instance de **MessageCallback** et appelez la méthode **setMessageCallback** avant d’ouvrir le client comme suit :

    ```
    client = new DeviceClient(connString, protocol);

    MessageCallback callback = new MessageCallback();
    client.setMessageCallback(callback, null);
    client.open();
    ```

    > [AZURE.NOTE] Si vous utilisez HTTP au lieu de MQTT ou AMQP comme type de transport, l’instance **DeviceClient** vérifie les messages à partir du IoT Hub rarement (moins de 25 minutes). Pour plus d’informations sur les différences entre prise en charge MQTT, AMQP et HTTP et concentrateur IoT la limitation, voir le [Guide du développeur IoT concentrateur][IoT Hub Developer Guide - C2D].

## <a name="send-a-cloud-to-device-message"></a>Envoyer un message cloud vers le périphérique

Dans cette section, vous créez une application console Java qui envoie des messages de cloud vers le périphérique à l’application de périphérique simulé. Vous devez l’Id de l’appareil que vous avez ajouté dans le didacticiel [prise en main IoT concentrateur] de l’appareil. Vous devez également la chaîne de connexion pour votre plateforme IoT que vous pouvez trouver dans le [portail Azure].

1. Créer un projet Maven appelé **c2d-envoyer des messages** à l’aide de la commande suivante à votre invite de commandes. Remarque : il s’agit d’une commande unique, longue :

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=send-c2d-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. À votre invite de commandes, naviguez vers le nouveau dossier c2d-envoyer des messages.

3. À l’aide d’un éditeur de texte, ouvrez le fichier pom.xml dans le dossier d’envoi de messages de c2d et ajoutez les dépendances suivantes pour le nœud **dépendances** . Ajout de la dépendance vous permet d’utiliser le package **iothub java-service client** dans votre application pour communiquer avec votre service concentrateur IoT :

    ```
    <dependency>
      <groupId>com.microsoft.azure.iothub-java-client</groupId>
      <artifactId>iothub-java-service-client</artifactId>
      <version>1.0.7</version>
    </dependency>
    ```

4. Enregistrez et fermez le fichier pom.xml.

5. À l’aide d’un éditeur de texte, ouvrez le fichier send-c2d-messages\src\main\java\com\mycompany\app\App.java.

6. Ajoutez les instructions suivantes **Importer** le fichier :

    ```
    import com.microsoft.azure.iot.service.sdk.*;
    import java.io.IOException;
    import java.net.URISyntaxException;
    ```

7. Ajoutez les variables de niveau classe suivantes à la classe **application** , en remplaçant **{yourhubconnectionstring}** et **{yourdeviceid}** par les valeurs votre indiqué précédemment :

    ```
    private static final String connectionString = "{yourhubconnectionstring}";
    private static final String deviceId = "{yourdeviceid}";
    private static final IotHubServiceClientProtocol protocol = IotHubServiceClientProtocol.AMQP;
    ```
    
8. Remplacez la méthode **principale** par le code suivant qui se connecte à votre plateforme IoT, envoie un message à votre appareil et puis attend un accusé de réception que le périphérique reçu et traité le message :

    ```
    public static void main(String[] args) throws IOException,
        URISyntaxException, Exception {
      ServiceClient serviceClient = ServiceClient.createFromConnectionString(
        connectionString, protocol);
      
      if (serviceClient != null) {
        serviceClient.open();
        FeedbackReceiver feedbackReceiver = serviceClient
          .getFeedbackReceiver(deviceId);
        if (feedbackReceiver != null) feedbackReceiver.open();

        Message messageToSend = new Message("Cloud to device message.");
        messageToSend.setDeliveryAcknowledgement(DeliveryAcknowledgement.Full);

        serviceClient.send(deviceId, messageToSend);
        System.out.println("Message sent to device");

        FeedbackBatch feedbackBatch = feedbackReceiver.receive(10000);
        if (feedbackBatch != null) {
          System.out.println("Message feedback received, feedback time: "
            + feedbackBatch.getEnqueuedTimeUtc().toString());
        }

        if (feedbackReceiver != null) feedbackReceiver.close();
        serviceClient.close();
      }
    }
    ```

    > [AZURE.NOTE] Pour facilité plus de, ce didacticiel ne pas implémenter n’importe quelle stratégie de réessayer. Dans le code de production, vous devez implémenter des stratégies de nouvelles tentatives (par exemple, intervalle exponentielle), comme indiqué dans l’article MSDN [Transitoires de gestion des pannes].

## <a name="run-the-applications"></a>Exécuter les applications

Vous êtes maintenant prêt à exécuter les applications.

1. À une invite de commande dans le dossier appareil simulé, exécutez la commande suivante pour commencer l’envoi de télémétrie à votre concentrateur IoT et à recevoir des messages de cloud vers le périphérique envoyés à partir de votre plateforme :

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App" 
    ```

    ![Exécuter l’application périphérique simulé][img-simulated-device]

2. À une invite de commande dans le dossier d’envoi de messages de c2d, exécutez la commande suivante pour envoyer un message cloud vers le périphérique et attendre un accusé de réception de commentaires :

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Exécutez la commande Envoyer le message cloud vers le périphérique][img-send-command]

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à envoyer et recevoir des messages de cloud vers le périphérique. 

Pour voir des exemples de solutions de bout en bout complètes qui utilisent IoT concentrateur, voir [Azure IoT Suite].

Pour plus d’informations sur le développement de solutions avec concentrateur IoT, consultez le [Guide du développeur IoT concentrateur].


<!-- Images -->
[img-simulated-device]: media/iot-hub-java-java-c2d/receivec2d.png
[img-send-command]:  media/iot-hub-java-java-c2d/sendc2d.png
<!-- Links -->

[Prise en main IoT concentrateur]: iot-hub-java-java-getstarted.md
[IoT Hub Developer Guide - C2D]: iot-hub-devguide-messaging.md
[Guide du développeur IoT concentrateur]: iot-hub-devguide.md
[Centre de développement IoT Azure]: http://www.azure.com/develop/iot
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/java-devbox-setup.md
[Gestion des erreurs transitoires]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Portail Azure]: https://portal.azure.com
[Suite IoT Azure]: https://azure.microsoft.com/documentation/suites/iot-suite/