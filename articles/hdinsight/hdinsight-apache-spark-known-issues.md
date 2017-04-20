<properties 
    pageTitle="Problèmes d’explosion Apache dans HDInsight connus | Microsoft Azure" 
    description="Problèmes connus d’explosion Apache dans HDInsight." 
    services="hdinsight" 
    documentationCenter="" 
    authors="mumian" 
    manager="jhubbard" 
    editor="cgronlun"
    tags="azure-portal"/>

<tags 
    ms.service="hdinsight" 
    ms.workload="big-data" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/25/2016" 
    ms.author="nitinme"/>

# <a name="known-issues-for-apache-spark-cluster-on-hdinsight-linux"></a>Problèmes connus pour cluster explosion Apache sur HDInsight Linux

Ce document effectue le suivi de tous les problèmes connus pour la version d’évaluation HDInsight Spark.  

##<a name="livy-leaks-interactive-session"></a>Livy perd de la session interactive
 
Lors du redémarrage Livy avec une session interactive (à partir de Ambari ou en raison de redémarrer l’ordinateur virtuel headnode 0) toujours active, perdrez une session interactive des tâches. Pour cette raison, nouvelles tâches peuvent bloqué dans l’état accepté et ne peut pas être démarrés.

**Atténuation :**

Procédez comme suit pour contourner ce problème :

1. SSH dans headnode. 
2. Exécutez la commande suivante pour rechercher l’ID d’application des tâches interactifs démarrés par le biais Livy. 

        yarn application –list

    La tâche par défaut noms seront Livy si les tâches ont été démarrés avec une session interactive Livy sans nom explicite spécifié, pour le Livy session démarrée en Jupyter bloc-notes, le nom du travail commence par remotesparkmagics_ *. 

3. Exécutez la commande suivante pour mettre fin à ces tâches. 

        yarn application –kill <Application ID>

Nouvelles tâches commence à s’exécuter. 

##<a name="spark-history-server-not-started"></a>Serveur de l’historique d’explosion non démarré 

Explosion historique Server n’est pas démarré automatiquement après la création d’un cluster.  

**Atténuation :** 

Démarrer manuellement le serveur de l’historique à partir de Ambari.

## <a name="permission-issue-in-spark-log-directory"></a>Problème d’autorisation dans le répertoire du journal explosion 

Quand hdiuser soumet une tâche avec spark-submit, il existe une java.io.FileNotFoundException d’erreur : /var/log/spark/sparkdriver_hdiuser.log (autorisation refusée) et le journal de pilote n’est pas écrit. 

**Atténuation :**
 
1. Ajouter hdiuser au groupe Hadoop. 
2. Accorder des 777 autorisations sur /var/log/spark après la création d’un cluster. 
3. Mettre à jour l’emplacement du journal explosion à l’aide de Ambari être un répertoire avec des 777 autorisations.  
4. Exécuter explosion-envoyer en tant que sudo.  

## <a name="issues-related-to-jupyter-notebooks"></a>Problèmes liés aux blocs-notes Jupyter

Voici quelques-uns des problèmes connus liés à des blocs-notes Jupyter.


### <a name="notebooks-with-non-ascii-characters-in-filenames"></a>Blocs-notes comportant des caractères non ASCII dans les noms de fichiers

Blocs-notes Jupyter pouvant être utilisées dans les clusters explosion HDInsight ne doivent pas avoir de caractères ASCII dans les noms de fichiers. Si vous essayez de télécharger un fichier via le UI Jupyter qui contient un nom de fichier non ASCII, il échoue silencieusement (autrement dit, Jupyter ne vous permettent de télécharger le fichier, mais elle ne soit lever une erreur visible). 

### <a name="error-while-loading-notebooks-of-larger-sizes"></a>Erreur lors du chargement de blocs-notes de tailles supérieures

Une erreur peut s’afficher **`Error loading notebook`** lorsque vous chargez blocs-notes qui sont plus grandes taille.  

**Atténuation :**

