<properties 
    pageTitle="Manuel de solution véhicule télémétrie analytique | Microsoft Azure" 
    description="Utiliser les fonctionnalités d’Intelligence Cortana pour Familiarisez-vous prédictive et en temps réel sur un véhicule santé et conduite habitudes." 
    services="machine-learning" 
    documentationCenter="" 
    authors="bradsev" 
    manager="jhubbard" 
    editor="cgronlun" />

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/12/2016" 
    ms.author="bradsev" />


# <a name="vehicle-telemetry-analytics-solution-playbook"></a>Manuel de solution véhicule télémétrie analytique

Cette **menu** fournit des liens vers les chapitres de ce manuel. 

[AZURE.INCLUDE [cap-vehicle-telemetry-playbook-selector](../../includes/cap-vehicle-telemetry-playbook-selector.md)]

## <a name="overview"></a>Vue d’ensemble
Ordinateurs super ont été déplacées déconnecter le laboratoire et sont désormais mis en garde dans notre garage ! Ces voitures pointe contiennent une multitude de capteurs, leur donner la possibilité de suivre et analyser des millions d’événements par seconde. Nous espérons que 2020, la plupart de ces voitures sera ont été connectée à internet. Imaginons appui dans l’intégrité des données pour fournir les meilleures de sécurité de classe, la fiabilité et expérience conduite ! Microsoft a fait de ce rêve une réalité avec Intelligence Cortana.

Cortana Intelligence de Microsoft est une suite analytique avancées qui vous permet de transformer vos données en action intelligente et données volumineux entièrement gérées. Nous souhaitons vous présenter le modèle de Solution Cortana Intelligence véhicule télémétrie Analytique. Cette solution montre comment concessionnaires voiture, voiture fabricants et compagnies d’assurance peuvent utiliser les fonctionnalités de Cortana Intelligence pour obtenir en temps réel et analyse prédictive véhicule santé et conduite habituelles. 

La solution est implémentée en tant que [modèle d’architecture lambda](https://en.wikipedia.org/wiki/Lambda_architecture) affichant la version complète potentiels de la plateforme Intelligence Cortana pour en temps réel et traitement par lots. Solution : 

- fournit un simulator télématique véhicule
- s’appuie sur événement Hubs pour traiter les millions d’événements de télémétrie véhicule simulé dans Azure 
- utilise des flux Analytique pour Familiarisez-vous en temps réel sur état de véhicule
-  conserve les données dans un espace de stockage à long terme pour analytique lot plus riche. 
- tirant parti d’apprentissage automatique pour détecter une anomalie dans en temps réel et traitement pour Familiarisez-vous prédictive par lots.
- exploite HDInsight pour transformer les données à l’échelle et Data Factory à gérer orchestration, planification, gestion des ressources et surveillance du pipeline de traitement par lots 
- donne cette solution un tableau de bord riche des données en temps réel et visualisations prédictive analytique à l’aide de Power BI

## <a name="architecture"></a>Architecture

![](./media/cortana-analytics-playbook-vehicle-telemetry/fig1-vehicle-telemetry-annalytics-solution-architecture.png)
*Figure 1 – l’Architecture de Solution véhicule télémétrie Analytique*

Cette solution inclut les **composants Cortana Intelligence** suivants et présente leur intégration de bout en bout


- **Événement Hubs** pour traiter les millions d’événements de télémétrie véhicule dans Azure.
- **Flux Analytique** pour obtenir des idées en temps réel sur état véhicule et persiste ces données dans un espace de stockage à long terme pour analytique lot plus riche.
- **Apprentissage automatique** pour détecter une anomalie en temps réel et traitement par lots à Familiarisez-vous prédictive.
- **HDInsight** est exploité pour transformer les données à l’échelle
- **Data Factory** gère orchestration, planification, gestion des ressources et de surveillance du pipeline de traitement par lots.
- **Power BI** vous donne cette solution un tableau de bord riche des données en temps réel et visualisations prédictive analytique.

Cette solution accède à deux différentes **sources de données**: 

- **Simulé véhicule signaux et les diagnostics**: une simulator télématique véhicule émet des informations de diagnostic et signaux qui correspondent à l’état du véhicule et le modèle de conduite à un moment donné dans le temps. 
- **Catalogue de véhicule**: un dataset de référence contenant un VIN au mappage de modèle.
