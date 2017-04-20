<properties
 pageTitle="Indicateurs de diagnostic IoT concentrateur"
 description="Une vue d’ensemble des mesures Azure IoT concentrateur, permettant aux utilisateurs d’évaluer l’intégrité globale de leur ressource"
 services="iot-hub"
 documentationCenter=""
 authors="nberdy"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="08/11/2016"
 ms.author="nberdy"/>

# <a name="introduction-to-diagnostic-metrics"></a>Présentation des indicateurs de diagnostic

Indicateurs de diagnostics vous offrent meilleures données relatives à l’état des ressources Azure dans votre abonnement Azure. Indicateurs permettent d’évaluer l’intégrité globale du service et les appareils connectés à celui-ci. Statistiques utilisateur-facing sont importants, car ils vous permettent de voir ce qui se passe vos problèmes de profondeur IoT concentrateur et l’aide sans avoir à contacter le support technique Azure.

Vous pouvez activer les mesures de diagnostics à partir du portail Azure.

## <a name="how-to-enable-diagnostic-metrics"></a>Comment activer les mesures de Diagnostics

1. Créer un concentrateur IoT. Vous trouverez des instructions sur la création d’un concentrateur IoT dans la [Prise en main] [ lnk-get-started] guide.

2. Ouvrez la carte de votre plateforme IoT. À partir de là, cliquez sur **Diagnostics**.

    ![][1]

3. Configurer votre des diagnostics en définissant le statut sur **** et en sélectionnant un compte de stockage Azure pour stocker les données de diagnostic. Vérifier **les mesures**et appuyez sur **Enregistrer**. Notez que le compte de stockage Azure doit être créé à l’avance et que vous êtes chargé séparément pour le stockage. Vous pouvez également choisir d’envoyer vos données diagnostics à un point de terminaison Hubs événement.

    ![][2]

4. Une fois que vous avez configuré les Diagnostics de vérification, revenez à la carte de concentrateur IoT **vue d’ensemble** . Informations de mesures sont remplies dans la section **analyse** de la cuillère. En cliquant sur le graphique s’ouvre le volet mesures où vous pouvez afficher un résumé des informations métriques pour votre plateforme IoT et modifier la sélection des mesures figurant dans le graphique. Vous pouvez également configurer des alertes basées sur des valeurs métriques.

    ![][3]

## <a name="metrics-and-how-to-use-them"></a>Mesures et comment les utiliser

Concentrateur IoT fournit plusieurs mesures pour vous fournir une vue d’ensemble de l’état de votre plateforme et le nombre total d’appareils connectés à celui-ci. Vous pouvez combiner les informations à partir de plusieurs indicateurs pour obtenir une plus grande image de l’état du concentrateur IoT. Le tableau suivant décrit les mesures qu'effectue le suivi de chaque concentrateur IoT, et comment chaque métrique est lié à l’état global du concentrateur IoT.

| Métrique | Description métrique | À quoi sert le métrique |
| ---- | ---- | ---- |
| d2c.telemetry.Ingress.allProtocol | Le nombre de messages envoyés sur tous les périphériques | Vue d’ensemble des données sur envoie des messages |
| d2c.telemetry.Ingress.Success | Le nombre de tous les messages réussies dans le hub | Vue d’ensemble de pénétration de message réussie dans le hub |
| c2d.Commands.egress.Complete.Success | Le nombre de tous les messages de commande effectuée par le périphérique de réception sur tous les périphériques | Avec les mesures sur abandon et rejeter, donne un aperçu des taux de réussite de commande C2D global |
| c2d.Commands.egress.Abandon.Success | Le nombre de tous les messages avec succès abandonnés par le périphérique de réception sur tous les périphériques | Met en évidence les problèmes potentiels si les messages sont prise abandonnés plus souvent que prévu |
| c2d.Commands.egress.Reject.Success | Le nombre de tous les messages avec succès rejeté par le périphérique de réception sur tous les périphériques | Met en évidence les problèmes potentiels si les messages sont prise rejetés plus souvent que prévu |
| devices.totalDevices | La moyenne, min et max du nombre d’appareils inscrit dans le hub IoT | Le nombre de vos périphériques sont enregistrés au concentrateur |
| devices.connectedDevices.allProtocol | La moyenne, min et max du nombre d’appareils connectés simultanées | Vue d’ensemble du nombre d’appareils connectés au concentrateur |

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez vu une vue d’ensemble des mesures de diagnostics, suivez ce lien pour en savoir plus sur la gestion des Azure IoT concentrateur :

- [Contrôle des opérations][lnk-monitor]

Pour en savoir davantage les fonctionnalités du concentrateur IoT, voir :

- [Guide du développeur][lnk-devguide]
- [Simulation d’un appareil avec le Kit de développement de passerelle IoT][lnk-gateway]

<!-- Links and images -->
[1]: media/iot-hub-metrics/enable-metrics-1.png
[2]: media/iot-hub-metrics/enable-metrics-2.png
[3]: media/iot-hub-metrics/enable-metrics-3.png

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-operations-monitoring]: iot-hub-operations-monitoring.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-dr]: iot-hub-ha-dr.md

[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
