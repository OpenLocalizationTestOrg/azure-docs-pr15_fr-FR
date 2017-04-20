<properties
    pageTitle="APPAREIL IoT Azure SDK pour C - IoTHubClient | Microsoft Azure"
    description="Pour plus d’informations sur l’utilisation de la bibliothèque IoTHubClient dans l’appareil IoT Azure SDK pour C"
    services="iot-hub"
    documentationCenter=""
    authors="olivierbloch"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="cpp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/06/2016"
     ms.author="obloch"/>

# <a name="microsoft-azure-iot-device-sdk-for-c--more-about-iothubclient"></a>Microsoft Azure IoT appareil SDK pour C – en savoir plus sur IoTHubClient

[premier article](iot-hub-device-sdk-c-intro.md) de cette série ainsi que l' **appareil Microsoft Azure IoT SDK pour C**. Cet article explique qu’il existe deux couches d’architecture dans SDK. À la base est la bibliothèque **IoTHubClient** qui gère directement la communication avec IoT concentrateur. Il existe également la bibliothèque **sérialiseur** qui crée de pour fournir des services de sérialisation. Dans cet article, nous allons fournir des détails supplémentaires sur la bibliothèque **IoTHubClient** .

L’article précédent décrit comment utiliser la bibliothèque **IoTHubClient** pour envoyer des événements à IoT concentrateur et recevoir des messages. Cet article étend cette discussion en expliquant comment gérer plus précisément *lorsque* vous envoyez et recevez des données, présentation de l' **API de niveau inférieur**. Nous vous expliquerons également comment joindre des propriétés aux événements (et de les récupérer à partir des messages) à l’aide de la propriété gestion des fonctionnalités de la bibliothèque **IoTHubClient** . Pour finir, nous allons fournir explication supplémentaire de différentes manières de traiter les messages reçus à partir du IoT Hub.

Cet article se termine par détaillé quelques rubriques divers, y compris le plus sur les informations d’identification de l’appareil et comment modifier le comportement de **IoTHubClient** par le biais des options de configuration.

Nous allons utiliser les exemples **IoTHubClient** pour expliquer les rubriques suivantes. Si vous voulez suivre, voir la **iothub\_client\_exemple\_http** et **iothub\_client\_exemple\_amqp** applications qui sont inclus dans le périphérique IoT Azure SDK pour c tout décrites dans les sections suivantes est présenté dans ces exemples.

Vous pouvez trouver le **périphérique IoT Azure SDK pour C** dans le référentiel de [Microsoft Azure IoT SDK](https://github.com/Azure/azure-iot-sdks) GitHub et afficher les détails de l’API dans la [référence de l’API C](http://azure.github.io/azure-iot-sdks/c/api_reference/index.html).

## <a name="the-lower-level-apis"></a>Les API de niveau inférieur

L’article précédent décrit le fonctionnement de base **IotHubClient** dans le cadre de la **iothub\_client\_exemple\_amqp** application. Par exemple, il expliqué l’initialisation de la bibliothèque à l’aide de ce code.

```
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

Il décrit comment envoyer des événements à l’aide de cet appel de fonction.

```
IoTHubClient_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message);
```

L’article décrit également comment faire pour recevoir des messages en enregistrant une fonction de rappel.

```
int receiveContext = 0;
IoTHubClient_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext);
```

L’article a également montré comment libérer des ressources à l’aide de code comme suit.

```
IoTHubClient_Destroy(iotHubClientHandle);
```

Il existe cependant des fonctions le Guide pour chacune de ces API :

-   IoTHubClient\_Oudre\_CreateFromConnectionString

-   IoTHubClient\_Oudre\_SendEventAsync

-   IoTHubClient\_Oudre\_SetMessageCallback

-   IoTHubClient\_Oudre\_Destroy


Ces fonctions toutes les incluent « Oudre » dans le nom de l’API. Différent de celui, les paramètres de chacune de ces fonctions sont identiques à leurs équivalents non Oudre. Toutefois, le comportement de ces fonctions est différent sur un plan important.

Lorsque vous appelez **IoTHubClient\_CreateFromConnectionString**, les bibliothèques sous-jacentes créent un nouveau thread qui s’exécute en arrière-plan. Ce thread envoie des événements et reçoit des messages d’IoT concentrateur. Aucun thread n’est créé lorsque vous travaillez avec l’API « Oudre ». La création de la thread d’arrière-plan est une commodité pour le développeur. Vous n’avez pas à vous soucier explicitement envoyer des événements et recevoir des messages à partir du IoT Hub--il s’effectue automatiquement en arrière-plan. En revanche, « Oudre « API qui vous permettre explicite contrôler la communication avec IoT Hub, si vous en avez besoin.

Pour mieux comprendre ce point, examinons un exemple :

Lorsque vous appelez **IoTHubClient\_SendEventAsync**, ce que vous faites réellement sont plaçant l’événement dans une mémoire tampon. Le thread d’arrière-plan créé quand vous appelez **IoTHubClient\_CreateFromConnectionString** en permanence surveille ce tampon et envoie des données qu’il contient à IoT concentrateur. Cela se produit en arrière-plan en même temps que le thread principal effectue autres activités.

De même, lorsque vous enregistrez une fonction de rappel pour les messages à l’aide de **IoTHubClient\_SetMessageCallback**, vous êtes demandant le Kit de développement pour que le thread d’arrière-plan appelle la fonction de rappel lorsqu’un message est reçu, indépendamment du thread principal.

API « Oudre » ne créez pas un thread d’arrière-plan. En revanche, une nouvelle API doit être appelée pour explicitement envoyer et recevoir des données à partir de IoT concentrateur. Ceci est illustré dans l’exemple suivant.

La **iothub\_client\_exemple\_http** application qui est incluse dans le Kit de développement montre les API de niveau inférieur. Dans cet exemple, nous envoyer des événements à IoT concentrateur avec un code comme suit :

```
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Message_%d_From_IoTHubClient_LL_Over_HTTP", i);
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText));

