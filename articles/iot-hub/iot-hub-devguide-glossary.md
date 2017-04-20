<properties
 pageTitle="Guide du développeur - glossaire | Microsoft Azure"
 description="Un glossaire des termes courants relatifs à IoT concentrateur"
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

# <a name="glossary-of-iot-hub-terms"></a>Glossaire des termes IoT concentrateur

Cet article répertorie certains des termes courants associés aux IoT concentrateur.

## <a name="advanced-message-queueing-protocol-amqp"></a>Message avancés file d’attente Protocol (AMQP)

[AMQP](https://www.amqp.org/) figure parmi les protocoles de messagerie qui prend en charge IoT concentrateur pour communiquer avec des appareils mobiles. Pour plus d’informations sur les protocoles de messagerie qui prend en charge IoT concentrateur, voir [Envoyer et recevoir des messages avec IoT concentrateur](iot-hub-devguide-messaging.md).

## <a name="cloud-to-device-c2d"></a>Cloud appareil (C2D)

Généralement utilisé pour faire référence aux messages envoyés à partir du IoT Hub à un appareil connecté. Ces messages sont souvent, les commandes qui indiquent le périphérique pour exécuter une action. Pour plus d’informations, voir [Envoyer et recevoir des messages avec IoT concentrateur](iot-hub-devguide-messaging.md).

## <a name="condition"></a>Condition

Fait référence à des informations d’état de périphérique, telles que la méthode de connectivité en cours d’utilisation, comme indiqué par une application d’appareil. Appareils peuvent également signaler leurs capacités. Vous pouvez interroger une condition et une fonctionnalité à l’aide de la double appareil.

## <a name="data-point-message"></a>Message de point de données

Un message de point de données est un message cloud vers le périphérique qui contient les données de télémétrie comme vent ou la température.

## <a name="desired-properties"></a>Propriétés de votre choix

Dans le contexte de jumeaux appareil, propriétés souhaitées servent conjointement avec *signalés propriétés* à synchroniser condition ou configuration du périphérique. Propriétés de votre choix ne peuvent être définies par l’application revenir fin et sont respectées par l’application d’appareil. 

## <a name="device-to-cloud-d2c"></a>APPAREIL-nuage (D2C)

Généralement utilisé pour faire référence aux messages envoyés à partir d’un appareil connecté à IoT concentrateur. Ces messages peuvent être messages [interactifs](#interactive-message) ou [point de données](#data-point-message) . Pour plus d’informations, voir [Envoyer et recevoir des messages avec IoT concentrateur](iot-hub-devguide-messaging.md).

## <a name="device-identity-registry"></a>Registre d’identité appareil

Le [Registre d’identité périphérique](iot-hub-devguide-identity-registry.md) est le composant intégré d’un concentrateur IoT qui stocke des informations sur les périphériques individuels autorisés à se connecter à un concentrateur.

## <a name="device"></a>APPAREIL

Dans le contexte de IoT, un périphérique est généralement un périphérique informatique autonome petite taille, qui peut-être collecter des données ou un contrôle d’autres appareils. Par exemple, un appareil peut être un dispositif de surveillance de l’environnement ou d’un contrôleur pour les systèmes abreuvement et ventilation dans une serre.

## <a name="device-twin"></a>Double appareil

Un [double appareil](iot-hub-devguide-device-twins.md) est une copie dans IoT Hub les paramètres d’une condition et une configuration d’un périphérique physique. Vous pouvez utiliser un double appareil pour gérer la configuration du périphérique physique.

## <a name="direct-method"></a>Méthode directe

Une [méthode directe](iot-hub-devguide-direct-methods.md) est un moyen vous permettant de déclencher la lecture d’une méthode d’exécuter sur un appareil en appelant une API sur votre concentrateur IoT.

## <a name="event-hub-compatible-endpoint"></a>Point de terminaison événement concentrateur compatible

Pour lire les messages appareil-nuage envoyés à votre concentrateur IoT, vous pouvez vous connecter à un point de terminaison sur votre concentrateur et utiliser n’importe quelle méthode événement concentrateur compatible à lire ces messages. Méthodes de concentrateur compatible d’événements incluent à l’aide de l’événement Hubs SDK et Azure flux Analytique.

## <a name="field-gateway"></a>Passerelle de champ

Une passerelle champ permet la connectivité pour les périphériques qui ne peut pas se connecter directement au concentrateur IoT et est généralement déployé localement avec vos appareils. Pour plus d’informations, voir [que constitue Azure IoT ?](iot-hub-what-is-iot-hub.md).

## <a name="job"></a>Tâche

Votre serveur principal de solution pouvez utiliser les travaux pour planifier et suivre les activités sur un ensemble de vos périphériques sont enregistrés avec votre concentrateur IoT. Activités incluent la mise à jour des propriétés de l’appareil double vous le souhaitez, mise à jour des balises double pour appareil mobile et l’appel des méthodes directes.

## <a name="protocol-gateway"></a>Passerelle de protocole

Une passerelle de protocole est généralement déployée dans le cloud et fournit des services de traduction pour périphériques qui se connectent à concentrateur IoT protocole. Pour plus d’informations, voir [que constitue Azure IoT ?](iot-hub-what-is-iot-hub.md).

## <a name="interactive-message"></a>Message interactif

Un message interactif est un nuage vers le périphérique déclenchant une action immédiate dans l’application principale. Par exemple, un appareil peut envoyer une alerte concernant une défaillance qui doit être enregistrée automatiquement dans un système CRM.

## <a name="iot-hub"></a>Concentrateur IoT

Concentrateur IoT est un service Azure entièrement géré qui permet de bidirectionnelle fiable et sécuriser les communications entre millions d’appareils IoT et principale solution. Pour plus d’informations, voir [que constitue Azure IoT ?](iot-hub-what-is-iot-hub.md).

## <a name="iot-suite"></a>IoT Suite

Azure IoT Suite packages ensemble plusieurs services Azure grâce aux solutions préconfigurées. Ces solutions préconfigurées permettent de prise en main rapidement mises en œuvre de bout en bout des scénarios IoT courants. Pour plus d’informations, voir [Nouveautés Azure IoT Suite ?](../iot-suite/iot-suite-overview.md).

## <a name="job"></a>Tâche

Permet à une [tâche](iot-hub-devguide-jobs.md) dans IoT Hub vous permet d’effectuer les opérations, telles qu’un microprogramme mise à niveau sur plusieurs appareils connectés à votre concentrateur.

## <a name="mqtt"></a>MQTT

[MQTT](http://mqtt.org/) figure parmi les protocoles de messagerie qui prend en charge IoT concentrateur pour communiquer avec des appareils mobiles. Pour plus d’informations sur les protocoles de messagerie qui prend en charge IoT concentrateur, voir [Envoyer et recevoir des messages avec IoT concentrateur](iot-hub-devguide-messaging.md).

## <a name="reported-properties"></a>Propriétés signalées

Dans le contexte de jumeaux appareil, propriétés signalées sont utilisées conjointement avec les *propriétés souhaitées* pour la synchronisation de condition ou configuration du périphérique. Propriétés signalées ne peut être définies par l’application de l’appareil et peuvent être lus et interrogées par l’application principale.

## <a name="tags"></a>Balises

Dans le contexte de devcie jumeaux, balises sont appareil métadonnées stockés et récupérés par l’application principale sous la forme d’un document JSON. Les balises ne sont pas visibles aux applications sur un appareil.

## <a name="system-properties"></a>Propriétés du système

Dans le contexte de jumeaux appareil, propriétés système sont en lecture seule et incluent des informations concernant l’utilisation du périphérique comme dernier état de connexion et d’heure activité.