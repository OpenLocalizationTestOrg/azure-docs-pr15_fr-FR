<properties
    pageTitle="Utiliser les propriétés double | Microsoft Azure"
    description="Ce didacticiel vous montre comment utiliser les propriétés double"
    services="iot-hub"
    documentationCenter=".net"
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

# <a name="tutorial-use-desired-properties-to-configure-devices-preview"></a>Didacticiel : Utiliser les propriétés souhaitées pour configurer les périphériques (preview)

[AZURE.INCLUDE [iot-hub-selector-twin-how-to-configure](../../includes/iot-hub-selector-twin-how-to-configure.md)]

À la fin de ce didacticiel, vous aurez deux applications console Node.js :

* **SimulateDeviceConfiguration.js**, une application de périphérique simulé qui attend une mise à jour de la configuration souhaitée et indique l’état d’un processus de mise à jour de configuration simulé.
* **SetDesiredConfigurationAndQuery**, une application console .NET destinée à être exécutés à partir de la partie principale, ce qui définit la configuration de votre choix sur un appareil et requêtes le processus de mise à jour de configuration.

> [AZURE.NOTE] L’article [IoT concentrateur SDK] [ lnk-hub-sdks] fournit des informations sur les différents kits de développement que vous pouvez utiliser pour créer des applications appareil et principale.

Pour effectuer ce didacticiel, vous devez les éléments suivants :

+ Microsoft Visual Studio 2015.

+ Version Node.js 0.10.x ou version ultérieure.

+ Un compte Azure actif. (Si vous n’avez pas un compte, vous pouvez créer un [compte gratuit] [ lnk-free-trial] en quelques minutes.)

Si vous avez suivi la [prise en main jumeaux appareil] [ lnk-twin-tutorial] didacticiel, vous disposez déjà d’un concentrateur de gestion activée appareil et une identité d’appareil appelée **myDeviceId**; et vous pouvez passer à la [créer l’application périphérique simulé] [ lnk-how-to-configure-createapp] section.

[AZURE.INCLUDE [iot-hub-get-started-create-hub-pp](../../includes/iot-hub-get-started-create-hub-pp.md)]

## <a name="create-the-simulated-device-app"></a>Créer l’application périphérique simulé

Dans cette section, vous créez une application de console Node.js qui se connecte à votre plateforme en tant que **myDeviceId**, attend une mise à jour de la configuration souhaitée et rapports puis mises à jour sur le processus de mise à jour de configuration simulé.

1. Créer un nouveau dossier vide nommé **simulatedeviceconfiguration**. Dans le dossier **simulatedeviceconfiguration** , créez un nouveau fichier package.json à l’aide de la commande suivante à votre invite de commandes. Accepter toutes les valeurs par défaut :

    ```
    npm init
    ```

2. À votre invite de commande dans le dossier **simulatedeviceconfiguration** , exécutez la commande suivante pour installer le **périphérique d’iot azure**et **azure-iot-appareil-mqtt** package :

    ```
    npm install azure-iot-device@dtpreview azure-iot-device-mqtt@dtpreview --save
    ```

3. À l’aide d’un éditeur de texte, créez un nouveau fichier **SimulateDeviceConfiguration.js** dans le dossier **simulatedeviceconfiguration** .

4. Ajoutez le code suivant dans le fichier **SimulateDeviceConfiguration.js** et remplacez l’espace réservé **{chaîne de connexion appareil}** avec la chaîne de connexion que vous avez copiée lorsque vous avez créé l’identité du périphérique **myDeviceId** :

        'use strict';
        var Client = require('azure-iot-device').Client;
        var Protocol = require('azure-iot-device-mqtt').Mqtt;

        var connectionString = '{device connection string}';
        var client = Client.fromConnectionString(connectionString, Protocol);

        client.open(function(err) {
            if (err) {
                console.error('could not open IotHub client');
            } else {
                client.getTwin(function(err, twin) {
                    if (err) {
                        console.error('could not get twin');
                    } else {
                        console.log('retrieved device twin');
                        twin.properties.reported.telemetryConfig = {
                            configId: "0",
                            sendFrequency: "24h"
                        }
                        twin.on('properties.desired', function(desiredChange) {
                            console.log("received change: "+JSON.stringify(desiredChange));
                            var currentTelemetryConfig = twin.properties.reported.telemetryConfig;
                            if (desiredChange.telemetryConfig &&desiredChange.telemetryConfig.configId !== currentTelemetryConfig.configId) {
                                initConfigChange(twin);
                            }
                        });
                    }
                });
            }
        });

    L’objet **Client** expose toutes les méthodes requises pour interagir avec jumeaux appareil de l’appareil. Le code précédent, après que qu’il l’initialisation de l’objet **Client** , récupère le double pour **myDeviceId**et attache un gestionnaire pour la mise à jour sur les propriétés souhaitées. Le gestionnaire vérifie qu’il est une demande de modification de configuration en comparant le configIds, puis appelle une méthode qui démarre le changement de configuration.

    Notez que dans un souci de simplicité, le code précédent utilise une valeur par défaut codé en dur pour la configuration initiale. Une application réelle serait charger probablement que la configuration à partir d’un stockage local.
    
    > [AZURE.IMPORTANT] Événements de modification de propriété souhaitée sont toujours émis une seule fois lors de la connexion de l’appareil, n’oubliez pas de vérifier qu’il existe une modification réelle dans les propriétés souhaitées avant d’effectuer une action.

