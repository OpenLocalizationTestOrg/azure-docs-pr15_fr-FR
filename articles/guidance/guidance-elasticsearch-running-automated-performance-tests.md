
<properties
   pageTitle="Exécution des tests de performances Elasticsearch automatisés | Microsoft Azure"
   description="Description de la façon dont vous pouvez exécuter les tests de performances dans votre environnement."
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
   
# <a name="running-the-automated-elasticsearch-performance-tests"></a>Exécution des tests de performances Elasticsearch automatisés

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

Cet article fait [partie d’une série](guidance-elasticsearch.md). 

Les documents [performances de réception de données de réglage pour Elasticsearch sur Azure] et [réglage agrégation des données et les performances des requêtes pour Elasticsearch sur Azure] décrivent un certain nombre de tests de performances qui ont été exécutées sur un cluster Elasticsearch exemple.

Ces tests ont été un script pour leur permettre de s’exécuter dans une façon automatisée. Ce document décrit comment vous pouvez répéter les tests dans votre environnement.

## <a name="prerequisites"></a>Conditions préalables

Les tests automatisés requièrent les éléments suivants :

-  Un cluster Elasticsearch.

- Une configuration environnement JMeter comme décrit dans le document [Création d’un environnement de test de performance pour Elasticsearch sur Azure].

