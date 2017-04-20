 <properties
    pageTitle="Introduction à Hadoop - Nouveautés Hadoop sur HDInsight ? | Microsoft Azure"
    description="Obtenez une présentation Hadoop, traitement de données volumineux distribué et l’analyse et les composants de l’écosystème Hadoop dans le cloud sur HDInsight."
    keywords="analyse de données volumineux, présentation hadoop, ce qui est hadoop, technologique hadoop, hadoop réseau"
    services="hdinsight"
    documentationCenter=""
    authors="cjgronlund"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/21/2016"
   ms.author="cgronlun"/>


# <a name="an-introduction-to-the-hadoop-ecosystem-on-azure-hdinsight"></a>Présentation du réseau Hadoop sur Azure HDInsight

 Cet article fournit une introduction à Hadoop sur Azure HDInsight, son réseau et des données volumineuses. En savoir plus sur les composants Hadoop, terminologie commune et scénarios d’analyse de données volumineux.

## <a name="what-is-hadoop-on-hdinsight"></a>Qu’est Hadoop sur HDInsight ?

Hadoop fait référence à un réseau de logiciels libres est une infrastructure de traitement distribuées, le stockage et l’analyse des jeux de données volumineux sur groupes d’ordinateurs. Azure HDInsight rend les composants Hadoop à partir de la distribution **Hortonworks données plateforme (HDP)** disponibles dans le cloud et est déployée et dispositions clusters gérées avec une plus grande fiabilité et disponibilité.  

