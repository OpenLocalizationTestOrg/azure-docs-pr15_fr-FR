<properties
 pageTitle="APPAREIL informations métadonnées dans la solution d’analyse à distance | Microsoft Azure"
 description="Une description de la IoT Azure préconfiguré contrôle à distance solution et son architecture."
 services=""
 suite="iot-suite"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-suite"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/12/2016"
 ms.author="dobett"/>

# <a name="device-information-metadata-in-the-remote-monitoring-preconfigured-solution"></a>Solution préconfigurée appareil informations métadonnées dans le contrôle à distance

La gamme de IoT Azure surveillance solution préconfigurée à distance illustre une approche pour la gestion des métadonnées de l’appareil. Cet article décrit l’approche que cette solution permet d’accéder à vous permettent de comprendre :

- Les métadonnées d’appareil la solution stocke.
- Comment la solution gère les métadonnées de l’appareil.

## <a name="context"></a>Contexte

La solution préconfigurée contrôle à distance utilise [Azure IoT concentrateur] [ lnk-iot-hub] pour activer vos périphériques envoyer des données dans le cloud. Concentrateur IoT inclut un [Registre d’identité appareil] [ lnk-identity-registry] pour contrôler l’accès à IoT concentrateur. Le Registre IoT concentrateur appareil identité est différente de la solution spécifiques *du Registre du périphérique* qui stocke les métadonnées des informations de périphérique de surveillance à distance. La solution d’analyse à distance utilise un [DocumentDB] [ lnk-docdb] base de données pour implémenter son Registre appareil pour stocker les métadonnées des informations de périphérique. L' [Architecture de référence Microsoft Azure IoT] [ lnk-ref-arch] décrit le rôle du Registre appareil dans une solution IoT classique.

> [AZURE.NOTE] La solution préconfigurée d’analyse à distance conserve le Registre identité appareil synchronisé avec le Registre de périphérique. Les deux utilisent le même id appareil à identifier de façon unique chaque périphérique est connecté à votre concentrateur IoT.

L' [Aperçu de gestion des périphériques IoT concentrateur] [ lnk-dm-preview] ajoute des fonctionnalités à concentrateur IoT qui sont semblables aux fonctionnalités de gestion des informations appareil décrites dans cet article. Pour l’instant, la solution d’analyse à distance utilise uniquement les fonctionnalités de (disponibilité générale) généralement disponibles dans IoT concentrateur.

## <a name="device-information-metadata"></a>APPAREIL informations métadonnées

APPAREIL informations métadonnées JSON document stocké dans la base de données DocumentDB de Registre appareil présente la structure suivante :

```
{
  "DeviceProperties": {
    "DeviceID": "deviceid1",
    "HubEnabledState": null,
    "CreatedTime": "2016-04-25T23:54:01.313802Z",
    "DeviceState": "normal",
    "UpdatedTime": null
    },
  "SystemProperties": {
    "ICCID": null
  },
  "Commands": [],
  "CommandHistory": [],
  "IsSimulatedDevice": false,
  "id": "fe81a81c-bcbc-4970-81f4-7f12f2d8bda8"
}
```

- **DeviceProperties**: le périphérique lui-même écrit ces propriétés et de l’appareil est l’autorité de ces données. D’autres propriétés de l’appareil exemple incluent fabricant, numéro de modèle et numéro de série. 
- **ID de périphérique**: l’id de périphérique unique. Cette valeur est la même dans le Registre IoT concentrateur appareil identité.
- **HubEnabledState**: l’état de l’appareil dans IoT Hub. Cette valeur est définie initialement **null** jusqu'à ce que l’appareil se connecte d’abord. Dans le portail solution, une valeur **null** est représentée par le périphérique en cours « enregistré, mais n’est pas présent. »
- **CreatedTime**: heure de création de l’appareil.
- **DeviceState**: l’état signalées par le périphérique.
- **UpdatedTime**: l’heure de l’appareil a été mis à jour à partir du portail de solution.
- **SystemProperties**: le portail solution écrit les propriétés du système et le périphérique n’a pas connaissance de ces propriétés. Un exemple de propriété système est **ICCID** si la solution n’est autorisée avec et connecté à un service de gestion des périphériques activés par l’Assistant.
- **Commandes**: une liste des commandes prend en charge de l’appareil. Le périphérique fournit ces informations à la solution.
- **CommandHistory**: une liste des commandes envoyées par la solution d’analyse à distance à l’appareil et l’état de ces commandes.
- **IsSimulatedDevice**: un indicateur qui identifie un périphérique comme un périphérique simulé.
- **ID**: DocumentDB identificateur unique pour ce document appareil.

