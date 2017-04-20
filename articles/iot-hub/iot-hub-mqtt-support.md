<properties
 pageTitle="Prise en charge IoT concentrateur MQTT | Microsoft Azure"
 description="Description de MQTT prend en charge au niveau des concentrateur IoT"
 services="iot-hub"
 documentationCenter=".net"
 authors="kdotchkoff"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="10/24/2016"
 ms.author="kdotchko"/>

# <a name="iot-hub-mqtt-support"></a>Prise en charge IoT concentrateur MQTT

Concentrateur IoT permet aux périphériques de communiquer avec les points de terminaison appareil IoT concentrateur à l’aide de la [MQTT v3.1.1] [ lnk-mqtt-org] protocole sur le port 8883 ou v3.1.1 MQTT via le protocole WebSocket sur le port 443. Concentrateur IoT requiert toutes les communications appareil à être sécurisées en utilisant SSL/TLS (par conséquent, concentrateur IoT non prises en charge de connexions non sécurisées via le port 1883).

## <a name="connecting-to-iot-hub"></a>Connexion à un concentrateur IoT

Un périphérique peut utiliser le protocole MQTT pour vous connecter à un concentrateur IoT en utilisant les bibliothèques dans le [Microsoft Azure IoT SDK] [ lnk-device-sdks] ou à l’aide de la MQTT protocole directement.

## <a name="using-the-device-client-sdks"></a>Utilise le client appareil SDK

[Client d’appareil SDK] [ lnk-device-sdks] qui prennent en charge le protocole MQTT sont disponibles pour Java, Node.js, C, c# et Python. Le client d’appareil SDK utiliser la chaîne de connexion IoT concentrateur standard pour établir une connexion à un concentrateur IoT. Pour utiliser le protocole MQTT, le paramètre de protocole client doit être défini à **MQTT**. Par défaut, le client d’appareil SDK se connecter à un concentrateur IoT avec la **CleanSession** indicateur défini à **0** et utiliser **1 de qualité de service** pour échanger des messages avec le hub IoT.

Lorsqu’un périphérique est connecté à un concentrateur IoT, le client d’appareil SDK fournissent des méthodes permettant du périphérique pour envoyer des messages et recevoir des messages à partir d’un concentrateur IoT.

Le tableau suivant contient des liens vers des exemples de code pour chaque langue prise en charge et spécifiant le paramètre à utiliser pour établir une connexion à un concentrateur IoT en utilisant le protocole MQTT.