IoTHubClient_LL_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message)
```

Les trois premières lignes créent le message, et la dernière ligne envoie l’événement. Toutefois, comme mentionné précédemment, « envoi » l’événement indique que les données sont simplement placées dans une mémoire tampon. Rien n’est transmis sur le réseau lorsque nous appelons **IoTHubClient\_Oudre\_SendEventAsync**. Dans l’ordre de pénétration réellement les données à IoT concentrateur, vous devez appeler **IoTHubClient\_Oudre\_DoWork**, comme dans cet exemple :

```
while (1)
{
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(1000);
}
```

Ce code (à partir de la **iothub\_client\_exemple\_http** application) appelle à plusieurs reprises **IoTHubClient\_Oudre\_DoWork**. Chaque fois que **IoTHubClient\_Oudre\_DoWork** est appelé, il envoie certains événements à partir de la mémoire tampon à IoT concentrateur et il récupère un message en file d’attente envoyé à l’appareil. Ce dernier cas signifie que si nous avons enregistré une fonction de rappel pour les messages, puis le rappel est appelé (en supposant que tous les messages sont la file d’attente). Nous peut inscrit telle une fonction de rappel avec un code comme suit :

```
IoTHubClient_LL_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext)
```

La raison pour laquelle qui **IoTHubClient\_Oudre\_DoWork** est souvent appelée d’une boucle qui est chaque fois qu’elle est appelée, il envoie *certaines* mises en mémoire tamponné événements à IoT concentrateur et récupère *l’autre* message en attente vers le haut de l’appareil. Chaque appel n’est pas garantie pour envoyer des mises en mémoire tampon tous les événements ou pour extraire tous les messages en file d’attente. Si vous voulez envoyer tous les événements dans la mémoire tampon, puis poursuivez sur le traitement des autres, vous pouvez remplacer cette boucle avec un code comme suit :

```
IOTHUB_CLIENT_STATUS status;

