<properties
   pageTitle="Connecter un périphérique utilisant C sur mbed | Microsoft Azure"
   description="Décrit comment connecter un appareil à la Suite de IoT Azure préconfiguré à distance solution d’analyse à l’aide d’une application écrite en C en cours d’exécution sur un appareil mbed."
   services=""
   suite="iot-suite"
   documentationCenter="na"
   authors="dominicbetts"
   manager="timlt"
   editor=""/>

<tags
   ms.service="iot-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/05/2016"
   ms.author="dobett"/>


# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-mbed"></a>Connectez votre appareil à la solution préconfigurée contrôle à distance (mbed)

[AZURE.INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## <a name="build-and-run-the-c-sample-solution"></a>Créer et exécuter la solution C

Suivez les instructions ci-dessous décrivent la procédure de connexion une [Compatibles avec les mbed Freescale FRDM-K64F] [ lnk-mbed-home] appareil à la solution d’analyse à distance.

### <a name="connect-the-mbed-device-to-your-network-and-desktop-machine"></a>Connectez le périphérique mbed sur votre ordinateur de bureau et de réseau

1. Connectez le périphérique mbed à votre réseau à l’aide d’un câble Ethernet. Cette étape est nécessaire, car l’exemple d’application requiert un accès à internet.

2. Voir [Prise en main mbed] [ lnk-mbed-getstarted] pour connecter votre périphérique mbed à votre ordinateur de bureau.

3. Si votre ordinateur de bureau exécutant Windows, voir [Configuration PC] [ lnk-mbed-pcconnect] pour configurer l’accès port série sur votre appareil mbed.

### <a name="create-an-mbed-project-and-import-the-sample-code"></a>Créer un projet mbed et importer les exemples de code

1. Dans votre navigateur web, accédez au [site du développeur](https://developer.mbed.org/)mbed.org. Si vous n’avez pas inscrit, vous voyez une option pour créer un compte (c’est gratuit). Dans le cas contraire, ouvrez une session avec vos informations d’identification de compte. Cliquez ensuite sur **compilateur** dans le coin supérieur droit de la page. Cette action vous amène à l’interface de *l’espace de travail* .

2. Vérifiez que la plate-forme matérielle que vous utilisez apparaît dans le coin supérieur droit de la fenêtre, ou cliquez sur l’icône dans le coin droit pour sélectionner votre plateforme.

3. Dans le menu principal, cliquez sur **Importer** . Cliquez sur **Cliquez ici** pour importer à partir de lien d’URL en regard du logo de globe mbed.

    ![][6]

4. Dans la fenêtre contextuelle, entrez le lien pour le https://developer.mbed.org/users/AzureIoTClient/code/remote_monitoring/ code exemple, puis cliquez sur **Importer**.

    ![][7]

5. Vous pouvez voir dans la fenêtre du compilateur mbed que l’importation de ce projet importe également différentes bibliothèques. Certaines sont fournies et gérées par l’équipe Azure IoT ([azureiot_common](https://developer.mbed.org/users/AzureIoTClient/code/azureiot_common/), [iothub_client](https://developer.mbed.org/users/AzureIoTClient/code/iothub_client/), [iothub_amqp_transport](https://developer.mbed.org/users/AzureIoTClient/code/iothub_amqp_transport/), [azure_uamqp](https://developer.mbed.org/users/AzureIoTClient/code/azure_uamqp/)), tandis que d’autres personnes sont disponibles dans le catalogue de bibliothèques mbed des bibliothèques tierces.

    ![][8]

6. Ouvrez le fichier remote_monitoring\remote_monitoring.c et recherchez le code suivant dans le fichier :

    ```
    static const char* deviceId = "[Device Id]";
    static const char* deviceKey = "[Device Key]";
    static const char* hubName = "[IoTHub Name]";
    static const char* hubSuffix = "[IoTHub Suffix, i.e. azure-devices.net]";
    ```

7. Remplacer [Id de périphérique] et [clé périphérique], avec les données de votre appareil pour activer le programme d’exemple pour vous connecter à votre plateforme IoT. Utilisez le nom d’hôte du concentrateur IoT pour remplacer le [nom IoTHub] et les espaces réservés [suffixe IoTHub, c'est-à-dire azure-devices.net]. Par exemple, si votre nom d’hôte du concentrateur IoT est **contoso.azure devices.net**, **contoso** est le **hubName** et tous les éléments une fois la **hubSuffix**:

    ```
    static const char* deviceId = "mydevice";
    static const char* deviceKey = "mykey";
    static const char* hubName = "contoso";
    static const char* hubSuffix = "azure-devices.net";
    ```

    ![][9]

### <a name="walk-through-the-code"></a>Passez en revue le code

Si vous êtes intéressé fonctionnement du programme, cette section décrit certaines parties clés des exemples de code. Si vous voulez juste exécuter le code, passez directement à [créer et exécuter le programme](#buildandrun).

#### <a name="defining-the-model"></a>Définition du modèle

Cet exemple utilise le [sérialiseur] [ lnk-serializer] bibliothèque pour définir un modèle qui spécifie les messages de l’appareil peut envoyer à IoT concentrateur et recevoir IoT concentrateur. Dans cet exemple, l’espace de noms **Contoso** définit un modèle de **Thermostat** qui spécifie les données de télémétrie **température**, **ExternalTemperature**et **l’humidité** ainsi que des métadonnées tels que l’id de périphérique et les commandes de l’appareil répond aux propriétés de l’appareil :

```
BEGIN_NAMESPACE(Contoso);

DECLARE_STRUCT(SystemProperties,
    ascii_char_ptr, DeviceID,
    _Bool, Enabled
);

DECLARE_STRUCT(DeviceProperties,
ascii_char_ptr, DeviceID,
_Bool, HubEnabledState
);

DECLARE_MODEL(Thermostat,

    /* Event data (temperature, external temperature and humidity) */
    WITH_DATA(int, Temperature),
    WITH_DATA(int, ExternalTemperature),
    WITH_DATA(int, Humidity),
    WITH_DATA(ascii_char_ptr, DeviceId),

    /* Device Info - This is command metadata + some extra fields */
    WITH_DATA(ascii_char_ptr, ObjectType),
    WITH_DATA(_Bool, IsSimulatedDevice),
    WITH_DATA(ascii_char_ptr, Version),
    WITH_DATA(DeviceProperties, DeviceProperties),
    WITH_DATA(ascii_char_ptr_no_quotes, Commands),

    /* Commands implemented by the device */
    WITH_ACTION(SetTemperature, int, temperature),
    WITH_ACTION(SetHumidity, int, humidity)
);

END_NAMESPACE(Contoso);
```

Les définitions pour les commandes **SetTemperature** et **SetHumidity** répondant à l’appareil sont associées à la définition du modèle :

```
EXECUTE_COMMAND_RESULT SetTemperature(Thermostat* thermostat, int temperature)
{
    (void)printf("Received temperature %d\r\n", temperature);
    thermostat->Temperature = temperature;
    return EXECUTE_COMMAND_SUCCESS;
}

EXECUTE_COMMAND_RESULT SetHumidity(Thermostat* thermostat, int humidity)
{
    (void)printf("Received humidity %d\r\n", humidity);
    thermostat->Humidity = humidity;
    return EXECUTE_COMMAND_SUCCESS;
}
```

#### <a name="connecting-the-model-to-the-library"></a>Connectez le modèle à la bibliothèque

Les fonctions **sendMessage** et **IoTHubMessage** sont du code réutilisable pour l’envoi de télémétrie de l’appareil et connexion des messages à partir du IoT Hub pour les gestionnaires de commandes.

#### <a name="the-remotemonitoringrun-function"></a>La fonction remote_monitoring_run

Fonction **principale** du programme appelle la fonction **remote_monitoring_run** lorsque l’application commence à exécuter le comportement de l’appareil en tant que concentrateur IoT appareil client. Cette fonction **remote_monitoring_run** se compose principalement de paires imbriquées des fonctions :

- **plateforme\_initialisation** et **plateforme\_deinit** effectuer des opérations d’initialisation et d’arrêt spécifique à la plateforme.
- **sérialiseur\_initialisation** et **sérialiseur\_deinit** initialisation et désélectionnez initialisation la bibliothèque sérialiseur.
- **IoTHubClient\_créer** et **IoTHubClient\_Destroy** créer une poignée de client, **iotHubClientHandle**, utilisant les informations d’identification de l’appareil pour vous connecter à votre plateforme IoT.

Dans la section principale de la fonction **remote_monitoring_run** , le programme effectue les opérations suivantes à l’aide de la poignée de **iotHubClientHandle** :

- Crée une instance du modèle thermostat Contoso et configure les rappels de message pour les deux commandes.
- Envoie des informations sur le périphérique lui-même, y compris les commandes que qui prend en charge, à votre concentrateur IoT à l’aide de la bibliothèque sérialiseur. Lorsque le hub reçoit ce message, il modifie l’état du périphérique dans le tableau de bord **en attente** en cours **d’exécution**.
- Démarre une boucle **tandis que** qui envoie température, les températures externes et les valeurs humidité à concentrateur IoT toutes les secondes.

Pour référence, Voici un exemple de message **DeviceInfo** envoyé à IoT concentrateur au démarrage :

```
{
  "ObjectType":"DeviceInfo",
  "Version":"1.0",
  "IsSimulatedDevice":false,
  "DeviceProperties":
  {
    "DeviceID":"mydevice01", "HubEnabledState":true
  }, 
  "Commands":
  [
    {"Name":"SetHumidity", "Parameters":[{"Name":"humidity","Type":"double"}]},
    { "Name":"SetTemperature", "Parameters":[{"Name":"temperature","Type":"double"}]}
  ]
}
```

Pour référence, Voici un exemple de **télémétrie** message envoyé à IoT concentrateur :

```
{"DeviceId":"mydevice01", "Temperature":50, "Humidity":50, "ExternalTemperature":55}
```

Pour référence, Voici un échantillon reçu de concentrateur IoT la **commande** :

```
{
  "Name":"SetHumidity",
  "MessageId":"2f3d3c75-3b77-4832-80ed-a5bb3e233391",
  "CreatedTime":"2016-03-11T15:09:44.2231295Z",
  "Parameters":{"humidity":23}
}
```

<a id="buildandrun"/>
### <a name="build-and-run-the-program"></a>Créer et exécuter le programme

1. Cliquez sur **compiler** pour générer le programme. Vous pouvez en toute sécurité ignorer les avertissements, mais si la compilation génère des erreurs, les corriger avant de poursuivre.

2. Si la génération réussit, le site Web du compilateur mbed génère un fichier .bin avec le nom de votre projet et téléchargements sur votre ordinateur local. Copiez le fichier .bin à l’appareil. Enregistrez le fichier .bin sur l’appareil provoque le périphérique redémarrer et exécutez le programme contenu dans le fichier .bin. Vous pouvez redémarrer manuellement le programme à tout moment en appuyant sur le bouton de réinitialisation sur le périphérique mbed.

3. Se connecter à l’appareil à l’aide d’une application cliente SSH, tel que PuTTY. Vous pouvez déterminer le port série que votre appareil utilise en vérifiant le Gestionnaire de périphériques Windows.

    ![][11]

4. Dans PuTTY, cliquez sur le type de connexion **en série** . Le périphérique se connecte généralement à 9 600 bauds, alors entrez 9600 dans la zone **vitesse** . Cliquez sur **Ouvrir**.

5. Le programme démarre l’exécution. Vous devrez peut-être réinitialiser la carte (appuyez sur CTRL + Pause ou appuyez sur le bouton Réinitialiser la carte) si le programme ne démarre pas automatiquement lorsque vous vous connectez.

    ![][10]

[AZURE.INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]


[6]: ./media/iot-suite-connecting-devices-mbed/mbed1.png
[7]: ./media/iot-suite-connecting-devices-mbed/mbed2a.png
[8]: ./media/iot-suite-connecting-devices-mbed/mbed3a.png
[9]: ./media/iot-suite-connecting-devices-mbed/suite6.png
[10]: ./media/iot-suite-connecting-devices-mbed/putty.png
[11]: ./media/iot-suite-connecting-devices-mbed/mbed6.png

[lnk-mbed-home]: https://developer.mbed.org/platforms/FRDM-K64F/
[lnk-mbed-getstarted]: https://developer.mbed.org/platforms/FRDM-K64F/#getting-started-with-mbed
[lnk-mbed-pcconnect]: https://developer.mbed.org/platforms/FRDM-K64F/#pc-configuration
[lnk-serializer]: https://azure.microsoft.com/documentation/articles/iot-hub-device-sdk-c-intro/#serializer