Si vous obtenez cette erreur, il ne signifie pas que vos données sont endommagé ou perdu.  Vos blocs-notes sont toujours sur le disque dans `/var/lib/jupyter`, et vous pouvez SSH dans le cluster pour y accéder. Vous pouvez copier vos blocs-notes à partir de votre cluster sur votre ordinateur local (à l’aide de SCP ou WinSCP) comme une sauvegarde pour éviter la perte de toutes les données importantes dans le bloc-notes. Vous pouvez ensuite SSH tunnel dans votre headnode port 8001 d’accéder aux Jupyter sans passer la passerelle.  À partir de là, vous pouvez effacer la sortie de votre bloc-notes et enregistrer de nouveau pour réduire la taille du bloc-notes.

Pour éviter cette erreur reproduise à l’avenir, vous devez suivre quelques recommandations :

* Il est important de conserver une petite taille bloc-notes. Aucune sortie à partir de vos projets explosion est renvoyé à Jupyter est conservé dans le bloc-notes.  Il est recommandé de représenter avec Jupyter en général pour éviter de `.collect()` sur RDD grande ou dataframes ; en revanche, si vous permettant de lire le contenu d’un RDD, vous pouvez en cours d’exécution `.take()` ou `.sample()` afin que votre sortie hypothèse n’est pas trop long.
* En outre, lorsque vous enregistrez un bloc-notes, effacer tous les de sortie des cellules pour réduire la taille.

### <a name="notebook-initial-startup-takes-longer-than-expected"></a>Démarrage initial bloc-notes prend plus longtemps que prévu 

Première instruction code Jupyter bloc-notes à l’aide de magie explosion peut prendre plus d’une minute.  

**Explication :**
 
Cela se produit car lorsque la première cellule de code est exécutée. En arrière-plan Cela lance la configuration de la session et explosion, SQL et contextes Hive sont configurées. Une fois que les contextes sont définis, la première instruction est exécutée et Ceci donne l’impression que l’instruction a eu beaucoup de temps.

### <a name="jupyter-notebook-timeout-in-creating-the-session"></a>Délai de bloc-notes Jupyter lors de la création de la session

Lorsque cluster explosion est parti de ressources, les noyaux explosion et Pyspark dans le bloc-notes Jupyter n’expire tentez de créer la session. 

**Minimisation :** 

1. Libérez des ressources dans votre cluster explosion par :

    - Arrêt d’autres blocs-notes explosion en accédant au menu fermer et arrêter ou en cliquant sur Arrêter dans l’Explorateur de bloc-notes.
    - L’arrêt d’autres applications explosion à partir de fils.

2. Redémarrez le bloc-notes que vous avez essayé de démarrage. Suffisamment de ressources doit être disponibles pour vous permet de créer une session maintenant.

##<a name="see-also"></a>Voir aussi

* [Vue d’ensemble : Apache explosion sur Azure HDInsight](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Scénarios

* [Explosion avec BI : effectuer une analyse de données interactives à l’aide d’explosion dans HDInsight avec les outils de décisionnel](hdinsight-apache-spark-use-bi-tools.md)

* [Explosion avec apprentissage automatique : utilisation explosion dans HDInsight pour analyser la température de construction à l’aide de données HVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Explosion avec apprentissage automatique : utilisation explosion dans HDInsight pour prévoir des résultats de l’inspection alimentaires](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Diffusion en continu explosion : Utilisation explosion dans HDInsight pour la création d’applications en continu en temps réel](hdinsight-apache-spark-eventhub-streaming.md)

* [Analyse de journal de site Web à l’aide d’explosion dans HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Créer et exécuter des applications

* [Créer une application autonome à l’aide de Scala](hdinsight-apache-spark-create-standalone-application.md)

* [Exécution de tâches à distance sur un cluster explosion à l’aide de Livy](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Outils et extensions

* [Plug-in des outils HDInsight IntelliJ idée permet de créer et soumettre des applications Scala d’explosion](hdinsight-apache-spark-intellij-tool-plugin.md)

* [Plug-in utilisation HDInsight outils idée IntelliJ déboguer applications explosion à distance](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)

* [Utiliser les blocs-notes Zeppelin avec un cluster explosion sur HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [Noyaux disponibles pour Jupyter bloc-notes cluster explosion pour HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)

* [Utiliser les packages externes avec Jupyter blocs-notes](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

* [Installer Jupyter sur votre ordinateur et vous connecter à un cluster HDInsight Spark](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gérer les ressources

* [Gestion des ressources pour le cluster Apache explosion dans Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

* [Suivre et débogage travaux s’exécutant sur un cluster Apache explosion dans HDInsight](hdinsight-apache-spark-job-debugging.md)
