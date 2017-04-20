<properties
 pageTitle="Prise en main gestion des appareils | Microsoft Azure"
 description="Ce didacticiel montre comment commencer à utiliser la gestion des périphériques sur Azure IoT concentrateur"
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

# <a name="tutorial-get-started-with-device-management-preview"></a>Didacticiel : Prise en main gestion des périphériques (preview)

## <a name="introduction"></a>Introduction
IoT cloud applications peuvent utiliser primitives dans Azure IoT Hub, à savoir la double appareil et les méthodes directes, pour commencer à distance et surveiller les actions de gestion des périphériques sur les appareils.  Cet article fournit des instructions et code d’une application en nuage IoT et un périphérique utiliser pour lancer et surveiller un redémarrage périphérique distant en utilisant IoT concentrateur.

Pour démarrer et surveiller les actions de gestion des périphériques sur vos appareils à partir d’une application sur le nuage, principale à distance, utilisez des primitives Azure IoT concentrateur comme [double appareil] [ lnk-devtwin] et les [méthodes de (C2D) cloud vers le périphérique][lnk-c2dmethod]. Ce didacticiel montre comment une application principale et un périphérique peuvent fonctionner ensemble pour vous permettre de démarrer et surveiller redémarrage le périphérique à distance IoT concentrateur.

Une méthode C2D vous permet de lancer des actions de gestion des périphériques (par exemple, redémarrez, usine par défaut et mise à jour) à partir d’une application principale dans le cloud. L’appareil est responsable de :

- Gestion de la requête de méthode envoyée à partir de IoT concentrateur.
- Lance l’action spécifique appareil correspondante sur l’appareil.
- Mise à jour de statut via la double périphérique signalé propriétés IoT concentrateur.

Vous pouvez utiliser une application principale dans le cloud pour exécuter des requêtes de double appareil pour signaler l’avancement de vos actions de gestion des périphériques.

Ce didacticiel vous explique comment procéder pour :

- Utiliser le portail Azure pour créer un concentrateur IoT et créer une identité appareil dans votre concentrateur IoT.
- Créer un périphérique simulé qui comporte une méthode directe qui permet de redémarrage qui peut être appelé par le cloud.
- Créer une application console qui appelle la méthode directe redémarrage sur le périphérique simulé via votre concentrateur IoT.

À la fin de ce didacticiel, vous avez deux applications console Node.js :

**dmpatterns_getstarted_device.js**, qui se connecte à votre concentrateur IoT avec l’identité de l’appareil créée précédemment, reçoit une méthode directe redémarrage, simule un redémarrage physique et indique l’heure pour le dernier redémarrage.

**dmpatterns_getstarted_service.js**, qui appelle une méthode directement sur le périphérique simulé, affiche la réponse et affiche le double de périphérique mis à jour a signalé propriétés.

Pour effectuer ce didacticiel, vous devez les éléments suivants :

Version Node.js 0.12.x ou version ultérieure, <br/>  [Préparer votre environnement de développement] [ lnk-dev-setup] décrit comment installer Node.js pour ce didacticiel sur Windows ou Linux.

Un compte Azure actif. (Si vous n’avez pas un compte, vous pouvez créer un [compte gratuit] [ lnk-free-trial] en quelques minutes.)

[AZURE.INCLUDE [iot-hub-get-started-create-hub-pp](../../includes/iot-hub-get-started-create-hub-pp.md)]

## <a name="create-a-simulated-device-app"></a>Créer une application de périphérique simulé

Dans cette section, vous créez une application console Node.js qui répond à une méthode directe appelée par le cloud, ce qui déclenche un redémarrage périphérique simulé et utilise la double périphérique signalé propriétés pour activer les requêtes de double appareil identifier les appareils et quand ils dernier redémarrage.

1. Créer un nouveau dossier vide nommé **manageddevice**.  Dans le dossier **manageddevice** , créez un fichier package.json à l’aide de la commande suivante à votre invite de commandes.  Accepter toutes les valeurs par défaut :

    ```
    npm init
    ```
    
2. À votre invite de commande dans le dossier **manageddevice** , exécutez la commande suivante pour installer le **azure-iot-device@dtpreview** package SDK appareil et **azure-iot-device-mqtt@dtpreview** package :

    ```
    npm install azure-iot-device@dtpreview azure-iot-device-mqtt@dtpreview --save
    ```

