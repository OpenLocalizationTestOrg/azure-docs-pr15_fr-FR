<properties
 pageTitle="Azure IoT préconfiguré solutions | Microsoft Azure"
 description="Une description de la IoT Azure préconfiguré solutions et leur architecture avec des liens vers des ressources supplémentaires."
 services=""
 suite="iot-suite"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-suite"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="08/09/2016"
 ms.author="dobett"/>

# <a name="what-are-the-azure-iot-suite-preconfigured-solutions"></a>Quelles sont les solutions Azure IoT Suite préconfiguré ?

Les solutions Azure IoT Suite préconfigurés sont mises en œuvre de modèles de solution IoT courants que vous pouvez déployer sur Azure à l’aide de votre abonnement. Vous pouvez utiliser les solutions préconfigurées :

- Comme point de départ pour vos propres solutions IoT.
- Pour en savoir plus sur les modèles courants dans le développement et de conception de la solution IoT.

Chaque solution préconfigurée est une implémentation complète et de bout en bout qui utilise des appareils simulés pour générer de télémétrie.

Outre le déploiement et l’exécution des solutions dans Azure, vous pouvez télécharger le code source complet puis personnaliser et étendre la solution pour répondre à vos besoins IoT spécifiques.

> [AZURE.NOTE] Pour déployer une des solutions préconfigurées, visitez le [Microsoft Azure IoT Suite][lnk-azureiotsuite]. L’article de la [prise en main les solutions IoT préconfiguré] [ lnk-getstarted-preconfigured] fournit plus d’informations sur le déploiement et exécutez une des solutions.

Le tableau suivant montre comment les solutions correspondent aux fonctionnalités IoT spécifiques :

| Solution | Réception de données | Identité de l’appareil | Commande et contrôle | Règles et Actions | Analytique prédictive |
|------------------------|-----|-----|-----|-----|-----|
| [Contrôle à distance][lnk-getstarted-preconfigured] | Oui | Oui | Oui | Oui | -   |
| [Maintenance prédictive][lnk-predictive-maintenance] | Oui | Oui | Oui | Oui | Oui |

- *Réception de données*: pénétration de données à l’échelle dans le cloud.
- *Identité de l’appareil*: gestion des identités uniques de chaque appareil connecté.
- *Commande et contrôle*: envoyer des messages à un appareil à partir du cloud à l’origine de l’appareil exécuter une action.
- *Les règles et les actions*: le serveur principal de solution utilise des règles pour agir sur appareil-nuage des données spécifiques.
- *Analytique prédictive*: principaux solution s’applique analyse les données d’appareil-nuage afin de prédire lorsque des actions spécifiques doivent avoir lieu. Par exemple, analyse de télémétrie de moteur d’avion pour déterminer quand maintenance moteur est due.

## <a name="remote-monitoring-preconfigured-solution-overview"></a>Présentation de la solution surveillance préconfiguré à distance

Nous avons choisi décrire la solution préconfigurée contrôle à distance dans cet article, car il illustre plusieurs éléments de conception communs que les autres solutions partagent.

Le diagramme suivant illustre les éléments clés de la solution d’analyse à distance. Les sections suivantes fournissent des informations supplémentaires sur ces éléments.

![Surveillance à distance préconfiguré architecture de solution][img-remote-monitoring-arch]

## <a name="devices"></a>Appareils mobiles

Lorsque vous déployez la solution préconfigurée contrôle à distance, quatre périphériques simulés sont préalablement générés de la solution qui simuler un dispositif de refroidissement. Ces périphériques simulés ont un modèle intégré de température et humidité émet télémétrie. Ces appareils simulés sont inclus pour illustrer le flux de bout en bout des données via la solution et fournir une source pratique de télémétrie et une cible pour les commandes si vous êtes développeur principale à l’aide de la solution comme point de départ pour une implémentation personnalisée.

Lorsqu’un appareil se connecte tout d’abord à IoT concentrateur dans la solution préconfigurée contrôle à distance, le message d’informations appareil envoyé au concentrateur IoT énumère la liste des commandes de l’appareil peut répondre aux. Dans la solution préconfigurée d’analyse à distance, les commandes sont : 

- *Appareil ping*: l’appareil répond à cette commande avec un accusé de réception. Ceci est utile pour vérifier que l’appareil est toujours active et à l’écoute.
- *Démarrer télémétrie*: indique le périphérique pour commencer l’envoi de télémétrie.
- *Arrêter de télémétrie*: indique le périphérique pour l’arrêter l’envoi de télémétrie.
- *Température de définir le Point de modification*: contrôle les valeurs de télémétrie température simulé envoie le périphérique. Ceci est utile pour le test logique principale.
- *Télémétrie diagnostic*: contrôle si le périphérique doit envoyer la température externe comme télémétrie.
- *Changer l’état périphérique*. : définit la propriété de métadonnées état appareil qui indique le périphérique. Ceci est utile pour le test logique principale.

Vous pouvez ajouter plus simulés appareils à la solution qu’émettre la même télémétrie et de répondre à ces commandes. 

## <a name="iot-hub"></a>Concentrateur IoT

Dans cette solution préconfigurée, l’instance IoT concentrateur correspond à la *Passerelle Cloud* dans une [architecture de solution IoT]classique[lnk-what-is-azure-iot].