> [AZURE.NOTE] Informations sur les périphériques peuvent également inclure des métadonnées pour décrire la télémétrie que le périphérique envoie à IoT concentrateur. La solution d’analyse à distance utilise ces métadonnées de télémétrie pour personnaliser la façon dont le tableau de bord affiche [télémétrie dynamique][lnk-dynamic-telemetry].

## <a name="lifecycle"></a>Cycle de vie

Lorsque vous créez un périphérique dans le portail de solution, la solution crée une entrée dans son Registre appareil comme indiqué précédemment. La plupart des informations est générée initiale et la **HubEnabledState** a la valeur **null**. À ce stade, la solution crée également une entrée pour le périphérique dans le Registre d’identité appareil qui génère les clés de que l’appareil utilise pour vous authentifier avec IoT concentrateur.

Lorsqu’un appareil se connecte tout d’abord à la solution, il envoie un message d’informations de périphérique. Ce message d’information appareil inclut des propriétés de périphérique tels que le fabricant du périphérique, le numéro de modèle et le numéro de série. Un message d’informations appareil comprend également une liste des commandes de que l’appareil prend en charge, y compris des informations sur les paramètres de la commande. Lorsque la solution reçoit ce message, il met à jour les métadonnées d’informations de périphérique dans le Registre de périphérique.

### <a name="view-and-edit-device-information-in-the-solution-portal"></a>Afficher et modifier des informations sur le périphérique dans le portail de solution

La liste des périphériques dans le portail de solution affiche les propriétés suivantes sous forme de colonnes : **état**, **ID de périphérique**, **le fabricant**, **Numéro de modèle**, **numéro de série**, **microprogramme**, **plateforme**, **processeur**et **RAM installé**. Les propriétés du périphérique **Latitude** et **Longitude** lecteur de l’emplacement dans la carte Bing Map sur le tableau de bord. 

![Liste des périphériques][img-device-list]

Si vous cliquez sur **Modifier** dans le volet de **Détails de l’appareil** dans le portail de solution, vous pouvez modifier les propriétés suivantes. Modifier ces propriétés met à jour l’enregistrement du périphérique dans la base de données DocumentDB. Cependant, si un périphérique envoie un message d’informations sur le périphérique mis à jour, il remplace les modifications apportées dans le portail de solution. Vous ne pouvez pas modifier les **ID de périphérique**, **nom d’hôte**, **HubEnabledState**, **CreatedTime**, **DeviceState**et **UpdatedTime** propriétés dans le portail solution parce que seul le périphérique a autorité sur ces propriétés.

![Modification de l’appareil][img-device-edit]

Vous pouvez utiliser le portail de solution pour supprimer un périphérique de votre solution. Lorsque vous supprimez un périphérique, la solution supprime les métadonnées informations appareil le Registre de périphérique solution et l’entrée de périphérique dans le Registre IoT concentrateur appareil identité. Avant de pouvoir supprimer un appareil, vous devez le désactiver.

![Suppression de l’appareil][img-device-remove]

## <a name="device-information-message-processing"></a>Traitement des messages appareil d’informations