3. À l’aide d’un éditeur de texte, créez un nouveau fichier **dmpatterns_getstarted_device.js** dans le dossier **manageddevice** .

4. Ajoutez les éléments suivants 'requièrent' instructions au début du fichier **dmpatterns_getstarted_device.js** :

    ```
    'use strict';

    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```

5. Ajouter une variable **connectionString** et utilisez-le pour créer un client de l’appareil.  Remplacez la chaîne de connexion par la chaîne de connexion appareil.  

    ```
    var connectionString = 'HostName={youriothostname};DeviceId=myDeviceId;SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

6. Ajoutez la fonction suivante pour mettre en œuvre la méthode directe sur l’appareil

    ```
    var onReboot = function(request, response) {
        
        // Respond the cloud app for the direct method
        response.send(200, 'Reboot started', function(err) {
            if (!err) {
                console.error('An error occured when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });
        
        // Report the reboot before the physical restart
        var date = new Date();
        var patch = {
            iothubDM : {
                reboot : {
                    lastReboot : date.toISOString(),
                }
            }
        };

        // Get device Twin
        client.getTwin(function(err, twin) {
            if (err) {
                console.error('could not get twin');
            } else {
                console.log('twin acquired');
                twin.properties.reported.update(patch, function(err) {
                    if (err) throw err;
                    console.log('Device reboot twin state reported')
                });  
            }
        });
        
        // Add your device's reboot API for physical restart.
        console.log('Rebooting!');
    };
    ```

7. Ouvrez la connexion à votre concentrateur IoT et démarrer le récepteur méthode directe :

    ```
    client.open(function(err) {
        if (err) {
            console.error('Could not open IotHub client');
        }  else {
            console.log('Client opened.  Waiting for reboot method.');
            client.onDeviceMethod('reboot', onReboot);
        }
    });
    ```
    
8. Enregistrez et fermez le fichier **dmpatterns_getstarted_device.js** .

 [AZURE.NOTE] Pour simplifier les choses, ce didacticiel ne pas implémenter une stratégie de réessayer. Dans le code de production, vous devez implémenter des stratégies de nouvelles tentatives (par exemple, un intervalle exponentielle), comme indiqué dans l’article MSDN [Transitoires de gestion des pannes][lnk-transient-faults].

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Déclencher la lecture d’une réinitialisation à distance sur le périphérique à l’aide d’une méthode directe 

Dans cette section, vous créez une application de console Node.js qui lance une réinitialisation à distance sur un appareil à l’aide d’une méthode directe et utilise des requêtes de double appareil pour rechercher la dernière période de redémarrage pour cet appareil.

1. Créer un nouveau dossier vide nommé **triggerrebootondevice**.  Dans le dossier **triggerrebootondevice** , créez un fichier package.json à l’aide de la commande suivante à votre invite de commandes.  Accepter toutes les valeurs par défaut :

    ```
    npm init
    ```
    
2. À votre invite de commande dans le dossier **triggerrebootondevice** , exécutez la commande suivante pour installer le **azure-iothub@dtpreview** package SDK appareil et **azure-iot-device-mqtt@dtpreview** package :

    ```
    npm install azure-iothub@dtpreview --save
    ```
    
3. À l’aide d’un éditeur de texte, créez un nouveau fichier **dmpatterns_getstarted_service.js** dans le dossier **triggerrebootondevice** .

4. Ajoutez les éléments suivants 'requièrent' instructions au début du fichier **dmpatterns_getstarted_service.js** :

    ```
    'use strict';

    var Registry = require('azure-iothub').Registry;
    var Client = require('azure-iothub').Client;
    ```

5. Ajoutez les déclarations de variable suivantes et remplacer les valeurs d’espace réservé :

    ```
    var connectionString = '{iothubconnectionstring}';
    var registry = Registry.fromConnectionString(connectionString);
    var client = Client.fromConnectionString(connectionString);
    var deviceToReboot = 'myDeviceId';
    ```
    
6. Ajoutez la fonction suivante pour appeler la méthode de l’appareil pour redémarrer l’équipement cible :

    ```
    var startRebootDevice = function(twin) {

        var methodName = "reboot";
        
        var methodParams = {
            methodName: methodName,
            payload: null,
            timeoutInSeconds: 30
        };
        
        client.invokeDeviceMethod(deviceToReboot, methodParams, function(err, result) {
            if (err) { 
                console.error("Direct method error: "+err.message);
            } else {
                console.log("Successfully invoked the device to reboot.");  
            }
        });
    };
    ```

7. Ajoutez la fonction suivante pour interroger le périphérique et obtenir la dernière fois redémarrage :

    ```
    var queryTwinLastReboot = function() {

        registry.getTwin(deviceToReboot, function(err, twin){

            if (twin.properties.reported.iothubDM != null)
            {
                if (err) {
                    console.error('Could not query twins: ' + err.constructor.name + ': ' + err.message);
                } else {
                    var lastRebootTime = twin.properties.reported.iothubDM.reboot.lastReboot;
                    console.log('Last reboot time: ' + JSON.stringify(lastRebootTime, null, 2));
                }
            } else 
                console.log('Waiting for device to report last reboot time.');
        });
    };
    ```
    
8. Ajoutez le code suivant pour appeler les fonctions qui doit déclencher la méthode directe redémarrage et la requête pour la dernière fois redémarrage :

    ```
    startRebootDevice();
    setInterval(queryTwinLastReboot, 2000);
    ```
    
9. Enregistrez et fermez le fichier **dmpatterns_getstarted_service.js** .

## <a name="run-the-applications"></a>Exécuter les applications

Vous êtes maintenant prêt à exécuter les applications.

1. À l’invite dans le dossier **manageddevice** , exécutez la commande suivante pour commencer à écouter de la méthode directe redémarrage.

    ```
    node dmpatterns_getstarted_device.js
    ```

2. À l’invite dans le dossier **triggerrebootondevice** , exécutez la commande suivante pour déclencher le redémarrage à distance et la requête pour le double appareil rechercher le dernier redémarrer heure.

    ```
    node dmpatterns_getstarted_service.js
    ```

3. Vous verrez le réagir à la méthode directe en imprimant le message

## <a name="customize-and-extend-the-device-management-actions"></a>Personnaliser et étendre le périphérique actions de gestion

Vos solutions IoT peuvent développer l’ensemble des modèles de gestion des périphériques défini ou activer des modèles personnalisés à l’aide de la double appareil et primitives de méthode C2D. Autres actions de gestion des périphériques et exemples d’usine par défaut, mise à jour, mise à jour logicielle, gestion de l’alimentation, connectivité réseau et gestion, chiffrement des données.

## <a name="device-maintenance-windows"></a>Fenêtres de maintenance appareil

En règle générale, vous configurez les périphériques pour effectuer des actions à un horaire que réduire les interruptions et temps d’arrêt.  Fenêtres de maintenance appareil sont un modèle fréquemment utilisé pour définir l’heure quand un appareil doit mettre à jour sa configuration. Vos solutions principale peuvent utiliser les propriétés souhaitées de la double appareil définir et activer une stratégie sur votre appareil qui permet une période de maintenance. Lorsqu’un périphérique reçoit la stratégie de fenêtre de maintenance, il peut utiliser la propriété signalée de la double appareil pour signaler l’état de la stratégie. L’application principale pouvez ensuite utiliser des requêtes appareil double certifier la conformité des appareils et chaque stratégie.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez utilisé une méthode directe pour déclencher la lecture d’une réinitialisation à distance sur un appareil utilisé le double appareil signalées propriétés pour signaler la dernière fois redémarrage de l’appareil et interrogée pour le double appareil découvrir la dernière fois redémarrage de l’appareil à partir du cloud.

Pour poursuivre la prise en main IoT concentrateur et les modèles de gestion des périphériques comme à distance sur la mise à jour de microprogramme air, voir :

[Didacticiel : Comment effectuer une mise à jour][lnk-fwupdate]

Pour découvrir comment étendre votre IoT méthode solution et planifiez appelle sur plusieurs appareils, voir [planification et les travaux de diffusion] [ lnk-tutorial-jobs] didacticiel.

Pour poursuivre la prise en main IoT concentrateur, voir [mise en route avec le Kit de développement de passerelle IoT][lnk-gateway-SDK].


<!-- images and links -->
[img-output]: media/iot-hub-get-started-with-dm/image6.png
[img-dm-ui]: media/iot-hub-get-started-with-dm/dmui.png

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md

[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-fwupdate]: iot-hub-firmware-update.md
[Azure portal]: https://portal.azure.com/
[Using resource groups to manage your Azure resources]: ../azure-portal/resource-group-portal.md
[lnk-dm-github]: https://github.com/Azure/azure-iot-device-management
[lnk-tutorial-jobs]: iot-hub-schedule-jobs.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx