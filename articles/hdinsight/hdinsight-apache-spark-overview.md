<properties 
    pageTitle="Une vue d’ensemble d’explosion Apache dans HDInsight | Microsoft Azure" 
    description="Présentation de scénarios dans lesquelles vous pouvez utiliser explosion sur HDInsight dans vos applications et Apache explosion dans HDInsight." 
    services="hdinsight" 
    documentationCenter="" 
    authors="nitinme" 
    manager="jhubbard" 
    editor="cgronlun"
    tags="azure-portal"/>

<tags 
    ms.service="hdinsight" 
    ms.workload="big-data" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="08/25/2016" 
    ms.author="nitinme"/>

# <a name="overview-apache-spark-on-hdinsight-linux"></a>Vue d’ensemble : Apache explosion sous HDInsight Linux
 
<a href="http://spark.apache.org/" target="_blank">Explosion Apache</a> est un traitement framework qui prend en charge de traitement en mémoire pour améliorer les performances des applications analytiques données volumineuses en parallèle open source. Moteur de traitement et intégré pour la vitesse, facilité d’utilisation et analytique complexes. Fonctionnalités de calcul en mémoire d’explosion rendent un bon choix pour algorithmes itératifs dans les calculs de graphique et l’apprentissage machine. Explosion est également compatible avec stockage d’objets Blob Azure (WASB) afin que vos données existantes stockées dans Azure peuvent être facilement traitées via explosion.

Lorsque vous créez un cluster explosion dans HDInsight, vous créez des ressources de calcul Azure avec explosion installé et configuré. Il suffit d’environ 10 minutes pour créer un cluster explosion dans HDInsight. Les données soient traités sont stockées dans le stockage Blob Azure. Voir [utiliser le stockage Blob Azure avec HDInsight][hdinsight-storage].

![Explosion Apache sur Azure HDInsight] (./media/hdinsight-apache-spark-overview/hdispark.architecture.png  "Explosion Apache sur Azure HDInsight")


**Vous voulez commencer à utiliser explosion Apache sur Azure HDInsight ?** Voir [démarrage rapide : créer un cluster explosion sur HDInsight Linux et exécuter des exemples d’applications à l’aide de Jupyter](hdinsight-apache-spark-jupyter-spark-sql.md).

>[AZURE.NOTE] Pour obtenir la liste des problèmes connus et limitations avec la version actuelle, voir [problèmes de Apache explosion dans Azure HDInsight (Linux)](hdinsight-apache-spark-jupyter-spark-sql.md).


## <a name="why-use-spark-on-azure-hdinsight"></a>Pourquoi utiliser explosion sur Azure HDInsight ? 

Azure HDInsight propose un service d’explosion entièrement géré. Avantages de l’utilisation d’explosion sur HDInsight sont les suivantes :

