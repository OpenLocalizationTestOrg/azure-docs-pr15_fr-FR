<properties
    pageTitle="Créer une application HBase à l’aide de Maven et Java, puis déployer à HDInsight basé sur Linux | Microsoft Azure"
    description="Découvrez comment utiliser Maven Apache pour créer une application basée sur Java Apache HBase, puis déployer à HDInsight basé sur Linux dans le cloud Azure."
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="larryfr"/>

#<a name="use-maven-to-build-java-applications-that-use-hbase-with-linux-based-hdinsight-hadoop"></a>Maven permet de créer des applications Java qui utilisent HBase avec basé sur Linux HDInsight (Hadoop)

Découvrez comment créer et créer une application [Apache HBase](http://hbase.apache.org/) dans Java à l’aide de Apache Maven. Utilisez l’application avec un cluster basé sur Linux HDInsight.

[Maven](http://maven.apache.org/) est un outil de compréhension qui vous permet de créer des logiciels, documentation et rapports pour les projets Java et de gestion de projets logiciels. Dans cet article, vous allez apprendre à utiliser pour créer une application Java simple qui crée, requêtes et supprime un tableau HBase sur un cluster HDInsight basé sur Linux.

> [AZURE.NOTE] Les étapes décrites dans ce document part du principe que vous utilisez un cluster basé sur Linux HDInsight. Pour plus d’informations sur l’utilisation d’un cluster HDInsight fonctionnant sous Windows, voir [Utiliser Maven pour créer des applications Java qui utilisent HBase avec HDInsight fonctionnant sous Windows](hdinsight-hbase-build-java-maven.md)

##<a name="requirements"></a>Configuration requise

* [Plate-forme Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 7 ou version ultérieure

* [Maven](http://maven.apache.org/)

* [Un cluster basé sur Linux Azure HDInsight avec HBase](../hdinsight-hbase-tutorial-get-started-linux.md#create-hbase-cluster)

    > [AZURE.NOTE] Les étapes décrites dans ce document ont été testés avec des versions HDInsight cluster 3.2, 3.3 et 3.4. Les valeurs par défaut fournies dans les exemples sont pour un cluster 3.4 HDInsight.

* **Familiarisation avec SSH et SCP**. Pour plus d’informations sur l’utilisation de SSH et SCP avec HDInsight, voir les rubriques suivantes :

    * **Les clients Linux, Unix ou OS X**: voir [Utiliser SSH avec basé sur Linux Hadoop sur HDInsight de Linux, OS X ou Unix](hdinsight-hadoop-linux-use-ssh-unix.md)

    * **Les clients Windows**: consultez [Utiliser SSH avec basé sur Linux Hadoop sur HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

##<a name="create-the-project"></a>Créer le projet

1. À partir de la ligne de commande dans votre environnement de développement, accédez à l’emplacement où vous souhaitez créer le projet, par exemple, `cd code/hdinsight`.

2. Utilisez la commande __mvn__ , qui est installée avec Maven, qui pour permet de générer la génération de modèles automatique pour le projet.

        mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=hbaseapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

    Cela crée un nouveau répertoire dans le répertoire actif, avec le nom spécifié par le paramètre __artifactID__ (**hbaseapp** dans cet exemple). Ce répertoire contient les éléments suivants :

    * __pom.XML__: le modèle d’objet du projet ([POM](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html)) contient des informations et la configuration détails utilisés pour générer le projet.

    * __src__: le répertoire qui contient le répertoire __principaux/java/com/microsoft/exemples__ , vous allez créer l’application.

3. Supprimez le fichier __src/test/java/com/microsoft/examples/apptest.java__ car il n’est pas utilisée dans cet exemple.

##<a name="update-the-project-object-model"></a>Mettre à jour le modèle objet Project

1. Modifiez le fichier __pom.xml__ et ajoutez le code suivant à l’intérieur de la `<dependencies>` section :

        <dependency>
          <groupId>org.apache.hbase</groupId>
          <artifactId>hbase-client</artifactId>
          <version>1.1.2</version>
        </dependency>

    Maven vous indique que le projet requiert __hbase client__ version __1.1.2__. Au moment de la compilation, cela est téléchargé à partir du référentiel Maven par défaut. Vous pouvez utiliser la [Recherche référentiel Central Maven](http://search.maven.org/#artifactdetails%7Corg.apache.hbase%7Chbase-client%7C0.98.4-hadoop2%7Cjar) pour en savoir plus sur cette dépendance.

    > [AZURE.IMPORTANT] Le numéro de version doit correspondre à la version de HBase qui est fourni avec votre cluster HDInsight. Utilisez le tableau suivant pour trouver le numéro de version correct.

  	| Version de cluster HDInsight | Version HBase à utiliser |
  	| ----- | ----- |
  	| 3.2 | 0.98.4-hadoop2 |
  	| 3.3 et 3.4 | 1.1.2 |

    Pour plus d’informations sur les versions HDInsight et les composants, voir [Quels sont les différents composants Hadoop disponibles avec HDInsight](hdinsight-component-versioning.md).

2. Si vous utilisez un 3.3 HDInsight ou cluster 3.4, vous devez également ajouter des éléments suivants pour le `<dependencies>` section :

        <dependency>
            <groupId>org.apache.phoenix</groupId>
            <artifactId>phoenix-core</artifactId>
            <version>4.4.0-HBase-1.1</version>
        </dependency>
    
    Charger les composants phoenix cœur, qui sont nécessaires avec la version Hbase 1.1.x.

2. Ajoutez le code suivant dans le fichier __pom.xml__ . Il doit être à l’intérieur de la `<project>...</project>` balises dans le fichier, par exemple, entre `</dependencies>` et `</project>`.

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

    Cela permet de configurer une ressource (__la rendez-vous/hbase-site.xml__), qui contient des informations de configuration pour HBase.

    > [AZURE.NOTE] Vous pouvez également définir les valeurs de configuration via le code. Consultez les commentaires dans l’exemple de __Create table__ qui suit pour savoir comment procéder.

    Cela configure également le [Plug-in du compilateur Maven](http://maven.apache.org/plugins/maven-compiler-plugin/) et [Maven ombre du plug-in](http://maven.apache.org/plugins/maven-shade-plugin/). Le compilateur plug-in est utilisé pour compiler la topologie. L’ombrage du plug-in est utilisé pour empêcher la duplication de licence dans le package JAR intégré par Maven. La raison pour laquelle qu'il est utilisé est que les fichiers de licence en double génèrent une erreur en cours d’exécution sur le cluster HDInsight. À l’aide de maven-ombre-plug-in avec la `ApacheLicenseResourceTransformer` implémentation empêche cette erreur.

    Le maven-ombre-plug-in génère également un jar premiers (ou fat jar,) qui contient toutes les dépendances requises par l’application.

3. Enregistrez le fichier __pom.xml__ .

4. Créer un nouveau répertoire nommé __la rendez-vous__ dans le répertoire __hbaseapp__ . Cela servira pour conserver les informations de configuration pour la connexion à HBase.

5. Utilisez la commande suivante pour copier la configuration HBase à partir du serveur HDInsight à l’annuaire __la rendez-vous__ . Remplacer le **nom d’utilisateur** le nom de votre connexion SSH. Remplacez **NOMDUCLUSTER** par votre nom de cluster HDInsight :

        scp USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:/etc/hbase/conf/hbase-site.xml ./conf/hbase-site.xml

    > [AZURE.NOTE] Si vous avez utilisé un mot de passe pour votre compte SSH, vous devrez entrer le mot de passe. Si vous avez utilisé un code SSH avec le compte, vous devrez peut-être utiliser les `-i` paramètre pour spécifier le chemin d’accès au fichier de clé. L’exemple suivant se charge de la clé privée `~/.ssh/id_rsa`:
    >
    > `scp -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:/etc/hbase/conf/hbase-site.xml ./conf/hbase-site.xml`

##<a name="create-the-application"></a>Créer l’application

1. Accédez au répertoire __hbaseapp/src/principaux/java/com/microsoft/exemples__ et renommez le fichier app.java en __CreateTable.java__.

2. Ouvrez le fichier __CreateTable.java__ et remplacez le contenu existant avec les éléments suivants :

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
            //
            //NOTE: Actual zookeeper host names can be found using Ambari:
            //curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts"
            
            //Linux-based HDInsight clusters use /hbase-unsecure as the znode parent
            config.set("zookeeper.znode.parent","/hbase-unsecure");

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

4. Dans le répertoire __hbaseapp/src/principaux/java/com/microsoft/exemples__ , créez un nouveau fichier nommé __SearchByEmail.java__. Utilisez ce qui suit le contenu de ce fichier :

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

6. Dans le répertoire __hbaseapp/src/principaux/hava/com/microsoft/exemples__ , créez un nouveau fichier nommé __DeleteTable.java__. Utilisez ce qui suit le contenu de ce fichier :

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

2. À partir du répertoire __hbaseapp__ , utilisez la commande suivante pour générer un fichier JAR qui contient l’application :

        mvn clean package

    Cela supprime les objets de génération précédente, télécharge les dépendances qui n’ont pas été déjà installés, puis crée et conditionne l’application.

3. Lorsque la commande est terminée, le répertoire __hbaseapp/cible__ contiendra un fichier nommé __hbaseapp-1.0-SNAPSHOT.jar__.

    > [AZURE.NOTE] Le fichier __hbaseapp-1.0-SNAPSHOT.jar__ est un premiers jar (parfois appelé un jar fat,) qui contient toutes les dépendances requises pour exécuter l’application.

##<a name="upload-the-jar-file-and-run-jobs"></a>Téléchargez le fichier JAR et l’exécution de tâches

1. Utiliser les éléments suivants pour télécharger le fichier jar au cluster HDInsight. Remplacer le **nom d’utilisateur** le nom de votre connexion SSH. Remplacez **NOMDUCLUSTER** par votre nom de cluster HDInsight :

        scp ./target/hbaseapp-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.

    Cela au répertoire de base pour votre compte d’utilisateur SSH pour télécharger le fichier.

    > [AZURE.NOTE] Si vous avez utilisé un mot de passe pour votre compte SSH, vous devrez entrer le mot de passe. Si vous avez utilisé un code SSH avec le compte, vous devrez peut-être utiliser les `-i` paramètre pour spécifier le chemin d’accès au fichier de clé. L’exemple suivant se charge de la clé privée `~/.ssh/id_rsa`:
    >
    > `scp -i ~/.ssh/id_rsa ./target/hbaseapp-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.`

2. Utiliser le protocole SSH pour vous connecter au cluster HDInsight. Remplacer le **nom d’utilisateur** le nom de votre connexion SSH. Remplacez **NOMDUCLUSTER** par votre nom de cluster HDInsight :

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    > [AZURE.NOTE] Si vous avez utilisé un mot de passe pour votre compte SSH, vous devrez entrer le mot de passe. Si vous avez utilisé un code SSH avec le compte, vous devrez peut-être utiliser les `-i` paramètre pour spécifier le chemin d’accès au fichier de clé. L’exemple suivant se charge de la clé privée `~/.ssh/id_rsa`:
    >
    > `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`

3. Une fois connecté, utilisez ce qui suit pour créer une nouvelle table HBase à l’aide de l’application Java :

        hadoop jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.CreateTable

    Cela sera créer une table HBase nommée __personnes__et y ajouter des données.

4. Ensuite, utilisez ce qui suit pour rechercher des adresses de messagerie stockées dans la table :

        hadoop jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.SearchByEmail contoso.com

    Vous devez recevoir les résultats suivants :

        Franklin Holtz - ID: 2
        Franklin Holtz - franklin@contoso.com - ID: 2
        Rae Schroeder - ID: 4
        Rae Schroeder - rae@contoso.com - ID: 4
        Gabriela Ingram - ID: 6
        Gabriela Ingram - gabriela@contoso.com - ID: 6

##<a name="delete-the-table"></a>Supprimer le tableau

Lorsque vous avez terminé avec l’exemple, utilisez la commande suivante à partir de la session PowerShell Azure pour supprimer la table de __personnes__ utilisée dans cet exemple :

    hadoop jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.DeleteTable

