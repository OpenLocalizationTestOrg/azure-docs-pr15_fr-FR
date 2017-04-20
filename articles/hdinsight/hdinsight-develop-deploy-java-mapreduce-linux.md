<properties
    pageTitle="Développer des programmes Java MapReduce pour basé sur Linux HDInsight | Microsoft Azure"
    description="Apprenez à développer des programmes Java MapReduce et les déployer à HDInsight basé sur Linux."
    services="hdinsight"
    editor="cgronlun"
    manager="jhubbard"
    authors="Blackmist"
    documentationCenter=""
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="10/11/2016"
    ms.author="larryfr"/>

# <a name="develop-java-mapreduce-programs-for-hadoop-on-hdinsight-linux"></a>Développer des programmes Java MapReduce pour Hadoop sur HDInsight Linux

Ces documents vous guide dans l’utilisation de Apache Maven pour créer une application MapReduce, puis déploiement et l’exécution sur un Hadoop basé sur Linux sur cluster HDInsight.

##<a name="prerequisites"></a>Conditions préalables

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- [JDK Java](http://www.oracle.com/technetwork/java/javase/downloads/) 7 ou version ultérieure (ou un équivalent, par exemple OpenJDK)

- [Apache Maven](http://maven.apache.org/)

- **Un abonnement Azure**

- **Azure infrastructure du langage commun**

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

##<a name="configure-environment-variables"></a>Configurer les variables d’environnement

Variables d’environnement suivantes peuvent être définis lorsque vous installez Java et JDK. Toutefois, vous devez vérifier qu’ils existent et qu’ils contiennent les valeurs correctes pour votre système.

* **JAVA_HOME** - doivent pointer vers le répertoire où l’environnement d’exécution Java (JRE) est installé. Par exemple, dans un système OS X, Unix ou Linux, il doit avoir une valeur semblable à `/usr/lib/jvm/java-7-oracle`. Dans Windows, il a la valeur semblable à`c:\Program Files (x86)\Java\jre1.7`

* **Chemin d’accès** : doit contenir les chemins d’accès suivants :

    * **JAVA_HOME** (ou le chemin d’accès équivalent)

    * **JAVA_HOME\bin** (ou le chemin d’accès équivalent)

    * Le répertoire où Maven est installé

##<a name="create-a-new-maven-project"></a>Créer un projet Maven

1. À partir d’une session Terminal Server, ou une ligne de commande dans votre environnement de développement, accédez à l’emplacement que vous souhaitez stocker ce projet.

3. Utilisez la commande __mvn__ , qui est installée avec Maven, qui pour permet de générer la génération de modèles automatique pour le projet.

        mvn archetype:generate -DgroupId=org.apache.hadoop.examples -DartifactId=wordcountjava -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

    Cela créera un nouveau répertoire dans le répertoire actif, avec le nom spécifié par le paramètre __artifactID__ (**wordcountjava** dans cet exemple). Ce répertoire contient les éléments suivants :

    * __pom.xml__ - le [Modèle d’objet projet (POM)](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html) qui contient des informations et la configuration des détails utilisés pour générer le projet.

    * __src__ - le répertoire qui contient le répertoire __java/principaux/org/hadoop/apache/exemples__ , vous allez créer l’application.

3. Supprimez le fichier __src/test/java/org/apache/hadoop/examples/apptest.java__ , car il ne sera pas utilisé dans cet exemple.

##<a name="add-dependencies"></a>Ajouter des dépendances

1. Modifiez le fichier __pom.xml__ et ajoutez les éléments suivants à l’intérieur de la `<dependencies>` section :

        <dependency>
          <groupId>org.apache.hadoop</groupId>
          <artifactId>hadoop-mapreduce-examples</artifactId>
          <version>2.5.1</version>
          <scope>provided</scope>
        </dependency>
        <dependency>
          <groupId>org.apache.hadoop</groupId>
          <artifactId>hadoop-mapreduce-client-common</artifactId>
          <version>2.5.1</version>
          <scope>provided</scope>
        </dependency>
        <dependency>
          <groupId>org.apache.hadoop</groupId>
          <artifactId>hadoop-common</artifactId>
          <version>2.5.1</version>
          <scope>provided</scope>
        </dependency>

    Cette option indique à Maven que le projet requiert les bibliothèques (répertoriés dans &lt;artifactId\>) avec une version spécifique (dans la liste &lt;version\>). Au moment de la compilation, cela est téléchargé à partir du référentiel Maven par défaut. Vous pouvez utiliser la [recherche de référentiel Maven](http://search.maven.org/#artifactdetails%7Corg.apache.hadoop%7Chadoop-mapreduce-examples%7C2.5.1%7Cjar) pour afficher une plus grande.

    La `<scope>provided</scope>` indique Maven que ces dépendances ne doivent pas être empaquetés avec l’application, car ils seront fournies par le cluster HDInsight au moment de l’exécution.

2. Ajoutez le code suivant dans le fichier __pom.xml__ . Il doit être à l’intérieur de la `<project>...</project>` balises dans le fichier ; par exemple, entre `</dependencies>` et `</project>`.

        <build>
          <plugins>
            <plugin>
              <groupId>org.apache.maven.plugins</groupId>
              <artifactId>maven-shade-plugin</artifactId>
              <version>2.3</version>
              <configuration>
                <transformers>
                  <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
                  </transformer>
                </transformers>
              </configuration>
              <executions>
                <execution>
                  <phase>package</phase>
                    <goals>
                      <goal>shade</goal>
                    </goals>
                </execution>
              </executions>
            </plugin>
            <plugin>
              <groupId>org.apache.maven.plugins</groupId>
              <artifactId>maven-compiler-plugin</artifactId>
              <configuration>
               <source>1.7</source>
               <target>1.7</target>
              </configuration>
            </plugin>
          </plugins>
        </build>

    Le plug-in première configure le [Plug-in d’appliquer un ombrage Maven](http://maven.apache.org/plugins/maven-shade-plugin/), qui est utilisé pour générer un uberjar (parfois appelé un fatjar), qui contient des dépendances requises par l’application. Il évite également que doublons de licences au sein du package jar, qui peuvent entraîner des problèmes sur certains systèmes.

    Le plug-in deuxième configure le compilateur Maven, qui est utilisé pour définir la version de Java requis par cette application vers la version utilisée sur le cluster HDInsight.

3. Enregistrez le fichier __pom.xml__ .

##<a name="create-the-mapreduce-application"></a>Créer l’application MapReduce

1. Accédez au répertoire __wordcountjava/src/principaux/java/org/apache/hadoop/exemples__ et renommez le fichier __App.java__ en __WordCount.java__.

2. Ouvrez le fichier __WordCount.java__ dans un éditeur de texte et remplacez le contenu avec les éléments suivants :

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

    Notez le nom du package est **org.apache.hadoop.examples** et le nom du cours est **WordCount**. Vous allez utiliser ces noms lorsque vous envoyez le travail MapReduce.

3. Enregistrez le fichier.

##<a name="build-the-application"></a>Créer l’application

1. Accédez au répertoire __wordcountjava__ , si vous n’avez pas encore fait.

2. Utiliser la commande suivante pour créer un fichier JAR contenant l’application :

        mvn clean package

    Ceci nettoyer les objets de génération précédente, téléchargez les dépendances qui n’ont pas déjà été installés, puis générer et empaqueter l’application.

3. Une fois la commande terminée, le répertoire __wordcountjava/cible__ contiendra un fichier nommé __wordcountjava-1.0-SNAPSHOT.jar__.

    > [AZURE.NOTE] Le fichier __wordcountjava-1.0-SNAPSHOT.jar__ est un uberjar, qui contient non seulement le travail WordCount, mais aussi dépendances requises par la tâche en cours d’exécution.


##<a id="upload"></a>Télécharger le fichier jar.

Pour télécharger le fichier jar dans le headnode HDInsight, utilisez la commande suivante :

    scp wordcountjava-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:

    Replace __USERNAME__ with your SSH user name for the cluster. Replace __CLUSTERNAME__ with the HDInsight cluster name.

Cette action copie les fichiers à partir du système local dans le nœud de tête.

> [AZURE.NOTE] Si vous avez utilisé un mot de passe pour protéger votre compte SSH, le système vous demandera le mot de passe. Si vous avez utilisé un code SSH, vous devrez peut-être utiliser les `-i` paramètre et le chemin d’accès à la clé privée. Par exemple, `scp -i /path/to/private/key wordcountjava-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:`.

##<a name="run"></a>Exécuter la tâche MapReduce

1. Se connecter à HDInsight à l’aide de SSH comme décrit dans les articles suivants :

    - [Utiliser le protocole SSH avec basé sur Linux Hadoop sur HDInsight de Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

    - [Utiliser le protocole SSH avec basé sur Linux Hadoop sur HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

2. À partir de la session SSH, utilisez la commande suivante pour exécuter l’application MapReduce :

        yarn jar wordcountjava.jar org.apache.hadoop.examples.WordCount wasbs:///example/data/gutenberg/davinci.txt wasbs:///example/data/wordcountout

    Cela permet de compter les mots dans le fichier davinci.txt l’application WordCount MapReduce et stocker les résultats à __wasbs : / / / exemple/données/wordcountout__. Le fichier d’entrée et sortie sont stockés sur le stockage par défaut pour le cluster.

3. Une fois que la tâche est terminée, utilisez ce qui suit pour afficher les résultats :

        hdfs dfs -cat wasbs:///example/data/wordcountout/*

    Vous devez recevoir une liste de mots et les comptages, incluant des valeurs similaires à ce qui suit :

        zeal    1
        zelus   1
        zenith  2

##<a id="nextsteps"></a>Étapes suivantes

Dans ce document, vous avez appris à développer un travail MapReduce Java. Voir les documents suivants pour d’autres façons de travailler avec HDInsight.

- [Utiliser Hive avec HDInsight][hdinsight-use-hive]
- [Utiliser cochon avec HDInsight][hdinsight-use-pig]
- [Utiliser MapReduce avec HDInsight](hdinsight-use-mapreduce.md)

Pour plus d’informations, voir également le [Centre de développement Java](https://azure.microsoft.com/develop/java/).

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-ODBC]: hdinsight-connect-excel-hive-ODBC-driver.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md

[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md

[powershell-PSCredential]: http://social.technet.microsoft.com/wiki/contents/articles/4546.working-with-passwords-secure-strings-and-credentials-in-windows-powershell.aspx

