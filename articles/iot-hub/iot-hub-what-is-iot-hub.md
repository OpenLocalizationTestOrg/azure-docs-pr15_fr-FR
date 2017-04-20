<properties
 pageTitle="Vue d’ensemble du concentrateur IoT Azure | Microsoft Azure"
 description="Vue d’ensemble du service Azure IoT concentrateur : qu’est iot concentrateur, la connectivité de périphérique, internet de modèles de communication choses et modèle de communication assisté de service"
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
 ms.date="08/25/2016"
 ms.author="dobett"/>

# <a name="what-is-azure-iot-hub"></a>Qu’est Azure IoT concentrateur ?

Bienvenue dans Azure IoT concentrateur. Cet article fournit une vue d’ensemble du concentrateur de IoT Azure et explique pourquoi vous devez utiliser ce service pour mettre en œuvre une solution Internet des objets (IoT). Concentrateur IoT Azure est un service entièrement géré qui permet de bidirectionnelle fiable et sécuriser les communications entre millions d’appareils IoT et principale solution. Concentrateur IoT Azure :

- Fournit fiable appareil-nuage et messagerie à l’échelle de cloud pour appareil.
- Permet de sécuriser les communications à l’aide des informations d’identification de sécurité par périphérique et contrôle d’accès.
- Fournit une surveillance étendue pour la connectivité de l’appareil et événements de gestion des identités appareil.
- Inclut des bibliothèques d’appareil pour les langues et les plateformes les plus populaires.

L’article [comparaison de IoT concentrateur et événement Hubs] [ lnk-compare] décrit les principales différences entre ces deux services et présente les avantages de l’utilisation d’IoT concentrateur dans vos solutions IoT.

![Concentrateur IoT Azure comme passerelle cloud dans internet de solution d’éléments][img-architecture]

> [AZURE.NOTE] Pour une présentation détaillée de IoT architecture, voir l' [Architecture de référence Microsoft Azure IoT][lnk-refarch].

## <a name="iot-device-connectivity-challenges"></a>Problèmes de connectivité des périphériques IoT

Concentrateur IoT et les bibliothèques appareil vous aident à résoudre les problèmes de la fiabilité et en toute sécurité connexion appareils à principaux solution. IoT appareils :

- Sont souvent des systèmes intégrés avec aucun opérateur humaine.
- Peut être distants, où l’accès physique est coûteux.
- Peut uniquement être accessibles via les principaux solution.
- Peut avoir limité power et ressources de traitement.
- Peut-être la connectivité réseau intermittents, lentes ou coûteuses.
- Devrez peut-être utiliser les protocoles d’application spécifiques au secteur, propriétaires ou personnalisé.
- Peut être créé à l’aide d’un large éventail de plateformes matérielle et logicielle populaires.

Outre les conditions ci-dessus, n’importe quelle solution IoT doit également fournir échelle, la sécurité et la fiabilité. Le jeu d’exigences de connectivité est long et complexe mettre en œuvre lors de l’utilisation de technologies d’assistance traditionnels, comme conteneurs web et messageries agents.

## <a name="why-use-azure-iot-hub"></a>Pourquoi utiliser Azure IoT concentrateur

Concentrateur IoT Azure résout les problèmes de connectivité des périphériques des façons suivantes :

-   **Authentification par périphérique et la connectivité sécurisée**. Vous pouvez configurer chaque périphérique avec sa propre [clé de sécurité] [ lnk-devguide-security] pour l’activer pour vous connecter à IoT concentrateur. Le [Registre d’identité IoT concentrateur] [ lnk-devguide-identityregistry] stocke les identités des appareils et les touches dans une solution. Un serveur principal de solution peut ajouter des périphériques individuels pour autoriser ou refuser des listes pour activer totalement accès des appareils.

-   **Surveillance des opérations de connectivité d’appareil**. Vous pouvez recevoir des journaux d’opération détaillées sur les opérations de gestion des périphériques identité et événements de connectivité d’appareil. Cette fonctionnalité de surveillance permet à votre solution IoT identifier les problèmes de connectivité, telles que les appareils que vous essaient de vous connecter avec les informations d’identification incorrectes, envoyer des messages trop fréquemment, ou rejeter tous les messages de cloud vers le périphérique.

