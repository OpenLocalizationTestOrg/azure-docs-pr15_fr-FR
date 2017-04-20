<properties
   pageTitle="Connecter un périphérique utilisant C sur Linux | Microsoft Azure"
   description="Décrit comment connecter un appareil à la Suite de IoT Azure préconfiguré à distance solution d’analyse à l’aide d’une application écrite en langage C s’exécutant sur Linux."
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


# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-linux"></a>Connectez votre appareil à la solution préconfigurée contrôle à distance (Linux)

[AZURE.INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## <a name="build-and-run-a-sample-c-client-linux"></a>Créer et exécuter un exemple C client Linux

Les procédures suivantes vous montrent comment créer une application cliente, écrit en C et intégré et exécuter sur Ubuntu Linux, qui communique avec la solution préconfigurée d’analyse à distance. Pour effectuer cette procédure, vous avez besoin d’un périphérique en cours d’exécution Ubuntu version 15.04 ou 15.10. Avant de commencer, installez les packages prérequises sur votre appareil Ubuntu à l’aide de la commande suivante :

```
sudo apt-get install cmake gcc g++
```

## <a name="install-the-client-libraries-on-your-device"></a>Installer les bibliothèques du client sur votre appareil

Les bibliothèques de client Azure IoT concentrateur sont disponibles en tant que package que vous pouvez l’installer sur votre appareil Ubuntu à l’aide de la commande **get susceptibles** . Effectuez les étapes suivantes pour installer le package qui contient les IoT concentrateur client des fichiers de bibliothèque et l’en-tête sur votre ordinateur Ubuntu :

1. Ajouter le référentiel AzureIoT sur l’ordinateur :

    ```
    sudo add-apt-repository ppa:aziotsdklinux/ppa-azureiot
    sudo apt-get update
    ```

2. Installer le package de développement du c sdk azure iot

    ```
    sudo apt-get install -y azure-iot-sdk-c-dev
    ```

## <a name="add-code-to-specify-the-behavior-of-the-device"></a>Ajouter du code pour spécifier le comportement de l’appareil

Sur votre ordinateur Ubuntu, créez un dossier nommé **distant\_surveillance**. Dans la **distant\_surveillance** dossier créer les quatre fichiers **main.c**, **distant\_monitoring.c**, **distant\_monitoring.h**et **CMakeLists.txt**.

Les bibliothèques du client sérialiseur IoT concentrateur utilisent un modèle pour spécifier le format des messages qu'envoie le périphérique à IoT concentrateur et les commandes qu’il reçoit des IoT concentrateur.

1. Dans un éditeur de texte, ouvrez le **distant\_monitoring.c** fichier. Ajoutez le code suivant `#include` instructions :

    ```
    #include "iothubtransportamqp.h"
    #include "schemalib.h"
    #include "iothub_client.h"
    #include "serializer.h"
    #include "schemaserializer.h"
    #include "azure_c_shared_utility/threadapi.h"
    #include "azure_c_shared_utility/platform.h"
    ```

2. Ajoutez les déclarations de variable suivantes après la `#include` instructions. Remplacer les valeurs d’espace réservé [Id de périphérique] et [clé périphérique] avec les valeurs de votre appareil à partir du tableau de bord solution contrôle à distance. Utilisez le nom d’hôte IoT concentrateur du tableau de bord pour remplacer [nom IoTHub]. Par exemple, si votre nom d’hôte du concentrateur IoT est **contoso.azure devices.net**, remplacez [nom IoTHub] avec **contoso**:

    ```
    static const char* deviceId = "[Device Id]";
    static const char* deviceKey = "[Device Key]";
    static const char* hubName = "IoTHub Name]";
    static const char* hubSuffix = "azure-devices.net";
    ```

3. Ajoutez le code suivant pour définir le modèle qui permet l’appareil communiquer avec IoT concentrateur. Ce modèle indique que l’appareil envoie température, la température externe, humidité et un id de périphérique sous forme de télémétrie. Le périphérique envoie également les métadonnées relatives à l’appareil à IoT Hub, ainsi qu’une liste des commandes qui prend en charge de l’appareil. Cet appareil répond aux commandes **SetTemperature** et **SetHumidity**:

    ```
    // Define the Model
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

### <a name="add-code-to-implement-the-behavior-of-the-device"></a>Ajouter du code pour implémenter le comportement de l’appareil

Ajoutez les fonctions à exécuter lorsque le périphérique reçoit une commande dans le hub et le code à envoyer de télémétrie simulé au concentrateur.

1. Ajoutez les fonctions suivantes qui s’exécutent lorsque le périphérique reçoit les commandes **SetTemperature** et **SetHumidity** définies dans le modèle :

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

2. Ajoutez la fonction suivante qui envoie un message à IoT concentrateur :

    ```
    static void sendMessage(IOTHUB_CLIENT_HANDLE iotHubClientHandle, const unsigned char* buffer, size_t size)
    {
      IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromByteArray(buffer, size);
      if (messageHandle == NULL)
      {
        printf("unable to create a new IoTHubMessage\r\n");
      }
      else
      {
        if (IoTHubClient_SendEventAsync(iotHubClientHandle, messageHandle, NULL, NULL) != IOTHUB_CLIENT_OK)
        {
          printf("failed to hand over the message to IoTHubClient");
        }
        else
        {
          printf("IoTHubClient accepted the message for delivery\r\n");
        }

        IoTHubMessage_Destroy(messageHandle);
      }
    free((void*)buffer);
    }
    ```

3. Ajoutez la fonction suivante qui connecte la bibliothèque sérialisation dans le Kit de développement :

    ```
    static IOTHUBMESSAGE_DISPOSITION_RESULT IoTHubMessage(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
    {
      IOTHUBMESSAGE_DISPOSITION_RESULT result;
      const unsigned char* buffer;
      size_t size;
      if (IoTHubMessage_GetByteArray(message, &buffer, &size) != IOTHUB_MESSAGE_OK)
      {
        printf("unable to IoTHubMessage_GetByteArray\r\n");
        result = EXECUTE_COMMAND_ERROR;
      }
      else
      {
        /*buffer is not zero terminated*/
        char* temp = malloc(size + 1);
        if (temp == NULL)
        {
          printf("failed to malloc\r\n");
          result = EXECUTE_COMMAND_ERROR;
        }
        else
        {
          memcpy(temp, buffer, size);
          temp[size] = '\0';
          EXECUTE_COMMAND_RESULT executeCommandResult = EXECUTE_COMMAND(userContextCallback, temp);
          result =
            (executeCommandResult == EXECUTE_COMMAND_ERROR) ? IOTHUBMESSAGE_ABANDONED :
            (executeCommandResult == EXECUTE_COMMAND_SUCCESS) ? IOTHUBMESSAGE_ACCEPTED :
            IOTHUBMESSAGE_REJECTED;
          free(temp);
        }
      }
      return result;
    }
    ```

4. Ajoutez la fonction suivante pour vous connecter à IoT concentrateur, envoyer et recevoir des messages et se déconnecter à partir du hub. Notez la façon dont le périphérique envoie des métadonnées relatives à elle-même, y compris les commandes que qui prend en charge, à concentrateur IoT lorsqu’il se connecte. Ces métadonnées permet à la solution mettre à jour l’état du périphérique **en cours d’exécution** sur le tableau de bord :

    ```
    void remote_monitoring_run(void)
    {
      if (platform_init() != 0)
      {
        printf("Failed to initialize the platform.\r\n");
      }
      else
      {
        if (serializer_init(NULL) != SERIALIZER_OK)
        {
          printf("Failed on serializer_init\r\n");
        }
        else
        {
          IOTHUB_CLIENT_CONFIG config;
          IOTHUB_CLIENT_HANDLE iotHubClientHandle;

          config.deviceId = deviceId;
          config.deviceKey = deviceKey;
          config.deviceSasToken = NULL;
          config.iotHubName = hubName;
          config.iotHubSuffix = hubSuffix;
          config.protocol = AMQP_Protocol;
          iotHubClientHandle = IoTHubClient_Create(&config);
          if (iotHubClientHandle == NULL)
          {
            (void)printf("Failed on IoTHubClient_CreateFromConnectionString\r\n");
          }
          else
          {
            Thermostat* thermostat = CREATE_MODEL_INSTANCE(Contoso, Thermostat);
            if (thermostat == NULL)
            {
              (void)printf("Failed on CREATE_MODEL_INSTANCE\r\n");
            }
            else
            {
              STRING_HANDLE commandsMetadata;

              if (IoTHubClient_SetMessageCallback(iotHubClientHandle, IoTHubMessage, thermostat) != IOTHUB_CLIENT_OK)
              {
                printf("unable to IoTHubClient_SetMessageCallback\r\n");
              }
              else
              {

                /* send the device info upon startup so that the cloud app knows
                   what commands are available and the fact that the device is up */
                thermostat->ObjectType = "DeviceInfo";
                thermostat->IsSimulatedDevice = false;
                thermostat->Version = "1.0";
                thermostat->DeviceProperties.HubEnabledState = true;
                thermostat->DeviceProperties.DeviceID = (char*)deviceId;

                commandsMetadata = STRING_new();
                if (commandsMetadata == NULL)
                {
                  (void)printf("Failed on creating string for commands metadata\r\n");
                }
                else
                {
                  /* Serialize the commands metadata as a JSON string before sending */
                  if (SchemaSerializer_SerializeCommandMetadata(GET_MODEL_HANDLE(Contoso, Thermostat), commandsMetadata) != SCHEMA_SERIALIZER_OK)
                  {
                    (void)printf("Failed serializing commands metadata\r\n");
                  }
                  else
                  {
                    unsigned char* buffer;
                    size_t bufferSize;
                    thermostat->Commands = (char*)STRING_c_str(commandsMetadata);

                    /* Here is the actual send of the Device Info */
                    if (SERIALIZE(&buffer, &bufferSize, thermostat->ObjectType, thermostat->Version, thermostat->IsSimulatedDevice, thermostat->DeviceProperties, thermostat->Commands) != IOT_AGENT_OK)
                    {
                      (void)printf("Failed serializing\r\n");
                    }
                    else
                    {
                      sendMessage(iotHubClientHandle, buffer, bufferSize);
                    }

                  }

                  STRING_delete(commandsMetadata);
                }

                thermostat->Temperature = 50;
                thermostat->ExternalTemperature = 55;
                thermostat->Humidity = 50;
                thermostat->DeviceId = (char*)deviceId;

                while (1)
                {
                  unsigned char*buffer;
                  size_t bufferSize;

                  (void)printf("Sending sensor value Temperature = %d, Humidity = %d\r\n", thermostat->Temperature, thermostat->Humidity);

                  if (SERIALIZE(&buffer, &bufferSize, thermostat->DeviceId, thermostat->Temperature, thermostat->Humidity, thermostat->ExternalTemperature) != IOT_AGENT_OK)
                  {
                    (void)printf("Failed sending sensor value\r\n");
                  }
                  else
                  {
                    sendMessage(iotHubClientHandle, buffer, bufferSize);
                  }

                  ThreadAPI_Sleep(1000);
                }
              }

              DESTROY_MODEL_INSTANCE(thermostat);
            }
            IoTHubClient_Destroy(iotHubClientHandle);
          }
          serializer_deinit();
        }
        platform_deinit();
      }
    }
    ```
    
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

### <a name="add-code-to-invoke-the-remotemonitoringrun-function"></a>Ajouter du code pour appeler la fonction remote_monitoring_run

Dans un éditeur de texte, ouvrez le fichier **remote_monitoring.h** . Ajoutez le code suivant :

```
void remote_monitoring_run(void);
```

Dans un éditeur de texte, ouvrez le fichier **main.c** . Ajoutez le code suivant :

```
#include "remote_monitoring.h"

int main(void)
{
    remote_monitoring_run();

    return 0;
}
```

## <a name="use-cmake-to-build-the-client-application"></a>CMake permet de créer l’application cliente

Les étapes suivantes décrivent comment utiliser *CMake* pour créer votre application cliente.

1. Dans un éditeur de texte, ouvrez le fichier **CMakeLists.txt** dans le dossier **remote_monitoring** .

2. Ajoutez les instructions suivantes pour définir la méthode créer votre application cliente :

    ```
    cmake_minimum_required(VERSION 2.8.11)

    set(AZUREIOT_INC_FOLDER ".." "/usr/include/azureiot")

    include_directories(${AZUREIOT_INC_FOLDER})

    set(sample_application_c_files
        ./remote_monitoring.c
        ./main.c
    )

    set(sample_application_h_files
        ./remote_monitoring.h
    )

    add_executable(sample_app ${sample_application_c_files} ${sample_application_h_files})

    target_link_libraries(sample_app
        serializer
        iothub_client
        iothub_client_amqp_transport
        aziotsharedutil
        uamqp
        pthread
        curl
        ssl
        crypto
    )
    ```

3. Dans le dossier **remote_monitoring** , créez un dossier pour stocker les fichiers *rendre* génère CMake, puis exécutez les commandes **cmake** et **Vérifiez** comme suit :

    ```
    mkdir cmake
    cd cmake
    cmake ../
    make
    ```

4. Exécuter l’application cliente et envoyer de télémétrie à IoT concentrateur :

    ```
    ./sample_app
    ```

[AZURE.INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]

