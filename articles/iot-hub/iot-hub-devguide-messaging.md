<properties
 pageTitle="Guide du développeur - messagerie | Microsoft Azure"
 description="Guide de développeur IoT concentrateur Azure - appareil-nuage et cloud vers le périphérique de messagerie"
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

# <a name="send-and-receive-messages-with-iot-hub"></a>Envoyer et recevoir des messages avec concentrateur IoT

## <a name="overview"></a>Vue d’ensemble

Concentrateur IoT fournit les primitives messageries suivants pour communiquer avec un appareil :

- [APPAREIL-nuage] [ lnk-d2c] principal d’un appareil à l’application.
- [Cloud vers le périphérique] [ lnk-c2d] à partir d’une application back-end (*service* ou sur le *cloud*).

Propriétés principales de fonctionnalités de messagerie IoT concentrateur sont la fiabilité et la longévité des messages. Ces propriétés permettent résilience à connectivité intermittente sur le côté de l’appareil et de charger les pointes de traitement sur le côté de cloud de l’événement. Concentrateur IoT met en œuvre *au moins une fois* les garanties de remise pour la messagerie appareil-nuage et cloud vers le périphérique.

Concentrateur IoT prend en charge plusieurs [protocoles appareil not] [ lnk-protocols] (par exemple, MQTT, AMQP et HTTP). Pour prendre en charge une interopérabilité entre des protocoles, IoT concentrateur définit un [format de message courant] [ lnk-message-format] qui prennent en charge tous les protocoles not appareil.

Concentrateur IoT expose un [point de terminaison événement concentrateur compatible] [ lnk-compatible-endpoint] pour permettre aux applications principale lire les messages de l’appareil-nuage reçus par le hub.

### <a name="when-to-use"></a>Quand utiliser

La messagerie est une fonctionnalité de base de IoT concentrateur. Utilisez-le dès que vous souhaitez envoyer des messages à partir de votre appareil à votre serveur principal ou envoyer des messages à partir de votre serveur principal à un appareil.

Pour une comparaison des services IoT concentrateur et Hubs d’événement, voir [comparaison de IoT concentrateur et événement Hubs][lnk-compare].

## <a name="device-to-cloud-messages"></a>Messages d’appareil-nuage

Vous envoyez des messages de périphérique-nuage via un point de terminaison appareil NOT (**/devices/ {ID de périphérique} / messages/événements**). Votre service principale reçoit des messages de périphérique-nuage via un point de terminaison de service NOT (**/messages/events**) est compatible avec [Hubs événement][lnk-event-hubs]. Par conséquent, vous pouvez utiliser standard [événement Hubs intégration et SDK] [ lnk-compatible-endpoint] pour recevoir des messages de l’appareil-nuage.

Concentrateur IoT mettent en œuvre, de l’appareil-nuage de messagerie de façon similaire à [l’Événement Hubs][lnk-event-hubs]. Messages d’appareil-nuage du concentrateur IoT sont plus comme événement Hubs *événements* que [Service Bus] [ lnk-servicebus] *messages*.

Cette mise en œuvre comporte les conséquences suivantes :

* De même aux événements événement Hubs, messages appareil-nuage sont résistant et conservées dans un concentrateur IoT pendant sept jours (voir [les options de configuration de périphérique-nuage][lnk-d2c-configuration]).
* Messages d’appareil-nuage sont répartis sur un ensemble fixe de partitions qui est défini au moment de la création (voir [les options de configuration de périphérique-nuage][lnk-d2c-configuration]).
* Comme aux événements Hubs, clients lire des messages de l’appareil-nuage doivent gérer partitions et vérification. Voir [Événement Hubs - consommation d’événements][lnk-event-hubs-consuming-events].
* Comme événement Hubs événements, messages appareil-nuage peuvent être au maximum 256 Ko et peuvent être regroupés par lots optimiser envoie. Lots peuvent être au maximum 256 Ko et 500 messages au maximum.

Toutefois, il existe quelques différences importantes entre IoT concentrateur appareil-nuage messagerie et Hubs événement :

