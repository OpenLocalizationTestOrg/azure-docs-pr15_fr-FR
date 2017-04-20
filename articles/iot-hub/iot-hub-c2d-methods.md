<properties
 pageTitle="Utiliser les méthodes directes | Microsoft Azure"
 description="Ce didacticiel vous montre comment utiliser les méthodes directes"
 services="iot-hub"
 documentationCenter=""
 authors="nberdy"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="10/05/2016"
 ms.author="nberdy"/>

# <a name="tutorial-use-direct-methods"></a>Didacticiel : Utiliser des méthodes directes

## <a name="introduction"></a>Introduction

Concentrateur IoT Azure est un service entièrement géré qui permet aux fiable et sécuriser les communications bidirectionnelle entre millions d’appareils IoT et une application back-end. Didacticiels précédents ([prise en main IoT concentrateur] et [Envoyer des messages de Cloud vers le périphérique avec concentrateur IoT]) illustrent la fonctionnalité de messagerie appareil-nuage et cloud vers le périphérique base du concentrateur IoT. Concentrateur IoT vous donne la possibilité d’appeler méthodes non durables sur les appareils à partir du cloud. Méthodes de représentent une interaction demande-réponse avec un appareil similaire à un appel HTTP dans la mesure où ils réussissent ou échouent immédiatement (après un délai d’attente défini par l’utilisateur) afin d’indiquer l’état de l’appel de l’utilisateur. [Appeler une méthode directe sur un appareil] [ lnk-devguide-methods] décrit les méthodes plus en détail et propose des conseils sur l’utilisation des méthodes par rapport aux messages cloud vers le périphérique.

Ce didacticiel vous explique comment procéder pour :

- Utiliser le portail Azure pour créer un concentrateur IoT et créer une identité appareil dans votre concentrateur IoT.
- Créer un périphérique simulé qui possède une méthode directe qui peut être appelée par le cloud.
- Créer une application console qui appelle une méthode directement sur le périphérique simulé via votre concentrateur IoT.

> [AZURE.NOTE] Pour l’instant, les méthodes directes sont accessibles uniquement à partir d’appareils qui se connectent à concentrateur IoT en utilisant le protocole MQTT. Reportez-vous à la [prise en charge MQTT] [ lnk-devguide-mqtt] article pour obtenir des instructions sur la conversion d’application appareil existante à utiliser MQTT.

À la fin de ce didacticiel, vous avez deux applications console Node.js :

* **CallMethodOnDevice.js**, qui appelle une méthode sur le périphérique simulé et affiche la réponse.
* **SimulatedDevice.js**, qui se connecte à votre concentrateur IoT avec l’identité de l’appareil créée précédemment et répond à la méthode appelée par le cloud.

> [AZURE.NOTE] L’article [IoT concentrateur SDK] [ lnk-hub-sdks] fournit des informations sur les différents kits de développement que vous pouvez utiliser pour créer les deux applications à s’exécuter sur des appareils mobiles et votre solution back end.

Pour effectuer ce didacticiel, vous devez les éléments suivants :

+ Version Node.js 0.10.x ou version ultérieure.

+ Un compte Azure actif. (Si vous n’avez pas un compte, vous pouvez créer un [compte gratuit] [ lnk-free-trial] en quelques minutes.)

[AZURE.INCLUDE [iot-hub-get-started-create-hub-pp](../../includes/iot-hub-get-started-create-hub-pp.md)]

## <a name="create-a-simulated-device-app"></a>Créer une application de périphérique simulé

Dans cette section, vous créez une application console Node.js qui répond à une méthode appelée par le cloud.

1. Créer un nouveau dossier vide nommé **simulateddevice**. Dans le dossier **simulateddevice** , créez un fichier package.json à l’aide de la commande suivante à votre invite de commandes. Accepter toutes les valeurs par défaut :

    ```
    npm init
    ```

2. À votre invite de commande dans le dossier **simulateddevice** , exécutez la commande suivante pour installer le **périphérique d’iot azure** SDK appareil package et **azure-iot-appareil-mqtt** :

    ```
    npm install azure-iot-device@dtpreview azure-iot-device-mqtt@dtpreview --save
    ```

3. À l’aide d’un éditeur de texte, créez un nouveau fichier **SimulatedDevice.js** dans le dossier **simulateddevice** .

4. Ajoutez le code suivant `require` instructions au début du fichier **SimulatedDevice.js** :

    ```
    'use strict';

    var Mqtt = require('azure-iot-device-mqtt').Mqtt;
    var DeviceClient = require('azure-iot-device').Client;
    ```

5. Ajouter une variable **connectionString** et utilisez-le pour créer un client de l’appareil. Remplacez **{chaîne de connexion appareil}** par la chaîne de connexion générées dans la section *Création d’une identité de l’appareil* :

    ```
    var connectionString = '{device connection string}';
    var client = DeviceClient.fromConnectionString(connectionString, Mqtt);
    ```

6. Ajoutez la fonction suivante pour mettre en œuvre la méthode sur l’appareil :

    ```
    function onWriteLine(request, response) {
        console.log(request.payload);

        response.send(200, 'Input was written to log.', function(err) {
            if(err) {
                console.error('An error ocurred when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.' );
            }
        });
    }
    ```

