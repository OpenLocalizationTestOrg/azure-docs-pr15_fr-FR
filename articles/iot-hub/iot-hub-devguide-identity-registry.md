<properties
 pageTitle="Guide du développeur - Registre d’identité périphérique | Microsoft Azure"
 description="Guide du développeur IoT concentrateur Azure - description du périphérique identité Registre et comment l’utiliser pour gérer vos périphériques"
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

# <a name="manage-device-identities-in-iot-hub"></a>Gestion des identités appareil dans IoT Hub

## <a name="overview"></a>Vue d’ensemble

Chaque concentrateur IoT comporte un Registre d’identité appareil qui stocke des informations sur les appareils qui sont autorisés à se connecter au concentrateur. Avant d’un périphérique peut se connecter à un concentrateur, doit comporter une entrée pour cet appareil dans le Registre d’identité périphérique du concentrateur. Un périphérique doit également s’authentifier avec le hub en fonction des informations d’identification stockées dans le Registre identité appareil.

À un niveau élevé, le Registre identité appareil est un ensemble reste compatibles avec des ressources d’identité appareil. Lorsque vous ajoutez une entrée dans le Registre, IoT concentrateur crée un ensemble de ressources par périphérique dans le service tel que la file d’attente contenant les messages cloud vers le périphérique en cours d’exécution.

### <a name="when-to-use"></a>Quand utiliser

Utilisez le Registre identité appareil lorsque vous avez besoin de configurer les périphériques qui se connectent à vous IoT concentrateur et lorsque vous avez besoin contrôler l’accès par périphérique aux points de terminaison appareil NOT dans votre concentrateur.

> [AZURE.NOTE] Le Registre identité appareil ne contient pas toutes les métadonnées spécifiques à l’application.

## <a name="device-identity-registry-operations"></a>Opérations de Registre appareil identité

Le Registre IoT concentrateur appareil identité expose les opérations suivantes :

* Création d’identité de l’appareil
* Mettre à jour appareil identité
* Récupérer l’identité appareil par ID
* Supprimer l’identité de l’appareil
* Liste jusqu'à 1000 identités
* Exporter toutes les identités à Azure blob storage
* Importer des identités depuis le stockage blob Azure

Toutes ces opérations peuvent utiliser simultané, comme indiqué dans [RFC7232][lnk-rfc7232].

> [AZURE.IMPORTANT] La seule façon de récupérer toutes les identités du Registre d’un concentrateur identité consiste à utiliser l' [exportation] [ lnk-export] fonctionnalité.

Un registre d’identité IoT concentrateur appareil :

- Ne contient pas les métadonnées de l’application.
- Sont accessibles à un dictionnaire, à l’aide de l' **ID de périphérique** comme clé.
- Ne prend pas en charge les requêtes expressifs.

Une solution IoT comporte généralement une banque spécifique à la solution distincte qui contient des métadonnées spécifiques à l’application. Par exemple, le magasin de solution spécifiques dans une solution de construction actives doit enregistrer la salle dans laquelle une sonde de température est déployé.

> [AZURE.IMPORTANT] Utiliser uniquement le Registre identité appareil pour la gestion des périphériques et la mise en service des opérations. Opérations de haut débit en cours d’exécution ne doivent pas dépendre d’opérations dans le Registre identité appareil. Par exemple, la vérification de l’état de connexion d’un périphérique avant l’envoi d’une commande n’est pas un modèle pris en charge. N’oubliez pas de vérifier la [limitation de taux] [ lnk-quotas] pour le Registre d’identité de périphérique et la [pulsation appareil] [ lnk-guidance-heartbeat] motif.

## <a name="disable-devices"></a>Désactiver des périphériques

Vous pouvez désactiver les périphériques en mettant à jour la propriété de **l’état** d’une identité dans le Registre. En règle générale, vous utilisez cette propriété dans deux scénarios :

- Pendant le processus d’orchestration mise en service. Pour plus d’informations, voir [Configuration du périphérique][lnk-guidance-provisioning].
- Si, pour une raison quelconque, vous envisagez d’un appareil est compromis ou est devenu non autorisé.

## <a name="import-and-export-device-identities"></a>Importer et exporter des identités des appareils

Vous pouvez exporter des identités appareil en bloc à partir du Registre d’identité d’un concentrateur IoT, à l’aide des opérations asynchrones sur [point de terminaison fournisseur IoT concentrateur ressource][lnk-endpoints]. Exportations sont des travaux longue un conteneur blob fourni par le client permet d’enregistrer les données d’identité appareil lire à partir du Registre identité.

Vous pouvez importer des identités appareil en bloc dans le Registre d’identité d’un concentrateur IoT, à l’aide des opérations asynchrones sur [point de terminaison fournisseur IoT concentrateur ressource][lnk-endpoints]. Importe des fichiers sont des travaux longue qui utilisent des données dans un conteneur blob fourni par le client pour écrire des données d’identité de périphérique dans le Registre identité appareil.

