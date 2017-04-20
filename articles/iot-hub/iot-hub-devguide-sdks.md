<properties
 pageTitle="Guide du développeur - IoT concentrateur SDK | Microsoft Azure"
 description="Azure IoT concentrateur développeur guide - informations et des liens vers divers Azure IoT concentrateur périphérique et service kits de développement logiciel."
 services="iot-hub"
 documentationCenter=""
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

# <a name="iot-hub-sdks"></a>Concentrateur IoT SDK

## <a name="iot-hub-device-sdks"></a>Périphérique concentrateur IoT SDK

Le périphérique Microsoft Azure IoT SDK contient du code qui facilite les périphériques de construction et les applications qui se connectent à et sont gérées par services Azure IoT concentrateur.

Le périphérique IoT suivant SDK sont disponibles pour télécharger à partir de GitHub :

- [APPAREIL IoT Azure SDK pour C] [ lnk-c-device-sdk] écrit en C ANSI (C99) pour la portabilité et la compatibilité de nombreuses plates-formes.
- [APPAREIL IoT Azure SDK pour .NET][lnk-dotnet-device-sdk]
- [APPAREIL IoT Azure SDK pour Java][lnk-java-device-sdk]
- [APPAREIL IoT Azure SDK pour Node.js][lnk-node-device-sdk]
- [Microsoft Azure IoT appareil SDK pour Python 2.7][lnk-python-device-sdk]

> [AZURE.NOTE] Afficher les fichiers Lisez-moi dans les référentiels GitHub pour plus d’informations sur l’utilisation de langue et les responsables du lot spécifique à la plateforme installer binaires et dépendances sur votre ordinateur de développement.

## <a name="os-platforms-and-hardware-compatibility"></a>Compatibilité plateformes de système d’exploitation et le matériel

Pour plus d’informations sur la compatibilité SDK avec des périphériques matériels spécifiques, voir la [Azure certifié pour le catalogue d’appareil IoT][lnk-certified].

## <a name="iot-hub-service-sdks"></a>Concentrateur IoT service SDK

Kits de développement logiciel Microsoft Azure IoT service contient du code qui facilite la création d’applications qui interagissent directement avec IoT concentrateur pour gérer les appareils et sécurité.

Le service IoT suivant SDK sont disponibles pour télécharger à partir de GitHub :

- [Service IoT Azure SDK pour .NET][lnk-dotnet-service-sdk]
- [Service IoT Azure SDK pour Node.js][lnk-node-service-sdk]
- [Service IoT Azure SDK pour Java][lnk-java-service-sdk]

> [AZURE.NOTE] Afficher les fichiers Lisez-moi dans les référentiels GitHub pour plus d’informations sur l’utilisation de langue et les responsables du lot spécifique à la plateforme installer binaires et dépendances sur votre ordinateur de développement.

## <a name="azure-iot-gateway-sdk"></a>Kit de développement logiciel Azure IoT passerelle

Ce SDK Azure IoT passerelle contient l’infrastructure et les modules pour créer des solutions de passerelle IoT. Vous pouvez étendre le Kit de développement pour créer des passerelles adaptés à n’importe quel scénario de bout en bout.

Vous pouvez télécharger le [Kit de développement logiciel Azure IoT passerelle] [ lnk-gateway-sdk] de GitHub.

## <a name="online-api-reference-documentation"></a>Documentation de référence API en ligne

Voici une liste de liens vers online documentation de référence API pour appareil IoT Azure, le service et les bibliothèques de passerelle :

- [Internet des objets (IoT) .NET][lnk-dotnet-ref]
- [Concentrateur IoT reste][lnk-rest-ref]
- [APPAREIL IoT Azure SDK pour C][lnk-c-ref]
- [APPAREIL IoT Azure SDK pour Java][lnk-java-ref]
- [Service IoT Azure SDK pour Java][lnk-java-service-ref]
- [APPAREIL IoT Azure SDK pour Node.js][lnk-node-ref]
- [Service IoT Azure SDK pour Node.js][lnk-node-service-ref]
- [Azure IoT passerelle SDK][lnk-gateway-ref]

## <a name="next-steps"></a>Étapes suivantes

Autres rubriques de référence dans ce guide pour les développeurs IoT concentrateur sont les suivantes :

- [Points de terminaison IoT concentrateur][lnk-devguide-endpoints]
- [Langage de requête pour jumeaux, méthodes et les tâches][lnk-devguide-query]
- [Les quotas et la limitation][lnk-devguide-quotas]
- [Prise en charge IoT concentrateur MQTT][lnk-devguide-mqtt]

<!-- Links and images -->

[lnk-c-device-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/c/readme.md
[lnk-dotnet-device-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/csharp/device/readme.md
[lnk-java-device-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/java/device/readme.md
[lnk-dotnet-service-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/csharp/service/README.md
[lnk-java-service-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/java/service/readme.md
[lnk-node-device-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/node/device/readme.md
[lnk-node-service-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/node/service/README.md
[lnk-python-device-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/python/device/readme.md
[lnk-certified]: https://catalog.azureiotsuite.com/
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/README.md

[lnk-dotnet-ref]: https://msdn.microsoft.com/library/mt488521.aspx
[lnk-c-ref]: http://azure.github.io/azure-iot-sdks/c/api_reference/index.html
[lnk-java-ref]: http://azure.github.io/azure-iot-sdks/java/device/api_reference/index.html
[lnk-node-ref]: http://azure.github.io/azure-iot-sdks/node/api_reference/azure-iot-device/1.0.15/index.html
[lnk-rest-ref]: https://msdn.microsoft.com/library/mt548492.aspx
[lnk-java-service-ref]: http://azure.github.io/azure-iot-sdks/java/service/api_reference/index.html
[lnk-node-service-ref]: http://azure.github.io/azure-iot-sdks/node/api_reference/azure-iothub/1.0.17/index.html
[lnk-gateway-ref]: http://azure.github.io/azure-iot-gateway-sdk/api_reference/c/html/

[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md