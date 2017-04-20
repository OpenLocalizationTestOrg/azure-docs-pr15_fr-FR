> [AZURE.SELECTOR]
- [Node.js](../articles/iot-hub/iot-hub-node-node-twin-getstarted.md)
- [C#](../articles/iot-hub/iot-hub-csharp-node-twin-getstarted.md)

## <a name="introduction"></a>Introduction

Jumeaux appareil est des documents JSON qui stockent les informations d’état de périphérique (métadonnées, les configurations et conditions). Concentrateur IoT persiste un double appareil pour chaque appareil auxquels vous vous connectez à IoT concentrateur.

Utilisez jumeaux appareil à :

* Stocker les métadonnées appareil à partir de votre serveur principal.
* Rapport informations sur l’état actuel telles que les fonctionnalités disponibles et conditions (par exemple, la méthode connectivité utilisée) à partir de votre application d’appareil.
* Synchroniser l’état du flux de travail longue (par exemple, des mises à jour de microprogramme et de configuration) entre application appareil et serveur principal.
* Interroger votre appareil métadonnées, configuration ou état.

> [AZURE.NOTE] Jumeaux appareil est conçues pour la synchronisation et pour interroger des conditions et leur configuration. Utiliser les [messages d’appareil-nuage] [ lnk-d2c] pour les séquences d’événements horodatés (par exemple, des flux de données de télémétrie de données temporelles) et les [méthodes de cloud vers le périphérique] [ lnk-methods] pour un contrôle interactif d’appareils, tels que la mise sous tension un ventilateurs à partir d’une application contrôlée par l’utilisateur.

Jumeaux appareil est stockés dans un concentrateur IoT et contenir :

* *balises*, appareil métadonnées accessibles uniquement par le serveur principal ;
* *Propriétés de votre choix*, objets JSON modifiables par le système principal et peuvent être observés par l’application d’appareil ; et
* mettre fin à *Propriétés signalées*, objets JSON modifiables par l’application de l’appareil et lisibles par l’arrière. Balises et des propriétés ne peut pas contenir de tableaux, mais peuvent être imbriquées les objets.

![][img-twin]

En outre, la partie principale de l’application peut interroger jumeaux appareil basé sur toutes les données ci-dessus.
Reportez-vous à [jumeaux d’appareil comprendre] [ lnk-twins] pour plus d’informations sur jumeaux appareil et pour le [langage de requête IoT concentrateur] [ lnk-query] référence pour l’interrogation.

> [AZURE.NOTE] Pour l’instant, jumeaux appareil est accessibles uniquement à partir d’appareils qui se connectent à concentrateur IoT en utilisant le protocole MQTT. Reportez-vous à la [prise en charge MQTT] [ lnk-devguide-mqtt] article pour obtenir des instructions sur la conversion d’application appareil existante à utiliser MQTT.

Ce didacticiel vous explique comment procéder pour :

- Créer une application principale qui ajoute des *balises* à un double périphérique et un périphérique simulé qui indique son canal de connectivité comme un *signalés propriété* sur le double de l’appareil.
- Interroger des équipements à partir de votre application serveur principal à l’aide de filtres sur les balises et les propriétés précédemment créées.


<!-- images -->
[img-twin]: media/iot-hub-selector-twin-get-started/twin.png

<!-- links -->
[lnk-query]: ../articles/iot-hub/iot-hub-devguide-query-language.md
[lnk-twins]: ../articles/iot-hub/iot-hub-devguide-device-twins.md
[lnk-d2c]: ../articles/iot-hub/iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
[lnk-devguide-mqtt]: ../articles/iot-hub/iot-hub-mqtt-support.md