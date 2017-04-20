<properties
   pageTitle="Déployer et gérer des topologies vague Apache sur basé sur Linux HDInsight | Microsoft Azure"
   description="Découvrez comment déployer, surveiller et gérer des topologies Apache vague à l’aide du tableau de bord vague de sur HDInsight basé sur Linux. Utiliser Hadoop tools pour Visual Studio."
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
   ms.date="09/07/2016"
   ms.author="larryfr"/>

# <a name="deploy-and-manage-apache-storm-topologies-on-linux-based-hdinsight"></a>Déployer et gérer des topologies vague Apache sur HDInsight basé sur Linux

Dans ce document, découvrir les notions de base de gestion et de surveillance topologies vague s’exécutant sur vague de Linux sur clusters HDInsight.

> [AZURE.IMPORTANT] Les étapes décrites dans cet article nécessitent une vague de basé sur Linux sur cluster HDInsight. Pour plus d’informations sur le déploiement et de surveillance topologies sur HDInsight fonctionnant sous Windows, voir [déployer et gérer des topologies vague Apache sur HDInsight fonctionnant sous Windows](hdinsight-storm-deploy-monitor-topology.md)

## <a name="prerequisites"></a>Conditions préalables

* **Vague d’A Linux sur cluster HDInsight**: voir [prise en main vague d’Apache sur HDInsight](hdinsight-apache-storm-tutorial-get-started-linux.md) les étapes de création d’un cluster

