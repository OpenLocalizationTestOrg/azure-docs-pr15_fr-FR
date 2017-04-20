<properties
    pageTitle="Utiliser teinte avec Hadoop sur clusters HDInsight Linux | Microsoft Azure"
    description="Découvrez comment installer et utiliser teinte avec clusters Hadoop sur HDInsight Linux."
    services="hdinsight"
    documentationCenter=""
    authors="nitinme"
    manager="jhubbard"
    editor="cgronlun"/>

<tags 
    ms.service="hdinsight" 
    ms.workload="big-data" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/13/2016" 
    ms.author="nitinme"/>

# <a name="install-and-use-hue-on-hdinsight-hadoop-clusters"></a>Installer et utiliser teinte sur HDInsight Hadoop clusters

Découvrez comment installer teinte sur clusters HDInsight Linux et utiliser tunnel pour acheminer les demandes teinte.

## <a name="what-is-hue"></a>Qu’est teinte ?

Teinte est un ensemble d’applications Web utilisé pour interagir avec un cluster Hadoop. Vous pouvez utiliser teinte pour parcourir le stockage associé à un cluster Hadoop (WASB, dans le cas HDInsight clusters), puis exécutez les tâches Hive et scripts cochon, etc.. Les composants suivants sont disponibles avec les installations teinte sur un cluster HDInsight Hadoop.

* Éditeur de Hive cire
* Cochon
* Gestionnaire de Metastore
* Oozie
* FileBrowser (qui parle au conteneur par défaut WASB)
* Navigateur de travail

