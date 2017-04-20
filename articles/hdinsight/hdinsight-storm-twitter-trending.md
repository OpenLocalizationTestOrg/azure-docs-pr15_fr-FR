<properties
   pageTitle="Twitter sujets actuels avec vague d’Apache sur HDInsight | Microsoft Azure"
   description="Découvrez comment utiliser Trident pour créer une topologie Apache vague qui détermine rubriques actuels sur Twitter basé sur hashtags."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"
    tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="java"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/27/2016"
   ms.author="larryfr"/>

#<a name="determine-twitter-trending-topics-with-apache-storm-on-hdinsight"></a>Déterminer les sujets actuels Twitter avec vague d’Apache sur HDInsight

Découvrez comment utiliser Trident pour créer une topologie vague qui détermine actuels rubriques (balises hachage) sur Twitter.

Trident est une abstraction de haut niveau qui fournit des outils tels que des jointures, agrégations, le regroupement, fonctions et filtres. En outre, Trident ajoute primitives pour effectuer un traitement incrémentiel, avec état. Cet exemple montre comment vous pouvez créer une topologie à l’aide de plusieurs fonctions intégrées fournies par Trident, fonction et un bec personnalisé.

> [AZURE.NOTE] Cet exemple est largement selon l’exemple [Vague de Trident](https://github.com/jalonsoramos/trident-storm) en Juan Alonso.

##<a name="requirements"></a>Configuration requise

* <a href="http://www.oracle.com/technetwork/java/javase/downloads/index.html" target="_blank">Java et JDK 1.7</a>

* <a href="http://maven.apache.org/what-is-maven.html" target="_blank">Maven</a>

* <a href="http://git-scm.com/" target="_blank">GIT</a>

* Un compte de développeur Twitter

##<a name="download-the-project"></a>Télécharger le projet

Utilisez le code suivant pour cloner le projet localement.

    git clone https://github.com/Blackmist/TwitterTrending

##<a name="topology"></a>Topologie

La topologie dans cet exemple est la suivante :

![topologie](./media/hdinsight-storm-twitter-trending/trident.png)

> [AZURE.NOTE] Il s’agit d’une vue simplifiée de la topologie. Plusieurs instances des composants seront répartis sur les nœuds dans le cluster.

Le code Trident qui mettent en œuvre, la topologie est la suivante :

    topology.newStream("spout", spout)
        .each(new Fields("tweet"), new HashtagExtractor(), new Fields("hashtag"))
        .groupBy(new Fields("hashtag"))
        .persistentAggregate(new MemoryMapState.Factory(), new Count(), new Fields("count"))
        .newValuesStream()
        .applyAssembly(new FirstN(10, "count"))
        .each(new Fields("hashtag", "count"), new Debug());

Ce code effectue les opérations suivantes :

1. Crée un nouveau flux de données à partir de bec. BEC extrait tweet Twitter et les filtres de mots clés spécifiques (amour, votre musique et café dans cet exemple).

2. HashtagExtractor, une fonction personnalisée, est utilisé pour extraire les balises hachage chaque tweet. Ils sont émis vers le flux.

3. Le flux de données est regroupé par une balise hachage et passé à une agrégation. Cette agrégation crée un compteur de nombre de fois chaque balise hachage s’est produite. Ces données sont conservées en mémoire. Enfin, un nouveau flux de données qui contient la balise hachage et le nombre est émis.

4. Étant donné que nous intéresse uniquement les balises hachage les plus populaires d’un lot donné de tweets, l’assembly **FirstN** est appliqué pour renvoyer uniquement les 10 premières valeurs, en fonction du champ nombre.

> [AZURE.NOTE] Que le bec ou HashtagExtractor, nous utilisons une fonctionnalité Trident intégrée.
>
> Pour plus d’informations sur les opérations intégrées, voir <a href="https://storm.apache.org/apidocs/storm/trident/operation/builtin/package-summary.html" target="_blank">storm.trident.operation.builtin Package</a>.
>
> Pour implémenter Trident-état autre que MemoryMapState, voir les rubriques suivantes :
>
> * <a href="https://github.com/fhussonnois/storm-trident-elasticsearch" target="_blank">Recherche élastique vague Trident</a>
>
> * <a href="https://github.com/kstyrc/trident-redis" target="_blank">Trident redis</a>

###<a name="the-spout"></a>BEC

BEC, **TwitterSpout**, utilise <a href="http://twitter4j.org/en/" target="_blank">Twitter4j</a> pour extraire tweet Twitter. Un filtre est créé (amour, votre musique et café dans cet exemple), puis le tweet entrants (état) qui correspond au filtre est stockés dans une file d’attente blocage liée. (Pour plus d’informations, voir <a href="http://docs.oracle.com/javase/7/docs/api/java/util/concurrent/LinkedBlockingQueue.html" target="_blank">LinkedBlockingQueue classe</a>.) Enfin, les éléments sont extrait la file d’attente et émis à la topologie.

###<a name="the-hashtagextractor"></a>La HashtagExtractor

Pour extraire les balises hachage, <a href="http://twitter4j.org/javadoc/twitter4j/EntitySupport.html#getHashtagEntities--" target="_blank">getHashtagEntities</a> est utilisée pour récupérer toutes les balises hachage qui figurent dans la tweet. Ils sont ensuite émis vers le flux.

##<a name="enable-twitter"></a>Activer Twitter

Procédez comme suit pour enregistrer une nouvelle application Twitter et obtenir les consommateur et accéder aux informations jetons nécessaires pour lire à partir de Twitter :

1. Accédez à <a href="https://apps.twitter.com" target="_blank">Applications Twitter</a> et cliquez sur le bouton **créer une nouvelle application** . Lorsque vous renseignez le formulaire, laissez le champ **URL de rappel** vide.

2. Lorsque l’application est créée, cliquez sur l’onglet **clés et des jetons d’accès** .

3. Copiez les informations **Clé de consommateur** et le **Code Secret consommateur** .

4. Dans la partie inférieure de la page, sélectionnez **créer mon jeton d’accès** si aucune jetons n’existent. Lorsque les jetons ont été créés, copiez les informations de **Jeton d’accès** et le **Code Secret jeton Access** .

5. Dans le projet **TwitterSpoutTopology** que vous cloner précédemment, ouvrez le fichier **resources/twitter4j.properties** , ajoutez les informations que vous avez recueilli des étapes précédentes et puis enregistrez le fichier.

##<a name="build-the-topology"></a>Créer la topologie

Utilisez le code suivant pour générer le projet :

        cd [directoryname]
        mvn compile

##<a name="test-the-topology"></a>Tester la topologie

Pour tester la topologie localement, utilisez la commande suivante :

    mvn compile exec:java -Dstorm.topology=com.microsoft.example.TwitterTrendingTopology

Après le démarrage de la topologie, vous devez voir les informations de débogage qui contient le hachage balises et compte émises par la topologie. La sortie doit ressembler à ce qui suit :

    DEBUG: [Quicktellervalentine, 7]
    DEBUG: [GRAMMYs, 7]
    DEBUG: [AskSam, 7]
    DEBUG: [poppunk, 1]
    DEBUG: [rock, 1]
    DEBUG: [punkrock, 1]
    DEBUG: [band, 1]
    DEBUG: [punk, 1]
    DEBUG: [indonesiapunkrock, 1]

##<a name="next-steps"></a>Étapes suivantes

À présent que vous avez testé la topologie localement, découvrez comment déployer la topologie : [déployer et gérer des topologies vague Apache sur HDInsight](hdinsight-storm-deploy-monitor-topology.md).

Peut également vous intéresser dans les rubriques vague suivantes :

* [Développer des topologies Java pour vague d’HDInsight à l’aide de Maven](hdinsight-storm-develop-java-topology.md)

* [Développer des topologies c# pour vague d’HDInsight à l’aide de Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md)

Pour plus d’exemples vague pour HDinsight :

* [Exemples de topologies pour vague d’HDInsight](hdinsight-storm-example-topology.md)
