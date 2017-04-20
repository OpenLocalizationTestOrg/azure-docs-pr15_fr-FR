<properties
 pageTitle="Guide du développeur - points de terminaison IoT concentrateur | Microsoft Azure"
 description="Guide du développeur IoT concentrateur Azure - informations de référence sur les points de terminaison IoT concentrateur"
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

# <a name="reference---iot-hub-endpoints"></a>Référence - points de terminaison IoT concentrateur

## <a name="list-of-iot-hub-endpoints"></a>Liste des points de terminaison IoT concentrateur

Concentrateur IoT Azure est un service client multiples qui expose ses fonctionnalités à différents intervenants. L’illustration ci-dessous présente les différents points de terminaison que IoT concentrateur expose.

![Points de terminaison IoT concentrateur][img-endpoints]

Voici une description des points de terminaison :

* **Fournisseur de ressources**. Le fournisseur de ressources IoT concentrateur expose un [Gestionnaire de ressources Azure] [ lnk-arm] interface qui permet aux propriétaires d’abonnement Azure créer et supprimer IoT hubs et mettre à jour les propriétés du concentrateur IoT. Propriétés du concentrateur IoT régissent [stratégies de sécurité au niveau du concentrateur][lnk-accesscontrol], plutôt que d’utiliser le contrôle d’accès au niveau du périphérique et options fonctionnelles pour messagerie cloud vers le périphérique et appareil-nuage. Le fournisseur de ressources IoT concentrateur vous permet également [d’Exporter les identités des appareils][lnk-importexport].
* **Gestion des identités appareil**. Chaque concentrateur IoT expose un jeu de points de terminaison HTTP reste pour gérer les identités appareil (créer, récupérer, mettre à jour et supprimer). [Identités des appareils] [ lnk-device-identities] sont utilisées pour appareil d’authentification et contrôle d’accès.
* **Gestion des appareils double**. Chaque concentrateur IoT expose un ensemble de service not HTTP reste du point de terminaison de la requête et mettre à jour des [jumeaux appareil] [ lnk-twins] (mettre à jour des balises et des propriétés).
* **Gestion des tâches**. Chaque concentrateur IoT expose un ensemble de service not HTTP reste du point de terminaison de la requête et gérer les [travaux][lnk-jobs].
* **Points de terminaison de périphérique**. Pour chaque périphérique mis en service dans le Registre d’identité de périphérique, concentrateur IoT expose un jeu de points de terminaison qu’un périphérique peut utiliser pour envoyer et recevoir des messages :
    - *Envoyer des messages de l’appareil-nuage*. Utilisez ce point de terminaison pour [Envoyer des messages de l’appareil-nuage][lnk-d2c].
    - *Réception des messages du cloud vers le périphérique*. Un périphérique utilise ce point de terminaison pour recevoir les [messages cloud vers le périphérique][lnk-c2d].
    - *Démarrer une conversation de téléchargements de fichiers*. Un périphérique utilise ce point de terminaison pour recevoir un URI associations de sécurité de stockage Azure IoT concentrateur pour [télécharger un fichier][lnk-upload].
    - *Récupérer et propriétés du double mise à jour*. Un périphérique utilise ce points de terminaison pour accéder aux son [double appareil][lnk-twins]de propriétés.
    - *Demandes de méthodes directes de réception*. Un périphérique utilise ce points de terminaison pour écouter [méthodes directes][lnk-methods]de requêtes.

    Ces points de terminaison sont présentés à l’aide de [MQTT v3.1.1][lnk-mqtt], HTTP 1.1 et [1.0 AMQP] [ lnk-amqp] protocoles. Notez que AMQP est également disponible sur [WebSocket] [ lnk-websockets] sur le port 443.
    
    Endpoins les jumeaux et les méthodes sont disponibles uniquement à l’aide de [MQTT v3.1.1][lnk-mqtt].

