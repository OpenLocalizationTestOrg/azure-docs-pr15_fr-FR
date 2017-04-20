<properties
 pageTitle="Guide du développeur - méthodes directes | Microsoft Azure"
 description="Guide du développeur IoT concentrateur Azure - utilisez les méthodes directe pour appeler le code sur vos appareils"
 services="iot-hub"
 documentationCenter=".net"
 authors="nberdy"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/30/2016" 
 ms.author="nberdy"/>

# <a name="invoke-a-direct-method-on-a-device-preview"></a>Appeler une méthode directe sur un appareil (preview)

## <a name="overview"></a>Vue d’ensemble

Concentrateur IoT vous donne la possibilité d’appeler des méthodes sur les appareils à partir du cloud. Méthodes de représentent une interaction demande-réponse avec un appareil similaire à un appel HTTP dans la mesure où ils réussissent ou échouent immédiatement (après un délai d’attente défini par l’utilisateur) afin d’indiquer l’état de l’appel de l’utilisateur. Ceci est utile pour des scénarios au cours d’une action immédiate diffèrent selon que l’appareil n’a pas pu répondre, tels qu’envoyer une SMS réactivation à un périphérique si le périphérique est en mode hors connexion (SMS en cours supérieurs à un appel de méthode).

Vous pouvez considérer une méthode comme un appel de procédure distante directement à l’appareil. Seules les méthodes qui ont été implémentées sur un appareil peuvent être appelés à partir du cloud. Si le nuage tente d’appeler une méthode sur un appareil qui n’a pas cette méthode définie, l’appel de méthode échoue.

Chaque méthode appareil cible un périphérique unique. [Tâches] [ lnk-devguide-jobs] offrent un moyen pour appeler des méthodes sur plusieurs appareils et appel de la méthode pour les appareils déconnectés en file d’attente.

Toute personne disposant d’autorisations de **service de se connecter** sur IoT concentrateur peut-être appeler une méthode sur un appareil.

### <a name="when-to-use"></a>Quand utiliser

Méthodes de périphérique sont semblables aux [messages cloud vers le périphérique] [ lnk-devguide-messages] dans la mesure où les deux activer la principale cloud passer des informations sur un périphérique, mais ils sont fondamentalement différents. Point de vue conceptuel, méthodes sont synchrone et pas résistant, tandis que messages cloud vers le périphérique sont asynchrones avec jusqu'à 48 heures de durabilité.

Méthodes suivent un modèle de requête-réponse et ne sont pas résistants. L’absence de durabilité fournit deux avantages immédiates lors de l’exécution de commandes appareils :

- **Retour immédiat sur exécution de la méthode** indique qu’il n’est pas nécessaire pour vous permet de gérer les relations entre les demande et réponse.
- **Débit** signifie que les opérations peuvent être effectuées plus rapidement, car IoT concentrateur ne fournit pas de n’importe quel durabilité. Concentrateur IoT permet aux autres appels de méthode unitaire que les messages de cloud vers le périphérique.

