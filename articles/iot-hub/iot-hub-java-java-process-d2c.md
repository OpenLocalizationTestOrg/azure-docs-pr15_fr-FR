<properties
    pageTitle="Traiter les messages d’appareil-nuage IoT concentrateur (Java) | Microsoft Azure"
    description="Suivez ce didacticiel Java pour découvrir des motifs utiles pour traiter les messages d’appareil-nuage IoT concentrateur."
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
     ms.date="09/01/2016"
     ms.author="dobett"/>

# <a name="tutorial-how-to-process-iot-hub-device-to-cloud-messages-using-java"></a>Didacticiel : Comment traiter les messages de périphérique-nuage IoT concentrateur à l’aide de Java

[AZURE.INCLUDE [iot-hub-selector-process-d2c](../../includes/iot-hub-selector-process-d2c.md)]

## <a name="introduction"></a>Introduction

Concentrateur IoT Azure est un service entièrement géré qui permet aux fiable et sécuriser les communications bidirectionnelle entre millions d’appareils IoT et une application back-end. Autres didacticiels ([prise en main IoT concentrateur] et [Envoyer des messages de cloud vers le périphérique avec concentrateur IoT][lnk-c2d]) vous montrer comment utiliser la fonctionnalité de messagerie appareil-nuage et cloud vers le périphérique base du concentrateur IoT.

Ce didacticiel s’appuie sur le code indiqué dans le didacticiel [prise en main IoT concentrateur] et indique les deux modèles scalable que vous pouvez utiliser pour traiter les messages de l’appareil-nuage :

- Le stockage fiable de messages appareil-nuage dans le [stockage blob Azure]. Un scénario courant est *chemin d’accès à froid* analytique, dans lequel vous stockez les données de télémétrie dans des objets BLOB à utiliser comme entrée dans les processus analytique. Ces processus peuvent être pilotées par les outils tels que [Azure Data Factory] ou la pile [HDInsight (Hadoop)] .

- Le traitement fiable des messages d’appareil-nuage *interactive* . Messages de l’appareil-nuage sont interactifs lorsqu’elles sont immédiates déclencheurs pour un ensemble d’actions dans l’application principale. Par exemple, un appareil peut envoyer un message d’alarme déclenchant l’insertion d’un bon dans un système CRM. En revanche, les messages de *point de données* flux simplement à un moteur analytique. Par exemple, télémétrie température d’un appareil qui doit être stockées pour une analyse ultérieure est un message de point de données.

Comme concentrateur IoT expose un [Événement concentrateur][lnk-event-hubs]-point de terminaison compatible pour recevoir des messages appareil-nuage ce didacticiel utilise une instance de [EventProcessorHost] . Cette instance :

* Fiable stocke les messages de *point de données* dans le stockage blob Azure.
* Transfère *interactifs* appareil-nuage des messages à une [file d’attente Bus des services] Azure pour un traitement immédiat.

Bus des services permet de garantir traitement fiable de messages interactifs, étant donné qu’il fournit les points de contrôle par message et heure, basées sur fenêtre Suppression des doublons.

> [AZURE.NOTE] Une instance de **EventProcessorHost** est la seule façon de traiter les messages interactifs. D’autres options incluent [Azure Service TISSU] [ lnk-service-fabric] et [Azure flux Analytique][lnk-stream-analytics].

À la fin de ce didacticiel, vous exécutez trois applications console Java :

* **appareil simulé**, une version modifiée de l’application créée dans le didacticiel [prise en main IoT concentrateur] , envoie des messages de périphérique-nuage de point de données chaque deuxième et interactive appareil-nuage messages toutes les 10 secondes. Cette application utilise le protocole AMQP pour communiquer avec IoT concentrateur.
* **messages de d2c processus** utilise la classe [EventProcessorHost] pour extraire les messages à partir du point de terminaison compatible événement concentrateur. Il puis fiable stocke les messages de point de données dans le stockage blob Azure et transfert des messages interactifs dans une file d’attente Bus des services.
* **processus interactifs messages** désélectionnez en file d’attente les messages interactifs à partir de la file d’attente Bus des services.

> [AZURE.NOTE] Concentrateur IoT prend en charge SDK pour de nombreux plateformes d’appareil et langues, y compris C, Java et JavaScript. Pour obtenir des instructions sur la façon de remplacer le périphérique simulé dans ce didacticiel avec un appareil physique et comment établir une connexion à un concentrateur IoT appareils, voir le [Centre de développement IoT Azure].

Ce didacticiel s’applique directement à d’autres modes de consommation de messages événement concentrateur compatibles, telles que les projets [HDInsight (Hadoop)] . Pour plus d’informations, voir le [guide du développeur Azure IoT concentrateur - appareil vers le cloud].

Pour effectuer ce didacticiel, vous devez les éléments suivants :

+ Une version complète de travail du didacticiel [prise en main IoT concentrateur] .

+ Java SE 8. <br/> [Préparer votre environnement de développement] [ lnk-dev-setup] décrit comment installer Java pour ce didacticiel sur Windows ou Linux.

+ Maven 3.  <br/> [Préparer votre environnement de développement] [ lnk-dev-setup] décrit comment installer Maven pour ce didacticiel sur Windows ou Linux.

