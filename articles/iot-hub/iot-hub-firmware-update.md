<properties
 pageTitle="Comment effectuer une mise à jour de microprogramme | Microsoft Azure"
 description="Ce didacticiel montre comment effectuer une mise à jour"
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

# <a name="tutorial-how-to-do-a-firmware-update-preview"></a>Didacticiel : Comment effectuer un microprogramme mise à jour (preview)

## <a name="introduction"></a>Introduction
Dans la [mise en route de la gestion des appareils] [ lnk-dm-getstarted] didacticiel, vous avez appris à utiliser le [double de l’appareil] [ lnk-devtwin] et les [méthodes de (C2D) cloud vers le périphérique] [ lnk-c2dmethod] primitives redémarrer à distance un appareil. Ce didacticiel utilise les mêmes primitives IoT concentrateur et fournit des conseils et vous montre comment effectuer une mise à jour de bout en bout simulé microprogramme.  Ce modèle est utilisé dans l’implémentation de la mise à jour de microprogramme pour l’échantillon appareil Edison Intel.

Ce didacticiel vous explique comment procéder pour :

- Créer une application console qui appelle la méthode directe firmwareUpdate sur le périphérique simulé via votre concentrateur IoT.
- Créer un périphérique simulé qui mettent en œuvre, une méthode directe firmwareUpdate qui mène à un processus en plusieurs étapes qui attend de télécharger l’image du microprogramme, l’image du microprogramme et les téléchargements et enfin applique ème microprogramme image.  Dans l’ensemble de l’exécution de chaque étape les utilisations d’appareil le double signalés propriétés pour mettre à jour la progression.

À la fin de ce didacticiel, vous avez deux applications console Node.js :

**dmpatterns_fwupdate_service.js**, qui appelle une méthode directement sur le périphérique simulé, affiche la réponse et régulièrement signalé (chaque 500 ms) affiche le double de périphérique mis à jour les propriétés.

**dmpatterns_fwupdate_device.js**, qui se connecte à votre concentrateur IoT avec l’identité de l’appareil créée précédemment, reçoit une méthode directe firmwareUpdate, parcourt un processus état multiples pour simuler une mise à jour de microprogramme, y compris : en attente de téléchargement de l’image, le téléchargement de la nouvelle image et l’application pour terminer l’image.


Pour effectuer ce didacticiel, vous devez les éléments suivants :

Version Node.js 0.12.x ou version ultérieure, <br/>  [Préparer votre environnement de développement] [ lnk-dev-setup] décrit comment installer Node.js pour ce didacticiel sur Windows ou Linux.

Un compte Azure actif. (Si vous n’avez pas un compte, vous pouvez créer un [compte gratuit] [ lnk-free-trial] en quelques minutes.)

Suivez l’article [mise en route de la gestion des périphériques](iot-hub-device-management-get-started.md) pour créer votre concentrateur IoT et obtenir la chaîne de connexion.

[AZURE.INCLUDE [iot-hub-get-started-create-hub-pp](../../includes/iot-hub-get-started-create-hub-pp.md)]

## <a name="create-a-simulated-device-app"></a>Créer une application de périphérique simulé

Dans cette section, vous créez une application console Node.js qui répond à une méthode directe appelée par le cloud, ce qui déclenche une mise à jour de microprogramme périphérique simulé et utilise la double périphérique signalé propriétés pour activer les requêtes de double appareil identifier les appareils et quand ils dernier redémarrage.

1. Créer un nouveau dossier vide nommé **manageddevice**.  Dans le dossier **manageddevice** , créez un fichier package.json à l’aide de la commande suivante à votre invite de commandes.  Accepter toutes les valeurs par défaut :

    ```
    npm init
    ```
    
2. À votre invite de commande dans le dossier **manageddevice** , exécutez la commande suivante pour installer le **azure-iot-device@dtpreview** package SDK appareil et **azure-iot-device-mqtt@dtpreview** package :

    ```
    npm install azure-iot-device@dtpreview azure-iot-device-mqtt@dtpreview --save
    ```

3. À l’aide d’un éditeur de texte, créez un nouveau fichier **dmpatterns_fwupdate_device.js** dans le dossier **manageddevice** .

4. Ajoutez les éléments suivants 'requièrent' instructions au début du fichier **dmpatterns_fwupdate_device.js** :

    ```
    'use strict';

    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```

