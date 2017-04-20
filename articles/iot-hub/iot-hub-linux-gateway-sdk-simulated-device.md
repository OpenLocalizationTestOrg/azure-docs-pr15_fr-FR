<properties
    pageTitle="Simuler un appareil avec le Kit de développement de passerelle IoT | Microsoft Azure"
    description="Azure IoT passerelle SDK procédure pas à pas pour illustrer télémétrie envoi d’un appareil simulé à l’aide du Kit de développement Azure IoT passerelle à l’aide de Linux."
    services="iot-hub"
    documentationCenter=""
    authors="chipalost"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="cpp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/29/2016"
     ms.author="andbuc"/>


# <a name="azure-iot-gateway-sdk-beta--send-device-to-cloud-messages-with-a-simulated-device-using-linux"></a>Azure IoT passerelle Kit de développement logiciel (version bêta) – envoyer des messages avec un appareil simulé à l’aide de Linux appareil-nuage

[AZURE.INCLUDE [iot-hub-gateway-sdk-simulated-selector](../../includes/iot-hub-gateway-sdk-simulated-selector.md)]

## <a name="build-and-run-the-sample"></a>Créer et exécuter l’exemple

Avant de commencer, vous devez :

- [Configurer votre environnement de développement] [ lnk-setupdevbox] pour travailler avec le Kit de développement sous Linux.
- [Créer un concentrateur IoT] [ lnk-create-hub] dans votre abonnement Azure, vous devez le nom de votre concentrateur pour effectuer cette procédure. Si vous n’avez pas un compte, vous pouvez créer un [compte gratuit] [ lnk-free-trial] en quelques minutes.
- Ajouter deux appareils à votre concentrateur IoT et prenez note de leurs ID et les clés de l’appareil. Vous pouvez utiliser l' [Explorateur de périphérique ou iothub explorer] [ lnk-explorer-tools] outil pour ajouter vos périphériques au concentrateur IoT vous avez créé à l’étape précédente et récupérer leurs clés.

Pour créer l’exemple :

1. Ouvrez un shell.
2. Accédez au dossier racine dans votre copie locale du **Kit de développement logiciel iot azure passerelle** référentiel.
3. Exécuter le script **tools/build.sh** . Ce script utilise l’utilitaire **cmake** pour créer un dossier nommé **créer** dans le dossier racine de votre copie locale du **Kit de développement logiciel iot azure passerelle** référentiel et générer un makefile. Ensuite, le script génère la solution et exécute les tests.

> [AZURE.NOTE]  Chaque fois que vous exécutez le script **build.sh** , il supprime et recrée ensuite le dossier **créer** dans le dossier racine de votre copie locale du **Kit de développement logiciel iot azure passerelle** référentiel.

Pour exécuter l’exemple :

Dans un éditeur de texte, ouvrez le fichier **samples/simulated_device_cloud_upload/src/simulated_device_cloud_upload_lin.json** dans votre copie locale du **Kit de développement logiciel iot azure passerelle** référentiel. Ce fichier configure les modules dans l’exemple de passerelle :

- Le module **IoTHub** se connecte à votre plateforme IoT. Vous devez le configurer pour envoyer des données à votre plateforme IoT. Plus précisément, définissez la valeur **IoTHubName** sur le nom de votre plateforme IoT et définissez la valeur **IoTHubSuffix** **azure devices.net**. Définissez la valeur de **Transport** à l’un des : « HTTP », « AMQP » ou « MQTT ». Notez que pour l’instant, seul « HTTP » partagera une connexion TCP pour tous les messages de l’appareil. Si vous définissez la valeur « AMQP » ou « MQTT », la passerelle conservera une connexion TCP distincte à IoT concentrateur pour chaque périphérique.
- Le module de **mappage** mappe les adresses MAC de vos périphériques simulés à votre ID d’appareil IoT concentrateur. Assurez-vous que les valeurs **d’ID de périphérique** correspondent aux codes des deux périphériques que vous avez ajouté à votre concentrateur IoT et que les valeurs **deviceKey** contiennent les clés de vos périphériques de deux.
- Les modules **BLE1** et **BLE2** sont les appareils simulés. Remarque : comment leurs adresses MAC correspondent à celles dans le module de **mappage** .
- Le module **journal** enregistre votre activité de passerelle vers un fichier.
- Les valeurs de **chemin d’accès du module** ci-dessous part du principe que vous exécuterez l’échantillon depuis la racine de votre copie locale du **Kit de développement logiciel iot azure passerelle** référentiel.
- Le tableau **des liens** en bas du fichier JSON connecte les modules **BLE1** et **BLE2** pour le module de **mappage** et le module de **mappage** pour le module **IoTHub** . Elle garantit également que tous les messages sont enregistrés par le module de **journal** .

```
{
    "modules" :
    [ 
        {
            "module name" : "IoTHub",
            "module path" : "./build/modules/iothub/libiothub_hl.so",
            "args" : 
            {
                "IoTHubName" : "{Your IoT hub name}",
                "IoTHubSuffix" : "azure-devices.net",
                "Transport": "HTTP"
            }
        },
        {
            "module name" : "mapping",
            "module path" : "./build/modules/identitymap/libidentity_map_hl.so",
            "args" : 
            [
                {
                    "macAddress" : "01-01-01-01-01-01",
                    "deviceId"   : "{Device ID 1}",
                    "deviceKey"  : "{Device key 1}"
                },
                {
                    "macAddress" : "02-02-02-02-02-02",
                    "deviceId"   : "{Device ID 2}",
                    "deviceKey"  : "{Device key 2}"
                }
            ]
        },
        {
            "module name":"BLE1",
            "module path" : "./build/modules/simulated_device/libsimulated_device_hl.so",
            "args":
            {
                "macAddress" : "01-01-01-01-01-01"
            }
        },
        {
            "module name":"BLE2",
            "module path" : "./build/modules/simulated_device/libsimulated_device_hl.so",
            "args":
            {
                "macAddress" : "02-02-02-02-02-02"
            }
        },
        {
            "module name":"Logger",
            "module path" : "./build/modules/logger/liblogger_hl.so",
            "args":
            {
                "filename":"./deviceCloudUploadGatewaylog.log"
            }
        }
    ],
    "links" : [
        { "source" : "*", "sink" : "Logger" },
        { "source" : "BLE1", "sink" : "mapping" },
        { "source" : "BLE2", "sink" : "mapping" },
        { "source" : "mapping", "sink" : "IoTHub" }
    ]
}

```

Enregistrer toutes les modifications apportées au fichier de configuration.

Pour exécuter l’exemple :

1. Dans votre shell, accédez au dossier racine dans votre copie locale du **Kit de développement logiciel iot azure passerelle** référentiel.
2. Exécutez la commande suivante :

    ```
    ./build/samples/simulated_device_cloud_upload/simulated_device_cloud_upload_sample ./samples/simulated_device_cloud_upload/src/simulated_device_cloud_upload_lin.json
    ```

3. Vous pouvez utiliser l' [Explorateur de périphérique ou iothub explorer] [ lnk-explorer-tools] outil pour contrôler les messages IoT concentrateur reçoit de la passerelle.

## <a name="next-steps"></a>Étapes suivantes

Si vous souhaitez comprendre plus avancées le Kit de développement de passerelle IoT et tester les quelques exemples de code, consultez les ressources et les didacticiels pour les développeurs suivants :

- [Envoyer des messages de l’appareil-nuage à partir d’un périphérique réel avec le Kit de développement de passerelle IoT][lnk-physical-device]
- [Kit de développement logiciel Azure IoT passerelle][lnk-gateway-sdk]

Pour en savoir davantage les fonctionnalités du concentrateur IoT, voir :

- [Guide du développeur][lnk-devguide]
- [Sécurisez votre solution IoT notions fondamentales sur vers le haut][lnk-securing]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-explorer-tools]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/manage_iot_hub.md
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/

[lnk-physical-device]: iot-hub-gateway-sdk-physical-device.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-securing]: iot-hub-security-ground-up.md
[lnk-create-hub]: iot-hub-create-through-portal.md