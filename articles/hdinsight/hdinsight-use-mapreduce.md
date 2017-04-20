<properties
   pageTitle="MapReduce avec Hadoop sur HDInsight | Microsoft Azure"
   description="Apprenez à l’exécution de tâches MapReduce sur Hadoop dans clusters HDInsight. Vous exécutez une opération de Nb word base implémentée sous la forme d’une tâche MapReduce Java."
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

# <a name="use-mapreduce-in-hadoop-on-hdinsight"></a>Utiliser MapReduce dans Hadoop sur HDInsight

[AZURE.INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

Dans cet article, vous allez apprendre à exécution de tâches MapReduce sur Hadoop dans clusters HDInsight. Nous exécuter une opération de Nb word base implémentée sous la forme d’une tâche MapReduce Java.

##<a id="whatis"></a>Qu’est MapReduce ?

Hadoop MapReduce est une structure logicielle pour l’écriture de travaux qui traitent de grandes quantités de données. Données d’entrée sont divisées en segments indépendantes, qui sont ensuite traitées en parallèle sur les nœuds dans votre cluster. Un travail MapReduce sont constitués de deux fonctions :

* **Mapper**: utilise les données d’entrée, analyse (généralement avec filtrer et trier des opérations) et émet tuples (paires clé-valeur)
* **Réducteur**: consomme tuples émise par le Mapper et effectue une opération de synthèse qui crée un résultat plus petit, combiné à partir des données Mapper

Un exemple de tâche de base word count MapReduce est illustré dans le diagramme suivant :

![HDI. WordCountDiagram][image-hdi-wordcountdiagram]

Le résultat de cette tâche est un nombre de fois combien chaque mot s’est produite dans le texte qui ont été analysé.

* La mapper prend chaque ligne à partir de la saisie de texte en tant qu’entrée et décompose en mots. Il émet une valeur de clé/paire chaque fois qu’un mot se produit du mot est suivi par un chiffre 1. Le résultat est trié avant d’envoyer à réducteur.

* Réducteur de la somme de ces nombres individuels pour chaque mot et émet une paire clé/valeur unique qui contient le mot suivi par la somme de ses occurrences.

MapReduce peut être implémentée dans différentes langues. Java est l’implémentation la plus courante et est utilisé pour la démonstration dans ce document.

### <a name="hadoop-streaming"></a>Hadoop diffusion en continu

Langues ou structures basés sur Java et la Machine virtuelle Java (par exemple, Scalding ou en cascade,) peut être exécuté directement en tant que MapReduce tâche, comme dans une application Java. D’autres personnes, tels que c# ou Python ou des exécutables autonomes, doivent utiliser Hadoop diffusion en continu.

Diffusion en continu Hadoop communique avec la mapper et réducteur via STDIN et STDOUT - la mapper réducteur de lire les données une ligne à la fois STDIN et écrire la sortie dans STDOUT. Chaque ligne de lecture ou émis par la mapper et réducteur doit être placé dans le format d’une paire clé/valeur, délimitée par un onglet charaacter :

    [key]/t[value]

Pour plus d’informations, voir [Hadoop diffusion en continu](http://hadoop.apache.org/docs/r1.2.1/streaming.html).

Pour obtenir des exemples d’utilisation Hadoop en continu avec HDInsight, voir les rubriques suivantes :

* [Développer des travaux MapReduce Python](hdinsight-hadoop-streaming-python.md)

##<a id="data"></a>À propos de l’exemple de données

Dans cet exemple, pour les exemples de données, vous allez utiliser les blocs-notes de Leonardo Da Vinci, qui sont fournies comme document de texte dans votre cluster HDInsight.

Les exemples de données sont stockées dans le stockage Blob Azure HDInsight utilise en tant que le système de fichiers par défaut pour les clusters Hadoop. HDInsight peut accéder aux fichiers stockés dans le stockage Blob en utilisant le préfixe **wasb** . Par exemple, pour accéder au fichier exemple.log, vous utiliseriez la syntaxe suivante :

    wasbs:///example/data/gutenberg/davinci.txt

Stockage d’objets Blob Azure étant l’espace de stockage par défaut pour HDInsight, vous pouvez également accéder le fichier à l’aide de **/example/data/gutenberg/davinci.txt**.

> [AZURE.NOTE] Dans la syntaxe précédente, **wasbs : / / /** est utilisé pour accéder aux fichiers qui sont stockés dans le conteneur de stockage par défaut pour votre cluster HDInsight. Si vous avez spécifié des comptes d’espace de stockage supplémentaire lorsque vous votre cluster sa mise en service, et que vous voulez accéder aux fichiers stockés dans ces comptes, vous pouvez accéder aux données en spécifiant l’adresse du compte nom et de stockage conteneur. Par exemple, **wasbs://mycontainer@mystorage.blob.core.windows.net/example/data/gutenberg/davinci.txt**.

##<a id="job"></a>À propos de l’exemple MapReduce

La tâche MapReduce qui est utilisée dans cet exemple se trouve dans **wasbs://example/jars/hadoop-mapreduce-examples.jar**, et il est fourni avec votre cluster HDInsight. Cet onglet contient un exemple de compter word que vous exécuterez contre **davinci.txt**.

> [AZURE.NOTE] 2.1 HDInsight clusters, sur l’emplacement du fichier est **wasbs:///example/jars/hadoop-examples.jar**.

Pour référence, voici le code Java pour le travail word count MapReduce :

    package org.apache.hadoop.examples;

    import java.io.IOException;
    import java.util.StringTokenizer;

    import org.apache.hadoop.conf.Configuration;
    import org.apache.hadoop.fs.Path;
    import org.apache.hadoop.io.IntWritable;
    import org.apache.hadoop.io.Text;
    import org.apache.hadoop.mapreduce.Job;
    import org.apache.hadoop.mapreduce.Mapper;
    import org.apache.hadoop.mapreduce.Reducer;
    import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
    import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
    import org.apache.hadoop.util.GenericOptionsParser;

    public class WordCount {

      public static class TokenizerMapper
           extends Mapper<Object, Text, Text, IntWritable>{

        private final static IntWritable one = new IntWritable(1);
        private Text word = new Text();

        public void map(Object key, Text value, Context context
                        ) throws IOException, InterruptedException {
          StringTokenizer itr = new StringTokenizer(value.toString());
          while (itr.hasMoreTokens()) {
            word.set(itr.nextToken());
            context.write(word, one);
          }
        }
      }

      public static class IntSumReducer
           extends Reducer<Text,IntWritable,Text,IntWritable> {
        private IntWritable result = new IntWritable();

        public void reduce(Text key, Iterable<IntWritable> values,
                           Context context
                           ) throws IOException, InterruptedException {
          int sum = 0;
          for (IntWritable val : values) {
            sum += val.get();
          }
          result.set(sum);
          context.write(key, result);
        }
      }

      public static void main(String[] args) throws Exception {
        Configuration conf = new Configuration();
        String[] otherArgs = new GenericOptionsParser(conf, args).getRemainingArgs();
        if (otherArgs.length != 2) {
          System.err.println("Usage: wordcount <in> <out>");
          System.exit(2);
        }
        Job job = new Job(conf, "word count");
        job.setJarByClass(WordCount.class);
        job.setMapperClass(TokenizerMapper.class);
        job.setCombinerClass(IntSumReducer.class);
        job.setReducerClass(IntSumReducer.class);
        job.setOutputKeyClass(Text.class);
        job.setOutputValueClass(IntWritable.class);
        FileInputFormat.addInputPath(job, new Path(otherArgs[0]));
        FileOutputFormat.setOutputPath(job, new Path(otherArgs[1]));
        System.exit(job.waitForCompletion(true) ? 0 : 1);
      }
    }

Pour obtenir des instructions rédiger votre travail MapReduce, voir [programmes développer Java MapReduce pour HDInsight](hdinsight-develop-deploy-java-mapreduce-linux.md).

##<a id="run"></a>Exécuter la MapReduce

HDInsight pouvez exécuter HiveQL travaux à l’aide de plusieurs méthodes. Utilisez le tableau suivant pour déterminer quelle méthode vous consiste, puis cliquez sur le lien pour une procédure pas à pas.

| **Utilisez cette commande**...                                                    | **.. .pour, procédez comme suit**                                       | .. .avec ce **système d’exploitation cluster** | .. contre ce **système d’exploitation client** |
|:-------------------------------------------------------------------|:--------------------------------------------------------|:------------------------------------------|:-----------------------------------------|
| [SSH](hdinsight-hadoop-use-mapreduce-ssh.md)                       | Utilisez la commande Hadoop via **SSH**                  | Linux                                     | Linux, Unix, Mac OS X ou Windows        |
| [Coin](hdinsight-hadoop-use-mapreduce-curl.md)                     | Soumettre la tâche à distance à l’aide de **repos**               | Linux ou Windows                          | Linux, Unix, Mac OS X ou Windows        |
| [Windows PowerShell](hdinsight-hadoop-use-mapreduce-powershell.md) | Soumettre la tâche à distance à l’aide de **Windows PowerShell** | Linux ou Windows                          | Windows                                  |
| [Bureau à distance](hdinsight-hadoop-use-mapreduce-remote-desktop)    | Utilisez la commande Hadoop via le **Bureau à distance**       | Windows                                   | Windows                                  |

##<a id="nextsteps"></a>Étapes suivantes

Bien que MapReduce offre puissantes fonctionnalités de diagnostics, il peut être un peu difficile au masque. Il existe plusieurs structures basé sur Java qui facilitent la définir MapReduce applications, ainsi que des technologies d’assistance comme cochon et Hive, qui offrent un moyen plus simple pour utiliser des données dans HDInsight. Pour plus d’informations, consultez les articles suivants :

* [Développer des programmes Java MapReduce pour HDInsight](hdinsight-develop-deploy-java-mapreduce-linux.md)

* [Développer les Python streaming MapReduce programmes pour HDInsight](hdinsight-hadoop-streaming-python.md)

* [Développer des travaux MapReduce échaudage avec Apache Hadoop sur HDInsight](hdinsight-hadoop-mapreduce-scalding.md)

* [Utiliser Hive avec HDInsight][hdinsight-use-hive]

* [Utiliser cochon avec HDInsight][hdinsight-use-pig]

* [Exécuter les exemples HDInsight][hdinsight-samples]


[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-develop-mapreduce-jobs]: hdinsight-develop-deploy-java-mapreduce-linux.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-samples]: hdinsight-run-samples.md
[hdinsight-provision]: hdinsight-provision-clusters.md

[powershell-install-configure]: ../powershell-install-configure.md

[image-hdi-wordcountdiagram]: ./media/hdinsight-use-mapreduce/HDI.WordCountDiagram.gif
