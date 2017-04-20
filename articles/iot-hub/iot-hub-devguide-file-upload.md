<properties
 pageTitle="Guide du développeur - téléchargement de fichier | Microsoft Azure"
 description="Guide du développeur Azure IoT concentrateur - le téléchargement de fichiers à partir d’un appareil à IoT concentrateur"
 services="iot-hub"
 documentationCenter=".net"
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/30/2016" 
 ms.author="dobett"/>

# <a name="upload-files-from-a-device"></a>Télécharger des fichiers à partir d’un appareil

## <a name="overview"></a>Vue d’ensemble

Comme indiqué dans les [points de terminaison IoT concentrateur] [ lnk-endpoints] article, appareils peuvent initier des téléchargements de fichiers en envoyant une notification par un point de terminaison appareil NOT (**/devices/ {ID de périphérique} / fichiers**).  Lorsqu’un appareil avertit IoT Hub d’un téléchargement terminé, concentrateur IoT génère des notifications de téléchargement de fichier que vous pouvez recevoir via un point de terminaison du service NOT (**/messages/servicebound/filenotifications**) sous forme de messages.

Au lieu de messages brokering via concentrateur IoT lui-même, IoT concentrateur sert à la place un expéditeur à un compte de stockage Azure associé. Un périphérique demande un jeton de stockage concentrateur IoT spécifique au fichier de que l’appareil souhaite télécharger. Le périphérique utilise l’URI SAS à télécharger le fichier vers le stockage, et lorsque le téléchargement est terminé l’appareil envoie une notification d’achèvement à IoT concentrateur. Concentrateur IoT vérifie que le fichier a été téléchargé, puis ajoute une notification de téléchargement de fichier pour le nouveau service orienté fichier notification messagerie point de terminaison.

Avant de télécharger un fichier à partir d’un appareil à IoT concentrateur, vous devez configurer votre concentrateur en [associant un stockage Azure] [ lnk-associate-storage] du compte à celui-ci.

Votre appareil peut puis [initialisation un téléchargement] [ lnk-initialize] et puis [Avertir IoT concentrateur] [ lnk-notify] lorsque le transfert est terminé. Vous pouvez également lorsqu’un appareil avertit IoT concentrateur que le téléchargement est terminé, le service peut générer un [message de notification][lnk-service-notification].

### <a name="when-to-use"></a>Quand utiliser

Utiliser cette fonctionnalité concentrateur IoT lorsque vous avez besoin de télécharger un fichier à partir d’un appareil à votre service principale au lieu d’envoyer un message via IoT concentrateur.

## <a name="associate-an-azure-storage-account-with-iot-hub"></a>Associer un compte de stockage Azure IoT concentrateur

Pour utiliser la fonctionnalité de téléchargement de fichiers, vous devez d’abord lier un compte de stockage Azure au concentrateur IoT. Cela, vous pouvez soit via le [portail Azure][lnk-management-portal], ou par le biais du [fournisseur de ressources IoT concentrateur API REST][lnk-resource-provider-apis]. Une fois que vous avez associé un compte de stockage Azure à votre plateforme IoT, le service retourne un URI SAS sur un appareil lorsque l’appareil envoie une demande de téléchargement de fichier.

> [AZURE.NOTE] La [Azure IoT concentrateur SDK] [ lnk-sdks] gérer automatiquement récupération SAS URI, téléchargement du fichier et la notification IoT Hub d’un téléchargement terminé.

## <a name="initialize-a-file-upload"></a>Initialisation chargement d’un fichier

Concentrateur IoT a un point de terminaison spécifiquement pour les périphériques demander un URI SAS pour le stockage de télécharger un fichier. Le périphérique lance le processus de téléchargement de fichier en envoyant un billet au concentrateur IoT en `{iot hub}.azure-devices.net/devices/{deviceId}/files` avec le corps JSON suivant :

```
{
    "blobName": "{name of the file for which a SAS URI will be generated}"
}
```

Concentrateur IoT renvoie les valeurs suivantes, qui utilise le périphérique à télécharger le fichier :