- Pour plus d’informations sur l’importation et exportation des API, voir [fournisseur de ressources IoT concentrateur API REST][lnk-resource-provider-apis].
- Pour en savoir plus sur l’exécution d’importer et exporter des travaux, voir [gestion en bloc des identités des appareils IoT concentrateur][lnk-bulk-identity].

## <a name="device-provisioning"></a>La configuration de périphériques

Les données de l’appareil qui stocke une solution IoT donnée dépendant des besoins spécifiques de cette solution. Mais, au minimum, une solution doit stocker clés d’authentification et les identités des appareils. Concentrateur IoT Azure inclut un registre d’identité qui peut stocker les valeurs pour chaque périphérique tels qu’ID, les clés d’authentification et les codes d’état. Une solution peut utiliser d’autres services comme stockage de table Azure, le stockage d’objets blob Azure ou DocumentDB Azure Azure pour stocker des données d’un périphérique supplémentaire.

*Mise en service de l’appareil* est la procédure d’ajout de données initiale d’un appareil aux magasins de votre solution. Pour activer un nouvel appareil pour vous connecter à votre concentrateur, vous devez ajouter un nouvel appareil ID et clés dans le Registre identité IoT concentrateur. Dans le cadre du processus de mise à disponibilité, vous devrez peut-être initialisation des données dans d’autres banques solution.

## <a name="device-heartbeat"></a>Pulsation appareil

Le Registre identité IoT concentrateur contient un champ intitulé **connectionState**. Vous ne devez utiliser le champ **connectionState** pendant le développement et débogage, IoT solutions ne doivent pas interroger le champ en cours d’exécution (par exemple, pour vérifier si un périphérique est connecté afin de décider si vous souhaitez envoyer un message cloud vers le périphérique ou un SMS).

Si votre solution IoT a besoin de savoir si un périphérique est connecté (en cours d’exécution, ou avec plus de précision que celle offerte par la propriété **connectionState** ), votre solution doit implémenter le *motif pulsation*.

Dans le modèle pulsation, le périphérique envoie des messages de l’appareil-nuage au moins une fois chaque unité de temps (par exemple, au moins une fois par heure) fixe. Cela signifie que même si un appareil n’a pas de données à envoyer, il envoie toujours un message appareil-nuage vide (généralement avec une propriété qui identifie comme une pulsation). Sur le côté de service, la solution met à jour une carte avec la dernière pulsation reçue pour chaque appareil et suppose qu’il existe un problème avec un périphérique si elle ne reçoit pas un message de pulsation pendant la durée prévue.

Une implémentation plus complexe peut inclure les informations des [opérations de surveillance] [ lnk-devguide-opmon] pour identifier les périphériques qui sont, essayez de vous connecter ou communiquer mais ignore. Lorsque vous implémentez le modèle pulsation, veillez à vérifier [les Quotas IoT concentrateur et limitations][lnk-quotas].

> [AZURE.NOTE] Si une solution IoT doit l’état de connexion appareil uniquement pour déterminer s’il faut envoyer des messages de cloud vers le périphérique, et les messages ne sont pas diffusées sur les grands ensembles de périphériques, un motif beaucoup plus simple à prendre en considération consiste à utiliser un court délai d’expiration. Cela permet d’obtenir le même résultat que la conservation d’un connexion état du Registre du périphérique en utilisant le modèle pulsation, tout en étant beaucoup plus efficace. Il est également possible, en demandant à être informé par IoT Hub de quels sont les appareils sont en mesure de recevoir des messages et qui ne sont pas en ligne ou n’ont pu être les accusés de réception message.

## <a name="reference-topics"></a>Rubriques de référence :

Les rubriques de référence vous fournissent plus d’informations sur le Registre identité devcie.

## <a name="device-identity-properties"></a>Propriétés de l’identité appareil

Identités appareil sont représentées sous forme de documents JSON avec les propriétés suivantes.

