<properties
 pageTitle="Développeur guide - comprendre jumeaux appareil | Microsoft Azure"
 description="Guide du développeur IoT concentrateur Azure - utilisation appareil jumeaux pour synchroniser les données d’état et la configuration entre IoT concentrateur et vos appareils"
 services="iot-hub"
 documentationCenter=".net"
 authors="fsautomata"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/30/2016" 
 ms.author="elioda"/>

# <a name="understand-device-twins---preview"></a>Comprendre jumeaux appareil : afficher un aperçu

## <a name="overview"></a>Vue d’ensemble

*Jumeaux appareil* sont des documents JSON qui stockent les informations d’état de périphérique (métadonnées, les configurations et conditions). Concentrateur IoT persiste un double appareil pour chaque appareil auxquels vous vous connectez à IoT concentrateur. Cet article décrit :

* la structure de la double périphérique : *balises*et *votre choix* *signalés propriétés*, et
* les opérations appareil applications et en fin peut effectuer sur jumeaux appareil.

> [AZURE.NOTE] Pour l’instant, jumeaux appareil est accessibles uniquement à partir d’appareils qui se connectent à concentrateur IoT en utilisant le protocole MQTT. Reportez-vous à la [prise en charge MQTT] [ lnk-devguide-mqtt] article pour obtenir des instructions sur la conversion d’application appareil existante à utiliser MQTT.

### <a name="when-to-use"></a>Quand utiliser

Utilisez jumeaux appareil à :

* Stocker des métadonnées spécifiques du périphérique dans le cloud, par exemple, emplacement de déploiement d’un distributeur.
* Rapport des informations d’état actuel telles que les fonctionnalités disponibles et conditions depuis votre application de périphérique, par exemple, un périphérique se connectant par le biais cellulaires ou Wi-Fi.
* Synchroniser l’état du flux de travail longue entre appareil application et précédent fin, par exemple, serveur principal spécifiant la nouvelle version microprogramme pour installer et l’application d’appareil reporting différentes étapes du processus de mise à jour.
* Interroger votre appareil métadonnées, configuration ou état.

Utiliser les [messages d’appareil-nuage] [ lnk-d2c] pour les séquences de horodatées litiges série chronologique de données capteur ou alarmes. Utiliser les [méthodes de cloud vers le périphérique] [ lnk-methods] pour un contrôle interactif de périphériques, par exemple un ventilateurs sous tension.

## <a name="device-twins"></a>Jumeaux appareil

Jumeaux appareil stocker les informations relatives aux périphériques qui :

- Extrémités arrière et l’appareil peuvent utiliser pour synchroniser configuration et conditions d’appareil.
- L’application principale permet de requête et cibles longue opérations.

Cycle de vie d’un double appareil est lié à l' [identité de l’appareil]correspondante[lnk-identity]. Jumeaux est implicitement créés et supprimés lorsqu’une nouvelle identité de l’appareil est créée ou supprimée dans IoT Hub.

Un double appareil est un document JSON qui inclut :

* **Balises**. Un document JSON lire et rédigé par le serveur principal. Les balises ne sont pas visibles aux applications de l’appareil.
* **Propriétés souhaité**. Utilisé avec des propriétés signalées pour synchroniser condition ou configuration du périphérique. Propriétés de votre choix ne peuvent être définies par l’application revenir fin et peuvent être lus par l’application d’appareil. L’application d’appareil peut être avertie en temps réel des modifications dans les propriétés souhaitées.
* **Propriétés déclaré**. Utilisé conjointement avec les propriétés souhaitées pour synchroniser condition ou configuration du périphérique. Propriétés signalées ne peut être définies par l’application d’appareil et peuvent être lus et interrogées en application principale.

En outre, la racine de la double appareil contient les propriétés en lecture seule à partir de l’identité correspondante, comme contenues dans le [Registre d’identité appareil][lnk-identity].

![][img-twin]

Voici un exemple d’un document appareil double JSON :

        {
            "deviceId": "devA",
            "generationId": "123",
            "status": "enabled",
            "statusReason": "provisioned",
            "connectionState": "connected",
            "connectionStateUpdatedTime": "2015-02-28T16:24:48.789Z",
            "lastActivityTime": "2015-02-30T16:24:48.789Z",

            "tags": {
                "$etag": "123",
                "deploymentLocation": {
                    "building": "43",
                    "floor": "1"
                }
            },
            "properties": {
                "desired": {
                    "telemetryConfig": {
                        "sendFrequency": "5m"
                    },
                    "$metadata" : {...},
                    "$version": 1
                },
                "reported": {
                    "telemetryConfig": {
                        "sendFrequency": "5m",
                        "status": "success"
                    }
                    "batteryLevel": 55,
                    "$metadata" : {...},
                    "$version": 4
                }
            }
        }

