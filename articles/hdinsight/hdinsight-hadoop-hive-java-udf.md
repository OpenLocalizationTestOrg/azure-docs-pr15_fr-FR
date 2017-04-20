<properties
pageTitle="Utiliser une fonction Java définies par l’utilisateur (UDF) avec Hive dans HDInsight | Microsoft Azure"
description="Découvrez comment créer et utiliser une fonction Java définies par l’utilisateur (UDF) à partir de Hive dans HDInsight."
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="jhubbard"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="java"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="09/27/2016"
ms.author="larryfr"/>

#<a name="use-a-java-udf-with-hive-in-hdinsight"></a>Utiliser un Java UDF avec Hive dans HDInsight

Hive est très utile pour utiliser des données dans HDInsight, mais vous pouvez parfois besoin un plus grand objectif langue. Hive permet de créer des fonctions définies par l’utilisateur (UDF) à l’aide d’une variété de langages de programmation. Dans ce document, vous allez apprendre à utiliser un fichier UDF Java Hive.

## <a name="requirements"></a>Configuration requise

* Un abonnement Azure

* Un cluster HDInsight (Windows ou Linux)

    > [AZURE.NOTE] La plupart des étapes décrites dans ce document sont prises en charge sur les deux types de cluster ; Toutefois, les étapes permettant de télécharger le fichier UDF compilé au cluster et exécutez-le sont spécifiques aux clusters basés sur Linux. Liens vers des informations qui peuvent être utilisées avec clusters basés sur Windows sont fournis.