```
{
    "correlationId": "somecorrelationid",
    "hostname": "contoso.azure-devices.net",
    "containerName": "testcontainer",
    "blobName": "test-device1/image.jpg",
    "sasToken": "1234asdfSAStoken"
}
```

### <a name="deprecated-initialize-a-file-upload-with-a-get"></a>Déconseillées : initialisation chargement d’un fichier avec une commande GET

> [AZURE.NOTE] Cette section décrit les fonctionnalités déconseillées pour la réception d’un SAS URI IoT concentrateur. Utilisez la méthode POST décrite ci-dessus.

Concentrateur IoT contient deux points de terminaison REST pour prendre en charge de téléchargement de fichier, afin d’obtenir l’URI SAS pour le stockage et l’autre pour être averti le hub IoT d’un téléchargement terminé. Le périphérique lance le processus de téléchargement de fichier en envoyant une commande GET au concentrateur IoT en `{iot hub}.azure-devices.net/devices/{deviceId}/files/{filename}`. Le hub renvoie un SAS URI spécifique vers le fichier à télécharger et un ID de corrélation à utiliser une fois que le téléchargement est terminé.

## <a name="notify-iot-hub-of-a-completed-file-upload"></a>Avertir IoT Hub de chargement d’un fichier terminé

L’appareil est responsable de téléchargement du fichier de stockage à l’aide de la SDK de stockage Azure. Une fois le téléchargement terminé, le périphérique envoie un billet au concentrateur IoT en `{iot hub}.azure-devices.net/devices/{deviceId}/files/notifications` avec le corps JSON suivant :

```
{
    "correlationId": "{correlation ID received from the initial request}",
    "isSuccess": bool,
    "statusCode": XXX,
    "statusDescription": "Description of status"
}
```

La valeur de `isSuccess` est un représentant booléenne que le fichier a été téléchargé ou non. Le code d’état pour `statusCode` est le statut pour le téléchargement du fichier sur le stockage et la `statusDescription` correspond à la `statusCode`.

## <a name="reference-topics"></a>Rubriques de référence :

Les rubriques de référence vous fournissent plus d’informations sur le téléchargement de fichiers à partir d’un appareil.

## <a name="file-upload-notifications"></a>Notifications de téléchargement de fichier

Lorsqu’un appareil télécharge un fichier et vous avertit concentrateur IoT d’achèvement de téléchargement, le service génère éventuellement un message de notification qui contient l’emplacement de stockage et le nom du fichier.

Comme indiqué dans les [points de terminaison][lnk-endpoints], concentrateur IoT fournit des notifications de téléchargement de fichier par un point de terminaison du service NOT (**/messages/servicebound/fileuploadnotifications**) sous forme de messages. La sémantique de réception pour les notifications de téléchargement de fichier sont les mêmes que pour les messages de cloud vers le périphérique et ont la même [cycle de vie message][lnk-lifecycle]. Chaque message, extraite de l’extrémité de notification de téléchargement de fichier est un enregistrement JSON avec les propriétés suivantes :

| Propriété | Description |
| -------- | ----------- |
| EnqueuedTimeUtc | Horodatage indiquant quand la notification a été créée. |
| ID de périphérique | **ID de périphérique** de l’appareil que vous télécharger le fichier. |
| BlobUri | URI du fichier téléchargé. |
| BlobName | Nom du fichier téléchargé. |
| LastUpdatedTime | Horodatage indiquant que lorsque le fichier a été mis à jour. |
| BlobSizeInBytes | Taille du fichier téléchargé. |

**Exemple**. Il s’agit d’un corps de l’exemple d’un message de notification de téléchargement de fichier.

```
{
    "deviceId":"mydevice",
    "blobUri":"https://{storage account}.blob.core.windows.net/{container name}/mydevice/myfile.jpg",
    "blobName":"mydevice/myfile.jpg",
    "lastUpdatedTime":"2016-06-01T21:22:41+00:00",
    "blobSizeInBytes":1234,
    "enqueuedTimeUtc":"2016-06-01T21:22:43.7996883Z"
}
```

## <a name="file-upload-notification-configuration-options"></a>Options de configuration de notification de téléchargement fichier

