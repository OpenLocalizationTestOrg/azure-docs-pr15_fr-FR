<properties
    pageTitle="Azure IoT concentrateur pour la prise en main de Node.js | Microsoft Azure"
    description="Concentrateur IoT Azure avec Node.js prise en main didacticiel. Utilisez Azure IoT concentrateur et Node.js avec les kits de développement Microsoft Azure IoT à mettre en œuvre une solution Internet des objets."
    services="iot-hub"
    documentationCenter="nodejs"
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="javascript"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/12/2016"
     ms.author="dobett"/>

# <a name="get-started-with-azure-iot-hub-for-nodejs"></a>Prise en main Azure IoT concentrateur pour Node.js

[AZURE.INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

À la fin de ce didacticiel, vous avez trois applications console Node.js :

* **CreateDeviceIdentity.js**, qui crée une identité de l’appareil et une clé de sécurité associées pour connecter votre périphérique simulé.
* **ReadDeviceToCloudMessages.js**, qui affiche la télémétrie envoyé par votre périphérique simulé.
* **SimulatedDevice.js**, qui se connecte à votre concentrateur IoT à l’identité de l’appareil créée précédemment et envoie un message de télémétrie chaque deuxième à l’aide du protocole AMQP.

> [AZURE.NOTE] L’article [IoT concentrateur SDK] [ lnk-hub-sdks] fournit des informations sur les différents kits de développement que vous pouvez utiliser pour créer les deux applications à s’exécuter sur des appareils mobiles et votre solution back end.

Pour effectuer ce didacticiel, vous devez les éléments suivants :

+ Version Node.js 0.10.x ou version ultérieure.

+ Un compte Azure actif. (Si vous n’avez pas un compte, vous pouvez créer un [compte gratuit] [ lnk-free-trial] en quelques minutes.)

[AZURE.INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Vous venez de créer votre concentrateur IoT. Vous avez le nom d’hôte IoT concentrateur et la chaîne de connexion IoT concentrateur dont vous avez besoin pour effectuer le reste de ce didacticiel.

## <a name="create-a-device-identity"></a>Création d’une identité de l’appareil

Dans cette section, vous créez une application console Node.js qui crée une identité de périphérique dans le Registre identité dans votre hub IoT. Un périphérique ne peut pas se connecter à IoT concentrateur, à moins qu’une entrée dans le Registre identité appareil. Reportez-vous à la section **Périphérique identité Registre** du [Guide du développeur IoT concentrateur] [ lnk-devguide-identity] pour plus d’informations. Lorsque vous exécutez cette application console, il génère un ID d’appareil unique et une clé que votre appareil peut utiliser pour identifier lorsqu’il envoie des messages de l’appareil-nuage à IoT concentrateur.

1. Créer un nouveau dossier vide nommé **createdeviceidentity**. Dans le dossier **createdeviceidentity** , créez un fichier package.json à l’aide de la commande suivante à votre invite de commandes. Accepter toutes les valeurs par défaut :

    ```
    npm init
    ```

2. À votre invite de commande dans le dossier **createdeviceidentity** , exécutez la commande suivante pour installer le package de Service SDK **azure iothub** :

    ```
    npm install azure-iothub --save
    ```

3. À l’aide d’un éditeur de texte, créez un fichier **CreateDeviceIdentity.js** dans le dossier **createdeviceidentity** .

4. Ajoutez le code suivant `require` instruction au début du fichier **CreateDeviceIdentity.js** :

    ```
    'use strict';
    
    var iothub = require('azure-iothub');
    ```

5. Ajoutez le code suivant dans le fichier **CreateDeviceIdentity.js** et remplacez la valeur de l’espace réservé par la chaîne de connexion pour le hub IoT que vous avez créé dans la section précédente : 

    ```
    var connectionString = '{iothub connection string}';
    
    var registry = iothub.Registry.fromConnectionString(connectionString);
    ```

6. Ajoutez le code suivant pour créer une définition de périphérique dans le Registre identité appareil dans votre hub IoT. Ce code crée un périphérique si l’id de périphérique n’existe pas dans le Registre, dans le cas contraire, elle renvoie la clé du périphérique existant :

    ```
    var device = new iothub.Device(null);
    device.deviceId = 'myFirstNodeDevice';
    registry.create(device, function(err, deviceInfo, res) {
      if (err) {
        registry.get(device.deviceId, printDeviceInfo);
      }
      if (deviceInfo) {
        printDeviceInfo(err, deviceInfo, res)
      }
    });

    function printDeviceInfo(err, deviceInfo, res) {
      if (deviceInfo) {
        console.log('Device id: ' + deviceInfo.deviceId);
        console.log('Device key: ' + deviceInfo.authentication.SymmetricKey.primaryKey);
      }
    }
    ```

7. Enregistrez et fermez le fichier **CreateDeviceIdentity.js** .

8. Pour exécuter l’application **createdeviceidentity** , exécutez la commande suivante à l’invite de commandes dans le dossier createdeviceidentity :

    ```
    node CreateDeviceIdentity.js 
    ```

9. Prenez note de **l’id de périphérique** et la **clé du périphérique**. Vous avez besoin de ces valeurs lorsque vous créez une application qui se connecte à concentrateur IoT en tant que périphérique.

> [AZURE.NOTE] Le Registre d’identité IoT concentrateur stocke uniquement des identités des appareils pour permettre un accès sécurisé au concentrateur. Il stocke les ID de l’équipement et clés à utiliser en tant que les informations d’identification de sécurité et un indicateur activé ou désactivé que vous pouvez utiliser pour désactiver l’accès pour un périphérique individuel. Si votre application a besoin stocker les autres métadonnées spécifiques à l’appareil, elle doit utiliser un magasin spécifique à l’application. Reportez-vous au [Guide de développeur IoT concentrateur] [ lnk-devguide-identity] pour plus d’informations.

## <a name="receive-device-to-cloud-messages"></a>Recevoir des messages de l’appareil-nuage

Dans cette section, vous créez une application console Node.js qui lit les messages de l’appareil-nuage de IoT concentrateur. Un concentrateur IoT expose un [Événement Hubs][lnk-event-hubs-overview]-point de terminaison compatible pour pouvoir lire les messages de l’appareil-nuage. Pour simplifier les choses, ce didacticiel crée un lecteur de base qui n’est pas adapté à un déploiement haut débit. Le [traitement des messages appareil-nuage] [ lnk-process-d2c-tutorial] didacticiel vous montre comment traiter les messages appareil-nuage à l’échelle. La [Prise en main événement Hubs] [ lnk-eventhubs-tutorial] didacticiel fournit des informations plus sur la façon de traiter les messages à partir de l’événement Hubs et ne s’applique aux points de terminaison compatibles IoT concentrateur événement concentrateur.

> [AZURE.NOTE] Le point de terminaison compatible événement concentrateur pour la lecture des messages de l’appareil-nuage toujours utilise le protocole AMQP.

1. Créer un nouveau dossier vide nommé **readdevicetocloudmessages**. Dans le dossier **readdevicetocloudmessages** , créez un fichier package.json à l’aide de la commande suivante à votre invite de commandes. Accepter toutes les valeurs par défaut :

    ```
    npm init
    ```

2. À votre invite de commande dans le dossier **readdevicetocloudmessages** , exécutez la commande suivante pour installer le package **azure-événement-hubs** :

    ```
    npm install azure-event-hubs --save
    ```

3. À l’aide d’un éditeur de texte, créez un fichier **ReadDeviceToCloudMessages.js** dans le dossier **readdevicetocloudmessages** .

4. Ajoutez le code suivant `require` instructions au début du fichier **ReadDeviceToCloudMessages.js** :

    ```
    'use strict';

    var EventHubClient = require('azure-event-hubs').Client;
    ```

5. Ajoutez la déclaration des variables suivante et remplacez la valeur de l’espace réservé par la chaîne de connexion pour votre plateforme IoT :

    ```
    var connectionString = '{iothub connection string}';
    ```

6. Ajoutez les deux fonctions suivantes qui imprimer le résultat dans la console :

    ```
    var printError = function (err) {
      console.log(err.message);
    };

    var printMessage = function (message) {
      console.log('Message received: ');
      console.log(JSON.stringify(message.body));
      console.log('');
    };
    ```

7. Ajoutez le code suivant pour créer **EventHubClient**, ouvrez la connexion à votre concentrateur IoT et créer un récepteur pour chaque partition. Cette application utilise un filtre lorsqu’il crée un récepteur afin que le destinataire lit uniquement les messages envoyés à concentrateur IoT après que le récepteur démarre en cours d’exécution. Ce filtre est utile dans un environnement de test afin de voir uniquement l’ensemble actuel des messages. Dans un environnement de production, votre code devez vous assurer qu’il traite tous les messages - voir [comment traiter les messages d’appareil-nuage IoT concentrateur] [ lnk-process-d2c-tutorial] didacticiels pour plus d’informations :

    ```
    var client = EventHubClient.fromConnectionString(connectionString);
    client.open()
        .then(client.getPartitionIds.bind(client))
        .then(function (partitionIds) {
            return partitionIds.map(function (partitionId) {
                return client.createReceiver('$Default', partitionId, { 'startAfterTime' : Date.now()}).then(function(receiver) {
                    console.log('Created partition receiver: ' + partitionId)
                    receiver.on('errorReceived', printError);
                    receiver.on('message', printMessage);
                });
            });
        })
        .catch(printError);
    ```

8. Enregistrez et fermez le fichier **ReadDeviceToCloudMessages.js** .

## <a name="create-a-simulated-device-app"></a>Créer une application de périphérique simulé

Dans cette section, vous créez une application console Node.js qui simule un appareil qui envoie des messages de l’appareil-nuage à un concentrateur IoT.

1. Créer un nouveau dossier vide nommé **simulateddevice**. Dans le dossier **simulateddevice** , créez un fichier package.json à l’aide de la commande suivante à votre invite de commandes. Accepter toutes les valeurs par défaut :

    ```
    npm init
    ```

2. À votre invite de commande dans le dossier **simulateddevice** , exécutez la commande suivante pour installer le **périphérique d’iot azure** SDK appareil package et **azure-iot-appareil-amqp** :

    ```
    npm install azure-iot-device azure-iot-device-amqp --save
    ```

3. À l’aide d’un éditeur de texte, créez un nouveau fichier **SimulatedDevice.js** dans le dossier **simulateddevice** .

4. Ajoutez le code suivant `require` instructions au début du fichier **SimulatedDevice.js** :

    ```
    'use strict';

    var clientFromConnectionString = require('azure-iot-device-amqp').clientFromConnectionString;
    var Message = require('azure-iot-device').Message;
    ```

5. Ajouter une variable **connectionString** et utilisez-le pour créer un client de l’appareil. Remplacez **{youriothostname}** avec le nom du concentrateur IoT vous avez créé la section *Création d’un concentrateur IoT* . Remplacez **{yourdevicekey}** par la valeur de clé appareil générées dans la section *Création d’une identité de l’appareil* :

    ```
    var connectionString = 'HostName={youriothostname};DeviceId=myFirstNodeDevice;SharedAccessKey={yourdevicekey}';
    
    var client = clientFromConnectionString(connectionString);
    ```

6. Ajoutez la fonction suivante pour afficher le résultat de l’application :

    ```
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```

7. Créer un rappel et utiliser la fonction **setInterval** pour envoyer un message à votre concentrateur IoT toutes les secondes :

    ```
    var connectCallback = function (err) {
      if (err) {
        console.log('Could not connect: ' + err);
      } else {
        console.log('Client connected');

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

8. Ouvrez la connexion à votre concentrateur IoT et commencer à envoyer des messages :

    ```
    client.open(connectCallback);
    ```

9. Enregistrez et fermez le fichier **SimulatedDevice.js** .

> [AZURE.NOTE] Pour simplifier les choses, ce didacticiel ne pas implémenter une stratégie de réessayer. Dans le code de production, vous devez implémenter des stratégies de nouvelles tentatives (par exemple, un intervalle exponentielle), comme indiqué dans l’article MSDN [Transitoires de gestion des pannes][lnk-transient-faults].


## <a name="run-the-applications"></a>Exécuter les applications

Vous êtes maintenant prêt à exécuter les applications.

1. À une invite de commande dans le dossier **readdevicetocloudmessages** , exécutez la commande suivante pour commencer la surveillance votre concentrateur IoT :

    ```
    node ReadDeviceToCloudMessages.js 
    ```

    ![Node.js application cliente de service IoT concentrateur pour contrôler les messages de l’appareil-nuage][7]

2. À une invite de commande dans le dossier **simulateddevice** , exécutez la commande suivante pour commencer à envoyer des données de télémétrie à votre concentrateur IoT :

    ```
    node SimulatedDevice.js
    ```

    ![Application Node.js IoT concentrateur appareil client pour envoyer des messages de l’appareil-nuage][8]

3. La vignette de **l’utilisation** du [portail Azure] [ lnk-portal] affiche le nombre de messages envoyés au concentrateur :

    ![Azure portail l’utilisation vignette nombre de messages envoyés à IoT concentrateur][43]

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous configuré un nouveau concentrateur IoT dans le portail Azure et puis créée une identité appareil dans identité Registre son. Vous avez utilisé cette identité appareil pour activer l’application périphérique simulé envoyer des messages appareil-nuage au concentrateur. Vous avez également créé une application qui affiche les messages reçus par le hub. 

Pour continuer la prise en main IoT concentrateur et Explorer d’autres scénarios IoT, voir :

- [Connecter votre périphérique][lnk-connect-device]
- [Mise en route de la gestion des appareils][lnk-device-management]
- [Prise en main du Kit de développement de passerelle IoT][lnk-gateway-SDK]

Pour découvrir comment étendre votre solution IoT et traiter des messages de périphérique-nuage à l’échelle, consultez le [traitement des messages appareil-nuage] [ lnk-process-d2c-tutorial] didacticiel.

<!-- Images. -->
[7]: ./media/iot-hub-node-node-getstarted/runapp1.png
[8]: ./media/iot-hub-node-node-getstarted/runapp2.png
[43]: ./media/iot-hub-csharp-csharp-getstarted/usage.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide-identity-registry.md
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-device-management]: iot-hub-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
