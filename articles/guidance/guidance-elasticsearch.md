
<properties
   pageTitle="Elasticsearch aussi les instructions Azure | Microsoft Azure"
   description="Elasticsearch Azure aussi les instructions."
   services=""
   documentationCenter="na"
   authors="dragon119"
   manager="bennage"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/22/2016"
   ms.author="masashin"/>

# <a name="elasticsearch-on-azure-guidance"></a>Elasticsearch aussi les instructions Azure 

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

Elasticsearch est un moteur de recherche open source hautement scalaire et la base de données. Il est utile dans les situations nécessitant une analyse rapide et la recherche des informations contenues dans des groupes de données volumineux. Ce guide examine certains aspects essentiels à prendre en considération lorsque vous créez un cluster Elasticsearch, l’accent sur les façons d’optimiser et testez votre configuration.

> [AZURE.NOTE]Ce guide connaissance base de Elasticsearch. Pour plus d’informations, visitez le [site Web Elasticsearch](https://www.elastic.co/products/elasticsearch). 

- **[Elasticsearch en cours d’exécution sur Azure][]** fournit une brève introduction à la structure générale des Elasticsearch et explique comment implémenter un cluster de Elasticsearch à l’aide d’Azure. 
- **[Performances de réception de données de réglage pour Elasticsearch sur Azure][]** décrit le déploiement d’un cluster Elasticsearch et fournit une analyse approfondie des différentes options de configuration à prendre en considération lorsque vous attendez un taux élevé de réception de données.
- **[Agrégation de données de réglage et les performances des requêtes pour Elasticsearch sur Azure][]** fournit une analyse approfondie des options pour prendre en considération au moment de décider comment optimiser votre système de performances des requêtes et de recherche.
- **[Configuration de résilience et récupération sur Elasticsearch sur Azure][]** décrit certaines fonctionnalités importantes d’un cluster Elasticsearch qui peuvent vous aider à réduire les risques de perte de données et les heures de récupération de données étendu.
- **[Création d’un environnement de test de performance pour Elasticsearch sur Azure][]** décrit comment configurer un environnement de test de la réception de données de performance et les charges de travail de requête dans un cluster Elasticsearch. 
- **[Mise en œuvre d’un JMeter plan pour Elasticsearch de test][]** résume en cours d’exécution des tests de performances implémentées à l’aide de plans de test JMeter avec code Java inséré en tant qu’un test JUnit permettant d’effectuer des tâches telles que le téléchargement de données dans le cluster Elasticsearch.
- **[Déploiement d’un échantillon de JMeter JUnit pour tester les performances de Elasticsearch][]** décrit comment créer et utiliser un échantillon de JUnit qui peut générer et télécharger des données sur un cluster Elasticsearch. Il fournit une approche hautement flexible pour charger le test qui peut générer de grandes quantités de données de test sans en fonction des fichiers de données externes. 
- **[Exécution des tests résilience Elasticsearch automatisés][]** résume comment exécuter les tests de résilience utilisés dans cette série. 
- **[Exécution des tests de performances Elasticsearch automatisés][]** indique comment exécuter les tests de performance utilisés dans cette série.


[Elasticsearch en cours d’exécution sur Azure]: guidance-elasticsearch-running-on-azure.md
[Réglage des performances de réception de données pour Elasticsearch sur Azure]: guidance-elasticsearch-tuning-data-ingestion-performance.md
[Création d’un environnement de test pour Elasticsearch sur Azure de performances]: guidance-elasticsearch-creating-performance-testing-environment.md
[Mise en œuvre d’un Plan de Test JMeter pour Elasticsearch]: guidance-elasticsearch-implementing-jmeter-test-plan.md
[Déploiement d’un échantillon de JMeter JUnit pour tester les performances de Elasticsearch]: guidance-elasticsearch-deploying-jmeter-junit-sampler.md
[Réglage d’agrégation des données et des performances des requêtes pour Elasticsearch sur Azure]: guidance-elasticsearch-tuning-data-aggregation-and-query-performance.md
[Configuration de résilience et récupération sur Elasticsearch sur Azure]: guidance-elasticsearch-configuring-resilience-and-recovery.md
[Exécution des Tests de résilience Elasticsearch automatisé]: guidance-elasticsearch-running-automated-resilience-tests.md
[Exécution des Tests de performances Elasticsearch automatisé]: guidance-elasticsearch-running-automated-performance-tests.md
