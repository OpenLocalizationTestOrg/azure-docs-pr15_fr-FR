<properties
 pageTitle="Guide du développeur - tâches | Microsoft Azure"
 description="Guide du développeur IoT concentrateur Azure - planification des tâches à exécuter sur plusieurs appareils connectés à votre concentrateur"
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

# <a name="schedule-jobs-on-multiple-devices-preview"></a>Planifier des tâches sur plusieurs appareils (preview)

## <a name="overview"></a>Vue d’ensemble

Comme décrit à articles précédents, Azure IoT Hub autorise un certain nombre de blocs de construction ([balises et des propriétés de l’appareil double] [ lnk-twin-devguide] et les [méthodes de cloud vers le périphérique][lnk-dev-methods]).  En règle générale, les applications de serveur principal IoT permettent appareil administrateurs et opérateurs de mettre à jour et interagir avec les périphériques IoT en bloc et à une heure précise.  Tâches encapsulent l’exécution de mises à jour de périphériques double et les méthodes de C2D par rapport à un ensemble de périphériques à la fois Échéancier.  Par exemple, un opérateur utiliseriez une application serveur principal qui serait initier et effectuer le suivi d’une tâche pour un ensemble de périphériques dans la création de 43 et plancher 3 à la fois qui serait impossible d’interruption de service pour les opérations de la création de redémarrer.

### <a name="when-to-use"></a>Quand utiliser

Prendre en compte à l’aide de tâches lorsque : en solution principale doit planifier et suivre la progression toutes les activités suivantes sur un ensemble de l’appareil :

- Mettre à jour les propriétés du périphérique double vous le souhaitez
- Mettre à jour les balises double pour appareil mobile
- Appeler des méthodes C2D

## <a name="job-lifecycle"></a>Cycle de vie de projet

Tâches sont initiées par le serveur principal de solution et gérées par IoT concentrateur.  Vous pouvez lancer une tâche via un URI service NOT (`{iot hub}/jobs/v2/{device id}/methods/<jobID>?api-version=2016-09-30-preview`) et pour rechercher l’avancement sur une tâche en cours d’exécution via un URI service NOT (`{iot hub}/jobs/v2/<jobId>?api-version=2016-09-30-preview`).  Une fois qu’une tâche est lancée, l’interrogation de travaux activer l’application serveur principal actualiser l’état de l’exécution de tâches.

