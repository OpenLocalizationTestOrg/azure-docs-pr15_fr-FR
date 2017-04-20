<properties
   pageTitle="Analyser des données de capteur avec vague d’Apache et HBase | Microsoft Azure"
   description="Découvrez comment vous connecter à vague d’Apache avec un réseau virtuel. Utilisez vague d’avec HBase pour traiter les données capteur à partir d’un concentrateur de l’événement et visualiser avec D3.js."
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
   ms.date="09/20/2016"
   ms.author="larryfr"/>

# <a name="analyze-sensor-data-with-apache-storm-event-hub-and-hbase-in-hdinsight-hadoop"></a>Analyser des données de capteur avec vague d’Apache, événement concentrateur et HBase dans HDInsight (Hadoop) 

Découvrez comment utiliser vague d’Apache sur HDInsight pour traiter les données capteur Azure événement concentrateur, stockez-le dans Apache HBase sur HDInsight et visualiser à l’aide de D3.js en cours d’exécution comme une application Web Azure.

Le modèle Azure le Gestionnaire de ressources utilisé dans ce document montre comment créer plusieurs ressources Azure dans un groupe de ressources. En particulier, il crée un réseau virtuel Azure, les deux clusters HDInsight (vague d’et HBase) et une application Web Azure. Une mise en œuvre node.js d’un tableau de bord web en temps réel est déployé automatiquement à l’application web.

> [AZURE.NOTE] Les informations contenues dans ce document et l’exemple fourni, ont été testés à l’aide de basé sur Linux HDInsight 3.3 et 3.4 versions cluster.

## <a name="prerequisites"></a>Conditions préalables

