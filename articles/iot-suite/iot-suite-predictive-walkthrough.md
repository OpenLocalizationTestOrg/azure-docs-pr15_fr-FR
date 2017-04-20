<properties
 pageTitle="Procédure pas à pas de maintenance prédictive | Microsoft Azure"
 description="Une procédure pas à pas de la solution de gestion prédictive préconfigurée IoT Azure."
 services=""
 suite="iot-suite"
 documentationCenter=""
 authors="aguilaaj"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-suite"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="08/17/2016"
 ms.author="araguila"/>

# <a name="predictive-maintenance-preconfigured-solution-walkthrough"></a>Procédure pas à pas de maintenance prédictive préconfigurée solution

## <a name="introduction"></a>Introduction

La solution de gestion prédictive préconfigurée IoT Suite est une solution de bout en bout pour un scénario d’entreprise qui prévoit le point lorsque défaillance est susceptible de se produire. Vous pouvez utiliser cette solution préconfigurée fait pour des activités telles que l’optimisation de maintenance. La solution combine services Azure IoT Suite clés, y compris un [Apprentissage automatique Azure] [ lnk_machine_learning] espace de travail. Cet espace de travail contient des expériences, basés sur un jeu de données exemple public, pour prévoir la restant utile vie (RUL) un moteur d’avion. La solution met en œuvre entièrement au scénario d’entreprise IoT comme point de départ vous permettant de planifier et implémenter une solution qui répond aux besoins de votre entreprise spécifique.

## <a name="logical-architecture"></a>Architecture logique

Le diagramme suivant décrit les composants logiques de la solution préconfigurée :

![][img-architecture]

Les éléments bleus sont Azure services qui sont configurés dans l’emplacement que vous sélectionnez lorsque vous configurez la solution préconfigurée. Vous pouvez prévoir la solution préconfigurée dans la région États-Unis Extrême-Orient, Europe du Nord ou Asie de l’est.

Certaines ressources ne sont pas disponibles dans les régions où vous devez configurer la solution préconfigurée. Les éléments oranges dans le diagramme représentent les services Azure sa mise en service dans la plus proche disponible région (Sud, Europe Ouest ou pays d’Asie du Sud-est) donnée la région sélectionnée.

L’élément verte est un périphérique simulé qui représente un moteur d’avion. Vous pouvez en savoir plus sur les appareils suivants simulés dans la section suivante.

Les éléments gris représentent des composants implémentent des fonctionnalités de *Gestion des équipements* . La version actuelle de la solution de gestion prédictive préconfigurée ne configure pas ces ressources. Pour plus d’informations sur l’administration de l’appareil, reportez-vous à la [solution préconfigurée de surveillance à distance][lnk-remote-monitoring].

## <a name="simulated-devices"></a>Appareils simulés

Dans la solution préconfigurée, un périphérique simulé représente un moteur d’avion. La solution est configurée avec deux moteurs qui correspondent à un seul avion. Chaque moteur émet quatre types de télémétrie : capteur 9, 11 capteur, capteur 14 et 15 capteur fournissent les données nécessaires pour le modèle d’apprentissage automatique calculer la restant utile vie (RUL) pour le moteur. Chaque périphérique simulé envoie les messages de télémétrie suivants à IoT concentrateur :

*Inventaire*. Un cycle représente un vol finale de longueur variable entre 2 à 10 h dans lequel les données de télémétrie sont capturées chaque demi-heure pendant le vol.

*Télémétrie*. Il existe quatre capteurs qui représentent les attributs de moteur. Les capteurs sont étiquetés générique 9 capteur, capteur 11, 14 capteur et capteur 15. Ces 4 capteurs représentent télémétrie suffisante pour obtenir des résultats pertinents à partir du modèle d’apprentissage automatique pour RUL. Ce modèle est créé à partir d’un jeu de données public qui inclut des données de capteur moteur réel. Pour plus d’informations sur les modalités de création du modèle à partir du jeu de données d’origine, voir le [Modèle de Maintenance prédictive Cortana Intelligence galerie][lnk-cortana-analytics].

Les appareils simulés peuvent gérer les commandes suivantes envoyés à partir d’un concentrateur IoT :

| Commande | Description |
|---------|-------------|
| StartTelemetry | Contrôle l’état de la simulation.<br/>Démarre le périphérique envoi de télémétrie     |
| StopTelemetry  | Contrôle l’état de la simulation.<br/>Arrêt du périphérique envoi de télémétrie |

Concentrateur IoT fournit d’accusé de réception commande appareil.

## <a name="azure-stream-analytics-job"></a>Tâche de flux de données Analytique Azure

**Travail : télémétrie** fonctionne sur le flux de télémétrie appareil entrantes à l’aide de deux instructions. La première sélectionne toutes les télémétrie à partir des périphériques et envoie ces données au stockage à partir de l’endroit où il est affiché dans l’application web blob. La deuxième instruction calcule les valeurs des capteurs moyenne sur une fenêtre décalée de deux minutes et envoie ces données via le hub de l’événement à un **processeur d’événements**.