while ((IoTHubClient_LL_GetSendStatus(iotHubClientHandle, &status) == IOTHUB_CLIENT_OK) && (status == IOTHUB_CLIENT_SEND_STATUS_BUSY))
{
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(1000);
}
```

Ce code appelle **IoTHubClient\_Oudre\_DoWork** jusqu'à ce que tous les événements de la mémoire tampon ont été envoyés à IoT concentrateur. Remarque Cela n’également implique que tous les messages en file d’attente ont été reçus. Partie de la raison est que vérifier les messages « tout » n’est pas une action comme déterministe. Que se passe-t-il si vous récupérez des « tout » des messages, mais puis un autre est envoyé à l’appareil immédiatement après ? Une meilleure façon de traiter avec un délai d’expiration programmé. Par exemple, la fonction de rappel de message pu réinitialiser un minuteur chaque fois qu’elle est appelée. Vous pouvez ensuite écrire logique pour poursuivre le traitement si, par exemple, aucun message n’a été reçu des dernières secondes *X* .

Lorsque vous avez terminé la pénétration de molécules événements et recevoir des messages, veillez à appeler la fonction correspondante pour nettoyer les ressources.

```
IoTHubClient_LL_Destroy(iotHubClientHandle);
```

Il est essentiel qu’un seul ensemble d’API pour envoyer et recevoir des données avec un thread d’arrière-plan et un autre jeu d’API qui effectue la même chose sans le thread d’arrière-plan. Un grand nombre de développeurs peut être préférable des API non - Oudre, mais les API de niveau inférieur sont utiles lorsque le développeur souhaite explicite contrôler la transmission de réseau. Par exemple, certains appareils collecter les données dans le temps et événements pénétration uniquement à des intervalles spécifiés (par exemple, une fois toutes les heures ou une fois par jour). Les API de niveau inférieur vous permettent de contrôler explicitement lorsque vous envoyez et recevez des données à partir de IoT concentrateur. D’autres personnes préfèrent simplement la simplicité qui fournissent les API de niveau inférieur. Tout ce qui se passe sur le thread principal plutôt que sur certains cela se produise de travail en arrière-plan.

N’importe quel modèle que vous choisissez, veillez à être cohérent dans quelles API que vous utilisez. Si vous démarrez en appelant **IoTHubClient\_Oudre\_CreateFromConnectionString**, veillez à utiliser uniquement l’API de niveau inférieur correspondants pour tout travail suivi :

-   IoTHubClient\_Oudre\_SendEventAsync

-   IoTHubClient\_Oudre\_SetMessageCallback

-   IoTHubClient\_Oudre\_Destroy

-   IoTHubClient\_Oudre\_DoWork

L’inverse est également vrai. Si vous commencez avec **IoTHubClient\_CreateFromConnectionString**, puis utilisez les API non - Oudre pour un traitement supplémentaire.

Dans le périphérique IoT Azure SDK pour C, voir la **iothub\_client\_exemple\_http** application pour obtenir un exemple complet de l’API de niveau inférieur. La **iothub\_client\_exemple\_amqp** application peut être référencée pour obtenir un exemple complet de l’API Oudre.

## <a name="property-handling"></a>Gestion de propriété

Jusqu'à présent lorsque que nous avons décrit l’envoi de données, nous avons été faites référence dans le corps du message. Par exemple, considérez ce code :

```
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Hello World");
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText));
IoTHubClient_LL_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message)
```

Cet exemple envoie un message à concentrateur IoT avec le texte « Bonjour tout le monde ». Toutefois, IoT concentrateur permet également de propriétés à joindre à chaque message. Les propriétés sont des paires nom/valeur qui peuvent être attachés au message. Par exemple, nous pouvons modifier le code précédent pour joindre une propriété au message :

```
MAP_HANDLE propMap = IoTHubMessage_Properties(message.messageHandle);
sprintf_s(propText, sizeof(propText), "%d", i);
Map_AddOrUpdate(propMap, "SequenceNumber", propText);
```

Nous allons commencer par appel **IoTHubMessage\_propriétés** et en lui passant la poignée de notre message. Ce que nous revenir est un **carte\_gérer** référence qui permet de commencer à ajouter des propriétés. Accompli en appelant **carte\_AddOrUpdate**, qui contient une référence à une carte\_poignée, le nom de la propriété et la valeur de propriété. Cette API nous pouvons ajouter autant de propriétés que nous le souhaitez.

Lorsque l’événement est lu à partir de **l’Événement Hubs**, le destinataire peut énumérer les propriétés et extraire les valeurs correspondantes. Par exemple, dans .NET ce serait faire en accédant à la [collection Properties de l’objet EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.properties.aspx).

Dans l’exemple précédent, nous allons joignant des propriétés pour un événement qui nous envoyer à IoT concentrateur. Propriétés peuvent également être associées aux messages reçus à partir du IoT Hub. Si nous voulons récupérer les propriétés d’un message, nous pouvons utiliser le code suivant dans notre fonction de rappel de message :

```
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    . . .

    // Retrieve properties from the message
    MAP_HANDLE mapProperties = IoTHubMessage_Properties(message);
    if (mapProperties != NULL)
    {
        const char*const* keys;
        const char*const* values;
        size_t propertyCount = 0;
        if (Map_GetInternals(mapProperties, &keys, &values, &propertyCount) == MAP_OK)
        {
            if (propertyCount > 0)
            {
                printf("Message Properties:\r\n");
                for (size_t index = 0; index < propertyCount; index++)
                {
                    printf("\tKey: %s Value: %s\r\n", keys[index], values[index]);
                }
                printf("\r\n");
            }
        }
    }

    . . .
}
```

L’appel vers **IoTHubMessage\_propriétés** renvoie la **carte\_gérer** référence. Nous transmettre puis cette référence à **carte\_GetInternals** pour obtenir une référence à un tableau de paires nom/valeur (ainsi que d’un compteur de propriétés). À ce stade, il est très simple d’énumérer les propriétés pour obtenir les valeurs que nous voulons.

Vous n’êtes pas obligé d’utiliser des propriétés dans votre application. Toutefois, si vous avez besoin définir les événements ou les récupérer des messages, la bibliothèque **IoTHubClient** facilite.

## <a name="message-handling"></a>Gestion des messages

Comme indiqué précédemment, lorsque les messages arrivent IoT concentrateur la bibliothèque **IoTHubClient** répond en appelant une fonction de rappel enregistré. Il existe un paramètre de retour de cette fonction que vous voulez attribuer une explication supplémentaire. Voici un extrait de la fonction de rappel dans le **iothub\_client\_exemple\_http** exemple d’application :

```
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    . . .
    return IOTHUBMESSAGE_ACCEPTED;
}
```

Notez que la valeur de retour est de type **IOTHUBMESSAGE\_destruction\_résultat** et dans ce cas particulier, nous retournons **IOTHUBMESSAGE\_accepté**. Il existe d’autres valeurs que nous pouvons retourner à partir de cette fonction qui change la manière dont la bibliothèque **IoTHubClient** réagit au rappel de message. Voici les options.

-   **IOTHUBMESSAGE\_accepté** – le message a été traité avec succès. La bibliothèque **IoTHubClient** n’appelle pas la fonction de rappel à nouveau avec le même message.

-   **IOTHUBMESSAGE\_rejeté** : le message n’a pas été traité et il n’existe aucune souhaité pour le faire à l’avenir. La bibliothèque **IoTHubClient** ne doit pas appeler la fonction de rappel à nouveau avec le même message.

-   **IOTHUBMESSAGE\_abandonné** – le message n’a pas été traité avec succès, mais la bibliothèque **IoTHubClient** doit appeler la fonction de rappel à nouveau avec le même message.

Pour les codes de retour deux premiers, la bibliothèque **IoTHubClient** envoie un message à concentrateur IoT indiquant que le message doit être supprimé de la file d’attente et pas été remis à nouveau. Le résultat est le même (le message est supprimé de la file d’attente), mais que le message a été accepté ou refusé est toujours enregistré.  L’enregistrement de cette distinction est utile d’expéditeurs du message qui peuvent écouter des commentaires et déterminer si un appareil a acceptées ou refusées un message particulier.

Dans le dernier cas un message est également envoyé à IoT concentrateur, mais il indique que le message doit être redistribué. Vous allez généralement abandon d’un message si vous rencontrez des erreurs mais vous voulez essayer traiter le message à nouveau. En revanche, rejeter un message est approprié lorsque vous rencontrez une erreur irrécupérable (ou si vous décidez simplement que vous ne voulez pas traiter le message).

Dans tous les cas, tenir compte des différents codes de retour afin que vous pouvez susciter le comportement de que votre choix à partir de la bibliothèque **IoTHubClient** .

## <a name="alternate-device-credentials"></a>Informations d’identification de l’autre appareil

Comme indiqué précédemment, la première chose à faire lorsque vous travaillez avec la bibliothèque **IoTHubClient** consiste à obtenir un **IOTHUB\_CLIENT\_gérer** avec un appel comme suit :

```
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

