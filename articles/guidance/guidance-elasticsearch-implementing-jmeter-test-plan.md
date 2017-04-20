<properties
   pageTitle="Mise en œuvre d’un plan de test JMeter pour Elasticsearch | Microsoft Azure"
   description="Pour exécuter les performances des tests pour Elasticsearch avec JMeter."
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
   ms.author="masashin" />
   
# <a name="implementing-a-jmeter-test-plan-for-elasticsearch"></a>Mise en œuvre d’un plan de test JMeter pour Elasticsearch

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

Cet article fait [partie d’une série](guidance-elasticsearch.md). 

Les tests de performances menés contre Elasticsearch ont été implémentées à l’aide de plans de test JMeter avec code Java inséré en tant qu’un test JUnit permettant d’effectuer des tâches telles que le téléchargement de données dans le cluster. Les plans de test et le code de JUnit sont décrites dans les [performances de réception de données de réglage pour Elasticsearch sur Azure][]et [réglage agrégation des données et les performances des requêtes pour Elasticsearch sur Azure][].

L’objectif de ce document est à synthétiser l’expérience clé acquise de la création et en cours d’exécution ces plans de test. La page [JMeter recommandations](http://jmeter.apache.org/usermanual/best-practices.html) sur le site Web Apache JMeter contient plus GRG conseils sur l’utilisation de JMeter efficacement.

## <a name="implementing-a-jmeter-test-plan"></a>Mise en œuvre d’un plan de test JMeter

La liste suivante résume les éléments que vous devez prendre en compte lors de la création d’un plan de test JMeter :

- Créer un groupe de threads distincte pour chaque test que vous souhaitez effectuer. Un test peut contenir plusieurs étapes, y compris des automates, échéances, avant et processeurs postérieures à la, échantillonnages et récepteurs.

- Évitez de créer trop de threads dans un groupe de threads. Un trop grand nombre de threads entraînera JMeter échec avec « Se déconnecter de mémoire » exceptions. Il est préférable d’ajouter d’autres serveurs subordonnés JMeter chacun d’eux exécutant un plus petit nombre de threads que vous essayez d’exécuter un grand nombre de threads sur un serveur JMeter unique.

![](./media/guidance-elasticsearch/jmeter-testing1.png)

- Pour évaluer les performances du cluster, intégrer le plug-in [Analyseur de performances métriques collecteur](http://jmeter-plugins.org/wiki/PerfMon/) dans le plan de test. Il s’agit d’un récepteur JMeter qui est disponible dans l’un des plug-ins JMeter standards. Enregistrer les données de performance brute dans un jeu de fichiers au format de valeurs séparées par des virgules (CSV) et traiter les lorsque le test est terminé. Il est plus efficace et impose moins OCULAIRE sur JMeter que vous tentez de traiter les données qu’il est capturé. 

![](./media/guidance-elasticsearch/jmeter-testing2.png)

Vous pouvez utiliser un outil tel Excel pour importer les données et générer une plage de graphiques fins d’analyse.

Vous pouvez capturer les informations suivantes :

- Utilisation de l’UC de tous les nœuds du cluster Elasticsearch.

- Le nombre d’octets lus par seconde à partir du disque pour tous les nœuds.

- Si possible, le pourcentage de temps processeur passé à attendre e/s devant être effectuées sur chaque nœud. Ce n’est pas toujours possible pour les machines virtuelles Windows, mais vous pouvez créer une mesure personnalisée (une mesure de l’exécution) qui s’exécute la commande shell suivante pour appeler *vmstat* sur un nœud pour Linux :

```Shell
sh:-c:vmstat 1 5 | awk 'BEGIN { line=0;total=0;}{line=line+1;if(line&gt;1){total=total+\$16;}}END{print total/4}'
```

Champ 16 dans la sortie de *vmstat* contient la durée du processeur passée en attente d’e/s. Pour plus d’informations sur le fonctionne de cette déclaration, consultez la [commande vmstat](http://linuxcommand.org/man_pages/vmstat8.html).

- Le nombre d’octets envoyés et reçus via le réseau à chaque nœud.

- Récepteurs de rapport d’agrégation distincts permet d’enregistrer les performances et la fréquence des opérations réussies et non. Capturer les données échec et de succès dans différents fichiers.

![](./media/guidance-elasticsearch/jmeter-testing3.png)

- Conserver chaque cas de test JMeter plus simple possible pour vous permettent de corrélation directement des performances avec les actions de test spécifique. Dans les cas de test qui nécessitent une logique complexe, envisagez d’encapsuler cette logique dans un test JUnit et utilisez l’échantillon de demande de JUnit JMeter pour exécuter le test.

- L’échantillon de demande HTTP permet d’effectuer les opérations HTTP, tels que GET, POST, placer ou supprimer. Par exemple, vous pouvez exécuter Elasticsearch recherches en utilisant une requête POST et en fournissant les détails de la requête dans la zone de *Données relatives au corps* :

![](./media/guidance-elasticsearch/jmeter-testing4.png)

- Pour faciliter la répétabilité et réutiliser, paramétrer JMeter test plans de test. Vous pouvez ensuite utiliser l’écriture de script pour automatiser l’exécution des plans de test.

## <a name="implementing-a-junit-test"></a>Mise en œuvre d’un test JUnit

Vous pouvez incorporer code complexe dans un plan de test JMeter en créant un ou plusieurs tests JUnit. Vous pouvez écrire un test JUnit en utilisant un environnement de développement intégré (IDE) Java tels que Eclipse. [Déploiement d’un échantillon de JMeter JUnit pour tester les performances de Elasticsearch][] fournit des informations sur la façon de configurer un environnement de développement approprié.

La liste suivante répertorie quelques pratiques recommandées que vous devez suivre lors de l’écriture du code d’un test JUnit :

- Utilisez le constructeur de classe de test pour passer des paramètres de l’initialisation dans le test. JMeter pouvez utiliser un constructeur qui prend un argument de chaîne unique. Dans le constructeur, analyser cet argument dans ses éléments individuels, comme le montre l’exemple suivant :

```Java
private String hostName = "";
private String indexName = "";
private String typeName = "";
private int port = 0;
private String clusterName = "";
private int itemsPerBatch = 0;

/\* JUnit test class constructor \*/
public ElasticsearchLoadTest2(String params) {
    /* params is a string containing a set of comma separated values for:
        hostName
        indexName
        typeName
        port
        clustername
        itemsPerBatch
    */

    /* Parse the parameter string into an array of string items */
    String delims = "\[ \]\*,\[ \]\*"; // comma surrounded by zero or more spaces
    String\[\] items = params.split(delims);

    /* Note: Parameter validation code omitted */

    /* Use the parameters to populate variables used by the test */
    hostName = items[0];
    indexName = items[1];
    typeName = items[2];
    port = Integer.parseInt(items[3]);
    clusterName = items[4];
    itemsPerBatch = Integer.parseInt(items[5]);

    if(itemsPerBatch == 0)
        itemsPerBatch = 1000;
}
```

- Évitez d’opérations e/s ou autres opérations beaucoup de temps dans le constructeur ou classe de test d’installation, car ils exécutent chaque fois que le test JUnit s’exécute. (Le même test JUnit peut s’exécuter des milliers d’heures pour chaque test de performances exécutés depuis JMeter).

- Envisagez d’utiliser le programme d’installation unique pour l’initialisation coûteux cas de test.

- Si le test requiert un grand nombre de paramètres d’entrée, stocker les informations de configuration de test dans un fichier de configuration distinct et transmettre l’emplacement de ce fichier dans le constructeur.

- Évitez irréversible les chemins d’accès dans le code de chargement de test. Il peuvent entraîner des erreurs en raison des différences entre les systèmes d’exploitation tels que Windows et Linux.

- Utiliser des assertions pour indiquer les méthodes d’essai échecs dans JUnit afin que vous puissiez suivez-les avec JMeter et les utiliser en tant que des mesures d’une entreprise. Si possible, transmettre des informations concernant la cause du problème, comme illustré en gras dans l’exemple suivant :

```Java
@Test
public void bulkInsertTest() throws IOException {
    ...
    BulkResponse bulkResponse = bulkRequest.execute().actionGet();
    assertFalse(
        bulkResponse.buildFailureMessage(), bulkResponse.hasFailures());
        ...
}
```


[Running Elasticsearch on Azure]: guidance-elasticsearch-running-on-azure.md
[Réglage des performances de réception de données pour Elasticsearch sur Azure]: guidance-elasticsearch-tuning-data-ingestion-performance.md
[Déploiement d’un échantillon de JMeter JUnit pour tester les performances de Elasticsearch]: guidance-elasticsearch-deploying-jmeter-junit-sampler.md
[Réglage d’agrégation des données et des performances des requêtes pour Elasticsearch sur Azure]: guidance-elasticsearch-tuning-data-aggregation-and-query-performance.md