-   **Un ensemble étendu de bibliothèques d’appareil**. [APPAREIL IoT Azure SDK] [ lnk-device-sdks] sont disponibles et pris en charge pour différentes langues et plateformes--C pour plusieurs versions de Linux, Windows et les systèmes d’exploitation en temps réel. Azure IoT appareil SDK prennent également en charge les langues gérées, tels que c#, Java et JavaScript.

-   **Extensibilité et IoT protocoles**. Si votre solution ne peut pas utiliser les bibliothèques de périphérique, concentrateur IoT expose un protocole public qui permet aux périphériques à utiliser en mode natif le MQTT v3.1.1, HTTP 1.1 ou AMQP 1.0 protocoles. Vous pouvez également étendre IoT concentrateur pour prendre en charge les protocoles personnalisés par :

    - Création d’une passerelle de champ avec le [Kit de développement logiciel Azure IoT passerelle] [ lnk-gateway-sdk] qui convertit votre protocole personnalisé à un des trois protocoles comprises par IoT concentrateur. 
    - Personnalisation de la [passerelle de protocole Azure IoT][protocol-gateway], un composant source ouverte qui s’exécute dans le cloud.

-   **Échelle**. Concentrateur IoT Azure s’adapte à millions d’appareils connectés simultanément et des millions d’événements par seconde.

Les avantages sont génériques à nombreux modèles de communication. Concentrateur IoT actuellement vous permet de mettre en œuvre des modèles de communication spécifiques suivants :

-   **Réception d’appareil-nuage basé sur des événements.** Concentrateur IoT peut recevoir fiable millions d’événements par seconde à partir de vos appareils. Il peut ensuite les traiter sur votre chemin d’accès rapide à l’aide d’un moteur de processeur événement. Il peut également les stocker sur votre chemin à froid pour l’analyse. Concentrateur IoT conserve les données d’événement pendant sept jours pour garantir le traitement fiable et retirer des pointes lors du chargement.

-   * *Messagerie cloud vers le périphérique fiable (ou *commandes*). ** principaux solution peut utiliser IoT concentrateur pour envoyer des messages avec un programme de garantie remise au moins une des équipements individuels. Chaque message comporte un paramètre de durée de vie individuel, et le serveur principal peut demander des confirmations de remise et d’expiration. Ces confirmations assurer une grande visibilité sur le cycle de vie d’un message cloud vers le périphérique. Vous pouvez ensuite implémenter logique métier qui inclut des opérations qui s’exécutent sur les appareils.

-   **Télécharger des fichiers et des données mises en cache capteur dans le cloud.** Vos périphériques peuvent télécharger des fichiers sur le stockage Azure à l’aide de sa MU géré par IoT concentrateur. Concentrateur IoT peut générer des notifications lors de l’arrivent des fichiers dans le cloud pour activer le serveur principal à les traiter.

## <a name="gateways"></a>Passerelles

Une passerelle dans une solution IoT est généralement une [passerelle de protocole] [ lnk-gateway] qui est déployé dans le nuage ou d’une [passerelle de champ] [ lnk-field-gateway] qui est déployé localement avec vos appareils. Une passerelle protocole effectue une traduction protocol, par exemple MQTT à AMQP. Une passerelle champ peut exécuter analytique sur le bord, prendre des décisions urgents pour réduire la latence, fournissent des services de gestion des périphériques, appliquer la sécurité et confidentialité contraintes et également effectuer la conversion de protocole. Les deux types de passerelle agissent comme intermédiaires entre vos périphériques et votre concentrateur IoT.

Une passerelle champ diffère un périphérique de routage le trafic simple (par exemple, un périphérique de traduction d’adresses réseau ou un pare-feu), car il exécute généralement un rôle actif dans la gestion des flux d’accès et des informations dans votre solution.

Une solution peut inclure les passerelles protocole et de champ.

## <a name="how-does-iot-hub-work"></a>Comment fonctionne le IoT Hub ?