Un concentrateur IoT reçoit télémétrie à partir des périphériques à un point de terminaison unique. Un concentrateur IoT gère également les points de terminaison spécifiques appareil où chaque appareils peuvent le retrouver les commandes qui lui sont envoyés.

Le hub IoT rend le télémétrie reçu disponible via la télémétrie côté service lire le point de terminaison.

## <a name="azure-stream-analytics"></a>Flux de données Azure Analytique

La solution préconfigurée utilise trois [Azure flux Analytique] [ lnk-asa] travaux (ASA) pour filtrer le flux de télémétrie à partir des périphériques :


- *Travail DeviceInfo* - génère des données à un concentrateur de l’événement qui route les messages spécifiques appareil d’enregistrement, envoyés quand un appareil connecté une première fois ou en réponse à une commande **Modifier l’état appareil** , dans le Registre de périphérique solution (une base de données DocumentDB). 
- *Travail de télémétrie* - envoie télémétrie brutes pour tous les à Azure blob storage pour le stockage de froid et calcule les agrégations de télémétrie qui s’affichent dans le tableau de bord solution.
- Filtres de *tâche de règles* - le flux de télémétrie pour les valeurs qui dépassent les seuils de règle et renvoie les données à un concentrateur de l’événement. Lorsqu’une règle se déclenche, l’affichage de tableau de bord du portail de solution affiche cet événement sous la forme d’une nouvelle ligne dans le tableau d’historique alarme et déclenche une action basée sur les paramètres définis dans les affichages de règles et les Actions dans le portail de solution.

Dans cette solution préconfigurée, les tâches ASA font partie de la **solution IoT back-end** dans une [architecture de solution IoT]classique[lnk-what-is-azure-iot].

## <a name="event-processor"></a>Processeur d’événements

Dans cette solution préconfigurée, le processeur d’événements fait partie de la **solution IoT back-end** dans une [architecture de solution IoT]classique[lnk-what-is-azure-iot].

Les travaux **DeviceInfo** et **règles** ASA envoyer leur sortie aux hubs événement de remise à d’autres services de serveur principal. La solution utilise un [EventPocessorHost] [ lnk-event-processor] instance, qui s’exécute dans un [WebJob][lnk-web-job], pour lire les messages à partir de ces hubs événement. **EventProcessorHost** utilise les données **DeviceInfo** pour mettre à jour les données de périphérique dans la base de données DocumentDB et utilise les données de **règles** pour appeler la logique application et les mettre à jour que les alertes affichent dans le portail de solution.

## <a name="device-identity-registry-and-documentdb"></a>Registre d’identité périphérique et DocumentDB

Chaque concentrateur IoT inclut un [Registre d’identité appareil] [ lnk-identity-registry] qui stocke les clés de l’appareil. IoT Hub utilise ces informations authentifier les périphériques - un périphérique doit être enregistré et disposer d’une clé valide avant il peut se connecter au concentrateur.

Cette solution stocke des informations supplémentaires sur les appareils mobiles tels que leur état, les commandes qu’ils prennent en charge et autres métadonnées. La solution utilise une base de données DocumentDB pour stocker ces données appareil spécifique à la solution et le portail de solution extrait les données à partir de cette base de données DocumentDB pour l’affichage et modification.

La solution doit comporter également les informations dans le Registre identité appareil synchronisé avec le contenu de la base de données DocumentDB. **EventProcessorHost** utilise les données de projet d’analytique **DeviceInfo** flux pour gérer la synchronisation.

## <a name="solution-portal"></a>Portail de solution

![Tableau de bord de solution][img-dashboard]

Le portail de solution est une interface utilisateur basée sur le web qui est déployée dans le cadre de la solution préconfigurée dans le cloud. Il vous permet de :

- Afficher l’historique de télémétrie et d’alarme dans un tableau de bord.
- Configurer de nouveaux appareils.
- Gérer et analyser des appareils mobiles.
- Envoyer des commandes à des appareils spécifiques.
- Gérer les règles et actions.

Dans cette solution préconfigurée, le portail solution forms partie de la **solution IoT back-end** et **traitement et la connectivité de l’entreprise** dans l' [architecture de solution IoT]classique[lnk-what-is-azure-iot].

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les architectures de solution IoT, voir [services Microsoft Azure IoT : Architecture de référence][lnk-refarch].

Maintenant, vous savez quel une solution préconfigurée est, vous pouvez commencer en déployant la solution *contrôle à distance* préconfigurés : [prise en main les solutions préconfigurées][lnk-getstarted-preconfigured].

[img-remote-monitoring-arch]: ./media/iot-suite-what-are-preconfigured-solutions/remote-monitoring-arch1.png
[img-dashboard]: ./media/iot-suite-what-are-preconfigured-solutions/dashboard.png
[lnk-what-is-azure-iot]: iot-suite-what-is-azure-iot.md
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-event-processor]: ../event-hubs/event-hubs-programming-guide.md#event-processor-host
[lnk-web-job]: ../app-service-web/web-sites-create-web-jobs.md
[lnk-identity-registry]: ../iot-hub/iot-hub-devguide-identity-registry.md
[lnk-predictive-maintenance]: iot-suite-predictive-overview.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
[lnk-getstarted-preconfigured]: iot-suite-getstarted-preconfigured-solutions.md