Dans l’objet racine, sont les propriétés du système et des objets conteneur pour `tags` et les deux `reported` et `desired properties`. La `properties` conteneur contient des éléments en lecture seule (`$metadata`, `$etag`, et `$version`) qui sont décrites respectivement dans les [métadonnées double] [ lnk-twin-metadata] et [simultané] [ lnk-concurrency] sections.

### <a name="reported-property-example"></a>Exemple de propriétés signalés

Dans l’exemple ci-dessus, le double appareil contient un `batteryLevel` propriété est signalée par l’application d’appareil. Cette propriété rend possible interroger et fonctionnent sur les périphériques basés sur le niveau de batterie signalés dernière. Un autre exemple aurait les options de connectivité ou de fonctionnalités du périphérique appareil application rapport.

Notez les propriétés comment signalées simplifier les scénarios où le serveur principal n’est intéressé par la dernière valeur connue d’une propriété. Utiliser les [messages d’appareil-nuage] [ lnk-d2c] si le serveur principal doit traiter télémétrie appareil sous la forme de séquences d’événements horodatés, tels que de la série chronologique.

### <a name="desired-configuration-example"></a>Exemple de configuration souhaitée

Dans l’exemple ci-dessus, le `telemetryConfig` propriétés souhaitées et signalées sont utilisées par l’application de fin et appareil précédent pour synchroniser la configuration de télémétrie pour cet appareil. Par exemple :

1. Le principal de l’application définit la propriété désirée avec la valeur de configuration souhaitée. Voici la partie du document avec la propriété souhaitée :

        ...
        "desired": {
            "telemetryConfig": {
                "sendFrequency": "5m"
            },
            ...
        },
        ...
        
2. L’application de l’appareil est informée de la modification immédiatement si connecté, ou à la première se reconnecter. L’application d’appareil signale ensuite la configuration de mise à jour (ou une condition d’erreur à l’aide du `status` propriété). Voici la partie des propriétés signalées :

        ...
        "reported": {
            "telemetryConfig": {
                "sendFrequency": "5m",
                "status": "success"
            }
            ...
        }
        ...

3. Le principal application peut conserver suivre les résultats de l’opération de configuration de nombreux appareils, en [demandant] [ lnk-query] jumeaux.

> [AZURE.NOTE] Extraits de code ci-dessus sont des exemples, optimisés pour améliorer la lisibilité de la façon dont possible pour coder une configuration de l’appareil et son statut. Concentrateur IoT n’impose un schéma spécifique pour les propriétés souhaitées et signalés dans la jumeaux appareil.

Dans de nombreux cas jumeaux est utilisées pour synchroniser les opérations de longue tels que les mises à jour. Reportez-vous à [utiliser les propriétés souhaitées pour configurer les périphériques] [ lnk-twin-properties] pour plus d’informations sur l’utilisation des propriétés pour synchroniser et effectuer le suivi de temps en cours d’exécution opérations sur les périphériques.

## <a name="back-end-operations"></a>Opérations principale

Le serveur principal fonctionne sur le double à l’aide des opérations suivantes atomiques, exposées via HTTP :

1. **Récupérer double par code**. Cette opération retourne le contenu d’un document de double, y compris les balises et que vous le souhaitez, signalé et propriétés système.
2. **Partiellement mettre à jour double**. Cette opération permet du serveur principal pour partiellement mettre à jour le double liens ou toutes les propriétés souhaitées. La mise à jour partielle est exprimé sous forme d’un document JSON qui ajoute ou met à jour toute propriété mentionnée. Définir les propriétés `null` sont supprimés. Par exemple, ce qui suit crée une nouvelle propriété de votre choix avec valeur `{"newProperty": "newValue"}`, remplace la valeur existante de `existingProperty` avec `"otherNewValue"`et supprime complètement `otherOldProperty`. Aucune modification se produire à d’autres propriétés souhaitées existantes ou les balises suivantes :

        {
            "properties": {
                "desired": {
                    "newProperty": {
                        "nestedProperty": "newValue"
                    },
                    "existingProperty": "otherNewValue",
                    "otherOldProperty": null
                }
            }
        }

3. **Remplacer les propriétés souhaitées**. Cette opération permet du serveur principal remplacer toutes les propriétés souhaitées complètement remplacer par un nouveau document JSON pour `properties/desired`.
4. **Remplacer des balises**. Comme pour remplacer les propriétés de votre choix, cette opération permet du serveur principal pour complètement écraser toutes les balises existants et remplacer par un nouveau document JSON pour `tags`.

Prend en charge toutes les opérations ci-dessus [simultané] [ lnk-concurrency] et requièrent l’autorisation **ServiceConnect** , telles que définies dans la [sécurité] [ lnk-security] article.