| Langue                   | Paramètre de protocole        |
| -------------------------- | ------------------------- |
| [Node.js][lnk-sample-node] | Azure-iot-appareil-mqtt     |
| [Java][lnk-sample-java]    | IotHubClientProtocol.MQTT |
| [C][lnk-sample-c]          | MQTT_Protocol             |
| [C#][lnk-sample-csharp]    | TransportType.Mqtt        |
| [Python][lnk-sample-python] | IoTHubTransportProvider.MQTT |

### <a name="migrating-a-device-app-from-amqp-to-mqtt"></a>Migration d’une application d’appareil de AMQP vers MQTT
Si vous utilisez le [client d’appareil SDK][lnk-device-sdks], à partir de l’aide AMQP à MQTT requiert une modification du paramètre de protocole lors de l’initialisation de client comme indiqué ci-dessus.

Dans ce cas, veillez à vérifier les éléments suivants :

* AMQP renvoie des erreurs de nombreux facteurs, tandis que MQTT met fin à la connexion. Par conséquent votre logique exceptions peuvent nécessiter certaines modifications.
* MQTT ne prend pas en charge les opérations de *Refuser* lors de la réception de [messages C2D][lnk-messaging]. Si votre serveur principal a besoin recevoir une réponse de l’application d’appareil, envisagez d’utiliser des [méthodes directes][lnk-methods].

## <a name="using-the-mqtt-protocol-directly"></a>À l’aide du protocole MQTT directement

Si un périphérique ne peut pas utiliser le client d’appareil SDK, elle peut toujours se connecter aux points de terminaison périphérique public en utilisant le protocole MQTT. Dans le paquet **se connecter** le périphérique doit utiliser les valeurs suivantes :

- Pour le champ **identifiant du client** , utilisez l' **ID de périphérique**. 
- Le champ **nom d’utilisateur** , utilisez `{iothubhostname}/{device_id}`, où {iothubhostname} est l’enregistrement CName complète du concentrateur IoT.

    Par exemple, si le nom de votre plateforme IoT est **contoso.azure devices.net** et que le nom de votre appareil est **MyDevice01**, le champ **nom d’utilisateur** complet doit contenir `contoso.azure-devices.net/MyDevice01`.

- Pour le champ **mot de passe** , utiliser un jeton associations de sécurité. Le format du jeton associations de sécurité est identique à celle des protocoles HTTP et AMQP :<br/>`SharedAccessSignature sig={signature-string}&se={expiry}&sr={URL-encoded-resourceURI}`.

    Pour plus d’informations sur la façon de générer des jetons associations de sécurité, consultez la section périphérique de [jetons de sécurité à l’aide de IoT concentrateur][lnk-sas-tokens].
    
    Lorsque vous testez, vous pouvez également utiliser l' [Explorateur de périphérique] [ lnk-device-explorer] outil permet de générer rapidement un jeton associations de sécurité que vous pouvez copier et coller dans votre propre code :
    
    1. Accédez à l’onglet **gestion** de l’Explorateur de périphérique.
    2. Cliquez sur **Sa jeton** (en haut à droite).
    3. Sous **SASTokenForm**, sélectionnez votre appareil dans **l’ID de périphérique** de liste déroulante. Définir votre **TTL**.
    4. Cliquez sur **Générer** pour créer votre jeton.
    
    Le jeton de sa est généré présente cette structure :   `HostName={your hub name}.azure-devices.net;DeviceId=javadevice;SharedAccessSignature=SharedAccessSignature sr={your hub name}.azure-devices.net%2fdevices%2fMyDevice01&sig=vSgHBMUG.....Ntg%3d&se=1456481802`.

    La partie de ce jeton à utiliser comme est le champ **mot de passe** pour vous connecter à l’aide de MQTT :   `SharedAccessSignature sr={your hub name}.azure-devices.net%2fdevices%2fyDevice01&sig=vSgHBMUG.....Ntg%3d&se=1456481802g%3d&se=1456481802`.

Pour MQTT se connecter et se déconnecter de paquets, IoT concentrateur émet un événement sur le canal **Opérations de surveillance** avec des informations supplémentaires qui peuvent vous aider à résoudre les problèmes de connectivité.

### <a name="sending-messages-to-iot-hub"></a>Envoi de messages à IoT concentrateur

Après avoir effectué une connexion réussie, un appareil peut envoyer des messages à IoT concentrateur à l’aide de `devices/{device_id}/messages/events/` ou `devices/{device_id}/messages/events/{property_bag}` comme un **Nom de la rubrique**. La `{property_bag}` élément Active le périphérique envoyer des messages avec les propriétés supplémentaires dans un format codée au format url. Par exemple :

```
RFC 2396-encoded(<PropertyName1>)=RFC 2396-encoded(<PropertyValue1>)&RFC 2396-encoded(<PropertyName2>)=RFC 2396-encoded(<PropertyValue2>)…
```

> [AZURE.NOTE] Cela `{property_bag}` élément utilise le même codage en ce qui concerne les chaînes de requête dans le protocole HTTP.

L’application cliente de périphérique permet également `devices/{device_id}/messages/events/{property_bag}` comme le **sera le nom de la rubrique** définir *ne les messages* soient transférés en tant que message télémétrie.

Concentrateur IoT ne reconnaît pas les messages de qualité de service 2. Si un client d’appareil publie un message avec **2 de qualité de service**, concentrateur IoT ferme la connexion réseau.
Concentrateur IoT n’est pas conservé conserver des messages. Si un périphérique envoie un message avec l’indicateur de **conserver** la valeur 1, concentrateur IoT ajoute le **x-opter-conserver** propriété application au message. Dans ce cas, au lieu de conserver le message conserver, concentrateur IoT passe à l’application principale.

### <a name="receiving-messages"></a>Recevoir des messages

Pour recevoir des messages à partir du IoT Hub, un appareil doit s’abonner à l’aide de `devices/{device_id}/messages/devicebound/#` comme **Filtre rubrique**. Le caractère générique à plusieurs niveaux **#** dans le filtre de rubrique est utilisé uniquement pour autoriser le périphérique à recevoir des propriétés supplémentaires dans le nom de la rubrique. Concentrateur IoT n’autorise pas l’utilisation de la **#** ou **?** caractères génériques pour le filtrage des rubriques sous-adresse. Dans la mesure où IoT concentrateur n’est pas un agent de messagerie de publication sub objectif général, il prend uniquement en charge les noms de la rubrique documentation et les filtres de la rubrique.

Veuillez note, que l’appareil ne recevrez pas tous les messages à partir du IoT Hub, avant qu’il est abonnée correctement son point de terminaison spécifique du périphérique, représentée par la `devices/{device_id}/messages/devicebound/#` filtre rubrique. Après l’établissement d’abonnement réussie, le périphérique commencerez à recevoir uniquement les messages de cloud vers le périphérique qui ont été envoyés après l’heure de l’abonnement à celui-ci. Si le périphérique se connecte avec l’indicateur **CleanSession** défini à **0**, l’abonnement est conservé dans les sessions de différentes. Dans ce cas, la prochaine fois qu’il connecte à **CleanSession 0** le périphérique recevront les messages en attente qui ont été envoyés à celui-ci a été déconnecté. Si le périphérique utilise indicateur **CleanSession** bien que la valeur **1** , il ne recevrez pas tous les messages à partir du IoT Hub jusqu'à ce qu’il permet de s’abonner à son point de terminaison appareil.

Concentrateur IoT propose des messages avec le **Nom de la rubrique** `devices/{device_id}/messages/devicebound/`, ou `devices/{device_id}/messages/devicebound/{property_bag}` si il existe des propriétés de message. `{property_bag}`contient des paires de codée au format url clé/valeur de propriétés de message. Uniquement les propriétés de l’application et les propriétés système définissable par l’utilisateur (par exemple, **messageId** ou **correlationId**) sont incluses dans l’ensemble des propriétés. Noms des propriétés système ont le préfixe **$**, propriétés de l’application utilisent le nom d’origine de la propriété sans préfixe.

Lorsqu’un client de périphérique permet de s’abonner à un sujet avec **2 de qualité de service**, IoT concentrateur accorde niveau de qualité de service maximal 1 dans le paquet **SUBACK** . Après cela, concentrateur IoT remise des messages à l’appareil à l’aide de la qualité de service 1.

### <a name="additional-considerations"></a>Considérations supplémentaires

En termes de final, si vous avez besoin personnaliser le comportement du protocole MQTT sur le côté cloud, vous devez examiner la [passerelle de protocole Azure IoT] [ lnk-azure-protocol-gateway] qui vous permet de déployer une passerelle de protocole personnalisé High performance interface directement avec IoT concentrateur. La passerelle de protocole Azure IoT vous permet de personnaliser le périphérique pour prendre en compte les déploiements MQTT brownfield ou les autres protocoles personnalisés. Cette approche nécessite-t-il, cependant, que vous exécutez et employer une passerelle protocole personnalisé.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, reportez-vous aux [Remarques sur MQTT prend en charge] [ lnk-mqtt-devguide] dans le guide du développeur Azure IoT concentrateur.

Pour en savoir plus sur le protocole MQTT, consultez la [documentation de MQTT][lnk-mqtt-docs].

Pour plus d’informations sur la planification de votre déploiement IoT concentrateur, voir :

- [Azure certifié pour le catalogue d’appareil IoT][lnk-devices]
- [Prise en charge les protocoles supplémentaires][lnk-protocols]
- [Comparer avec Hubs d’événement][lnk-compare]
- [Mise à l’échelle, HA et DR][lnk-scaling]

Pour en savoir davantage les fonctionnalités du concentrateur IoT, voir :

- [Guide du développeur][lnk-devguide]
- [Simulation d’un appareil avec le Kit de développement de passerelle IoT][lnk-gateway]

[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-mqtt-org]: http://mqtt.org/
[lnk-mqtt-docs]: http://mqtt.org/documentation
[lnk-sample-node]: https://github.com/Azure/azure-iot-sdks/blob/develop/node/device/samples/simple_sample_device.js
[lnk-sample-java]: https://github.com/Azure/azure-iot-sdks/blob/develop/java/device/samples/send-receive-sample/src/main/java/samples/com/microsoft/azure/iothub/SendReceive.java
[lnk-sample-c]: https://github.com/Azure/azure-iot-sdks/tree/master/c/iothub_client/samples/iothub_client_sample_mqtt
[lnk-sample-csharp]: https://github.com/Azure/azure-iot-sdks/tree/master/csharp/device/samples
[lnk-sample-python]: https://github.com/Azure/azure-iot-sdks/tree/master/python/device/samples
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/readme.md
[lnk-sas-tokens]: iot-hub-devguide-security.md#using-sas-tokens-as-a-device
[lnk-mqtt-devguide]: iot-hub-devguide-messaging.md#notes-on-mqtt-support
[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md

[lnk-devices]: https://catalog.azureiotsuite.com/
[lnk-protocols]: iot-hub-protocol-gateway.md
[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md

[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-messaging]: iot-hub-devguide-messaging.md