| Fonctionnalité                             | Description       |
|-------------------------------------|-------------------|
| Faciliter la création de clusters            | Vous pouvez créer un nouveau cluster explosion sur HDInsight en minutes à l’aide du portail de gestion Azure, PowerShell Azure ou le Kit de développement .NET HDInsight. Voir [prise en main cluster explosion dans HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md) |
| Facilité d’utilisation                     | Explosion dans HDInsight clusters inclut des blocs-notes Jupyter préconfigurés. Vous pouvez les utiliser pour traitement de données interactive et de visualisation. L’URL pour l’est https://CLUSTERNAME.azurehdinsight.net/jupyter. Remplacez __NOMDUCLUSTER__ par le nom de votre cluster explosion HDInsight.|
| API REST                       | Explosion dans HDInsight inclut [Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server), l’API REST basé sur serveur de travaux explosion pour envoyer à distance et contrôler les tâches en cours d’exécution. |
| Prise en charge de magasin de Lake données Azure | Explosion sur HDInsight peut être configurée pour utiliser le magasin de Lake données Azure comme un espace de stockage supplémentaire. Pour plus d’informations sur les données Lake Store, voir [Vue d’ensemble du magasin de Lake des données Azure](../data-lake-store/data-lake-store-overview.md).
| Intégration à des services Azure | Explosion sur HDInsight est fourni avec un connecteur à Azure événement Hubs. Les utilisateurs peuvent générer des applications en continu à l’aide de l’événement Hubs, en plus de [Kafka](http://kafka.apache.org/), qui est déjà disponible dans le cadre d’explosion. |
| Prise en charge pour le serveur R  | Vous pouvez configurer un serveur R sur cluster HDInsight Spark pour exécuter des calculs de R distribués avec la vitesse prévue avec un cluster explosion. Pour plus d’informations, voir [prise en main à l’aide de serveur R sur HDInsight](hdinsight-hadoop-r-server-get-started.md).   |
| Intégration avec IntelliJ idée  | Vous pouvez utiliser le HDInsight Plugin pour IntelliJ pour créer et soumettre des applications à un cluster HDInsight Spark. Pour plus d’informations, voir [Utiliser HDInsight outils plug-in idée IntelliJ créer des applications pour HDInsight explosion Linux cluster explosion](hdinsight-apache-spark-intellij-tool-plugin.md). |
| Demandes simultanées              | Explosion dans HDInsight prend en charge les demandes simultanées. Cela permet de plusieurs requêtes à partir d’un utilisateur ou de plusieurs requêtes à partir de différents utilisateurs et applications de partager les mêmes ressources cluster. |
| La mise en cache sur SSDs                 | Vous pouvez choisir en cache des données en mémoire ou dans SSDs joints aux nœuds du cluster. La mise en cache en mémoire fournit les meilleures performances de requête, mais peut être coûteux ; la mise en cache dans SSDs fournit une option intéressante pour améliorer les performances de requête sans avoir besoin de créer un cluster dont la taille est requise pour l’ajuster à l’ensemble du dataset en mémoire.|
| Intégration avec les outils de décisionnel       | Explosion pour HDInsight fournit des liens pour outils de décisionnel tels que [Power BI](http://www.powerbi.com/) et [Tableau](http://www.tableau.com/products/desktop) pour analytique de données.|
| Bibliothèques Anaconda prédéfinis        | Explosion clusters sur HDInsight fournis avec les bibliothèques Anaconda préinstallés. [Anaconda](http://docs.continuum.io/anaconda/) prévoit près de 200 bibliothèques apprentissage automatique, analyse des données, visualisation, etc..|
| Extensibilité élevées                     | Bien que vous pouvez spécifier le nombre de nœuds dans votre cluster lors de la création, vous souhaiterez peut-être agrandir ou réduire le cluster pour correspondre à la charge de travail. Tous les clusters HDInsight permettent de modifier le nombre de nœuds dans le cluster. En outre, clusters explosion peuvent être supprimées avec aucune perte de données dans la mesure où toutes les données sont stockées dans le stockage Blob Azure. |
| Prise en charge 24/7                    | Explosion sur HDInsight est fourni avec prise en charge de niveau de l’entreprise 24/7 et accord de 99,9 % le temps.|



## <a name="what-are-the-use-cases-for-spark-on-hdinsight"></a>Quels sont les exemples d’utilisation pour explosion sur HDInsight ?

Apache explosion dans HDInsight permet les scénarios suivants.

### <a name="interactive-data-analysis-and-bi"></a>Analyse de données interactive et BI

[Consulter un didacticiel](hdinsight-apache-spark-use-bi-tools.md)

Apache explosion dans HDInsight stocke les données dans des objets BLOB Azure. Experts professionnels et principaux décideurs pouvant analyser et créer des rapports sur ces données et utiliser Microsoft Power BI pour créer des rapports interactifs à partir des données analysées. Analystes démarrer à partir des données structurée non structurées/semi-transparent dans le stockage Azure, définir un schéma pour les données à l’aide des blocs-notes et générer des modèles de données à l’aide de Microsoft Power BI. Explosion dans HDInsight prend également en charge plusieurs outils tiers BI tels que Tableau Qlikview et Lumira SAP rendant une solution idéale pour les analystes de données, experts professionnels et principaux décideurs.

### <a name="iterative-machine-learning"></a>Apprentissage automatique itératif

[Consulter un didacticiel : prévoir création les températures en utilisant les données HVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

[Consulter un didacticiel : prévoir les résultats de l’inspection alimentaires](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

Explosion Apache est fourni avec [MLlib](http://spark.apache.org/mllib/), un apprentissage bibliothèque basée sur explosion de l’ordinateur. En outre, explosion sur HDInsight inclut également Anaconda, une distribution Python constituée de plusieurs packages d’apprentissage automatique. Combinez cette solution avec une prise en charge intégrée pour les blocs-notes Jupyter, et vous disposez d’un environnement haut de gamme pour créer des applications d’apprentissage automatique.  

### <a name="streaming-and-real-time-data-analysis"></a>Analyse des données en continu et en temps réel

[Consulter un didacticiel](hdinsight-apache-spark-eventhub-streaming.md)

Analyse des données en temps réel est utilisé pour les scénarios allant réduisant le temps d’analyse de données de traitement des données qu’il arrive, à la création d’un véritable solution la diffusion. Explosion dans HDInsight offre une prise en charge complète pour créer des solutions analytique en temps réel. Alors qu’explosion a déjà connecteurs pour ajouter des données provenant de nombreuses sources telles que Kafka, Flume, Twitter, ZeroMQ ou TCP sockets, explosion dans HDInsight ajoute une prise en charge exhaustive pour traiter les données à partir d’Azure événement Hubs. Événement Hubs sont le service files d’attente plus couramment utilisé sur Azure. Avoir une prise en charge de prédéfinies pour événement Hubs rend au service dans HDInsight une solution idéale pour créer le pipeline d’analytique en temps réel.

##<a name="next-steps"></a>Quels composants sont inclus dans le cadre d’un cluster explosion ?

Explosion dans HDInsight inclut les composants suivants sont disponibles sur les groupes par défaut.

- [Explosion Core](https://spark.apache.org/docs/1.5.1/). Inclut explosion Core, explosion SQL, commandé flux API, GraphX et MLlib.
- [Anaconda](http://docs.continuum.io/anaconda/)
- [Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server)
- [Bloc-notes Jupyter](https://jupyter.org)

Explosion dans HDInsight fournit également un [pilote ODBC](http://go.microsoft.com/fwlink/?LinkId=616229) pour la connectivité à des groupes d’explosion dans un HDInsight dans les Outils BI tels que Microsoft Power BI et Tableau.

## <a name="where-do-i-start"></a>Où commencer ?

Précédez la création d’un cluster explosion sur HDInsight Linux. Voir [démarrage rapide : créer un cluster explosion sur HDInsight Linux et exécuter des exemples d’applications à l’aide de Jupyter](hdinsight-apache-spark-jupyter-spark-sql.md). 

## <a name="next-steps"></a>Étapes suivantes

### <a name="scenarios"></a>Scénarios

* [Explosion avec BI : effectuer une analyse de données interactives à l’aide d’explosion dans HDInsight avec les outils de décisionnel](hdinsight-apache-spark-use-bi-tools.md)

* [Explosion avec apprentissage automatique : utilisation explosion dans HDInsight pour analyser la température de construction à l’aide de données HVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Explosion avec apprentissage automatique : utilisation explosion dans HDInsight pour prévoir des résultats de l’inspection alimentaires](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Diffusion en continu explosion : Utilisation explosion dans HDInsight pour la création d’applications en continu en temps réel](hdinsight-apache-spark-eventhub-streaming.md)

* [Analyse de journal de site Web à l’aide d’explosion dans HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Créer et exécuter des applications

* [Créer une application autonome à l’aide de Scala](hdinsight-apache-spark-create-standalone-application.md)

* [Exécution de tâches à distance sur un cluster explosion à l’aide de Livy](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Outils et extensions

* [Plug-in des outils HDInsight IntelliJ idée permet de créer et soumettre des applications Scala d’explosion](hdinsight-apache-spark-intellij-tool-plugin.md)

* [Plug-in utilisation HDInsight outils idée IntelliJ déboguer applications explosion à distance](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)

* [Utiliser les blocs-notes Zeppelin avec un cluster explosion sur HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [Noyaux disponibles pour Jupyter bloc-notes cluster explosion pour HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)

* [Utiliser les packages externes avec Jupyter blocs-notes](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

* [Installer Jupyter sur votre ordinateur et vous connecter à un cluster HDInsight Spark](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gérer les ressources

* [Gestion des ressources pour le cluster Apache explosion dans Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

* [Suivre et débogage travaux s’exécutant sur un cluster Apache explosion dans HDInsight](hdinsight-apache-spark-job-debugging.md)


[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