* **Points de terminaison de Service**. Chaque concentrateur IoT expose un jeu de points de terminaison de que votre application principale peut utiliser pour communiquer avec vos appareils. Ces points de terminaison sont actuellement uniquement exposée à l’aide de [AMQP] [ lnk-amqp] protocole, à l’exception du point de terminaison méthode appel qui est exposé via HTTP 1.1.
    - *Recevoir des messages appareil-nuage*. Ce point de terminaison est compatible avec [Azure événement Hubs][lnk-event-hubs]. Un service principale s’en servir à lire tous les [messages de l’appareil-nuage] [ lnk-d2c] envoyé par vos appareils.
    - *Cloud vers le périphérique envoyez et recevez des accusés de remise*. Ces points de terminaison activer votre application principale envoyer des [messages cloud vers le périphérique]fiable[lnk-c2d]et de recevoir la livraison correspondante ou les accusés de réception d’expiration.
    - *Recevoir des notifications de fichier*. Ce point de terminaison de messagerie vous permet de recevoir des notifications de lors de vos périphériques correctement téléchargement un fichier. 
    - *Appel de la méthode directe*. Ce point de terminaison permet à un service de données principale appeler une [méthode directe] [ lnk-methods] sur un appareil.

[IoT concentrateur API et SDK] [ lnk-sdks] article décrit les différentes façons d’accéder à ces points de terminaison.

Enfin, il est important de noter que tous les points de terminaison IoT concentrateur utilisent le [TLS] [ lnk-tls] protocole et aucun point de terminaison n’est jamais exposée sur canaux non chiffré/non sécurisé.

## <a name="field-gateways"></a>Passerelles de champ

Dans une solution IoT, une *passerelle champ* se situe entre vos périphériques et vos points de terminaison IoT concentrateur. Il est généralement situé à proximité vos appareils. Vos périphériques de communiquent directement avec la passerelle de champ en utilisant un protocole pris en charge par les appareils. La passerelle champ se connecte à un point de terminaison IoT concentrateur à l’aide d’un protocole est pris en charge par IoT concentrateur. Une passerelle de champ peut être matériel hautement spécialisé ou un faible puissance ordinateur exécutant un logiciel qui permet de réaliser le scénario de bout en bout auquel la passerelle est destinée.

Vous pouvez utiliser le [Kit de développement logiciel Azure IoT passerelle] [ lnk-gateway-sdk] pour mettre en œuvre une passerelle de champ. Ce SDK offre des fonctionnalités spécifiques telles que la possibilité de multiplexage la communication de plusieurs appareils sur la même connexion à IoT concentrateur.

## <a name="next-steps"></a>Étapes suivantes

Autres rubriques de référence dans ce guide pour les développeurs IoT concentrateur sont les suivantes :

- [Langage de requête pour jumeaux, méthodes et les tâches][lnk-devguide-query]
- [Les quotas et la limitation][lnk-devguide-quotas]
- [Prise en charge IoT concentrateur MQTT][lnk-devguide-mqtt]

[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk

[img-endpoints]: ./media/iot-hub-devguide-endpoints/endpoints.png
[lnk-amqp]: https://www.amqp.org/
[lnk-mqtt]: http://mqtt.org/
[lnk-websockets]: https://tools.ietf.org/html/rfc6455
[lnk-arm]: ../azure-resource-manager/resource-group-overview.md
[lnk-event-hubs]: http://azure.microsoft.com/documentation/services/event-hubs/

[lnk-tls]: https://tools.ietf.org/html/rfc5246


[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-accesscontrol]: iot-hub-devguide-security.md#access-control-and-permissions
[lnk-importexport]: iot-hub-devguide-identity-registry.md#import-and-export-device-identities
[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-device-identities]: iot-hub-devguide-identity-registry.md
[lnk-upload]: iot-hub-devguide-file-upload.md
[lnk-c2d]: iot-hub-devguide-messaging.md#cloud-to-device-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-jobs]: iot-hub-devguide-jobs.md

[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md