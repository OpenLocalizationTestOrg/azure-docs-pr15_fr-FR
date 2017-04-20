<properties
    pageTitle="Envoyer des messages de cloud vers le périphérique avec concentrateur IoT | Microsoft Azure"
    description="Suivez ce didacticiel pour apprendre à envoyer des messages cloud vers le périphérique à l’aide d’Azure IoT concentrateur avec Java."
    services="iot-hub"
    documentationCenter="nodejs"
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="javascript"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/23/2016"
     ms.author="dobett"/>

# <a name="tutorial-how-to-send-cloud-to-device-messages-with-iot-hub-and-nodejs"></a>Didacticiel : Comment envoyer des messages de cloud vers le périphérique avec concentrateur IoT et Node.js

[AZURE.INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## <a name="introduction"></a>Introduction

Concentrateur IoT Azure est un service entièrement géré qui vous aide à activer la communication bidirectionnelle fiable et sécurisée entre millions d’appareils IoT et mettre fin à une application précédent. Le didacticiel [prise en main IoT concentrateur] montre comment créer un concentrateur IoT, mise en service d’une identité appareil qu’il contient et un périphérique simulé qui envoie des messages de l’appareil-nuage de code.

Ce didacticiel s’appuie sur la [prise en main IoT concentrateur]. Il vous explique comment procéder pour :

- À partir de votre application cloud principale, envoyer des messages de cloud vers le périphérique à un seul périphérique via IoT concentrateur.
- Recevoir des messages de cloud vers le périphérique sur un appareil.
- À partir de votre cloud application back-end, demander un accusé de réception (*commentaires*) pour les messages envoyés à un appareil IoT concentrateur.

Vous pouvez trouver plus d’informations sur les messages de cloud vers le périphérique dans le [Guide du développeur IoT concentrateur][IoT Hub Developer Guide - C2D].

À la fin de ce didacticiel, vous exécutez deux applications console Node.js :

* **SimulatedDevice**, une version modifiée de l’application créée dans [prise en main IoT concentrateur], qui se connecte à votre concentrateur IoT et reçoit des messages cloud vers le périphérique.
* **SendCloudToDeviceMessage**, qui envoie un message cloud vers le périphérique à l’appareil simulé via IoT concentrateur et puis reçoit son accusé de réception.

> [AZURE.NOTE] Concentrateur IoT prend en charge SDK pour les nombreuses langues (y compris C, Java et Javascript) via Azure IoT appareil SDK et les plateformes d’appareil. Pour obtenir des instructions détaillées sur la connexion de votre appareil au code de ce didacticiel et généralement à Azure IoT concentrateur, voir le [Centre de développement IoT Azure].

Pour effectuer ce didacticiel, vous devez les éléments suivants :

+ Version Node.js 0.10.x ou version ultérieure.

+ Un compte Azure actif. (Si vous n’avez pas un compte, vous pouvez créer un [compte gratuit] [ lnk-free-trial] en quelques minutes.)

## <a name="receive-messages-on-the-simulated-device"></a>Recevoir des messages sur le périphérique simulé

Dans cette section, vous modifiez l’application périphérique simulé vous avez créé dans [prise en main IoT concentrateur] pour recevoir des messages de cloud vers le périphérique à partir du hub IoT.

1. À l’aide d’un éditeur de texte, ouvrez le fichier SimulatedDevice.js.

2. Modifiez la fonction **connectCallback** pour gérer les messages envoyés à partir de IoT concentrateur. Dans cet exemple, le périphérique appelle toujours la fonction **complète** pour informer IoT concentrateur que le message a été traitée. Votre nouvelle version de la fonction **connectCallback** ressemble à ceci :

    ```
    var connectCallback = function (err) {
      if (err) {
        console.log('Could not connect: ' + err);
      } else {
        console.log('Client connected');
        client.on('message', function (msg) {
          console.log('Id: ' + msg.messageId + ' Body: ' + msg.data);
          client.complete(msg, printResultFor('completed'));
        });
        // Create a message and send it to the IoT Hub every second
        setInterval(function(){
            var windSpeed = 10 + (Math.random() * 4);
            var data = JSON.stringify({ deviceId: 'myFirstNodeDevice', windSpeed: windSpeed });
            var message = new Message(data);
            console.log("Sending message: " + message.getData());
            client.sendEvent(message, printResultFor('send'));
        }, 1000);
      }
    };
    ```

    > [AZURE.NOTE] Si vous utilisez HTTP au lieu de MQTT ou AMQP comme type de transport, l’instance **DeviceClient** vérifie les messages à partir du IoT Hub rarement (moins de 25 minutes). Pour plus d’informations sur les différences entre prise en charge MQTT, AMQP et HTTP et concentrateur IoT la limitation, voir le [Guide du développeur IoT concentrateur][IoT Hub Developer Guide - C2D].

## <a name="send-a-cloud-to-device-message"></a>Envoyer un message cloud vers le périphérique

Dans cette section, vous créez une application console Node.js qui envoie des messages de cloud vers le périphérique à l’application de périphérique simulé. Vous devez l’Id de l’appareil que vous avez ajouté dans le didacticiel [prise en main IoT concentrateur] de l’appareil. Vous devez également la chaîne de connexion pour votre plateforme IoT que vous pouvez trouver dans le [portail Azure].

1. Créer un dossier vide appelé **sendcloudtodevicemessage**. Dans le dossier **sendcloudtodevicemessage** , créez un fichier package.json à l’aide de la commande suivante à votre invite de commandes. Accepter toutes les valeurs par défaut :

    ```
    npm init
    ```

2. À votre invite de commande dans le dossier **sendcloudtodevicemessage** , exécutez la commande suivante pour installer le package **azure iothub** :

    ```
    npm install azure-iothub --save
    ```

3. À l’aide d’un éditeur de texte, créez un nouveau fichier **SendCloudToDeviceMessage.js** dans le dossier **sendcloudtodevicemessage** .

4. Ajoutez le code suivant `require` instructions au début du fichier **SendCloudToDeviceMessage.js** :

    ```
    'use strict';
    
    var Client = require('azure-iothub').Client;
    var Message = require('azure-iot-common').Message;
    ```

5. Ajoutez le code suivant au fichier **SendCloudToDeviceMessage.js** . Remplacez la valeur d’espace réservé chaîne connexion par la chaîne de connexion pour le hub IoT que vous avez créé dans le didacticiel [prise en main IoT concentrateur] . Remplacez l’espace réservé appareil cible par l’id de l’appareil que vous avez ajouté dans le didacticiel [prise en main IoT concentrateur] périphérique :

    ```
    var connectionString = '{iot hub connection string}';
    var targetDevice = '{device id}';

    var serviceClient = Client.fromConnectionString(connectionString);
    ```

6. Ajoutez la fonction suivante pour imprimer les résultats de l’opération à la console :

    ```
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```

7. Ajoutez la fonction suivante pour afficher les messages de commentaires de remise dans la console :

    ```
    function receiveFeedback(err, receiver){
      receiver.on('message', function (msg) {
        console.log('Feedback message:')
        console.log(msg.getData().toString('utf-8'));
      });
    }
    ```

8. Ajoutez le code suivant pour envoyer un message à votre appareil et gérer le message de commentaires lorsque le périphérique reconnaît le message cloud vers le périphérique :

    ```
    serviceClient.open(function (err) {
      if (err) {
        console.error('Could not connect: ' + err.message);
      } else {
        console.log('Service client connected');
        serviceClient.getFeedbackReceiver(receiveFeedback);
        var message = new Message('Cloud to device message.');
        message.ack = 'full';
        message.messageId = "My Message ID";
        console.log('Sending message: ' + message.getData());
        serviceClient.send(targetDevice, message, printResultFor('send'));
      }
    });
    ```

7. Enregistrez et fermez le fichier **SendCloudToDeviceMessage.js** .

## <a name="run-the-applications"></a>Exécuter les applications

Vous êtes maintenant prêt à exécuter les applications.

1. À l’invite dans le dossier **simulateddevice** , exécutez la commande suivante pour envoyer de télémétrie à IoT concentrateur et à recevoir des messages de cloud vers le périphérique :

    ```
    node SimulatedDevice.js 
    ```

    ![Exécuter l’application périphérique simulé][img-simulated-device]

2. À une invite de commande dans le dossier **sendcloudtodevicemessage** , exécutez la commande suivante pour envoyer un message cloud vers le périphérique et attendre que les commentaires d’accusé de réception :

    ```
    node SendCloudToDeviceMessage.js 
    ```

    ![Exécuter l’application pour envoyer la commande c2d][img-send-command]

    > [AZURE.NOTE] Pour facilité plus de, ce didacticiel ne pas implémenter n’importe quelle stratégie de réessayer. Dans le code de production, vous devez implémenter des stratégies de nouvelles tentatives (par exemple, intervalle exponentielle), comme indiqué dans l’article MSDN [Transitoires de gestion des pannes].

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à envoyer et recevoir des messages de cloud vers le périphérique. 

Pour voir des exemples de solutions de bout en bout complètes qui utilisent IoT concentrateur, voir [Azure IoT Suite].

Pour plus d’informations sur le développement de solutions avec concentrateur IoT, consultez le [Guide du développeur IoT concentrateur].

<!-- Images -->
[img-simulated-device]: media/iot-hub-node-node-c2d/receivec2d.png
[img-send-command]:  media/iot-hub-node-node-c2d/sendc2d.png

<!-- Links -->

[Prise en main IoT concentrateur]: iot-hub-node-node-getstarted.md
[IoT Hub Developer Guide - C2D]: iot-hub-devguide-messaging.md
[Guide du développeur IoT concentrateur]: iot-hub-devguide.md
[Centre de développement IoT Azure]: http://www.azure.com/develop/iot
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md
[Gestion des erreurs transitoires]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Portail Azure]: https://portal.azure.com
[Suite IoT Azure]: https://azure.microsoft.com/documentation/suites/iot-suite/