Hadoop Apache a été le projet open source d’origine pour traitement de données volumineux. Suivant a été le développement de logiciels et utilitaires considérés comme faisant partie de l’environnement technologique Hadoop, y compris Apache ruche, Apache HBase, Apache explosion et bien d’autres. Pour plus d’informations, voir [vue d’ensemble de l’écosystème Hadoop dans HDInsight](#overview) .

## <a name="what-is-big-data"></a>Que sont les données volumineux ?

Données volumineuses décrivent tout organisme volumineux d’informations numériques, à partir du texte dans un Twitter flux, aux informations à partir d’équipements industriels, capteur vers des informations sur le client de navigation et achats sur un site Web. Données volumineuses peuvent être historiques (ce qui signifie que les données stockées) ou en temps réel (c'est-à-dire diffusés en continu directement à partir de la source). Données volumineux sont collectées dans les volumes exponentielle, à des vitesses toujours plus rapidement et dans un développement divers formats.

Pour les données volumineux à fournir intelligents ou un aperçu, vous devez collecter les données pertinentes et poser les questions appropriées. Vous devez également Vérifiez que les données sont accessibles, nettoyé, analysées et puis présenté de façon utile. Il s’agit là analyse de données volumineux sur Hadoop dans HDInsight peut vous aider.

## <a name="overview"></a>Vue d’ensemble de l’écosystème Hadoop dans HDInsight

HDInsight est une distribution cloud sur Microsoft Azure de l’environnement technologique Apache Hadoop expansion rapide pour l’analyse de données volumineux. Il inclut les mises en œuvre d’explosion Apache, HBase, vague de, cochon, Hive, Sqoop, Oozie, Ambari et ainsi de suite. HDInsight intègre également des outils d’analyse décisionnelle (BI) tels que Power BI, Excel, SQL Server Analysis Services et SQL Server Reporting Services.

### <a name="hadoop-hbase-spark-storm-and-customized-clusters"></a>Hadoop, HBase, explosion, vague d’et clusters personnalisées

HDInsight fournit des configurations de cluster pour Apache Hadoop, explosion, HBase ou vague de. Ou bien, vous pouvez [Personnaliser clusters avec les actions de script](hdinsight-hadoop-customize-cluster-linux.md).

* **Hadoop**: offre un stockage de données fiable avec [HADOOP](#hdfs)et un modèle de programmation [MapReduce](#mapreduce) simple pour traiter et analyser des données en parallèle.

* **<a target="_blank" href="http://spark.apache.org/">Apache explosion</a>**: une infrastructure de traitement en parallèle qui prend en charge de traitement en mémoire pour améliorer les performances des applications d’analyse de données volumineux, créer du works pour SQL, flux de données et et d’apprentissage de l’ordinateur. Voir [vue d’ensemble : qu’est Apache explosion dans HDInsight ?](hdinsight-apache-spark-overview.md)

* **<a target="_blank" href="http://hbase.apache.org/">HBase</a>**: une base de données NoSQL basé sur Hadoop qui fournit un accès aléatoire et la cohérence forte de grandes quantités de données non structurées et semi-structurées - potentiellement milliards de lignes arrive millions de colonnes. Voir [vue d’ensemble des HBase sur HDInsight](hdinsight-hbase-overview.md).

* **<a  target="_blank" href="https://storm.incubator.apache.org/">Vague d’Apache</a>**: un système de calcul distribué et en temps réel pour le traitement des flux de données volumineux rapidement. Vague d’est proposée comme un cluster géré dans HDInsight. Voir [analyse des données en temps réel capteur à l’aide de vague d’et Hadoop](hdinsight-storm-sensor-data-analysis.md).

### <a name="example-customization-scripts"></a>Exemples de scripts de personnalisation

Actions de script sont des scripts qui s’exécutent pendant la mise en service de cluster et peuvent être utilisés pour installer des composants supplémentaires sur le cluster. Pour les clusters basés sur Linux, il s’agit des scripts Bash.

Les exemples de scripts suivants sont fournies par l’équipe HDInsight :

* [Teinte](hdinsight-hadoop-hue-linux.md): ensemble des applications web utilisé pour interagir avec un cluster. Linux cluster uniquement.

* [Giraph](hdinsight-hadoop-giraph-install-linux.md): traitement des relations entre des éléments ou des personnes d’un modèle de graphique.

* [R](hdinsight-hadoop-r-scripts-linux.md): un environnement pour le calcul statistique utilisée dans l’apprentissage automatique et langue open source.

* [Mode série sur LAN.r](hdinsight-hadoop-solr-install-linux.md): une plateforme de recherche à l’échelle de l’entreprise qui permet de recherche en texte intégral sur des données.

Pour plus d’informations sur le développement de vos propres Actions de Script, consultez [développement de Script Action avec HDInsight](hdinsight-hadoop-script-actions-linux.md).


## <a name="what-are-the-hadoop-components-and-utilities"></a>Quels sont les composants Hadoop et utilitaires ?

Les composants et utilitaires suivants sont inclus dans les clusters HDInsight.

* **[Ambari](#ambari)**: mise en service, gestion, surveillance et utilitaires de Cluster.

* **[Avro](#avro)** (Bibliothèque Microsoft .NET pour Avro) : sérialisation de données pour l’environnement Microsoft .NET.

* **[Hive & HCatalog](#hive)**: langage SQL (Structured Query)-comme l’interrogation et une couche de gestion de stockage et de tableau.

* **[Mahout](#mahout)**: pour machine scalable d’apprentissage applications.

* **[MapReduce](#mapreduce)**: framework héritée pour Hadoop distributed gestion des ressources et de traitement. Voir [fils](#yarn), la structure des ressources de la nouvelle génération.

* **[Oozie](#oozie)**: gestion des flux de travail.

* **[Phoenix](#phoenix)**: couche de base de données relationnelle sur HBase.

* **[Cochon](#pig)**: scripting plus simple pour les transformations MapReduce.

* **[Sqoop](#sqoop)**: importation et exportation de données.

* **[Tez](#tez)**: permet de processus de données accrue afin de s’exécuter à l’échelle.

* **[Fils](#yarn)**: partie de la bibliothèque principale Hadoop et la nouvelle génération de l’infrastructure de logiciel MapReduce.

* **[Soigneur](#zookeeper)**: débute des processus dans des systèmes distribués.

> [AZURE.NOTE] Des informations sur les composants spécifiques et des informations de version, voir [composants Hadoop, le contrôle de version et les offres de service dans HDInsight][component-versioning]

### <a name="ambari"></a>Ambari

Apache Ambari est pour la mise en service, gestion et analyse des clusters Hadoop Apache. Il inclut une collection de sites intuitive des outils de l’opérateur et un ensemble puissant d’API masquer la complexité de Hadoop, ce qui simplifie le fonctionnement des clusters. HDInsight basé sur Linux clusters offrent de la Ambari web interface utilisateur et l’API REST Ambari, tandis que les clusters basés sur Windows fournissent un sous-ensemble de l’API REST. Vues Ambari sur HDInsight clusters permettent aux fonctionnalités d’interface utilisateur du plug-in.

Voir [clusters HDInsight gérer à l’aide de Ambari](hdinsight-hadoop-manage-ambari.md) (Linux uniquement), [groupes Hadoop moniteur dans un HDInsight à l’aide de l’API Ambari](hdinsight-monitor-use-ambari-api.md)et <a target="_blank" href="https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md">Apache Ambari API référence</a>.

### <a name="avro"></a>Avro (bibliothèque Microsoft .NET pour Avro)

La bibliothèque Microsoft .NET pour Avro met en œuvre le format d’échange de données binaires compact Avro Apache pour la sérialisation pour l’environnement Microsoft .NET. Il utilise <a target="_blank" href="http://www.json.org/">La Notation JSON (JavaScript Object)</a> pour définir un schéma indépendant du langage qui souscrit interopérabilité entre les langages, ce qui signifie données sérialisées dans une langue peuvent être lus dans un autre. Vous trouverez des informations détaillées sur la mise en forme dans le < une cible = _ « vierge » href = « http://avro.apache.org/docs/current/spec.html » > Apache Avro spécification</a>.
Le format des fichiers Avro prend en charge le modèle de programmation MapReduce distribué. Les fichiers sont « fractionnables », ce qui signifie que vous pouvez rechercher n’importe quel point dans un fichier et démarrez lecture à partir d’un bloc particulier. Pour plus d’informations, voir [données Serialize avec la bibliothèque Microsoft .NET pour Avro](hdinsight-dotnet-avro-serialization.md).


### <a name="hdfs"></a>HADOOP

Système un fichier distribué Hadoop (HDFS) est un système de fichiers DFS qui, avec MapReduce et fils, est l’essentiel du réseau Hadoop. HADOOP s’agit du système de fichier standard pour clusters Hadoop sur HDInsight.

### <a name="hive"></a>Hive & HCatalog

<a target="_blank" href="http://hive.apache.org/">Apache ruche</a> est un logiciel de magasin de données basé sur Hadoop qui vous permet de la requête et gérer les grands ensembles de données dans le stockage distribué à l’aide d’un langage similaire à SQL appelé HiveQL. Hive, comme cochon, est une abstraction par-dessus MapReduce. Lorsque vous exécutez, Hive traduit des requêtes en une série de tâches MapReduce. Hive est conceptuel plus proche à un système de gestion de base de données relationnelle que cochon et convient pour une utilisation avec des données structurées plus. Pour les données non structurées, cochon est le meilleur choix. Voir [utiliser Hive avec Hadoop dans HDInsight](hdinsight-use-hive.md).

<a target="_blank" href="https://cwiki.apache.org/confluence/display/Hive/HCatalog/">Apache HCatalog</a> est une couche de gestion de table et de stockage pour Hadoop qui offre aux utilisateurs une vue de données relationnelle. Dans HCatalog, vous pouvez lire et écrire des fichiers au format pour lequel une SerDe ruche (sérialiseur désérialiseur) peuvent être écrites.

### <a name="mahout"></a>Mahout

<a target="_blank" href="https://mahout.apache.org/">Apache Mahout</a> est une bibliothèque scalable d’apprentissage algorithmes qui s’exécutent sur Hadoop de l’ordinateur. En utilisant les principes de calcul, les applications de formation machine enseignent systèmes pour en savoir à partir des données et permet de déterminer le comportement futur au-delà de résultats possibles. Voir [recommandations pour générer vidéo à l’aide de Mahout sur Hadoop](hdinsight-mahout.md).

### <a name="mapreduce"></a>MapReduce
MapReduce est l’infrastructure de logiciel hérité pour Hadoop pour écrire des applications pour une sélection de jeux de données volumineux de processus en parallèle. Un travail MapReduce fractionne les grands ensembles de données et organise les données en paires clé-valeur pour traitement.

[Fils](#yarn) est la structure des ressources de nouvelle génération Hadoop responsable et d’application et est appelé MapReduce 2.0. Tâches MapReduce s’exécuter sur fils.

Pour plus d’informations sur MapReduce, voir <a target="_blank" href="http://wiki.apache.org/hadoop/MapReduce">MapReduce</a> dans le Hadoop Wiki.

### <a name="oozie"></a>Oozie
<a target="_blank" href="http://oozie.apache.org/">Oozie Apache</a> est un système de débute de flux de travail qui gère les travaux Hadoop. Il est intégré à la pile Hadoop et prend en charge des travaux Hadoop pour MapReduce, cochon, Hive et Sqoop. Il peut également être utilisé pour planifier des tâches spécifiques à un système, tels que les programmes Java ou des scripts shell. Voir [utiliser Oozie avec Hadoop](hdinsight-use-oozie.md).

### <a name="phoenix"></a>Phoenix
<a  target="_blank" href="http://phoenix.apache.org/">Phoenix Apache</a> est un calque de base de données relationnelle sur HBase. Phoenix inclut un pilote JDBC qui permet aux utilisateurs de la requête et la gestion des tables SQL directement. Phoenix traduit les requêtes et les autres instructions dans des appels d’API de NoSQL - au lieu d’à l’aide de MapReduce - et donc permettant aux applications plus rapides en haut NoSQL stores. Voir [utiliser Apache Phoenix et non avec les clusters HBase](hdinsight-hbase-phoenix-squirrel.md).


### <a name="pig"></a>Cochon
<a  target="_blank" href="http://pig.apache.org/">Cochon Apache</a> est une plateforme de haut niveau que vous pouvez effectuer des transformations MapReduce complexes sur grands ensembles de données à l’aide d’un langage de script simple appelé cochon Latin. Cochon convertit les scripts Latin cochon afin qu’elles s’exécutent dans Hadoop. Vous pouvez créer des fonctions définies par l’utilisateur (UDF) pour étendre cochon Latin. Voir [utiliser cochon avec Hadoop](hdinsight-use-pig.md).

### <a name="sqoop"></a>Sqoop
<a  target="_blank" href="http://sqoop.apache.org/">Apache Sqoop</a> est outil que transferts en bloc des données entre Hadoop et bases de données relationnelles ce un SQL ou autres magasins de données structurées, plus efficacement possible. Voir [utiliser Sqoop avec Hadoop](hdinsight-use-sqoop.md).

### <a name="tez"></a>Tez
<a  target="_blank" href="http://tez.apache.org/">Apache Tez</a> est une infrastructure d’applications basée sur des fils Hadoop qui s’exécute graphiques complexes, acycliques général du traitement des données. Il est un successeur plus flexible et puissant à l’infrastructure MapReduce qui permet aux données accrue processus, tel que Hive, peuvent fonctionner plus efficacement à l’échelle. Voir [« Utilisation des Tez de Apache pour améliorer les performances » dans Utilisation ruche et HiveQL](hdinsight-use-hive.md#usetez).

### <a name="yarn"></a>FILS
FILS Apache est la nouvelle génération de MapReduce (MapReduce 2.0 ou MRv2) et prend en charge les scénarios de traitement des données au-delà MapReduce traitement par lots avec une flexibilité supérieure et traitement en temps réel. FILS fournit la gestion des ressources et une infrastructure d’applications distribuées. Tâches MapReduce s’exécuter sur fils.

Pour en savoir plus sur fils, voir <a target="_blank" href="http://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html">Apache Hadoop NextGen MapReduce (fils)</a>.


### <a name="zookeeper"></a>Soigneur
<a  target="_blank" href="http://zookeeper.apache.org/">Apache soigneur</a> coordonnées processus en gros systèmes distribués au moyen d’un espace de noms hiérarchique partagé de Register de données (znodes). Znodes contenir de petites quantités d’informations meta nécessaires pour coordonner des processus : état, emplacement, la configuration et ainsi de suite.

## <a name="programming-languages-on-hdinsight"></a>Langages de programmation sur HDInsight

Clusters HBase et vague d’explosion de clusters--Hadoop, HDInsight--prend en charge un certain nombre de langages de programmation, mais certains ne sont pas installés par défaut. Pour les bibliothèques, modules, ou packages ne pas installés par défaut, utilisent une action de script pour installer le composant. Consultez [Script action développement avec HDInsight](hdinsight-hadoop-script-actions-linux.md).

### <a name="default-programming-language-support"></a>Valeur par défaut de la prise en charge du langage de programmation

Par défaut, HDInsight clusters prise en charge :

* Java

* Python

Langues supplémentaires peuvent être installés à l’aide des actions de script : [développement d’action Script avec HDInsight](hdinsight-hadoop-script-actions-linux.md).

### <a name="java-virtual-machine-jvm-languages"></a>Langues de machine virtuelle (JVM) Java

Nombreuses langues que Java peuvent être exécutées à l’aide d’une machine virtuelle (machine virtuelle Java) ; Toutefois, certaines de ces langues en cours d’exécution peuvent nécessiter des composants supplémentaires installés sur le cluster.

Ces langues basées sur machine virtuelle Java sont prises en charge sur les clusters HDInsight :

* Clojure

* Jython (Python pour Java)

* Scala

### <a name="hadoop-specific-languages"></a>Langues Hadoop spécifiques

HDInsight clusters prennent en charge les langues suivantes sont spécifiques à la réseau Hadoop :

* Cochon Latin pour les travaux cochon

* HiveQL pour les tâches de Hive et SparkSQL


## <a name="advantage"></a>Avantages de Hadoop dans le cloud

Dans le cadre de l’écosystème Azure cloud, Hadoop dans HDInsight propose de nombreux avantages, parmi lesquels :

* La configuration automatique d’Hadoop cluster. HDInsight clusters sont plus faciles à créer que la configuration manuelle Hadoop clusters. Pour plus d’informations, voir [groupes de mise en service Hadoop dans HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

* Composants de Hadoop de pointe. Pour plus d’informations, voir [composants Hadoop, le contrôle de version et offres de service dans HDInsight][component-versioning].

* Disponibilité et fiabilité des clusters. Pour plus d’informations, voir [la disponibilité et la fiabilité des groupes Hadoop dans un HDInsight](hdinsight-high-availability-linux.md) .

* Stockage des données efficace et économique avec stockage d’objets Blob Azure, une option Hadoop compatibles. Pour plus d’informations, voir [utiliser Azure Blob storage avec Hadoop dans HDInsight](hdinsight-hadoop-use-blob-storage.md) .

* Intégration avec d’autres services Azure, y compris les [applications Web](https://azure.microsoft.com/documentation/services/app-service/web/) et la [Base de données SQL](https://azure.microsoft.com/documentation/services/sql-database/).

* Autres tailles machine virtuelle et types de clusters HDInsight en cours d’exécution. Voir [composants Hadoop, le contrôle de version et les offres de service dans HDInsight] [ component-versioning] pour plus d’informations.

* Mise à l’échelle de cluster. Cluster mise à l’échelle vous permet de modifier le nombre de nœuds d’un cluster de HDInsight en cours d’exécution sans avoir à supprimer ou le recréer.

* Prise en charge réseau virtuel. HDInsight clusters peuvent servir avec réseau virtuel Azure pour prendre en charge isolement de ressources de cloud ou des scénarios hybride lier des ressources de cloud avec celles dans votre centre de données.

* Entrée de coût. Démarrez une [version d’évaluation gratuite](https://azure.microsoft.com/free/)ou [HDInsight pour plus d’informations](https://azure.microsoft.com/pricing/details/hdinsight/), consultez.

Pour en savoir plus sur les avantages sur Hadoop dans HDInsight, reportez-vous à la [page fonctionnalités Azure pour HDInsight][marketing-page].

## <a name="hdinsight-standard-and-hdinsight-premium"></a>HDInsight Standard et Premium HDInsight

HDInsight fournit des offres en nuage données volumineuses dans deux catégories, Standard et Premium. HDInsight Standard fournit un cluster de grandes organisations peuvent utiliser pour exécuter leurs charges de travail données volumineuses. HDInsight Premium s’appuie sur qui et fournit avancées analytique et les fonctionnalités de sécurité pour un cluster HDInsight. Pour plus d’informations, voir [Azure HDInsight Premium](hdinsight-component-versioning.md#hdinsight-standard-and-hdinsight-premium)


## <a id="resources"></a>Ressources pour en savoir plus sur l’analyse de données volumineux, Hadoop et HDInsight

Appuyer sur cette présentation Hadoop dans le cloud et l’analyse de données volumineux avec les ressources suivantes.


### <a name="hadoop-documentation-for-hdinsight"></a>Documentation Hadoop de HDInsight

* [Documentation HDInsight](https://azure.microsoft.com/documentation/services/hdinsight/): la page de documentation pour Hadoop sur Azure HDInsight avec des liens vers des articles, des vidéos et des ressources supplémentaires.

* [Prise en main Hadoop dans HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md): un didacticiel de démarrage rapide pour la mise en service HDInsight Hadoop clusters et en exécutant des exemples de requêtes Hive.

* [Prise en main explosion dans HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md): un didacticiel de démarrage rapide pour la création d’un cluster explosion et en cours d’exécution des requêtes SQL explosion interactives.

* [Utiliser un serveur R sur HDInsight](hdinsight-hadoop-r-server-get-started.md): commencer à utiliser R serveur dans HDInsight Premium.

* [Mise en service HDInsight clusters](hdinsight-hadoop-provision-linux-clusters.md): Découvrez comment configurer un cluster HDInsight Hadoop via le portail Azure, Azure infrastructure du langage commun ou PowerShell Azure.


### <a name="apache-hadoop"></a>Hadoop Apache

* <a target="_blank" href="http://hadoop.apache.org/">Hadoop Apache</a>: en savoir plus sur la bibliothèque de logiciels Apache Hadoop, un cadre qui permet au traitement distribué de grands volumes de données au sein de groupes d’ordinateurs.

* <a target="_blank" href="http://hadoop.apache.org/docs/r1.0.4/hdfs_design.html">HADOOP</a>: en savoir plus sur l’architecture et la conception du Hadoop Distributed File System, le système de stockage principal utilisé par les applications Hadoop.

* <a target="_blank" href="http://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html">Didacticiel MapReduce</a>: en savoir plus sur la structure de programmation pour écrire des applications Hadoop qui traitent rapidement de grandes quantités de données en parallèle sur les grands groupes de nœuds de calcul.


### <a name="microsoft-business-intelligence"></a>Business intelligence de Microsoft

Outils de décision familiers entreprise - comme Excel, PowerPivot, SQL Server Analysis Services et SQL Server Reporting Services - récupérer, leur analyse et les données intégrées à HDInsight à l’aide du complément Power Query ou le pilote ODBC de Microsoft ruche des rapports.

Ces outils de décisionnel peuvent aider dans votre analyse de données volumineuses :

* [Connecter Excel à Hadoop avec Power Query](hdinsight-connect-excel-power-query.md): Découvrez comment connecter Excel sur le compte de stockage Azure qui stocke les données associées à votre cluster HDInsight à l’aide de Microsoft Power Query pour Excel. Poste de travail Windows obligatoire. Programme Works avec cluster fonctionnant sous Windows ou Linux.

* [Connecter Excel à Hadoop avec le pilote ODBC ruche de Microsoft](hdinsight-connect-excel-hive-odbc-driver.md): Découvrez comment importer des données à partir de HDInsight avec le pilote ODBC ruche de Microsoft. Poste de travail Windows obligatoire. Programme Works avec cluster fonctionnant sous Windows ou Linux.

* [Microsoft Cloud plateforme](http://www.microsoft.com/server-cloud/solutions/business-intelligence/default.aspx): en savoir plus sur Power BI pour Office 365, télécharger la version d’évaluation de SQL Server et configurer SharePoint Server 2013 et SQL Server BI.

* [SQL Server Analysis Services](http://msdn.microsoft.com/library/hh231701.aspx).

* [Outils SQL Server Reporting Services](http://msdn.microsoft.com/library/ms159106.aspx).




[marketing-page]: https://azure.microsoft.com/services/hdinsight/
[component-versioning]: hdinsight-component-versioning.md
[zookeeper]: http://zookeeper.apache.org/
