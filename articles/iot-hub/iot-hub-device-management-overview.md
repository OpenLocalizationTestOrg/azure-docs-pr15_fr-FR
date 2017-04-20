<properties
 pageTitle="Vue d’ensemble de gestion de périphérique concentrateur IoT | Microsoft Azure"
 description="Cet article fournit une vue d’ensemble de la gestion des appareils dans Azure IoT Hub : cycle de vie entreprise appareil, redémarrez, usine par défaut, mise à jour, configuration, jumeaux appareil, requêtes, travaux"
 services="iot-hub"
 documentationCenter=""
 authors="bzurcher"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="10/03/2016"
 ms.author="bzurcher"/>

# <a name="overview-of-azure-iot-hub-device-management-preview"></a>Vue d’ensemble de la gestion des périphériques Azure IoT concentrateur (preview)

## <a name="introduction"></a>Introduction

Concentrateur IoT Azure fournit les fonctionnalités et un modèle d’extensibilité qui permettent aux périphériques et principale aux développeurs de créer des solutions de gestion des périphériques IoT robustes. IoT appareils comprises entre capteurs limitées et Microcontrôleurs usage unique, et des passerelles puissantes qui router les communications pour les groupes d’appareils.  En outre, le cas d’utilisation et la configuration requise pour les opérateurs IoT varie considérablement secteurs.  Malgré cette variation, gestion des périphériques Azure IoT concentrateur fournit les fonctionnalités, les modèles et les bibliothèques de code pour répondre à un ensemble centralisée de périphériques et utilisateurs finaux.

Une partie essentielle de création d’une solution IoT permettent de fournir une stratégie pour comment opérateurs gérer la gestion en continu de leur collection des périphériques d’entreprise réussie. Opérateurs IoT nécessitent outils simples et fiables et applications leur permettant de se concentrer sur les aspects de leur travail plus stratégiques. Cet article fournit :

- Un bref aperçu des approche Azure IoT concentrateur pour la gestion des appareils.
- Une description de principes de gestion des périphériques communs.
- Description du cycle de vie appareil.
- Vue d’ensemble des modèles de gestion des périphériques courants.

## <a name="iot-device-management-principles"></a>Principes de gestion des périphériques IoT

IoT implique un ensemble unique de problèmes de gestion des périphériques et chaque solution d’entreprise devez prendre en compte les principes suivants :

![Azure IoT concentrateur device management principes un graphique][img-dm_principles]

- **Échelle et automatisation**: IoT solutions nécessitent des outils simples qui peuvent automatiser les tâches courantes et activer un peu au personnel de gérer des millions d’appareils. Au quotidien, les opérateurs estiment gérer les opérations d’appareil à distance, en bloc et uniquement être prévenu lorsqu’un problème survient qui nécessitent leur attention directe.

- **Ouverture et compatibilité**: réseau de périphérique IoT The est extrêmement centralisée. Outils de gestion doivent être adaptés pour s’adapter à une multitude de classes de périphériques et protocoles plateformes. Opérateurs doivent être en mesure de prendre en charge de nombreux types d’appareils, à partir des contraintes plus puces processus unique incorporés, sur des ordinateurs puissants et entièrement fonctionnelles.

- **Sensibilisation de contexte**: IoT environnements sont dynamiques et évolution. Fiabilité de service est essentielle. Opérations de gestion des périphériques doivent prendre en compte les fenêtres de maintenance, États réseau et power, conditions en cours d’utilisation et appareil géolocalisation pour garantir ces temps d’arrêt de maintenance n’affecte les opérations critiques ou créez des conditions dangereuses SLA.

- **Service de nombreux rôles**: prise en charge pour les flux de travail uniques et les processus des rôles d’opérations IoT est crucial. Le personnel d’exploitation doit fonctionnent en harmonie avec les contraintes de départements internes donnés.  Ils doivent également découvrir durable façons d’informations sur les opérations d’appareil en surface en temps réel à superviseurs et autres rôles d’encadrement entreprise.