* **Familiarisation avec SSH et SCP**: pour plus d’informations sur l’utilisation de SSH et SCP avec HDInsight, consultez la rubrique suivante :

    * **Les clients Linux, Unix ou OS X**: voir [Utiliser SSH avec basé sur Linux Hadoop sur HDInsight de Linux, OS X ou Unix](hdinsight-hadoop-linux-use-ssh-unix.md)

    * **Les clients Windows**: consultez [Utiliser SSH avec basé sur Linux Hadoop sur HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

* **SCP un client**: cela est fourni avec tous les systèmes Linux, Unix et OS X. Pour les clients de Windows, nous vous recommandons PSCP, qui est disponible à partir de la [page de téléchargement PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

## <a name="start-a-storm-topology"></a>Démarrer une topologie vague

### <a name="using-ssh-and-the-storm-command"></a>À l’aide de SSH et la commande vague

1. Utiliser le protocole SSH pour vous connecter au cluster HDInsight. Remplacer le **nom d’utilisateur** le nom de votre connexion SSH. Remplacez **NOMDUCLUSTER** par votre nom de cluster HDInsight :

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    Pour plus d’informations sur l’utilisation de SSH pour vous connecter à votre cluster HDInsight, voir les documents suivants :
    
    * **Les clients Linux, Unix ou OS X**: voir [Utiliser SSH avec basé sur Linux Hadoop sur HDInsight de Linux, OS X ou Unix](hdinsight-hadoop-linux-use-ssh-unix.md)
    
    * **Les clients Windows**: consultez [Utiliser SSH avec basé sur Linux Hadoop sur HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

2. Utilisez la commande suivante pour démarrer un exemple de topologie :

        storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-0.9.3.2.2.4.9-1.jar storm.starter.WordCountTopology WordCount

    L’exemple de topologie WordCount démarre sur le cluster. Aléatoirement il générera phrases et pour compter les occurrences de chaque mot dans la phrase.

    > [AZURE.NOTE] Lorsque vous soumettez topologie pour le cluster, vous devez d’abord copier le fichier jar contenant le cluster avant d’utiliser la `storm` commande. Cela peut être effectuée à l’aide de la `scp` commande à partir du client où se trouve le fichier. Par exemple,`scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`
    >
    > L’exemple WordCount et autres exemples de starter vague, figurent déjà dans votre cluster en `/usr/hdp/current/storm-client/contrib/storm-starter/`.

### <a name="programmatically"></a>Par programme

Vous pouvez par programme déployez une topologie vague d’HDInsight en communication avec le service Nimbus hébergé dans votre cluster. [https://github.com/Azure-Samples/hdinsight-Java-Deploy-Storm-Topology](https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology) fournit un exemple d’application Java illustrant comment déployer et démarrer une topologie via le service Nimbus.

## <a name="monitor-and-manage-using-the-storm-command"></a>Surveiller et gérer à l’aide de la commande vague

La `storm` utilitaire permet de travailler avec exécution topologies à partir de la ligne de commande. Voici une liste des commandes fréquemment utilisées. Utiliser `storm -h` pour une liste complète des commandes.

### <a name="list-topologies"></a>Liste topologies

Utiliser la commande suivante pour obtenir la liste en cours d’exécution topologies :

    storm list
    
Ceci renvoie des informations semblables à ce qui suit :

    Topology_name        Status     Num_tasks  Num_workers  Uptime_secs
    -------------------------------------------------------------------
    WordCount            ACTIVE     29         2            263

### <a name="deactivate-and-reactivate"></a>Désactiver et réactiver

La désactivation d’une topologie il met en pause jusqu'à ce qu’il est supprimé ou réactivé. Pour désactiver et réactiver, utilisez ce qui suit :

    storm Deactivate TOPOLOGYNAME
    
    storm Activate TOPOLOGYNAME

### <a name="kill-a-running-topology"></a>Supprimer une topologie en cours d’exécution

Continueront topologies vague, une fois démarrés, en cours d’exécution jusqu'à l’arrêt. Pour arrêter une topologie, utilisez la commande suivante :

    storm stop TOPOLOGYNAME

### <a name="rebalance"></a>Rééquilibrage

Rééquilibrage une topologie permet au système afin de réviser le parallélisme de la topologie. Par exemple, si vous avez redimensionné le cluster pour ajouter plus de notes, rééquilibrage permettra à une topologie en cours d’exécution utiliser les nouveaux nœuds.

> [AZURE.WARNING] Rééquilibrage tout d’abord une topologie de désactive la topologie, puis répartit travailleurs de manière égale au sein du cluster, et enfin retourne la topologie de l’état que qui était dans avant rééquilibrage s’est produite. Donc si la topologie était active, il redevient active. Si elle a été désactivée, il reste désactivé.

    storm rebalance TOPOLOGYNAME

## <a name="monitor-and-manage-using-the-storm-ui"></a>Surveiller et gérer à l’aide de l’interface utilisateur vague d'

L’interface utilisateur vague de fournit une interface web pour l’utilisation de topologies en cours d’exécution et est inclus sur votre cluster HDInsight. Pour afficher l’interface utilisateur vague de, utilisez un navigateur web pour ouvrir __https://CLUSTERNAME.azurehdinsight.net/stormui__, où __CLUSTERNAME__ est le nom de votre cluster.

> [AZURE.NOTE] Si vous êtes invité à fournir un nom d’utilisateur et mot de passe, entrez l’administrateur de cluster (administrateur) et votre mot de passe que vous avez utilisé quand créer le cluster.


### <a name="main-page"></a>Page principale

La page principale de l’interface utilisateur vague de fournit les informations suivantes :

* **Cluster résumé**: informations de base concernant le cluster vague.

* **Topologie de synthèse**: une liste de l’exécution de topologies. Utilisez les liens de cette section pour afficher plus d’informations sur les topologies spécifiques.

* **Responsable résumé**: informations sur le contrôleur vague.

* **Configuration Nimbus**: configuration Nimbus pour le cluster.

### <a name="topology-summary"></a>Topologie de synthèse

Sélectionner un lien de la section **topologie de synthèse** affiche les informations suivantes sur la topologie :

* **Topologie de synthèse**: informations de base sur la topologie.

* **Actions de topologie**: actions de gestion que vous pouvez effectuer pour la topologie.

  * **Activer**: traitement de CV d’une topologie désactivée.

  * **Désactiver**: interrompt une topologie en cours d’exécution.

  * **Rééquilibrer**: ajuste le parallélisme de la topologie. Vous devez rééquilibrer topologies en cours d’exécution après avoir modifié le nombre de nœuds dans le cluster. Cela permet de la topologie d’ajustez le parallélisme à compense le nombre d’augmentation ou de diminution des nœuds dans le cluster.

    Pour plus d’informations, voir <a href="http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html" target="_blank">Présentation du parallélisme d’une topologie vague</a>.

  * **Supprimer**: termine une topologie vague après le délai spécifié.

* **Statistiques de topologie**: statistiques de la topologie. Utilisez les liens dans la colonne de la **fenêtre** pour définir la durée des autres entrées dans la page.

* **Becs verseurs amovibles**: les becs verseurs utilisées par la topologie. Utilisez les liens de cette section pour afficher plus d’informations sur becs verseurs spécifiques.

* **Boulons**: les boulons utilisées par la topologie. Utilisez les liens de cette section pour afficher plus d’informations sur les boulons spécifiques.

* **Configuration de la topologie**: la configuration de la topologie sélectionnée.

### <a name="spout-and-bolt-summary"></a>Bec et boulons résumé

Sélection d’un bec des sections **becs verseurs amovibles** ou **boulons** affiche les informations suivantes sur l’élément sélectionné :

* **Composant Résumé**: informations de base sur les boulons bec.

* **Statistiques bec/boulons**: statistiques sur les boulons bec. Utilisez les liens dans la colonne de la **fenêtre** pour définir la durée des autres entrées dans la page.

* **Statistiques de saisie** (boulons uniquement) : informations sur les flux d’entrée utilisés par les boulons.

* **Statistiques de sortie**: informations sur les flux de données émis par cet goulotte ou boulons.

* **Les exécuteurs**: informations sur les instances du bec ou boulons. Sélectionnez le **Port** entrée d’un administrateur afficher le journal des informations de diagnostic produites pour cette instance spécifique.

* **Erreurs**: les informations d’erreur pendant ce goulotte ou boulons.

## <a name="rest-api"></a>API REST

L’interface utilisateur vague d’intégré en haut de l’API REST, pour vous pouvez d’effectuer la gestion similaires et la fonctionnalité d’analyse à l’aide de l’API REST. Vous pouvez utiliser l’API REST pour créer des outils personnalisés pour gérer et surveiller topologies vague.

Pour plus d’informations, voir [L’API REST vague d’interface utilisateur](http://storm.apache.org/releases/0.9.6/STORM-UI-REST-API.html). Les informations suivantes sont spécifiques à l’utilisation de l’API REST avec vague d’Apache sur HDInsight.

> [AZURE.IMPORTANT] L’API REST vague de n’est pas disponible publiquement via internet et doit être accessible à l’aide d’un tunnel SSH vers le nœud de tête cluster HDInsight. Pour plus d’informations sur la création et à l’aide d’un tunnel SSH, voir [Utiliser SSH tunnel pour accéder à l’interface utilisateur Ambari web, ResourceManager, JobHistory, NameNode, Oozie et autres web l’interface utilisateur](hdinsight-linux-ambari-ssh-tunnel.md).

### <a name="base-uri"></a>URI de base

URI de base pour l’API REST sur clusters HDInsight basés sur Linux est disponible sur le nœud de tête en **https://HEADNODEFQDN:8744/api/v1/**; Toutefois, le nom de domaine du nœud principal est généré lors de la création de cluster et n’est pas statique.

Vous pouvez trouver le nom de domaine complet (FQDN) pour le nœud de tête cluster de différentes manières :

* __À partir d’un SSH session__: utilisez la commande `headnode -f` à partir d’une session SSH pour le cluster.

* __À partir du Web de Ambari__: sélectionnez les __Services__ à partir du haut de la page, puis sélectionnez __vague de__. À partir de l’onglet __Résumé__ , sélectionnez __Vague d’interface utilisateur Server__. Le nom de domaine complet du nœud qui la vague d’interface utilisateur et l’API REST s’exécute sur sera en haut de la page.

* __À partir de l’API Ambari REST__: utilisez la commande `curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/STORM/components/STORM_UI_SERVER"` pour récupérer des informations à propos du nœud la vague d’interface utilisateur et l’API REST s’exécutent sur. Remplacez le __mot de passe__ par le mot de passe d’administrateur pour le cluster. Remplacez __NOMDUCLUSTER__ par le nom du cluster. Dans la réponse, l’entrée « host_name » contient le nom de domaine complet du nœud.

### <a name="authentication"></a>Authentification

Demandes à l’API REST doivent utiliser **l’authentification de base**, afin que vous utilisez le nom de l’administrateur cluster HDInsight et le mot de passe.

> [AZURE.NOTE] Étant donné que l’authentification de base est envoyée à l’aide de texte clair, vous devez **toujours** utiliser HTTPS pour sécuriser les communications avec le cluster.

### <a name="return-values"></a>Valeurs de retour

Informations sont renvoyées à partir de l’API REST peuvent uniquement être utilisables à partir de dans le cluster ou machines virtuelles sur le même réseau virtuel Azure que le cluster. Par exemple, le nom de domaine complet (FQDN) renvoyé pour les serveurs soigneur ne sera pas accessible à partir d’Internet.

## <a name="next-steps"></a>Étapes suivantes

À présent que vous avez appris à déployer et surveiller topologies à l’aide du tableau de bord vague de, découvrez comment [topologies basé sur Java développer à l’aide de Maven](hdinsight-storm-develop-java-topology.md).

Pour une liste des exemples de topologies plus, voir [exemples de topologies pour vague d’HDInsight](hdinsight-storm-example-topology.md).