> [AZURE.NOTE] Lorsque vous lancez un travail, les noms des propriétés et des valeurs peuvent contenir uniquement US-ASCII imprimable alphanumériques, à l’exception dans le jeu suivant : ``{'$', '(', ')', '<', '>', '@', ',', ';', ':', '\', '"', '/', '[', ']', '?', '=', '{', '}', SP, HT}``.

## <a name="reference-topics"></a>Rubriques de référence :

Les rubriques de référence vous fournissent plus d’informations sur l’utilisation des tâches.

## <a name="jobs-to-execute-direct-methods"></a>Tâches à exécuter méthodes directes

Voici les détails de demande HTTP 1.1 pour l’exécution d’une [méthode directe] [ lnk-dev-methods] sur un ensemble de périphériques à l’aide d’une tâche :

    ```
    PUT /jobs/v2/<jobId>?api-version=2016-09-30-preview
    
    Authorization: <config.sharedAccessSignature>
    Content-Type: application/json; charset=utf-8
    Request-Id: <guid>
    User-Agent: <sdk-name>/<sdk-version>

    {
        jobId: '<jobId>',
        type: 'scheduleDirectRequest', 
        cloudToDeviceMethod: {
            methodName: '<methodName>',
            payload: <payload>,                 
            timeoutInSeconds: methodTimeoutInSeconds 
        },
        queryCondition: '<queryOrDevices>', // if the queryOrDevices parameter is a string
        deviceIds: '<queryOrDevices>',      // if the queryOrDevices parameter is an array
        startTime: <jobStartTime>,          // as an ISO-8601 date string
        maxExecutionTimeInSeconds: <maxExecutionTimeInSeconds>        
    }
    ```
    
## <a name="jobs-to-update-device-twin-properties"></a>Tâches pour mettre à jour les propriétés du périphérique double

Voici les détails de demande HTTP 1.1 pour mettre à jour les propriétés du périphérique double à l’aide d’une tâche :

    ```
    PUT /jobs/v2/<jobId>?api-version=2016-09-30-preview
    Authorization: <config.sharedAccessSignature>
    Content-Type: application/json; charset=utf-8
    Request-Id: <guid>
    User-Agent: <sdk-name>/<sdk-version>

    {
        jobId: '<jobId>',
        type: 'scheduleTwinUpdate', 
        updateTwin: <patch>                 // Valid JSON object
        queryCondition: '<queryOrDevices>', // if the queryOrDevices parameter is a string
        deviceIds: '<queryOrDevices>',      // if the queryOrDevices parameter is an array
        startTime: <jobStartTime>,          // as an ISO-8601 date string
        maxExecutionTimeInSeconds: <maxExecutionTimeInSeconds>        // format TBD
    }
    ```

## <a name="querying-for-progress-on-jobs"></a>Interrogation de l’avancement des tâches

Voici les détails de demande HTTP 1.1 pour [interroger pour les travaux][lnk-query]:

    ```
    GET /jobs/v2/query?api-version=2016-09-30-preview[&jobType=<jobType>][&jobStatus=<jobStatus>][&pageSize=<pageSize>][&continuationToken=<continuationToken>]
    
    Authorization: <config.sharedAccessSignature>
    Content-Type: application/json; charset=utf-8
    Request-Id: <guid>
    User-Agent: <sdk-name>/<sdk-version>
    ```
    
Le continuationToken est fournie à partir de la réponse.  

## <a name="jobs-properties"></a>Propriétés des tâches

Voici une liste des propriétés et les descriptions correspondantes, qui peuvent être utilisées lors de la recherche pour les tâches ou les résultats de la tâche.

| Propriété | Description |
| -------------- | -----------------|
| **ID de travail** | Application fournie ID de la tâche. |
| **heure de début** | Application fournie heure de début (ISO8601) pour la tâche. |
| **l’heure de fin** | Concentrateur IoT fourni date (ISO8601) pour lorsque la tâche est terminée. Valide uniquement une fois que le travail atteint l’état « terminé ». | 
| **type** | Types de tâches : |
| | **scheduledUpdateTwin**: un travail utilisé pour mettre à jour un ensemble de propriétés double souhaitée ou indicateurs. |
| | **scheduledDeviceMethod**: un travail utilisé pour appeler une méthode de l’appareil sur un ensemble de double. |
| **état** | État actuel de la tâche. Valeurs possibles pour l’état : |
| | **en attente** : planifié et en attente de traitement par le service de travail. |
| | **planifiée** : prévue pour une date ultérieure. |
| | **en cours d’exécution** : projet actif. |
| | **annulé** : tâche a été annulée. |
| | **Échec** : échouée de la tâche. |
| | **terminé** : fin de la tâche. |
| **deviceJobStatistics** | Statistiques de l’exécution de la tâche. |

Lors de la visualisation, l’objet deviceJobStatistics est disponible uniquement après que la tâche est terminée.

| Propriété | Description |
| -------------- | -----------------|
| **deviceJobStatistics.deviceCount** | Nombre d’unités de la tâche. |
| **deviceJobStatistics.failedCount** | Nombre de périphériques dans laquelle la tâche a échoué. |
| **deviceJobStatistics.succeededCount** | Nombre de périphériques dans laquelle le travail a réussi. |
| **deviceJobStatistics.runningCount** | Nombre d’unités qui sont en cours d’exécution du travail. |
| **deviceJobStatistics.pendingCount** | Nombre d’unités qui sont en attente pour exécuter la tâche. |


### <a name="additional-reference-material"></a>Documents de référence supplémentaires

Autres rubriques de référence dans le Guide du développeur sont les suivantes :

- [Points de terminaison IoT concentrateur] [ lnk-endpoints] décrit les différents points de terminaison chaque concentrateur IoT expose pour les opérations de runtime et la gestion.
- [Limitation et les quotas de] [ lnk-quotas] décrit les quotas qui s’appliquent au service IoT concentrateur et le comportement de limitation s’attendre lorsque vous utilisez le service.
- [Concentrateur IoT périphérique et service SDK] [ lnk-sdks] répertorie le langage diverses SDK vous une utilisation lorsque vous développez périphérique et service applications qui interagissent avec IoT concentrateur.
- [Langage de requête pour jumeaux, méthodes et travaux] [ lnk-query] décrit le langage de requête que vous pouvez utiliser pour récupérer des informations à partir du IoT Hub sur votre jumeaux appareil, les méthodes et les tâches.
- [Prise en charge IoT concentrateur MQTT] [ lnk-devguide-mqtt] donne des informations supplémentaires sur la prise en charge IoT concentrateur pour le protocole MQTT.

## <a name="next-steps"></a>Étapes suivantes

Si vous voulez essayer certaines des concepts décrits dans cet article, il est possible que vous intéresser le didacticiel IoT concentrateur suivant :

- [Tâches de planification et de diffusion][lnk-jobs-tutorial]

<!-- links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-jobs-tutorial]: iot-hub-schedule-jobs.md
[lnk-c2d-methods]: iot-hub-c2d-methods.md
[lnk-dev-methods]: iot-hub-devguide-direct-methods.md
[lnk-get-started-twin]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-devguide]: iot-hub-devguide-device-twins.md