> [AZURE.WARNING] Composants fournis avec le cluster HDInsight entièrement prises en charge et Support Microsoft aidera à identifier et résoudre les problèmes liés à ces composants.
>
> Composants personnalisés recevront RCSI prise en charge pour vous aider à résoudre le problème. Cela peut provoquer de résoudre le problème ou vous demandant de s’engager canaux disponibles pour les technologies d’ouvrir la source où se trouve une grande expertise pour que la technologie. Par exemple, de nombreux sites communautaires qui peuvent être utilisés, comme : [forum MSDN pour HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com). Projets Apache contient des sites de projet sur [http://apache.org](http://apache.org), par exemple : [Hadoop](http://hadoop.apache.org/).

## <a name="install-hue-using-script-actions"></a>Installer teinte à l’aide des Actions de Script

L’action de script suivantes peut servir à installer teinte sur un cluster HDInsight basé sur Linux.
https://hdiconfigactions.BLOB.Core.Windows.NET/linuxhueconfigactionv02/Install-hue-uber-v02.sh
    
Cette section fournit des instructions sur la façon d’utiliser le script lors de la configuration du cluster à l’aide du portail Azure. 

> [AZURE.NOTE] PowerShell Azure, l’infrastructure du langage commun Azure, le Kit de développement .NET HDInsight ou modèles Azure le Gestionnaire de ressources peuvent également être utilisés pour appliquer des actions de script. Vous pouvez également appliquer des actions de script à clusters en cours d’exécution. Pour plus d’informations, voir [Personnaliser HDInsight clusters avec les Actions de Script](hdinsight-hadoop-customize-cluster-linux.md).

1. Démarrer un cluster de mise en service en suivant les étapes de [mise en service HDInsight clusters sur Linux](hdinsight-hadoop-provision-linux-clusters.md#portal), mais ne se terminent pas mise en service.

    > [AZURE.NOTE] Pour installer teinte sur clusters HDInsight, la taille headnode recommandée est au moins A4 (8 cœurs, 14 Go de mémoire).

2. Sur la carte de **Configuration facultatives** , sélectionnez les **Actions de Script**et fournissez les informations comme indiqué ci-dessous :

    ![Paramètres d’action script fournir pour teinte] (./media/hdinsight-hadoop-hue-linux/hue_script_action.png "Paramètres d’action script fournir pour teinte")

    * __Nom__: entrez un nom convivial pour l’action de script.
    * __SCRIPT URI__: https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh
    * __Tête__: Activez cette option
    * __Employé CONSULTANT__: laissez ce champ vide.
    * __SOIGNEUR__: laissez ce champ vide.
    * __Paramètres__: laissez ce champ vide.

3. En bas des **Actions de Script**, utilisez le bouton **Sélectionner** pour enregistrer la configuration. Enfin, utilisez le bouton **Sélectionner** en bas de la cuillère **Configuration facultatives** pour enregistrer les informations de configuration facultatif.

4. Continuez à mise en service le cluster comme décrit dans [mise en service HDInsight clusters sur Linux](hdinsight-hadoop-provision-linux-clusters.md#portal).

## <a name="use-hue-with-hdinsight-clusters"></a>Utiliser teinte avec clusters HDInsight

Tunneling SSH est le seul moyen d’accéder au teinte sur le cluster une fois qu’elle s’exécute. Tunnel viaSSH autorise le trafic accéder directement à la headnode du cluster où teinte est en cours d’exécution. Une fois que le cluster a terminé la mise en service, procédez comme suit pour utiliser teinte sur un cluster HDInsight Linux.

1. Utiliser les informations dans [Utiliser SSH tunnel pour accéder à l’interface utilisateur web Ambari, ResourceManager, JobHistory, NameNode, Oozie et autres web l’interface utilisateur](hdinsight-linux-ambari-ssh-tunnel.md) pour créer un tunnel SSH depuis votre système de client pour le cluster HDInsight, puis configurez votre navigateur Web pour utiliser le tunnel SSH en tant que proxy.

2. Une fois que vous avez créé un tunnel SSH et configuré votre navigateur pour passer le trafic proxy, vous devez trouver le nom d’hôte du nœud principal principal. Vous pouvez le faire en vous connectant au cluster à l’aide de SSH sur le port 22. Par exemple, `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net` où __NomUtilisateur__ représente votre nom d’utilisateur SSH et __CLUSTERNAME__ est le nom de votre cluster.

    Pour plus d’informations sur l’utilisation de SSH, voir les documents suivants :

    * [Utiliser le protocole SSH avec basé sur Linux HDInsight à partir d’un client Linux, Unix ou Mac OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
    * [Utiliser le protocole SSH avec basé sur Linux HDInsight à partir d’un client Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

3. Une fois connecté, utilisez la commande suivante pour obtenir le nom de domaine complet de la headnode principal :

        hostname -f

    Ceci renverra un nom semblable à ce qui suit :

        hn0-myhdi-nfebtpfdv1nubcidphpap2eq2b.ex.internal.cloudapp.net
    
    Il s’agit du nom d’hôte de la headnode primaire où se trouve le site Web teinte.

2. Utiliser le navigateur pour ouvrir le portail teinte à http://HOSTNAME:8888. Remplacez le nom d’hôte avec le nom que vous avez obtenu à l’étape précédente.

    > [AZURE.NOTE] Lorsque vous connectez pour la première fois, vous devrez créer un compte pour vous connecter au portail teinte. Les informations d’identification que vous spécifiez ici sont limités au portail et ne sont pas liées à l’administrateur ou les informations d’identification de l’utilisateur SSH vous avez spécifié lors de la mise en service le cluster.

    ![Connexion au portail teinte] (./media/hdinsight-hadoop-hue-linux/HDI.Hue.Portal.Login.png "Spécifier les informations d’identification portail teinte")

### <a name="run-a-hive-query"></a>Exécuter une requête Hive

1. À partir du portail teinte, cliquez sur **Éditeurs de requête**, puis cliquez sur **la ruche** pour ouvrir l’éditeur Hive.

    ![Utiliser Hive] (./media/hdinsight-hadoop-hue-linux/HDI.Hue.Portal.Hive.png "Utiliser Hive")

2. Sous l’onglet **assister** , sous **base de données**, vous devriez voir **hivesampletable**. Il s’agit d’un exemple de tableau qui est fourni avec tous les clusters Hadoop sur HDInsight. Entrez un exemple de requête dans le volet droit et voir le résultat sous l’onglet **résultats** dans le volet inférieur, comme le montre la capture d’écran.

    ![Exécuter la ruche de requête] (./media/hdinsight-hadoop-hue-linux/HDI.Hue.Portal.Hive.Query.png "Exécuter la ruche de requête")

    Vous pouvez également utiliser l’onglet **graphique** pour afficher une représentation visuelle du résultat.

### <a name="browse-the-cluster-storage"></a>Parcourir le stockage de cluster

1. À partir du portail teinte, cliquez sur **Explorateur de fichiers** dans le coin supérieur droit de la barre de menus.

2. Par défaut, l’Explorateur de fichiers s’ouvre dans le répertoire **/user/myuser** . Cliquez sur la barre oblique juste avant le répertoire de l’utilisateur dans le chemin d’accès pour accéder à la racine du conteneur stockage Azure associé au cluster.

    ![Explorateur de fichiers d’utilisation] (./media/hdinsight-hadoop-hue-linux/HDI.Hue.Portal.File.Browser.png "Explorateur de fichiers d’utilisation")

3. Un clic droit sur un fichier ou dossier pour afficher les opérations disponibles. Utilisez le bouton **Télécharger** dans le coin droit pour télécharger des fichiers dans le répertoire actuel. Utilisez le bouton **Nouveau** pour créer des fichiers ou des répertoires.

> [AZURE.NOTE] L’Explorateur de fichiers teinte peut uniquement afficher le contenu du conteneur par défaut associé au cluster HDInsight. Les comptes/conteneurs de stockage supplémentaire peut avoir associé au cluster ne sera pas accessibles à l’aide de l’Explorateur de fichiers. Toutefois, les autres conteneurs associés au cluster sera toujours accessibles pour les travaux Hive. Par exemple, si vous entrez la commande `dfs -ls wasbs://newcontainer@mystore.blob.core.windows.net` dans l’éditeur Hive, vous pouvez voir le contenu d’autres conteneurs également. Dans cette commande, **newcontainer** n’est pas le conteneur par défaut associé à un cluster.

## <a name="important-considerations"></a>Considérations importantes

1. Le script utilisé pour installer teinte installe uniquement sur le headnode principal du cluster.

2. Pendant l’installation, plusieurs services Hadoop (HDFS, fils, RM2, Oozie) sont redémarrés pour mettre à jour de la configuration. Une fois le script terminé l’installation teinte, il peut prendre du temps pour d’autres services Hadoop à démarrer. Cela peut affecter les performances de teinte initiale. Une fois que tous les services démarrent, teinte est entièrement fonctionnelle.

3.  Teinte ne comprend pas de travaux Tez, qui représente la valeur par défaut en cours pour Hive. Si vous souhaitez utiliser MapReduce comme le moteur d’exécution Hive, mettre à jour le script afin d’utiliser la commande suivante dans votre script :

        set hive.execution.engine=mr;

4.  Avec les clusters Linux, vous pouvez avoir un scénario où vos services exécutent sur la headnode primaire tandis que le Gestionnaire de ressources peut s’exécuter sur le moniteur secondaire. Tel est le cas peut entraîner des erreurs (comme illustrées ci-après) lorsque vous utilisez teinte pour afficher les détails des tâches en cours d’exécution sur le cluster. Toutefois, vous pouvez afficher les détails d’une tâche lorsque la tâche est terminée.

    ![Erreur portail teinte] (./media/hdinsight-hadoop-hue-linux/HDI.Hue.Portal.Error.png "Erreur portail teinte")

    Il s’agit en raison d’un problème connu. Pour résoudre ce problème, modifiez Ambari afin que le Gestionnaire de ressources active s’exécute également sur le headnode principal.

5.  Teinte comprend WebHDFS HDInsight clusters utiliser à l’aide de stockage Azure `wasbs://`. Par conséquent, le script personnalisé est utilisé avec l’action de script installe WebWasb, qui est un service compatible avec WebHDFS pour communiquer avec WASB. Par conséquent, même si le portail teinte indique HADOOP dans des emplacements (par exemple, lorsque vous déplacez votre souris sur le **Navigateur de fichier**), il doit être interprétée comme WASB.


## <a name="next-steps"></a>Étapes suivantes

- [Installer Giraph sur les clusters HDInsight](hdinsight-hadoop-giraph-install-linux.md). Personnalisation de cluster permet d’installer Giraph sur HDInsight Hadoop clusters. Giraph vous permet d’effectuer le traitement de graphique à l’aide de Hadoop, et il peut être utilisé avec Azure HDInsight.

- [Installer mode série sur LAN.r sur clusters HDInsight](hdinsight-hadoop-solr-install-linux.md). Personnalisation de cluster permet d’installer mode série sur LAN.r sur HDInsight Hadoop clusters. Mode série sur LAN.r vous permet d’effectuer des opérations de recherche puissante sur les données stockées.

- [Installer R sur clusters HDInsight](hdinsight-hadoop-r-scripts-linux.md). Personnalisation de cluster permet d’installer R sur HDInsight Hadoop clusters. R est un environnement pour calculer des statistiques et langue open source. Il propose des centaines de fonctions statistiques intégrées et sa propre langage de programmation qui combine les aspects de la programmation orientée objet et fonctionnelle. Il fournit également des fonctions graphiques étendues.

[powershell-install-configure]: install-configure-powershell-linux.md
[hdinsight-provision]: hdinsight-provision-clusters-linux.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
