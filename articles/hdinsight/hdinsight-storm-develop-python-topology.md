<properties
   pageTitle="Utiliser des composants Python dans une topologie vague sur HDinsight | Microsoft Azure"
   description="Découvrez comment vous pouvez utiliser des composants Python à partir avec vague d’Apache sur Azure HDInsight. Vous allez apprendre à utiliser les composants Python d’un type de Java et Clojure en fonction de la topologie de vague."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="python"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/27/2016"
   ms.author="larryfr"/>

#<a name="develop-apache-storm-topologies-using-python-on-hdinsight"></a>Développer des topologies Apache vague à l’aide de Python sur HDInsight

Vague d’Apache prend en charge plusieurs langues, même ce qui vous permet de combiner des composants de plusieurs langues dans une topologie. Dans ce document, vous allez apprendre à utiliser des composants Python dans vos topologies Java et vague de Clojure sur HDInsight.

##<a name="prerequisites"></a>Conditions préalables

* Python 2.7 ou version ultérieure

* Java JDK 1.7 ou version ultérieure

* [Leiningen](http://leiningen.org/)

##<a name="storm-multi-language-support"></a>Prise en charge multilingue de vague

Vague d’a été conçu pour fonctionner avec des composants écrits à l’aide de n’importe quel langage de programmation, mais cela nécessite que les composants comprennent comment travailler avec la [définition Thrift pour vague de](https://github.com/apache/storm/blob/master/storm-core/src/storm.thrift). Pour Python, un module est fourni dans le cadre du projet Apache vague qui vous permet de communiquer facilement avec vague de. Vous pouvez trouver ce module sur [https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py](https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py).

Dans la mesure où vague d’Apache est un processus Java qui s’exécute sur la Machine virtuelle (machine virtuelle Java), les composants écrits dans d’autres langues sont exécutées comme sous-processus. Les bits vague en cours d’exécution dans la machine virtuelle Java communique avec ces sous-processus à l’aide de messages JSON envoyés via stdin/stdout. Vous trouverez plus d’informations sur les communications entre les composants dans la documentation de [protocole Multi-lang](https://storm.apache.org/documentation/Multilang-protocol.html) .

###<a name="the-storm-module"></a>Le module vague

Le module vague (https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py), fournit les bits nécessaires pour créer des composants de Python qui fonctionnent avec vague de.

Ainsi, par exemple `storm.emit` pour émettre tuples, et `storm.logInfo` pour écrire dans les journaux. Je vous encourage à lire sur le fichier et à comprendre qu’il propose.

##<a name="challenges"></a>Défis

L’utilisation du module __storm.py__ , vous pouvez créer becs verseurs Python qui utilisent des données et les boulons traitent des données, alors que la définition de topologie vague globale fils de communication entre les composants est toujours écrites à l’aide de Java ou Clojure. En outre, si vous utilisez Java, vous devez également créer les composants Java agissent comme une interface aux composants Python.

En outre, étant donné que clusters vague exécutent en mode distribué, vous devez vous assurer que les modules requis par vos composants Python sont disponibles sur tous les nœuds de travail dans le cluster. Vague de n’offre un moyen simple pour effectuer cette opération pour les ressources multi-lang - que vous disposiez inclure toutes les dépendances dans le cadre du fichierJAR pour la topologie, ou installer manuellement des dépendances sur chaque nœud de travail dans le cluster.

###<a name="java-vs-clojure-topology-definition"></a>Définition de topologie Java et Clojure

Les deux méthodes de définition d’une topologie, Clojure est de loin le plus simple/partir comme vous pouvez le faire directement composants de python une référence dans la définition de la topologie. Pour les définitions de topologie basé sur Java, vous devez également définir composants Java qui gèrent les choses que vous déclarez les champs dans les tuples retourné par les composants Python.

Ces deux méthodes sont décrites dans ce document, ainsi que des exemples de projets.

##<a name="python-components-with-a-java-topology"></a>Composants de Python avec une topologie Java

> [AZURE.NOTE] Cet exemple est disponible dans [https://github.com/Azure-Samples/hdinsight-python-storm-wordcount](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount) dans le répertoire __JavaTopology__ . Il s’agit d’un projet Maven en fonction. Si vous ne connaissez pas Maven, voir [topologies basé sur Java développer avec vague d’Apache sur HDInsight](hdinsight-storm-develop-java-topology.md) pour plus d’informations sur la création d’un projet Maven pour une topologie vague.

Une topologie basée sur Java qui utilise Python (ou autres composants de langage machine virtuelle Java,) s’affiche au départ pour utiliser des composants Java ; mais si vous recherchez dans chacune des becs verseurs Java/boulons, vous verrez code similaire à ce qui suit :

    public SplitBolt() {
        super("python", "countbolt.py");
    }

C’est là Java appelle Python et exécute le script qui contient la logique boulons réel. Les Java becs verseurs/boulons (par exemple) simplement déclarer les champs dans le tuple qui sera émise par le composant Python sous-jacente.

Les fichiers Python réels sont stockés dans le `/multilang/resources` répertoire dans cet exemple. La `/multilang` répertoire référencée dans la __pom.xml__:

<resources>
    <resource>
        <!-- Where the Python bits are kept -->
        <directory>{basedir} $/ multilang</directory>
    </resource>
</resources>

Cela inclut tous les fichiers dans le `/multilang` dossier dans le fichier jar qui doit être généré à partir de ce projet.

> [AZURE.IMPORTANT] Notez que cette option spécifie uniquement la `/multilang` répertoire et pas `/multilang/resources`. Vague d’attend ressources non machine virtuelle Java dans un `resources` répertoire, afin qu’il est en interne déjà recherché. Placement des composants dans ce dossier vous permet de simplement référence par son nom dans le code Java. Par exemple, `super("python", "countbolt.py");`. Une autre façon de penser il est que vague de voit la `resources` répertoire racine (/) pour accéder aux ressources multi-lang.
>
> Pour ce projet exemple, le `storm.py` module est inclus dans le `/multilang/resources` répertoire.

###<a name="build-and-run-the-project"></a>Créer et exécuter le projet

Pour exécuter ce projet localement, utilisez simplement la commande Maven suivante pour créer et exécuter en mode local :

    mvn compile exec:java -Dstorm.topology=com.microsoft.example.WordCount

Utilisez ctrl + c pour arrêter le processus.

Pour déployer le projet sur un cluster HDInsight vague d’Apache en cours d’exécution, procédez comme suit :

1. Créer un pot premiers :

        mvn package

    Cela créera un fichier nommé __WordCount--1.0 SNAPSHOT.jar__ dans les `/target` répertoire pour ce projet.

2. Téléchargez le fichier jar au cluster Hadoop à l’aide d’une des méthodes suivantes :

    * Pour les __basé sur Linux__ HDInsight clusters : utilisation `scp WordCount-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:WordCount-1.0-SNAPSHOT.jar` pour copier le fichier jar au cluster, remplacement de nom d’utilisateur avec votre nom d’utilisateur SSH et NOMDUCLUSTER avec le nom du cluster HDInsight.

        Une fois que le fichier de téléchargement est terminé, connectez-vous au cluster à l’aide de SSH et démarrez la topologie à l’aide`storm jar WordCount-1.0-SNAPSHOT.jar com.microsoft.example.WordCount wordcount`

    * Pour les __fonctionnant sous Windows__ HDInsight clusters : se connecter au tableau de bord vague d’en accédant à HTTPS://CLUSTERNAME.azurehdinsight.net/ dans votre navigateur. Remplacez NOMDUCLUSTER avec votre nom de cluster HDInsight et fournir un nom d’administrateur et mot de passe lorsque vous y êtes invité.

        À l’aide de l’écran, effectuer les opérations suivantes :

        * __Fichier JAR__: sélectionnez __Parcourir__, puis sélectionnez le fichier __WordCount-1.0-SNAPSHOT.jar__
        * __Nom du cours__: entrez`com.microsoft.example.WordCount`
        * __Paramètres supplémentaires__: entrez un nom convivial tel que `wordcount` pour identifier la topologie

        Enfin, sélectionnez __Envoyer__ pour démarrer la topologie.

> [AZURE.NOTE] Une fois démarré, une topologie vague s’exécute jusqu'à l’arrêt (tués). Pour arrêter la topologie, utilisez des options du `storm kill TOPOLOGYNAME` commande à partir de la ligne de commande (SSH session vers un cluster Linux, par exemple) ou à l’aide de l’interface utilisateur vague de, sélectionnez la topologie, puis le bouton __Supprimer__ .

##<a name="python-components-with-a-clojure-topology"></a>Composants de Python avec une topologie Clojure

> [AZURE.NOTE] Cet exemple est disponible dans [https://github.com/Azure-Samples/hdinsight-python-storm-wordcount](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount) dans le répertoire __ClojureTopology__ .

Cette topologie a été créée à l’aide de [Leiningen](http://leiningen.org) pour [créer un nouveau projet Clojure](https://github.com/technomancy/leiningen/blob/stable/doc/TUTORIAL.md#creating-a-project). Après cela, les modifications suivantes au projet avec génération de modèles ont été apportées :

* __Project.CLJ__: ajoutés dépendances pour vague d’et exclusions pour les éléments qui peuvent provoquer un problème lors du déploiement sur le serveur HDInsight.
* __ressources/ressources__: Leiningen crée une valeur par défaut `resources` répertoire, alors que les fichiers stockés ici semblent sont ajoutés à la racine du fichier jar créé à partir de ce projet, et vague d’attend des fichiers dans le répertoire sous-adresse `resources`. Pour un sous répertoire a été ajouté et les fichiers Python sont stockés dans `resources/resources`. Au moment de l’exécution, il sera traité comme la racine (/) pour accéder aux composants Python.
* __src/WordCount/Core.CLJ__: ce fichier contient la définition de la topologie et est référencé à partir du fichier __project.clj__ . Pour plus d’informations sur l’utilisation de Clojure pour définir une topologie vague, voir [Clojure DSL](https://storm.apache.org/documentation/Clojure-DSL.html).

###<a name="build-and-run-the-project"></a>Créer et exécuter le projet

__Pour créer et exécuter le projet localement__, utilisez la commande suivante :

    lein clean, run

Pour arrêter la topologie, utilisez __Ctrl + C__.

__Pour créer un uberjar et déployer à HDInsight__, procédez comme suit :

1. Créer un uberjar contenant la topologie et les dépendances requises :

        lein uberjar

    Cela créera un nouveau fichier nommé `wordcount-1.0-SNAPSHOT.jar` dans les `target\uberjar+uberjar` répertoire.
    
2. Pour déployer et exécuter la topologie à un cluster de HDInsight, utilisez une des méthodes suivantes :

    * __Basé sur Linux HDInsight__
    
        1. Copiez le fichier sur le nœud de tête cluster HDInsight à l’aide `scp`. Par exemple :
        
                scp wordcount-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:wordcount-1.0-SNAPSHOT.jar
                
            Remplacez nom_utilisateur par un utilisateur SSH pour votre cluster et NOMDUCLUSTER avec votre nom de cluster HDInsight.
            
        2. Une fois que le fichier a été copié pour le cluster, utiliser le protocole SSH pour vous connecter au cluster et soumettre la tâche. Pour plus d’informations sur l’utilisation de SSH avec HDInsight, voir une des opérations suivantes :
        
            * [Utiliser le protocole SSH avec basé sur Linux HDInsight de Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
            * [Utiliser le protocole SSH avec basé sur Linux HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
            
        3. Une fois connecté, utilisez les éléments suivants pour démarrer la topologie :
        
                storm jar wordcount-1.0-SNAPSHOT.jar wordcount.core wordcount
    
    * __HDInsight fonctionnant sous Windows__
    
        1. Se connecter au tableau de bord vague d’en accédant à HTTPS://CLUSTERNAME.azurehdinsight.net/ dans votre navigateur. Remplacez NOMDUCLUSTER avec votre nom de cluster HDInsight et fournir un nom d’administrateur et mot de passe lorsque vous y êtes invité.

        2. À l’aide de l’écran, effectuer les opérations suivantes :

            * __Fichier JAR__: sélectionnez __Parcourir__, puis sélectionnez le fichier __wordcount-1.0-SNAPSHOT.jar__
            * __Nom du cours__: entrez`wordcount.core`
            * __Paramètres supplémentaires__: entrez un nom convivial tel que `wordcount` pour identifier la topologie

            Enfin, sélectionnez __Envoyer__ pour démarrer la topologie.

> [AZURE.NOTE] Une fois démarré, une topologie vague s’exécute jusqu'à l’arrêt (tués). Pour arrêter la topologie, utilisez des options du `storm kill TOPOLOGYNAME` commande à partir de la ligne de commande (SSH session vers un cluster de Linux) ou à l’aide de l’interface utilisateur vague de, sélectionnez la topologie, puis le bouton __Supprimer__ .

##<a name="next-steps"></a>Étapes suivantes

Dans ce document, vous avez appris comment utiliser les composants Python à partir d’une topologie vague. Voir les documents d’autres manières d’utiliser Python avec HDInsight suivants :

* [Comment utiliser Python pour la diffusion des travaux MapReduce](hdinsight-hadoop-streaming-python.md)
* [Comment utiliser Python utilisateur définie par fonctions (UDF) dans cochon et Hive](hdinsight-python.md)
