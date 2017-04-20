<properties
 pageTitle="Comment planifier des travaux | Microsoft Azure"
 description="Ce didacticiel montre comment planifier des tâches"
 services="iot-hub"
 documentationCenter=".net"
 authors="juanjperez"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/30/2016" 
 ms.author="juanpere"/>

# <a name="tutorial-schedule-and-broadcast-jobs-preview"></a>Didacticiel : Planifier et diffuser des travaux (preview)

## <a name="introduction"></a>Introduction
Concentrateur IoT Azure est un service entièrement géré qui permet une application principale créer et effectuer le suivi des tâches de planifier et mettre à jour des millions d’appareils.  Tâches peuvent être utilisées pour les actions suivantes :

- Mettre à jour les propriétés du périphérique double vous le souhaitez
- Mettre à jour les balises double pour appareil mobile
- Méthodes de cloud vers le périphérique d’appel

Point de vue conceptuel, une tâche à la ligne une des actions suivantes et effectue le suivi de l’avancement de l’exécution par rapport à un ensemble de périphériques, qui est défini par une requête double.  Par exemple, à l’aide d’une tâche une application principale peut appeler une méthode redémarrage sur 10 000 périphériques, spécifié par une requête double et planifiée à l’avenir.  Cette application peut puis suivre la progression que chacune de ces appareils de réception et exécuter la méthode de redémarrage.

En savoir plus sur chacune de ces fonctionnalités dans les articles suivants :

- Double périphérique et propriétés : [prise en main double] [ lnk-get-started-twin] et [didacticiel : comment utiliser propriétés double][lnk-twin-props]
- Méthodes de cloud vers le périphérique : [guide pour les développeurs - méthodes directes] [ lnk-dev-methods] et [didacticiel : méthodes C2D][lnk-c2d-methods]

Ce didacticiel vous explique comment procéder pour :

- Créer un périphérique simulé qui comporte une méthode directe qui permet de lockDoor qui peut être appelé par l’application en fin.
- Créer une application console qui appelle la méthode directe lockDoor sur le périphérique simulé à l’aide d’une tâche et met à jour les propriétés double souhaité à l’aide d’une tâche de l’appareil.

À la fin de ce didacticiel, vous avez deux applications console Node.js :

**simDevice.js**, qui se connecte à votre concentrateur IoT avec l’identité de l’appareil et reçoit une méthode directe lockDoor.

**scheduleJobService.js**, qui appelle une méthode directe sur le périphérique simulé et mettre à jour les propriétés de disired de double à l’aide d’une tâche.

Pour effectuer ce didacticiel, vous devez les éléments suivants :

Version Node.js 0.12.x ou version ultérieure, <br/>  [Préparer votre environnement de développement] [ lnk-dev-setup] décrit comment installer Node.js pour ce didacticiel sur Windows ou Linux.

Un compte Azure actif. (Si vous n’avez pas un compte, vous pouvez créer un [compte gratuit] [ lnk-free-trial] en quelques minutes.)

[AZURE.INCLUDE [iot-hub-get-started-create-hub-pp](../../includes/iot-hub-get-started-create-hub-pp.md)]

## <a name="create-a-simulated-device-app"></a>Créer une application de périphérique simulé

Dans cette section, vous créez une application console Node.js qui répond à une méthode directe appelée par le cloud, ce qui déclenche un redémarrage périphérique simulé et utilise la double périphérique signalé propriétés pour activer les requêtes de double appareil identifier les appareils et quand ils dernier redémarrage.

1. Créer un nouveau dossier vide nommé **simDevice**.  Dans le dossier **simDevice** , créez un fichier package.json à l’aide de la commande suivante à votre invite de commandes.  Accepter toutes les valeurs par défaut :

    ```
    npm init
    ```
    
2. À votre invite de commande dans le dossier **simDevice** , exécutez la commande suivante pour installer le **périphérique d’iot azure** SDK appareil package et **azure-iot-appareil-mqtt** :

    ```
    npm install azure-iot-device@dtpreview azure-iot-device-mqtt@dtpreview --save
    ```

3. À l’aide d’un éditeur de texte, créez un nouveau fichier **simDevice.js** dans le dossier **simDevice** .

4. Ajoutez les éléments suivants 'requièrent' instructions au début du fichier **simDevice.js** :

    ```
    'use strict';

    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```

