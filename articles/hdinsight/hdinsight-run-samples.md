<properties
    pageTitle="Exécution des exemples Hadoop dans HDInsight | Microsoft Azure"
    description="Commencer à utiliser le service Azure HDInsight avec les exemples fournis. Utiliser des scripts PowerShell qui s’exécutent MapReduce programmes sur clusters de données."
    services="hdinsight"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/21/2016"
    ms.author="jgao"/>

#<a name="run-hadoop-mapreduce-samples-in-windows-based-hdinsight"></a>Exécution des exemples Hadoop MapReduce dans HDInsight fonctionnant sous Windows

[AZURE.INCLUDE [samples-selector](../../includes/hdinsight-run-samples-selector.md)]

Un ensemble d’exemples sont fournies pour vous aider à mise en route travaux MapReduce en cours d’exécution sur clusters Hadoop à l’aide de Azure HDInsight. Ces exemples sont rendus disponibles sur chacun des groupes HDInsight gérée que vous créez. Pour vous familiariser avec à l’aide des applets de commande PowerShell Azure à l’exécution des tâches sur des clusters Hadoop, ces exemples en cours d’exécution.

- [**Statistiques**][hdinsight-sample-wordcount]: compte les occurrences d’un mot dans un fichier texte.
- [**Diffusion en continu de mots c#**][hdinsight-sample-csharp-streaming]: compte les occurrences d’un mot dans un fichier texte à l’aide de l’interface de diffusion en continu Hadoop.
- [**Estimateur PI**][hdinsight-sample-pi-estimator]: utilise une statistique (quasi-coupon Monte Carlo) méthode pour évaluer la valeur de pi.
- [**10 Go Graysort**][hdinsight-sample-10gb-graysort]: exécuter un objectif général GraySort sur un fichier de 10 Go à l’aide de HDInsight. Il existe trois tâches à exécuter : Teragen pour générer les données, Terasort pour trier les données, puis Teravalidate pour confirmer que les données ont été triées correctement.

>[AZURE.NOTE] Vous trouverez le code source dans l’appendice. 

Documentation présentée sous d’autres beaucoup existe sur le web pour les technologies Hadoop, tels que programmation MapReduce basé sur Java et diffusion en continu et documentation sur les applets de commande qui sont utilisées dans Windows PowerShell l’écriture de script. Pour plus d’informations sur ces ressources, voir :

- [Développer des programmes Java MapReduce pour Hadoop dans HDInsight](hdinsight-develop-deploy-java-mapreduce-linux.md)
- [Soumettre des travaux Hadoop dans HDInsight](hdinsight-submit-hadoop-jobs-programmatically.md)
- [Introduction à Azure HDInsight][hdinsight-introduction]

Aujourd'hui, un grand nombre de personnes sélectionnez Hive et cochon MapReduce.  Pour plus d’informations, voir :

- [Utiliser Hive dans HDInsight](hdinsight-use-hive.md)
- [Utiliser cochon dans HDInsight](hdinsight-use-pig.md)
 
**Conditions préalables**:

- **Azure un abonnement**. Voir [Azure obtenir la version d’évaluation gratuite](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **un cluster HDInsight**. Pour obtenir des instructions sur les différentes façons dont ces clusters peuvent être créés, voir [Hadoop créer des groupes dans un HDInsight](hdinsight-provision-clusters.md).
- **Un poste de travail avec Azure PowerShell**.

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

## <a name="hdinsight-sample-wordcount"></a>Word count - Java 

Pour envoyer un projet MapReduce, vous d’abord créez une définition de travail MapReduce. Dans la définition de travail, vous spécifiez le fichier jar du programme MapReduce et l’emplacement du fichier jar, qui est **wasbs:///example/jars/hadoop-mapreduce-examples.jar**, le nom du cours et les arguments.  Le programme MapReduce wordcount prend deux arguments : le fichier source qui sera utilisé pour compter les mots et l’emplacement de sortie.

Vous trouverez le code source à l' [appendice A](#apendix-a---the-word-count-MapReduce-program-in-java).

Pour la procédure de développement d’un programme Java MapReduce, voir - [développer Java MapReduce programmes pour Hadoop dans HDInsight](hdinsight-develop-deploy-java-mapreduce-linux.md)
 
**Soumettre un travail word count MapReduce**

1. Ouvrez **Windows PowerShell ISE**. Pour plus d’informations, voir [installer et configurer Azure PowerShell][powershell-install-configure].
2. Collez le script PowerShell suivant :

        $subscriptionName = "<Azure Subscription Name>"
        $resourceGroupName = "<Resource Group Name>"
        $clusterName = "<HDInsight cluster name>"             # HDInsight cluster name
        
        Select-AzureRmSubscription -SubscriptionName $subscriptionName
        
        # Define the MapReduce job
        $mrJobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
                                    -JarFile "wasbs:///example/jars/hadoop-mapreduce-examples.jar" `
                                    -ClassName "wordcount" `
                                    -Arguments "wasbs:///example/data/gutenberg/davinci.txt", "wasbs:///example/data/WordCountOutput1"
        
        # Submit the job and wait for job completion
        $cred = Get-Credential -Message "Enter the HDInsight cluster HTTP user credential:" 
        $mrJob = Start-AzureRmHDInsightJob `
                            -ResourceGroupName $resourceGroupName `
                            -ClusterName $clusterName `
                            -HttpCredential $cred `
                            -JobDefinition $mrJobDefinition 
        
        Wait-AzureRmHDInsightJob `
            -ResourceGroupName $resourceGroupName `
            -ClusterName $clusterName `
            -HttpCredential $cred `
            -JobId $mrJob.JobId 
        
        # Get the job output
        $cluster = Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName
        $defaultStorageAccount = $cluster.DefaultStorageAccount -replace '.blob.core.windows.net'
        $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccount)[0].Value
        $defaultStorageContainer = $cluster.DefaultStorageContainer
        
        Get-AzureRmHDInsightJobOutput `
            -ResourceGroupName $resourceGroupName `
            -ClusterName $clusterName `
            -HttpCredential $cred `
            -DefaultStorageAccountName $defaultStorageAccount `
            -DefaultStorageAccountKey $defaultStorageAccountKey `
            -DefaultContainer $defaultStorageContainer  `
            -JobId $mrJob.JobId `
            -DisplayOutputType StandardError

        # Download the job output to the workstation
        $storageContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccount -StorageAccountKey $defaultStorageAccountKey 
        Get-AzureStorageBlobContent -Container $defaultStorageContainer -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force
        
        # Display the output file
        cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"

    La tâche MapReduce génère un fichier nommé *partie-r-00000*qui contient des mots et les nombres. Le script utilise la commande **findstr** pour répertorier tous les mots qui contient *« ici »*.

3. Définissez les 3 premières variables et exécuter le script.

## <a name="hdinsight-sample-csharp-streaming"></a>Word count - c# diffusion en continu

Hadoop fournit une API de flux à MapReduce, qui vous permet d’écrire carte et réduire des fonctions dans une langue différente Java.

> [AZURE.NOTE] La procédure dans ce didacticiel s’applique uniquement aux clusters HDInsight fonctionnant sous Windows. Pour obtenir un exemple de diffusion en continu pour les clusters basés sur Linux HDInsight, voir [programmes de diffusion en continu Python développer pour HDInsight](hdinsight-hadoop-streaming-python.md).

Dans l’exemple, la mapper et le réducteur sont exécutables lire les entrées de [stdin] [ stdin-stdout-stderr] (ligne par ligne) et émettre la sortie de [stdout][stdin-stdout-stderr]. Le programme compte tous les mots dans le texte.

Lorsqu’un fichier exécutable est spécifié pour **mappeurs**, chaque tâche mapper lance le fichier exécutable en tant qu’un processus distinct lors de l’initialisation de la mapper. Pendant l’exécution de la tâche mapper, il convertit ses données entrées en lignes et flux les lignes à la [stdin] [ stdin-stdout-stderr] du processus.

En attendant, la mapper collecte la sortie orientée ligne du stdout du processus. Il convertit chaque ligne dans une paire clé/valeur, qui est collectée en tant que le résultat de la mapper. Par défaut, le préfixe d’une ligne jusqu'à la première tabulation est la clé, et le reste de la ligne (sauf le caractère de tabulation) est la valeur. S’il n’existe aucun caractère de tabulation dans la ligne, ligne entière est considérée comme clé et la valeur est null.

Lorsqu’un fichier exécutable est spécifié pour **REDUCTEURS**, chaque tâche réducteur lance le fichier exécutable en tant qu’un processus distinct lors de l’initialisation de la réducteur. Comme la tâche réducteur s’exécute, il convertit ses paires clé/valeurs d’entrée en lignes, il flux les lignes à la [stdin] [ stdin-stdout-stderr] du processus.

En attendant, le réducteur de collecte la sortie orientée ligne du [stdout] [ stdin-stdout-stderr] du processus. Il convertit une paire clé/valeur, qui est collectée en tant que le résultat de la réducteur de chaque ligne. Par défaut, le préfixe d’une ligne jusqu'à la première tabulation est la clé, et le reste de la ligne (sauf le caractère de tabulation) est la valeur.

Pour plus d’informations sur l’interface Hadoop diffusion en continu, voir [Hadoop Streaming] [diffusion en continu hadoop].

**Pour envoyer un flux de travail Nb word c#**

- Suivez la procédure de [mots - Java](#word-count-java)et remplacer la définition de travail avec les éléments suivants :

        $mrJobDefinition = New-AzureRmHDInsightStreamingMapReduceJobDefinition `
                                -Files "/example/apps/cat.exe","/example/apps/wc.exe" `
                                -Mapper "cat.exe" `
                                -Reducer "wc.exe" `
                                -InputPath "/example/data/gutenberg/davinci.txt" `
                                -OutputPath "/example/data/StreamingOutput/wc.txt"  


    Le fichier de sortie doit être :
    
        example/data/StreamingOutput/wc.txt/part-00000      
                                
## <a name="hdinsight-sample-pi-estimator"></a>Estimateur PI

L’estimateur pi utilise une statistique (quasi-coupon Monte Carlo) méthode pour évaluer la valeur de pi. Points placés au hasard à l’intérieur d’une unité carrée également relèvent au sein d’un cercle inscrit dans ce carré avec une probabilité égale à la zone du cercle, pi/4. La valeur de pi peut être estimée de la valeur de 4R, où R est le rapport entre le nombre de points à l’intérieur du cercle pour le nombre total de points qui se trouvent dans le carré. Obtenir l’exemple de points utilisés est grande, plus l’estimation est.

Le script fourni pour cet exemple soumet une tâche jar Hadoop et est configuré pour exécuter avec une valeur 16 cartes, chacun d'entre eux est requise pour calculer les points d’échantillon 10 millions par les valeurs de paramètre. Ces valeurs de paramètre peuvent être modifiés pour améliorer la valeur estimée de pi. Pour référence, les 10 premières décimales de pi sont 3.1415926535.

**Soumettre un travail estimateur pi**

- Suivez la procédure de [mots - Java](#word-count-java)et remplacer la définition de travail avec les éléments suivants :

        $mrJobJobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
                                    -JarFile "wasbs:///example/jars/hadoop-mapreduce-examples.jar" `
                                    -ClassName "pi" `
                                    -Arguments "16", "10000000"

## <a name="hdinsight-sample-10gb-graysort"></a>10 Go Graysort

Cet exemple utilise un petit 10 Go de données afin qu’elle puisse être exécutée relativement rapidement. Il utilise les applications MapReduce développées par Owen O'Malley et Arun Murthy qui a remporté la référence de tri to (« daytona ») à usage général annuel en 2009 avec un taux de 0.578 to/min (100 To en minutes 173). Pour plus d’informations sur cette et d’autres critères de tri, consultez le site [Sortbenchmark](http://sortbenchmark.org/) .

Cet exemple utilise trois séries de programmes MapReduce :

1. **TeraGen** est un programme MapReduce que vous pouvez utiliser pour générer les lignes de données à trier.
2. **TeraSort** exemples de données d’entrée et utilise MapReduce pour trier les données dans un ordre total. TeraSort est un tri standard des fonctions MapReduce, à l’exception d’un partitionneur personnalisé qui utilise une liste triée des clés N-1 échantillonnée qui définissent la plage de clés pour chaque réduire. En particulier, toutes les clés de ces exemples [i-1] < = clé < exemple [i] sont envoyées à réduire i. Cela garanties que les sorties de réduisent i sont tous les inférieure à la sortie de réduire i + 1.
3. **TeraValidate** est un programme MapReduce qui valide que le résultat est trié globalement. Il crée un mappage par fichier dans le répertoire de sortie, et chaque carte garantit que chaque clé est inférieure ou égale à la précédente. La fonction carte génère également des enregistrements des premier et derniers clés de chaque fichier et la fonction réduire garantit que la première clé de fichier i est supérieure à la dernière clé d’i-1 de fichier. Des problèmes sont signalés comme résultat de la réduction avec les touches qui sont désordre.

Le format d’entrée et de sortie, utilisé par les trois applications, lit et écrit les fichiers texte dans le format approprié. Le résultat de la réduction a réplication définie à 1, au lieu de la valeur par défaut 3, car le contexte de test d’évaluation ne nécessite pas que les données de sortie être répliquées sur plusieurs nœuds.

Trois tâches sont requises par l’exemple, correspondant chacune à un des programmes MapReduce décrites dans l’introduction :

1. Générer les données pour le tri en exécutant la tâche MapReduce **TeraGen** .
2. Trier les données en exécutant la tâche MapReduce **TeraSort** .
3. Vérifiez que les données ont été correctement triées en exécutant la tâche **TeraValidate** MapReduce.

**Pour envoyer les tâches**

- Suivez la procédure de [mots - Java](#word-count-java)et utilisez les définitions de travail suivantes :

    $teragen = New-AzureRmHDInsightMapReduceJobDefinition `
                                -JarFile "/example/jars/hadoop-mapreduce-examples.jar" ` 
   - NomClasse « teragen » «-Arguments «-Dmapred.map.tasks=50 », « 100000000 », « / exemple / / 10 Go tri-d’entrée de données »
    
    $terasort = New-AzureRmHDInsightMapReduceJobDefinition `
                                -JarFile "/example/jars/hadoop-mapreduce-examples.jar" ` 
   - NomClasse « terasort » «-Arguments »-Dmapred.map.tasks=50 », «-Dmapred.reduce.tasks=25 », « / exemple / / 10 Go tri-d’entrée de données », « / exemple/données/10 Go-tri-sortie »
    
    $teravalidate = New-AzureRmHDInsightMapReduceJobDefinition `
                                -JarFile "/example/jars/hadoop-mapreduce-examples.jar" ` 
   - NomClasse « teravalidate » «-Arguments »-Dmapred.map.tasks=50 », «-Dmapred.reduce.tasks=25 », « / exemple/données/10 Go-tri-sortie », « / exemple/données/10 Go-tri-permet de contrôler »


##<a name="next-steps"></a>Étapes suivantes 

À partir de cet article et les articles dans chacun des échantillons, vous avez appris exécuter les exemples inclus dans les groupes HDInsight à l’aide de PowerShell Azure. Pour les didacticiels sur l’utilisation de cochon Hive et MapReduce avec HDInsight, consultez les rubriques suivantes :

* [Prise en main avec Hadoop Hive dans HDInsight à analyser l’utilisation d’un combiné mobile][hdinsight-get-started]
* [Utiliser cochon avec Hadoop sur HDInsight][hdinsight-use-pig]
* [Utiliser Hive avec Hadoop sur HDInsight][hdinsight-use-hive]
* [Soumettre des travaux Hadoop dans HDInsight] [hdinsight-submit-jobs]
* [Documentation présentée sous d’Azure HDInsight SDK][hdinsight-sdk-documentation]
* [Déboguer Hadoop dans HDInsight : messages d’erreur] [hdinsight-errors]


## <a name="appendix-a---the-word-count-source-code"></a>Appendice A - le code Word count source

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


## <a name="appendix-b---the-word-count-streaming-source-code"></a>Annexe B : le nombre de mots diffusion en continu de code source

Le programme MapReduce utilise l’application cat.exe comme une interface de mappage pour transmettre le texte dans la console et l’application wc.exe que l’interface réduire pour compter le nombre de mots transmis à partir d’un document. Mapper et réducteur de lire les caractères, ligne par ligne, à partir du flux d’entrée standard (stdin) et écrire dans le flux de sortie standard (stdout).

    // The source code for the cat.exe (Mapper).

    using System;
    using System.IO;

    namespace cat
    {
        class cat
        {
            static void Main(string[] args)
            {
                if (args.Length > 0)
                {
                    Console.SetIn(new StreamReader(args[0]));
                }

                string line;
                while ((line = Console.ReadLine()) != null)
                {
                    Console.WriteLine(line);
                }
            }
        }
    }



Le code mapper dans le fichier cat.cs utilise un [StreamReader] [ streamreader] objet à lire les caractères du flux entrant à la console qui écrit ensuite le flux de données dans le flux de sortie standard avec statique [Console.Writeline] [ console-writeline] méthode.


    // The source code for wc.exe (Reducer) is:

    using System;
    using System.IO;
    using System.Linq;

    namespace wc
    {
        class wc
        {
            static void Main(string[] args)
            {
                string line;
                var count = 0;

                if (args.Length > 0){
                    Console.SetIn(new StreamReader(args[0]));
                }

                while ((line = Console.ReadLine()) != null) {
                    count += line.Count(cr => (cr == ' ' || cr == '\n'));
                }
                Console.WriteLine(count);
            }
        }
    }


Le code réducteur dans le fichier wc.cs utilise un [StreamReader] [ streamreader] objet pour lire les caractères à partir du flux d’entrée standard qui ont été générés par la mapper cat.exe. Lorsqu’il lit les caractères avec la [Console.Writeline] [ console-writeline] méthode, il compte les mots en comptant les espaces et les caractères de fin de ligne à la fin de chaque mot. Il écrit ensuite le total vers le flux de sortie standard avec [Console.Writeline] [ console-writeline] méthode.





## <a name="appendix-c---the-pi-estimator-source-code"></a>Annexe C - le code Pi estimateur source

L’estimateur pi code Java qui contient les fonctions mapper et réducteur est disponible pour inspection ci-dessous. Le programme mapper génère un nombre spécifié de points placés au hasard à l’intérieur d’un carré unité et puis compte le nombre de ces points situés à l’intérieur du cercle. Le programme réducteur sont additionnés points comptés en les mappeurs et puis calcule la valeur de pi à partir de la formule 4R, où R est le rapport entre le nombre de points comptés dans le cercle et le nombre total de points qui se trouvent dans le carré.

    /**
    * Licensed to the Apache Software Foundation (ASF) under one
    * or more contributor license agreements. See the NOTICE file
    * distributed with this work for additional information
    * regarding copyright ownership. The ASF licenses this file
    * to you under the Apache License, Version 2.0 (the
    * "License"); you may not use this file except in compliance
    * with the License. You may obtain a copy of the License at
    *
    * http://www.apache.org/licenses/LICENSE-2.0
    *
    * Unless required by applicable law or agreed to in writing, software
    * distributed under the License is distributed on an "AS IS" BASIS,
    * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or   implied.
    * See the License for the specific language governing permissions and
    * limitations under the License.
    */

    package org.apache.hadoop.examples;

    import java.io.IOException;
    import java.math.BigDecimal;
    import java.util.Iterator;

    import org.apache.hadoop.conf.Configured;
    import org.apache.hadoop.fs.FileSystem;
    import org.apache.hadoop.fs.Path;
    import org.apache.hadoop.io.BooleanWritable;
    import org.apache.hadoop.io.LongWritable;
    import org.apache.hadoop.io.SequenceFile;
    import org.apache.hadoop.io.Writable;
    import org.apache.hadoop.io.WritableComparable;
    import org.apache.hadoop.io.SequenceFile.CompressionType;
    import org.apache.hadoop.mapred.FileInputFormat;
    import org.apache.hadoop.mapred.FileOutputFormat;
    import org.apache.hadoop.mapred.JobClient;
    import org.apache.hadoop.mapred.JobConf;
    import org.apache.hadoop.mapred.MapReduceBase;
    import org.apache.hadoop.mapred.Mapper;
    import org.apache.hadoop.mapred.OutputCollector;
    import org.apache.hadoop.mapred.Reducer;
    import org.apache.hadoop.mapred.Reporter;
    import org.apache.hadoop.mapred.SequenceFileInputFormat;
    import org.apache.hadoop.mapred.SequenceFileOutputFormat;
    import org.apache.hadoop.util.Tool;
    import org.apache.hadoop.util.ToolRunner;


    //A Map-reduce program to estimate the value of Pi
    //using quasi-Monte Carlo method.
    //
    //Mapper:
    //Generate points in a unit square
    //and then count points inside/outside of the inscribed circle of the square.
    //
    //Reducer:
    //Accumulate points inside/outside results from the mappers.
    //Let numTotal = numInside + numOutside.
    //The fraction numInside/numTotal is a rational approximation of
    //the value (Area of the circle)/(Area of the square),
    //where the area of the inscribed circle is Pi/4
    //and the area of unit square is 1.
    //Then, Pi is estimated value to be 4(numInside/numTotal).
    //

    public class PiEstimator extends Configured implements Tool {
    //tmp directory for input/output
    static private final Path TMP_DIR = new Path(
    PiEstimator.class.getSimpleName() + "_TMP_3_141592654");

    //2-dimensional Halton sequence {H(i)},
    //where H(i) is a 2-dimensional point and i >= 1 is the index.
    //Halton sequence is used to generate sample points for Pi estimation.
    private static class HaltonSequence {
    // Bases
    static final int[] P = {2, 3};
    //Maximum number of digits allowed
    static final int[] K = {63, 40};

    private long index;
    private double[] x;
    private double[][] q;
    private int[][] d;

    //Initialize to H(startindex),
    //so the sequence begins with H(startindex+1).
    HaltonSequence(long startindex) {
    index = startindex;
    x = new double[K.length];
    q = new double[K.length][];
    d = new int[K.length][];
    for(int i = 0; i < K.length; i++) {
    q[i] = new double[K[i]];
    d[i] = new int[K[i]];
    }

    for(int i = 0; i < K.length; i++) {
    long k = index;
    x[i] = 0;

    for(int j = 0; j < K[i]; j++) {
    q[i][j] = (j == 0? 1.0: q[i][j-1])/P[i];
    d[i][j] = (int)(k % P[i]);
    k = (k - d[i][j])/P[i];
    x[i] += d[i][j] * q[i][j];
    }
    }
    }

    //Compute next point.
    //Assume the current point is H(index).
    //Compute H(index+1).
    //@return a 2-dimensional point with coordinates in [0,1)^2
    double[] nextPoint() {
    index++;
    for(int i = 0; i < K.length; i++) {
    for(int j = 0; j < K[i]; j++) {
    d[i][j]++;
    x[i] += q[i][j];
    if (d[i][j] < P[i]) {
    break;
    }
    d[i][j] = 0;
    x[i] -= (j == 0? 1.0: q[i][j-1]);
    }
    }
    return x;
    }
    }

    //Mapper class for Pi estimation.
    //Generate points in a unit square and then
    //count points inside/outside of the inscribed circle of the square.
    public static class PiMapper extends MapReduceBase
    implements Mapper<LongWritable, LongWritable, BooleanWritable, LongWritable> {

    //Map method.
    //@param offset samples starting from the (offset+1)th sample.
    //@param size the number of samples for this map
    //@param out output {ture->numInside, false->numOutside}
    //@param reporter
    public void map(LongWritable offset,
    LongWritable size,
    OutputCollector<BooleanWritable, LongWritable> out,
    Reporter reporter) throws IOException {

    final HaltonSequence haltonsequence = new HaltonSequence(offset.get());
    long numInside = 0L;
    long numOutside = 0L;

    for(long i = 0; i < size.get(); ) {
    //generate points in a unit square
    final double[] point = haltonsequence.nextPoint();

    //count points inside/outside of the inscribed circle of the square
    final double x = point[0] - 0.5;
    final double y = point[1] - 0.5;
    if (x*x + y*y > 0.25) {
    numOutside++;
    } else {
    numInside++;
    }

    //report status
    i++;
    if (i % 1000 == 0) {
    reporter.setStatus("Generated " + i + " samples.");
    }
    }

    //output map results
    out.collect(new BooleanWritable(true), new LongWritable(numInside));
    out.collect(new BooleanWritable(false), new LongWritable(numOutside));
    }
    }


    //Reducer class for Pi estimation.
    //Accumulate points inside/outside results from the mappers.
    public static class PiReducer extends MapReduceBase
    implements Reducer<BooleanWritable, LongWritable, WritableComparable<?>, Writable> {

    private long numInside = 0;
    private long numOutside = 0;
    private JobConf conf; //configuration for accessing the file system

    //Store job configuration.
    @Override
    public void configure(JobConf job) {
    conf = job;
    }


    // Accumulate number of points inside/outside results from the mappers.
    // @param isInside Is the points inside?
    // @param values An iterator to a list of point counts
    // @param output dummy, not used here.
    // @param reporter

    public void reduce(BooleanWritable isInside,
    Iterator<LongWritable> values,
    OutputCollector<WritableComparable<?>, Writable> output,
    Reporter reporter) throws IOException {
    if (isInside.get()) {
    for(; values.hasNext(); numInside += values.next().get());
    } else {
    for(; values.hasNext(); numOutside += values.next().get());
    }
    }

    //Reduce task done, write output to a file.
    @Override
    public void close() throws IOException {
    //write output to a file
    Path outDir = new Path(TMP_DIR, "out");
    Path outFile = new Path(outDir, "reduce-out");
    FileSystem fileSys = FileSystem.get(conf);
    SequenceFile.Writer writer = SequenceFile.createWriter(fileSys, conf,
    outFile, LongWritable.class, LongWritable.class,
    CompressionType.NONE);
    writer.append(new LongWritable(numInside), new LongWritable(numOutside));
    writer.close();
    }
    }

    //Run a map/reduce job for estimating Pi.
    //@return the estimated value of Pi.
    public static BigDecimal estimate(int numMaps, long numPoints, JobConf jobConf
    )
    throws IOException {
    //setup job conf
    jobConf.setJobName(PiEstimator.class.getSimpleName());

    jobConf.setInputFormat(SequenceFileInputFormat.class);

    jobConf.setOutputKeyClass(BooleanWritable.class);
    jobConf.setOutputValueClass(LongWritable.class);
    jobConf.setOutputFormat(SequenceFileOutputFormat.class);

    jobConf.setMapperClass(PiMapper.class);
    jobConf.setNumMapTasks(numMaps);

    jobConf.setReducerClass(PiReducer.class);
    jobConf.setNumReduceTasks(1);

    // turn off speculative execution, because DFS doesn't handle
    // multiple writers to the same file.
    jobConf.setSpeculativeExecution(false);

    //setup input/output directories
    final Path inDir = new Path(TMP_DIR, "in");
    final Path outDir = new Path(TMP_DIR, "out");
    FileInputFormat.setInputPaths(jobConf, inDir);
    FileOutputFormat.setOutputPath(jobConf, outDir);

    final FileSystem fs = FileSystem.get(jobConf);
    if (fs.exists(TMP_DIR)) {
     throw new IOException("Tmp directory " + fs.makeQualified(TMP_DIR)
     + " already exists. Please remove it first.");
     }
     if (!fs.mkdirs(inDir)) {
     throw new IOException("Cannot create input directory " + inDir);
     }

     //generate an input file for each map task
     try {
     for(int i=0; i < numMaps; ++i) {
     final Path file = new Path(inDir, "part"+i);
     final LongWritable offset = new LongWritable(i * numPoints);
     final LongWritable size = new LongWritable(numPoints);
     final SequenceFile.Writer writer = SequenceFile.createWriter(
     fs, jobConf, file,
     LongWritable.class, LongWritable.class, CompressionType.NONE);
     try {
     writer.append(offset, size);
     } finally {
     writer.close();
     }
     System.out.println("Wrote input for Map #"+i);
     }

     //start a map/reduce job
     System.out.println("Starting Job");
     final long startTime = System.currentTimeMillis();
     JobClient.runJob(jobConf);
     final double duration = (System.currentTimeMillis() - startTime)/1000.0;
     System.out.println("Job Finished in " + duration + " seconds");

     //read outputs
     Path inFile = new Path(outDir, "reduce-out");
     LongWritable numInside = new LongWritable();
     LongWritable numOutside = new LongWritable();
     SequenceFile.Reader reader = new SequenceFile.Reader(fs, inFile, jobConf);
     try {
     reader.next(numInside, numOutside);
     } finally {
     reader.close();
     }

     //compute estimated value
     return BigDecimal.valueOf(4).setScale(20)
     .multiply(BigDecimal.valueOf(numInside.get()))
     .divide(BigDecimal.valueOf(numMaps))
     .divide(BigDecimal.valueOf(numPoints));
     } finally {
     fs.delete(TMP_DIR, true);
     }
     }

    //Parse arguments and then runs a map/reduce job.
    //Print output in standard out.
    //@return a non-zero if there is an error. Otherwise, return 0.
     public int run(String[] args) throws Exception {
     if (args.length != 2) {
     System.err.println("Usage: "+getClass().getName()+" <nMaps> <nSamples>");
     ToolRunner.printGenericCommandUsage(System.err);
     return -1;
     }

     final int nMaps = Integer.parseInt(args[0]);
     final long nSamples = Long.parseLong(args[1]);

     System.out.println("Number of Maps = " + nMaps);
     System.out.println("Samples per Map = " + nSamples);

     final JobConf jobConf = new JobConf(getConf(), getClass());
     System.out.println("Estimated value of Pi is "
     + estimate(nMaps, nSamples, jobConf));
     return 0;
     }

     //main method for running it as a stand alone command.
     public static void main(String[] argv) throws Exception {
     System.exit(ToolRunner.run(null, new PiEstimator(), argv));
     }
     }
     
## <a name="appendix-d---the-10gb-graysort-source-code"></a>AnnexeD - le code 10 Go graysort source

Le code pour le programme TeraSort MapReduce est présenté pour inspection dans cette section.


    /**
     * Licensed to the Apache Software Foundation (ASF) under one
     * or more contributor license agreements.  See the NOTICE file
     * distributed with this work for additional information
     * regarding copyright ownership.  The ASF licenses this file
     * to you under the Apache License, Version 2.0 (the
     * "License"); you may not use this file except in compliance
     * with the License.  You may obtain a copy of the License at
     *
     *     http://www.apache.org/licenses/LICENSE-2.0
     *
     * Unless required by applicable law or agreed to in writing, software
     * distributed under the License is distributed on an "AS IS" BASIS,
     * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
     * See the License for the specific language governing permissions and
     * limitations under the License.
     */

    package org.apache.hadoop.examples.terasort;

    import java.io.IOException;
    import java.io.PrintStream;
    import java.net.URI;
    import java.util.ArrayList;
    import java.util.List;

    import org.apache.commons.logging.Log;
    import org.apache.commons.logging.LogFactory;
    import org.apache.hadoop.conf.Configured;
    import org.apache.hadoop.filecache.DistributedCache;
    import org.apache.hadoop.fs.FileSystem;
    import org.apache.hadoop.fs.Path;
    import org.apache.hadoop.io.NullWritable;
    import org.apache.hadoop.io.SequenceFile;
    import org.apache.hadoop.io.Text;
    import org.apache.hadoop.mapred.FileOutputFormat;
    import org.apache.hadoop.mapred.JobClient;
    import org.apache.hadoop.mapred.JobConf;
    import org.apache.hadoop.mapred.Partitioner;
    import org.apache.hadoop.util.Tool;
    import org.apache.hadoop.util.ToolRunner;

    /**
     * Generates the sampled split points, launches the job,
     * and waits for it to finish.
     * <p>
     * To run the program:
     * <b>bin/hadoop jar hadoop-examples-*.jar terasort in-dir out-dir</b>
     */

    public class TeraSort extends Configured implements Tool {
      private static final Log LOG = LogFactory.getLog(TeraSort.class);

      /**
       * A partitioner that splits text keys into roughly equal
       * partitions in a global sorted order.
       */

      static class TotalOrderPartitioner implements Partitioner<Text,Text>{
        private TrieNode trie;
        private Text[] splitPoints;

        /**
         * A generic trie node
         */
        static abstract class TrieNode {
          private int level;
          TrieNode(int level) {
            this.level = level;
          }
          abstract int findPartition(Text key);
          abstract void print(PrintStream strm) throws IOException;
          int getLevel() {
            return level;
          }
        }

        /**
         * An inner trie node that contains 256 children based on the next
         * character.
         */
        static class InnerTrieNode extends TrieNode {
          private TrieNode[] child = new TrieNode[256];

          InnerTrieNode(int level) {
            super(level);
          }
          int findPartition(Text key) {
            int level = getLevel();
            if (key.getLength() <= level) {
              return child[0].findPartition(key);
            }
            return child[key.getBytes()[level]].findPartition(key);
          }
          void setChild(int idx, TrieNode child) {
            this.child[idx] = child;
          }
          void print(PrintStream strm) throws IOException {
            for(int ch=0; ch < 255; ++ch) {
              for(int i = 0; i < 2*getLevel(); ++i) {
                strm.print(' ');
              }
              strm.print(ch);
              strm.println(" ->");
              if (child[ch] != null) {
                child[ch].print(strm);
              }
            }
          }
        }

        /**
         * A leaf trie node that does string compares to figure out where the given
         * key belongs between lower..upper.
         */
        static class LeafTrieNode extends TrieNode {
          int lower;
          int upper;
          Text[] splitPoints;
          LeafTrieNode(int level, Text[] splitPoints, int lower, int upper) {
            super(level);
            this.splitPoints = splitPoints;
            this.lower = lower;
            this.upper = upper;
          }
          int findPartition(Text key) {
            for(int i=lower; i<upper; ++i) {
              if (splitPoints[i].compareTo(key) >= 0) {
                return i;
              }
            }
            return upper;
          }
          void print(PrintStream strm) throws IOException {
            for(int i = 0; i < 2*getLevel(); ++i) {
              strm.print(' ');
            }
            strm.print(lower);
            strm.print(", ");
            strm.println(upper);
          }
        }


        /**
         * Read the cut points from the given sequence file.
         * @param fs the file system
         * @param p the path to read
         * @param job the job config
         * @return the strings to split the partitions on
         * @throws IOException
         */
        private static Text[] readPartitions(FileSystem fs, Path p,
                                             JobConf job) throws IOException {
          SequenceFile.Reader reader = new SequenceFile.Reader(fs, p, job);
          List<Text> parts = new ArrayList<Text>();
          Text key = new Text();
          NullWritable value = NullWritable.get();
          while (reader.next(key, value)) {
            parts.add(key);
            key = new Text();
          }
          reader.close();
          return parts.toArray(new Text[parts.size()]);  
        }

        /**
         * Given a sorted set of cut points, build a trie that will find the correct
         * partition quickly.
         * @param splits the list of cut points
         * @param lower the lower bound of partitions 0..numPartitions-1
         * @param upper the upper bound of partitions 0..numPartitions-1
         * @param prefix the prefix that we have already checked against
         * @param maxDepth the maximum depth we will build a trie for
         * @return the trie node that will divide the splits correctly
         */
        private static TrieNode buildTrie(Text[] splits, int lower, int upper,
                                          Text prefix, int maxDepth) {
          int depth = prefix.getLength();
          if (depth >= maxDepth || lower == upper) {
            return new LeafTrieNode(depth, splits, lower, upper);
          }
          InnerTrieNode result = new InnerTrieNode(depth);
          Text trial = new Text(prefix);
          // append an extra byte on to the prefix
          trial.append(new byte[1], 0, 1);
          int currentBound = lower;
          for(int ch = 0; ch < 255; ++ch) {
            trial.getBytes()[depth] = (byte) (ch + 1);
            lower = currentBound;
            while (currentBound < upper) {
              if (splits[currentBound].compareTo(trial) >= 0) {
                break;
              }
              currentBound += 1;
            }
            trial.getBytes()[depth] = (byte) ch;
            result.child[ch] = buildTrie(splits, lower, currentBound, trial,
                                         maxDepth);
          }
          // pick up the rest
          trial.getBytes()[depth] = 127;
          result.child[255] = buildTrie(splits, currentBound, upper, trial,
                                        maxDepth);
          return result;
        }

        public void configure(JobConf job) {
          try {
            FileSystem fs = FileSystem.getLocal(job);
            Path partFile = new Path(TeraInputFormat.PARTITION_FILENAME);
            splitPoints = readPartitions(fs, partFile, job);
            trie = buildTrie(splitPoints, 0, splitPoints.length, new Text(), 2);
          } catch (IOException ie) {
            throw new IllegalArgumentException("can't read paritions file", ie);
          }
        }

        public TotalOrderPartitioner() {
        }

        public int getPartition(Text key, Text value, int numPartitions) {
          return trie.findPartition(key);
        }

      }

      public int run(String[] args) throws Exception {
        LOG.info("starting");
        JobConf job = (JobConf) getConf();
        Path inputDir = new Path(args[0]);
        inputDir = inputDir.makeQualified(inputDir.getFileSystem(job));
        Path partitionFile = new Path(inputDir, TeraInputFormat.PARTITION_FILENAME);
        URI partitionUri = new URI(partitionFile.toString() +
                                   "#" + TeraInputFormat.PARTITION_FILENAME);
        TeraInputFormat.setInputPaths(job, new Path(args[0]));
        FileOutputFormat.setOutputPath(job, new Path(args[1]));
        job.setJobName("TeraSort");
        job.setJarByClass(TeraSort.class);
        job.setOutputKeyClass(Text.class);
        job.setOutputValueClass(Text.class);
        job.setInputFormat(TeraInputFormat.class);
        job.setOutputFormat(TeraOutputFormat.class);
        job.setPartitionerClass(TotalOrderPartitioner.class);
        TeraInputFormat.writePartitionFile(job, partitionFile);
        DistributedCache.addCacheFile(partitionUri, job);
        DistributedCache.createSymlink(job);
        job.setInt("dfs.replication", 1);
        TeraOutputFormat.setFinalSync(job, true);
        JobClient.runJob(job);
        LOG.info("done");
        return 0;
      }

      /**
       * @param args
       */

      public static void main(String[] args) throws Exception {
        int res = ToolRunner.run(new JobConf(), new TeraSort(), args);
        System.exit(res);
      }
    }














 




[hdinsight-errors]: hdinsight-debug-jobs.md

[hdinsight-sdk-documentation]: https://msdn.microsoft.com/library/azure/dn479185.aspx

[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-introduction]: hdinsight-hadoop-introduction.md


[powershell-install-configure]: ../powershell-install-configure.md

[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md

[hdinsight-samples]: hdinsight-run-samples.md
[hdinsight-sample-10gb-graysort]: #hdinsight-sample-10gb-graysort
[hdinsight-sample-csharp-streaming]: #hdinsight-sample-csharp-streaming
[hdinsight-sample-pi-estimator]: #hdinsight-sample-pi-estimator
[hdinsight-sample-wordcount]: #hdinsight-sample-wordcount

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

[streamreader]: http://msdn.microsoft.com/library/system.io.streamreader.aspx
[console-writeline]: http://msdn.microsoft.com/library/system.console.writeline
[stdin-stdout-stderr]: https://msdn.microsoft.com/library/3x292kth.aspx