Messages cloud vers le périphérique ne sont pas nécessairement des commandes à l’appareil, mais préférez représentent un service cloud passant certains bits des informations à l’appareil pour qu’elle décrochez à sa convenance, et à l’appareil qui peut ou peut ne pas répond. Messages de cloud vers le périphérique ont un plus long délai d’attente (jusqu'à 48 heures) alors que les méthodes expirent beaucoup plus rapidement.

Utiliser des méthodes de périphérique pour appel de commande immédiate sur un appareil et les tâches pour un appel planifiée d’une commande sur un appareil.

## <a name="method-lifecycle"></a>Méthode de cycle de vie

Méthodes sont implémentées sur l’appareil et peuvent nécessiter zéro, un ou plusieurs entrées dans la charge utile de méthode d’instanciation correctement. Vous appelez une méthode directe via un URI service NOT (`{iot hub}/twins/{device id}/methods/`). Un périphérique reçoit méthodes directes via une rubrique MQTT spécifique à l’appareil (`$iothub/methods/POST/{method name}/`). Nous pouvons prennent en charge méthodes sur les protocoles réseau côté périphérique supplémentaires à l’avenir.

> [AZURE.NOTE] Lorsque vous appelez une méthode directe sur un appareil, les noms et valeurs peuvent contenir uniquement US-ASCII imprimable alphanumériques, à l’exception dans le jeu suivant : ``{'$', '(', ')', '<', '>', '@', ',', ';', ':', '\', '"', '/', '[', ']', '?', '=', '{', '}', SP, HT}``.

Méthodes sont synchrones et soit réussir ou échouer après un délai (par défaut : 30 secondes, définissables vers le haut pour 3 600 secondes). Méthodes sont utiles dans les scénarios interactifs dans laquelle vous souhaitez qu’un périphérique pour agir uniquement si le périphérique est en ligne et de réception commandes, comme une lumière à partir d’un téléphone. Dans ce cas, vous souhaitez voir un immédiate réussite ou l’échec afin que le service en nuage peut agir dès que possible sur le résultat. L’appareil peut retourner certains corps du message comme résultat de la méthode, mais elle n’est pas nécessaire pour la méthode pour le faire. Il existe aucune garantie sur la commande ou n’importe quel sémantique concurrence sur appels de méthode.

Appels de méthode appareil sont HTTP uniquement à partir du bord de cloud et MQTT uniquement à partir du bord de l’appareil.

## <a name="reference-topics"></a>Rubriques de référence :

Les rubriques de référence vous fournissent plus d’informations sur l’utilisation de méthodes directes.

## <a name="invoke-a-direct-method-from-a-back-end-app"></a>Appeler une méthode directement à partir d’une application principale

### <a name="method-invocation"></a>Appel de la méthode

Appels de méthode direct sur un appareil sont appels HTTP qui comprennent :

- *URI* spécifique à l’appareil (`{iot hub}/twins/{device id}/methods/`)
- La *méthode* de billet
- *En-têtes* contenant l’autorisation, demander ID, type de contenu et codage du contenu
- Un JSON transparent *corps* au format suivant :

```
{
    "methodName": "reboot",
    "timeoutInSeconds": 200,
    "payload": {
        "input1": "someInput",
        "input2": "anotherInput"
    }
}
```

  Délai d’expiration est en secondes. Si le délai d’expiration n’est pas définie, la valeur par défaut est 30 secondes.
  
### <a name="response"></a>Réponse

Le serveur principal reçoit une réponse qui comprend :

- *Code d’état HTTP*, qui est utilisé pour les erreurs entrant à partir du IoT Hub, y compris une erreur 404 pour les appareils connectés n’est pas actuellement
- *En-têtes* contenant l’etag, demander ID, type de contenu et codage du contenu
- Un JSON *corps* au format suivant :

```
{
    "status" : 201,
    "payload" : {...}
}
```
  
   Les deux `status` et `body` sont fournies par l’appareil et utilisées pour répondre par le code d’état de l’appareil et/ou la description.

## <a name="handle-a-direct-method-on-a-devcie"></a>Gérer une méthode directement sur un devcie

### <a name="method-invocation"></a>Appel de la méthode

Appareils recevoir des demandes de méthode directe dans la rubrique MQTT :`$iothub/methods/POST/{method name}/?$rid={request id}`

Le corps qui reçoit l’appareil est au format suivant :

```
{
    "input1": "someInput",
    "input2": "anotherInput"
}
```

Requêtes de méthodes sont qualité de service 0.

### <a name="response"></a>Réponse

Le périphérique envoie des réponses aux `$iothub/methods/res/{status}/?$rid={request id}`, où :

 - La `status` propriété est l’état d’exécution de la méthode fournie par appareil.
 - La `$rid` propriété est l’ID de demande à partir de l’appel de méthode reçu IoT concentrateur.

Le corps est défini par l’appareil et peut être n’importe quel état.

## <a name="additional-reference-material"></a>Documents de référence supplémentaires

Autres rubriques de référence dans le Guide du développeur sont les suivantes :

- [Points de terminaison IoT concentrateur] [ lnk-endpoints] décrit les différents points de terminaison chaque concentrateur IoT expose pour les opérations de runtime et la gestion.
- [Limitation et les quotas de] [ lnk-quotas] décrit les quotas qui s’appliquent au service IoT concentrateur et le comportement de limitation s’attendre lorsque vous utilisez le service.
- [Concentrateur IoT périphérique et service SDK] [ lnk-sdks] répertorie le langage diverses SDK vous une utilisation lorsque vous développez périphérique et service applications qui interagissent avec IoT concentrateur.
- [Langage de requête pour jumeaux, méthodes et travaux] [ lnk-query] décrit le langage de requête que vous pouvez utiliser pour récupérer des informations à partir du IoT Hub sur votre jumeaux appareil, les méthodes et les tâches.
- [Prise en charge IoT concentrateur MQTT] [ lnk-devguide-mqtt] donne des informations supplémentaires sur la prise en charge IoT concentrateur pour le protocole MQTT.

## <a name="next-steps"></a>Étapes suivantes

À présent que vous avez appris à utiliser les méthodes directes, pouvant vous intéresser dans la rubrique Guide du développeur suivante :

- [Planifier des tâches sur plusieurs appareils][lnk-devguide-jobs]

Si vous voulez essayer certaines des concepts décrits dans cet article, il est possible que vous intéresser le didacticiel IoT concentrateur suivant :

- [Utiliser les méthodes de cloud vers le périphérique][lnk-methods-tutorial]

<!-- links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-methods-tutorial]: iot-hub-c2d-methods.md
[lnk-devguide-messages]: iot-hub-devguide-messaging.md
