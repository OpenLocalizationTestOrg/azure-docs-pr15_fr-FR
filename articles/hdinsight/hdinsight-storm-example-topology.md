<properties
 pageTitle="Exemples de topologies vague Apache sur HDInsight | Microsoft Azure"
 description="Liste des exemples de topologies vague créé et testé avec vague d’Apache sur HDInsight, y compris des topologies c# et Java de base et l’utilisation de Hubs événement."
 services="hdinsight"
 documentationCenter=""
 authors="Blackmist"
 manager="jhubbard"
 editor="cgronlun"
    tags="azure-portal"/>

<tags
 ms.service="hdinsight"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="big-data"
 ms.date="08/23/2016"
 ms.author="larryfr"/>

# <a name="example-storm-toplogies-and-components-for-apache-storm-on-hdinsight"></a>Exemple vague toplogies et des composants de vague d’Apache sur HDInsight

Voici une liste d’exemples créées et gérées par Microsoft pour une utilisation avec vague d’Apache sur HDInsight. Ces exemples couvrent une variété de sujets, de la création de base c# et topologies Java à l’utilisation des services Azure tels que les événements Hubs, DocumentDB, Power BI, base de données SQL, HBase sur HDInsight et le stockage Azure. Voici quelques exemples illustrent également comment travailler avec les technologies non Azure ou même non Microsoft, tels que SignalR et Socket.IO

| Description                                                                                             | Montre                                         | Langage ou la structure         |
|:--------------------------------------------------------------------------------------------------------|:-----------------------------------------------------|:---------------------------|
| [Écrire sur Azure données Lake Store vague d’Apache](hdinsight-storm-write-data-lake-store.md) | Écriture au magasin Lake des données Azure | Java |
| [Événement concentrateur bec et boulons source](https://github.com/apache/storm/tree/master/external/storm-eventhubs) | Source de l’événement concentrateur bec et boulons | Java |
| [Développer des topologies basé sur Java pour vague d’Apache sur HDInsight][5797064f]                                 | Maven                                                | Java                       |
| [Développer des topologies c# pour vague d’Apache sur HDInsight à l’aide de Visual Studio][16fce2d1]                     | HDInsight Tools pour Visual Studio                    | C#, Java                   |
| [Créer plusieurs flux de données dans une topologie c# vague][ec5a4064]                                         | Plusieurs flux                                     | C#                         |
| [Déterminer les sujets actuels Twitter avec vague d’HDInsight][3c86c7c8]                                   | Trident                                              | Java, Trident              |
| [Traiter les événements de Azure événement Hubs avec vague d’HDInsight (c#)][844d1d81]                                | Événement Hubs                                           | C# et Java                |
| [Traiter les événements de Azure événement Hubs avec vague d’HDInsight (Java)](hdinsight-storm-develop-java-event-hub-topology.md) | Événement Hubs | Java |
| [Permet de visualiser des données à partir d’une topologie vague Power Bi][94d15238]                              | Power BI                                             | C#                         |
| [Analyse des données de capteur avec vague d’et HBase dans HDInsight][ab894747]                                     | Événement Hubs, HBase, Socket.IO, tableau de bord Web          | C#, Java, JavaScript, HTML |
| [Traiter des données de capteur véhicule à partir de l’événement Hubs vague d’associés à HDInsight][246ee964]                        | Événement Hubs, DocumentDb, stockage Azure Blob (WASB)    | C#, Java                   |
| [Extraire, transformation et chargement (ETL) à partir d’Azure événement Hubs à HBase, à l’aide de vague de sur HDInsight][b4b68194] | Événement Hubs, HBase                                    | C#                         |
| [Modèle de projet c# vague topologie pour l’utilisation de services Azure de vague d’HDInsight][ce0c02a2]  | Événement Hubs, DocumentDb, SQL de base de données, HBase, SignalR | C#, Java                   |
| [Tests d’évaluation extensibilité élevées pour lire à partir d’Azure événement Hubs vague d’associés à HDInsight][d6c540e3]           | Débit des messages, événements Hubs, base de données SQL         | C#, Java                   |
| [Faire correspondre les événements à l’aide de vague d’et HBase sur HDInsight](hdinsight-storm-correlation-topology.md) | HBase | C# |
| [Utiliser les Python avec vague d’HDInsight](hdinsight-storm-develop-python-topology.md) | Composants de Python avec Java et Clojure basés topologies vague | Python |

## <a name="next-steps"></a>Étapes suivantes

* [Prise en main vague d’Apache sur HDInsight][2b8c3488]

* [Apprenez à déployer et gérer des topologies vague avec vague d’HDInsight][6eb0d3b8]

  [2b8c3488]: hdinsight-apache-storm-tutorial-get-started-linux.md "Apprenez à créer une vague de sur cluster HDInsight et utiliser le tableau de bord vague de pour déployer des exemples de topologies."
  [6eb0d3b8]: hdinsight-storm-deploy-monitor-topology.md "Apprenez à déployer et gérer des topologies à l’aide des outils de HDInsight et vague d’interface utilisateur ou le tableau de bord vague de basée sur le web pour Visual Studio."
  [16fce2d1]: hdinsight-storm-develop-csharp-visual-studio-topology.md "Découvrez comment créer des topologies c# vague en utilisant les outils HDInsight pour Visual Studio."
  [5797064f]: hdinsight-storm-develop-java-topology.md "Apprenez à créer des topologies vague dans Java, à l’aide de Maven, en créant une topologie de base wordcount."
  [94d15238]: hdinsight-storm-power-bi-topology.md "Montre comment écrire des données dans Power BI à partir d’une topologie c#, puis créer un tableau de bord et un graphique à partir des données."
  [ec5a4064]: https://github.com/Blackmist/csharp-storm-example "Montre une topologie vague simple qui effectue une wordcount implémenté en c#. Cela montre également comment créer plusieurs flux de données au sein d’une topologie c#."
  [844d1d81]: hdinsight-storm-develop-csharp-event-hub-topology.md "Apprenez à lire et écrire des données à partir d’Azure événement Hubs avec vague de sur HDInsight."
  [ab894747]: hdinsight-storm-sensor-data-analysis.md "Découvrez comment utiliser vague d’Apache sur HDInsight pour traiter les données du capteur à partir d’Azure événement Hubs, à l’aide de D3.js visualiser et (facultatif), stockez-le à HBase."
  [3c86c7c8]: hdinsight-storm-twitter-trending.md "Découvrez comment utiliser Trident pour créer une topologie vague qui détermine les sujets actuels (basés sur hashtags,) sur Twitter."
  [246ee964]: hdinsight-storm-iot-eventhub-documentdb.md "Découvrez comment utiliser une topologie vague pour lire les messages à partir d’Azure événement Hubs, lire des documents à partir d’Azure DocumentDB pour faire référence à des données et enregistrer les données au stockage Azure."
  [d6c540e3]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/EventCountExample "Plusieurs topologies pour illustrer débit lors de la lecture à partir d’Azure événement Hubs et stocker à SQL de base de données à l’aide de vague d’Apache sur HDInsight."
  [b4b68194]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample "Apprenez à lire les données à partir d’Azure événement Hubs, agrégation et transformer les données, puis stocker dans HBase sur HDInsight."
  [ce0c02a2]: https://github.com/hdinsight/hdinsight-storm-examples/tree/master/templates/HDInsightStormExamples "Ce projet contient des modèles de becs verseurs, boulons et topologies pour interagir avec les différents services Azure comme événement Hubs, DocumentDB et base de données SQL."
 