Outre ces opérations, le serveur principal peut interroger le jumeaux à l’aide d’un type SQL de [langage de requête][lnk-query]et effectuer les opérations sur les grands ensembles de jumeaux à l’aide de [travaux][lnk-jobs].

## <a name="device-operations"></a>Opérations d’unité

L’application d’appareil fonctionne sur le double à l’aide d’atomiques opérations suivantes :

1. **Récupérer double**. Cette opération retourne le contenu d’un document de double (y compris les balises et vous le souhaitez, signalé et propriétés système) pour le périphérique actuellement connecté.
2. **Partiellement mise à jour signalée propriétés**. Cette opération permet de la mise à jour partielle des propriétés de l’appareil connecté signalées. Il utilise le même format de mise à jour JSON comme serveur principal les en regard de mise à jour partielle des propriétés souhaitées.
3. **Observer souhaité propriétés**. Le périphérique actuellement connecté pouvez choisir d’être informé des mises à jour les propriétés souhaitées dès qu’elles se produisent. Le périphérique reçoit la même forme de mise à jour (remplacement partiel ou complè) exécutée par le serveur principal.

Toutes les opérations ci-dessus requièrent l’autorisation **DeviceConnect** , telles que définies dans la [sécurité] [ lnk-security] article.

Le [périphérique IoT Azure SDK] [ lnk-sdks] permettent d’utiliser les opérations ci-dessus à partir de nombreuses langues et plateformes. Vous trouverez plus d’informations sur les détails de primitives du IoT concentrateur pour la synchronisation des propriétés de votre choix dans [le flux de reconnexion appareil][lnk-reconnection].

> [AZURE.NOTE] Pour l’instant, jumeaux appareil est accessibles uniquement à partir d’appareils qui se connectent à concentrateur IoT en utilisant le protocole MQTT.

## <a name="reference-topics"></a>Rubriques de référence :

Les rubriques de référence vous fournissent plus d’informations sur le contrôle de l’accès à votre concentrateur IoT.

## <a name="tags-and-properties-format"></a>Mettre en forme des balises et des propriétés

Les balises, les propriétés souhaitées et signalées sont objets JSON avec les restrictions suivantes :

* Clés tous les objets JSON sont des chaînes UNICODE 128 car la casse. Autorisé caractères exclure les caractères de contrôle UNICODE (segments C0 et C1), et `'.'`, `' '`, et `'$'`.
* Toutes les valeurs d’objet JSON peuvent être des types de JSON suivants : booléen, nombre, chaîne, objet. Les tableaux ne sont pas autorisés.

## <a name="twin-size"></a>Taille double

Concentrateur IoT applique une limite de 8 Ko sur les valeurs de `tags`, `properties/desired`, et `properties/reported`, à l’exclusion des éléments en lecture seule.
La taille est calculée en comptant tous les caractères en excluant UNICODE contrôlent caractères (segments C0 et C1) et l’espace `' '` lorsqu’il s’affiche en dehors d’une constante de chaîne.
Concentrateur IoT refuse avec l’erreur toutes les opérations que vous souhaitez augmenter la taille de ces documents au-dessus de la limite.

## <a name="twin-metadata"></a>Métadonnées double

Concentrateur IoT conserve l’horodatage de la dernière mise à jour pour chaque objet JSON dans les propriétés de votre choix et signalées. Les horodatages sont en UTC et codé au format [ISO8601] `YYYY-MM-DDTHH:MM:SS.mmmZ`.
Par exemple :

        {
            ...
            "properties": {
                "desired": {
                    "telemetryConfig": {
                        "sendFrequency": "5m"
                    },
                    "$metadata": {
                        "telemetryConfig": {
                            "sendFrequency": {
                                "$lastUpdated": "2016-03-30T16:24:48.789Z"
                            },
                            "$lastUpdated": "2016-03-30T16:24:48.789Z"
                        },
                        "$lastUpdated": "2016-03-30T16:24:48.789Z"
                    },
                    "$version": 23
                },
                "reported": {
                    "telemetryConfig": {
                        "sendFrequency": "5m",
                        "status": "success"
                    }
                    "batteryLevel": "55%",
                    "$metadata": {
                        "telemetryConfig": {
                            "sendFrequency": "5m",
                            "status": {
                                "$lastUpdated": "2016-03-31T16:35:48.789Z"
                            },
                            "$lastUpdated": "2016-03-31T16:35:48.789Z"
                        }
                        "batteryLevel": {
                            "$lastUpdated": "2016-04-01T16:35:48.789Z"
                        },
                        "$lastUpdated": "2016-04-01T16:24:48.789Z"
                    },
                    "$version": 123
                }
            }
            ...
        }

Ces informations sont conservées à chaque niveau (et pas seulement les feuilles de la structure JSON) pour conserver les mises à jour qui suppriment des clés d’objet.