Concentrateur IoT Azure mettent en œuvre, la [communication assisté service] [ lnk-service-assisted-pattern] motif organisée servant d’intermédiaire les interactions entre vos périphériques et votre solution back end. L’objectif de communication assisté service est d’établir digne de confiance, chemins d’accès de communication bidirectionnelle entre un système de contrôle, tels que IoT concentrateur et appareils spéciaux déployée dans non approuvé espace physique. Le modèle établit les principes suivants :

- Sécurité est prioritaire sur toutes les autres fonctionnalités.
- Appareils n’acceptent pas les informations du réseau indésirables. Un périphérique établit toutes les connexions et des itinéraires de manière sortant uniquement. Pour un périphérique de recevoir une commande à partir de la partie principale, le périphérique doit régulièrement établir une connexion pour vérifier les commandes en attente à traiter.
- Appareils doivent se connecter à ou établir des itinéraires à des services connus qu’ils sont ressources avec, comme concentrateur IoT uniquement.
- Le chemin d’accès de communication entre l’appareil et le service ou entre les périphériques et de passerelles est sécurisé à la couche de protocole d’application.
- Authentification et au niveau du système d’autorisation sont basées sur les identités non gérées par périphérique. Ils permettent d’informations d’accès et les autorisations presque instantanément révocable.
- Une communication bidirectionnelle pour appareils mobiles qui se connectent sporadiquement en raison de connectivité ou de power problèmes est facilitée en maintenant les commandes et les notifications de périphérique jusqu'à ce qu’un périphérique se connecte pour recevoir les. Concentrateur IoT conserve les files d’attente spécifique à l’appareil pour les commandes qu'il envoie.
- Données de charge utile application sont sécurisées séparément pour protégé lors des transferts via des passerelles à un service particulier.

Le secteur mobile a utilisé le modèle de communication assisté service à l’échelle considérable d’implémenter des services de notification push tels que des [Services de Notification Push Windows][lnk-wns], [Google Cloud messagerie][lnk-google-messaging]et le [Service de Notification Push Apple][lnk-apple-push].

Concentrateur IoT est pris en charge sur le chemin d’accès homologation du ExpressRoute public.

## <a name="next-steps"></a>Étapes suivantes

Pour découvrir comment Azure IoT Hub autorise la gestion des périphériques normalisée IoT vous permettant de gérer à distance, configurer et mettre à jour vos appareils, voir [Gestion des périphériques vue d’ensemble de Azure IoT concentrateur][lnk-device-management].

Pour mettre en œuvre des applications clientes sur un large éventail de systèmes d’exploitation et plates-formes matérielles appareil, vous pouvez utiliser l’appareil IoT SDK. Le périphérique IoT SDK inclure des bibliothèques qui facilitent l’envoi de télémétrie à un concentrateur IoT et reçoivent les commandes de cloud vers le périphérique. Lorsque vous utilisez le SDK, vous pouvez choisir de divers protocoles réseau pour communiquer avec IoT concentrateur. Pour plus d’informations, voir les [informations relatives à l’appareil SDK][lnk-device-sdks].

Pour commencer à écrire du code et l’exécution des exemples, voir la [prise en main IoT concentrateur] [ lnk-get-started] didacticiel.

[img-architecture]: media/iot-hub-what-is-iot-hub/hubarchitecture.png


[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[protocol-gateway]: https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md
[lnk-service-assisted-pattern]: http://blogs.msdn.com/b/clemensv/archive/2014/02/10/service-assisted-communication-for-connected-devices.aspx "Service assistée Communication, billet de blog par Clemens Vasters"
[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-gateway]: iot-hub-protocol-gateway.md
[lnk-field-gateway]: iot-hub-devguide-endpoints.md#field-gateways
[lnk-devguide-identityregistry]: iot-hub-devguide-identity-registry.md
[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-wns]: https://msdn.microsoft.com/library/windows/apps/mt187203.aspx
[lnk-google-messaging]: https://developers.google.com/cloud-messaging/
[lnk-apple-push]: https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW9
[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk
[lnk-device-management]: iot-hub-device-management-overview.md
