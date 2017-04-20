<properties
 pageTitle="Solutions Azure pour Internet des objets | Microsoft Azure"
 description="Une vue d’ensemble des IoT sur Azure, y compris une architecture de solution exemple et comment elle est liée à Azure IoT concentrateur appareil SDK et des solutions préconfigurées"
 services="iot-hub"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="10/05/2016"
 ms.author="dobett"/>

[AZURE.INCLUDE [iot-azure-and-iot](../../includes/iot-azure-and-iot.md)]

## <a name="next-steps"></a>Étapes suivantes

Concentrateur IoT Azure est un service Azure qui permet de sécuriser et fiable communication bidirectionnelle entre votre application back-end et millions d’appareils. Il permet à l’application principale à :

- Recevoir télémétrie à l’échelle de vos appareils.
- Acheminer des données à partir de vos périphériques à un processeur d’événements de flux de données.
- Recevoir des téléchargements de fichiers à partir d’appareils.
- Envoyer des commandes de cloud pour appareil à des appareils spécifiques.

Vous pouvez utiliser IoT concentrateur pour implémenter votre propre principaux de la solution. En outre, concentrateur IoT inclut un Registre identité appareil utilisé pour la mise en service des appareils, de leurs informations d’identification de sécurité et leurs droits pour se connecter au concentrateur. Pour en savoir plus sur IoT concentrateur, voir [que constitue IoT ?] [lnk-iot-hub].

Pour découvrir comment Azure IoT Hub autorise la gestion des périphériques normalisée IoT vous permettant de gérer à distance, configurer et mettre à jour vos appareils, voir [Gestion des périphériques vue d’ensemble de Azure IoT concentrateur][lnk-device-management].

Pour mettre en œuvre des applications clientes sur un large éventail de systèmes d’exploitation et plates-formes matérielles appareil, vous pouvez utiliser l’appareil IoT SDK. Le périphérique IoT SDK inclure des bibliothèques qui facilitent l’envoi de télémétrie à un concentrateur IoT et reçoivent les commandes de cloud vers le périphérique. Lorsque vous utilisez le SDK, vous pouvez choisir de plusieurs protocoles réseau pour communiquer avec IoT concentrateur. Pour plus d’informations, voir les [informations relatives à l’appareil SDK][lnk-device-sdks].

Pour commencer à écrire du code et l’exécution des exemples, voir la [prise en main IoT concentrateur] [ lnk-getstarted] didacticiel.

Vous pouvez également intéresser [Azure IoT Suite][lnk-iot-suite], qui est un ensemble de solutions préconfigurés. IoT Suite vous permet de prise en main rapide et mettre à l’échelle IoT projets pour traiter des scénarios IoT courants, tels que le contrôle à distance, la gestion des ressources et de maintenance prédictive.

[lnk-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-iot-hub]: iot-hub-what-is-iot-hub.md
[lnk-iot-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-iotdev]: https://azure.microsoft.com/develop/iot/
[lnk-device-management]: iot-hub-device-management-overview.md