7. Ouvrir la connexion à votre concentrateur IoT et a commencé initialisation que l’auditeur méthode :

    ```
    client.open(function(err) {
        if (err) {
            console.error('could not open IotHub client');
        }  else {
            console.log('client opened');
            client.onDeviceMethod('writeLine', onWriteLine);
        }
    });
    ```

8. Enregistrez et fermez le fichier **SimulatedDevice.js** .

> [AZURE.NOTE] Pour simplifier les choses, ce didacticiel ne pas implémenter une stratégie de réessayer. Dans le code de production, vous devez implémenter des stratégies de nouvelles tentatives (par exemple, nouvelle tentative de connexion), comme indiqué dans l’article MSDN [Transitoires de gestion des pannes][lnk-transient-faults].

## <a name="call-a-method-on-a-device"></a>Appeler une méthode sur un appareil

Dans cette section, vous créez une application de console Node.js qui appelle une méthode sur le périphérique simulé, puis affiche la réponse.

1. Créer un nouveau dossier vide nommé **callmethodondevice**. Dans le dossier **callmethodondevice** , créez un fichier package.json à l’aide de la commande suivante à votre invite de commandes. Accepter toutes les valeurs par défaut :

    ```
    npm init
    ```

2. À votre invite de commande dans le dossier **callmethodondevice** , exécutez la commande suivante pour installer le package **azure iothub** :

    ```
    npm install azure-iothub@dtpreview --save
    ```

3. À l’aide d’un éditeur de texte, créez un fichier **CallMethodOnDevice.js** dans le dossier **callmethodondevice** .

4. Ajoutez le code suivant `require` instructions au début du fichier **CallMethodOnDevice.js** :

    ```
    'use strict';

    var Client = require('azure-iothub').Client;
    ```

5. Ajoutez la déclaration des variables suivante et remplacez la valeur de l’espace réservé par la chaîne de connexion pour votre plateforme IoT :

    ```
    var connectionString = '{iothub connection string}';
    var methodName = 'writeLine';
    var deviceId = 'myDeviceId';
    ```

6. Créer le client pour ouvrir la connexion à votre concentrateur IoT.

    ```
    var client = Client.fromConnectionString(connectionString);
    ```
    
7. Ajoutez la fonction suivante pour appeler la méthode de l’appareil et imprimer la réponse du périphérique à la console :

    ```
    var methodParams = {
        methodName: methodName,
        payload: 'a line to be written',
        timeoutInSeconds: 30
    };

    client.invokeDeviceMethod(deviceId, methodParams, function (err, result) {
        if (err) {
            console.error('Failed to invoke method \'' + methodName + '\': ' + err.message);
        } else {
            console.log(methodName + ' on ' + deviceId + ':');
            console.log(JSON.stringify(result, null, 2));
        }
    });
    ```

7. Enregistrez et fermez le fichier **CallMethodOnDevice.js** .

## <a name="run-the-applications"></a>Exécuter les applications

Vous êtes maintenant prêt à exécuter les applications.

1. À une invite de commande dans le dossier **simulateddevice** , exécutez la commande suivante pour démarrer l’écoute des appels de méthode à partir de votre plateforme IoT :

    ```
    node SimulatedDevice.js
    ```

    ![][7]
    
2. À une invite de commande dans le dossier **callmethodondevice** , exécutez la commande suivante pour commencer la surveillance votre concentrateur IoT :

    ```
    node CallMethodOnDevice.js 
    ```

    ![][8]
    
3. Vous verrez l’appareil réagir à la méthode en imprimant le message et l’application appelée l’écran méthode la réponse de l’appareil :

    ![][9]
    
## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous configuré un nouveau concentrateur IoT dans le portail Azure et puis créée une identité appareil dans identité Registre son. Vous avez utilisé cette identité appareil pour activer l’application périphérique simulé réagir aux méthodes appelées par le cloud. Vous avez également créé une application qui appelle des méthodes sur l’appareil et affiche la réponse de l’appareil. 

Pour continuer la prise en main IoT concentrateur et Explorer d’autres scénarios IoT, voir :

- [Prise en main IoT concentrateur]
- [Planifier des tâches sur plusieurs appareils][lnk-devguide-jobs]

Pour découvrir comment étendre votre IoT méthode solution et planifiez appelle sur plusieurs appareils, voir [planification et les travaux de diffusion] [ lnk-tutorial-jobs] didacticiel.

<!-- Images. -->
[7]: ./media/iot-hub-c2d-methods/run-simulated-device.png
[8]: ./media/iot-hub-c2d-methods/run-callmethodondevice.png
[9]: ./media/iot-hub-c2d-methods/methods-output.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-tutorial-jobs]: iot-hub-schedule-jobs.md
[lnk-devguide-methods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[Envoyer des messages de Cloud vers le périphérique avec concentrateur IoT]: iot-hub-csharp-csharp-c2d.md
[Process Device-to-Cloud messages]: iot-hub-csharp-csharp-process-d2c.md
[Prise en main IoT concentrateur]: iot-hub-node-node-getstarted.md