* Comme indiqué dans [contrôler l’accès à un concentrateur IoT] [ lnk-devguide-security] section, concentrateur IoT permet par périphérique d’authentification et contrôle d’accès.
* Un concentrateur IoT permet aux millions d’appareils connectés simultanément (voir [les Quotas et la limitation][lnk-quotas]), tandis que l’événement Hubs est limitée aux 5000 connexions AMQP par espace de noms.
* Concentrateur IoT n’autorise pas arbitraire partition à l’aide d’un **PartitionKey**. Messages d’appareil-nuage sont regroupés par leur **ID de périphérique**d' origine.
* Mise à l’échelle IoT concentrateur est légèrement différente de celle mise à l’échelle Hubs événement. Pour plus d’informations, voir [Mise à l’échelle IoT concentrateur][lnk-guidance-scale].

> [AZURE.NOTE] Vous ne pouvez pas remplacer IoT concentrateur pour événement Hubs dans tous les scénarios. Par exemple, dans certains calculs de traitement d’événement, il peut être nécessaire repartitionnement événements par rapport à une autre propriété ou le champ avant d’analyser les flux de données. Dans ce scénario, vous pouvez utiliser un concentrateur de l’événement de dissocier les deux parties du flux pipeline de traitement. Pour plus d’informations, voir *Partitions* dans [Azure Event Hubs Overview][lnk-eventhub-partitions].

Pour plus d’informations sur la façon d’utiliser la messagerie de l’appareil-nuage, consultez [IoT concentrateur API et SDK][lnk-sdks].

