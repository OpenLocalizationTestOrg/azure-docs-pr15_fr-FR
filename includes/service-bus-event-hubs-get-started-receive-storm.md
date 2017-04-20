## <a name="receive-messages-with-apache-storm"></a>Recevoir des messages avec vague d’Apache

[**Vague d’Apache**](https://storm.incubator.apache.org) est un système de calcul en temps réel distribué qui simplifie le traitement fiable de flux illimitées de données. Cette section montre comment utiliser un bec vagues Hubs événements pour recevoir des événements à partir de l’événement Hubs. À l’aide de vague d’Apache, vous pouvez répartir des événements sur plusieurs processus hébergés dans des nœuds différents. L’intégration d’événement Hubs avec vague de simplifie la consommation événement en point de contrôle en toute transparence sa progression à l’aide de l’installation de vague soigneur, la gestion des points de contrôle permanentes et parallèle reçoit des événements Hubs.

Pour plus d’informations sur l’événement Hubs motifs de réception, reportez-vous à la [vue d’ensemble de l’événement Hubs][].

Ce didacticiel utilise une installation [Vague d’HDInsight][] , qui est fourni avec bec Hubs événement déjà disponible.

1. Suivez la procédure [Vague d’HDInsight - prise en main](../articles/hdinsight/hdinsight-storm-overview.md) pour créer un nouveau cluster HDInsight et s’y connecter via le Bureau à distance.

2. Copier la `%STORM_HOME%\examples\eventhubspout\eventhubs-storm-spout-0.9-jar-with-dependencies.jar` fichier pour votre environnement de développement local. Cet onglet contient le bec de vague d’événements.

3. Utiliser la commande suivante pour installer le package dans le magasin Maven local. Cela vous permet d’ajouter une référence dans le projet vague dans une étape ultérieure.

        mvn install:install-file -Dfile=target\eventhubs-storm-spout-0.9-jar-with-dependencies.jar -DgroupId=com.microsoft.eventhubs -DartifactId=eventhubs-storm-spout -Dversion=0.9 -Dpackaging=jar

4. Dans Eclipse, créez un nouveau projet Maven (cliquez sur **fichier**, **Nouveau**, puis **projet**).

    ![][12]

5. Sélectionnez **l’emplacement d’espace de travail utiliser par défaut**, puis cliquez sur **suivant**

6. Sélectionnez l’archétype **maven-archétype-démarrage rapide** , puis cliquez sur **suivant**

7. Insérer un **GroupId** et **ArtifactId**, puis cliquez sur **Terminer**

8. Dans **pom.xml**, ajoutez les dépendances suivantes dans le `<dependency>` nœud.

        <dependency>
            <groupId>org.apache.storm</groupId>
            <artifactId>storm-core</artifactId>
            <version>0.9.2-incubating</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>com.microsoft.eventhubs</groupId>
            <artifactId>eventhubs-storm-spout</artifactId>
            <version>0.9</version>
        </dependency>
        <dependency>
            <groupId>com.netflix.curator</groupId>
            <artifactId>curator-framework</artifactId>
            <version>1.3.3</version>
            <exclusions>
                <exclusion>
                    <groupId>log4j</groupId>
                    <artifactId>log4j</artifactId>
                </exclusion>
                <exclusion>
                    <groupId>org.slf4j</groupId>
                    <artifactId>slf4j-log4j12</artifactId>
                </exclusion>
            </exclusions>
            <scope>provided</scope>
        </dependency>

9. Dans le dossier **src** , créez un fichier nommé **config.Properties** et copiez le contenu suivant, en remplaçant les valeurs suivantes :

        eventhubspout.username = ReceiveRule

        eventhubspout.password = {receive rule key}

        eventhubspout.namespace = ioteventhub-ns

        eventhubspout.entitypath = {event hub name}

        eventhubspout.partitions.count = 16

        # if not provided, will use storm's zookeeper settings
        # zookeeper.connectionstring=localhost:2181

        eventhubspout.checkpoint.interval = 10

        eventhub.receiver.credits = 10

    La valeur de **eventhub.receiver.credits** détermine le nombre d’événements est regroupé avant de les publier le pipeline de vague. Pour des raisons de simplicité, cet exemple définit cette valeur sur 10. En production, elle doit généralement définie à des valeurs élevées ; par exemple, 1024.

10. Créer une nouvelle classe appelée **LoggerBolt** par le code suivant :

        import java.util.Map;
        import org.slf4j.Logger;
        import org.slf4j.LoggerFactory;
        import backtype.storm.task.OutputCollector;
        import backtype.storm.task.TopologyContext;
        import backtype.storm.topology.OutputFieldsDeclarer;
        import backtype.storm.topology.base.BaseRichBolt;
        import backtype.storm.tuple.Tuple;

        public class LoggerBolt extends BaseRichBolt {
            private OutputCollector collector;
            private static final Logger logger = LoggerFactory
                      .getLogger(LoggerBolt.class);

            @Override
            public void execute(Tuple tuple) {
                String value = tuple.getString(0);
                logger.info("Tuple value: " + value);

                collector.ack(tuple);
            }

            @Override
            public void prepare(Map map, TopologyContext context, OutputCollector collector) {
                this.collector = collector;
                this.count = 0;
            }

            @Override
            public void declareOutputFields(OutputFieldsDeclarer declarer) {
                // no output fields
            }

        }

    Cette boulons vague enregistre le contenu des événements reçus. Cela peut être facilement étendu destiné au stockage de tuples dans un service de stockage. Le [didacticiel d’analyse HDInsight capteur] utilise cette approche pour stocker des données HBase.

11. Créez une classe appelée **LogTopology** par le code suivant :

        import java.io.FileReader;
        import java.util.Properties;
        import backtype.storm.Config;
        import backtype.storm.LocalCluster;
        import backtype.storm.StormSubmitter;
        import backtype.storm.generated.StormTopology;
        import backtype.storm.topology.TopologyBuilder;
        import com.microsoft.eventhubs.samples.EventCount;
        import com.microsoft.eventhubs.spout.EventHubSpout;
        import com.microsoft.eventhubs.spout.EventHubSpoutConfig;

        public class LogTopology {
            protected EventHubSpoutConfig spoutConfig;
            protected int numWorkers;

            protected void readEHConfig(String[] args) throws Exception {
                Properties properties = new Properties();
                if (args.length > 1) {
                    properties.load(new FileReader(args[1]));
                } else {
                    properties.load(EventCount.class.getClassLoader()
                            .getResourceAsStream("Config.properties"));
                }

                String username = properties.getProperty("eventhubspout.username");
                String password = properties.getProperty("eventhubspout.password");
                String namespaceName = properties
                        .getProperty("eventhubspout.namespace");
                String entityPath = properties.getProperty("eventhubspout.entitypath");
                String zkEndpointAddress = properties
                        .getProperty("zookeeper.connectionstring"); // opt
                int partitionCount = Integer.parseInt(properties
                        .getProperty("eventhubspout.partitions.count"));
                int checkpointIntervalInSeconds = Integer.parseInt(properties
                        .getProperty("eventhubspout.checkpoint.interval"));
                int receiverCredits = Integer.parseInt(properties
                        .getProperty("eventhub.receiver.credits")); // prefetch count
                                                                    // (opt)
                System.out.println("Eventhub spout config: ");
                System.out.println("  partition count: " + partitionCount);
                System.out.println("  checkpoint interval: "
                        + checkpointIntervalInSeconds);
                System.out.println("  receiver credits: " + receiverCredits);

                spoutConfig = new EventHubSpoutConfig(username, password,
                        namespaceName, entityPath, partitionCount, zkEndpointAddress,
                        checkpointIntervalInSeconds, receiverCredits);

                // set the number of workers to be the same as partition number.
                // the idea is to have a spout and a logger bolt co-exist in one
                // worker to avoid shuffling messages across workers in storm cluster.
                numWorkers = spoutConfig.getPartitionCount();

                if (args.length > 0) {
                    // set topology name so that sample Trident topology can use it as
                    // stream name.
                    spoutConfig.setTopologyName(args[0]);
                }
            }

            protected StormTopology buildTopology() {
                TopologyBuilder topologyBuilder = new TopologyBuilder();

                EventHubSpout eventHubSpout = new EventHubSpout(spoutConfig);
                topologyBuilder.setSpout("EventHubsSpout", eventHubSpout,
                        spoutConfig.getPartitionCount()).setNumTasks(
                        spoutConfig.getPartitionCount());
                topologyBuilder
                        .setBolt("LoggerBolt", new LoggerBolt(),
                                spoutConfig.getPartitionCount())
                        .localOrShuffleGrouping("EventHubsSpout")
                        .setNumTasks(spoutConfig.getPartitionCount());
                return topologyBuilder.createTopology();
            }

            protected void runScenario(String[] args) throws Exception {
                boolean runLocal = true;
                readEHConfig(args);
                StormTopology topology = buildTopology();
                Config config = new Config();
                config.setDebug(false);

                if (runLocal) {
                    config.setMaxTaskParallelism(2);
                    LocalCluster localCluster = new LocalCluster();
                    localCluster.submitTopology("test", config, topology);
                    Thread.sleep(5000000);
                    localCluster.shutdown();
                } else {
                    config.setNumWorkers(numWorkers);
                    StormSubmitter.submitTopology(args[0], config, topology);
                }
            }

            public static void main(String[] args) throws Exception {
                LogTopology topology = new LogTopology();
                topology.runScenario(args);
            }
        }


    Cette classe crée un nouveau Hubs événement bec, en utilisant les propriétés de la configuration classez-le à instatiate. Il est important de noter que cet exemple crée autant de tâches becs verseurs comme étant le nombre de partitions dans le Hub de l’événement, pour pouvoir utiliser le parallélisme maximal autorisé par ce concentrateur de l’événement.

<!-- Links -->
[Vue d’ensemble des Hubs événement]: ../articles/event-hubs/event-hubs-overview.md
[Vague d’HDInsight]: ../articles/hdinsight/hdinsight-storm-overview.md
[Didacticiel d’analyse HDInsight capteur]: ../articles/hdinsight/hdinsight-storm-sensor-data-analysis.md

<!-- Images -->

[12]: ./media/service-bus-event-hubs-get-started-receive-storm/create-storm1.png