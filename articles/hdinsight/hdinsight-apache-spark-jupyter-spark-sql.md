<properties
    pageTitle="Créer un cluster explosion sur HDInsight Linux et utiliser SQL explosion de Jupyter pour une analyse interactive | Microsoft Azure"
    description="Des instructions détaillées sur la façon de créer rapidement une explosion Apache cluster dans HDInsight, puis utilisent explosion SQL de blocs-notes Jupyter pour exécuter des requêtes interactifs."
    services="hdinsight"
    documentationCenter=""
    authors="nitinme"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/28/2016"
    ms.author="nitinme"/>


# <a name="get-started-create-apache-spark-cluster-on-hdinsight-linux-and-run-interactive-queries-using-spark-sql"></a>Prise en main : créez Apache explosion cluster sur HDInsight Linux et exécuter des requêtes interactives à l’aide d’explosion SQL

Découvrez comment créer un cluster Apache explosion dans HDInsight, puis utilisez [Jupyter](https://jupyter.org) bloc-notes pour exécuter des requêtes interactives explosion SQL sur le cluster explosion.

   ![Commencer à utiliser Apache explosion dans HDInsight] (./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.getstartedflow.png  "Commencer à utiliser Apache explosion dans le didacticiel HDInsight. Étapes illustrés : créer un compte de stockage ; créer un cluster ; exécuter des instructions SQL explosion")

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="prerequisites"></a>Conditions préalables

- **Azure un abonnement**. Avant de commencer ce didacticiel, vous devez posséder un abonnement Azure. Voir [Azure obtenir la version d’évaluation gratuite](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Client A Secure Shell (SSH)**: Linux, Unix et OS X systèmes conservées un SSH client par le biais du `ssh` commande. Pour les systèmes Windows, nous vous recommandons de [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).
    
- **Touches SSH (Secure Shell) (facultatifs)**: vous pouvez sécuriser le compte SSH utilisé pour la connexion au cluster à l’aide d’un mot de passe ou une clé publique. À l’aide d’un mot de passe permet de vous familiariser rapidement, et vous devez utiliser cette option si vous souhaitez rapidement créer un cluster et exécuter certaines tâches de test. En utilisant une clé est plus sécurisé, mais il requiert le programme d’installation supplémentaire. Vous souhaiterez peut-être utiliser cette approche lorsque vous créez un cluster de production. Dans cet article, nous utilisons l’approche de mot de passe. Pour obtenir des instructions sur la façon de créer et utiliser des clés SSH avec HDInsight, consultez les articles suivants :

    -  À partir d’un ordinateur Linux - [Utiliser SSH avec basé sur Linux HDInsight (Hadoop) de Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md).
    
    -  À partir d’un ordinateur Windows - [Utiliser SSH avec basé sur Linux HDInsight (Hadoop) à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md).

>[AZURE.NOTE] Cet article utilise un modèle de gestionnaire de ressources Azure pour créer un cluster explosion qui utilise [Des objets BLOB de stockage Azure comme le stockage de cluster](hdinsight-hadoop-use-blob-storage.md). Vous pouvez également créer un cluster explosion qui utilise le [Magasin de Lake données Azure](../data-lake-store/data-lake-store-overview.md) comme un espace de stockage supplémentaire, en plus des objets BLOB de stockage Azure comme le stockage par défaut. Pour plus d’informations, voir [créer un cluster HDInsight avec données Lake Store](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

### <a name="access-control-requirements"></a>Exigences de contrôle d’accès

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-spark-cluster"></a>Création d’explosion cluster

Dans cette section, vous créez un cluster de version 3.4 HDInsight (explosion version 1.6.1) à l’aide d’un modèle de gestionnaire de ressources Azure. Pour plus d’informations sur les versions de HDInsight et de niveau de service souscrit, voir [le contrôle de version composant HDInsight](hdinsight-component-versioning.md). Pour les autres méthodes de création cluster, consultez [créer HDInsight clusters](hdinsight-hadoop-provision-linux-clusters.md).

1. Cliquez sur l’image suivante pour ouvrir le modèle dans le portail Azure.         

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-spark-cluster-in-hdinsight.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>
    
    Le modèle se trouve dans un conteneur blob publique, *https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-spark-cluster-in-hdinsight.json*. 
   
2. À partir de la carte de paramètres, entrez les informations suivantes :

    - **Nomducluster**: entrez un nom pour le cluster Hadoop que vous allez créer.
    - **Mot de passe et le nom de connexion cluster**: le nom de connexion par défaut est administrateur.
    - **SSH nom d’utilisateur et mot de passe**.
    
    Veuillez noter ces valeurs.  Vous en aurez besoin plus loin dans le didacticiel.

    > [AZURE.NOTE] SSH est utilisée pour accéder à distance cluster HDInsight en utilisant une ligne de commande. Le nom d’utilisateur et mot de passe que vous utilisez ici est utilisé lors de la connexion au cluster via SSH. En outre, le nom d’utilisateur SSH doit être unique, qu’il crée un compte d’utilisateur sur tous les nœuds de cluster HDInsight. Les éléments suivants sont les noms des comptes réservés pour une utilisation par les services sur le cluster et ne peuvent pas être utilisés en tant que le nom d’utilisateur SSH :
    >
    > racine, hdiuser, vague de, hbase, ubuntu, soigneur, hdfs, fils, mapred, hbase, hive, oozie, falcon, sqoop, administrateur, tez, hcat, hdinsight soigneur.

    > Pour plus d’informations sur l’utilisation de SSH avec HDInsight, voir un des articles suivants :

    > * [Utiliser le protocole SSH avec basé sur Linux Hadoop sur HDInsight de Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
    > * [Utiliser le protocole SSH avec basé sur Linux Hadoop sur HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

    
3. Cliquez sur **OK** pour enregistrer les paramètres.

4.la à partir de la carte de **déploiement personnalisé** , cliquez sur zone de liste déroulante **groupe de ressources** , puis cliquez sur **Nouveau** pour créer un nouveau groupe de ressources. Le groupe de ressources est un conteneur qui regroupe cluster, le compte de stockage dépendantes et autres ressources liées.

5 Cliquez sur **conditions juridiques**, puis cliquez sur **créer**.

6. Cliquez sur **créer**. Vous verrez une vignette intitulée déploiement d’envoi pour le déploiement du modèle. Il est nécessaire à environ 20 minutes pour créer le cluster et la base de données SQL.



## <a name="run-spark-sql-queries-using-a-jupyter-notebook"></a>Exécuter des requêtes SQL explosion à l’aide d’un bloc-notes Jupyter

Dans cette section, vous utilisez Jupyter portable pour exécuter des requêtes SQL explosion sur le cluster explosion. HDInsight Spark clusters offrent deux noyaux que vous pouvez utiliser avec le bloc-notes Jupyter. Il s’agit des :

* **PySpark** (pour les applications écrites dans Python)
* **Explosion** (pour les applications écrites dans Scala)

Dans cet article, vous allez utiliser le noyau PySpark. Dans l’article [noyaux disponibles sur les ordinateurs portables Jupyter avec clusters explosion HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md#why-should-i-use-the-new-kernels) , vous pouvez lire sur les avantages de l’utilisation du noyau PySpark en détail. Toutefois, deux ou trois principaux avantages de l’utilisation du noyau PySpark sont :

* Vous n’avez pas besoin définir les contextes explosion et Hive. Ils sont définis automatiquement pour vous.
* Vous pouvez utiliser magics de cellule, tel que `%%sql`, pour exécuter directement vos requêtes SQL ou Hive, sans les extraits de code précédents.
* Visualisation de la sortie pour les requêtes SQL ou Hive automatiquement.

### <a name="create-jupyter-notebook-with-pyspark-kernel"></a>Créer un bloc-notes Jupyter avec noyau PySpark 

1. À partir du [Portail Azure](https://portal.azure.com/), à partir de la startboard, cliquez sur la vignette pour votre cluster explosion (si vous l’épinglées à la startboard). Vous pouvez également accéder à votre cluster sous **Rechercher tout** > **Clusters HDInsight**.   

2. À partir de la carte de cluster explosion, cliquez sur **Tableau de bord Cluster**, puis cliquez sur **Bloc-notes Jupyter**. Si vous y êtes invité, entrez les informations d’identification d’administration pour le cluster.

    > [AZURE.NOTE] Le bloc-notes Jupyter peut également atteindre pour votre cluster en ouvrant l’URL suivante dans votre navigateur. Remplacez __NOMDUCLUSTER__ par le nom de votre cluster :
    >
    > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Créer un bloc-notes. Cliquez sur **Nouveau**, puis cliquez sur **PySpark**.

    ![Créer un bloc-notes Jupyter] (./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.note.jupyter.createnotebook.png "Créer un bloc-notes Jupyter")

3. Un bloc-notes est créé et ouvert avec le nom Untitled.pynb. Cliquez sur le nom du bloc-notes dans la partie supérieure, puis entrez un nom convivial.

    ![Fournir un nom pour le bloc-notes] (./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.note.jupyter.notebook.name.png "Fournir un nom pour le bloc-notes")

4. Étant donné que vous avez créé un bloc-notes à l’aide du noyau PySpark, vous n’avez pas besoin créer les contextes explicitement. Les contextes explosion et Hive est automatiquement créées pour vous lors de l’exécution de la première cellule de code. Vous pouvez commencer en important les types requis pour ce scénario. Pour ce faire, collez l’extrait de code suivantes dans une cellule et appuyez sur **MAJ + ENTRÉE**.

        from pyspark.sql.types import *
        
    Chaque fois que vous exécutez une tâche dans Jupyter, votre titre de fenêtre de navigateur web affiche un état **(occupé)** ainsi que le titre. Vous verrez également un cercle plein en regard du texte **PySpark** dans le coin supérieur droit. Une fois que la tâche est terminée, il se transforme en un cercle vide.

     ![Statut d’une tâche de bloc-notes Jupyter] (./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.jupyter.job.status.png "Statut d’une tâche de bloc-notes Jupyter")

4. Charger les exemples de données dans une table temporaire. Lorsque vous créez un cluster explosion dans HDInsight, l’exemple de fichier de données, **hvac.csv**, est copié dans le compte de stockage associé sous **\HdiSamples\HdiSamples\SensorSampleData\hvac**.

    Dans une cellule vide, collez le code suivant et appuyez sur **MAJ + ENTRÉE**. Cet exemple de code enregistre les données dans une table temporaire appelée **hvac**.

        # Load the data
        hvacText = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
        
        # Create the schema
        hvacSchema = StructType([StructField("date", StringType(), False),StructField("time", StringType(), False),StructField("targettemp", IntegerType(), False),StructField("actualtemp", IntegerType(), False),StructField("buildingID", StringType(), False)])
        
        # Parse the data in hvacText
        hvac = hvacText.map(lambda s: s.split(",")).filter(lambda s: s[0] != "Date").map(lambda s:(str(s[0]), str(s[1]), int(s[2]), int(s[3]), str(s[6]) ))
        
        # Create a data frame
        hvacdf = sqlContext.createDataFrame(hvac,hvacSchema)
        
        # Register the data fram as a table to run queries against
        hvacdf.registerTempTable("hvac")

5. Étant donné que vous utilisez un noyau PySpark, vous pouvez maintenant directement exécuter une requête SQL dans la table temporaire **hvac** que vous venez de créer à l’aide de la `%%sql` magie. Pour plus d’informations sur la `%%sql` magie, ainsi que d’autres magics disponibles avec le noyau PySpark, voir [noyaux disponibles sur les ordinateurs portables Jupyter avec clusters explosion HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md#why-should-i-use-the-new-kernels).
        
        %%sql
        SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"

5. Une fois que la tâche est terminée avec succès, le résultat sous forme de tableau suivant est affiché par défaut.

    ![Tableau résultant du résultat de la requête] (./media/hdinsight-apache-spark-jupyter-spark-sql/tabular.output.png "Tableau résultant du résultat de la requête")

    Vous pouvez également afficher les résultats dans ainsi que d’autres visualisations. Par exemple, un graphique en aires pour le même résultat ressemblera à ce qui suit.

    ![Graphique en aires du résultat de la requête] (./media/hdinsight-apache-spark-jupyter-spark-sql/area.output.png "Graphique en aires du résultat de la requête")


6. Une fois que vous avez terminé d’exécuter l’application, vous devez arrêter le bloc-notes pour libérer les ressources. Pour ce faire, dans le menu **fichier** sur le bloc-notes, cliquez sur **Fermer et arrêter**. Cette s’arrêter et fermer le bloc-notes.

##<a name="delete-the-cluster"></a>Supprimer le cluster

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


## <a name="see-also"></a>Voir aussi


* [Vue d’ensemble : Apache explosion sur Azure HDInsight](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Scénarios

* [Explosion avec BI : effectuer une analyse de données interactives à l’aide d’explosion dans HDInsight avec les outils de décisionnel](hdinsight-apache-spark-use-bi-tools.md)

* [Explosion avec apprentissage automatique : utilisation explosion dans HDInsight pour analyser la température de construction à l’aide de données HVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Explosion avec apprentissage automatique : utilisation explosion dans HDInsight pour prévoir des résultats de l’inspection alimentaires](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Diffusion en continu explosion : Utilisation explosion dans HDInsight pour la création d’applications en continu en temps réel](hdinsight-apache-spark-eventhub-streaming.md)

* [Analyse de journal de site Web à l’aide d’explosion dans HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

* [Analyse des données télémétrie un aperçu application à l’aide d’explosion dans HDInsight](hdinsight-spark-analyze-application-insight-logs.md)

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


[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage-create-storage-account.md