5. Ajoutez les méthodes suivantes avant la `client.open()` appel :

        var initConfigChange = function(twin) {
            var currentTelemetryConfig = twin.properties.reported.telemetryConfig;
            currentTelemetryConfig.pendingConfig = twin.properties.desired.telemetryConfig;
            currentTelemetryConfig.status = "Pending";

            var patch = {
            telemetryConfig: currentTelemetryConfig
            };
            twin.properties.reported.update(patch, function(err) {
                if (err) {
                    console.log('Could not report properties');
                } else {
                    console.log('Reported pending config change: ' + JSON.stringify(patch));
                    setTimeout(function() {completeConfigChange(twin);}, 60000);
                }
            });
        }

        var completeConfigChange =  function(twin) {
            var currentTelemetryConfig = twin.properties.reported.telemetryConfig;
            currentTelemetryConfig.configId = currentTelemetryConfig.pendingConfig.configId;
            currentTelemetryConfig.sendFrequency = currentTelemetryConfig.pendingConfig.sendFrequency;
            currentTelemetryConfig.status = "Success";
            delete currentTelemetryConfig.pendingConfig;
            
            var patch = {
                telemetryConfig: currentTelemetryConfig
            };
            patch.telemetryConfig.pendingConfig = null;

            twin.properties.reported.update(patch, function(err) {
                if (err) {
                    console.error('Error reporting properties: ' + err);
                } else {
                    console.log('Reported completed config change: ' + JSON.stringify(patch));
                }
            });
        };

    La méthode **initConfigChange** met à jour des propriétés signalées sur l’objet double local avec la mise à jour de configuration de la demande et définit le statut sur **en attente**, puis met à jour le double appareil sur le service. Après mise à jour de la double, il simule un processus long qui se termine à l’exécution de **completeConfigChange**. Cette méthode met à jour les propriétés signalés de double local définissant le statut sur **Réussite** et suppression de l’objet **pendingConfig** . Puis, il met à jour le double sur le service.

    Notez que, à enregistrer la bande passante, indiquées sont mises à jour en spécifiant uniquement les propriétés pour être modifié (nommée **correctif** dans le code ci-dessus), au lieu de remplacer l’ensemble du document.

    > [AZURE.NOTE] Ce didacticiel simuler pas tout comportement des mises à jour de la configuration simultanées. Certains processus de mise à jour de configuration peuvent être en mesure de prendre en compte les modifications de la configuration de la cible pendant la mise à jour est en cours d’exécution, d’autres personnes peut-être en file d’attente les et d’autres personnes peuvent les rejeter avec une condition d’erreur. Veillez à prendre en considération le comportement souhaité pour votre processus de configuration spécifiques et ajoutez la logique appropriée avant de lancer la modification de configuration.

6. Exécutez l’application d’appareil :

        node SimulateDeviceConfiguration.js

    Vous devriez voir le message `retrieved device twin`. Maintenir l’application en cours d’exécution.

## <a name="create-the-service-app"></a>Créer l’application de service

Dans cette section, vous allez créer une application console .NET qui met à jour les *propriétés souhaitées* dans la double associé à **myDeviceId** avec un nouvel objet de configuration de télémétrie. Ensuite, la jumeaux appareil stockées dans le hub des requêtes et indique la différence entre les configurations signalées et de votre choix de l’appareil.