## <a name="event-processor"></a>Processeur d’événements

Le **processeur d’événements** prend les valeurs capteur moyenne pour un cycle terminé. Il le passe ces valeurs à une API qui expose l’apprentissage automatique formé modèle pour calculer la RUL pour un moteur.

## <a name="azure-machine-learning"></a>Apprentissage automatique Azure

Pour plus d’informations sur les modalités de création du modèle à partir du jeu de données d’origine, voir le [Modèle de Maintenance prédictive Cortana Intelligence galerie][lnk-cortana-analytics].

## <a name="lets-start-walking"></a>Commençons parcours

Cette section décrit les composants de la solution décrit le cas d’utilisation prévue et fournit des exemples.

### <a name="predictive-maintenance-dashboard"></a>Tableau de bord de Maintenance prédictive

Cette page dans l’application web utilise des contrôles PowerBI JavaScript (voir le [référentiel PowerBI visuels][lnk-powerbi]) pour visualiser :

- Les données de sortie à partir des tâches de flux de données Analytique dans le stockage blob.
- Le nombre RUL et cycle par moteur d’avion.

### <a name="observing-the-behavior-of-the-cloud-solution"></a>Observez le comportement de la solution cloud

Dans le portail Azure, accédez au groupe de ressources avec le nom de la solution que vous avez choisi d’afficher vos ressources mis en service.

![][img-resource-group]

Lorsque vous configurez la solution préconfigurée, vous recevez un message électronique contenant un lien vers l’espace de travail d’apprentissage automatique. Vous pouvez également accéder à l’espace de travail d’apprentissage automatique à partir de [azureiotsuite.com] [ lnk-azureiotsuite] page de votre solution générée lorsqu’elle est dans l’état **prêt** .

![][img-machine-learning]

Dans le portail de la solution, vous pouvez voir que l’échantillon est configuré avec quatre périphériques simulés pour représenter deux avion avec deux moteurs par avion, chacune avec quatre capteurs. Lorsque vous accédez tout d’abord au portail solution, la simulation est arrêtée.

![][img-simulation-stopped]

Cliquez sur **Démarrer simulation** pour lancer la simulation dans lequel vous consultez l’historique de capteur, RUL, Cycles, et l’historique RUL remplir le tableau de bord.

![][img-simulation-running]

Lorsque RUL est inférieur à 160 (un seuil arbitraire choisi pour la démonstration), le portail de solution affiche un symbole d’avertissement en regard de l’affichage RUL en surbrillance le moteur d’avion en jaune. Notez comment les valeurs RUL ont une baisse général globale, mais tendent à rebond monter et Descendre. Ce problème provient du cycle de longueurs variables et la précision du modèle.

![][img-simulation-warning]

La simulation complète prend environ 35 minutes 148 cycles. Le seuil RUL 160 est atteint pour la première fois à environ 5 minutes et les deux moteurs atteint le seuil à environ 8 minutes.

La simulation s’exécute par le biais du dataset complète 148 cycles d’et règle des valeurs RUL et cycle final.

Vous pouvez arrêter la simulation à tout moment, mais en cliquant sur **Démarrer la Simulation** relecture la simulation à partir du début du jeu de données.

## <a name="next-steps"></a>Étapes suivantes

À présent que vous avez exécuté la solution de gestion prédictive préconfigurée vous souhaiterez peut-être modifier, consultez l’article [conseils sur la personnalisation des solutions préconfigurées][lnk-customize].

Le billet de blog [Maintenance prédictive IoT Suite - Options avancées -](http://social.technet.microsoft.com/wiki/contents/articles/33527.iot-suite-under-the-hood-predictive-maintenance.aspx) TechNet fournit des détails supplémentaires sur la solution de gestion prédictive préconfigurée.

Vous pouvez également Explorer les autres fonctionnalités et des fonctions des solutions Suite IoT préconfigurés :

- [Forum aux questions IoT gamme][lnk-faq]
- [Sécurité IoT d’emblée][lnk-security-groundup]


[img-architecture]: media/iot-suite-predictive-walkthrough/architecture.png
[img-resource-group]: media/iot-suite-predictive-walkthrough/resource-group.png
[img-machine-learning]: media/iot-suite-predictive-walkthrough/machine-learning.png
[img-simulation-stopped]: media/iot-suite-predictive-walkthrough/simulation-stopped.png
[img-simulation-running]: media/iot-suite-predictive-walkthrough/simulation-running.png
[img-simulation-warning]: media/iot-suite-predictive-walkthrough/simulation-warning.png

[lnk-powerbi]: https://www.github.com/Microsoft/PowerBI-visuals
[lnk_machine_learning]: https://azure.microsoft.com/services/machine-learning/
[lnk-remote-monitoring]: iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-cortana-analytics]: http://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Template-3
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-faq]: iot-suite-faq.md
[lnk-security-groundup]: securing-iot-ground-up.md