+ Un compte Azure actif. <br/>Si vous n’avez pas un compte, vous pouvez créer un [compte gratuit](https://azure.microsoft.com/free/) en quelques minutes.

Vous devez connaître certaines base de [Stockage Azure] et [Bus des services Azure].


## <a name="send-interactive-messages-from-a-simulated-device"></a>Envoyer des messages interactifs à partir d’un périphérique simulé

Dans cette section, vous modifiez l’application de périphérique simulé que vous avez créé dans le didacticiel [prise en main IoT concentrateur] pour envoyer des messages appareil-nuage interactifs au concentrateur IoT.

1. Utilisez un éditeur de texte pour ouvrir le fichier simulated-device\src\main\java\com\mycompany\app\App.java. Ce fichier contient le code de l’application **d’appareil simulé** que vous avez créé dans le didacticiel [prise en main IoT concentrateur] .

2. Ajoutez la classe imbriquée suivante à la classe **d’application** :

    ```
    private static class InteractiveMessageSender implements Runnable {
      public void run() {
        try {
          while (true) {
            String msgStr = "Alert message!";
            Message msg = new Message(msgStr);
            msg.setMessageId(java.util.UUID.randomUUID().toString());
            msg.setProperty("messageType", "interactive");
            System.out.println("Sending interactive message: " + msgStr);

            Object lockobj = new Object();
            EventCallback callback = new EventCallback();
            client.sendEventAsync(msg, callback, lockobj);

            synchronized (lockobj) {
              lockobj.wait();
            }
            Thread.sleep(10000);
          }
        } catch (InterruptedException e) {
          System.out.println("Finished sending interactive messages.");
        }
      }
    }
    ```

    Ce cours est similaire à la classe **MessageSender** dans le projet **smart device simulé** . Les seules différences sont que vous avez défini maintenant la propriété système **MessageId** , et une propriété personnalisée appelée **messageType**.
    Le code assigne un identificateur unique universel (UUID) à la propriété **MessageId** . Le Bus de Service peut utiliser cet identificateur pour supprime les messages qu’elle reçoit les doublons. L’exemple utilise la propriété **messageType** pour distinguer interactive des messages de point de données. L’application transmet ces informations dans les propriétés du message, plutôt que dans le corps du message, afin que le processeur d’événements n’a pas besoin désérialiser le message pour effectuer le routage des messages.

    > [AZURE.NOTE] Il est important de créer le **MessageId** utilisé pour la suppression des doublons messages interactifs dans le code de l’appareil. Communications réseau intermittents ou autres échecs, peuvent entraîner plusieurs retransmission du même message à partir de cet appareil. Vous pouvez également utiliser un ID de message sémantique, par exemple un hachage des champs de données pertinents message, à la place d’un UUID.

3. Modifiez la méthode **principale** pour envoyer les deux messages interactifs et point de données messages comme le montre l’extrait de code suivantes :

    ````
    MessageSender sender = new MessageSender();
    InteractiveMessageSender interactiveSender = new InteractiveMessageSender();

    ExecutorService executor = Executors.newFixedThreadPool(2);
    executor.execute(sender);
    executor.execute(interactiveSender);
    ````

4. Enregistrez et fermez le fichier simulated-device\src\main\java\com\mycompany\app\App.java.

    > [AZURE.NOTE] Pour simplifier la démonstration, ce didacticiel ne pas implémenter une stratégie de réessayer. Dans le code de production, vous devez implémenter une stratégie de nouvelles tentatives comme intervalle exponentielle, comme indiqué dans l’article MSDN [Transitoires de gestion des pannes].

5. Pour générer l’application **d’appareil simulé** à l’aide de Maven, exécutez la commande suivante à l’invite de commandes dans le dossier simulé à l’appareil :

    ```
    mvn clean package -DskipTests
    ```

## <a name="process-device-to-cloud-messages"></a>Traiter les messages appareil-nuage

Dans cette section, vous créez une application console Java qui traite les messages appareil-nuage IoT concentrateur. Concentrateur IOT expose un [concentrateur événement]-point de terminaison compatible pour activer une application lire les messages de l’appareil-nuage. Ce didacticiel utilise la classe [EventProcessorHost] pour traiter ces messages dans une application console. Pour plus d’informations sur la façon de traiter les messages à partir de l’événement Hubs, consultez le didacticiel [Prise en main avec Hubs de l’événement] .

Le défi principal lors de la mise en œuvre d’un stockage fiable des messages de point de données ou le transfert de messages interactifs, est que le traitement de l’événement repose sur consommateur de message pour fournir des points de contrôle pour sa progression. En outre, pour obtenir un haut débit, lorsque vous lisez à partir de l’événement Hubs vous devez fournir des points de contrôle par lots importants. Cette approche crée la possibilité de traitement en double pour un grand nombre de messages, s’il existe une défaillance et vous rétablissez le point de contrôle précédent. Dans ce didacticiel, vous allez apprendre à synchroniser le stockage Azure écrit et windows de suppression des doublons Bus des services avec points de contrôle **EventProcessorHost** .

Pour écrire des messages dans le stockage Azure fiable, l’exemple utilise la fonctionnalité de valider bloc individuel des [objets BLOB bloc][Azure Block Blobs]. Le processeur d’événements sont additionnés messages en mémoire jusqu'à ce qu’il est temps de fournir un point de contrôle. Par exemple, après que la mémoire tampon cumulée des messages atteigne la taille maximale de bloc de 4 Mo, ou après le Bus des services suppression des doublons fenêtre délai s’écoule. Ensuite, avant le point de contrôle, le code valide un nouveau bloc au blob.

Le processeur d’événements utilise Hubs événement décale message en tant que bloc ID. Ce mécanisme permet le processeur d’événements effectuer une vérification de la suppression des doublons avant qu’il valide le nouveau bloc de stockage, en prenant soin de cas de panne entre commettre un bloc ainsi qu’au point de contrôle.

> [AZURE.NOTE] Ce didacticiel utilise un seul compte de stockage Azure pour écrire tous les messages extraites IoT concentrateur. Pour décider si vous avez besoin utiliser plusieurs comptes de stockage Azure dans votre solution, voir [extensibilité du stockage Azure instructions].

L’application utilise la fonctionnalité de suppression des doublons Bus des services afin d’éviter les doublons lorsqu’il traite les messages interactifs. Le périphérique simulé affecte à chaque message interactif uniques **MessageId**. Cet identifiant Bus des services pour vous assurer que, dans la fenêtre de temps spécifié suppression des doublons, aucun deux message avec le même **MessageId** est remis vers les récepteurs. Cette suppression des doublons, ainsi que la sémantique de saisie semi-automatique par message fournie par files d’attente Bus des services, facilite la mise en œuvre le traitement fiable des messages interactifs.

Pour vous assurer qu’aucun message n’est renvoyé en dehors de la fenêtre de suppression des doublons, le code synchronise le mécanisme de point de contrôle **EventProcessorHost** avec la fenêtre de suppression des doublons de file d’attente Bus des services. Cette synchronisation est émise par forcer un point de contrôle au moins une fois chaque fois que la fenêtre de suppression des doublons s’écoule (dans ce didacticiel, la fenêtre est une heure).

> [AZURE.NOTE] Ce didacticiel utilise une seule file Service Bus partitionnée au processus de tous les messages interactifs extraites IoT concentrateur. Pour plus d’informations sur l’utilisation des files d’attente Bus des services afin de respecter les exigences extensibilité élevées de votre solution, consultez la documentation de [Bus des services Azure] .

### <a name="provision-an-azure-storage-account-and-a-service-bus-queue"></a>Configurer un compte de stockage Azure et une file d’attente Bus des services

Pour utiliser la classe [EventProcessorHost] , vous devez disposer d’un compte de stockage Azure pour activer **EventProcessorHost** enregistrer les informations de point de contrôle. Vous pouvez utiliser un compte de stockage Azure existant, ou suivez les instructions dans le [Stockage sur Azure] pour créer un nouveau. Prenez note de la chaîne de connexion de compte de stockage Azure.

> [AZURE.NOTE] Lorsque vous copiez et collez la chaîne de connexion de compte de stockage Azure, vérifiez qu’il n’y a aucuns espaces inclus.

Vous devez également une file d’attente de Bus des services pour activer le traitement fiable de messages interactifs. Vous pouvez créer une file d’attente par programme, avec une fenêtre de suppression des doublons une heure, comme indiqué dans [l’utilisation de files d’attente de Service Bus][file d’attente Bus des services]. Vous pouvez également utiliser le [portail classique Azure][lnk-classic-portal], en procédant comme suit :

1. Dans le coin inférieur gauche, cliquez sur **Nouveau** . Puis cliquez sur **Services d’application** > **Service Bus** > **file d’attente** > **Créer personnalisé**. Entrez le nom **d2ctutorial**, sélectionnez une région et utiliser un espace de noms existant ou créez-en un. Prenez note de l’espace de noms, vous en avez besoin plus loin dans ce didacticiel. Dans la page suivante, sélectionnez **Activer la détection des doublons**et définir la **fenêtre de délai de l’historique de détection de dupliquer** une heure. Puis cliquez sur la coche située dans le coin inférieur droit pour enregistrer votre configuration file d’attente.

    ![Créer une file d’attente portail Azure][30]

2. Dans la liste des files d’attente Bus des services, cliquez sur **d2ctutorial**, puis cliquez sur **configurer**. Créer deux stratégies d’accès partagé, un appelée **Envoyer** avec des autorisations **Envoyer** et un appelée **écouter** avec **écouter** des autorisations. Prenez note des valeurs de **clé primaire** pour les deux stratégies, vous avez besoin plus loin dans ce didacticiel. Lorsque vous avez terminé, cliquez sur **Enregistrer** dans la partie inférieure.

    ![Configurer une file d’attente portail Azure][31]

### <a name="create-the-event-processor"></a>Créer le processeur d’événements

Dans cette section, vous créez une application Java pour traiter les messages à partir du point de terminaison compatible événement concentrateur.

La première tâche consiste à ajouter un projet Maven appelé **d2c-traiter les messages** qui reçoit des messages de périphérique-nuage du point de terminaison compatible IoT concentrateur événement concentrateur et achemine les messages vers d’autres services de données principale.

1. Dans le dossier iot java-démarrer que vous avez créé dans le didacticiel [prise en main IoT concentrateur] , créez un projet de Maven appelé **d2c-traiter les messages** à l’aide de la commande suivante à votre invite de commandes. Remarque : il s’agit d’une commande unique, longue :

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=process-d2c-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. À votre invite de commandes, naviguez vers le nouveau dossier d2c-traitement des messages.

3. À l’aide d’un éditeur de texte, ouvrez le fichier pom.xml dans le dossier de messages de processus d2c et ajoutez les dépendances suivantes pour le nœud **dépendances** . Ces dépendances permettent d’utiliser les azure eventhubs, azure-eventhubs-eph et azure servicebus packages dans votre application pour interagir avec votre concentrateur IoT et file d’attente Bus des services :

    ```
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-eventhubs</artifactId>
      <version>0.8.0</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-eventhubs-eph</artifactId>
      <version>0.8.0</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-servicebus</artifactId>
      <version>0.9.4</version>
    </dependency>
    ```

4. Enregistrez et fermez le fichier pom.xml.

La tâche suivante consiste à ajouter une classe **ErrorNotificationHandler** au projet.

1. Utiliser un éditeur de texte pour créer un fichier process-d2c-messages\src\main\java\com\mycompany\app\ErrorNotificationHandler.java. Ajoutez le code suivant dans le fichier pour afficher les messages d’erreur de l’instance **EventProcesssorHost** :

    ```
    package com.mycompany.app;

    import java.util.function.Consumer;
    import com.microsoft.azure.eventprocessorhost.ExceptionReceivedEventArgs;

    public class ErrorNotificationHandler implements
        Consumer<ExceptionReceivedEventArgs> {
      @Override
      public void accept(ExceptionReceivedEventArgs t) {
        System.out.println("EventProcessorHost: Host " + t.getHostname()
            + " received general error notification during " + t.getAction() + ": "
            + t.getException().toString());
      }
    }
    ```

2. Enregistrez et fermez le fichier ErrorNotificationHandler.java.

Désormais, vous pouvez ajouter une classe qui mettent en œuvre, l’interface **IEventProcessor** . La classe **EventProcessorHost** appelle ce cours pour traiter les messages appareil-nuage reçus IoT concentrateur. Le code dans ce cours met en œuvre la logique pour stocker les messages parviennent dans un conteneur blob et transférer des messages interactifs dans la file d’attente Bus des services.

La méthode **onEvents** définit la variable **latestEventData** qui suit le nombre de décalage et la séquence du dernier message lu par ce processeur d’événements. N’oubliez pas que chaque processeur est responsable d’une partition unique. La méthode **onEvents** puis reçoit un lot de messages à partir du IoT Hub et les traite comme suit : il envoie des messages interactifs à la file d’attente Bus des services et ajoute les messages de point de données à la mémoire tampon **toAppend** . Si la mémoire tampon atteint la limite de bloc de 4 Mo, ou que les fenêtres de délai de suppression des doublons s’écoule (une heure après le dernier point de contrôle dans ce didacticiel), la méthode déclenche un point de contrôle.

La méthode **AppendAndCheckPoint** génère tout d’abord un **l’ID de bloc** pour le bloc à ajouter à l’objet blob. Stockage Azure requiert que tout bloquent ID pour avoir la même longueur, afin que la méthode remplit le décalage avec les zéros non significatifs. Ensuite, si un bloc de cette ID est déjà dans le blob, la méthode remplace par le contenu de mémoire tampon en cours.

> [AZURE.NOTE] Pour simplifier le code, ce didacticiel utilise un seul blob par partition pour stocker les messages. Une solution réelle est mise en œuvre yeux en créant des fichiers supplémentaires après un certain laps de temps, ou lorsqu’ils atteignent une certaine taille de fichier. N’oubliez pas qu’un blob Azure bloc peut contenir au maximum 195 Go de données.

La tâche suivante consiste pour implémenter l’interface **IEventProcessor** :

1. Utiliser un éditeur de texte pour créer un fichier process-d2c-messages\src\main\java\com\mycompany\app\EventProcessor.java.

2. Ajoutez les importations suivantes et la définition de classe dans le fichier EventProcessor.java. La classe **EventProcessor** met en œuvre l’interface **IEventProcessor** qui définit le comportement du client Hubs événement :

    ```
    package com.mycompany.app;

    import java.io.ByteArrayInputStream;
    import java.io.ByteArrayOutputStream;
    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.nio.charset.StandardCharsets;
    import java.time.Duration;
    import java.time.Instant;
    import java.util.ArrayList;
    import java.util.Base64;
    import java.util.concurrent.ExecutionException;

    import com.microsoft.azure.eventhubs.EventData;
    import com.microsoft.azure.eventprocessorhost.*;
    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.blob.*;
    import com.microsoft.windowsazure.services.servicebus.*;
    import com.microsoft.windowsazure.services.servicebus.models.BrokeredMessage;

    public class EventProcessor implements IEventProcessor {

    }
    ```

3. Ajoutez les méthodes suivantes à la classe **EventProcessor** pour implémenter l’interface **IEventProcessor** :

    ```
    @Override
    public void onOpen(PartitionContext context) throws Exception {
      System.out.println("EventProcessorHost: Partition "
          + context.getPartitionId() + " is opening");
    }

    @Override
    public void onClose(PartitionContext context, CloseReason reason)
        throws Exception {
      System.out.println("EventProcessorHost: Partition "
          + context.getPartitionId() + " is closing for reason "
          + reason.toString());
    }

    @Override
    public void onError(PartitionContext context, Throwable error) {
      System.out.println("EventProcessorHost: Partition "
          + context.getPartitionId() + " onError: " + error.toString());
    }

    @Override
    public void onEvents(PartitionContext context, Iterable<EventData> messages)
        throws Exception {
    }
    ```

4. Ajoutez les variables de niveau classe suivantes à la classe **EventProcessor** :

    ```
    public static CloudBlobContainer blobContainer;
    public static ServiceBusContract serviceBusContract;

    // Use a smaller MAX_BLOCK_SIZE value to test.
    final private int MAX_BLOCK_SIZE = 4 * 1024 * 1024;
    final private Duration MAX_CHECKPOINT_TIME = Duration.ofHours(1);

    private ByteArrayOutputStream toAppend = new ByteArrayOutputStream(
        MAX_BLOCK_SIZE);
    private Instant start = Instant.now();
    private EventData latestEventData;
    ```

5. Ajouter une méthode **AppendAndCheckPoint** avec la signature suivante à la classe **EventProcessor** :

    ```
    private void AppendAndCheckPoint(PartitionContext context)
      throws URISyntaxException, StorageException, IOException,
      IllegalArgumentException, InterruptedException, ExecutionException {
    }
    ```

6. Ajoutez le code suivant à la méthode **AppendAndCheckPoint** pour récupérer le nombre de décalage et la séquence de message en cours dans la partition :

    ```
    String currentOffset = latestEventData.getSystemProperties().getOffset();
    Long currentSequence = latestEventData.getSystemProperties().getSequenceNumber();
    System.out
        .printf(
            "\nAppendAndCheckPoint using partition: %s, offset: %s, sequence: %s\n",
            context.getPartitionId(), currentOffset, currentSequence);
    ```

7. Dans la méthode **AppendAndCheckPoint** , utilisez la valeur actuelle de décalage pour créer une instance de **BlockEntry** pour le bloc suivant enregistrer au blob :

    ```
    Long blockId = Long.parseLong(currentOffset);
    String blockIdString = String.format("startSeq:%1$025d", blockId);
    String encodedBlockId = Base64.getEncoder().encodeToString(
        blockIdString.getBytes(StandardCharsets.US_ASCII));
    BlockEntry block = new BlockEntry(encodedBlockId);
    ```

8. Dans la méthode **AppendAndCheckPoint** , téléchargez le dernier jeu de messages sur le blob bloc et récupérer la liste actuelle des blocs de :

    ```
    String blobName = String.format("iothubd2c_%s", context.getPartitionId());
    CloudBlockBlob currentBlob = blobContainer.getBlockBlobReference(blobName);

    currentBlob.uploadBlock(block.getId(),
        new ByteArrayInputStream(toAppend.toByteArray()), toAppend.size());
    ArrayList<BlockEntry> blockList = currentBlob.downloadBlockList();
    ```

9. Dans la méthode **AppendAndCheckPoint** , créez le bloc initial dans un nouveau blob ou ajouter le bloc au blob existant :

    ```
    if (currentBlob.exists()) {
      // Check if we should append new block or overwrite existing block
      BlockEntry last = blockList.get(blockList.size() - 1);
      if (blockList.size() > 0 && !last.getId().equals(block.getId())) {
        System.out.printf("Appending block %s to blob %s\n", blockId, blobName);
        blockList.add(block);
      } else {
        System.out.printf("Overwriting block %s in blob %s\n", blockId,
            blobName);
      }
    } else {
      System.out.printf("Creating initial block %s in new blob: %s\n", blockId,
          blobName);
      blockList.add(block);
    }
    currentBlob.commitBlockList(blockList);
    ```

10. Enfin, dans la méthode **AppendAndCheckPoint** , créer un point de contrôle sur la partition et préparer enregistrer le bloc de messages suivant :

    ```
    context.checkpoint(latestEventData);

    // Reset everything after the checkpoint.
    toAppend.reset();
    start = Instant.now();
    System.out.printf("Checkpointed on partition id: %s\n",
        context.getPartitionId());
    ```

11. Dans la méthode **onEvents** , ajoutez le code suivant pour recevoir des messages à partir du point de terminaison IoT concentrateur et transférer des messages interactifs dans la file d’attente Bus des services. Puis appelez la méthode **AppendAndCheckPoint** lorsque le bloc est plein ou le délai d’expiration :

    ```
    if (messages != null) {
      for (EventData eventData : messages) {
        latestEventData = eventData;
        byte[] data = eventData.getBody();
        if (eventData.getProperties().containsKey("messageType")
            && eventData.getProperties().get("messageType")
                .equals("interactive")) {
          String messageId = (String) eventData.getSystemProperties().get(
              "message-id");
          BrokeredMessage message = new BrokeredMessage(data);
          message.setMessageId(messageId);
          serviceBusContract.sendQueueMessage("d2ctutorial", message);
          continue;
        }
        if (toAppend.size() + data.length > MAX_BLOCK_SIZE
            || Duration.between(start, Instant.now()).compareTo(
                MAX_CHECKPOINT_TIME) > 0) {
          AppendAndCheckPoint(context);
        }
        toAppend.write(data);
      }
    }
    ```

12. Enfin, dans la méthode **onEvents** , ajoutez une clause « Sinon si » pour appeler **AppendAndCheckPoint** si le délai expire alors qu’il n’existe aucun message en provenance de concentrateur IoT :

    ```
    else if ((toAppend.size() > 0)
        && Duration.between(start, Instant.now())
            .compareTo(MAX_CHECKPOINT_TIME) > 0) {
      AppendAndCheckPoint(context);
    }
    ```

13. Enregistrer les modifications dans le fichier EventProcessor.java.

La dernière tâche dans le projet de **processus-d2c-messages** consiste à ajouter du code à la méthode **main** qui crée une instance de **EventProcessorHost** .

1. Utilisez un éditeur de texte pour ouvrir le fichier process-d2c-messages\src\main\java\com\mycompany\app\App.java.

2. Ajoutez les instructions suivantes **Importer** le fichier :

    ```
    import com.microsoft.azure.eventprocessorhost.*;
    import com.microsoft.azure.servicebus.ConnectionStringBuilder;
    import com.microsoft.azure.storage.CloudStorageAccount;
    import com.microsoft.azure.storage.StorageException;
    import com.microsoft.azure.storage.blob.CloudBlobClient;
    import com.microsoft.windowsazure.Configuration;
    import com.microsoft.windowsazure.services.servicebus.ServiceBusConfiguration;
    import com.microsoft.windowsazure.services.servicebus.ServiceBusService;

    import java.net.URISyntaxException;
    import java.security.InvalidKeyException;
    import java.util.concurrent.*;
    ```

3. Ajoutez la variable de niveau classe suivante à la classe **d’application** . Remplacez **{yourstorageaccountconnectionstring}** par la chaîne de connexion de compte de stockage Azure que vous avez effectuées une note de précédemment dans la section [configurer un compte de stockage Azure et une file d’attente Bus des services](#provision-an-azure-storage-account-and-a-service-bus-queue) :

    ```
    private final static String storageConnectionString = "{yourstorageaccountconnectionstring}";
    ```

4. Ajoutez les variables de niveau classe suivantes à la classe **application** et remplacez **{yourservicebusnamespace}** avec votre espace de noms de Service Bus et **{yourservicebussendkey}** par **Envoyer** la clé de votre file d’attente. Vous avez précédemment noté espace de noms et **écouter** des valeurs de clé dans la section [configurer un compte de stockage Azure et une file d’attente Bus des services](#provision-an-azure-storage-account-and-a-service-bus-queue) :

    ```
    private final static String serviceBusNamespace = "{yourservicebusnamespace}";
    private final static String serviceBusSasKeyName = "send";
    private final static String serviceBusSASKey = "{yourservicebussendkey}";
    private final static String serviceBusRootUri = ".servicebus.windows.net";
    ```

5. Ajoutez les variables de niveau classe suivantes à la classe **d’application** . Remplacez **{youreventhubcompatibleendpoint}** par la valeur de point de terminaison compatible événement concentrateur. La valeur de point de terminaison ressemble à **..., son espace de noms** vous ne devez supprimer la **petite : / /** préfixe et le suffixe **.servicebus.windows.net/** . Remplacez **{youreventhubcompatiblename}** par le nom de l’événement concentrateur compatible. Remplacez **{youriothubkey}** avec la touche **iothubowner** . Vous avez noté des valeurs suivantes dans la [créer un concentrateur IoT] [ lnk-create-an-iot-hub] section dans le didacticiel *prise en main Azure IoT concentrateur pour Java* :

    ```
    private final static String consumerGroupName = "$Default";
    private final static String namespaceName = "{youreventhubcompatibleendpoint}";
    private final static String eventHubName = "{youreventhubcompatiblename}";
    private final static String sasKeyName = "iothubowner";
    private final static String sasKey = "{youriothubkey}";
    ```

6. Modifier la signature de la méthode **main** comme suit :

    ```
    public static void main(String args[]) throws InvalidKeyException,
      URISyntaxException, StorageException {
    }
    ```

7. Ajoutez le code suivant à la méthode **principale** pour obtenir une référence au conteneur blob qui stocke les messages :

    ```
    System.out.println("Process D2C messages using EventProcessorHost");
    CloudStorageAccount account = CloudStorageAccount
        .parse(storageConnectionString);
    CloudBlobClient client = account.createCloudBlobClient();
    EventProcessor.blobContainer = client
        .getContainerReference("d2cjavatutorial");
    EventProcessor.blobContainer.createIfNotExists();
    ```

8. Ajoutez le code suivant à la méthode **principale** pour obtenir une référence au service Bus des services :

    ```
    Configuration config = ServiceBusConfiguration
        .configureWithSASAuthentication(serviceBusNamespace,
            serviceBusSasKeyName, serviceBusSASKey, serviceBusRootUri);
    EventProcessor.serviceBusContract = ServiceBusService.create(config);
    ```

9. Dans la méthode **principale** , configurez et créez une instance de **EventProcessorHost** . L’option **setInvokeProcessorAfterReceiveTimeout** garantit que l’instance **EventProcessorHost** appelle la méthode **onEvents** dans l’interface **IEventProcessor** même lorsqu’il n’y a aucun message à traiter. Puis la méthode **onEvents** appelle toujours la méthode **AppendAndCheckPoint** lors de l’expiration.

    ```
    ConnectionStringBuilder eventHubConnectionString = new ConnectionStringBuilder(
        namespaceName, eventHubName, sasKeyName, sasKey);
    EventProcessorHost host = new EventProcessorHost(eventHubName,
        consumerGroupName, eventHubConnectionString.toString(),
        storageConnectionString);
    EventProcessorOptions options = new EventProcessorOptions();
    options.setExceptionNotification(new ErrorNotificationHandler());
    options.setInvokeProcessorAfterReceiveTimeout(true);
    ```

10. Dans la méthode **principale** , enregistrer la mise en œuvre **IEventProcessor** avec l’instance **EventProcessorHost** :

    ```
    try {
      System.out.println("Registering host named " + host.getHostName());
      host.registerEventProcessor(EventProcessor.class, options).get();
    } catch (Exception e) {
      System.out.print("Failure while registering: ");
      if (e instanceof ExecutionException) {
        Throwable inner = e.getCause();
        System.out.println(inner.toString());
      } else {
        System.out.println(e.toString());
      }
      System.out.println(e.toString());
    }
    ```

11. Enfin, ajouter une logique à la méthode **principale** pour arrêter l’instance **EventProcessorHost** :

    ```
    System.out.println("Press enter to stop");
    try {
      System.in.read();
      host.unregisterEventProcessor();

      System.out.println("Calling forceExecutorShutdown");
      EventProcessorHost.forceExecutorShutdown(120);
    } catch (Exception e) {
      System.out.println(e.toString());
      e.printStackTrace();
    }

    System.out.println("End of sample");
    ```

12. Enregistrez et fermez le fichier process-d2c-messages\src\main\java\com\mycompany\app\App.java.

13. Pour créer l’application **d2c-traiter les messages** à l’aide de Maven, exécutez la commande suivante à l’invite de commandes dans le dossier d2c-traitement des messages :

    ```
    mvn clean package -DskipTests
    ```

## <a name="receive-interactive-messages"></a>Recevoir des messages interactifs

Dans cette section, vous écrivez une application console Java qui reçoit les messages interactifs à partir de la file d’attente Bus des services.

La première tâche consiste à ajouter un projet Maven appelé **processus interactifs messages** qui reçoit les messages envoyés dans la file d’attente Bus de Service à partir des instances **EventProcessor** .

1. Dans le dossier iot java-démarrer que vous avez créé dans le didacticiel [prise en main IoT concentrateur] , créez un projet de Maven appelé **processus interactifs messages** à l’aide de la commande suivante à votre invite de commandes. Remarque : il s’agit d’une commande unique, longue :

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=process-interactive-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. À votre invite de commandes, naviguez vers le nouveau dossier de messages interactifs processus.

3. À l’aide d’un éditeur de texte, ouvrez le fichier pom.xml dans le dossier processus interactifs messages et ajoutez les dépendances suivantes pour le nœud **dépendances** . Cette dépendance permet d’utiliser le package azure servicebus dans votre application pour interagir avec votre file d’attente Bus des services :

    ```
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-servicebus</artifactId>
      <version>0.9.4</version>
    </dependency>
    ```

4. Enregistrez et fermez le fichier pom.xml.

La tâche suivante consiste à ajouter du code pour récupérer des messages dans la file d’attente Bus des services.

1. Utilisez un éditeur de texte pour ouvrir le fichier process-interactive-messages\src\main\java\com\mycompany\app\App.java.

2. Ajoutez le code suivant `import` instructions pour le fichier :

    ```
    import java.io.IOException;
    import java.util.concurrent.ExecutorService;
    import java.util.concurrent.Executors;

    import com.microsoft.windowsazure.Configuration;
    import com.microsoft.windowsazure.exception.ServiceException;
    import com.microsoft.windowsazure.services.servicebus.*;
    import com.microsoft.windowsazure.services.servicebus.models.*;
    ```

3. Ajoutez les variables de niveau classe suivantes à la classe **application** et remplacez **{yourservicebusnamespace}** avec votre espace de noms de Service Bus et **{yourservicebuslistenkey}** par clé **écouter** votre file d’attente. Vous avez précédemment noté espace de noms et **écouter** des valeurs de clé dans la section [configurer un compte de stockage Azure et une file d’attente Bus des services](#provision-an-azure-storage-account-and-a-service-bus-queue) :

    ```
    private final static String serviceBusNamespace = "{yourservicebusnamespace}";
    private final static String serviceBusSasKeyName = "listen";
    private final static String serviceBusSASKey = "{yourservicebuslistenkey}";
    private final static String serviceBusRootUri = ".servicebus.windows.net";
    private final static String queueName = "d2ctutorial";
    private static ServiceBusContract service = null;
    ```

4. Ajouter la classe imbriquée suivante à la classe **App** de recevoir les messages de la file d’attente :

    ```
    private static class MessageReceiver implements Runnable {
      public void run() {
        ReceiveMessageOptions opts = ReceiveMessageOptions.DEFAULT;
        try {
          while (true) {
            ReceiveQueueMessageResult resultQM = service.receiveQueueMessage(
                queueName, opts);
            BrokeredMessage message = resultQM.getValue();
            if (message != null && message.getMessageId() != null) {
              System.out.println("MessageID: " + message.getMessageId());
              System.out.print("From queue: ");
              byte[] b = new byte[200];
              String s = null;
              int numRead = message.getBody().read(b);
              while (-1 != numRead) {
                s = new String(b);
                s = s.trim();
                System.out.print(s);
                numRead = message.getBody().read(b);
              }
              System.out.println();
            } else {
              Thread.sleep(1000);
            }
          }
        } catch (InterruptedException e) {
          System.out.println("Finished.");
        } catch (ServiceException e) {
          System.out.println("ServiceException: " + e.getMessage());
        } catch (IOException e) {
          System.out.println("IOException: " + e.getMessage());
        }
      }
    }
    ```

5. Modifier la signature de la méthode **main** comme suit :

    ```
    public static void main(String args[]) throws ServiceException, IOException {
    }
    ```

6. Dans la méthode **principale** , ajoutez le code suivant pour démarrer l’écoute pour les nouveaux messages :

    ```
    System.out.println("Process interactive messages");

    Configuration config = ServiceBusConfiguration
        .configureWithSASAuthentication(serviceBusNamespace,
            serviceBusSasKeyName, serviceBusSASKey, serviceBusRootUri);
    service = ServiceBusService.create(config);

    MessageReceiver receiver = new MessageReceiver();

    ExecutorService executor = Executors.newFixedThreadPool(2);
    executor.execute(receiver);

    System.out.println("Press ENTER to exit.");
    System.in.read();
    executor.shutdownNow();
    ```

7. Enregistrez et fermez le fichier process-interactive-messages\src\main\java\com\mycompany\app\App.java.

8. Pour créer l’application de **processus interactifs messages** à l’aide de Maven, exécutez la commande suivante à l’invite de commandes dans le dossier processus interactifs messages :

    ```
    mvn clean package -DskipTests
    ```

## <a name="run-the-applications"></a>Exécuter les applications

Vous êtes maintenant prêt à exécuter les trois applications.

1.  Pour exécuter l’application de **processus interactifs messages** , dans une invite de commandes ou shell, accédez au dossier processus interactifs messages et exécutez la commande suivante :

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Exécuter le processus interactifs messages][processinteractive]

2.  Pour exécuter l’application **d2c-traitement des messages** , dans une invite de commandes ou shell, accédez au dossier d2c-traitement des messages et exécutez la commande suivante :

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Exécuter les messages de d2c de processus][processd2c]

3.  Pour exécuter l’application **smart device simulé** , dans une invite de commandes ou shell, accédez au dossier simulé à l’appareil et exécutez la commande suivante :

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Exécuter périphérique simulé][simulateddevice]

> [AZURE.NOTE] Pour afficher les mises à jour dans votre blob, vous devrez peut-être réduire la constante **MAX_BLOCK_SIZE** de la classe **StoreEventProcessor** à une valeur inférieure, par exemple **1024**. Cette modification est utile, car il prend un certain temps pour atteindre la limite de taille de bloc avec les données envoyées par le périphérique simulé. Avec une plus petite taille de bloc, vous n’avez pas besoin d’attendre si longtemps pour afficher le blob créées et mis à jour. Toutefois, à l’aide d’une plus grande taille de bloc rend l’application plus adaptée.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris comment traiter fiable point de données et les messages d’appareil-nuage interactive à l’aide de la classe [EventProcessorHost] .

[Comment envoyer des messages de cloud vers le périphérique avec concentrateur IoT] [ lnk-c2d] vous montre comment envoyer des messages à vos périphériques à partir de votre serveur principal.

Pour voir des exemples de solutions de bout en bout complètes qui utilisent IoT concentrateur, voir [Azure IoT Suite][lnk-suite].

Pour plus d’informations sur le développement de solutions avec concentrateur IoT, consultez le [Guide du développeur IoT concentrateur].

<!-- Images. -->
[simulateddevice]: ./media/iot-hub-java-java-process-d2c/runsimulateddevice.png
[processinteractive]: ./media/iot-hub-java-java-process-d2c/runprocessinteractive.png
[processd2c]: ./media/iot-hub-java-java-process-d2c/runprocessd2c.png

[30]: ./media/iot-hub-java-java-process-d2c/createqueue2.png
[31]: ./media/iot-hub-java-java-process-d2c/createqueue3.png

<!-- Links -->

[Stockage d’objets blob Azure]: ../storage/storage-dotnet-how-to-use-blobs.md
[Données Azure usine]: https://azure.microsoft.com/documentation/services/data-factory/
[HDInsight (Hadoop)]: https://azure.microsoft.com/documentation/services/hdinsight/
[File d’attente Bus des services]: ../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md

[Guide du développeur IoT concentrateur Azure - appareil vers le cloud]: iot-hub-devguide-messaging.md

[Stockage Azure]: https://azure.microsoft.com/documentation/services/storage/
[Bus des services Azure]: https://azure.microsoft.com/documentation/services/service-bus/

[Guide du développeur IoT concentrateur]: iot-hub-devguide.md
[Prise en main IoT concentrateur]: iot-hub-java-java-getstarted.md
[Centre de développement IoT Azure]: https://azure.microsoft.com/develop/iot
[lnk-service-fabric]: https://azure.microsoft.com/documentation/services/service-fabric/
[lnk-stream-analytics]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-event-hubs]: https://azure.microsoft.com/documentation/services/event-hubs/
[Gestion des erreurs transitoires]: https://msdn.microsoft.com/library/hh675232.aspx

<!-- Links -->
[À propos du stockage Azure]: ../storage/storage-create-storage-account.md#create-a-storage-account
[Prise en main avec Hubs d’événement]: ../event-hubs/event-hubs-java-ephjava-getstarted.md
[Extensibilité élevées de stockage Azure instructions]: ../storage/storage-scalability-targets.md
[Azure Block Blobs]: https://msdn.microsoft.com/library/azure/ee691964.aspx
[Event Hubs]: ../event-hubs/event-hubs-overview.md
[EventProcessorHost]: https://github.com/Azure/azure-event-hubs/tree/master/java/azure-eventhubs-eph
[Gestion des erreurs transitoires]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-classic-portal]: https://manage.windowsazure.com
[lnk-c2d]: iot-hub-java-java-process-d2c.md
[lnk-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/java-devbox-setup.md
[lnk-create-an-iot-hub]: iot-hub-java-java-getstarted.md#create-an-iot-hub