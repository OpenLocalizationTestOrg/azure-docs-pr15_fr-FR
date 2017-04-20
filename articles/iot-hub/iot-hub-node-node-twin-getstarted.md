<properties
    pageTitle="Prise en main jumeaux | Microsoft Azure"
    description="Ce didacticiel vous montre comment utiliser jumeaux"
    services="iot-hub"
    documentationCenter="node"
    authors="fsautomata"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="node"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/13/2016"
     ms.author="elioda"/>

# <a name="tutorial-get-started-with-device-twins-preview"></a>Didacticiel : Prise en main jumeaux appareil (preview)

[AZURE.INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

À la fin de ce didacticiel, vous aurez deux applications console Node.js :

* **AddTagsAndQuery.js**, une application Node.js destinée à être exécutés à partir de la partie principale, ce qui ajoute des balises et des requêtes jumeaux appareil.
* **TwinSimulatedDevice.js**, une application Node.js qui simule un périphérique qui se connecte à votre concentrateur IoT à l’identité de l’appareil créée précédemment et indique l’état de connectivité.

> [AZURE.NOTE] L’article [IoT concentrateur SDK] [ lnk-hub-sdks] fournit des informations sur les différents kits de développement que vous pouvez utiliser pour créer des applications appareil et principale.

Pour effectuer ce didacticiel, vous devez les éléments suivants :

+ Version Node.js 0.10.x ou version ultérieure.

+ Un compte Azure actif. (Si vous n’avez pas un compte, vous pouvez créer un [compte gratuit] [ lnk-free-trial] en quelques minutes.)

[AZURE.INCLUDE [iot-hub-get-started-create-hub-pp](../../includes/iot-hub-get-started-create-hub-pp.md)]

## <a name="create-the-service-app"></a>Créer l’application de service

Dans cette section, vous créez une application de console de Node.js qui ajoute des métadonnées emplacement à la double associé aux **myDeviceId**. Il puis requêtes la jumeaux stockées dans le hub sélection les appareils trouvant dans aux États-Unis, puis ceux que reporting une connexion de données cellulaires.

1. Créer un nouveau dossier vide nommé **addtagsandqueryapp**. Dans le dossier **addtagsandqueryapp** , créez un nouveau fichier package.json à l’aide de la commande suivante à votre invite de commandes. Accepter toutes les valeurs par défaut :

    ```
    npm init
    ```

2. À votre invite de commande dans le dossier **addtagsandqueryapp** , exécutez la commande suivante pour installer le package **azure iothub** :

    ```
    npm install azure-iothub@dtpreview --save
    ```

3. À l’aide d’un éditeur de texte, créez un nouveau fichier **AddTagsAndQuery.js** dans le dossier **addtagsandqueryapp** .

4. Ajoutez le code suivant dans le fichier **AddTagsAndQuery.js** et remplacez l’espace réservé **{chaîne de connexion de service}** avec la chaîne de connexion que vous avez copiée lorsque vous avez créé votre concentrateur :

        'use strict';
        var iothub = require('azure-iothub');
        var connectionString = '{service hub connection string}';
        var registry = iothub.Registry.fromConnectionString(connectionString);

        registry.getTwin('myDeviceId', function(err, twin){
            if (err) {
                console.error(err.constructor.name + ': ' + err.message);
            } else {
                var patch = {
                    tags: {
                        location: {
                            region: 'US',
                            plant: 'Redmond43'
                      }
                    }
                };
             
                twin.update(patch, function(err) {
                  if (err) {
                    console.error('Could not update twin: ' + err.constructor.name + ': ' + err.message);
                  } else {
                    console.log(twin.deviceId + ' twin updated successfully');
                    queryTwins();
                  }
                });
            }
        });

    L’objet **Registry** expose toutes les méthodes requises pour interagir avec jumeaux appareil à partir du service. Le code précédent initialise tout d’abord l’objet **Registry** , puis récupère le double pour **myDeviceId**et enfin met à jour ses balises avec les informations d’emplacement de votre choix.

    Après la mise à jour les balises il appelle la fonction **queryTwins** .

7. Ajoutez le code suivant à la fin **AddTagsAndQuery.js** pour mettre en œuvre de la fonction **queryTwins** :

        var queryTwins = function() {
            var query = registry.createQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'", 100);
            query.nextAsTwin(function(err, results) {
                if (err) {
                    console.error('Failed to fetch the results: ' + err.message);
                } else {
                    console.log("Devices in Redmond43: " + results.map(function(twin) {return twin.deviceId}).join(','));
                }
            });
            
            query = registry.createQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity.type = 'cellular'", 100);
            query.nextAsTwin(function(err, results) {
                if (err) {
                    console.error('Failed to fetch the results: ' + err.message);
                } else {
                    console.log("Devices in Redmond43 using cellular network: " + results.map(function(twin) {return twin.deviceId}).join(','));
                }
            });
        };

    Le code précédent s’exécute deux requêtes : la première sélectionne uniquement les jumeaux appareil de périphériques situés dans la plante **Redmond43** et le second raffine la requête pour sélectionner uniquement les périphériques qui sont également connectés via un réseau cellulaire.

    Notez que le code précédent, lorsqu’il crée l’objet de **requête** , spécifie un nombre maximal de documents renvoyés. L’objet de **requête** contient une propriété booléenne **hasMoreResults** que vous pouvez utiliser pour appeler les méthodes de **nextAsTwin** plusieurs fois pour extraire tous les résultats. Une méthode appelée **suivante** est disponible pour les résultats qui ne sont pas jumeaux appareil par exemple, les résultats de requêtes d’agrégation.

8. Exécutez l’application avec :

        node AddTagsAndQuery.js

    Vous devez voir un périphérique dans les résultats de la requête demandant tous les périphériques situés dans **Redmond43** et aucun pour la requête qui limite les résultats aux périphériques qui utilisent un réseau cellulaire.

    ![][1]

Dans la section suivante vous créez une application d’appareil qui signale les informations de connectivité et modifie le résultat de la requête dans la section précédente.

## <a name="create-the-device-app"></a>Créer l’application d’appareil

Dans cette section, vous créez un Node.js application console qui se connecte à votre plateforme en tant que **myDeviceId**, puis met à jour son double signalés propriétés comportent les informations qu’il est connecté à l’aide d’un réseau cellulaire.

> [AZURE.NOTE] Pour l’instant, jumeaux appareil est accessibles uniquement à partir d’appareils qui se connectent à concentrateur IoT en utilisant le protocole MQTT. Reportez-vous à la [prise en charge MQTT] [ lnk-devguide-mqtt] article pour obtenir des instructions sur la conversion d’application appareil existante à utiliser MQTT.

1. Créer un nouveau dossier vide nommé **reportconnectivity**. Dans le dossier **reportconnectivity** , créez un nouveau fichier package.json à l’aide de la commande suivante à votre invite de commandes. Accepter toutes les valeurs par défaut :

    ```
    npm init
    ```

2. À votre invite de commande dans le dossier **reportconnectivity** , exécutez la commande suivante pour installer le **périphérique d’iot azure**et **azure-iot-appareil-mqtt** package :

    ```
    npm install azure-iot-device@dtpreview azure-iot-device-mqtt@dtpreview --save
    ```

3. À l’aide d’un éditeur de texte, créez un nouveau fichier **ReportConnectivity.js** dans le dossier **reportconnectivity** .

4. Ajoutez le code suivant dans le fichier **ReportConnectivity.js** et remplacez l’espace réservé **{chaîne de connexion appareil}** avec la chaîne de connexion que vous avez copiée lorsque vous avez créé l’identité du périphérique **myDeviceId** :

        'use strict';
        var Client = require('azure-iot-device').Client;
        var Protocol = require('azure-iot-device-mqtt').Mqtt;

        var connectionString = '{device connection string}';
        var client = Client.fromConnectionString(connectionString, Protocol);

        client.open(function(err) {
        if (err) {
            console.error('could not open IotHub client');
        }  else {
            console.log('client opened');

            client.getTwin(function(err, twin) {
            if (err) {
                console.error('could not get twin');
            } else {
                var patch = {
                    connectivity: {
                        type: 'cellular'
                    }
                };

                twin.properties.reported.update(patch, function(err) {
                    if (err) {
                        console.error('could not update twin');
                    } else {
                        console.log('twin state reported');
                        process.exit();
                    }
                });
            }
            });
        }
        });

    L’objet **Client** expose toutes les méthodes que vous avez besoin pour interagir avec jumeaux appareil de l’appareil. Le code précédent, après que qu’il l’initialisation de l’objet **Client** , récupère le double pour **myDeviceId** et sa propriété signalée mis à jour avec les informations de connectivité.

5. Exécuter l’application d’appareil

        node ReportConnectivity.js

    Vous devriez voir le message `twin state reported`.

6. À présent que le périphérique a signalé ses informations de connexion, il doit apparaître dans les deux requêtes. Revenir en arrière dans le dossier **addtagsandqueryapp** et exécutez à nouveau les requêtes :

        node AddTagsAndQuery.js

    Cette fois **myDeviceId** doit apparaître dans les résultats de la requête.

    ![][3]

## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous configuré un nouveau concentrateur IoT dans le portail Azure et puis créée une identité appareil dans identité Registre son. Vous ajouté appareil métadonnées sous forme de balises à partir d’une application principale et écrit une application périphérique simulé pour signaler des informations de connectivité d’appareil dans le double de l’appareil. Vous avez également appris à ces informations à l’aide du concentrateur IoT langage de requête SQL de type de requête.

Utilisez les ressources suivantes pour découvrir comment :

- envoyer de télémétrie à partir d’appareils avec la [prise en main IoT concentrateur] [ lnk-iothub-getstarted] didacticiel,
- configurer les périphériques à l’aide des propriétés de votre choix du double avec l’option [utiliser les propriétés souhaitées pour configurer les périphériques] [ lnk-twin-how-to-configure] didacticiel,
- contrôler les périphériques de façon interactive (par exemple, activer un ventilateurs à partir d’une application contrôlée par l’utilisateur), avec les [méthodes directes utiliser] [ lnk-methods-tutorial] didacticiel.

<!-- images -->
[1]: media/iot-hub-node-node-twin-getstarted/service1.png
[3]: media/iot-hub-node-node-twin-getstarted/service2.png

<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-identity]: iot-hub-devguide-identity-registry.md

[lnk-iothub-getstarted]: iot-hub-node-node-getstarted.md
[lnk-device-management]: iot-hub-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/

[lnk-twin-how-to-configure]: iot-hub-node-node-twin-how-to-configure.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md

[lnk-methods-tutorial]: iot-hub-c2d-methods.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md