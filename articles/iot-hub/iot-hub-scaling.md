<properties
 pageTitle="Concentrateur IoT Azure mise à l’échelle | Microsoft Azure"
 description="Décrit comment mettre à l’échelle Azure IoT concentrateur."
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
 ms.date="09/19/2016"
 ms.author="elioda"/>

# <a name="scaling-iot-hub"></a>Mise à l’échelle IoT concentrateur

Concentrateur IoT Azure peut prendre en charge jusqu'à un millions d’appareils connecté simultanément. Pour plus d’informations, voir [IoT concentrateur tarifs][lnk-pricing]. Chaque unité IoT concentrateur permet un certain nombre de messages quotidiennes.

Pour redimensionner correctement votre solution, pensez à votre utiliser particulier IoT concentrateur. En particulier, vous pouvez le débit pointe requis pour les catégories des opérations suivantes :

* Messages d’appareil-nuage
* Messages cloud vers le périphérique
* Opérations de Registre identité

Outre ces informations de débit, voir [les quotas de IoT concentrateur et limitations][] et concevoir votre solution en conséquence.

## <a name="device-to-cloud-and-cloud-to-device-message-throughput"></a>Débit des messages appareil-nuage et cloud vers le périphérique

La meilleure façon de dimensionner une solution IoT concentrateur consiste à évaluer le trafic sur une base par unité.

Messages d’appareil-nuage suivent ces instructions de débit.

| Niveau | Débit | Taux d’envoi prolongée |
| ---- | -------------------- | ------------------- |
| S1 | Jusqu'à Ko 1111/minute par unité<br/>(1,5 Go/jour/unit) | Moyenne des messages/minute 278 par unité<br/>(400 000 messages/jour unitaire) |
| S2 | Jusqu'à 16 Mo/minute par unité<br/>(22,8 Go/jour/unit) | Moyenne des messages/minute 4167 par unité<br/>(6 millions messages/jour unitaire) |
| S3 | Jusqu'à Mo 814/minute par unité<br/>(1144.4 Go/jour/unit) | Moyenne des messages/minute 208,333 par unité<br/>(300 millions de messages/jour unitaire) |

## <a name="identity-registry-operation-throughput"></a>Débit d’opération identité Registre

Opérations de Registre IoT concentrateur identité ne sont pas supposés pour être pour l’exécution des opérations, car ils sont principalement liés à la configuration de périphériques.

Pour les nombres de performances rupture spécifiques, voir [limitations et les quotas IoT concentrateur][].

## <a name="sharding"></a>Ont

Pendant un concentrateur IoT unique peut s’adapter à des millions d’appareils, votre solution nécessite parfois caractéristiques de performances spécifiques ne garantit pas un concentrateur IoT unique. Dans ce cas, il est recommandé de partition vos périphériques dans plusieurs IoT hubs. Plusieurs IoT hubs lisses rupture de trafic et obtenir le débit nécessaire ou taux opération requis.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir davantage les fonctionnalités du concentrateur IoT, voir :

- [Guide du développeur][lnk-devguide]
- [Simulation d’un appareil avec le Kit de développement de passerelle IoT][lnk-gateway]

[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[Limitations et les quotas IoT concentrateur]: iot-hub-devguide-quotas-throttling.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