5. Ajouter une variable **connectionString** et utilisez-le pour créer un client de l’appareil.  

    ```
    var connectionString = 'HostName={youriothostname};DeviceId={yourdeviceid};SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

6. Ajoutez la fonction suivante pour gérer la méthode lockDoor.

    ```
    var onLockDoor = function(request, response) {
        
        // Respond the cloud app for the direct method
        response.send(200, function(err) {
            if (!err) {
                console.error('An error occured when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });
        
        console.log('Locking Door!');
    };
    ```

7. Ajoutez le code suivant pour inscrire le Gestionnaire de la méthode lockDoor.

    ```
    client.open(function(err) {
        if (err) {
            console.error('Could not connect to IotHub client.');
        }  else {
            console.log('Client connected to IoT Hub.  Waiting for reboot direct method.');
            client.onDeviceMethod('lockDoor', onLockDoor);
        }
    });
    ```
    
8. Enregistrez et fermez le fichier **simDevice.js** .

> [AZURE.NOTE] Pour simplifier les choses, ce didacticiel ne pas implémenter une stratégie de réessayer. Dans le code de production, vous devez implémenter des stratégies de nouvelles tentatives (par exemple, un intervalle exponentielle), comme indiqué dans l’article MSDN [Transitoires de gestion des pannes][lnk-transient-faults].

## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-twins-properties"></a>Planifier des tâches pour appeler une méthode directe et mise à jour des propriétés d’un double

Dans cette section, vous créez une application console Node.js qui démarre un lockDoor à distance sur un appareil à l’aide d’une méthode directe et mettre à jour les propriétés de la double.

1. Créer un nouveau dossier vide nommé **scheduleJobService**.  Dans le dossier **scheduleJobService** , créez un fichier package.json à l’aide de la commande suivante à votre invite de commandes.  Accepter toutes les valeurs par défaut :

    ```
    npm init
    ```
    
2. À votre invite de commande dans le dossier **scheduleJobService** , exécutez la commande suivante pour installer le package d’appareil SDK **azure iothub** et **azure-iot-appareil-mqtt** :

    ```
    npm install azure-iothub@dtpreview uuid --save
    ```
    
3. À l’aide d’un éditeur de texte, créez un nouveau fichier **scheduleJobService.js** dans le dossier **scheduleJobService** .

4. Ajoutez les éléments suivants 'requièrent' instructions au début du fichier **dmpatterns_gscheduleJobServiceetstarted_service.js** :

    ```
    'use strict';

    var uuid = require('uuid');
    var JobClient = require('azure-iothub').JobClient;
    ```

5. Ajoutez les déclarations de variable suivantes et remplacer les valeurs d’espace réservé :

    ```
    var connectionString = '{iothubconnectionstring}';
    var deviceArray = ['myDeviceId'];
    var startTime = new Date();
    var maxExecutionTimeInSeconds =  3600;
    var jobClient = JobClient.fromConnectionString(connectionString);
    ```
    
6. Ajoutez la fonction suivante qui sera utilisée pour contrôler l’exécution de la tâche :

    ```
    function monitorJob (jobId, callback) {
        var jobMonitorInterval = setInterval(function() {
            jobClient.getJob(jobId, function(err, result) {
            if (err) {
                console.error('Could not get job status: ' + err.message);
            } else {
                console.log('Job: ' + jobId + ' - status: ' + result.status);
                if (result.status === 'completed' || result.status === 'failed' || result.status === 'cancelled') {
                clearInterval(jobMonitorInterval);
                callback(null, result);
                }
            }
            });
        }, 5000);
    }
    ```

7. Ajoutez le code suivant pour planifier le travail qui appelle la méthode de l’appareil :

    ```
    var methodParams = {
        methodName: 'lockDoor',
        payload: null,
        timeoutInSeconds: 45
    };

    var methodJobId = uuid.v4();
    console.log('scheduling Device Method job with id: ' + methodJobId);
    jobClient.scheduleDeviceMethod(methodJobId,
                                deviceArray,
                                methodParams,
                                startTime,
                                maxExecutionTimeInSeconds,
                                function(err) {
        if (err) {
            console.error('Could not schedule device method job: ' + err.message);
        } else {
            monitorJob(methodJobId, function(err, result) {
                if (err) {
                    console.error('Could not monitor device method job: ' + err.message);
                } else {
                    console.log(JSON.stringify(result, null, 2));
                }
            });
        }
    });
    ```
        
8. Ajoutez le code suivant pour planifier le travail pour mettre à jour le double :

    ```
    var twinPatch = {
        etag: '*',
        desired: {
            building: '43',
            floor: 3
        }
    };

    var twinJobId = uuid.v4();

    console.log('scheduling Twin Update job with id: ' + twinJobId);
    jobClient.scheduleTwinUpdate(twinJobId,
                                deviceArray,
                                twinPatch,
                                startTime,
                                maxExecutionTimeInSeconds,
                                function(err) {
        if (err) {
            console.error('Could not schedule twin update job: ' + err.message);
        } else {
            monitorJob(twinJobId, function(err, result) {
                if (err) {
                    console.error('Could not monitor twin update job: ' + err.message);
                } else {
                    console.log(JSON.stringify(result, null, 2));
                }
            });
        }
    });
    ```
    
9. Enregistrez et fermez le fichier **scheduleJobService.js** .

## <a name="run-the-applications"></a>Exécuter les applications

Vous êtes maintenant prêt à exécuter les applications.

1. À l’invite dans le dossier **simDevice** , exécutez la commande suivante pour commencer à écouter de la méthode directe redémarrage.

    ```
    node simDevice.js
    ```

2. À l’invite dans le dossier **scheduleJobService** , exécutez la commande suivante pour déclencher le redémarrage à distance et la requête pour le double appareil rechercher le dernier redémarrer heure.

    ```
    node scheduleJobService.js
    ```

3. Vous verrez le résultat de l’appareil et applications serveur principal.


## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez utilisé une tâche pour planifier une méthode directe d’un périphérique et la mise à jour des propriétés de double appareil.

Pour poursuivre la prise en main IoT concentrateur et les modèles de gestion des périphériques comme à distance sur la mise à jour de microprogramme air, voir :

[Didacticiel : Comment effectuer une mise à jour][lnk-fwupdate]

Pour poursuivre la prise en main IoT concentrateur, voir [mise en route avec le Kit de développement de passerelle IoT][lnk-gateway-SDK].

[lnk-get-started-twin]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-props]: iot-hub-node-node-twin-how-to-configure.md
[lnk-c2d-methods]: iot-hub-c2d-methods.md
[lnk-dev-methods]: iot-hub-devguide-direct-methods.md
[lnk-fwupdate]: iot-hub-firmware-update.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx