<properties
    pageTitle="Utiliser un périphérique réel avec le Kit de développement de passerelle IoT | Microsoft Azure"
    description="Azure IoT passerelle SDK procédure pas à pas à l’aide d’un appareil Texas Instruments SensorTag pour envoyer des données à concentrateur IoT via une passerelle s’exécutant sur un Module de calculer Edison Intel"
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


# <a name="azure-iot-gateway-sdk-beta--send-device-to-cloud-messages-with-a-real-device-using-linux"></a>Azure IoT passerelle Kit de développement logiciel (version bêta) – envoyer des messages avec un appareil réel à l’aide de Linux appareil-nuage

Cette procédure pas à pas de l' [exemple de faible énergétique Bluetooth] [ lnk-ble-samplecode] vous montre comment utiliser le [Kit de développement logiciel Azure IoT passerelle] [ lnk-sdk] à transférer télémétrie appareil-nuage à IoT concentrateur à partir d’un périphérique physique et comment acheminer les commandes IoT concentrateur vers un périphérique physique.

Cette procédure pas à pas couvre :

* **Architecture**: architecture des informations importantes l’échantillon énergétique faible Bluetooth.

* **Créer et exécuter**: les étapes nécessaires pour créer et exécuter l’exemple.

## <a name="architecture"></a>Architecture

La procédure pas à pas vous montre comment créer et exécuter une passerelle IoT sur un Module Edison calculer Intel qui s’exécute Linux. La passerelle est créée en utilisant le Kit de développement de passerelle IoT. L’exemple utilise un appareil Texas Instruments SensorTag Bluetooth faible énergétique (tableau) pour recueillir des données de température.

Lorsque vous exécutez la passerelle il :

- Se connecte à un appareil SensorTag en utilisant le protocole Bluetooth faible énergétique (tableau).
- Se connecte à concentrateur IoT en utilisant le protocole HTTP.
- Transfère télémétrie de l’appareil SensorTag à IoT concentrateur.
- Achemine commandes IoT concentrateur à l’appareil SensorTag.

La passerelle contient les modules suivants :

- *Module de tableau* qui interagit avec un appareil de tableau de recevoir les données de température des commandes de l’appareil et envoyez-le à l’appareil.
- *Tableau nuage module appareil* qui convertit les messages JSON entrants à partir du cloud dans les instructions de tableau pour le *module de tableau*.
- *Module de journal* qui enregistre tous les messages de passerelle.
- Un *module de mappage d’identité* devient entre les adresses MAC des périphériques tableau et identités des appareils Azure IoT concentrateur.
- Un *module IoT concentrateur* qui télécharge les données de télémétrie à un concentrateur IoT et reçoit des commandes de l’appareil un concentrateur IoT.
- Un *module d’imprimante tableau* qui interprète télémétrie de l’appareil de tableau et imprime mis en forme les données sur la console pour activer la résolution des problèmes et le débogage.

### <a name="how-data-flows-through-the-gateway"></a>Le flux des données via la passerelle

Le diagramme de bloc suivant illustre le pipeline de flux de données de téléchargement télémétrie :

![](media/iot-hub-gateway-sdk-physical-device/gateway_ble_upload_data_flow.png)

Les étapes un élément de télémétrie vous permet d’accéder à IoT concentrateur se déplacer d’un appareil de tableau sont :

1. Le périphérique de tableau génère un échantillon température et envoie via Bluetooth vers le module de tableau de la passerelle.
2. Le module de tableau reçoit l’échantillon et publie à l’intermédiaire ainsi que l’adresse MAC de l’appareil.
3. Le module de mappage d’identité récupère ce message et utilise une table interne pour traduire l’adresse MAC du périphérique en une identité de périphérique concentrateur IoT (un ID de périphérique et une clé de périphérique). Il publie ensuite un message qui contient les données d’exemple température, l’adresse MAC de l’appareil, l’ID du périphérique et la clé du périphérique.
4. Le module IoT concentrateur reçoit ce nouveau message (généré par le module de mappage d’identité) et publie à IoT concentrateur.
5. Le module journal enregistre tous les messages à partir de l’intermédiaire vers un fichier de disque.

Le diagramme de bloc suivant illustre le pipeline de flux de données de commande appareil :

![](media/iot-hub-gateway-sdk-physical-device/gateway_ble_command_data_flow.png)

1. Le module concentrateur IoT consulte régulièrement le hub IoT pour les nouveaux messages de commande.
2. Lorsque le module IoT concentrateur reçoit un nouveau message de commande, il publie à l’intermédiaire.
3. Le module de mappage d’identité récupère le message de commande et utilise une table interne pour traduire l’ID du périphérique concentrateur IoT sur un appareil adresse MAC. Il publie ensuite un nouveau message qui inclut l’adresse MAC du périphérique cible dans l’Explorateur de propriétés du message.
4. Le nuage tableau module appareil récupère ce message et il se traduit par l’instruction tableau appropriée pour le module de tableau. Il publie ensuite un nouveau message.
5. Le module de tableau récupère ce message et permet de lancer l’instruction e/s en communication avec le périphérique de tableau.
6. Le module journal enregistre tous les messages à partir de l’intermédiaire vers un fichier de disque.

## <a name="prepare-your-hardware"></a>Préparer votre matériel

Ce didacticiel suppose que vous utilisez un périphérique [Texas Instruments SensorTag](http://www.ti.com/ww/en/wireless_connectivity/sensortag2015/index.html) connecté à un tableau Edison Intel.

### <a name="set-up-the-edison-board"></a>Configurer le forum Edison

Avant de commencer, vérifiez que vous pouvez vous connecter votre périphérique Edison à votre réseau sans fil. Pour configurer votre appareil Edison, vous devez vous connecter à un ordinateur hôte. Intel fournit la prise en main des repères pour les systèmes d’exploitation suivants :

- [Prise en main le forum de développement Intel Edison sur Windows 64 bits][lnk-setup-win64].
- [Prise en main le forum de développement Intel Edison sur Windows 32 bits][lnk-setup-win32].
- [Prise en main le forum de développement Intel Edison sur Mac OS X][lnk-setup-osx].
- [Prise en main le forum Edison Intel® sous Linux][lnk-setup-linux].

Pour configurer votre appareil Edison et familiarisez-vous avec lui, vous devez effectuer toutes les étapes décrites dans ces « prise en main » des articles à l’exception de la dernière étape, « Choisissez IDE », qui n’est pas nécessaire pour le didacticiel en cours. À la fin du processus d’installation Edison vous avez :

- Mise jour votre Edison avec du dernier microprogramme.
- Établir une connexion en série de votre hôte vers la Edison.
- Exécuter le script **configure_edison** pour définir un mot de passe et activer le Wi-Fi sur votre Edison.

### <a name="enable-connectivity-to-the-sensortag-device-from-your-edison-board"></a>Activer la connectivité à l’appareil SensorTag à partir de votre carte Edison

Avant d’exécuter l’échantillon, vous devez vérifier que votre carte Edison peut se connecter à l’appareil SensorTag.

Vous devez tout d’abord vérifier que votre Edison peut se connecter à l’appareil SensorTag.

1. Débloquer bluetooth sur le Edison et vérifiez que le numéro de version est **5.37**.
    
    ```
    rfkill unblock bluetooth
    bluetoothctl --version
    ```

2. Exécuter la commande **bluetoothctl** . Vous êtes maintenant dans un shell interactif bluetooth. 

3. Entrez la commande **power sur** le contrôleur bluetooth sous tension. Vous devriez voir ressemblant à :
    
    ```
    [NEW] Controller 98:4F:EE:04:1F:DF edison [default]
    ```

4. Dans l’interface bluetooth interactif, entrez la commande **analyse dans** pour rechercher des appareils bluetooth. Vous devriez voir ressemblant à :
    
    ```
    Discovery started
    [CHG] Controller 98:4F:EE:04:1F:DF Discovering: yes
    ```

5. Rendre SensorTag détectable en appuyant sur le bouton petit (la LED verte doit faire clignoter). La Edison doit rechercher le périphérique SensorTag :
    
    ```
    [NEW] Device A0:E6:F8:B5:F6:00 CC2650 SensorTag
    [CHG] Device A0:E6:F8:B5:F6:00 TxPower: 0
    [CHG] Device A0:E6:F8:B5:F6:00 RSSI: -43
    ```
    
    Dans cet exemple, vous pouvez voir que l’adresse MAC du périphérique SensorTag est **A0:E6:F8:B5:F6:00**.

6. Désactiver l’analyse en entrant la commande **analyse désactiver** .
    
    ```
    [CHG] Controller 98:4F:EE:04:1F:DF Discovering: no
    Discovery stopped
    ```

7. Se connecter à votre appareil SensorTag à l’aide de son adresse MAC en entrant **connecter \<adresse MAC >**. Notez que l’exemple de sortie ci-dessous est abrégé :
    
    ```
    Attempting to connect to A0:E6:F8:B5:F6:00
    [CHG] Device A0:E6:F8:B5:F6:00 Connected: yes
    Connection successful
    [CHG] Device A0:E6:F8:B5:F6:00 UUIDs: 00001800-0000-1000-8000-00805f9b34fb
    ...
    [NEW] Primary Service
            /org/bluez/hci0/dev_A0_E6_F8_B5_F6_00/service000c
            Device Information
    ...
    [CHG] Device A0:E6:F8:B5:F6:00 GattServices: /org/bluez/hci0/dev_A0_E6_F8_B5_F6_00/service000c
    ...
    [CHG] Device A0:E6:F8:B5:F6:00 Name: SensorTag 2.0
    [CHG] Device A0:E6:F8:B5:F6:00 Alias: SensorTag 2.0
    [CHG] Device A0:E6:F8:B5:F6:00 Modalias: bluetooth:v000Dp0000d0110
    ```
    
    Remarque : Vous pouvez répertorier les caractéristiques GATT de l’appareil à nouveau à l’aide de la commande **attributs de liste** .

8. Vous pouvez maintenant déconnecter le périphérique à l’aide de la commande **Déconnecter** et quitter puis à partir du shell bluetooth à l’aide de la commande **Quitter** :
    
    ```
    Attempting to disconnect from A0:E6:F8:B5:F6:00
    Successful disconnected
    [CHG] Device A0:E6:F8:B5:F6:00 Connected: no
    ```

Vous êtes maintenant prêt à exécuter l’exemple de tableau passerelle sur votre appareil Edison.

## <a name="run-the-ble-gateway-sample"></a>Exécuter l’exemple de tableau passerelle

Pour exécuter l’exemple de tableau dans votre Edison, vous devez effectuer les trois tâches :

- Configurer des deux périphériques exemple dans votre concentrateur IoT.
- Créer le Kit de développement de passerelle IoT sur votre appareil Edison.
- Configurer et exécuter l’exemple de tableau sur votre appareil Edison.

Au moment de l’écriture, le Kit de développement de passerelle IoT prend uniquement en charge les passerelles qui utilisent des modules de tableau sous Linux.

### <a name="configure-two-sample-devices-in-your-iot-hub"></a>Configurer des deux périphériques d’exemple dans votre concentrateur IoT

- [Créer un concentrateur IoT] [ lnk-create-hub] dans votre abonnement Azure, vous devez le nom de votre concentrateur pour effectuer cette procédure. Si vous n’avez pas un compte, vous pouvez créer un [compte gratuit] [ lnk-free-trial] en quelques minutes.
- Ajouter un périphérique appelé **SensorTag_01** à votre concentrateur IoT et prenez note de sa clé id et appareil. Vous pouvez utiliser l' [Explorateur de périphérique ou iothub explorer] [ lnk-explorer-tools] outils pour ajouter cet appareil au hub IoT que vous avez créé à l’étape précédente et récupérer sa clé. Vous allez mapper cet appareil à l’appareil SensorTag lorsque vous configurez la passerelle.

### <a name="build-the-iot-gateway-sdk-on-your-edison-device"></a>Créer le Kit de développement de passerelle IoT sur votre appareil Edison

La version de **git** sur la Edsion ne prend pas en charge submodules. Pour télécharger la source complète pour le Kit de développement de passerelle IoT à la Edison que vous avez deux possibilités :

- Option #1 : Cloner le [Kit de développement logiciel Azure IoT passerelle] [ lnk-sdk] référentiel de votre Edison et puis cloner manuellement le référentiel pour chaque submodule.
- Option #2 : Cloner le [Kit de développement logiciel Azure IoT passerelle] [ lnk-sdk] référentiel sur un appareil bureau où **git** prend en charge submodules, puis copiez le référentiel complète avec submodules sur votre Edison.

Si vous choisissez l’option #2, utilisez les commandes **git** suivantes pour cloner le Kit de développement de passerelle IoT et tous ses submodules :

```
git clone --recursive https://github.com/Azure/azure-iot-gateway-sdk.git 
git submodule update --init --recursive
```

Vous devez puis zip l’intégralité du référentiel local dans un même fichier archive avant de le copier vers le Edison. Vous pouvez utiliser un utilitaire tel que **pscp** qui est inclus avec **Putty** pour copier le fichier d’archive vers la Edison. Par exemple :

```
pscp .\gatewaysdk.zip root@192.168.0.45:/home/root
```

Lorsque vous avez une copie complète du référentiel IoT passerelle SDK sur votre Edison, vous pouvez générer à l’aide de la commande suivante à partir du dossier qui contient le Kit de développement :

```
./tools/build.sh
```

### <a name="configure-and-run-the-ble-sample-on-your-edison-device"></a>Configurer et exécuter l’exemple de tableau sur votre appareil Edison

Pour démarrer et exécuter l’exemple, vous devez configurer chaque module qui fait partie de la passerelle. Cette configuration est fournie dans un fichier JSON et vous devez configurer tous les cinq modules participants. Il est un exemple de fichier JSON fourni dans le référentiel appelé **gateway_sample.json** que vous pouvez utiliser comme point de départ pour créer votre propre fichier de configuration. Ce fichier est dans le dossier **exemples/ble_gateway_hl/src** dans la copie locale du référentiel IoT passerelle SDK.

Les sections suivantes décrivent comment modifier ce fichier de configuration pour l’exemple de tableau et part du principe que le référentiel IoT passerelle SDK se trouve dans le **/home/root/azure-iot-gateway-sdk /** dossier sur votre appareil Edison. Si le référentiel se trouve ailleurs, vous devez ajuster les chemins d’accès en conséquence :

#### <a name="logger-configuration"></a>Configuration de journal

En supposant que le référentiel de passerelle se trouve dans le dossier **/home/root/azure-iot-gateway-sdk /**, configurez le module journal comme suit :

```json
{
    "module name": "logger",
    "module path": "/home/root/azure-iot-gateway-sdk/build/modules/logger/liblogger_hl.so",
    "args":
    {
        "filename":"/home/root/gw_logger.log"
    }
}
```

#### <a name="ble-module-configuration"></a>Configuration du module tableau

L’exemple de configuration pour l’appareil tableau suppose un appareil Texas Instruments SensorTag. N’importe quel appareil tableau standard qui peut fonctionner comme un périphérique GATT devrait fonctionner mais vous devez mettre à jour les ID caractéristique GATT et les données (pour obtenir des instructions d’écriture). Ajoutez l’adresse MAC de votre appareil SensorTag : 

```json
{
  "module name": "SensorTag",
  "module path": "/home/root/azure-iot-gateway-sdk/build/modules/ble/libble_hl.so",
  "args": {
    "controller_index": 0,
    "device_mac_address": "<<AA:BB:CC:DD:EE:FF>>",
    "instructions": [
      {
        "type": "read_once",
        "characteristic_uuid": "00002A24-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A25-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A26-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A27-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A28-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A29-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "write_at_init",
        "characteristic_uuid": "F000AA02-0451-4000-B000-000000000000",
        "data": "AQ=="
      },
      {
        "type": "read_periodic",
        "characteristic_uuid": "F000AA01-0451-4000-B000-000000000000",
        "interval_in_ms": 1000
      },
      {
        "type": "write_at_exit",
        "characteristic_uuid": "F000AA02-0451-4000-B000-000000000000",
        "data": "AA=="
      }
    ]
  }
}
```

#### <a name="iot-hub-module"></a>Module IoT concentrateur

Ajoutez le nom de votre plateforme IoT. La valeur de suffixe est généralement **azure devices.net**:

```json
{
  "module name": "IoTHub",
  "module path": "/home/root/azure-iot-gateway-sdk/build/modules/iothub/libiothub_hl.so",
  "args": {
    "IoTHubName": "<<Azure IoT Hub Name>>",
    "IoTHubSuffix": "<<Azure IoT Hub Suffix>>",
    "Transport": "HTTP"
  }
}
```

#### <a name="identity-mapping-module-configuration"></a>Configuration du module identité mappage

Ajoutez l’adresse MAC de votre appareil SensorTag et l’Id de l’appareil et la clé de l’appareil **SensorTag_01** que vous avez ajouté à votre concentrateur IoT :

```json
{
  "module name": "mapping",
  "module path": "/home/root/azure-iot-gateway-sdk/build/modules/identitymap/libidentity_map_hl.so",
  "args": [
    {
      "macAddress": "<<AA:BB:CC:DD:EE:FF>>",
      "deviceId": "<<Azure IoT Hub Device ID>>",
      "deviceKey": "<<Azure IoT Hub Device Key>>"
    }
  ]
}
```

#### <a name="ble-printer-module-configuration"></a>Configuration du module imprimante de tableau

```json
{
    "module name": "BLE Printer",
    "module path": "/home/root/azure-iot-gateway-sdk/build/samples/ble_gateway_hl/ble_printer/libble_printer.so",
    "args": null
}
```

#### <a name="routing-configuration"></a>Configuration de routage

La configuration suivante vérifie les éléments suivants :
- Le module **journal** reçoit et consigner tous les messages.
- Le module **SensorTag** envoie des messages pour le **mappage** et le **Tableau imprimante** modules.
- Le module de **mappage** envoie des messages vers le module **IoTHub** soient envoyées à votre concentrateur IoT vers le haut.
- Le module **IoTHub** envoie des messages sur le module de **mappage** .
- Le module de **mappage** envoie des messages sur le module **SensorTag** .

```json
"links" : [
    {"source" : "*", "sink" : "Logger" },
    {"source" : "SensorTag", "sink" : "mapping" },
    {"source" : "SensorTag", "sink" : "BLE Printer" },
    {"source" : "mapping", "sink" : "IoTHub" },
    {"source" : "IoTHub", "sink" : "mapping" },
    {"source" : "mapping", "sink" : "SensorTag" }
  ]
```

Pour exécuter l’exemple vous exécutez **ble_gateway_hl** binaire passant le chemin d’accès au fichier de configuration JSON. Si vous avez utilisé le fichier **gateway_sample.json** , la commande à exécuter ressemble à ceci :

```
./build/samples/ble_gateway_hl/ble_gateway_hl ./samples/ble_gateway_hl/src/gateway_sample.json
```

Vous devrez peut-être appuyez sur le bouton petit sur le périphérique SensorTag pour la découverte avant d’exécuter l’échantillon.

Lorsque vous exécutez l’exemple, vous pouvez utiliser [Explorateur de périphérique ou iothub explorer] [ lnk-explorer-tools] outil pour contrôler les messages de la passerelle transfère à partir du périphérique SensorTag.

## <a name="send-cloud-to-device-messages"></a>Envoyer des messages de cloud vers le périphérique

Le module de tableau prend également en charge l’envoi d’instructions à partir du IoT Hub Azure à l’appareil. Vous pouvez utiliser l' [Explorateur de périphérique Azure IoT concentrateur](https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/doc/how_to_use_device_explorer.md) ou l' [Explorateur de concentrateur IoT](https://github.com/Azure/azure-iot-sdks/tree/master/tools/iothub-explorer) pour envoyer des messages JSON qui le module de passerelle tableau passe le périphérique de tableau. Par exemple, si vous utilisez le périphérique Texas Instruments SensorTag vous pouvez envoyer les messages JSON suivants à l’appareil IoT concentrateur.

- Réinitialiser tous les voyants et l’alarme (les désactiver)

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "AA=="
    }
    ```

- Configurer des e/s en tant que 'distant'

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA66-0451-4000-B000-000000000000",
      "data": "AQ=="
    }
    ```

- Activer la LED rouge

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "AQ=="
    }
    ```

- Activer le vert

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "Ag=="
    }
    ```

- Activer l’alarme

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "BA=="
    }
    ```

Le comportement par défaut pour un périphérique utilisant le protocole HTTP pour vous connecter à un concentrateur IoT consiste à vérifier chaque 25 minutes pour une nouvelle commande. Par conséquent, si vous envoyez plusieurs commandes distinctes, vous devez attendre 25 minutes pour que l’appareil de recevoir chaque commande.

> [AZURE.NOTE] La passerelle vérifie également pour les nouvelles commandes chaque fois qu’il démarre afin que vous pouvez le forcer pour le traitement d’une commande à arrêter et redémarrer la passerelle.

## <a name="next-steps"></a>Étapes suivantes

Si vous souhaitez comprendre plus avancées le Kit de développement de passerelle IoT et tester les quelques exemples de code, consultez les ressources et les didacticiels pour les développeurs suivants :

- [Kit de développement logiciel Azure IoT passerelle][lnk-sdk]

Pour en savoir davantage les fonctionnalités du concentrateur IoT, voir :

- [Guide du développeur][lnk-devguide]

<!-- Links -->
[lnk-ble-samplecode]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/samples/ble_gateway_hl
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-explorer-tools]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/manage_iot_hub.md
[lnk-setup-win64]: https://software.intel.com/get-started-edison-windows
[lnk-setup-win32]: https://software.intel.com/get-started-edison-windows-32
[lnk-setup-osx]: https://software.intel.com/get-started-edison-osx
[lnk-setup-linux]: https://software.intel.com/get-started-edison-linux
[lnk-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/


[lnk-devguide]: iot-hub-devguide.md
[lnk-create-hub]: iot-hub-create-through-portal.md 
