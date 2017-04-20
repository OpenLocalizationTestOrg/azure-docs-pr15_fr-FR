<properties
pageTitle="Créer une application HBase à l’aide de Maven et déployer à HDInsight fonctionnant sous Windows | Microsoft Azure"
description="Découvrez comment utiliser Maven Apache pour créer une application basée sur Java Apache HBase, puis déployer vers un cluster basé sur Windows Azure HDInsight."
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="jhubbard"
editor="cgronlun"
tags="azure-portal"/>

<tags
ms.service="hdinsight"
ms.workload="big-data"
ms.tgt_pltfrm="na"
ms.devlang="na"
ms.topic="article"
ms.date="10/03/2016"
ms.author="larryfr"/>

#<a name="use-maven-to-build-java-applications-that-use-hbase-with-windows-based-hdinsight-hadoop"></a>Maven permet de créer des applications Java qui utilisent HBase avec HDInsight fonctionnant sous Windows (Hadoop)

Découvrez comment créer et créer une application [Apache HBase](http://hbase.apache.org/) dans Java à l’aide de Apache Maven. Utilisez l’application avec Azure HDInsight (Hadoop).

[Maven](http://maven.apache.org/) est un outil de compréhension qui vous permet de créer des logiciels, documentation et rapports pour les projets Java et de gestion de projets logiciels. Dans cet article, vous apprenez à utiliser pour créer une application Java simple qui crée, requêtes, et supprime un tableau HBase sur un cluster Azure HDInsight.

> [AZURE.NOTE] Les étapes décrites dans ce document part du principe que vous utilisez un cluster HDInsight fonctionnant sous Windows. Pour plus d’informations sur l’utilisation d’un cluster basé sur Linux HDInsight, voir [Utiliser Maven pour créer des applications Java qui utilisent HBase avec HDInsight basé sur Linux](hdinsight-hbase-build-java-maven-linux.md)

##<a name="requirements"></a>Configuration requise

* [Plate-forme Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 7 ou version ultérieure

* [Maven](http://maven.apache.org/)


* [Un cluster fonctionnant sous Windows HDInsight avec HBase](hdinsight-hbase-tutorial-get-started.md#create-hbase-cluster)


    > [AZURE.NOTE] Les étapes décrites dans ce document ont été testés avec des versions HDInsight cluster 3.2 et 3.3. Les valeurs par défaut fournies dans les exemples sont pour un cluster 3.3 HDInsight.

##<a name="create-the-project"></a>Créer le projet

1. À partir de la ligne de commande dans votre environnement de développement, accédez à l’emplacement où vous souhaitez créer le projet, par exemple, `cd code\hdinsight`.

2. Utilisez la commande __mvn__ , qui est installée avec Maven, qui pour permet de générer la génération de modèles automatique pour le projet.

        mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=hbaseapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

    Cette commande crée un répertoire à l’emplacement actuel, avec le nom spécifié par le paramètre __artifactID__ (**hbaseapp** dans cet exemple). Ce répertoire contient les éléments suivants :

    * __pom.XML__: le modèle d’objet du projet ([POM](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html)) contient des informations et la configuration détails utilisés pour générer le projet.

    * __src__: le répertoire qui contient le répertoire __main\java\com\microsoft\examples__ , vous allez créer l’application.

3. Supprimez le fichier __src\test\java\com\microsoft\examples\apptest.java__ car il n’est pas utilisé dans cet exemple.

##<a name="update-the-project-object-model"></a>Mettre à jour le modèle objet Project

1. Modifiez le fichier __pom.xml__ et ajoutez le code suivant à l’intérieur de la `<dependencies>` section :

        <dependency>
          <groupId>org.apache.hbase</groupId>
          <artifactId>hbase-client</artifactId>
          <version>1.1.2</version>
        </dependency>

    Cette section explique Maven que le projet requiert __hbase client__ version __1.1.2__. Au moment de la compilation, cette interdépendance est téléchargée à partir du référentiel Maven par défaut. Vous pouvez utiliser la [Recherche référentiel Central Maven](http://search.maven.org/#artifactdetails%7Corg.apache.hbase%7Chbase-client%7C0.98.4-hadoop2%7Cjar) pour en savoir plus sur cette dépendance.

    > [AZURE.IMPORTANT] Le numéro de version doit correspondre à la version de HBase qui est fourni avec votre cluster HDInsight. Utilisez le tableau suivant pour trouver le numéro de version correct.

  	| Version de cluster HDInsight | Version HBase à utiliser |
  	| ----- | ----- |
  	| 3.2 | 0.98.4-hadoop2 |
  	| 3.3 | 1.1.2 |

    Pour plus d’informations sur les versions HDInsight et les composants, voir [Quels sont les différents composants Hadoop disponibles avec HDInsight](hdinsight-component-versioning.md).

2. Si vous utilisez un cluster 3.3 HDInsight, vous devez également ajouter des éléments suivants pour le `<dependencies>` section :

        <dependency>
            <groupId>org.apache.phoenix</groupId>
            <artifactId>phoenix-core</artifactId>
            <version>4.4.0-HBase-1.1</version>
        </dependency>
    
    Cette dépendance va charger les composants phoenix cœur, qui sont utilisés par Hbase version 1.1.x.

2. Ajoutez le code suivant dans le fichier __pom.xml__ . Cette section doit être à l’intérieur de la `<project>...</project>` balises dans le fichier, par exemple, entre `</dependencies>` et `</project>`.

        <build>
          <sourceDirectory>src</sourceDirectory>
          <resources>
              <resource>
                <directory>${basedir}/conf</directory>
                <filtering>false</filtering>
                <includes>
                  <include>hbase-site.xml</include>
                </includes>
              </resource>
            </resources>
          <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.3</version>
                <configuration>
                    <source>1.7</source>
                    <target>1.7</target>
                </configuration>
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

    La `<resources>` section configure une ressource (__conf\hbase site.xml__) qui contient des informations de configuration pour HBase.

    > [AZURE.NOTE] Vous pouvez également définir les valeurs de configuration via le code. Consultez les commentaires dans l’exemple de __Create table__ qui suit pour savoir comment procéder.

    Cela `<plugins>` section configure les [Plug-in du compilateur Maven](http://maven.apache.org/plugins/maven-compiler-plugin/) et les [Plug-in d’appliquer un ombrage Maven](http://maven.apache.org/plugins/maven-shade-plugin/). Le compilateur plug-in est utilisé pour compiler la topologie. L’ombrage du plug-in est utilisé pour empêcher la duplication de licence dans le package JAR intégré par Maven. La raison pour laquelle qu'il est utilisé est que les fichiers de licence en double génèrent une erreur en cours d’exécution sur le cluster HDInsight. À l’aide de maven-ombre-plug-in avec la `ApacheLicenseResourceTransformer` implémentation empêche cette erreur.

    Le maven-ombre-plug-in génère également une premiers jar (ou fat jar) qui contient toutes les dépendances requises par l’application.

3. Enregistrez le fichier __pom.xml__ .

4. Créer un nouveau répertoire nommé __la rendez-vous__ dans le répertoire __hbaseapp__ . Dans le répertoire __la rendez-vous__ , créez un fichier nommé __hbase site.xml__. En tant que le contenu du fichier, utilisez ce qui suit :

        <?xml version="1.0"?>
        <?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
        <!--
        /**
          * Copyright 2010 The Apache Software Foundation
          *
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
        -->
        <configuration>
          <property>
            <name>hbase.cluster.distributed</name>
            <value>true</value>
          </property>
          <property>
            <name>hbase.zookeeper.quorum</name>
            <value>zookeeper0,zookeeper1,zookeeper2</value>
          </property>
          <property>
            <name>hbase.zookeeper.property.clientPort</name>
            <value>2181</value>
          </property>
        </configuration>

    Ce fichier permet de charger la configuration HBase pour un cluster HDInsight.

    > [AZURE.NOTE] Il s’agit d’un fichier hbase-site.xml minimale, et elle contient les paramètres minimales vierge pour le cluster HDInsight.

3. Enregistrez le fichier __hbase site.xml__ .

##<a name="create-the-application"></a>Créer l’application

1. Accédez au répertoire __hbaseapp\src\main\java\com\microsoft\examples__ et renommez le fichier app.java en __CreateTable.java__.

2. Ouvrez le fichier __CreateTable.java__ et remplacez le contenu existant par le code suivant :

        package com.microsoft.examples;
        import java.io.IOException;

        import org.apache.hadoop.conf.Configuration;
        import org.apache.hadoop.hbase.HBaseConfiguration;
        import org.apache.hadoop.hbase.client.HBaseAdmin;
        import org.apache.hadoop.hbase.HTableDescriptor;
        import org.apache.hadoop.hbase.TableName;
        import org.apache.hadoop.hbase.HColumnDescriptor;
        import org.apache.hadoop.hbase.client.HTable;
        import org.apache.hadoop.hbase.client.Put;
        import org.apache.hadoop.hbase.util.Bytes;

        public class CreateTable {
          public static void main(String[] args) throws IOException {
            Configuration config = HBaseConfiguration.create();

            // Example of setting zookeeper values for HDInsight
            // in code instead of an hbase-site.xml file
            //
            // config.set("hbase.zookeeper.quorum",
            //            "zookeepernode0,zookeepernode1,zookeepernode2");
            //config.set("hbase.zookeeper.property.clientPort", "2181");
            //config.set("hbase.cluster.distributed", "true");
            // The following sets the znode root for Linux-based HDInsight
            //config.set("zookeeper.znode.parent","/hbase-unsecure");

            // create an admin object using the config
            HBaseAdmin admin = new HBaseAdmin(config);

            // create the table...
            HTableDescriptor tableDescriptor = new HTableDescriptor(TableName.valueOf("people"));
            // ... with two column families
            tableDescriptor.addFamily(new HColumnDescriptor("name"));
            tableDescriptor.addFamily(new HColumnDescriptor("contactinfo"));
            admin.createTable(tableDescriptor);

            // define some people
            String[][] people = {
                { "1", "Marcel", "Haddad", "marcel@fabrikam.com"},
                { "2", "Franklin", "Holtz", "franklin@contoso.com" },
                { "3", "Dwayne", "McKee", "dwayne@fabrikam.com" },
                { "4", "Rae", "Schroeder", "rae@contoso.com" },
                { "5", "Rosalie", "burton", "rosalie@fabrikam.com"},
                { "6", "Gabriela", "Ingram", "gabriela@contoso.com"} };

            HTable table = new HTable(config, "people");

            // Add each person to the table
            //   Use the `name` column family for the name
            //   Use the `contactinfo` column family for the email
            for (int i = 0; i< people.length; i++) {
              Put person = new Put(Bytes.toBytes(people[i][0]));
              person.add(Bytes.toBytes("name"), Bytes.toBytes("first"), Bytes.toBytes(people[i][1]));
              person.add(Bytes.toBytes("name"), Bytes.toBytes("last"), Bytes.toBytes(people[i][2]));
              person.add(Bytes.toBytes("contactinfo"), Bytes.toBytes("email"), Bytes.toBytes(people[i][3]));
              table.put(person);
            }
            // flush commits and close the table
            table.flushCommits();
            table.close();
          }
        }

    Il s’agit de la classe __Create table__ , ce qui crée une table nommée __personnes__ et remplissez-la avec certains utilisateurs prédéfinis.

3. Enregistrez le fichier __CreateTable.java__ .

4. Dans le répertoire __hbaseapp\src\main\java\com\microsoft\examples__ , créez un nouveau fichier nommé __SearchByEmail.java__. Utilisez le code suivant en tant que le contenu de ce fichier :

        package com.microsoft.examples;
        import java.io.IOException;

        import org.apache.hadoop.conf.Configuration;
        import org.apache.hadoop.hbase.HBaseConfiguration;
        import org.apache.hadoop.hbase.client.HTable;
        import org.apache.hadoop.hbase.client.Scan;
        import org.apache.hadoop.hbase.client.ResultScanner;
        import org.apache.hadoop.hbase.client.Result;
        import org.apache.hadoop.hbase.filter.RegexStringComparator;
        import org.apache.hadoop.hbase.filter.SingleColumnValueFilter;
        import org.apache.hadoop.hbase.filter.CompareFilter.CompareOp;
        import org.apache.hadoop.hbase.util.Bytes;
        import org.apache.hadoop.util.GenericOptionsParser;

        public class SearchByEmail {
          public static void main(String[] args) throws IOException {
            Configuration config = HBaseConfiguration.create();

            // Use GenericOptionsParser to get only the parameters to the class
            // and not all the parameters passed (when using WebHCat for example)
            String[] otherArgs = new GenericOptionsParser(config, args).getRemainingArgs();
            if (otherArgs.length != 1) {
              System.out.println("usage: [regular expression]");
              System.exit(-1);
            }

            // Open the table
            HTable table = new HTable(config, "people");

            // Define the family and qualifiers to be used
            byte[] contactFamily = Bytes.toBytes("contactinfo");
            byte[] emailQualifier = Bytes.toBytes("email");
            byte[] nameFamily = Bytes.toBytes("name");
            byte[] firstNameQualifier = Bytes.toBytes("first");
            byte[] lastNameQualifier = Bytes.toBytes("last");

            // Create a new regex filter
            RegexStringComparator emailFilter = new RegexStringComparator(otherArgs[0]);
            // Attach the regex filter to a filter
            //   for the email column
            SingleColumnValueFilter filter = new SingleColumnValueFilter(
              contactFamily,
              emailQualifier,
              CompareOp.EQUAL,
              emailFilter
            );

            // Create a scan and set the filter
            Scan scan = new Scan();
            scan.setFilter(filter);

            // Get the results
            ResultScanner results = table.getScanner(scan);
            // Iterate over results and print  values
            for (Result result : results ) {
              String id = new String(result.getRow());
              byte[] firstNameObj = result.getValue(nameFamily, firstNameQualifier);
              String firstName = new String(firstNameObj);
              byte[] lastNameObj = result.getValue(nameFamily, lastNameQualifier);
              String lastName = new String(lastNameObj);
              System.out.println(firstName + " " + lastName + " - ID: " + id);
              byte[] emailObj = result.getValue(contactFamily, emailQualifier);
              String email = new String(emailObj);
              System.out.println(firstName + " " + lastName + " - " + email + " - ID: " + id);
            }
            results.close();
            table.close();
          }
        }

    La classe __SearchByEmail__ peut être utilisée pour interroger les lignes par adresse de messagerie. Parce qu’il utilise un filtre d’expressions régulières, vous pouvez fournir une chaîne ou une expression régulière lors de l’utilisation de la classe.

5. Enregistrez le fichier __SearchByEmail.java__ .

6. Dans le répertoire __hbaseapp\src\main\hava\com\microsoft\examples__ , créez un nouveau fichier nommé __DeleteTable.java__. Utilisez le code suivant en tant que le contenu de ce fichier :

        package com.microsoft.examples;
        import java.io.IOException;

        import org.apache.hadoop.conf.Configuration;
        import org.apache.hadoop.hbase.HBaseConfiguration;
        import org.apache.hadoop.hbase.client.HBaseAdmin;

        public class DeleteTable {
          public static void main(String[] args) throws IOException {
            Configuration config = HBaseConfiguration.create();

            // Create an admin object using the config
            HBaseAdmin admin = new HBaseAdmin(config);

            // Disable, and then delete the table
            admin.disableTable("people");
            admin.deleteTable("people");
          }
        }

    Ce cours est de nettoyage de cet exemple en désactivant et suppression de la table créée par la classe __Create table__ .

7. Enregistrez le fichier __DeleteTable.java__ .

##<a name="build-and-package-the-application"></a>Générer et empaqueter l’application

1. Ouvrez une invite de commandes, puis accédez au répertoire __hbaseapp__ .

2. Utiliser la commande suivante pour créer un fichier JAR qui contient l’application :

        mvn clean package

    Cela supprime les objets de génération précédente, télécharge les dépendances qui n’ont pas été déjà installés, puis crée et conditionne l’application.

3. Lorsque la commande est terminée, le répertoire __hbaseapp\target__ contient un fichier nommé __hbaseapp-1.0-SNAPSHOT.jar__.

    > [AZURE.NOTE] Le fichier __hbaseapp-1.0-SNAPSHOT.jar__ est un premiers jar (parfois appelé un jar fat,) qui contient toutes les dépendances requises pour exécuter l’application.

##<a name="upload-the-jar-file-and-start-a-job"></a>Téléchargez le fichier JAR et démarrer une tâche

Il existe plusieurs manières de télécharger un fichier sur votre cluster HDInsight, comme décrit dans [télécharger des données pour les projets Hadoop de HDInsight](hdinsight-upload-data.md). Les étapes suivantes utilisent Azure PowerShell.

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]


1. Après avoir installé et configuré Azure PowerShell, créez un nouveau fichier nommé __hbase runner.psm1__. Utilisez ce qui suit le contenu de ce fichier :

        <#
        .SYNOPSIS
        Copies a file to the primary storage of an HDInsight cluster.
        .DESCRIPTION
        Copies a file from a local directory to the blob container for
        the HDInsight cluster.
        .EXAMPLE
        Start-HBaseExample -className "com.microsoft.examples.CreateTable"
        -clusterName "MyHDInsightCluster"
        
        .EXAMPLE
        Start-HBaseExample -className "com.microsoft.examples.SearchByEmail"
        -clusterName "MyHDInsightCluster"
        -emailRegex "contoso.com"
        
        .EXAMPLE
        Start-HBaseExample -className "com.microsoft.examples.SearchByEmail"
        -clusterName "MyHDInsightCluster"
        -emailRegex "^r" -showErr
        #>
        
        function Start-HBaseExample {
        [CmdletBinding(SupportsShouldProcess = $true)]
        param(
        #The class to run
        [Parameter(Mandatory = $true)]
        [String]$className,
        
        #The name of the HDInsight cluster
        [Parameter(Mandatory = $true)]
        [String]$clusterName,
        
        #Only used when using SearchByEmail
        [Parameter(Mandatory = $false)]
        [String]$emailRegex,
        
        #Use if you want to see stderr output
        [Parameter(Mandatory = $false)]
        [Switch]$showErr
        )
        
        Set-StrictMode -Version 3
        
        # Is the Azure module installed?
        FindAzure
        
        # Get the login for the HDInsight cluster
        $creds = Get-Credential
        
        # Get storage information
        $storage = GetStorage -clusterName $clusterName
        
        # The JAR
        $jarFile = "wasbs:///example/jars/hbaseapp-1.0-SNAPSHOT.jar"
        
        # The job definition
        $jobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
            -JarFile $jarFile `
            -ClassName $className `
            -Arguments $emailRegex
        
        # Get the job output
        $job = Start-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobDefinition $jobDefinition `
            -HttpCredential $creds
        Write-Host "Wait for the job to complete ..." -ForegroundColor Green
        Wait-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobId $job.JobId `
            -HttpCredential $creds
        if($showErr)
        {
        Write-Host "STDERR"
        Get-AzureRmHDInsightJobOutput `
                    -Clustername $clusterName `
                    -JobId $job.JobId `
                    -DefaultContainer $storage.container `
                    -DefaultStorageAccountName $storage.storageAccount `
                    -DefaultStorageAccountKey $storage.storageAccountKey `
                    -HttpCredential $creds `
                    -DisplayOutputType StandardError
        }
        Write-Host "Display the standard output ..." -ForegroundColor Green
        Get-AzureRmHDInsightJobOutput `
                    -Clustername $clusterName `
                    -JobId $job.JobId `
                    -DefaultContainer $storage.container `
                    -DefaultStorageAccountName $storage.storageAccount `
                    -DefaultStorageAccountKey $storage.storageAccountKey `
                    -HttpCredential $creds
        }
        
        <#
        .SYNOPSIS
        Copies a file to the primary storage of an HDInsight cluster.
        .DESCRIPTION
        Copies a file from a local directory to the blob container for
        the HDInsight cluster.
        .EXAMPLE
        Add-HDInsightFile -localPath "C:\temp\data.txt"
        -destinationPath "example/data/data.txt"
        -ClusterName "MyHDInsightCluster"
        .EXAMPLE
        Add-HDInsightFile -localPath "C:\temp\data.txt"
        -destinationPath "example/data/data.txt"
        -ClusterName "MyHDInsightCluster"
        -Container "MyContainer"
        #>
        
        function Add-HDInsightFile {
            [CmdletBinding(SupportsShouldProcess = $true)]
            param(
                #The path to the local file.
                [Parameter(Mandatory = $true)]
                [String]$localPath,
                
                #The destination path and file name, relative to the root of the container.
                [Parameter(Mandatory = $true)]
                [String]$destinationPath,
                
                #The name of the HDInsight cluster
                [Parameter(Mandatory = $true)]
                [String]$clusterName,
                
                #If specified, overwrites existing files without prompting
                [Parameter(Mandatory = $false)]
                [Switch]$force
            )
            
            Set-StrictMode -Version 3
            
            # Is the Azure module installed?
            FindAzure
            
            # Get authentication for the cluster
            $creds=Get-Credential
            
            # Does the local path exist?
            if (-not (Test-Path $localPath))
            {
                throw "Source path '$localPath' does not exist."
            }
            
            # Get the primary storage container
            $storage = GetStorage -clusterName $clusterName
            
            # Upload file to storage, overwriting existing files if -force was used.
            Set-AzureStorageBlobContent -File $localPath `
                -Blob $destinationPath `
                -force:$force `
                -Container $storage.container `
                -Context $storage.context
        }
        
        function FindAzure {
            # Is there an active Azure subscription?
            $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
            if(-not($sub))
            {
                throw "No active Azure subscription found! If you have a subscription, use the Login-AzureRmAccount cmdlet to login to your subscription."
            }
        }
        
        function GetStorage {
            param(
                [Parameter(Mandatory = $true)]
                [String]$clusterName
            )
            $hdi = Get-AzureRmHDInsightCluster -ClusterName $clusterName
            # Does the cluster exist?
            if (!$hdi)
            {
                throw "HDInsight cluster '$clusterName' does not exist."
            }
            # Create a return object for context & container
            $return = @{}
            $storageAccounts = @{}
            
            # Get storage information
            $resourceGroup = $hdi.ResourceGroup
            $storageAccountName=$hdi.DefaultStorageAccount.split('.')[0]
            $container=$hdi.DefaultStorageContainer
            $storageAccountKey=(Get-AzureRmStorageAccountKey `
                -Name $storageAccountName `
            -ResourceGroupName $resourceGroup)[0].Value
            # Get the resource group, in case we need that
            $return.resourceGroup = $resourceGroup
            # Get the storage context, as we can't depend
            # on using the default storage context
            $return.context = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
            # Get the container, so we know where to
            # find/store blobs
            $return.container = $container
            # Return storage accounts to support finding all accounts for
            # a cluster
            $return.storageAccount = $storageAccountName
            $return.storageAccountKey = $storageAccountKey
            
            return $return
        }
        # Only export the verb-phrase things
        export-modulemember *-*

    Ce fichier contient deux modules :

    * __Ajouter HDInsightFile__ - permet de télécharger des fichiers à HDInsight

    * __Démarrage HBaseExample__ - utilisés pour exécuter les classes créés précédemment

2. Enregistrez le fichier __hbase runner.psm1__ .

3. Ouvrir une nouvelle fenêtre PowerShell Azure, accédez au répertoire __hbaseapp__ et puis exécutez la commande suivante.

        PS C:\ Import-Module c:\path\to\hbase-runner.psm1

    Modifier le chemin d’accès à l’emplacement du fichier __hbase runner.psm1__ créé précédemment. Cela permet d’enregistrer le module pour cette session PowerShell Azure.

2. Utiliser la commande suivante pour télécharger le __hbaseapp-1.0-SNAPSHOT.jar__ à votre cluster HDInsight.

        Add-HDInsightFile -localPath target\hbaseapp-1.0-SNAPSHOT.jar -destinationPath example/jars/hbaseapp-1.0-SNAPSHOT.jar -clusterName hdinsightclustername

    Remplacez __hdinsightclustername__ par le nom de votre cluster HDInsight. La commande télécharge le __hbaseapp-1.0-SNAPSHOT.jar__ à l’emplacement de __l’exemple/bocaux__ dans le stockage principal pour votre cluster HDInsight.

3. Une fois que les fichiers sont téléchargés, utilisez le code suivant pour créer une table à l’aide de la __hbaseapp__:

        Start-HBaseExample -className com.microsoft.examples.CreateTable -clusterName hdinsightclustername

    Remplacez __hdinsightclustername__ par le nom de votre cluster HDInsight.

    Cette commande crée une nouvelle table nommée __personnes__ dans votre cluster HDInsight. Cette commande n’affiche pas de sortie dans la fenêtre de la console.

2. Pour rechercher des entrées de la table, utilisez la commande suivante :

        Start-HBaseExample -className com.microsoft.examples.SearchByEmail -clusterName hdinsightclustername -emailRegex contoso.com

    Remplacez __hdinsightclustername__ par le nom de votre cluster HDInsight.

    Cette commande utilise la classe **SearchByEmail** pour rechercher les lignes dont la famille de colonne __contactinformation__ et la colonne de __messagerie__ , contient la chaîne __contoso.com__. Vous devez recevoir les résultats suivants :

          Franklin Holtz - ID: 2
          Franklin Holtz - franklin@contoso.com - ID: 2
          Rae Schroeder - ID: 4
          Rae Schroeder - rae@contoso.com - ID: 4
          Gabriela Ingram - ID: 6
          Gabriela Ingram - gabriela@contoso.com - ID: 6

    À l’aide de __fabrikam.com__ pour la `-emailRegex` valeur renvoie les utilisateurs qui ont __fabrikam.com__ dans le champ adresse de messagerie. Étant donné que cette recherche est activée à l’aide d’un filtre d’expression régulière, vous pouvez également entrer des expressions régulières, tels que __^ r__, les entrées qui renvoie l’endroit où le message commence par la lettre « r ».

##<a name="delete-the-table"></a>Supprimer le tableau

Lorsque vous avez terminé avec l’exemple, utilisez la commande suivante à partir de la session PowerShell Azure pour supprimer la table de __personnes__ utilisée dans cet exemple :

    Start-HBaseExample -className com.microsoft.examples.DeleteTable -clusterName hdinsightclustername

Remplacez __hdinsightclustername__ par le nom de votre cluster HDInsight.

##<a name="troubleshooting"></a>Résolution des problèmes

###<a name="no-results-or-unexpected-results-when-using-start-hbaseexample"></a>Aucun résultat ou des résultats inattendus lors de l’utilisation de démarrage HBaseExample

Utiliser la `-showErr` paramètre pour afficher l’erreur standard (STDERR) qui se produit lors de l’exécution de la tâche.