* Un abonnement Azure. Voir [Azure obtenir la version d’évaluation gratuite](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

    > [AZURE.IMPORTANT] Vous n’avez pas besoin d’un cluster HDInsight existant ; les étapes décrites dans ce document créera les ressources suivantes :
    >
    > * Un réseau virtuel Azure
    > * Une vague de sur cluster HDInsight (basé sur Linux, 2 nœuds de travail)
    > * Un HBase sur HDInsight cluster (basé sur Linux, 2 nœuds de travail)
    > * Une application Web Azure qui héberge le tableau de bord web

* [Node.js](http://nodejs.org/): il est utilisé pour afficher un aperçu du tableau de bord web localement sur votre environnement de développement.

* [Java et le 1.7 JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html): permet de développer la topologie vague.

* [Maven](http://maven.apache.org/what-is-maven.html): permet de créer et de compilation du projet.

* [GIT](http://git-scm.com/): permet de télécharger le projet à partir de GitHub.

* __Un clientSSH__ : utilisé pour la connexion pour les groupes HDInsight basé sur Linux. Pour plus d’informations sur l’utilisation de SSH avec HDInsight, voir les documents suivants.

    * [Utiliser le protocole SSH avec HDInsight à partir d’un client Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

    * [Utiliser le protocole SSH avec HDInsight à partir d’un client Linux, Unix ou Mac](hdinsight-hadoop-linux-use-ssh-unix.md)

    > [AZURE.NOTE] Vous devez également avoir accès à la `scp` commande, qui est utilisé pour copier des fichiers entre votre environnement de développement local et le cluster HDInsight à l’aide de SSH.

## <a name="architecture"></a>Architecture

![diagramme d’architecture](./media/hdinsight-storm-sensor-data-analysis/devicesarchitecture.png)

Cet exemple se compose des éléments suivants :

* **Azure événement Hubs**: contient des données qui sont collectées à partir de capteurs. Dans cet exemple, une application est fournie qui génère les données.

* **Vague d’HDInsight**: fournit en temps réel traitement des données à partir de l’événement concentrateur.

* **HBase sur HDInsight**: offre un espace de stockage de données NoSQL permanente pour les données après qu’elle a été traitée par vague de.

* **Service de réseau virtuel Azure**: permet de sécuriser les communications entre la vague de sur HDInsight et HBase sur les clusters HDInsight.

    > [AZURE.NOTE] Un réseau virtuel est requis pour pouvoir utiliser l’API cliente Java HBase, tel qu’il n’est pas exposé sur la passerelle publique pour les clusters HBase. L’installation de clusters HBase et vague de dans le même réseau virtuel permet le cluster vague (ou tout autre système sur le réseau virtuel,) accéder directement à HBase à l’aide de l’API client.

* **Site Web de tableau de bord**: un tableau de bord exemple que les graphiques de données en temps réel.

    * Le site Web est activée dans Node.js, et il peut s’exécuter sur les systèmes d’exploitation client pour le test, ou elle peut être déployée à des sites Web Azure.

    * [Socket.IO](http://socket.io/) est utilisé pour la communication en temps réel entre la topologie vague et le site Web.

        > [AZURE.NOTE] Il s’agit d’un détail d’implémentation. Vous pouvez utiliser n’importe quelle infrastructure de communications, tels que WebSocket brutes ou SignalR.

    * [D3.js](http://d3js.org/) est utilisé pour représenter les données envoyées au site Web.

> [AZURE.IMPORTANT] Deux clusters sont nécessaires, car il n’existe aucune méthode pour créer un cluster HDInsight pour vague d’et HBase pris en charge.

La topologie lit les données à partir de l’événement concentrateur à l’aide de la classe [org.apache.storm.eventhubs.spout.EventHubSpout](http://storm.apache.org/releases/0.10.1/javadocs/org/apache/storm/eventhubs/spout/class-use/EventHubSpout.html) et écrit des données dans HBase à l’aide de la classe [org.apache.storm.hbase.bolt.HBaseBolt](https://storm.apache.org/javadoc/apidocs/org/apache/storm/hbase/bolt/class-use/HBaseBolt.html) . Communication avec le site Web est établie à l’aide de [socket.io client.java](https://github.com/nkzawa/socket.io-client.java).

Voici un diagramme de la topologie.

![diagramme de la topologie](./media/hdinsight-storm-sensor-data-analysis/sensoranalysis.png)

> [AZURE.NOTE] Il s’agit d’un affichage très simplifié de la topologie. En cours d’exécution, une instance de chaque composant est créée pour chaque partition pour le Hub de l’événement en cours de lecture. Ces instances sont réparties entre les nœuds du cluster et données routage entre elles comme suit :
>
> * Données de bec à l’analyseur sont équilibrée de charge.
> * Données de l’analyseur au tableau de bord et HBase sont regroupées par identificateur d’appareil, afin que les messages de l’appareil même flux toujours au même composant.

### <a name="topology-components"></a>Composants de topologie

* **Bec EventHub**: bec est fourni dans le cadre de version vague Apache 0.10.0 ou version ultérieure.

    > [AZURE.NOTE] BEC événement Hubs utilisé dans cet exemple requiert une vague d’HDInsight la version cluster 3.3 ou 3.4. Pour plus d’informations sur l’utilisation de Hubs événement avec une version antérieure de vague d’HDInsight, voir [traiter les événements de Azure événement Hubs avec vague d’HDInsight](hdinsight-storm-develop-java-event-hub-topology.md).

* **ParserBolt.java**: les données qui sont émises par bec JSON brut, et il peut arriver que plusieurs événement est émis à la fois. Cette boulons montre comment lire les données émises par bec et émettre pour un flux de données sous forme d’un uplet qui contient plusieurs champs.

* **DashboardBolt.java**: cette procédure montre comment utiliser la bibliothèque de client Socket.io pour Java pour envoyer des données temps réel au tableau de bord web.

Cet exemple utilise la structure de [Flux](https://storm.apache.org/releases/0.10.0/flux.html) , afin que la définition de topologie se trouve dans les fichiers YAML. Il existe deux :

* __n° hbase.yaml__ - utiliser ce fichier lorsque vous testez la topologie dans votre environnement de développement. Il n’utilise pas HBase composants, dans la mesure où vous ne peut pas accéder à l’API Java HBase à partir de l’extérieur du réseau virtuel dans que le cluster sont situées.

* __avec hbase.yaml__ - utiliser ce fichier lors du déploiement de la topologie pour le cluster vague. Il utilise les composants HBase dans la mesure où elle s’exécute sur le même réseau virtuel que le cluster HBase.

## <a name="prepare-your-environment"></a>Préparer votre environnement

Avant d’utiliser cet exemple, vous devez créer un concentrateur d’événement Azure, qui lit la topologie vague.

### <a name="configure-event-hub"></a>Configurer événement concentrateur

Événement concentrateur est la source de données pour cet exemple. Procédez comme suit pour créer un nouveau concentrateur de l’événement.

1. À partir du [portail Azure](https://portal.azure.com), sélectionnez **+ Nouveau** -> __Internet des objets__ -> __Événement Hubs__.

2. Dans la carte __Namespace créer__ , exécutez les tâches suivantes :

    1. Entrez un __nom__ pour l’espace de noms.
    2. Sélectionnez un niveau de tarification. __Base__ est suffisant pour cet exemple.
    3. Sélectionnez l' __abonnement__ Azure à utiliser.
    4. Sélectionnez un groupe de ressources existant ou créez-en un.
    5. Sélectionnez l' __emplacement__ pour le Hub de l’événement.
    6. Sélectionnez __épingler au tableau de bord__, puis cliquez sur __créer__.

3. Une fois le processus de création terminée, la carte Hubs événement pour votre espace de noms s’affiche. De là, sélectionnez __+ Ajouter événement concentrateur__. Dans la carte __Créer événement concentrateur__ , entrez un nom de __sensordata__ , puis sélectionnez __créer__. Laisser les autres champs définis par défaut.

4. À partir de la carte Hubs événement pour votre espace de noms, sélectionnez __Événement Hubs__. Sélectionnez l’entrée __sensordata__ .

5. À partir de la carte pour la sensordata concentrateur de l’événement, sélectionnez __stratégies d’accès partagé__. Cliquez sur le lien __+ Ajouter__ pour ajouter les stratégies suivantes :


  	| Nom de la stratégie | Revendications |
  	| ----- | ----- |
  	| appareils mobiles | Envoyer |
  	| vague d' | Écouter |

5. Sélectionnez les deux stratégies, prenez note de la valeur de __Clé primaire__ . Vous aurez besoin de la valeur pour les deux stratégies aux étapes suivantes.

## <a name="download-and-configure-the-project"></a>Téléchargez et configurez le projet

Utilisez ce qui suit pour télécharger le projet à partir de GitHub.

    git clone https://github.com/Blackmist/hdinsight-eventhub-example

Une fois la commande terminée, vous devrez l’arborescence suivante :

    hdinsight-eventhub-example/
        TemperatureMonitor/ - this contains the topology
            resources/
                log4j2.xml - set logging to minimal
                no-hbase.yaml - topology definition for local testing
                with-hbase.yaml - topology definition that uses HBase in a virutal network
            src/ - the Java bolts
            dev.properties - contains configuration values for your environment
        dashboard/nodejs/ - this is the node.js web dashboard
        SendEvents/ - utilities to send fake sensor data

> [AZURE.NOTE] Ce document ne sont pas acheminés vers tous les détails du code inclus dans cet exemple ; Toutefois, le code est entièrement commenté.

Ouvrez le fichier **hdinsight-eventhub-example/TemperatureMonitor/dev.properties** et ajoutez vos informations d’événement concentrateur pour les lignes suivantes :

    eventhub.read.policy.name: storm
    eventhub.read.policy.key: KeyForTheStormPolicy
    eventhub.namespace: YourNamespace
    eventhub.name: sensordata

> [AZURE.NOTE] Cet exemple suppose que vous avez utilisé __vague__ d’en tant que le nom de la stratégie qui comporte une demande de remboursement __écouter__ , et que votre plateforme d’événement se nomme __sensordata__.

 Enregistrez le fichier après l’ajout de ces informations.

## <a name="compile-and-test-locally"></a>Compiler et tester localement

Avant le test, vous devez démarrer le tableau de bord pour afficher les résultats de la topologie et générer des données à stocker dans le Hub de l’événement.

> [AZURE.IMPORTANT] Le composant HBase de cette topologie n’est pas actif lorsque test localement, comme l’API Java pour le cluster HBase ne sont pas accessibles à partir de l’extérieur du réseau virtuel Azure qui contient les groupes.

### <a name="start-the-web-application"></a>Démarrez l’application web

1. Ouvrez une nouvelle invite de commande ou le terminal et accédez au répertoire **hdinsight-eventhub-exemple/tableau de bord**, puis utilisez la commande suivante pour installer les dépendances requises par l’application web :

        npm install

2. Utilisez la commande suivante pour démarrer l’application web :

        node server.js

    Vous devriez voir un message semblable à ce qui suit :

        Server listening at port 3000

2. Ouvrez un navigateur web, puis entrez **http://localhost:3000 /** comme adresse. Vous devriez voir une page semblable à ce qui suit :

    ![tableau de bord Web](./media/hdinsight-storm-sensor-data-analysis/emptydashboard.png)

    Laissez cette invite de commandes ou terminal ouverte. Après le test, utilisez Ctrl + C pour arrêter le serveur web.

### <a name="start-generating-data"></a>Commencer à générer des données

> [AZURE.NOTE] Les étapes décrites dans cette section utilisent Node.js afin qu’ils peuvent être utilisés dans n’importe quelle plate-forme. Pour d’autres exemples de langue, reportez-vous au répertoire **SendEvents** .

1. Ouvrez une nouvelle invite, shell ou terminal, accédez au répertoire **hdinsight-eventhub-exemple/SendEvents/nodejs**, puis utilisez la commande suivante pour installer les dépendances nécessaires à l’application :

        npm install

2. Ouvrez le fichier **app.js** dans un éditeur de texte et ajoutez les informations d’événement concentrateur obtenues précédemment :

        // ServiceBus Namespace
        var namespace = 'YourNamespace';
        // Event Hub Name
        var hubname ='sensordata';
        // Shared access Policy name and key (from Event Hub configuration)
        var my_key_name = 'devices';
        var my_key = 'YourKey';
    
    > [AZURE.NOTE] Cet exemple suppose que vous avez utilisé __sensordata__ en tant que le nom de votre événement concentrateur et __périphériques__ en tant que le nom de la stratégie qui comporte une demande de remboursement __Envoyer__ .

2. Utiliser la commande suivante pour insérer de nouvelles entrées dans le Hub de l’événement :

        node app.js

    Vous devriez voir plusieurs lignes de résultat qui contiennent les données envoyées à concentrateur de l’événement. Ces sera semblables à ce qui suit :

        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"0","Temperature":7}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"1","Temperature":39}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"2","Temperature":86}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"3","Temperature":29}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"4","Temperature":30}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"5","Temperature":5}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"6","Temperature":24}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"7","Temperature":40}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"8","Temperature":43}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"9","Temperature":84}

