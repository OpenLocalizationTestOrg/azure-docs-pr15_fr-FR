<properties
 pageTitle="Contrôle des opérations IoT concentrateur"
 description="Une vue d’ensemble des opérations d’Azure IoT concentrateur surveillance, ce qui vous permet de contrôler le statut d’opérations sur votre plateforme IoT en temps réel"
 services="iot-hub"
 documentationCenter=""
 authors="nberdy"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="08/11/2016"
 ms.author="nberdy"/>

# <a name="introduction-to-operations-monitoring"></a>Introduction au contrôle des opérations

Contrôle des opérations IoT concentrateur vous permet de surveiller l’état d’opérations sur votre plateforme IoT en temps réel. Concentrateur IoT effectue le suivi des événements entre plusieurs catégories d’opérations, et vous pouvez choisir dans l’envoi d’événements à partir d’une ou plusieurs catégories à un point de terminaison de votre plateforme IoT pour le traitement. Vous pouvez surveiller les données des erreurs ou configurer plus complexe traitement basé sur des modèles de données.

Concentrateur IoT surveille cinq catégories d’événements :

- Opérations d’identité unité
- Télémétrie appareil
- Commandes de cloud vers le périphérique
- Connexions
- Téléchargements de fichiers

## <a name="how-to-enable-operations-monitoring"></a>Comment activer le contrôle des opérations

1. Créer un concentrateur IoT. Vous trouverez des instructions sur la création d’un concentrateur IoT dans la [Prise en main] [ lnk-get-started] guide.

2. Ouvrez la carte de votre plateforme IoT. À partir de là, cliquez sur **les opérations de surveillance**.

    ![][1]

3. Sélectionnez les catégories de surveillance que vous souhaitez analyser, puis cliquez sur **Enregistrer**. Les événements sont disponibles pour la lecture du point de terminaison compatible événement concentrateur répertoriés dans **les paramètres de surveillance**. Le point de terminaison IoT concentrateur est appelée `messages/operationsmonitoringevents`.

    ![][2]

## <a name="event-categories-and-how-to-use-them"></a>Catégories d’événements et comment les utiliser

Chaque contrôle catégorie pistes des opérations un autre type d’interaction avec chaque catégorie de surveillance et IoT concentrateur comporte un schéma qui définit la façon dont les événements de cette catégorie sont structurés.

### <a name="device-identity-operations"></a>Opérations d’identité unité

La catégorie appareil identité opérations effectue le suivi des erreurs qui se produisent lorsque vous tentez de créer, mettre à jour ou supprimer une entrée identité Registre de votre plateforme IoT. Suivi de cette catégorie est utile pour les scénarios de mise en service.

    {
        "time": "UTC timestamp",
         "operationName": "create",
         "category": "DeviceIdentityOperations",
         "level": "Error",
         "statusCode": 4XX,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID",
         "durationMs": 1234,
         "userAgent": "userAgent",
         "sharedAccessPolicy": "accessPolicy"
    }

### <a name="device-telemetry"></a>Télémétrie appareil

La catégorie de télémétrie périphérique effectue le suivi des erreurs qui se produisent au niveau du concentrateur IoT et liés dans le pipeline de télémétrie. Cette catégorie comprend les erreurs qui se produisent lors de l’envoi des événements de télémétrie (par exemple, la limitation) et recevoir des événements de télémétrie (par exemple, lecteur non autorisé). Notez que cette catégorie ne peut pas détecter les erreurs provoquées par code en cours d’exécution sur le périphérique lui-même.

    {
         "messageSizeInBytes": 1234,
         "batching": 0,
         "protocol": "Amqp",
         "authType": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
         "time": "UTC timestamp",
         "operationName": "ingress",
         "category": "DeviceTelemetry",
         "level": "Error",
         "statusCode": 4XX,
         "statusType": 4XX001,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID",
         "EventProcessedUtcTime": "UTC timestamp",
         "PartitionId": 1,
         "EventEnqueuedUtcTime": "UTC timestamp"
    }

### <a name="cloud-to-device-commands"></a>Commandes de cloud vers le périphérique

La catégorie commandes cloud vers le périphérique effectue le suivi des erreurs qui se produisent au niveau du concentrateur IoT et liés dans le pipeline de commande appareil. Cette catégorie inclut les erreurs qui se produisent lors de l’envoi de commandes (par exemple, expéditeur non autorisé), la réception des commandes (par exemple, nombre de remise dépassé) et recevoir des commentaires de commande (tels que les commentaires expiré). Cette catégorie intercepter pas les erreurs d’un appareil qui gère incorrectement une commande si la commande a été correctement remise.

    {
         "messageSizeInBytes": 1234,
         "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
         "deliveryAcknowledgement": 0,
         "protocol": "Amqp",
         "time": " UTC timestamp",
         "operationName": "ingress",
         "category": "C2DCommands",
         "level": "Error",
         "statusCode": 4XX,
         "statusType": 4XX001,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID",
         "EventProcessedUtcTime": "UTC timestamp",
         "PartitionId": 1,
         "EventEnqueuedUtcTime": “UTC timestamp"
    }

### <a name="connections"></a>Connexions

La catégorie de connexions effectue le suivi des erreurs qui se produisent lorsque appareils connecter ou déconnecter à partir d’un concentrateur IoT. Suivi de cette catégorie est utile pour identifier les tentatives de connexion non autorisée et pour le suivi lorsqu’une connexion est perdue pour appareils mobiles dans les zones de connectivité médiocre.

    {
         "durationMs": 1234,
         "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
         "protocol": "Amqp",
         "time": " UTC timestamp",
         "operationName": "deviceConnect",
         "category": "Connections",
         "level": "Error",
         "statusCode": 4XX,
         "statusType": 4XX001,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID"
    }

### <a name="file-uploads"></a>Téléchargements de fichiers

La catégorie de téléchargement de fichier effectue le suivi des erreurs qui se produisent au niveau du concentrateur IoT et sont liées à la fonctionnalité de téléchargement de fichier. Cette catégorie comprend des erreurs se produisent avec SAS URI (par exemple, lorsqu’il arrive à expiration avant un appareil vous avertit le hub d’un téléchargement terminé), chargements échecs signalées par le périphérique et qu’un fichier se trouve pas dans le stockage lors de la création de message de notification IoT concentrateur. Notez que cette catégorie ne peut pas détecter les erreurs qui se produisent directement tandis que l’appareil est téléchargement d’un fichier vers le stockage.

    {
         "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
         "protocol": "HTTP",
         "time": " UTC timestamp",
         "operationName": "ingress",
         "category": "fileUpload",
         "level": "Error",
         "statusCode": 4XX,
         "statusType": 4XX001,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID",
         "blobUri": "http//bloburi.com",
         "durationMs": 1234
    }

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir davantage les fonctionnalités du concentrateur IoT, voir :

- [Guide du développeur][lnk-devguide]
- [Simulation d’un appareil avec le Kit de développement de passerelle IoT][lnk-gateway]

<!-- Links and images -->
[1]: media/iot-hub-operations-monitoring/enable-OM-1.png
[2]: media/iot-hub-operations-monitoring/enable-OM-2.png

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-diagnostic-metrics]: iot-hub-metrics.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-dr]: iot-hub-ha-dr.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md