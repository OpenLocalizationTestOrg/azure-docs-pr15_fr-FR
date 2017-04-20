<properties
 pageTitle="Rubriques du guide pour les développeurs pour concentrateur IoT | Microsoft Azure"
 description="Guide de développeur IoT concentrateur Azure incluant des points de terminaison IoT concentrateur, sécurité, Registre identité de périphérique, gestion des périphériques et la messagerie"
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

# <a name="azure-iot-hub-developer-guide"></a>Guide du développeur IoT concentrateur Azure

Concentrateur IoT Azure est un service entièrement géré qui vous aide à activer la communication bidirectionnelle fiable et sécurisée entre millions d’appareils IoT et mettre fin à une application précédent.

Concentrateur IoT Azure vous offre :

* Sécuriser les communications à l’aide des informations d’identification de sécurité par périphérique et contrôle d’accès.
* APPAREIL-nuage et cloud vers le périphérique hyper-échelle messagerie fiable.
* Connectivité appareil facile avec les bibliothèques appareil pour les langues et les plateformes les plus populaires.

Ce guide de développeur IoT concentrateur inclut les articles suivants :

- [Envoyer et recevoir des messages avec concentrateur IoT] [ devguide-messaging] décrit les fonctionnalités de messagerie (appareil-nuage et cloud vers le périphérique) expose IoT concentrateur. L’article inclut également des informations sur des rubriques telles que les formats de message et les protocoles de communication pris en charge et les numéros de port qu’ils utilisent.
- [Télécharger des fichiers à partir d’un périphérique] [ devguide-upload] décrit comment vous pouvez télécharger des fichiers à partir d’un appareil. L’article inclut également des informations sur des rubriques telles que les notifications que le processus uplaod peut envoyer des messages.
- [Gestion des identités appareil dans IoT Hub] [ devguide-identities] décrit ce qu’il stocke les informations du Registre de du concentrateur de chaque IoT périphérique identité, et comment vous pouvez accéder et le modifier.
- [Contrôler l’accès à un concentrateur IoT] [ devguide-security] décrit le modèle de sécurité utilisé pour accorder l’accès aux fonctionnalités IoT concentrateur pour les deux périphériques et composants le cloud. L’article inclut des informations sur l’utilisation de jetons et des certificats X.509 et des détails des autorisations que vous pouvez accorder.
- [Utiliser jumeaux appareil pour synchroniser l’état et les configurations] [ devguide-device-twins] décrit le concept de *double appareil* et les fonctionnalités qu’elle expose telles que la synchronisation d’un appareil avec son double. L’article inclut des informations sur les données stockées dans un double appareil.
- [Appeler une méthode directe sur un appareil] [ devguide-directmethods] décrit le cycle de vie d’une méthode directe, informations sur la façon d’appeler des méthodes sur un appareil à partir de votre application principale et de gérer la méthode directe sur votre appareil.
- [Planifier des tâches sur plusieurs appareils] [ devguide-jobs] décrit comment vous pouvez planifier des tâches sur plusieurs appareils. L’article explique comment soumettre des travaux d’effectuer des tâches en tant que l’exécution d’une méthode directe, mise à jour un devcie à l’aide d’un double devcie. Il décrit également comment interroger le statut d’une tâche.
- [Référence - points de terminaison IoT concentrateur] [ devguide-endpoints] décrit les différents points de terminaison chaque concentrateur IoT expose pour les opérations de runtime et la gestion. Cet article décrit également comment vous pouvez utiliser une passerelle de champ pour activer certains appareils pour vous connecter à vos points de terminaison IoT concentrateur.
- [Référence - langage de requête pour jumeaux, méthodes et travaux] [ devguide-query] décrit ce langage de requête qui permet d’extraire des informations de votre plateforme sur vos jumeaux appareil et les tâches.
- [Référence - quotas et des limitations] [ devguide-quotas] résume les quotas définis dans le service IoT concentrateur et le comportement de limitation vous pouvez vous attendre à voir lorsque vous dépassez un quota.
- [Référence - périphérique et service SDK] [ devguide-sdks] listes kits de développement logiciel vous permet de développement des applications périphérique et service qui interagissent avec votre concentrateur IoT. L’article inclut des liens vers la documentation en ligne de l’API.
- [Référence - prise en charge IoT concentrateur MQTT] [ devguide-mqtt] fournit des informations détaillées sur comment IoT concentrateur prend en charge le protocole MQTT. L’article décrit la prise en charge pour le protocole MQTT intégré pour les kits de développement et fournit des informations sur l’utilisation du protocole MQTT directement.
- [Glossaire] [ devguide-glossary] une liste des termes courants liés IoT concentrateur.



[devguide-messaging]: iot-hub-devguide-messaging.md
[devguide-upload]: iot-hub-devguide-file-upload.md
[devguide-identities]: iot-hub-devguide-identity-registry.md
[devguide-security]: iot-hub-devguide-security.md
[devguide-device-twins]: iot-hub-devguide-device-twins.md
[devguide-directmethods]: iot-hub-devguide-direct-methods.md
[devguide-jobs]: iot-hub-devguide-jobs.md
[devguide-endpoints]: iot-hub-devguide-endpoints.md
[devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[devguide-query]: iot-hub-devguide-query-language.md
[devguide-sdks]: iot-hub-devguide-sdks.md
[devguide-mqtt]: iot-hub-mqtt-support.md
[devguide-glossary]: iot-hub-devguide-glossary.md