> [AZURE.NOTE] Lorsque vous utilisez HTTP pour envoyer des messages de l’appareil-nuage, les noms des propriétés et des valeurs peuvent contenir uniquement des caractères alphanumériques ASCII, plus ``{'!', '#', '$', '%, '&', "'", '*', '*', '+', '-', '.', '^', '_', '`', '|', '~'}``.

### <a name="non-telemetry-traffic"></a>Trafic non télémétrie

Souvent, en plus de points de données de télémétrie, appareils envoient messages et des demandes qui nécessitent l’exécution et la gestion de la couche application métier logique. Par exemple, les alertes critiques qui doivent déclencher la lecture d’une action spécifique dans la partie principale ou réponses appareil aux commandes envoyées à partir de la partie principale.

Pour plus d’informations sur la meilleure façon de traiter ce type de message, consultez la [didacticiel : comment traiter les messages d’appareil-nuage IoT concentrateur] [ lnk-d2c-tutorial] didacticiel.

### <a name="device-to-cloud-configuration-options"></a>Options de configuration de l’appareil-nuage

Un concentrateur IoT expose les propriétés suivantes pour vous permettent de contrôler l’appareil à nuage de messagerie.

* **Nombre de partitions**. Définir cette propriété lors de la création pour définir le nombre de partitions pour réception appareil-nuage événement.
* **Durée de rétention**. Cette propriété spécifie la durée de rétention pour les messages de l’appareil-nuage. La valeur par défaut est un jour, mais elle peut être augmentée pour sept jours.

En outre, comme aux événements Hubs, concentrateur IoT permet de gérer les consommateurs sur le périphérique à nuage recevoir point de terminaison.

Vous pouvez modifier toutes ces propriétés, soit par programme via le [fournisseur de ressources IoT concentrateur API REST][lnk-resource-provider-apis], ou à l’aide du [portail Azure][lnk-management-portal].

### <a name="anti-spoofing-properties"></a>Propriétés de l’usurpation d’identité anti

Pour éviter l’usurpation d’adresse dans les messages de l’appareil-nuage, IoT concentrateur de périphérique cachets de tous les messages avec les propriétés suivantes :

* **ConnectionDeviceId**
* **ConnectionDeviceGenerationId**
* **ConnectionAuthMethod**

Les deux premiers contiennent les **ID de périphérique** et **generationId** du périphérique origine, en fonction de [Propriétés de l’identité appareil][lnk-device-properties].

La propriété **ConnectionAuthMethod** contient un objet JSON sérialisé, avec les propriétés suivantes :

```
{
  "scope": "{ hub | device}",
  "type": "{ symkey | sas}",
  "issuer": "iothub"
}
```

## <a name="cloud-to-device-messages"></a>Messages cloud vers le périphérique

Vous envoyez des messages de cloud vers le périphérique via un point de terminaison du service NOT (**/messages/devicebound**). Un périphérique reçoit les via un point de terminaison spécifique à l’appareil (**/devices/ {ID de périphérique} / messages/devicebound**).

Chaque message cloud vers le périphérique est destiné à un seul périphérique en définissant la propriété **à** **/devices/ {ID de périphérique} / messages/devicebound**.

>[AZURE.IMPORTANT] Chaque file d’attente appareil conserve au maximum 50 messages de cloud vers le périphérique. Essayez d’envoyer des messages plus les mêmes résultats appareil une erreur.

> [AZURE.NOTE] Lorsque vous envoyez des messages de cloud vers le périphérique, les noms des propriétés et des valeurs peuvent contenir uniquement des caractères alphanumériques ASCII, plus ``{'!', '#', '$', '%, '&', "'", '*', '*', '+', '-', '.', '^', '_', '`', '|', '~'}``.

### <a name="message-lifecycle"></a>Cycle de vie de message

Pour garantir au moins une fois la remise des messages, concentrateur IoT persiste messages cloud vers le périphérique dans files d’attente par périphérique. Appareils doivent confirmer explicitement *Saisie semi-automatique* pour IoT concentrateur pour les supprimer de la file d’attente. Cela garantit la résilience contre la connectivité et les périphériques.

Le diagramme suivant illustre le graphique d’état du cycle de vie d’un message cloud vers le périphérique.

![Cycle de vie message cloud vers le périphérique][img-lifecycle]

Lorsque le service envoie un message, il est considéré comme *en file d’attente*. Lorsqu’un appareil souhaite *recevoir* un message, concentrateur IoT *verrous* le message (définit l’état sur **Invisible**) permettre à d’autres threads sur le même appareil à recevoir d’autres messages. Lorsqu’un thread appareil termine le traitement d’un message, il vous avertit IoT concentrateur en *fin de* votre message.

Un périphérique permet également :

- *Refuser* le message, ce qui provoque IoT concentrateur définir l’état **Deadlettered** . Remarque : périphériques qui se connectent avec MQTT ne peut pas rejeter les messages C2D.
- *Abandonner* le message, ce qui provoque IoT concentrateur remettre le message la file d’attente, indiquant l’état de valeur **en file d’attente**.

Un thread peut échouer traiter un message sans avertir IoT concentrateur. Dans ce cas, les messages automatiquement de transition de l’état **Invisible** à l’état **en file d’attente** après un *délai de visibilité (ou verrouiller)*. La valeur par défaut de ce délai d’attente est une minute.

Un message faire la transition entre la **file d’attente** et **invisibles** États pour, au maximum, le nombre de fois spécifié dans la propriété de **remise max count** IoT concentrateur. Après ce nombre de transitions, concentrateur IoT définit l’état du message à **Deadlettered**. De même, concentrateur IoT définit l’état d’un message sur **Deadlettered** après son délai d’expiration (voir [durée de vie][lnk-ttl]).

Pour voir un didacticiel sur les messages de cloud vers le périphérique, [didacticiel : comment envoyer des messages de cloud vers le périphérique avec concentrateur IoT][lnk-c2d-tutorial]. Les rubriques de référence sur la façon dont les différentes API et SDK exposent les fonctionnalités de cloud vers le périphérique, voir [IoT concentrateur API et SDK][lnk-sdks].

> [AZURE.NOTE] En règle générale, les messages cloud vers le périphérique terminer chaque fois que la perte du message n’affecte pas la logique d’application. Par exemple, le contenu du message a été correctement conservé dans le stockage local, ou une opération a été exécutée avec succès. Le message présentant pourrait également des informations temporaires, dont la perte aurait aucun impact sur les fonctionnalités de l’application. Parfois, pour les tâches longue, vous pouvez effectuer le message cloud vers le périphérique après la conservation de la description de la tâche dans le stockage local. Vous pouvez ensuite avertir l’application principale avec un ou plusieurs messages appareil-nuage à différentes étapes de l’avancement de la tâche.

### <a name="message-expiration-time-to-live"></a>Expiration d’un message (durée de vie)

Chaque message cloud vers le périphérique a un délai d’expiration. Cette heure est définie par le service (dans la propriété **ExpiryTimeUtc** ) ou par IoT concentrateur pour la par défaut *durée de vie* spécifiée comme une propriété IoT concentrateur. Voir [les options de configuration Cloud vers le périphérique][lnk-c2d-configuration].

> [AZURE.NOTE] Une pratique courante pour tirer parti d’expiration d’un message et éviter envoyant des messages à appareils déconnectés, consiste à définir peu de temps de vie des valeurs. Cette approche permet d’obtenir le même résultat que la conservation de l’état de connexion périphérique, tout en étant plus efficace. Lorsque vous demandez des accusés de réception de message, concentrateur IoT vous signale les périphériques sont en mesure de recevoir des messages, et quels sont les appareils ne sont pas en ligne ou ont échoué.

### <a name="message-feedback"></a>Commentaires de message

Lorsque vous envoyez un message cloud vers le périphérique, le service peut demander la remise de commentaires par message concernant l’état final de ce message.

- Si vous définissez la propriété **Ack** sur **positif**, IoT concentrateur génère un message de commentaire si et uniquement si le message cloud vers le périphérique a atteint l’état **terminé** .
- Si vous définissez la propriété **Ack** en **négative**, concentrateur IoT génère un message de commentaires, et uniquement si, le message cloud vers le périphérique atteint l’état **Deadlettered** .
- Si vous définissez la propriété **Ack** **complet**, concentrateur IoT génère un message de commentaires dans les deux cas.

> [AZURE.NOTE] Si **Ack** est **complète**, et vous ne recevez un message d’évaluation, cela signifie que le message d’évaluation a expiré. Le service ne peut pas savoir où est passée au message d’origine. Dans la pratique, un service doit s’assurer qu’il peut traiter les commentaires avant son expiration. Le délai d’expiration maximale est deux jours, ce qui permet de suffisamment de temps pour obtenir le service en cours d’exécution à nouveau si une erreur se produit.

Comme indiqué dans les [points de terminaison][lnk-endpoints], concentrateur IoT propose des commentaires via un point de terminaison du service NOT (**/messages/servicebound/feedback**) sous forme de messages. La sémantique pour recevoir des commentaires sont les mêmes que pour les messages de cloud vers le périphérique et ont la même [cycle de vie Message][lnk-lifecycle]. Si possible, commentaires message sont regroupés dans un seul message, au format suivant :

| Propriété | Description |
| -------- | ----------- |
| EnqueuedTime | Horodatage indiquant que lorsque le message a été créé. |
| ID d’utilisateur | `{iot hub name}` |
| ContentType | `application/vnd.microsoft.iothub.feedback.json` |

Le corps est un tableau JSON numéro de série d’enregistrements, chacune avec les propriétés suivantes :

| Propriété | Description |
| -------- | ----------- |
| EnqueuedTimeUtc | Horodatage indiquant lorsque le résultat du message s’est produite. Par exemple, le périphérique est terminé ou le message arrive à expiration. |
| OriginalMessageId | **MessageId** du message cloud vers le périphérique auquel se rapportent ces informations de commentaires. |
| StatusCode | Integer obligatoire. Utilisé dans les messages de commentaires générés par IoT concentrateur. <br/> 0 = succès <br/> 1 = message expiré <br/> 2 = dépassé du nombre maximal de remise <br/> 3 = message rejeté |
| Description | Les valeurs de chaîne pour **StatusCode**. |
| ID de périphérique | **ID de périphérique** du périphérique cible du message cloud vers le périphérique à laquelle applique cet élément de commentaires. |
| DeviceGenerationId | **DeviceGenerationId** du périphérique cible du message cloud vers le périphérique à laquelle applique cet élément de commentaires. |


>[AZURE.IMPORTANT] Le service doit spécifier un **MessageId** du message cloud vers le périphérique pouvoir faire correspondre ses commentaires avec le message d’origine.

L’exemple suivant montre le corps d’un message de commentaires.

```
[
  {
    "OriginalMessageId": "0987654321",
    "EnqueuedTimeUtc": "2015-07-28T16:24:48.789Z",
    "StatusCode": 0
    "Description": "Success",
    "DeviceId": "123",
    "DeviceGenerationId": "abcdefghijklmnopqrstuvwxyz"
  },
  {
    ...
  },
  ...
]
```

### <a name="cloud-to-device-configuration-options"></a>Options de configuration cloud vers le périphérique

Chaque concentrateur IoT expose des options de configuration suivantes pour la messagerie cloud vers le périphérique.

| Propriété | Description | Par défaut et plage |
| -------- | ----------- | ----------------- |
| defaultTtlAsIso8601 | Par défaut TTL pour les messages de cloud vers le périphérique. | Intervalle ISO_8601 jusqu'à 2D (une minute minimale). Par défaut : 1 heure. |
| maxDeliveryCount | Nombre maximal de remise pour files d’attente cloud vers le périphérique par périphérique. | 1 à 100. Par défaut : 10. |
| feedback.ttlAsIso8601 | Rétention pour les messages de service liées aux commentaires. | Intervalle ISO_8601 jusqu'à 2D (une minute minimale). Par défaut : 1 heure. |
| feedback.maxDeliveryCount | Nombre maximal de remise pour file d’attente de commentaires. | 1 à 100. Par défaut : 100. |

Pour plus d’informations, voir [créer IoT hubs][lnk-portal].

## <a name="read-device-to-cloud-messages"></a>Lire les messages de l’appareil-nuage

Concentrateur IoT expose un point de terminaison de vos services principale pour lire les messages appareil-nuage reçus par votre concentrateur. Le point de terminaison est épreuve concentrateur compatible, qui vous permet d’utiliser un des mécanismes Active le service événement Hubs prend en charge pour la lecture des messages.

Lorsque vous utilisez le [Kit de développement Bus de Service Azure pour .NET] [ lnk-servicebus-sdk] ou l' [Événement Hubs - événement processeur hôte][lnk-eventprocessorhost], vous pouvez utiliser les chaînes de connexion IoT concentrateur disposant des autorisations appropriées. Ensuite utiliser **messages/événements** comme le nom de l’événement concentrateur.

Lorsque vous utilisez SDK (ou intégrations produit) qui ne savent pas IoT concentrateur, vous devez récupérer un point de terminaison compatible événement concentrateur et un nom de concentrateur de l’événement compatibles dans les paramètres IoT concentrateur dans le [portail Azure][lnk-management-portal]:

1. Dans la carte de concentrateur IoT, cliquez sur **messagerie**.
2. Dans la section **paramètres de périphérique-nuage** , vous recherchez les valeurs suivantes : **point de terminaison compatible événement concentrateur**, **événement concentrateur compatible avec le nom**et **Partitions**.

    ![Paramètres de périphérique-nuage][img-eventhubcompatible]

> [AZURE.NOTE] Si le Kit de développement requiert une valeur de **nom d’hôte** ou **Namespace** , supprimer le modèle à partir de l' **événement concentrateur compatible avec le point de terminaison**. Par exemple, si votre point de terminaison compatible événement concentrateur est **sb://iothub-ns-myiothub-1234.servicebus.windows.net/**, le **nom d’hôte** serait **iothub-ns-myiothub-1234.servicebus.windows.net**et le **Namespace** serait **iothub-ns-myiothub-1234**.

Vous pouvez ensuite utiliser aucune stratégie de sécurité d’accès partagé qui dispose des autorisations **ServiceConnect** pour vous connecter au concentrateur événement spécifié.

Si vous avez besoin créer une chaîne de connexion événement concentrateur en utilisant les informations précédente, utilisez le modèle suivant :

```
Endpoint={Event Hub-compatible endpoint};SharedAccessKeyName={iot hub policy name};SharedAccessKey={iot hub policy key}
```

Voici une liste des SDK et les intégrations d’entreprise que vous pouvez utiliser avec points de terminaison événement concentrateur compatible IoT concentrateur expose :

* [Client Java événement Hubs](https://github.com/hdinsight/eventhubs-client)
* [Vague d’Apache bec](../hdinsight/hdinsight-storm-develop-csharp-event-hub-topology.md). Vous pouvez afficher le [bec source](https://github.com/apache/storm/tree/master/external/storm-eventhubs) sur GitHub.
* [Intégration d’explosion Apache](../hdinsight/hdinsight-apache-spark-eventhub-streaming.md)

## <a name="reference-topics"></a>Rubriques de référence :

Les rubriques de référence vous fournissent plus d’informations sur l’échange de messages avec IoT concentrateur.

## <a name="message-format"></a>Format des messages

Messages IoT concentrateur se composent :

* Un ensemble de *Propriétés système*. Propriétés IoT concentrateur interprète ou définit. Cet ensemble est prédéterminé.
* Un ensemble de *Propriétés de l’application*. Dictionnaire de propriétés de chaîne que l’application peut définir y accéder, sans avoir à désérialiser le corps du message. Concentrateur IoT modifie jamais ces propriétés.
* Un corps binaire opaque.

Pour plus d’informations sur la façon dont le message est codé dans différents protocoles, voir [IoT concentrateur API et SDK][lnk-sdks].

Le tableau suivant répertorie l’ensemble des propriétés du système dans les messages IoT concentrateur.

| Propriété | Description |
| -------- | ----------- |
| MessageId | Un identificateur définissable par l’utilisateur pour le message, utilisé pour les modèles de requête-réponse. Format : Une chaîne respectant la casse (jusqu'à 128 caractères) de caractères alphanumériques ASCII 7 bits + `{'-', ':',’.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`. |
| Numéro de séquence | Numéro (unique par file d’attente appareil) affecté par IoT concentrateur à chaque message cloud vers le périphérique. |
| À | Une destination spécifiée dans le [Cloud vers le périphérique] [ lnk-c2d] messages. |
| ExpiryTimeUtc | Date et heure d’expiration d’un message. |
| EnqueuedTime | Date et heure, que le message a été reçu par IoT concentrateur. |
| ID de corrélation | Propriété de chaîne dans un message de réponse qui contient généralement l’ID du message de la demande, dans les modèles de requête-réponse. |
| ID d’utilisateur | Un ID permet de spécifier l’origine des messages. Lorsque des messages sont générés par IoT concentrateur, il est défini sur `{iot hub name}`. |
| Accusé de réception | Un générateur de message commentaires. Cette propriété est utilisée dans les messages de cloud vers le périphérique permet de demander IoT concentrateur pour générer des messages de commentaires à la suite de la consommation du message par le périphérique. Valeurs possibles : **Aucune** (par défaut) : aucun message commentaires est généré, **positif**: recevoir un message de commentaire si le message a été effectué, **négatif**: recevoir un message de commentaire si le message a expiré (ou nombre maximal de remise a été atteinte) sans être effectuée par le périphérique, ou **complète**: positifs et négatifs. Pour plus d’informations, consultez [commentaires Message][lnk-feedback]. |
| ConnectionDeviceId | Un ID défini par IoT concentrateur, sur appareil-nuage messages. Il contient l' **ID de périphérique** du périphérique qui a envoyé le message. |
| ConnectionDeviceGenerationId | Un ID défini par IoT concentrateur, sur appareil-nuage messages. Il contient la **generationId** (en fonction de [Propriétés de l’identité appareil][lnk-device-properties]) du périphérique qui a envoyé le message. |
| ConnectionAuthMethod | Méthode d’authentification définie par IoT concentrateur, sur appareil-nuage messages. Cette propriété contient des informations sur la méthode d’authentification utilisée pour authentifier le périphérique envoi du message. Pour plus d’informations, voir [appareil vers le cloud l’usurpation d’identité anti][lnk-antispoofing].|

## <a name="communication-protocols"></a>Protocoles de communication

Un concentrateur IoT permet aux périphériques à utiliser [MQTT][lnk-mqtt], MQTT via, selon toute évidence [AMQP][lnk-amqp], AMQP sur WebSocket et HTTP protocoles pour les communications côté périphérique. Le tableau suivant fournit les recommandations de haut niveau de votre choix du protocole :

| Protocole | Lorsque vous devez choisissez ce protocole |
| -------- | ------------------------------------ |
| MQTT <br> MQTT sur WebSocket     | Utiliser sur tous les appareils mobiles qui ne nécessitent pas à vous connecter plusieurs appareils (chacun avec ses propres informations d’identification par périphérique) sur la même connexion TLS. |
| AMQP <br> AMQP sur WebSocket    | Utiliser sur les passerelles champ et cloud pour tirer parti de connexion multiplexage tous les périphériques. |
| HTTP    | Utiliser pour les appareils qui ne prennent pas en charge les autres protocoles. |

Lorsque vous choisissez le protocole pour les communications côté périphérique, tenez compte des points suivants :

* **Motif de cloud vers le périphérique**. HTTP n’a pas un moyen efficace d’implémenter push server. Dès lors, lorsque vous utilisez HTTP, appareils interrogent IoT concentrateur pour les messages de cloud vers le périphérique. Cette approche est inefficace pour l’appareil et IoT concentrateur. Sous les instructions HTTP en cours, chaque appareil doit vérifier les messages 25 minutes ou plus. En revanche, MQTT et AMQP en charge push server lors de la réception des messages cloud vers le périphérique. Ils activent pousse immédiatement des messages à partir du IoT Hub à l’appareil. Si la latence de remise du problème se pose, MQTT ou AMQP sont les protocoles meilleures à utiliser. Pour les appareils connectés rarement, HTTP fonctionne également.
* **Passerelles de champ**. Lorsque vous utilisez MQTT et HTTP, vous ne puissiez pas connecter plusieurs appareils (chacun avec ses propres informations d’identification par périphérique) à l’aide de la même connexion TLS. Par conséquent, pour les [scénarios de passerelle champ][lnk-azure-gateway-guidance], ces protocoles sont non optimaux parce qu’ils nécessitent une connexion TLS entre la passerelle de champ et IoT concentrateur pour chaque appareil connectée à la passerelle de champ.
* **Appareils ressource faible**. Les bibliothèques MQTT et HTTP ont un encombrement que les bibliothèques AMQP. Dès lors, si l’appareil est limitée des ressources (par exemple, inférieur à 1 Mo de RAM), ces protocoles peuvent être la seule implémentation de protocole disponible.
* **Parcours réseau**. Le protocole AMQP standard utilise le port 5671, tandis que MQTT écoute sur le port 8883, ce qui peut entraîner des problèmes dans les réseaux qui sont fermées pour les protocoles non-HTTP. MQTT sur WebSocket, AMQP sur WebSocket et HTTP sont disponibles pour être utilisés dans ce scénario.
* **Taille de la charge**. MQTT et AMQP sont protocoles binaires entraînant charges plus compacts que HTTP.

> [AZURE.NOTE] Lorsque vous utilisez HTTP, chaque appareil doit vérifier les messages cloud vers le périphérique 25 minutes ou plus. Toutefois, pendant le développement, il est acceptable d’interrogation plus fréquemment que toutes les minutes 25.

## <a name="port-numbers"></a>Numéros de port

Appareils peuvent communiquer avec concentrateur IoT dans Azure à l’aide de divers protocoles. En règle générale, le choix du protocole est piloté par les exigences spécifiques de la solution. Le tableau suivant répertorie les ports sortants qui doivent être ouverts pour un appareil afin de pouvoir utiliser un protocole spécifique :

| Protocole | Ports |
| -------- | ------- |
| MQTT     | 8883    |
| MQTT sur WebSocket | 443    |
| AMQP     | 5671    |
| AMQP sur WebSocket | 443    |
| HTTP     | 443     |
| LWM2M (gestion des périphériques) | 5684 |

Une fois que vous avez créé un concentrateur IoT dans une zone Azure, le hub conserve la même adresse IP pendant la durée de vie de ce concentrateur. Toutefois, pour conserver la qualité de service, si Microsoft déplace le hub IoT vers une unité d’échelle différents puis elle reçoit une nouvelle adresse IP.

## <a name="notes-on-mqtt-support"></a>Remarques sur la prise en charge MQTT

Concentrateur IoT met en œuvre le protocole v3.1.1 MQTT avec les limitations suivantes et le comportement spécifique :

  * **2 de qualité de service n’est pas pris en charge**. Lorsqu’un client d’appareil publie un message avec **2 de qualité de service**, concentrateur IoT ferme la connexion réseau. Lorsqu’un client de périphérique permet de s’abonner à un sujet avec **2 de qualité de service**, IoT concentrateur accorde niveau de qualité de service maximal 1 dans le paquet **SUBACK** .
  * **Conserver les messages ne sont pas conservées**. Si un client d’appareil publie un message avec l’indicateur de conserver la valeur 1, concentrateur IoT ajoute le **x-opter-conserver** propriété application au message. Dans ce cas, IoT concentrateur n’est pas conservé le message conserver, mais il passe à la place à l’application principale.

Pour plus d’informations, voir [IoT concentrateur MQTT prend en charge][lnk-devguide-mqtt].

En termes de final, vous devez examiner la [passerelle de protocole Azure IoT] [ lnk-azure-protocol-gateway] qui vous permet de déployer une passerelle de protocole personnalisé High performance interface directement avec IoT concentrateur. La passerelle de protocole Azure IoT vous permet de personnaliser le périphérique pour prendre en compte les déploiements MQTT brownfield ou les autres protocoles personnalisés. Cette approche nécessite-t-il, cependant, que vous exécutez et employer une passerelle protocole personnalisé.

## <a name="additional-reference-material"></a>Documents de référence supplémentaires

Autres rubriques de référence dans le Guide du développeur sont les suivantes :

- [Points de terminaison IoT concentrateur] [ lnk-endpoints] décrit les différents points de terminaison chaque concentrateur IoT expose pour les opérations de runtime et la gestion.
- [Limitation et les quotas de] [ lnk-quotas] décrit les quotas qui s’appliquent au service IoT concentrateur et le comportement de limitation s’attendre lorsque vous utilisez le service.
- [Concentrateur IoT périphérique et service SDK] [ lnk-sdks] répertorie le langage diverses SDK vous une utilisation lorsque vous développez périphérique et service applications qui interagissent avec IoT concentrateur.
- [Langage de requête pour jumeaux, méthodes et travaux] [ lnk-query] décrit le langage de requête que vous pouvez utiliser pour récupérer des informations à partir du IoT Hub sur votre jumeaux appareil, les méthodes et les tâches.
- [Prise en charge IoT concentrateur MQTT] [ lnk-devguide-mqtt] donne des informations supplémentaires sur la prise en charge IoT concentrateur pour le protocole MQTT.

## <a name="next-steps"></a>Étapes suivantes

À présent que vous avez appris à envoyer et recevoir des messages avec IoT concentrateur, il est possible que vous intéresser dans les rubriques suivantes Guide du développeur :

- [Télécharger des fichiers à partir d’un appareil][lnk-devguide-upload]
- [Gestion des identités appareil dans IoT Hub][lnk-devguide-identities]
- [Contrôler l’accès à IoT concentrateur][lnk-devguide-security]
- [Jumeaux de périphérique permet de synchroniser les configurations et état][lnk-devguide-device-twins]
- [Appeler une méthode directe sur un appareil][lnk-devguide-directmethods]
- [Planifier des tâches sur plusieurs appareils][lnk-devguide-jobs]

Si vous voulez essayer certaines des concepts décrits dans cet article, il est possible que vous intéresser les didacticiels IoT concentrateur suivants :

- [Prise en main Azure IoT concentrateur][lnk-getstarted-tutorial]
- [Comment envoyer des messages de cloud vers le périphérique avec concentrateur IoT][lnk-c2d-tutorial]
- [Comment traiter les messages d’appareil-nuage IoT concentrateur][lnk-d2c-tutorial]


[img-lifecycle]: ./media/iot-hub-devguide-messaging/lifecycle.png
[img-eventhubcompatible]: ./media/iot-hub-devguide-messaging/eventhubcompatible.png

[lnk-resource-provider-apis]: https://msdn.microsoft.com/library/mt548492.aspx
[lnk-azure-gateway-guidance]: iot-hub-devguide-endpoints.md#field-gateways
[lnk-guidance-scale]: iot-hub-scaling.md
[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md
[lnk-amqp]: http://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf
[lnk-mqtt]: http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/mqtt-v3.1.1.pdf
[lnk-event-hubs]: http://azure.microsoft.com/documentation/services/event-hubs/
[lnk-event-hubs-consuming-events]: ../event-hubs/event-hubs-programming-guide.md#event-consumers
[lnk-management-portal]: https://portal.azure.com
[lnk-servicebus]: http://azure.microsoft.com/documentation/services/service-bus/
[lnk-eventhub-partitions]: ../event-hubs/event-hubs-overview.md#partitions
[lnk-portal]: iot-hub-create-through-portal.md

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-c2d]: iot-hub-devguide-messaging.md#cloud-to-device-messages
[lnk-compatible-endpoint]: iot-hub-devguide-messaging.md#read-device-to-cloud-messages
[lnk-protocols]: iot-hub-devguide-messaging.md#communication-protocols
[lnk-message-format]: iot-hub-devguide-messaging.md#message-format
[lnk-d2c-configuration]: iot-hub-devguide-messaging.md#device-to-cloud-configuration-options
[lnk-device-properties]: iot-hub-devguide-identity-registry.md#device-identity-properties
[lnk-ttl]: iot-hub-devguide-messaging.md#message-expiration-time-to-live
[lnk-c2d-configuration]: iot-hub-devguide-messaging.md#cloud-to-device-configuration-options
[lnk-lifecycle]: iot-hub-devguide-messaging.md#message-lifecycle
[lnk-feedback]: iot-hub-devguide-messaging.md#message-feedback
[lnk-antispoofing]: iot-hub-devguide-messaging.md#anti-spoofing-properties
[lnk-compare]: iot-hub-compare-event-hubs.md

[lnk-devguide-upload]: iot-hub-devguide-file-upload.md
[lnk-devguide-identities]: iot-hub-devguide-identity-registry.md
[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-devguide-device-twins]: iot-hub-devguide-device-twins.md
[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-servicebus-sdk]: https://www.nuget.org/packages/WindowsAzure.ServiceBus
[lnk-eventprocessorhost]: http://blogs.msdn.com/b/servicebus/archive/2015/01/16/event-processor-host-best-practices-part-1.aspx


[lnk-getstarted-tutorial]: iot-hub-csharp-csharp-getstarted.md
[lnk-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md
[lnk-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