Les arguments **IoTHubClient\_CreateFromConnectionString** sont la chaîne de connexion de notre périphérique et un paramètre qui indique le protocole nous permet de communiquer avec IoT concentrateur. La chaîne de connexion comporte un format qui s’affiche comme suit :

```
HostName=IOTHUBNAME.IOTHUBSUFFIX;DeviceId=DEVICEID;SharedAccessKey=SHAREDACCESSKEY
```

Il existe quatre éléments d’information dans cette chaîne : nom IoT Hub, suffixe IoT concentrateur, ID de périphérique et touche d’accès partagé. Vous obtenez le nom de domaine complet (FQDN) d’un concentrateur IoT lorsque vous créez votre instance concentrateur IoT dans le portail Azure — cela vous donne le nom de concentrateur IoT (la première partie du nom de domaine complet) et le suffixe de concentrateur IoT (reste du nom de domaine complet). Vous obtenez l’ID du périphérique et la touche d’accès partagés lorsque vous enregistrez votre appareil avec concentrateur IoT (comme décrit dans l' [article précédent](iot-hub-device-sdk-c-intro.md)).

**IoTHubClient\_CreateFromConnectionString** vous donne un moyen de l’initialisation de la bibliothèque. Si vous préférez, vous pouvez créer un nouveau **IOTHUB\_CLIENT\_gérer** à l’aide de ces paramètres individuels plutôt que la chaîne de connexion. Ceci est réalisé par le code suivant :

```
IOTHUB_CLIENT_CONFIG iotHubClientConfig;
iotHubClientConfig.iotHubName = "";
iotHubClientConfig.deviceId = "";
iotHubClientConfig.deviceKey = "";
iotHubClientConfig.iotHubSuffix = "";
iotHubClientConfig.protocol = HTTP_Protocol;
IOTHUB_CLIENT_HANDLE iotHubClientHandle = IoTHubClient_LL_Create(&iotHubClientConfig);
```

Cela permet de réaliser la même chose que **IoTHubClient\_CreateFromConnectionString**.

Il peut sembler évident que vous pouvez utiliser **IoTHubClient\_CreateFromConnectionString** plutôt que cette méthode plus détaillée de l’initialisation. N’oubliez pas, cependant, que lorsque vous enregistrez un appareil dans IoT concentrateur que vous obteniez est un ID de périphérique et la clé du périphérique (pas une chaîne de connexion). L’outil **Gestionnaire de périphériques** SDK introduite dans l' [article précédent](iot-hub-device-sdk-c-intro.md) utilise des bibliothèques dans le **service IoT Azure SDK** pour créer la chaîne de connexion à partir de l’ID du périphérique, clé de périphérique et concentrateur IoT nom d’hôte. Ainsi, l’appel **IoTHubClient\_Oudre\_créer** peut être préférable, car il vous n’avez pas à créer une chaîne de connexion. Utilisez la méthode est pratique.

## <a name="configuration-options"></a>Options de configuration

Jusqu'à présent, tout ce que décrits sur le fonctionnement de la bibliothèque **IoTHubClient** reflète son comportement par défaut. Toutefois, il existe plusieurs options que vous pouvez définir pour modifier le fonctionnement de la bibliothèque. Cela grâce à la **IoTHubClient\_Oudre\_SetOption** API. Prenons l’exemple :

```
unsigned int timeout = 30000;
IoTHubClient_LL_SetOption(iotHubClientHandle, "timeout", &timeout);
```

Il existe différentes options fréquemment utilisées :

-   **SetBatching** (booléen) – si **Vrai**, puis les données envoyées à IoT concentrateur est envoyé par lots. Si la **valeur false**, puis les messages sont envoyés individuellement. La valeur par défaut est **false**. Notez que l’option **SetBatching** s’applique uniquement pour le protocole HTTP et pas pour les protocoles MQTT ou AMQP.

-   **Délai d’attente** (entier) – cette valeur est représentée en millisecondes. Si vous envoyez une demande HTTP ou recevoir une réponse est plus longue que cette fois, puis la connexion arrive à expiration.

L’option traitement par lots est importante. Par défaut, les événements ingresses bibliothèque individuellement (un seul événement est tout ce que vous passez à **IoTHubClient\_Oudre\_SendEventAsync**). Si l’option traitement par lots est **Vrai**, la bibliothèque de collecte des événements autant que possible de la mémoire tampon (jusqu'à la taille maximale des messages qui IoT concentrateur acceptera).  Le lot d’événements est envoyé à IoT concentrateur dans un seul appel HTTP (les événements individuels sont regroupés dans un tableau JSON). L’activation le traitement par lots généralement entraîne des gains de performances volumineux dans la mesure où vous êtes réduction boucles réseau. Il réduit également considérablement la bande passante dans la mesure où vous envoyez un ensemble d’en-têtes HTTP avec un lot d’événements plutôt qu’un ensemble d’en-têtes pour chaque événement individuel. À moins d’avoir une raison spécifique à suivre dans le cas contraire, généralement vous souhaiterez activer le traitement par lots.

## <a name="next-steps"></a>Étapes suivantes

Cet article décrit en détail le comportement de la bibliothèque **IoTHubClient** figurant dans l' **appareil IoT Azure SDK pour C**. Grâce à ces informations, vous devez disposer d’une idée des fonctionnalités de la bibliothèque **IoTHubClient** . L' [article suivant](iot-hub-device-sdk-c-serializer.md) fournit des détails similaires sur la bibliothèque **sérialiseur** .

Pour plus d’informations sur le développement pour IoT concentrateur, voir la [IoT concentrateur SDK][lnk-sdks].

Pour en savoir davantage les fonctionnalités du concentrateur IoT, voir :

- [Simulation d’un appareil avec le Kit de développement de passerelle IoT][lnk-gateway]

[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