## <a name="iot-device-lifecycle"></a>Cycle de vie IoT appareil

Il existe un ensemble d’étapes de gestion des périphériques générales qui sont communes à tous les projets IoT. Dans Azure IoT, il existe cinq étapes dans le cycle de vie IoT appareil :

![Les cinq phases de cycle de vie d’appareil IoT Azure : planifier, mise en service, configurer, surveiller, retraite][img-device_lifecycle]

Dans chacune de ces cinq étapes, il existe plusieurs conditions d’opérateur appareil qui doivent être remplies pour fournir une solution complète :

- **Planifier**: activer les opérateurs créer un modèle de métadonnées appareil qui permet de leur facilement et précisément les requêtes et cibler un groupe de périphériques pour les opérations de gestion en bloc. Vous pouvez utiliser le double appareil pour stocker les métadonnées de cet appareil dans le formulaire des balises et des propriétés.

    *Lectures supplémentaires*: [prise en main jumeaux appareil][lnk-twins-getstarted], [jumeaux d’appareil comprendre][lnk-twins-devguide], [l’utilisation de propriétés double][lnk-twin-properties]

- **Mise en service**: configurer de nouveaux appareils à IoT concentrateur et activer les opérateurs découvrir les fonctionnalités de l’appareil immédiatement en toute sécurité.  Le Registre de périphérique concentrateur IoT permet de créer des informations d’identification et identités des appareils flexibles et effectuer cette opération en bloc à l’aide d’une tâche. Créer des périphériques pour signaler leurs fonctions et conditions via les propriétés de périphérique dans le double de l’appareil.

    *Lectures supplémentaires*: [Gérer les identités appareil][lnk-identity-registry], [gestion d’en bloc des identités appareil][lnk-bulk-identity], [l’utilisation de propriétés double][lnk-twin-properties]

- **Configurer**: faciliter la configuration des modifications en bloc et mises à jour aux périphériques tout en conservant la santé et la sécurité. Effectuer ces opérations de gestion des périphériques en bloc à l’aide des propriétés de votre choix ou méthodes directes et les travaux de diffusion.

    *Lectures supplémentaires*: [utiliser les méthodes directes][lnk-c2d-methods], [appeler une méthode directe sur un appareil][lnk-methods-devguide], [comment utiliser propriétés double][lnk-twin-properties], [calendrier et tâches de diffusion][lnk-jobs], [planifier des tâches sur plusieurs appareils][lnk-jobs-devguide]

- **Moniteur**: contrôler l’intégrité de collection de sites appareil globale, l’état des opérations en cours, et avertir les utilisateurs pour des problèmes peuvent nécessiter leur attention.  Appliquer le double appareil pour autoriser les périphériques à signaler les conditions d’exploitation en temps réel et l’état des opérations de mise à jour. Générer des rapports de tableau de bord puissantes qui les problèmes plus immédiats de surface à l’aide de requêtes de double appareil.

    *Lectures supplémentaires*: [comment utiliser propriétés double][lnk-twin-properties], [langage de requête pour jumeaux et les tâches][lnk-query-language]

- **Déclassement**: remplacer ou retirer des appareils après une défaillance, mise à niveau de cycle, ou à la fin de la durée de vie du service.  Le double de périphérique permet de tenir à jour les informations du périphérique si le périphérique physique est en cours de remplacement ou archivé si vous souhaitez déclassé. Utilisez le Registre de périphérique IoT concentrateur de révocation en toute sécurité des informations d’identification et identités des appareils.

    *Lectures supplémentaires*: [comment utiliser propriétés double][lnk-twin-properties], [Gérer les identités appareil][lnk-identity-registry]

## <a name="iot-hub-device-management-patterns"></a>Modèles de gestion des périphériques IoT concentrateur

IoT Hub autorise l’ensemble des modèles de gestion des périphériques suivantes.  Les [didacticiels de gestion des périphériques] [ lnk-get-started] expliquent plus en détail comment étendre ces modèles pour l’ajuster à votre scénario précis et pour la création de nouveaux modèles basés sur ces modèles de base.