## <a name="optimistic-concurrency"></a>Simultané

Balises, propriétés de votre choix et signalées tous les prennent en charge simultané.
Balises ont un etag, conformément au [RFC7232], qui représente la représentation JSON de la balise. Vous pouvez utiliser cette opérations conditionnelle mise à jour de la fin de garantir la cohérence.

Propriétés de votre choix et signalées n’ont pas etags, mais ont un `$version` valeur qui est garantie incrémentielles. Comme pour un etag, la version peut être utilisée par la partie mise à jour comme (une application d’appareil pour une propriété signalée) ou serveur principal pour une propriété de votre choix pour appliquer la cohérence des mises à jour.

Versions sont également utiles lorsqu’un agent observation (par exemple, l’application d’appareil observant les propriétés souhaitées) dans la recherche de courses entre le résultat d’une opération de récupérer et une notification de mise à jour. La section [flux de reconnexion appareil] [ lnk-reconnection] donne des informations supplémentaires.

## <a name="device-reconnection-flow"></a>Flux de reconnexion appareil

Concentrateur IoT ne conserve pas les notifications de mise à jour de propriétés souhaitées pour les appareils déconnectés. Il suit qu’un périphérique qui se connecte doit extraire le document propriétés souhaitées complète, en plus de l’abonnement pour les notifications de mise à jour. Donner la possibilité de courses entre les notifications de mise à jour et de récupération complète, le flux suivant doit être assuré :

1. Application d’appareil se connecte à un concentrateur IoT.
2. Application d’appareil d’abonnements pour les propriétés souhaitées notifications mises à jour.
3. Application d’appareil récupère le document complet pour les propriétés souhaitées.

L’application d’appareil pouvez ignorer toutes les notifications avec `$version` inférieure ou égale à la version du document récupéré complète. Cela est possible car IoT concentrateur garantit que les versions incrémentent toujours.

> [AZURE.NOTE] Cette logique est déjà implémentée dans l' [appareil IoT Azure SDK][lnk-sdks]. Cette description est utile uniquement si l’application d’appareil ne peut pas utiliser d’appareil IoT Azure SDK et devez programmer l’interface MQTT directement.

## <a name="additional-reference-material"></a>Documents de référence supplémentaires

Autres rubriques de référence dans le Guide du développeur sont les suivantes :

- [Points de terminaison IoT concentrateur] [ lnk-endpoints] décrit les différents points de terminaison chaque concentrateur IoT expose pour les opérations de runtime et la gestion.
- [Limitation et les quotas de] [ lnk-quotas] décrit les quotas qui s’appliquent au service IoT concentrateur et le comportement de limitation s’attendre lorsque vous utilisez le service.
- [Concentrateur IoT périphérique et service SDK] [ lnk-sdks] répertorie le langage diverses SDK vous une utilisation lorsque vous développez périphérique et service applications qui interagissent avec IoT concentrateur.
- [Langage de requête pour jumeaux, méthodes et travaux] [ lnk-query] décrit le langage de requête que vous pouvez utiliser pour récupérer des informations à partir du IoT Hub sur votre jumeaux appareil, les méthodes et les tâches.
- [Prise en charge IoT concentrateur MQTT] [ lnk-devguide-mqtt] donne des informations supplémentaires sur la prise en charge IoT concentrateur pour le protocole MQTT.

## <a name="next-steps"></a>Étapes suivantes

Désormais, vous avez appris sur jumeaux appareil, les rubriques suivantes pour les développeurs Guide pouvant vous intéresser :

- [Appeler une méthode directe sur un appareil][lnk-methods]
- [Planifier des tâches sur plusieurs appareils][lnk-jobs]

Si vous voulez essayer certaines des concepts décrits dans cet article, il est possible que vous intéresser les didacticiels IoT concentrateur suivants :

- [Comment utiliser le double de l’appareil][lnk-twin-tutorial]
- [Comment utiliser propriétés double][lnk-twin-properties]

<!-- links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-jobs]: iot-hub-devguide-jobs.md
[lnk-identity]: iot-hub-devguide-identity-registry.md
[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-security]: iot-hub-devguide-security.md

[ISO8601]: https://en.wikipedia.org/wiki/ISO_8601
[RFC7232]: https://tools.ietf.org/html/rfc7232
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-twin-tutorial]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-properties]: iot-hub-node-node-twin-how-to-configure.md
[lnk-twin-metadata]: iot-hub-devguide-device-twins.md#twin-metadata
[lnk-concurrency]: iot-hub-devguide-device-twins.md#optimistic-concurrency
[lnk-reconnection]: iot-hub-devguide-device-twins.md#device-reconnection-flow

[img-twin]: media/iot-hub-devguide-device-twins/twin.png