Messages d’information appareil envoyés par un périphérique sont distincts des messages de télémétrie. Messages d’information appareil incluront des informations telles que les propriétés de l’appareil et les commandes à qu'un appareil peut répondre tout l’historique des commandes. IoT Hub lui-même n’a pas connaissance des métadonnées contenues dans un message d’informations appareil et traite le message de la même façon qu’il traite les messages de l’appareil-nuage. Dans la solution d’analyse à distance, un [Azure flux Analytique] [ lnk-stream-analytics] tâche (ASA) lit les messages à partir de IoT concentrateur. Filtres pour les messages qui contiennent des tâches l’analytique flux **DeviceInfo** **« Type d’objet » : « DeviceInfo »** et les transfère à l’instance d’hôte **EventProcessorHost** qui s’exécute dans une tâche web. Logique de l’instance **EventProcessorHost** utilise l’id de périphérique pour rechercher l’enregistrement DocumentDB pour le périphérique spécifique et mettre à jour l’enregistrement. L’enregistrement de Registre appareil propose désormais des informations telles que les propriétés de l’appareil, les commandes et l’historique des commandes.

> [AZURE.NOTE] Un message d’informations appareil est un message appareil-nuage standard. La solution fait la distinction entre les messages d’information appareil et les messages de télémétrie à l’aide de requêtes ASA.

## <a name="example-device-information-records"></a>Exemples d’enregistrements informations appareil

La solution préconfigurée contrôle à distance utilise deux types d’enregistrements d’informations de périphérique : enregistrements pour les appareils simulés déployés avec la solution et les enregistrements pour les appareils personnalisés que vous vous connectez à la solution.

### <a name="simulated-device"></a>Périphérique simulé

L’exemple suivant illustre l’enregistrement d’informations appareil JSON pour un périphérique simulé. Cet enregistrement comporte une valeur définie pour **UpdatedTime**, qui indique le périphérique a envoyé un message **DeviceInfo** à IoT concentrateur. L’enregistrement inclut certaines propriétés courantes de l’appareil, définit les six commandes prennent en charge les appareils simulés et a l’indicateur **IsSimulatedDevice** définie sur **1**.

```
{
  "DeviceProperties": {
    "DeviceID": "SampleDevice001_455",
    "HubEnabledState": true,
    "CreatedTime": "2016-01-26T19:02:01.4550695Z",
    "DeviceState": "normal",
    "UpdatedTime": "2016-06-01T15:28:41.8105157Z",
    "Manufacturer": "Contoso Inc.",
    "ModelNumber": "MD-369",
    "SerialNumber": "SER9009",
    "FirmwareVersion": "1.39",
    "Platform": "Plat-34",
    "Processor": "i3-2191",
    "InstalledRAM": "3 MB",
    "Latitude": 47.583582,
    "Longitude": -122.130622
  },
  "Commands": [
    {
      "Name": "PingDevice",
      "Parameters": null
    },
    {
      "Name": "StartTelemetry",
      "Parameters": null
    },
    {
      "Name": "StopTelemetry",
      "Parameters": null
    },
    {
      "Name": "ChangeSetPointTemp",
      "Parameters": [
        {
          "Name": "SetPointTemp",
          "Type": "double"
        }
      ]
    },
    {
      "Name": "DiagnosticTelemetry",
      "Parameters": [
        {
          "Name": "Active",
          "Type": "boolean"
        }
      ]
    },
    {
      "Name": "ChangeDeviceState",
      "Parameters": [
        {
          "Name": "DeviceState",
          "Type": "string"
        }
      ]
    }
  ],
  "CommandHistory": [],
  "IsSimulatedDevice": 1,
  "Version": "1.0",
  "ObjectType": "DeviceInfo",
  "IoTHub": {
    "MessageId": null,
    "CorrelationId": null,
    "ConnectionDeviceId": "SampleDevice001_455",
    "ConnectionDeviceGenerationId": "635894317219942540",
    "EnqueuedTime": "0001-01-01T00:00:00",
    "StreamId": null
  },
  "SystemProperties": {
    "ICCID": null
  },
  "id": "7101c002-085f-4954-b9aa-7466980a2aaf"
}
```

