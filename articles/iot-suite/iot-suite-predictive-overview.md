<properties
 pageTitle="Solution préconfigurée de maintenance prédictive | Microsoft Azure"
 description="Une description de la solution de gestion prédictive préconfigurée IoT Azure."
 services=""
 suite="iot-suite"
 documentationCenter=""
 authors="stevehob"
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

# <a name="predictive-maintenance-preconfigured-solution-overview"></a>Présentation de la solution maintenance prédictive préconfigurée

La solution de *gestion prédictive* préconfiguré est une des [solutions préconfigurées] [ lnk_preconfigured_solutions] publié dans le cadre de [Microsoft Azure IoT Suite][lnk_iot_suite]. Cette solution s’intègre collection de télémétrie appareil en temps réel avec un modèle de prévision créé à l’aide [d’Apprentissage automatique Azure][lnk_machine_learning].


Grâce à Azure IoT Suite, les entreprises peuvent rapidement et facilement vous connecter à surveiller les biens et analyser des données en temps réel. La solution de gestion prédictive préconfigurée prend ces données et utilise des tableaux de bord riche et visualisations fournissant aux entreprises nouvelle intelligence qui peut l’efficacité du lecteur et améliorer les revenus.

## <a name="the-scenario"></a>Le scénario

Fabrikam est une compagnie aérienne régionale qui met l’accent sur l’expérience client excellent à des prix concurrentiels. Une cause de retards des avions est des problèmes de maintenance et maintenance de moteur d’avion est particulièrement complexe. Échec du moteur vol doit être évitée à tout prix, afin que Fabrikam examine régulièrement ses moteurs et respecte les recommandations relatives à un programme de maintenance planifiée. Toutefois, moteurs toujours ne pas porter le même. Certains maintenance inutile s’effectue sur les moteurs. Plus important, problèmes posent qui peuvent sol un avion avant l’exécution de maintenance. Cela provoque des retards coûteux, surtout si un avion se trouve à un emplacement où techniciens droite ou pièces ne sont pas disponibles.

Les moteurs d’avions de Fabrikam sont équipés de capteurs qui surveiller les conditions moteur vol. Fabrikam utiliser Azure IoT Suite pour collecter les données de capteur collectées pendant le vol. Après avoir années accumuler du moteur opérationnel et données échec, scientifiques de données de Fabrikam ont modélisation un moyen de prédire la restant utile vie (RUL) un moteur d’avion. Qu’ils ont identifiés ne de corrélation entre les données de quatre des capteurs moteur avec la vêtements moteur entraîne la défaillance éventuelle. Tandis que Fabrikam continue effectuer des vérifications régulières pour assurer la sécurité, il peut maintenant utiliser les modèles pour calculer la RUL pour chaque moteur après que chaque vol à l’aide de la télémétrie collectées à partir de moteurs pendant le vol. Fabrikam peut désormais prédire futures points de panne et plan de gestion et réparer à l’avance.

> [AZURE.NOTE] Le modèle de solution utilise des données d’usure moteur réel.

En prévoyant le seuil lors de la maintenance est requise, Fabrikam pouvez optimiser ses opérations pour réduire les coûts. Les coordinateurs maintenance fonctionnent avec planificateurs pour planifier la maintenance coïncidant avec un avion arrêt à un emplacement particulier et de garantir suffisamment de temps pour l’avion doivent se déconnecter de service sans à l’origine d’interruption de planification. Fabrikam peut planifier des techniciens en conséquence ; s’assurer qu’avion est pris en charge efficacement sans délai d’attente. Gestionnaires de contrôle stock recevoir des plans de maintenance, afin de pouvoir optimiser leur processus de commande et stock de pièces de rechange. Tout cela permet Fabrikam pour réduire le temps de sol avion et réduire les coûts d’exploitation tout en garantissant la sécurité des voyageurs et le personnel.

Pour mieux comprendre comment [Azure IoT Suite] [ lnk_iot_suite] fournit les fonctionnalités faut-il exploiter le potentiel de maintenance prédictive, veuillez consulter ce [infographic][lnk_infographic].

## <a name="how-the-predictive-maintenance-solution-is-built"></a>Comment la solution de gestion prédictive est créée

La solution exploite un modèle d’apprentissage automatique Azure existant disponible en tant que modèle pour afficher ces fonctionnalités travaille à télémétrie appareil collectée par les services IoT Suite. Microsoft a créé un [modèle de régression] [ lnk_regression_model] un moteur d’avion et publié le modèle terminé, données<sup>\[1\]</sup>et des instructions détaillées sur la façon d’utiliser le modèle.

La solution de gestion prédictive préconfigurée IoT Azure utilise le modèle de régression créé à partir de ce modèle ; Il est déployé dans votre abonnement Azure et exposé via une API générée automatiquement. La solution inclut un sous-ensemble de données de test représentant 4 (de 100 total) moteurs et le 4 (21 total) des flux de données capteur qui fournit un résultat exact à partir du modèle formé.

*\[1\] Saxena a et K. Goebel (2008). « Double flux moteur dégradation Simulation jeu de données », référentiel de données pronostic NASA Ames (http://ti.arc.nasa.gov/tech/dash/pcoe/prognostic-data-repository/), centre de recherche Ames NASA, Moffett champ, autorité de certification*

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur comment IoT Azure permet des scénarios de maintenance prédictive, lisez [capturer valeur à partir de l’Internet des objets][lnk_capture_value].

Prendre une [procédure pas à pas] [ lnk-predictive-walkthrough] de la maintenance prédictive solution préconfigurée.

[lnk-predictive-walkthrough]: iot-suite-predictive-walkthrough.md
[lnk_preconfigured_solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk_iot_suite]: iot-suite-overview.md
[lnk_machine_learning]: https://azure.microsoft.com/services/machine-learning/
[lnk_infographic]: https://www.microsoft.com/server-cloud/predictivemaintenance/Index.html
[lnk_regression_model]: http://gallery.cortanaanalytics.com/Collection/Predictive-Maintenance-Template-3
[lnk_capture_value]: http://download.microsoft.com/download/0/7/D/07D394CE-185D-4B96-AC3C-9B61179F7080/Capture_value_from_the_Internet%20of%20Things_with_Predictive_Maintenance.PDF

Vous pouvez également Explorer les autres fonctionnalités et des fonctions des solutions Suite IoT préconfigurés :

- [Forum aux questions IoT gamme][lnk-faq]
- [Sécurité IoT d’emblée][lnk-security-groundup]

[lnk-faq]: iot-suite-faq.md
[lnk-security-groundup]: securing-iot-ground-up.md
