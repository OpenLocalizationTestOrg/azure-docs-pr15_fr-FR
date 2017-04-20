<properties
 pageTitle="Comparer Azure concentrateur IoT aux événements Azure Hubs | Microsoft Azure"
 description="Comparaison des services Azure IoT concentrateur et Azure événement Hubs mise en surbrillance des différences fonctionnelles et des exemples d’utilisation."
 services="iot-hub"
 documentationCenter=""
 authors="fsautomata"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="06/06/2016"
 ms.author="elioda"/>

# <a name="comparison-of-azure-iot-hub-and-azure-event-hubs"></a>Comparaison d’Azure IoT concentrateur et Hubs événement Azure

Parmi les exemples d’utilisation principale pour concentrateur IoT consiste à recueillir télémétrie à partir d’appareils. Pour cette raison, IoT concentrateur est souvent comparée à [Azure événement Hubs][]. Comme concentrateur IoT, événement Hubs est un service qui permet aux événements et télémétrie pénétration dans le cloud à grande échelle, avec une latence faible et fiabilité de traitement des événements.

Toutefois, les services ont des nombreuses différences, qui sont détaillées dans le tableau suivant.

| Zone | Concentrateur IoT | Événement Hubs |
| ---- | ------- | ---------- |
| Modèles de communication | Permet de messagerie appareil-nuage et cloud vers le périphérique. | Active uniquement pénétration événement (généralement considérée pour appareil-nuage scénarios). |
| Prise en charge du protocole de périphériques | Prend en charge MQTT, AMQP, AMQP WebSocket et HTTP. En outre, IoT concentrateur fonctionne avec la [passerelle de protocole Azure IoT][lnk-azure-protocol-gateway], une mise en œuvre de passerelle protocole personnalisables pour prendre en charge les protocoles personnalisés. | Prend en charge AMQP, AMQP sur WebSocket et HTTP. |
| Sécurité | Fournit une identité par périphérique et contrôle d’accès révocable. Consultez la [section sécurité du guide pour les développeurs IoT concentrateur]. | Fournit [les stratégies d’accès partagé]à l’échelle Hubs événement[Event Hubs - security], avec révocation limitée prend en charge par le biais [des stratégies de l’éditeur][Event Hubs publisher policies]. Solutions IoT sont souvent nécessaire pour implémenter une solution personnalisée pour prendre en charge les informations d’identification par périphérique et les mesures anti-l’usurpation d’identité. |
| Contrôle des opérations | Permet aux solutions IoT s’abonner à un large éventail d’appareil identité connectivité et gestion des litiges erreurs d’authentification appareil individuels, la limitation et les exceptions de format incorrect. Ces événements permettent d’identifier les problèmes de connectivité au niveau du périphérique individuels. | Expose uniquement les mesures de regroupement. |
| Échelle | Est optimisé pour prendre en charge des millions de périphériques connectés simultanément. | Peut prendre en charge un nombre plus limité de connexions simultanées--jusqu'à 5 000 connexions AMQP, comme [les quotas de Bus des services Azure][]. Quant à eux, événement Hubs vous permet de définir la partition pour chaque message envoyé. |
| Kits SDK de périphériques | Fournit [appareil SDK] [ Azure IoT Hub SDKs] pour une grande variété de plateformes et langues. | Est prise en charge sur .NET et C. Fournit également AMQP et HTTP envoient des interfaces. |
| Téléchargement de fichier | Permet aux solutions IoT télécharger des fichiers à partir d’appareils dans le cloud. Inclut un point de terminaison de notification de fichier pour l’intégration de flux de travail et un contrôle de catégorie pour le support de débogage des opérations. | Utilise un motif de contrôle réclamer pour demander des fichiers à partir d’appareils et fournir des périphériques avec une clé de stockage pour la transaction manuellement. |

En résumé, même si le cas d’utilisation uniquement est pénétration de télémétrie appareil-nuage, concentrateur IoT fournit un service qui est conçu pour la connectivité IoT appareil. Il continue à développer des propositions de valeur pour ces scénarios avec des fonctionnalités spécifiques IoT. Événement Hubs est conçu pour pénétration événement à une grande échelle, les deux dans le contexte de scénarios notamment-centre de données et le centre de données interne.

Il n’est pas courant d’utiliser IoT concentrateur et Hubs événement dans la même solution--où IoT concentrateur gère la communication appareil-nuage, et événements Hubs pénétration événement stade ultérieur dans les moteurs de traitement en temps réel.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la planification de votre déploiement IoT concentrateur, voir [mise à l’échelle, HA et DR][lnk-scaling].

Pour en savoir davantage les fonctionnalités du concentrateur IoT, voir :

- [Guide du développeur][lnk-devguide]
- [Simulation d’un appareil avec le Kit de développement de passerelle IoT][lnk-gateway]

[Événement Azure Hubs]: ../event-hubs/event-hubs-what-is-event-hubs.md
[Section de sécurité du guide pour les développeurs IoT concentrateur]: iot-hub-devguide-security.md
[Event Hubs - security]: ../event-hubs/event-hubs-authentication-and-security-model-overview.md
[Event Hubs publisher policies]: ../event-hubs/event-hubs-overview.md#common-publisher-tasks
[Quotas de Bus des services Azure]: ../service-bus-messaging/service-bus-quotas.md
[Azure IoT Hub SDKs]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md

[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