### <a name="start-the-topology"></a>Démarrer la topologie

2. Ouvrez une nouvelle invite, shell ou terminal et modifier des répertoires à __Hdinsight-eventhub-exemple/TemperatureMonitor__et ensuite utiliser la commande suivante pour démarrer la topologie :

        mvn compile exec:java -Dexec.args="--local -R /no-hbase.yaml --filter dev.properties"
    
    Si vous utilisez PowerShell, utilisez à la place :

        mvn compile exec:java "-Dexec.args=--local -R /no-hbase.yaml --filter dev.properties"

    > [AZURE.NOTE] Si vous êtes sur un système Linux/Unix/OS X et que vous avez [installé vague de dans votre environnement de développement](http://storm.apache.org/releases/0.10.0/Setting-up-development-environment.html), vous pouvez utiliser les commandes suivantes à la place :
    >
    > `mvn compile package`
    > `storm jar target/WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local -R /no-hbase.yaml`

    Cette opération démarre la topologie définie dans le fichier __n° hbase.yaml__ en mode local. Les valeurs contenues dans le fichier __dev.properties__ fournissent les informations de connexion pour Hubs événement. Une fois démarré, la topologie lit les entrées à partir de l’événement concentrateur et les envoie au tableau de bord en cours d’exécution sur votre ordinateur local. Vous devriez voir les lignes apparaissent dans le tableau de bord web, similaire à ce qui suit :

    ![tableau de bord avec données](./media/hdinsight-storm-sensor-data-analysis/datadashboard.png)

3. Tandis que le tableau de bord est en cours d’exécution, utilisez la `node app.js` commande des étapes précédentes pour envoyer les nouvelles données aux événements Hubs. Étant donné que les valeurs de température sont générés aléatoirement, le graphique doit mettre à jour pour afficher les modifications importantes dans température.

    > [AZURE.NOTE] Vous devez être dans le répertoire __Hdinsight-eventhub-exemple/SendEvents/Nodejs__ lorsque vous utilisez la `node app.js` commande.

3. Après avoir vérifié que cela fonctionne, arrêtez la topologie à l’aide de Ctrl + C. Vous pouvez utiliser Ctrl + C pour arrêter le serveur web local également.

## <a name="create-a-storm-and-hbase-cluster"></a>Créer un cluster vague d’et HBase

Pour exécuter la topologie sur HDInsight et activer les boulons HBase, vous devez créer un HBase et le nouveau cluster vague. Les étapes décrites dans cette section utilisent un [modèle de gestionnaire de ressources Azure](../resource-group-template-deploy.md) pour créer un nouveau cluster réseau virtuel Azure et vague d’et HBase sur le réseau virtuel. Le modèle crée une application Web Azure également et déploie une copie du tableau de bord dans celle-ci.

> [AZURE.NOTE] Un réseau virtuel est utilisé pour que la topologie en cours d’exécution sur le cluster vague peut communiquer directement avec le cluster HBase à l’aide de l’API Java HBase.

Le modèle de gestionnaire de ressources utilisé dans ce document se trouve dans un conteneur blob publique à __https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-hbase-storm-cluster-in-vnet.json__.

1. Cliquez sur le bouton suivant pour se connecter à Azure et ouvrez le modèle de gestionnaire de ressources dans le portail Azure.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hbase-storm-cluster-in-vnet.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. À partir de la carte de **paramètres** , entrez les informations suivantes :

    ![Paramètres de HDInsight](./media/hdinsight-storm-sensor-data-analysis/parameters.png)
    
    * **BASECLUSTERNAME**: cette valeur sera utilisée comme nom de base pour la vague d’et HBase clusters. Par exemple, une saisie __hdi__ créera un cluster vague nommé __vague d’hdi__ et un cluster HBase nommé __hbase hdi__.
    * __CLUSTERLOGINUSERNAME__: le nom d’utilisateur pour les groupes vague d’et HBase.
    * __CLUSTERLOGINPASSWORD__: le mot de passe d’administrateur pour les groupes vague d’et HBase.
    * __SSHUSERNAME__: le SSH utilisateur créer pour les groupes vague d’et HBase.
    * __SSHPASSWORD__: le mot de passe pour l’utilisateur SSH pour les groupes vague d’et HBase.
    * __Emplacement__: la région qui seront créés dans les groupes.
    
    Cliquez sur __OK__ pour enregistrer les paramètres.
    
3. Utilisez la section de __groupe de ressources__ pour créer un nouveau groupe de ressources ou sélectionnez-en un.

4. Dans le menu déroulant de la __position du groupe de ressources__ , sélectionnez l’emplacement de même que celle sélectionnée pour le paramètre __d’emplacement__ .

5. Sélectionnez les __conditions juridiques__et puis cliquez sur __créer__.

6. Pour finir, cochez __épingler au tableau de bord__ , puis sélectionnez __créer__. Vous devrez patienter environ 20 minutes pour créer les groupes.

Une fois que les ressources ont été créés, vous êtes redirigé vers une carte du groupe de ressources qui contient les clusters et tableau de bord web.

![Carte de groupe de ressources pour la vnet et clusters](./media/hdinsight-storm-sensor-data-analysis/groupblade.png)

> [AZURE.IMPORTANT] Notez que les noms des groupes HDInsight sont __Vague de-nom de base__ et __Hbase-nom de base__, où nom de base est le nom que vous avez fourni pour le modèle. Vous allez utiliser ces noms dans les étapes suivantes lorsque vous vous connectez pour les groupes. Notez également que le nom du site du tableau de bord est le __nom de base-tableau de bord__. Vous allez utiliser cette ultérieurement lorsque vous affichez le tableau de bord.

## <a name="configure-the-dashboard-bolt"></a>Configurer les boulons de tableau de bord

Pour envoyer des données au tableau de bord déployé comme une application web, vous devez modifier la ligne suivante dans le fichier __dev.properties__ :

    dashboard.uri: http://localhost:3000

Modification `http://localhost:3000` à `http://BASENAME-dashboard.azurewebsites.net` et enregistrez le fichier. Remplacez le __nom de base__ avec le nom de base que vous avez indiquées dans l’étape précédente. Vous pouvez également utiliser le groupe de ressources créé précédemment pour sélectionner le tableau de bord et d’afficher l’URL.

## <a name="create-the-hbase-table"></a>Créer une table HBase

Pour stocker des données dans HBase, nous devons tout d’abord créer une table. Vous souhaitez généralement antérieurs ressources vague de doit écrire sur la création, comme la création de ressources à l’intérieur d’une topologie vague peuvent entraîner plusieurs copies distribuées du code tentez de créer la même ressource. Créer les ressources à l’extérieur de la topologie et simplement utiliser vague de pour lecture/écriture et analytique.

1. Utiliser le protocole SSH pour vous connecter au cluster HBase à l’aide de la SSH utilisateur et mot de passe fourni pour le modèle lors de la création de cluster. Par exemple, si la connexion à l’aide du `ssh` commande, vous utilisez la syntaxe suivante :

        ssh USERNAME@hbase-BASENAME-ssh.azurehdinsight.net
    
    Dans cette commande, remplacez le __nom d’utilisateur__ par le nom d’utilisateur SSH fourni lorsque vous créez le cluster et le __nom de base__ avec le nom de base que vous avez fourni. Lorsque vous y êtes invité, entrez le mot de passe pour l’utilisateur SSH.

2. À partir de la session SSH, démarrez le shell HBase.

        hbase shell
    
    Une fois que le shell a chargé, vous verrez un `hbase(main):001:0>` invite de commandes.

3. À partir du shell HBase, entrez la commande suivante pour créer une table pour stocker les données de capteur :

        create 'SensorData', 'cf'

4. Vérifiez que la table a été créée à l’aide de la commande suivante :

        scan 'SensorData'
        
    Ceci renvoie des informations similaires à l’exemple suivant, qui indique qu’il existe 0 lignes du tableau.
    
        ROW                   COLUMN+CELL                                       0 row(s) in 0.1900 seconds

5. Entrez les informations suivantes pour quitter le shell HBase :

        exit

## <a name="configure-the-hbase-bolt"></a>Configurer les boulons HBase

Pour écrire sur HBase le cluster vague, vous devez fournir les boulons HBase avec les détails de la configuration de votre cluster HBase. Le plus simple consiste à télécharger le __hbase site.xml__ du cluster et inclure dans votre projet. Vous devez également effacer les commentaires plusieurs dépendances dans le fichier __pom.xml__ , laquelle chargement le composant vague hbase et dépendances nécessaires.

> [AZURE.IMPORTANT] Vous devez également télécharger le fichier vague hbase.jar fourni sur votre vague de sur HDInsight 3.3 ou 3.4 cluster ; Cette version est compilée pour travailler avec HBase 1.1.x, qui est utilisé pour HBase sur HDInsight 3.3 et 3.4 clusters. Si vous utilisez un composant vague hbase ailleurs, il peut être compilé par rapport à une version antérieure de HBase.

### <a name="download-the-hbase-sitexml"></a>Télécharger le site.xml hbase

À partir d’une invite de commandes, utilisez SCP pour télécharger le fichier __hbase site.xml__ depuis le cluster. Dans l’exemple suivant, remplacez __nom_utilisateur__ par l’utilisateur SSH que vous avez fourni lorsque vous créez le cluster et le __nom de base__ avec le nom de base que vous avez spécifié précédemment. Lorsque vous y êtes invité, entrez le mot de passe pour l’utilisateur SSH. Remplacer le `/path/to/TemperatureMonitor/resources/hbase-site.xml` avec le chemin d’accès à ce fichier dans le projet TemperatureMonitor.

    scp USERNAME@hbase-BASENAME-ssh.azurehdinsight.net:/etc/hbase/conf/hbase-site.xml /path/to/TemperatureMonitor/resources/hbase-site.xml

Cela se télécharge le __hbase site.xml__ sur le chemin d’accès spécifié.

### <a name="download-and-install-the-storm-hbase-component"></a>Téléchargez et installez le composant vague hbase

1. À partir d’une invite de commandes, utilisez SCP à télécharger le fichier __vague d’hbase.jar__ du cluster vague. Dans l’exemple suivant, remplacez __nom_utilisateur__ par l’utilisateur SSH que vous avez fourni lorsque vous créez le cluster et le __nom de base__ avec le nom de base que vous avez spécifié précédemment. Lorsque vous y êtes invité, entrez le mot de passe pour l’utilisateur SSH.

        scp USERNAME@storm-BASENAME-ssh.azurehdinsight.net:/usr/hdp/current/storm-client/contrib/storm-hbase/storm-hbase*.jar .

    Cela télécharge un fichier nommé `storm-hbase-####.jar`, où ## est le numéro de version de vague de pour ce cluster. Prenez note de ce numéro, tel qu’il est utilisé ultérieurement.

2. Utilisez la commande suivante pour installer ce composant dans le référentiel Maven local sur votre environnement de développement. Cela permet de Maven rechercher le package lors de la compilation du projet. Remplacer __####__ avec le numéro de version inclus dans le nom du fichier.

        mvn install:install-file -Dfile=storm-hbase-####.jar -DgroupId=org.apache.storm -DartifactId=storm-hbase -Dversion=#### -Dpackaging=jar
    
    Si vous utilisez PowerShell, utilisez la commande suivante :

        mvn install:install-file "-Dfile=storm-hbase-####.jar" "-DgroupId=org.apache.storm" "-DartifactId=storm-hbase" "-Dversion=####" "-Dpackaging=jar"

### <a name="enable-the-storm-hbase-component-in-the-project"></a>Activer le composant vague hbase dans le projet

1. Ouvrez le fichier __TemperatureMonitor/pom.xml__ et supprimez les lignes suivantes :

        <!-- uncomment this section to enable the hbase-bolt
        end comment for hbase-bolt section -->
    
    > [AZURE.IMPORTANT] Supprimer uniquement ces deux lignes ; ne supprimez pas les lignes entre elles.
    
    Cela permet de plusieurs composants qui sont nécessaires pour communiquer avec HBase à l’aide de la boulons hbase.

2. Recherchez les lignes suivantes et remplacer __####__ avec le numéro de version du fichier vague hbase téléchargé précédemment.

        <dependency>
            <groupId>org.apache.storm</groupId>
            <artifactId>storm-hbase</artifactId>
            <version>####</version>
        </dependency>

    > [AZURE.IMPORTANT] Le numéro de version doit correspondre à la version que vous avez utilisé lors de l’installation du composant dans le référentiel Maven local, comme Maven utilise ces informations pour charger le composant lors de la création du projet.

2. Enregistrez le fichier __pom.xml__ .

## <a name="build-package-and-deploy-the-solution-to-hdinsight"></a>Créer, package et déployer la solution à HDInsight

Dans votre environnement de développement, suivez les étapes suivantes pour déployer la topologie vague au cluster vague.

1. À partir du répertoire __TemperatureMonitor__ , utilisez la commande suivante pour effectuer une nouvelle version et créer un package JAR à partir de votre projet :

        mvn clean compile package

    Cela créera un fichier nommé **TemperatureMonitor-1.0-SNAPSHOT.jar** dans le répertoire **cible** de votre projet.

2. Scp permet de télécharger le fichier __TemperatureMonitor-1.0-SNAPSHOT.jar__ dans votre cluster vague. Dans l’exemple suivant, remplacez __nom_utilisateur__ par l’utilisateur SSH que vous avez fourni lorsque vous créez le cluster et le __nom de base__ avec le nom de base que vous avez spécifié précédemment. Lorsque vous y êtes invité, entrez le mot de passe pour l’utilisateur SSH.

        scp target\TemperatureMonitor-1.0-SNAPSHOT.jar USERNAME@storm-BASENAME-ssh.azurehdinsight.net:TemperatureMonitor-1.0-SNAPSHOT.jar
    
    > [AZURE.NOTE] Il peut prendre plusieurs minutes pour télécharger le fichier, car il sera plusieurs mégaoctets.

    Utilisez scp à télécharger le fichier __dev.properties__ , car il contient les informations utilisées pour se connecter à l’événement Hubs et le tableau de bord.

        scp dev.properties USERNAME@storm-BASENAME-ssh.azurehdinsight.net:dev.properties

3. Une fois que les fichiers ont été téléchargés, connectez-vous au cluster à l’aide de SSH.

        ssh USERNAME@storm-BASENAME-ssh.azurehdinsight.net

4. À partir de la session SSH, utilisez la commande suivante pour démarrer la topologie.

        storm jar TemperatureMonitor-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /with-hbase.yaml --filter dev.properties
    
    Cette opération démarre la topologie à l’aide de la définition de topologie dans le fichier __avec hbase.yaml__ et les valeurs de configuration dans le fichier __dev.properties__ .

3. Une fois la topologie a déjà commencé, ouvrez un navigateur pour le site Web que vous avez publié sur Azure, puis utilisez le `node app.js` commande pour envoyer des données à l’événement concentrateur. Vous devriez voir le tableau de bord web mettre à jour pour afficher les informations.

    ![tableau de bord](./media/hdinsight-storm-sensor-data-analysis/datadashboard.png)

## <a name="view-hbase-data"></a>Afficher les données HBase

Une fois que vous avez envoyé des données de la topologie à l’aide `node app.js`, procédez comme suit pour vous connecter à HBase et vérifiez que les données ont été écrites dans la table que vous avez créée.

1. Utiliser le protocole SSH pour vous connecter au cluster HBase.

        ssh USERNAME@hbase-BASENAME-ssh.azurehdinsight.net

2. À partir de la session SSH, démarrez le shell HBase.

        hbase shell
    
    Une fois que le shell a chargé, vous verrez un `hbase(main):001:0>` invite de commandes.

2. Afficher les lignes de la table :

        scan 'SensorData'
        
    Cela doit renvoyer des informations similaires à ce qui suit, qui indique qu’il existe 0 lignes du tableau.
    
        hbase(main):002:0> scan 'SensorData'
        ROW                             COLUMN+CELL
        \x00\x00\x00\x00               column=cf:temperature, timestamp=1467290788277, value=\x00\x00\x00\x04
        \x00\x00\x00\x00               column=cf:timestamp, timestamp=1467290788277, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x01               column=cf:temperature, timestamp=1467290788348, value=\x00\x00\x00M
        \x00\x00\x00\x01               column=cf:timestamp, timestamp=1467290788348, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x02               column=cf:temperature, timestamp=1467290788268, value=\x00\x00\x00R
        \x00\x00\x00\x02               column=cf:timestamp, timestamp=1467290788268, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x03               column=cf:temperature, timestamp=1467290788269, value=\x00\x00\x00#
        \x00\x00\x00\x03               column=cf:timestamp, timestamp=1467290788269, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x04               column=cf:temperature, timestamp=1467290788356, value=\x00\x00\x00>
        \x00\x00\x00\x04               column=cf:timestamp, timestamp=1467290788356, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x05               column=cf:temperature, timestamp=1467290788326, value=\x00\x00\x00\x0D
        \x00\x00\x00\x05               column=cf:timestamp, timestamp=1467290788326, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x06               column=cf:temperature, timestamp=1467290788253, value=\x00\x00\x009
        \x00\x00\x00\x06               column=cf:timestamp, timestamp=1467290788253, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x07               column=cf:temperature, timestamp=1467290788229, value=\x00\x00\x00\x12
        \x00\x00\x00\x07               column=cf:timestamp, timestamp=1467290788229, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x08               column=cf:temperature, timestamp=1467290788336, value=\x00\x00\x00\x16
        \x00\x00\x00\x08               column=cf:timestamp, timestamp=1467290788336, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x09               column=cf:temperature, timestamp=1467290788246, value=\x00\x00\x001
        \x00\x00\x00\x09               column=cf:timestamp, timestamp=1467290788246, value=2015-02-10T14:43.05.00320Z
        10 row(s) in 0.1800 seconds

    > [AZURE.NOTE] Cette opération d’analyse retournera uniquement un maximum de 10 lignes de la table.

## <a name="delete-your-clusters"></a>Supprimer vos clusters

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Pour supprimer les groupes, stockage et web app en même temps, supprimer le groupe de ressources qui contient les.

## <a name="next-steps"></a>Étapes suivantes

Vous avez appris maintenant comment utiliser vague de pour lire les données d’événement Hubs, stockez-le dans HBase et visualiser les informations dans un tableau de bord externe à l’aide de Socket.io et D3.js.

* Pour plus d’exemples de topologies vague avec HDInsight, voir :

    * [Exemples de topologies pour vague d’HDInsight](hdinsight-storm-example-topology.md)

* Pour plus d’informations sur vague d’Apache, consultez le site [Vague d’Apache](https://storm.incubator.apache.org/) .

* Pour plus d’informations sur HBase sur HDInsight, voir la [HBase HDInsight vue d’ensemble](hdinsight-hbase-overview.md).

* Pour plus d’informations sur Socket.io, consultez le site [socket.io](http://socket.io/) .

* Pour plus d’informations sur D3.js, voir [D3.js - Documents par l’effort de données](http://d3js.org/).

* Pour plus d’informations sur la création de topologies en Java, voir [topologies Java développer pour vague d’Apache sur HDInsight](hdinsight-storm-develop-java-topology.md).

* Pour plus d’informations sur la création des topologies dans .NET, voir [développer c# topologies pour vague d’Apache sur HDInsight à l’aide de Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md).

[azure-portal]: https://portal.azure.com