### <a name="custom-device"></a>Périphérique personnalisé

L’exemple suivant montre l’enregistrement d’informations appareil JSON pour un périphérique personnalisé et comporte l’indicateur **IsSimulatedDevice** défini à **0**. Vous constatez que cet appareil personnalisé prend en charge les deux commandes et que le portail solution a envoyé une commande **SetTemperature** à l’appareil :

```
{
  "DeviceProperties": {
    "DeviceID": "mydevice01",
    "HubEnabledState": true,
    "CreatedTime": "2016-03-28T21:05:06.6061104Z",
    "DeviceState": "normal",
    "UpdatedTime": "2016-06-07T22:05:34.2802549Z"
  },
  "SystemProperties": {
    "ICCID": null
  },
  "Commands": [
    {
      "Name": "SetHumidity",
      "Parameters": [
        {
          "Name": "humidity",
          "Type": "int"
        }
      ]
    },
    {
      "Name": "SetTemperature",
      "Parameters": [
        {
          "Name": "temperature",
          "Type": "int"
        }
      ]
    }
  ],
  "CommandHistory": [
    {
      "Name": "SetTemperature",
      "MessageId": "2a0cec61-5eca-4de7-92dc-9c0bc4211c46",
      "CreatedTime": "2016-06-07T21:05:18.140796Z",
      "Parameters": {
        "temperature": 20
      },
      "UpdatedTime": "2016-06-07T21:05:18.716076Z",
      "Result": "Expired"
    }
  ],
  "IsSimulatedDevice": 0,
  "id": "6184ae0f-2d94-4fbd-91cd-4b193aecc9d1",
  "ObjectType": "DeviceInfo",
  "Version": "1.0",
  "IoTHub": {
    "MessageId": null,
    "CorrelationId": null,
    "ConnectionDeviceId": "SampleCustom",
    "ConnectionDeviceGenerationId": "635947959068246845",
    "EnqueuedTime": "0001-01-01T00:00:00",
    "StreamId": null
  }
}
```

La figure suivante montre le message JSON **DeviceInfo** le périphérique envoyé à mettre à jour les métadonnées d’informations appareil :

```
{ "ObjectType":"DeviceInfo",
  "Version":"1.0",
  "IsSimulatedDevice":false,
  "DeviceProperties": { "DeviceID":"mydevice01", "HubEnabledState":true },
  "Commands": [
    {"Name":"SetHumidity", "Parameters":[{"Name":"humidity","Type":"double"}]},
    {"Name":"SetTemperature", "Parameters":[{"Name":"temperature","Type":"double"}]}
  ]
}
```

## <a name="next-steps"></a>Étapes suivantes

À présent, vous avez terminé d’apprentissage comment vous pouvez personnaliser les solutions préconfigurées, vous pouvez explorer les autres fonctionnalités et des fonctions des solutions Suite IoT préconfigurés :

- [Présentation de la solution maintenance prédictive préconfigurée][lnk-predictive-overview]
- [Forum aux questions IoT gamme][lnk-faq]
- [Sécurité IoT d’emblée][lnk-security-groundup]



<!-- Images and links -->
[img-device-list]: media/iot-suite-remote-monitoring-device-info/image1.png
[img-device-edit]: media/iot-suite-remote-monitoring-device-info/image2.png
[img-device-remove]: media/iot-suite-remote-monitoring-device-info/image3.png

[lnk-iot-hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-identity-registry]: ../iot-hub/iot-hub-devguide-identity-registry.md
[lnk-docdb]: https://azure.microsoft.com/documentation/services/documentdb/
[lnk-ref-arch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
[lnk-stream-analytics]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-dm-preview]: ../iot-hub/iot-hub-device-management-overview.md
[lnk-dynamic-telemetry]: iot-suite-dynamic-telemetry.md

[lnk-predictive-overview]: iot-suite-predictive-overview.md
[lnk-faq]: iot-suite-faq.md
[lnk-security-groundup]: securing-iot-ground-up.md