5. Ajouter une variable **connectionString** et utilisez-le pour créer un client de l’appareil.  

    ```
    var connectionString = 'HostName={youriothostname};DeviceId=myDeviceId;SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

6. Ajoutez le code suivant fonction qui sera utilisée pour mettre à jour double signalés propriétés

    ```
    var reportFWUpdateThroughTwin = function(twin, firmwareUpdateValue) {
      var patch = {
          iothubDM : {
            firmwareUpdate : firmwareUpdateValue
          }
      };

      twin.properties.reported.update(patch, function(err) {
        if (err) throw err;
        console.log('twin state reported')
      });
    };
    ```
    
7. Ajoutez les fonctions suivantes, laquelle peut simuler le téléchargement et appliquer de l’image du microprogramme.

    ```
    var simulateDownloadImage = function(imageUrl, callback) {
      var error = null;
      var image = "[fake image data]";
      
      console.log("Downloading image from " + imageUrl);
      
      callback(error, image);
    }

    var simulateApplyImage = function(imageData, callback) {
      var error = null;
      
      if (!imageData) {
        error = {message: 'Apply image failed because of missing image data.'};
      }
      
      callback(error);
    }
    ```

8. Ajoutez le code suivant fonction qui met à jour l’état de mise à jour de microprogramme via le double signalé propriétés en attente de téléchargement.  En règle générale, appareils sont informés d’une mise à jour disponibles et définies par l’administrateur stratégie causes du périphérique pour démarrer le téléchargement et en appliquant la mise à jour.  Il s’agit de l’endroit où la logique d’activer cette stratégie s’exécute.  Pour simplifier, nous allons retarder pour 4 secondes et procéder au téléchargement de l’image du microprogramme. 

    ```
    var waitToDownload = function(twin, fwPackageUriVal, callback) {
      var now = new Date();
      
      reportFWUpdateThroughTwin(twin, {
        fwPackageUri: fwPackageUriVal,
        status: 'waiting',
        error : null,
        startedWaitingTime : now.toISOString()
      });
      setTimeout(callback, 4000);
    };
    ```
    
9. Ajoutez le code suivant fonction qui met à jour l’état de mise à jour de microprogramme via le double signalés propriétés pour le téléchargement de l’image du microprogramme.  Il suit simulation un téléchargement microprogramme et enfin met à jour l’état de mise à jour de microprogramme pour informer d’un téléchargement succès ou échec.

    ```
    var downloadImage = function(twin, fwPackageUriVal, callback) {
      var now = new Date();   
      
      reportFWUpdateThroughTwin(twin, {
        status: 'downloading',
      });
      
      setTimeout(function() {
        // Simulate download
        simulateDownloadImage(fwPackageUriVal, function(err, image) {
          
          if (err)
          {
            reportFWUpdateThroughTwin(twin, {
              status: 'downloadfailed',
              error: {
                code: error_code,
                message: error_message,
              }
            });
          }
          else {        
            reportFWUpdateThroughTwin(twin, {
              status: 'downloadComplete',
              downloadCompleteTime: now.toISOString(),
            });
            
            setTimeout(function() { callback(image); }, 4000);   
          }
        });
        
      }, 4000);
    }
    ```
    
10. Ajoutez le code suivant fonction qui met à jour l’état de mise à jour de microprogramme via le double signalés propriétés à l’application de l’image du microprogramme.  Il suit simulation d’une application de l’image du microprogramme et enfin met à jour l’état de mise à jour de microprogramme pour informer appliquer réussite ou échec.

    ```
    var applyImage = function(twin, imageData, callback) {
      var now = new Date();   
      
      reportFWUpdateThroughTwin(twin, {
        status: 'applying',
        startedApplyingImage : now.toISOString()
      });
      
      setTimeout(function() {
        
        // Simulate apply firmware image
        simulateApplyImage(imageData, function(err) {
          if (err) {
            reportFWUpdateThroughTwin(twin, {
              status: 'applyFailed',
              error: {
                code: err.error_code,
                message: err.error_message,
              }
            });
          } else { 
            reportFWUpdateThroughTwin(twin, {
              status: 'applyComplete',
              lastFirmwareUpdate: now.toISOString()
            });    
            
          }
        });
        
        setTimeout(callback, 4000);
        
      }, 4000);
    }
    ```

11. Ajouter la functoin suivante qui gérer la méthode firmwareUpdate et lancer le processus de mise à jour de microprogramme multi-phase.

    ```
    var onFirmwareUpdate = function(request, response) {
            
      // Respond the cloud app for the direct method
      response.send(200, 'FirmwareUpdate started', function(err) {
        if (!err) {
          console.error('An error occured when sending a method response:\n' + err.toString());
        } else {
          console.log('Response to method \'' + request.methodName + '\' sent successfully.');
        }
      });

      // Get the parameter from the body of the method request
      var fwPackageUri = JSON.parse(request.payload).fwPackageUri;

      // Obtain the device twin
      client.getTwin(function(err, twin) {
        if (err) {
          console.error('Could not get device twin.');
        } else {
          console.log('Device twin acquired.');
          
          // Start the multi-stage firmware update
          waitToDownload(twin, fwPackageUri, function() {
            downloadImage(twin, fwPackageUri, function(imageData) {
              applyImage(twin, imageData, function() {});    
            });  
          });

        }
      });
    }
    ```
    
12. Enfin, ajoutez le code suivant qui se connecte au concentrateur IoT en tant que périphérique, 

    ```
    client.open(function(err) {
      if (err) {
        console.error('Could not connect to IotHub client');
      }  else {
        console.log('Client connected to IoT Hub.  Waiting for firmwareUpdate direct method.');
      }
      
      client.onDeviceMethod('firmwareUpdate', onFirmwareUpdate(request, response));
    });
    ```

> [AZURE.NOTE] Pour simplifier les choses, ce didacticiel ne pas implémenter une stratégie de réessayer. Dans le code de production, vous devez implémenter des stratégies de nouvelles tentatives (par exemple, un intervalle exponentielle), comme indiqué dans l’article MSDN [Transitoires de gestion des pannes][lnk-transient-faults].

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>Déclencher la lecture d’une mise à jour de microprogramme à distance sur le périphérique à l’aide d’une méthode directe 

Dans cette section, vous créez une application de console Node.js qui démarre une mise à jour de microprogramme à distance sur un appareil à l’aide d’une méthode directe et utilise les requêtes double appareil pour obtenir périodiquement l’état de la mise à jour de microprogramme active sur votre appareil.


1. Créer un nouveau dossier vide nommé **triggerfwupdateondevice**.  Dans le dossier **triggerfwupdateondevice** , créez un fichier package.json à l’aide de la commande suivante à votre invite de commandes.  Accepter toutes les valeurs par défaut :

    ```
    npm init
    ```
    
2. À votre invite de commande dans le dossier **triggerfwupdateondevice** , exécutez la commande suivante pour installer le package d’appareil SDK **azure iothub** et **azure-iot-appareil-mqtt** :

    ```
    npm install azure-iot-hub@dtpreview --save
    ```
    
3. À l’aide d’un éditeur de texte, créez un nouveau fichier **dmpatterns_getstarted_service.js** dans le dossier **triggerfwupdateondevice** .

4. Ajoutez les éléments suivants 'requièrent' instructions au début du fichier **dmpatterns_getstarted_service.js** :

    ```
    'use strict';

    var Registry = require('azure-iothub').Registry;
    var Client = require('azure-iothub').Client;
    ```

5. Ajoutez les déclarations de variable suivantes et remplacer les valeurs d’espace réservé :

    ```
    var connectionString = '{device_connectionstring}';
    var registry = Registry.fromConnectionString(connectionString);
    var client = Client.fromConnectionString(connectionString);
    var deviceToUpdate = 'myDeviceId';
    ```
    
6. Ajoutez la fonction suivante pour rechercher et afficher la valeur de la firmwareUpdate signalés propriété.

    ```
    var queryTwinFWUpdateReported = function() {
        registry.getTwin(deviceToUpdate, function(err, twin){
            if (err) {
              console.error('Could not query twins: ' + err.constructor.name + ': ' + err.message);
            } else {
              console.log((JSON.stringify(twin.properties.reported.iothubDM.firmwareUpdate)) + "\n");
            }
        });
    };
    ```

7. Ajoutez la fonction suivante pour appeler la méthode firmwareUpdate pour redémarrer l’équipement cible :

    ```
    var startFirmwareUpdateDevice = function() {
      var params = {
          fwPackageUri: 'https://secureurl'
      };
      
      var methodName = "firmwareUpdate";
      var payloadData =  JSON.stringify(params);
      
      var methodParams = {
        methodName: methodName,
        payload: payloadData,
        timeoutInSeconds: 30
      };
      
      client.invokeDeviceMethod(deviceToUpdate, methodParams, function(err, result) {
        if (err) {
          console.error('Could not start the firmware update on the device: ' + err.message)
        } 
      });
    };
    ```

8. Enfin, ajoutez la fonction suivante au code pour démarrer le microprogramme séquence de mettre à jour et démarrer régulièrement montrant le double signalé propriétés :

    ```
    startFirmwareUpdateDevice();
    setInterval(queryTwinFWUpdateReported, 500);
    ```
    
9. Enregistrez et fermez le fichier **dmpatterns_fwupdate_service.js** .

## <a name="run-the-applications"></a>Exécuter les applications

Vous êtes maintenant prêt à exécuter les applications.

1. À l’invite dans le dossier **manageddevice** , exécutez la commande suivante pour commencer à écouter de la méthode directe redémarrage.

    ```
    node dmpatterns_fwupdate_device.js
    ```

2. À l’invite dans le dossier **triggerfwupdateondevice** , exécutez la commande suivante pour déclencher le redémarrage à distance et la requête pour le double appareil rechercher le dernier redémarrer heure.

    ```
    node dmpatterns_fwupdate_service.js
    ```

3. Vous verrez le réagir à la méthode directe en imprimant le message

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez utilisé une méthode directe pour déclencher la lecture d’une mise à jour de microprogramme à distance sur un appareil et utilisés régulièrement la double signalés propriétés pour comprendre la progression du microprogramme des mises à jour.  

Pour découvrir comment étendre votre IoT méthode solution et planifiez appelle sur plusieurs appareils, voir [planification et les travaux de diffusion] [ lnk-tutorial-jobs] didacticiel.

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-device-management-get-started.md
[lnk-tutorial-jobs]: iot-hub-schedule-jobs.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