1. Dans Visual Studio, ajoutez un projet Visual c# bureau classique de Windows à la solution actuelle en utilisant le modèle de projet **Application Console** . Nommez le projet **SetDesiredConfigurationAndQuery**.

    ![Nouveau projet Visual c# bureau classique de Windows][img-createapp]

2. Dans l’Explorateur, droit sur le projet **SetDesiredConfigurationAndQuery** , puis cliquez sur **Gérer les Packages Nuget**.

3. Dans la fenêtre **Gestionnaire de Package Nuget** , vérifiez que la case à cocher **inclure la version préliminaire** est activée, recherchez **microsoft.azure.devices**, sélectionnez **installer** pour installer le la version *préliminaire* de la **Microsoft.Azure.Devices** du package et acceptez les conditions d’utilisation. Cette procédure téléchargements, installations et ajoute une référence dans le [Kit de développement logiciel Microsoft Azure IoT Service] [ lnk-nuget-service-sdk] Nuget package et ses dépendances.

    ![Fenêtre du Gestionnaire de Package NuGet][img-servicenuget]

4. Ajoutez le code suivant `using` instructions dans la partie supérieure du fichier **Program.cs** :

        using Microsoft.Azure.Devices;
        using System.Threading;
        using Newtonsoft.Json;

5. Ajoutez les champs suivants à la classe **Program** . Remplacez la valeur de l’espace réservé par la chaîne de connexion pour le hub IoT que vous avez créé dans la section précédente.

        static RegistryManager registryManager;
        static string connectionString = "{iot hub connection string}";

6. Ajoutez la méthode suivante à la classe **Program** :

        static private async Task SetDesiredConfigurationAndQuery()
        {
            var twin = await registryManager.GetTwinAsync("myDeviceId");
            var patch = new {
                    properties = new {
                        desired = new {
                            telemetryConfig = new {
                                configId = Guid.NewGuid().ToString(),
                                sendFrequency = "5m"
                            }
                        }
                    }
                };
            
            await registryManager.UpdateTwinAsync(twin.DeviceId, JsonConvert.SerializeObject(patch), twin.ETag);
            Console.WriteLine("Updated desired state");

            while (true)
            {
                var query = registryManager.CreateQuery("SELECT * FROM devices WHERE deviceId = 'myDeviceId'");
                var results = await query.GetNextAsTwinAsync();
                foreach (var result in results)
                {
                    Console.WriteLine("Config report for: {0}", result.DeviceId);
                    Console.WriteLine("Desired telemetryConfig: {0}", JsonConvert.SerializeObject(result.Properties.Desired["telemetryConfig"], Formatting.Indented));
                    Console.WriteLine("Reported telemetryConfig: {0}", JsonConvert.SerializeObject(result.Properties.Reported["telemetryConfig"], Formatting.Indented));
                    Console.WriteLine();
                }
                Thread.Sleep(10000);
            }
        }

    L’objet **Registry** expose toutes les méthodes requises pour interagir avec jumeaux appareil à partir du service. Le code précédent, après que qu’il l’initialisation de l’objet **Registry** , récupère le double pour **myDeviceId**et met à jour ses propriétés souhaitées avec un nouvel objet de configuration de télémétrie.
    Ensuite, toutes les 10 secondes, elle la jumeaux stockées dans le hub des requêtes et imprime les configurations de télémétrie signalés et de votre choix. Reportez-vous à la [langue de la requête IoT concentrateur] [ lnk-query] pour apprendre à générer des rapports riches sur tous vos appareils.

    > [AZURE.IMPORTANT] Cette application requêtes IoT concentrateur toutes les 10 secondes à titre d’exemple. Utiliser des requêtes pour générer des rapports rencontrées par les utilisateurs de nombreux appareils et non à détecter des modifications. Si votre solution requiert des notifications en temps réel d’événements appareil utilisent les [messages d’appareil-nuage][lnk-d2c].

7. Enfin, ajoutez les lignes suivantes à la méthode **Main** :

        registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        SetDesiredConfigurationAndQuery();
        Console.WriteLine("Press any key to quit.");
        Console.ReadLine();

8. **SimulateDeviceConfiguration.js** en cours d’exécution, exécuter l’application .NET à partir de Visual Studio à l’aide de **F5** et que vous devriez voir la configuration signalée modifier **bloquent** **en attente** **succès** à nouveau avec le nouveau actif fréquence d’envoi des cinq minutes au lieu de 24 heures.

    > [AZURE.IMPORTANT] Il y a un délai de devenir une minute entre l’opération de rapport appareil et le résultat de la requête. Il s’agit pour activer l’infrastructure de requête pour l’utiliser à l’échelle très haute. Pour récupérer les affichages cohérentes d’un seul double utilisent la méthode **getDeviceTwin** dans la classe **Registry** .

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous définissez une configuration souhaitée comme *vous le souhaitez propriétés* à partir de la partie principale et avez écrit une application d’appareil pour détecter cette modification et de simuler un processus de mise à jour de plusieurs étapes reporting son statut en tant que *Propriétés signalées* à la double.

Utilisez les ressources suivantes pour découvrir comment :

- envoyer de télémétrie à partir d’appareils avec la [prise en main IoT concentrateur] [ lnk-iothub-getstarted] didacticiel,
- planifier ou exécuter des opérations sur les grands ensembles de périphériques Voir les [travaux utiliser pour planifier et diffuser des opérations d’unité] [ lnk-schedule-jobs] didacticiel.
- contrôler les périphériques de façon interactive (par exemple, activer un ventilateurs à partir d’une application contrôlée par l’utilisateur), avec les [méthodes directes utiliser] [ lnk-methods-tutorial] didacticiel.

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-node-twin-getstarted/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-node-twin-getstarted/createnetapp.png

<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/1.1.0-preview-004

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-dm-overview]: iot-hub-device-management-overview.md
[lnk-twin-tutorial]: iot-hub-node-node-twin-getstarted.md
[lnk-schedule-jobs]: iot-hub-schedule-jobs.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
[lnk-device-management]: iot-hub-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-iothub-getstarted]: iot-hub-node-node-getstarted.md
[lnk-methods-tutorial]: iot-hub-c2d-methods.md

[lnk-guid]: https://en.wikipedia.org/wiki/Globally_unique_identifier

[lnk-how-to-configure-createapp]: iot-hub-node-node-twin-how-to-configure.md#create-the-simulated-device-app