| Propriété | Options | Description |
| -------- | ------- | ----------- |
| ID de périphérique | mises à jour requises, en lecture seule sur | Une chaîne qui respecte la casse (jusqu'à 128 caractères) de caractères alphanumériques ASCII 7 bits + `{'-', ':', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`. |
| generationId | obligatoire, en lecture seule | Une chaîne de minuscules, générée concentrateur 128 caractères au maximum. Il est utilisé pour distinguer les appareils avec la même **ID de périphérique**, lorsqu’ils ont été supprimés et recréés. |
| eTag | obligatoire, en lecture seule | Chaîne représentant un faible etag pour l’identité du périphérique, conformément aux [RFC7232][lnk-rfc7232].|
| AUTH | facultatif | Objet composite contenant des éléments de sécurité et les informations d’authentification. |
| AUTH.symkey | facultatif | Un objet composite contenant un serveur principal et une clé secondaire, stocké au format en base 64. |
| état | Obligatoire | Un indicateur d’accès. Peut être **activé** ou **désactivé**. Si **activé**, l’appareil est autorisé à se connecter. Si **désactivé**, cet appareil ne peut pas accéder à un point de terminaison not appareil. |
| statusReason | facultatif | Une chaîne de caractères de longueur 128 qui stocke la raison de l’état de l’identité appareil. Tous les caractères UTF-8 sont autorisées. |
| statusUpdateTime | en lecture seule | Un indicateur temporel, montrant la date et l’heure de la dernière mise à jour d’état. |
| connectionState | en lecture seule | Un champ indiquant l’état de connexion : **connecté** ou **déconnecté**. Ce champ représente la vue IoT concentrateur de l’état de connexion du périphérique. **Important**: ce champ doit être utilisé uniquement à des fins de développement/débogage. L’état de connexion est mis à jour uniquement pour les périphériques à l’aide de MQTT ou AMQP. En outre, il est basé sur les requêtes au niveau du protocole Ping (ping MQTT ou AMQP ping), et elle peut avoir un délai maximum de 5 minutes seulement. Pour ces raisons, il peut être fausses, telles que les périphériques ont signalé comme étant connecté mais qui sont réellement déconnectés. |
| connectionStateUpdatedTime | en lecture seule | Un indicateur temporel, affichant la date et l’heure de la dernière l’état de connexion a été mis à jour. |
| lastActivityTime  | en lecture seule | Un indicateur temporel, affichant la date et l’heure de la dernière l’appareil connecté, reçu ou envoyé un message. |

> [AZURE.NOTE] État de connexion peut représenter uniquement la vue IoT concentrateur de l’état de la connexion. Mises à jour à cet état peuvent être retardées, en fonction des configurations et des conditions réseau.

## <a name="additional-reference-material"></a>Documents de référence supplémentaires

Autres rubriques de référence dans le Guide du développeur sont les suivantes :

- [Points de terminaison IoT concentrateur] [ lnk-endpoints] décrit les différents points de terminaison chaque concentrateur IoT expose pour les opérations de runtime et la gestion.
- [Limitation et les quotas de] [ lnk-quotas] décrit les quotas qui s’appliquent au service IoT concentrateur et le comportement de limitation s’attendre lorsque vous utilisez le service.
- [Concentrateur IoT périphérique et service SDK] [ lnk-sdks] répertorie le langage diverses SDK vous une utilisation lorsque vous développez périphérique et service applications qui interagissent avec IoT concentrateur.
- [Langage de requête pour jumeaux, méthodes et travaux] [ lnk-query] décrit le langage de requête que vous pouvez utiliser pour récupérer des informations à partir du IoT Hub sur votre jumeaux appareil, les méthodes et les tâches.
- [Prise en charge IoT concentrateur MQTT] [ lnk-devguide-mqtt] donne des informations supplémentaires sur la prise en charge IoT concentrateur pour le protocole MQTT.

## <a name="next-steps"></a>Étapes suivantes

À présent que vous avez appris à utiliser le Registre IoT concentrateur appareil identité, il est possible que vous intéresser dans les rubriques suivantes Guide du développeur :

- [Contrôler l’accès à IoT concentrateur][lnk-devguide-security]
- [Jumeaux de périphérique permet de synchroniser les configurations et état][lnk-devguide-device-twins]
- [Appeler une méthode directe sur un appareil][lnk-devguide-directmethods]
- [Planifier des tâches sur plusieurs appareils][lnk-devguide-jobs]

Si vous voulez essayer certaines des concepts décrits dans cet article, il est possible que vous intéresser le didacticiel IoT concentrateur suivant :

- [Prise en main Azure IoT concentrateur][lnk-getstarted-tutorial]


<!-- Links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-resource-provider-apis]: https://msdn.microsoft.com/library/mt548492.aspx
[lnk-guidance-provisioning]: iot-hub-devguide-identity-registry.md#device-provisioning
[lnk-guidance-heartbeat]: iot-hub-devguide-identity-registry.md#device-heartbeat
[lnk-rfc7232]: https://tools.ietf.org/html/rfc7232
[lnk-bulk-identity]: iot-hub-bulk-identity-mgmt.md
[lnk-export]: iot-hub-devguide-identity-registry.md#import-and-export-device-identities
[lnk-devguide-opmon]: iot-hub-operations-monitoring.md

[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-devguide-device-twins]: iot-hub-devguide-device-twins.md
[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md

[lnk-getstarted-tutorial]: iot-hub-csharp-csharp-getstarted.md