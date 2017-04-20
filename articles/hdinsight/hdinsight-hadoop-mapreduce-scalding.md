<properties
 pageTitle="Développer des travaux MapReduce échaudage avec Maven | Microsoft Azure"
 description="Découvrez comment utiliser Maven pour créer une tâche MapReduce échaudage, puis déployer et exécuter la tâche sur un Hadoop sur cluster HDInsight."
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
 ms.date="10/18/2016"
 ms.author="larryfr"/>

# <a name="develop-scalding-mapreduce-jobs-with-apache-hadoop-on-hdinsight"></a>Développer des travaux MapReduce échaudage avec Apache Hadoop sur HDInsight

Échaudage est une bibliothèque de Scala qui facilite la création de tâches Hadoop MapReduce. Il propose une syntaxe concise, ainsi que l’intégration avec Scala.

Dans ce document, découvrez comment utiliser Maven pour créer une tâche de MapReduce Nb word base écrite en Scalding. Vous allez puis apprendre à déployer et exécuter cette tâche sur un cluster HDInsight.

## <a name="prerequisites"></a>Conditions préalables

- **Azure un abonnement**. Voir [Azure obtenir la version d’évaluation gratuite](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Hadoop sur cluster HDInsight en fonction de Windows A ou Linux**. Voir [Hadoop basé sur Linux disposition sur HDInsight](hdinsight-hadoop-provision-linux-clusters.md) ou [fonctionnant sous Windows de mise en service Hadoop sur HDInsight](hdinsight-provision-clusters.md) pour plus d’informations.

* **[Maven](http://maven.apache.org/)**

* **[Plate-forme Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 7 ou version ultérieure**

## <a name="create-and-build-the-project"></a>Créer et générer le projet

1. Utilisez la commande suivante pour créer un nouveau projet Maven :

        mvn archetype:generate -DgroupId=com.microsoft.example -DartifactId=scaldingwordcount -DarchetypeGroupId=org.scala-tools.archetypes -DarchetypeArtifactId=scala-archetype-simple -DinteractiveMode=false

    Cette commande Créer un nouveau répertoire nommé **scaldingwordcount**et créer la structure d’une application Scala.

2. Dans le répertoire **scaldingwordcount** , ouvrez le fichier **pom.xml** et remplacez le contenu avec les éléments suivants :

        <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
            <modelVersion>4.0.0</modelVersion>
            <groupId>com.microsoft.example</groupId>
            <artifactId>scaldingwordcount</artifactId>
            <version>1.0-SNAPSHOT</version>
            <name>${project.artifactId}</name>
            <properties>
            <maven.compiler.source>1.6</maven.compiler.source>
            <maven.compiler.target>1.6</maven.compiler.target>
            <encoding>UTF-8</encoding>
            </properties>
            <repositories>
            <repository>
                <id>conjars</id>
                <url>http://conjars.org/repo</url>
            </repository>
            <repository>
                <id>maven-central</id>
                <url>http://repo1.maven.org/maven2</url>
            </repository>
            </repositories>
            <dependencies>
            <dependency>
                <groupId>com.twitter</groupId>
                <artifactId>scalding-core_2.11</artifactId>
                <version>0.13.1</version>
            </dependency>
            <dependency>
                <groupId>org.apache.hadoop</groupId>
                <artifactId>hadoop-core</artifactId>
                <version>1.2.1</version>
                <scope>provided</scope>
            </dependency>
            </dependencies>
            <build>
            <sourceDirectory>src/main/scala</sourceDirectory>
            <plugins>
                <plugin>
                <groupId>org.scala-tools</groupId>
                <artifactId>maven-scala-plugin</artifactId>
                <version>2.15.2</version>
                <executions>
                    <execution>
                    <id>scala-compile-first</id>
                    <phase>process-resources</phase>
                    <goals>
                        <goal>add-source</goal>
                        <goal>compile</goal>
                    </goals>
                    </execution>
                </executions>
                </plugin>
                <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-shade-plugin</artifactId>
                <version>2.3</version>
                <configuration>
                    <transformers>
                    <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
                    </transformer>
                    </transformers>
                    <filters>
                    <filter>
                        <artifact>*:*</artifact>
                        <excludes>
                        <exclude>META-INF/*.SF</exclude>
                        <exclude>META-INF/*.DSA</exclude>
                        <exclude>META-INF/*.RSA</exclude>
                        </excludes>
                    </filter>
                    </filters>
                </configuration>
                <executions>
                    <execution>
                    <phase>package</phase>
                    <goals>
                        <goal>shade</goal>
                    </goals>
                    <configuration>
                        <transformers>
                        <transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
                            <mainClass>com.twitter.scalding.Tool</mainClass>
                        </transformer>
                        </transformers>
                    </configuration>
                    </execution>
                </executions>
                </plugin>
            </plugins>
            </build>
        </project>

    Ce fichier décrit le projet, les dépendances et les plug-ins. Voici les entrées importantes :

    * **Maven.Compiler.source** et **maven.compiler.target**: définit la version Java pour ce projet

    * **référentiels**: les référentiels contenant les fichiers de dépendance utilisés par ce projet

    * **core_2.11 échaudage** et **hadoop cœur**: ce projet dépend Scalding et Hadoop packages de base

    * **Maven-scala plug-in**: plug-in pour compiler des applications scala

    * **Maven-ombre plug-in**: plug-in pour créer ombré bocaux (fat). Ce plug-in s’applique les filtres et les transformations ; specificially :

        * **filtres**: les filtres appliqués modifier les informations meta incluses dans le fichier jar. Pour empêcher les exceptions lors de l’exécution de la signature, cela exclut les divers fichiers de signature peuvent être inclus avec les dépendances.

        * **exécutions**: la configuration de l’exécution du package phase spécifie la classe **com.twitter.scalding.Tool** en tant que la classe principale pour le package. Sans cela, vous devez spécifier com.twitter.scalding.Tool, ainsi que la classe qui contient la logique d’application, lors de l’exécution de la tâche avec la commande hadoop.

3. Supprimez le répertoire **src/test** , comme vous n’allez pas créer des tests avec cet exemple.

4. Ouvrez le fichier **src/main/scala/com/microsoft/example/App.scala** et remplacez le contenu avec les éléments suivants :

        package com.microsoft.example

        import com.twitter.scalding._

        class WordCount(args : Args) extends Job(args) {
            // 1. Read lines from the specified input location
            // 2. Extract individual words from each line
            // 3. Group words and count them
            // 4. Write output to the specified output location
            TextLine(args("input"))
            .flatMap('line -> 'word) { line : String => tokenize(line) }
            .groupBy('word) { _.size }
            .write(Tsv(args("output")))

            //Tokenizer to split sentance into words
            def tokenize(text : String) : Array[String] = {
            text.toLowerCase.replaceAll("[^a-zA-Z0-9\\s]", "").split("\\s+")
            }
        }

    Pour ce faire implémenté une tâche de nombre de base de word.

5. Enregistrez et fermez les fichiers.

6. Utilisez la commande suivante à partir du répertoire **scaldingwordcount** pour générer et empaqueter l’application :

        mvn package

    Une fois que cette tâche est terminée, vous pouvez trouver le package qui contient l’application WordCount sur **target/scaldingwordcount-1.0-SNAPSHOT.jar**.

## <a name="run-the-job-on-a-linux-based-cluster"></a>Exécuter le travail sur un cluster Linux

> [AZURE.NOTE] Les étapes suivantes utilisent SSH et la commande Hadoop. Pour les autres méthodes de l’exécution de tâches MapReduce, voir [Utiliser MapReduce dans Hadoop sur HDInsight](hdinsight-use-mapreduce.md).

1. Pour télécharger le package à votre cluster HDInsight, utilisez la commande suivante :

        scp target/scaldingwordcount-1.0-SNAPSHOT.jar username@clustername-ssh.azurehdinsight.net:

    Cette action copie les fichiers à partir du système local dans le nœud de tête.

    > [AZURE.NOTE] Si vous avez utilisé un mot de passe pour protéger votre compte SSH, le système vous demandera le mot de passe. Si vous avez utilisé un code SSH, vous devrez peut-être utiliser les `-i` paramètre et le chemin d’accès à la clé privée. Par exemple,`scp -i /path/to/private/key target/scaldingwordcount-1.0-SNAPSHOT.jar username@clustername-ssh.azurehdinsight.net:.`

2. Utilisez la commande suivante pour vous connecter au nœud de tête cluster :

        ssh username@clustername-ssh.azurehdinsight.net

    > [AZURE.NOTE] Si vous avez utilisé un mot de passe pour protéger votre compte SSH, le système vous demandera le mot de passe. Si vous avez utilisé un code SSH, vous devrez peut-être utiliser les `-i` paramètre et le chemin d’accès à la clé privée. Par exemple,`ssh -i /path/to/private/key username@clustername-ssh.azurehdinsight.net`

3. Une fois connecté au nœud principal, utilisez la commande suivante pour exécuter la tâche de comptage de word

        yarn jar scaldingwordcount-1.0-SNAPSHOT.jar com.microsoft.example.WordCount --hdfs --input wasbs:///example/data/gutenberg/davinci.txt --output wasbs:///example/wordcountout

    La classe WordCount que vous implémentée précédemment s’exécute. `--hdfs`Indique la tâche à utiliser HADOOP. `--input`Spécifie le fichier texte d’entrée, tandis que `--output` Spécifie l’emplacement de sortie.

4. Une fois que la tâche est terminée, utilisez ce qui suit pour afficher le résultat.

        hdfs dfs -text wasbs:///example/wordcountout/*

    Ceci affiche des informations similaires à ce qui suit :

        writers 9
        writes  18
        writhed 1
        writing 51
        writings        24
        written 208
        writtenthese    1
        wrong   11
        wrongly 2
        wrongplace      1
        wrote   34
        wrotefootnote   1
        wrought 7

## <a name="run-the-job-on-a-windows-based-cluster"></a>Exécuter le travail sur un cluster Windows

Les étapes suivantes utilisent Windows PowerShell. Pour les autres méthodes de l’exécution de tâches MapReduce, voir [Utiliser MapReduce dans Hadoop sur HDInsight](hdinsight-use-mapreduce.md).

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

2. Démarrez Azure PowerShell et connectez-vous à votre compte Azure. Après avoir fourni vos informations d’identification, la commande renvoie des informations sur votre compte.

        Add-AzureRMAccount

        Id                             Type       ...
        --                             ----
        someone@example.com            User       ...

3. Si vous avez plusieurs abonnements, fournissent l’id de l’abonnement que vous souhaitez utiliser pour le déploiement.

        Select-AzureRMSubscription -SubscriptionID <YourSubscriptionId>

    > [AZURE.NOTE] Vous pouvez utiliser `Get-AzureRMSubscription` pour obtenir une liste de tous les abonnements associés à votre compte, qui inclut l’Id de l’abonnement pour chacun d'entre eux.

4. Utilisez le script suivant pour télécharger et exécuter la tâche WordCount. Remplacer `CLUSTERNAME` avec le nom de votre HDInsight cluster et assurez-vous que `$fileToUpload` est le chemin d’accès au fichier __scaldingwordcount-1.0-SNAPSHOT.jar__ .

        #Cluster name, file to be uploaded, and where to upload it
        $clustername = Read-Host -Prompt "Enter the HDInsight cluster name"
        $fileToUpload = Read-Host -Prompt "Enter the path to the scaldingwordcount-1.0-SNAPSHOT.jar file"
        $blobPath = "example/jars/scaldingwordcount-1.0-SNAPSHOT.jar"

        #Login to your Azure subscription
        Login-AzureRmAccount
        #Get HTTPS/Admin credentials for submitting the job later
        $creds = Get-Credential -Message "Enter the login credentials for the cluster"
        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=(Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
            -ResourceGroupName $resourceGroup)[0].Value

        #Create a storage content and upload the file
        $context = New-AzureStorageContext `
            -StorageAccountName $storageAccountName `
            -StorageAccountKey $storageAccountKey
            
        Set-AzureStorageBlobContent `
            -File $fileToUpload `
            -Blob $blobPath `
            -Container $container `
            -Context $context
            
        #Create a job definition and start the job
        $jobDef=New-AzureRmHDInsightMapReduceJobDefinition `
            -JobName ScaldingWordCount `
            -JarFile wasbs:///example/jars/scaldingwordcount-1.0-SNAPSHOT.jar `
            -ClassName com.microsoft.example.WordCount `
            -arguments "--hdfs", `
                        "--input", `
                        "wasbs:///example/data/gutenberg/davinci.txt", `
                        "--output", `
                        "wasbs:///example/wordcountout"
        $job = Start-AzureRmHDInsightJob `
            -clustername $clusterName `
            -jobdefinition $jobDef `
            -HttpCredential $creds
        Write-Output "Job ID is: $job.JobId"
        Wait-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobId $job.JobId `
            -HttpCredential $creds
        #Download the output of the job
        Get-AzureStorageBlobContent `
            -Blob example/wordcountout/part-00000 `
            -Container $container `
            -Destination output.txt `
            -Context $context
        #Log any errors that occured
        Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $job.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

     Lorsque vous exécutez le script, vous devrez entrer le nom d’utilisateur admin et le mot de passe pour votre cluster HDInsight. Toutes les erreurs qui se produisent pendant l’exécution de la tâche seront enregistrés dans la console.
     
6. Une fois que la tâche est terminée, le résultat est téléchargé vers fichier __output.txt__ dans le répertoire actif. Utilisez la commande suivante pour afficher les résultats.

        cat output.txt

    Le fichier doit contenir des valeurs semblables à ce qui suit :

        writers 9
        writes  18
        writhed 1
        writing 51
        writings        24
        written 208
        writtenthese    1
        wrong   11
        wrongly 2
        wrongplace      1
        wrote   34
        wrotefootnote   1
        wrought 7

## <a name="next-steps"></a>Étapes suivantes

À présent que vous avez appris à Scalding permet de créer des travaux MapReduce pour HDInsight, utilisez les liens suivants pour Explorer d’autres façons de travailler avec Azure HDInsight.

* [Utiliser Hive avec HDInsight](hdinsight-use-hive.md)

* [Utiliser cochon avec HDInsight](hdinsight-use-pig.md)

* [Utiliser des travaux MapReduce avec HDInsight](hdinsight-use-mapreduce.md)
