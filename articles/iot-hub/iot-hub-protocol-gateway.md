<properties
   pageTitle="Passerelle de protocole IoT Azure | Microsoft Azure"
   description="Décrit comment utiliser Azure IoT protocole passerelle pour étendre les fonctionnalités et la prise en charge du protocole d’Azure IoT concentrateur."
   services="iot-hub"
   documentationCenter=""
   authors="kdotchkoff"
   manager="timlt"
   editor=""/>

<tags
   ms.service="iot-hub"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/23/2016"
   ms.author="kdotchko"/>

# <a name="supporting-additional-protocols-for-iot-hub"></a>Prise en charge les protocoles supplémentaires pour IoT concentrateur

Concentrateur IoT Azure prise en charge native communication via les protocoles MQTT, AMQP et HTTP. Dans certains cas, appareils ou des passerelles de champ ne peuvent pas être en mesure d’utiliser un de ces protocoles standards et nécessitent adaptation protocol. Dans ce cas, vous pouvez utiliser une passerelle personnalisée. Une passerelle personnalisée permettent à adaptation de protocole pour les points de terminaison IoT concentrateur par le trafic vers et depuis IoT concentrateur de transition. Vous pouvez utiliser la [passerelle de protocole Azure IoT](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md) comme une passerelle personnalisée pour activer adaptation protocol pour IoT concentrateur.

## <a name="azure-iot-protocol-gateway"></a>Passerelle de protocole IoT Azure

La passerelle de protocole IoT Azure est un cadre pour adaptation de protocole est conçu pour grande échelle, la communication bidirectionnelle appareil avec IoT concentrateur. La passerelle de protocole est un composant SQL direct qui accepte les connexions du périphérique via un protocole spécifique. Il relie le trafic vers IoT concentrateur par rapport à 1.0 AMQP. La passerelle de protocole IoT Azure est disponible en tant qu’un projet de logiciels libres offre une grande souplesse pour l’ajout de prise en charge pour divers protocoles et les versions de protocole.

Vous pouvez déployer la passerelle protocole dans Azure de façon hautement scalable en utilisant des Services Cloud Azure collaborateur rôles. En outre, la passerelle de protocole peut être déployée dans les environnements locaux, tels que des passerelles de champ.

La passerelle de protocole IoT Azure inclut une carte du protocole MQTT qui vous permet de personnaliser le comportement du protocole MQTT si nécessaire. Dans la mesure où IoT concentrateur prend en charge le protocole v3.1.1 MQTT, uniquement envisagez d’utiliser la carte du protocole MQTT si vous avez besoin pour les personnalisations de protocole ou configuration spécifiques pour des fonctionnalités supplémentaires.

La carte MQTT montre également le modèle de programmation pour la création de cartes de protocole pour les autres protocoles. En outre, le modèle de programmation Azure IoT protocole passerelle vous permet de connecter des composants personnalisés pour un traitement spécialisé comme l’authentification personnalisée, transformations de message, compression/décompression ou chiffrement/déchiffrement du trafic entre les appareils mobiles et IoT concentrateur.

Pour une flexibilité, la passerelle de protocole et l’implémentation MQTT sont fournis dans un projet de logiciels libres. Cela vous permet de personnaliser la mise en œuvre selon vos besoins.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la passerelle de protocole IoT Azure et apprendre à utiliser et à déployer dans le cadre de votre solution IoT, voir :

* [Azure IoT protocole passerelle référentiel GitHub](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md)
* [Guide du développeur de passerelle du protocole IoT Azure](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/docs/DeveloperGuide.md)

Pour plus d’informations sur la planification de votre déploiement IoT concentrateur, voir :

- [Comparer avec Hubs d’événement][lnk-compare]
- [Mise à l’échelle, HA et DR][lnk-scaling]

Pour en savoir davantage les fonctionnalités du concentrateur IoT, voir :

- [Guide du développeur][lnk-devguide]
- [Simulation d’un appareil avec le Kit de développement de passerelle IoT][lnk-gateway]

[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