- **Redémarrer** - l’application principale vous informe le périphérique via une méthode directe qu’il a démarré un redémarrage.  Le périphérique utilise le périphérique double signalée propriétés pour mettre à jour l’état de redémarrage de l’appareil.

    ![Gestion des périphériques IoT concentrateur Azure redémarrer graphique motif][img-reboot_pattern]

- **Réinitialiser usine** - l’application principale vous informe le périphérique via une méthode directe qu’il a initié une réinitialisation factory.  Le périphérique utilise la double appareil signalées propriétés pour mettre à jour l’usine rétablir le statut du périphérique.

    ![Entreprise de gestion des périphériques IoT concentrateur Azure rétablir le motif graphique][img-facreset_pattern]

- **Configuration** - l’application principale utilise les propriétés du périphérique double souhaité pour configurer les logiciels en cours d’exécution sur l’appareil.  Le périphérique utilise le périphérique double signalée propriétés pour mettre à jour d’état de la configuration du périphérique.

    ![Graphique de modèle d’Azure IoT concentrateur appareil gestion configuration][img-config_pattern]

- **Mettre à jour les microprogramme** - l’application principale vous informe le périphérique via une méthode directe qu’il a initié une mise à jour.  Le périphérique établit un processus en plusieurs étapes pour télécharger le package microprogramme, appliquez le package microprogramme et enfin vous reconnecter au service IoT concentrateur.  Dans le processus en plusieurs étapes, le périphérique utilise le périphérique double signalés propriétés pour mettre à jour l’avancement et le statut du périphérique.

    ![Azure microprogramme de gestion du périphérique concentrateur IoT jour motif graphique][img-fwupdate_pattern]

- **Signaler la progression et état** - l’application principale s’exécute des requêtes appareil double, un ensemble de périphériques, générer des rapports sur l’état et la progression des actions en cours d’exécution sur les appareils.

    ![Gestion des périphériques IoT concentrateur Azure graphique motif avancement et le statut de création de rapports][img-report_progress_pattern]

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez utiliser les fonctionnalités, les motifs et les bibliothèques de code qui fournit de gestion des périphériques Azure IoT concentrateur, pour créer des applications IoT qui remplissent les exigences entreprise IoT opérateur dans chaque étape du cycle de vie appareil.

Pour poursuivre l’apprentissage sur les fonctionnalités de gestion de périphérique Azure IoT concentrateur, voir la [mise en route de la gestion des périphériques Azure IoT concentrateur] [ lnk-get-started] didacticiel.

<!-- Images and links -->
[img-dm_principles]: media/iot-hub-device-management-overview/image4.png
[img-device_lifecycle]: media/iot-hub-device-management-overview/image5.png
[img-config_pattern]: media/iot-hub-device-management-overview/configuration-pattern.png
[img-facreset_pattern]: media/iot-hub-device-management-overview/facreset-pattern.png
[img-fwupdate_pattern]: media/iot-hub-device-management-overview/fwupdate-pattern.png
[img-reboot_pattern]: media/iot-hub-device-management-overview/reboot-pattern.png
[img-report_progress_pattern]: media/iot-hub-device-management-overview/report-progress-pattern.png

[lnk-twins-devguide]: iot-hub-devguide-device-twins.md
[lnk-get-started]: iot-hub-device-management-get-started.md
[lnk-twins-getstarted]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-properties]: iot-hub-node-node-twin-how-to-configure.md
[lnk-hub-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-identity-registry]: iot-hub-devguide-identity-registry.md
[lnk-bulk-identity]: iot-hub-bulk-identity-mgmt.md
[lnk-query-language]: iot-hub-devguide-query-language.md
[lnk-c2d-methods]: iot-hub-c2d-methods.md
[lnk-methods-devguide]: iot-hub-devguide-direct-methods.md
[lnk-jobs]: iot-hub-schedule-jobs.md
[lnk-jobs-devguide]: iot-hub-devguide-jobs.md