* [JDK Java](http://www.oracle.com/technetwork/java/javase/downloads/) 7 ou version ultérieure (ou un équivalent, par exemple OpenJDK)

* [Apache Maven](http://maven.apache.org/)

* Un éditeur de texte ou IDE Java

    > [AZURE.IMPORTANT] Si vous utilisez un serveur basé sur Linux HDInsight, mais créer les fichiers Python sur un client Windows, vous devez utiliser un éditeur qui utilise saut de ligne comme une fin de ligne. Si vous ne savez pas si votre éditeur utilise saut de ligne ou CRLF, consultez la section [résolution des problèmes](#troubleshooting) pour obtenir la procédure sur la suppression du caractère CR à l’aide des utilitaires sur le cluster HDInsight.

## <a name="create-an-example-udf"></a>Créer un exemple UDF

1. À partir d’une ligne de commande, utilisez ce qui suit pour créer un nouveau projet Maven :

        mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=ExampleUDF -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

    > [AZURE.NOTE] Si vous utilisez PowerShell, vous devez placer les paramètres entre guillemets. Par exemple, `mvn archetype:generate "-DgroupId=com.microsoft.examples" "-DartifactId=ExampleUDF" "-DarchetypeArtifactId=maven-archetype-quickstart" "-DinteractiveMode=false"`.

    Cela créera un répertoire nommé __exampleudf__, qui contient le projet Maven.

2. Une fois que le projet a été créé, supprimez le répertoire __exampleudf/src/test__ qui a été créé dans le cadre du projet ; Il n’est pas utilisée dans cet exemple.

3. Ouvrez __exampleudf/pom.xml__, puis remplacez l’existante `<dependencies>` entrée avec les éléments suivants :

        <dependencies>
            <dependency>
                <groupId>org.apache.hadoop</groupId>
                <artifactId>hadoop-client</artifactId>
                <version>2.7.1</version>
                <scope>provided</scope>
            </dependency>
            <dependency>
                <groupId>org.apache.hive</groupId>
                <artifactId>hive-exec</artifactId>
                <version>1.2.1</version>
                <scope>provided</scope>
            </dependency>
        </dependencies>

    Ces entrées spécifient la version de Hadoop et Hive inclus avec HDInsight 3.3 et 3.4 clusters. Vous trouverez des informations sur les versions de Hadoop et Hive fournis avec HDInsight à partir du document de [contrôle de version composant HDInsight](hdinsight-component-versioning.md) .

    Ajouter un `<build>` section avant le `</project>` ligne à la fin du fichier. Cette section doit contenir les éléments suivants :

        <build>
            <plugins>
                <!-- build for Java 1.7, even if you're on a later version -->
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-compiler-plugin</artifactId>
                    <version>3.3</version>
                    <configuration>
                        <source>1.7</source>
                        <target>1.7</target>
                    </configuration>
                </plugin>
                <!-- build an uber jar -->
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-shade-plugin</artifactId>
                    <version>2.3</version>
                    <configuration>
                        <!-- Keep us from getting a can't overwrite file error -->
                        <transformers>
                            <transformer
                                    implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
                            </transformer>
                            <transformer implementation="org.apache.maven.plugins.shade.resource.ServicesResourceTransformer">
                            </transformer>
                        </transformers>
                        <!-- Keep us from getting a bad signature error -->
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
                        </execution>
                    </executions>
                </plugin>
            </plugins>
        </build>
    
    Ces entrées définissent comment générer le projet. Plus précisément, la version de Java qui utilise le projet et explique comment créer un uberjar pour le déploiement au cluster.

    Enregistrez le fichier une fois que les modifications ont été apportées.

4. Renommez __exampleudf/src/main/java/com/microsoft/examples/App.java__ __ExampleUDF.java__et ouvrez le fichier dans votre éditeur.

5. Remplacez le contenu du fichier __ExampleUDF.java__ avec les valeurs suivantes, puis enregistrez le fichier.

        package com.microsoft.examples;

        import org.apache.hadoop.hive.ql.exec.Description;
        import org.apache.hadoop.hive.ql.exec.UDF;
        import org.apache.hadoop.io.*;

        // Description of the UDF
        @Description(
            name="ExampleUDF",
            value="returns a lower case version of the input string.",
            extended="select ExampleUDF(deviceplatform) from hivesampletable limit 10;"
        )
        public class ExampleUDF extends UDF {
            // Accept a string input
            public String evaluate(String input) {
                // If the value is null, return a null
                if(input == null)
                    return null;
                // Lowercase the input string and return it
                return input.toLowerCase();
            }
        }

    Pour ce faire implémenté une UDF qui accepte une valeur de chaîne et renvoie une version de la chaîne en minuscules.

## <a name="build-and-install-the-udf"></a>Générer et installer le fichier UDF

1. Utilisez la commande suivante pour compiler et empaqueter le fichier UDF :

        mvn compile package

    Cela va générer, puis compresser l’UDF en __exampleudf/target/ExampleUDF-1.0-SNAPSHOT.jar__.

2. Utiliser la `scp` commande copier le fichier pour le cluster HDInsight.

        scp ./target/ExampleUDF-1.0-SNAPSHOT.jar myuser@mycluster-ssh.azurehdinsight

    Remplacez __myuser__ par le compte d’utilisateur SSH pour votre cluster. Remplacez __mon_cluster__ par le nom du cluster. Si vous avez utilisé un mot de passe pour sécuriser le compte SSH, vous devrez entrer le mot de passe. Si vous avez utilisé un certificat, vous devrez peut-être utiliser les `-i` paramètre pour spécifier le fichier de clé privée.

3. Connectez-vous au cluster à l’aide de SSH. 

        ssh myuser@mycluster-ssh.azurehdinsight.net

    Pour plus d’informations sur l’utilisation de SSH avec HDInsight, voir les documents suivants.

    * [Utiliser le protocole SSH avec basé sur Linux Hadoop sur HDInsight de Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

    * [Utiliser le protocole SSH avec basé sur Linux Hadoop sur HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

4. À partir de la session SSH, copiez le fichier jar à HDInsight de stockage.

        hdfs dfs -put ExampleUDF-1.0-SNAPSHOT.jar /example/jars

## <a name="use-the-udf-from-hive"></a>Utiliser le fichier UDF Hive

1. Utilisez ce qui suit pour démarrer le client Beeline à partir de la session SSH.

        beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin

    Cette commande suppose que vous avez utilisé la valeur par défaut de __l’administrateur__ pour le compte de connexion pour votre cluster.

2. Une fois que vous atteindrez la `jdbc:hive2://localhost:10001/>` invite, entrez les informations suivantes pour ajouter le fichier UDF à Hive et exposer comme une fonction.

        ADD JAR wasbs:///example/jars/ExampleUDF-1.0-SNAPSHOT.jar;
        CREATE TEMPORARY FUNCTION tolower as 'com.microsoft.examples.ExampleUDF';

3. Utilisez le fichier UDF pour convertir des valeurs récupérées à partir d’un tableau en chaînes de minuscules.

        SELECT tolower(deviceplatform) FROM hivesampletable LIMIT 10;

    Cela sera sélectionnez la plateforme de l’appareil (Android, Windows, iOS, etc.) dans le tableau, convertir la chaîne en minuscules et puis affichez-les. Le résultat sera semblable au suivant.

        +----------+--+
        |   _c0    |
        +----------+--+
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        +----------+--+

## <a name="next-steps"></a>Étapes suivantes

Pour d’autres façons de travailler avec Hive, voir [Utiliser la ruche avec HDInsight](hdinsight-use-hive.md).

Pour plus d’informations sur les fonctions Hive User-Defined, consultez la section [ruche des opérateurs et des fonctions définies par l’utilisateur](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF) du wiki Hive sur le site apache.org.