Chaque concentrateur IoT expose les options de configuration suivantes pour les notifications de téléchargement de fichier :

| Propriété | Description | Par défaut et plage |
| -------- | ----------- | ----------------- |
| **enableFileUploadNotifications** | Contrôle si les notifications de téléchargement de fichier sont écrits sur le point de terminaison de notifications de fichier. | Bool. Par défaut : True. |
| **fileNotifications.ttlAsIso8601** | Durée de vie par défaut les notifications de téléchargement de fichier. | ISO_8601 des intervalle jusqu'à 48 H (une minute minimale). Par défaut : 1 heure. |
| **fileNotifications.lockDuration** | Durée de verrouillage pour la file de notifications de téléchargement de fichiers. | 5 à 300 secondes (nombre minimal de 5 secondes). Par défaut : 60 secondes. |
| **fileNotifications.maxDeliveryCount** | Nombre maximal de remise pour le fichier télécharger file d’attente de notification. | 1 à 100. Par défaut : 100. |

## <a name="additional-reference-material"></a>Documents de référence supplémentaires

Autres rubriques de référence dans le Guide du développeur sont les suivantes :

- [Points de terminaison IoT concentrateur] [ lnk-endpoints] décrit les différents points de terminaison chaque concentrateur IoT expose pour les opérations de runtime et la gestion.
- [Limitation et les quotas de] [ lnk-quotas] décrit les quotas qui s’appliquent au service IoT concentrateur et le comportement de limitation s’attendre lorsque vous utilisez le service.
- [Concentrateur IoT périphérique et service SDK] [ lnk-sdks] répertorie le langage diverses SDK vous une utilisation lorsque vous développez périphérique et service applications qui interagissent avec IoT concentrateur.
- [Langage de requête pour jumeaux, méthodes et travaux] [ lnk-query] décrit le langage de requête que vous pouvez utiliser pour récupérer des informations à partir du IoT Hub sur votre jumeaux appareil, les méthodes et les tâches.
- [Prise en charge IoT concentrateur MQTT] [ lnk-devguide-mqtt] donne des informations supplémentaires sur la prise en charge IoT concentrateur pour le protocole MQTT.

## <a name="next-steps"></a>Étapes suivantes

À présent que vous avez appris à télécharger des fichiers à partir d’appareils utilisant un concentrateur IoT, pouvant vous intéresser dans les rubriques suivantes Guide du développeur :

- [Gestion des identités appareil dans IoT Hub][lnk-devguide-identities]
- [Contrôler l’accès à IoT concentrateur][lnk-devguide-security]
- [Jumeaux de périphérique permet de synchroniser les configurations et état][lnk-devguide-device-twins]
- [Appeler une méthode directe sur un appareil][lnk-devguide-directmethods]
- [Planifier des tâches sur plusieurs appareils][lnk-devguide-jobs]

Si vous voulez essayer certaines des concepts décrits dans cet article, il est possible que vous intéresser le didacticiel IoT concentrateur suivant :

- [Comment télécharger des fichiers à partir d’appareils dans le cloud avec concentrateur IoT][lnk-fileupload-tutorial]

[lnk-resource-provider-apis]: https://msdn.microsoft.com/library/mt548492.aspx
[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-management-portal]: https://portal.azure.com
[lnk-fileupload-tutorial]: iot-hub-csharp-csharp-file-upload.md
[lnk-associate-storage]: iot-hub-devguide-file-upload.md#associate-an-azure-storage-account-with-iot-hub
[lnk-initialize]: iot-hub-devguide-file-upload.md#initialize-a-file-upload
[lnk-notify]: iot-hub-devguide-file-upload.md#notify-iot-hub-of-a-completed-file-upload
[lnk-service-notification]: iot-hub-devguide-file-upload.md#file-upload-notifications
[lnk-lifecycle]: iot-hub-devguide-messaging.md#message-lifecycle

[lnk-devguide-identities]: iot-hub-devguide-identity-registry.md
[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-devguide-device-twins]: iot-hub-devguide-device-twins.md
[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
