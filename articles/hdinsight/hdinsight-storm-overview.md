<properties
    pageTitle="Introduction à la vague d’Apache sur HDInsight | Microsoft Azure"
    description="Obtenez une présentation vague d’Apache et découvrez comment vous pouvez utiliser vague de sur HDInsight pour créer des solutions d’analytique de données en temps réel dans le cloud."
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/11/2016"
   ms.author="larryfr"/>

#<a name="introduction-to-apache-storm-on-hdinsight-real-time-analytics-for-hadoop"></a>Introduction à la vague d’Apache sur HDInsight : analytique en temps réel pour Hadoop

Vague d’Apache sur HDInsight vous permet de créer des solutions analytique distribué et en temps réel dans l’environnement Azure à l’aide de [Apache Hadoop](http://hadoop.apache.org).

##<a name="what-is-apache-storm"></a>Qu’est vague d’Apache ?

Vague d’Apache est un système de calcul distribué, tolérance, open source qui permet de traiter les données en temps réel avec Hadoop. Solutions vague peuvent également fournir garantie traitement des données, avec la possibilité de relire les données qui n’a pas été traitées la première fois.

##<a name="why-use-storm-on-hdinsight"></a>Pourquoi utiliser vague de sur HDInsight ?

Vague d’Apache sur HDInsight est un cluster géré intégré à l’environnement Azure. Il fournit les avantages suivants :

* Fonctionne comme un service géré avec accord de 99,9 % le temps

* Utiliser la langue de votre choix : prend en charge les composants vague écrits en **Java**, **c#**et **Python**

    * Prend en charge un mélange de langages de programmation : lire les données à l’aide de Java, puis traiter à l’aide de C#
    
        > [AZURE.NOTE] C# topologies sont uniquement pris en charge sur les clusters HDInsight fonctionnant sous Windows.

    * Utiliser l’interface de Java **Trident** pour créer des topologies vague qui prennent en charge « une seule fois » traitement des messages, persistance de magasin de données « transactions » et un ensemble d’opérations d’analytique flux communs

* Comprend des fonctionnalités intégrées d’échelle en haut et en bas de l’échelle : mettre à l’échelle un cluster HDInsight avec aucun impact topologies vague en cours d’exécution

* Intégrer avec d’autres services Azure événement concentrateur, réseau virtuel Azure, base de données SQL, stockage d’objets Blob et DocumentDB inclus.

    * Combiner les fonctionnalités des multi-clusters HDInsight à l’aide de réseau virtuel Azure : créer des pipelines analytiques qui utilisent HDInsight, HBase ou Hadoop clusters

Pour une liste des sociétés qui utilisent vague d’Apache par leurs solutions analytique en temps réel, voir [Vague d’Apache à l’aide de sociétés](https://storm.apache.org/documentation/Powered-By.html).

Pour commencer à utiliser vague de, voir [prise en main vague d’HDInsight][gettingstarted].

###<a name="ease-of-provisioning"></a>Facilité de mise en service

Vous pouvez configurer un nouveau vague de sur cluster HDInsight en minutes. Spécifiez le nom du cluster, taille, compte d’administrateur et le compte de stockage. Azure créera le cluster, y compris les exemples de topologies et un tableau de bord de gestion web.

> [AZURE.NOTE] Vous pouvez également configurer clusters vague à l’aide de [l’Infrastructure du langage commun Azure](../xplat-cli-install.md) ou [PowerShell Azure](../powershell-install-configure.md).

Dans les 15 minutes de l’envoi de la demande, vous aurez un nouveau cluster vague en cours d’exécution et pipeline de prêt pour votre première analytique en temps réel.

###<a name="ease-of-use"></a>Facilité d’utilisation

__Vague de pour Linux sur clusters HDInsight__, vous pouvez vous connecter au cluster à l’aide de SSH et l’utilisation du `storm` commande pour démarrer et gérer des topologies. En outre, vous pouvez utiliser Ambari pour contrôler le service vague et l’interface utilisateur vague de pour surveiller et gérer des topologies en cours d’exécution.

Pour plus d’informations sur l’utilisation des clusters vague basé sur Linux, voir [prise en main vague d’Apache sur HDInsight basé sur Linux](hdinsight-apache-storm-tutorial-get-started-linux.md).

__Vague de pour Windows sur les clusters HDInsight__, les outils de HDInsight pour Visual Studio permettent de créer c# et hybride c# / topologies Java, afin de les envoyer à votre vague de sur cluster HDInsight.  

![Création d’un projet vague](./media/hdinsight-storm-overview/createproject.png)

HDInsight Tools pour Visual Studio fournit une interface qui vous permet de surveiller et de gérer des topologies vague sur un cluster.

![Gestion des vague](./media/hdinsight-storm-overview/stormview.png)

Pour obtenir un exemple de l’aide des outils HDInsight pour créer une application vague, voir [topologies c# vague de développer avec les outils de HDInsight pour Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md).

Pour plus d’informations sur les outils HDInsight pour Visual Studio, voir [commencer à utiliser les outils de HDInsight pour Visual Studio](../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md).

Chaque vague de sur HDInsight cluster fournit également un vague de tableau de bord web qui vous permet d’envoi, surveiller et gérer des topologies vague s’exécutant sur le cluster.

![Tableau de bord vague](./media/hdinsight-storm-overview/dashboard.png)

Pour plus d’informations sur le tableau de bord vague de, voir [déployer et gérer des topologies vague Apache sur HDInsight](hdinsight-storm-deploy-monitor-topology.md).

Vague de sur HDInsight fournit également facilite l’intégration avec Azure événement Hubs via l' **Événement concentrateur bec**. La version la plus récente de ce composant est disponible dans [https://github.com/hdinsight/hdinsight-storm-examples/tree/master/lib/eventhubs](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/lib/eventhubs). Pour plus d’informations sur l’utilisation de ce composant, voir les documents suivants.

* [Développer une topologie c# qui utilise Azure événement Hubs](hdinsight-storm-develop-csharp-event-hub-topology.md)

* [Développer une topologie Java qui utilise Azure événement Hubs](hdinsight-storm-develop-java-event-hub-topology.md)

###<a name="reliability"></a>Fiabilité

Vague d’Apache garantit toujours que tous les messages entrants seront entièrement traité, même lorsque l’analyse de données est répartie sur des centaines de nœuds.

Le **nœud Nimbus** fournit une fonctionnalité similaire à la Hadoop JobTracker, et il affecte des tâches à d’autres nœuds du cluster via **soigneur**. Les nœuds soigneur fournissent débute pour le cluster et facilitent la communication entre Nimbus et le processus **responsable** sur les nœuds de travail. Si un nœud de traitement s’arrête, le nœud Nimbus est informé et il affecte la tâche et données associées à un autre nœud.

La configuration par défaut pour vague d’Apache doit avoir un seul nœud Nimbus. Vague d’HDInsight s’exécute deux nœuds Nimbus. Si le nœud principal échoue, le cluster HDInsight bascule vers le nœud secondaire tandis que le nœud principal est restaurée.

![Diagramme de nimbus, soigneur et contrôleur](./media/hdinsight-storm-overview/nimbus.png)

###<a name="scale"></a>Échelle

Bien que vous pouvez spécifier le nombre de nœuds dans votre cluster lors de la création, vous souhaiterez peut-être agrandir ou réduire le cluster pour correspondre à la charge de travail. Tous les clusters HDInsight permettent de modifier le nombre de nœuds dans le cluster, même pendant le traitement des données.

> [AZURE.NOTE] Pour tirer parti des nouveaux nœuds ajoutés via l’option mise à l’échelle, vous devrez rééquilibrer topologies démarrés avant que la taille de cluster a été augmentée.

###<a name="support"></a>Prise en charge

Vague d’HDInsight est fourni avec prise en charge d’enterprise multi-niveaux 24/7. Vague d’HDInsight a également accord de 99,9 %. Cela signifie que nous garantit que le cluster ait connectivité externe au moins 99,9 % du temps.

##<a name="common-use-cases-for-real-time-analytics"></a>Scénarios d’utilisation courants pour analytique en temps réel

Voici quelques scénarios courants pour lesquels vous pouvez utiliser vague d’Apache sur HDInsight. Pour plus d’informations sur les scénarios réels, lisez [comment les sociétés utilisent vague de](https://storm.apache.org/documentation/Powered-By.html).

* Internet des objets (IoT)
* Détection de fraude
* Mise en réseau analytique
* Extraire, transformer, chargement (ETL)
* Contrôle en réseau
* Recherche
* Engagement mobile

##<a name="how-is-data-in-hdinsight-storm-processed"></a>Mode de traitement des données dans vague d’HDInsight

Vague d’Apache s’exécute **topologies** au lieu des tâches MapReduce qui peuvent vous connaissez dans HDInsight ou Hadoop. Une vague de sur HDInsight cluster contient deux types de nœuds : nœuds qui s’exécutent nœuds **Nimbus** et collaborateur qui s’exécutent **responsable**d’en-tête.

* **Nimbus**: similaire à la JobTracker dans Hadoop, il est responsable de la distribution de code dans l’ensemble du cluster, attribuer des tâches à des machines virtuelles et analyse de l’échec. HDInsight fournit deux nœuds Nimbus, il n’existe aucun point de défaillance pour vague d’HDInsight

* **Responsable**: pour chaque nœud de travail est responsable pour démarrer et arrêter **les processus de travail** sur le nœud.

* **Processus de travail**: exécute un sous-ensemble d’une **topologie**. Une topologie en cours d’exécution est répartie entre plusieurs processus de travail dans l’ensemble du cluster.

* **Topologie**: définit un graphique de calcul qui traite les **flux** de données. Contrairement aux tâches MapReduce, topologies exécutent jusqu'à ce que vous les arrêtez.

* **Flux de données**: une collection de **tuples**indépendante. Flux de données est obtenus par **becs verseurs amovibles** et **boulons**, et elles sont utilisées par les **boulons**.

* **Tuple**: une liste de valeurs dynamiquement tapées nommée.

* **Goulotte**: utilise des données à partir d’une source de données et émet un ou plusieurs **flux de données**.

    > [AZURE.NOTE] Dans de nombreux cas, les données sont lues à partir d’une file d’attente, tels que Kafka, Bus des services Azure files d’attente ou hubs événement. La file d’attente garantit que les données sont conservées s’il existe une panne.

* **Boulons**: consomme des **flux de données**, effectue le traitement de **tuples**et peuvent émettre des **flux de données**. Boulons sont également responsables pour écrire des données vers le stockage externe, comme une file d’attente, HDInsight, HBase, un blob ou tout autre magasin de données.

* **Apache Thrift**: une structure logicielle pour le développement scalable service multilingues. Il permet de créer des services qui fonctionnent entre C++, Java, Python, PHP, Ruby, Erlang, Perl, Haskell, c#, cacao, JavaScript, Node.js, Smalltalk et d’autres langues.

    * **Nimbus** est un service Thrift et une **topologie** est une définition Thrift, il est possible de développer topologies à l’aide d’une variété de langages de programmation.

Pour plus d’informations sur les composants vague, consultez le [didacticiel vague] [ apachetutorial] sur le site apache.org.


##<a name="what-programming-languages-can-i-use"></a>Quelles sont les langues programmation puis-je utiliser ?

La vague de sur HDInsight cluster prend en charge c#, Java et Python.

### <a name="c35"></a>C & #35 ;

Les outils de HDInsight pour Visual Studio permettent aux développeurs .NET de conception et implémentation d’une topologie en c#. Vous pouvez également créer des topologies hybrides qui utilisent des composants Java et c#.

Pour plus d’informations, voir [développer c# topologies pour vague d’Apache sur HDInsight à l’aide de Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md).

###<a name="java"></a>Java

La plupart des exemples Java que vous rencontrez sera Java simple ou Trident. Trident est une abstraction de haut niveau qui facilite l’effectuer les actions telles que les jointures, agrégations, de regroupement et de filtrage. Toutefois, Trident intervient sur des lots de tuples, qu’une solution Java brute traite un un uplet flux de données à la fois.

Pour plus d’informations sur Trident, consultez le [didacticiel Trident](https://storm.apache.org/documentation/Trident-tutorial.html) sur le site apache.org.

Pour obtenir des exemples de topologies Java et Trident, voir la [liste des exemples de topologies vague](hdinsight-storm-example-topology.md) ou les exemples vague starter sur votre cluster HDInsight.

Les exemples de vague starter se trouvent dans le répertoire __/usr/hdp/current/storm-client/contrib/storm-starter__ sur clusters basés sur Linux et le répertoire **%storm_home%\contrib\storm-starter** sur clusters fonctionnant sous Windows.

##<a name="what-are-some-common-development-patterns"></a>Quelles sont des modèles de développement communs ?

###<a name="guaranteed-message-processing"></a>Traitement des messages garantie

Vague de peut fournir différents niveaux de traitement de message garantie. Par exemple, une application vague base peut garantir au moins une transformation et Trident pouvez garantit-traitement une seule fois.

Pour plus d’informations, voir [garanties sur le traitement des données](https://storm.apache.org/about/guarantees-data-processing.html) sur le site apache.org.

###<a name="ibasicbolt"></a>IBasicBolt

Le modèle de lecture d’un uplet d’entrée, émission zéro ou plus tuples, puis acking tuple d’entrée immédiatement à la fin de la méthode execute est très courant et vague de fournit l’interface [IBasicBolt](https://storm.apache.org/apidocs/backtype/storm/topology/IBasicBolt.html) pour automatiser ce modèle.

###<a name="joins"></a>Jointures

Participation à deux flux de données peut varier entre les applications. Par exemple, vous pouvez joindre chaque tuple à partir de plusieurs flux dans un nouveau flux de données, ou vous pouvez joindre uniquement par lots de tuples une fenêtre spécifique. Dans les deux cas, vous rejoignez est possible à l’aide de [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29), qui est une façon de définir comment tuples sont routés vers détaillée.

Dans l’exemple Java suivant, fieldsGrouping est utilisé pour acheminer tuples issus de composants « 1 », « 2 » et « 3 » pour les boulons **MyJoiner** .

    builder.setBolt("join", new MyJoiner(), parallelism) .fieldsGrouping("1", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("2", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("3", new Fields("joinfield1", "joinfield2"));

###<a name="batching"></a>Le traitement par lots

Le traitement par lots peut être effectuée plusieurs manières. Avec une topologie Java vague simple, vous pourriez utilisez compteur simple au numéro de lot X de tuples avant les émission, ou un mécanisme de minutage interne appelé un uplet « tick » pour émettre un lot de toutes les X secondes.

Pour obtenir un exemple d’utilisation de tuples graduations, voir [analyse des données de capteur avec vague d’et HBase sur HDInsight](hdinsight-storm-sensor-data-analysis.md).

Si vous utilisez Trident, il est basé sur le traitement par lots de tuples.

###<a name="caching"></a>La mise en cache

Dans la mise en cache est souvent utilisée comme mécanisme pour accélérer le traitement, car elle conserve fréquemment utilisée actifs en mémoire. Comme une topologie est distribuée à plusieurs nœuds et plusieurs processus au sein de chaque nœud, envisagez d’utiliser [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29) pour vous assurer que tuples contenant les champs qui sont utilisés pour la recherche de cache sont toujours routés vers la même procédure. Cela permet d’éviter des processus duplication d’entrées du cache.

###<a name="streaming-top-n"></a>N premiers diffusion en continu

Lorsque votre topologie dépend de calculer une valeur « top N », comme les 5 tendances sur Twitter, vous devez calculer la valeur N premiers en parallèle et puis de fusionner la sortie de ces calculs dans une valeur globale. Pour cela, à l’aide de [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29) à acheminer par champ pour les boulons parallèles (que les données des partitions par valeur du champ) et puis itinéraire à un boulons qui globalement détermine la valeur N premiers.

Pour obtenir un exemple, voir l’exemple [RollingTopWords](https://github.com/nathanmarz/storm-starter/blob/master/src/jvm/storm/starter/RollingTopWords.java) .

##<a name="what-type-of-logging-does-storm-use"></a>Quel type d’enregistrement renverser utiliser ?

Vague d’utilise Log4j Apache pour enregistrer les informations. Par défaut, une grande quantité de données est consignée, et il peut être difficile à trier les informations. Vous pouvez inclure un fichier de configuration de la journalisation dans le cadre de la topologie vague pour contrôler l’enregistrement comportement.

Pour un exemple de topologie qui montre comment configurer la journalisation, voir exemple [WordCount basé sur Java](hdinsight-storm-develop-java-topology.md) pour vague d’HDInsight.

##<a name="next-steps"></a>Étapes suivantes

En savoir plus sur les solutions analytique en temps réel avec vague d’Apache dans HDInsight :

* [Prise en main vague d’HDInsight][gettingstarted]

* [Exemples de topologies pour vague d’HDInsight](hdinsight-storm-example-topology.md)

[stormtrident]: https://storm.apache.org/documentation/Trident-API-Overview.html
[samoa]: http://yahooeng.tumblr.com/post/65453012905/introducing-samoa-an-open-source-platform-for-mining
[apachetutorial]: https://storm.apache.org/documentation/Tutorial.html
[gettingstarted]: hdinsight-apache-storm-tutorial-get-started-linux.md