- [Python 3.5.1](https://www.python.org/downloads/release/python-351/) installés sur le masque des JMeter machine virtuelle.


## <a name="how-the-tests-work"></a>Comment fonctionnent les tests
L’exécution des tests à l’aide de JMeter. Un serveur principal JMeter charge un plan de test et passe à un ensemble de serveurs subordonnés JMeter qui en fait exécuter les tests. Le serveur principal JMeter coordonnées les serveurs subordonnés JMeter et sont additionnés les résultats.

Les plans de test suivants sont disponibles :

* [elasticsearchautotestplan3nodes.jmx](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/templates/elasticsearchautotestplan3nodes.jmx). Exécute le test de réception sur un cluster de 3 nœuds.

* [elasticsearchautotestplan6nodes.jmx](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/templates/elasticsearchautotestplan6nodes.jmx). Exécute le test de réception sur un cluster de 6-nœud.

* [elasticsearchautotestplan6qnodes.jmx](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/templates/elasticsearchautotestplan6qnodes.jmx). Exécute le test de réception et requête sur un cluster de 6-nœud.

* [elasticsearchautotestplan6nodesqueryonly.jmx](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/templates/elasticsearchautotestplan6nodesqueryonly.jmx). Exécute le test de requête uniquement sur un cluster de 6-nœud.


Vous pouvez utiliser ces plans de test comme base pour vos propres scénarios si vous avez besoin plus ou moins nœuds.

Les plans de test utilisent un échantillon de demande JUnit pour générer et charger les données de test. Le plan de test JMeter crée et exécute cette échantillon et surveille chacun des nœuds Elasticsearch pour les données de performance.  

## <a name="building-and-deploying-the-junit-jar-and-dependencies"></a>Créer et déployer le JUnit JAR et dépendances
Avant d’exécuter les tests de performances que vous devez télécharger, compiler et déployer les tests JUnit situés sous le dossier de performances/junitcode. Ces tests sont référencées par le plan de test JMeter. Pour plus d’informations, consultez la procédure « Importation d’un projet de test JUnit existant dans Eclipse » dans le document de [déploiement d’un échantillon de JMeter JUnit pour tester les performances de Elasticsearch].

Il existe deux versions des tests JUnit : 

- [Elasticsearch1.73](https://github.com/mspnp/azure-guidance/tree/master/ingestion-and-query-tests/junitcode/elasticsearch1.73). Utilisez ce code pour exécuter les tests de réception. Ces tests utilisent 1,73 Elasticsearch.

- [Elasticsearch2](https://github.com/mspnp/azure-guidance/tree/master/ingestion-and-query-tests/junitcode/elasticsearch2). Utilisez ce code pour exécuter les tests de requête. Ces tests utilisent Elasticsearch 2.1 et version ultérieure.

Copiez le fichier d’archive (JAR) Java approprié ainsi que le reste des dépendances sur vos ordinateurs JMeter. Le processus est décrit dans le [déploiement d’un échantillon de JMeter JUnit pour tester les performances de Elasticsearch][]. 

> **Important** Après le déploiement d’un test JUnit, utilisez JMeter pour charger et configurer les plans de test qui référence ce test JUnit et vérifiez que le groupe de threads BulkInsertLarge fait référence à la fichierJAR correct, le nom de la classe JUnit et procédé de test :
> 
> ![](./media/guidance-elasticsearch/performance-tests-image1.png)
> 
> Enregistrer les plans de test mis à jour avant d’exécuter les tests.

## <a name="creating-the-test-indexes"></a>Créer les index de test
Chaque test effectue réception et/ou requêtes sur un seul index indiquées lorsque le test est exécuté. Vous devez créer l’index en utilisant les schémas décrits dans les annexes aux documents [performances de réception de données de réglage pour Elasticsearch sur Azure] et [réglage agrégation des données et les performances des requêtes pour Elasticsearch sur Azure] et les configurer en fonction de votre scénario de test (valeurs document activé ou désactivé, plusieurs réplicas, etc.). Notez que les plans de test part du principe que l’index contient un seul type nommé *ctip*.

## <a name="configuring-the-test-script-parameters"></a>Configurer les paramètres de script de test
Copiez les fichiers de paramètre de script test suivants à l’ordinateur serveur JMeter :

* [run.properties](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/run.properties). Ce fichier Spécifie le nombre de threads de test JMeter à utiliser, la durée de test (en secondes), l’adresse IP du nœud (ou un équilibrage de charge dans le cluster Elasticsearch,) et le nom du cluster :

  ```ini
  nthreads=3
  duration=300
  elasticip=<IP Address or DNS Name Here>
  clustername=<Cluster Name Here>
  ```
  
  Modifiez ce fichier et spécifiez les valeurs appropriées pour votre test et cluster.

* [requête-config-win.ini](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/query-config-win.ini) et [requête-config-nix.ini](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/query-config-nix.ini). Ces deux fichiers contiennent les mêmes informations ; le fichier *win* est mis en forme pour les chemins d’accès et les noms de fichiers Windows et le fichier *nix* est mis en forme pour les chemins d’accès et les noms de fichiers Linux :

  ```ini
  [DEFAULT]
  debug=true #if true shows console logs.

  [RUN]
  pathreports=C:\Users\administrator1\jmeter\test-results\ #path where tests results are saved.
  jmx=C:\Users\administrator1\testplan.jmx #path to the JMeter test plan.
  machines=10.0.0.1,10.0.0.2,10.0.0.3 #IPs of the Elasticsearch data nodes separated by commas.
  reports=aggr,err,tps,waitio,cpu,network,disk,response,view #Name of the reports separated by commas.
  tests=idx1,idx2 #Elasticsearch index(es) name(s) to test, comma delimited if more than one.
  properties=run.properties #Name of the properties file.
  ```

  Modifier ce fichier pour spécifier les emplacements des résultats des essais, le nom du plan de test JMeter à s’exécuter, les adresses IP des nœuds de données Elasticsearch vous collectez des indicateurs de performance, les rapports contenant les données de performance brutes qui seront générées, et le nom (ou les noms par des virgules) de l’index sous test, si plusieurs , tests seront exécutera une après l’autre. Si le fichier run.properties se trouve dans un autre dossier ou un répertoire, spécifiez le chemin d’accès complet à ce fichier.

## <a name="running-the-tests"></a>L’exécution des tests

* Copiez le fichier [test.py de requête](https://github.com/mspnp/azure-guidance/blob/master/ingestion-and-query-tests/query-test.py) à l’ordinateur serveur JMeter, dans le même dossier que les run.properties et requête-config-win.ini (nix.ini de la configuration de la requête).

* Assurez-vous que jmeter.bat (Windows) ou jmeter.sh (Linux) sont sur le chemin d’accès exécutable pour votre environnement.

* Exécuter le script test.py de requête à partir de la ligne de commande pour exécuter les tests :

  ```cmd
  py query-test.py
  ```

* Lorsque le test est terminée, les résultats sont stockés les fichiers (CSV) spécifiées dans le fichier win.ini de configuration de la requête (nix.ini de la configuration de la requête) des valeurs de l’ensemble des séparées par des virgules. Vous pouvez utiliser Excel pour analyser et représenter ces données.


[Réglage des performances de réception de données pour Elasticsearch sur Azure]: guidance-elasticsearch-tuning-data-ingestion-performance.md
[Réglage d’agrégation des données et des performances des requêtes pour Elasticsearch sur Azure]: guidance-elasticsearch-tuning-data-aggregation-and-query-performance.md
[Création d’un environnement de test pour Elasticsearch sur Azure de performances]: guidance-elasticsearch-creating-performance-testing-environment.md
[Déploiement d’un échantillon de JMeter JUnit pour tester les performances de Elasticsearch]: guidance-elasticsearch-deploying-jmeter-junit-sampler.md
