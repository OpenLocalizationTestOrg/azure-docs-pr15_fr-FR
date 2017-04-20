<properties 
    pageTitle="Disponible avec des blocs-notes Jupyter sur HDInsight Spark noyaux clusters sur Linux | Microsoft Azure" 
    description="En savoir plus sur les noyaux de bloc-notes Jupyter supplémentaires avec cluster explosion sur HDInsight Linux." 
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
    ms.topic="article" 
    ms.date="10/05/2016" 
    ms.author="nitinme"/>


# <a name="kernels-available-for-jupyter-notebooks-with-apache-spark-clusters-on-hdinsight-linux"></a>Noyaux disponibles pour les blocs-notes Jupyter avec clusters explosion Apache sur HDInsight Linux

Cluster explosion Apache sur HDInsight (Linux) inclut les blocs-notes Jupyter que vous pouvez utiliser pour tester vos applications. Un noyau est un programme qui s’exécute et interprète votre code. HDInsight Spark clusters offrent deux noyaux que vous pouvez utiliser avec le bloc-notes Jupyter. Il s’agit des :

1. **PySpark** (pour les applications écrites dans Python)
2. **Explosion** (pour les applications écrites dans Scala)

Dans cet article, vous allez découvrir comment utiliser ces noyaux et quels sont les avantages que pouvez-vous à utiliser ces derniers.

**Conditions requises :**

Vous devez disposer des éléments suivants :

- Un abonnement Azure. Voir [Azure obtenir la version d’évaluation gratuite](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Un cluster explosion Apache sur HDInsight Linux. Pour plus d’informations, voir [groupes de créer Apache explosion dans Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).

## <a name="how-do-i-use-the-kernels"></a>Comment utiliser les noyaux ? 

1. À partir du [Portail Azure](https://portal.azure.com/), à partir de la startboard, cliquez sur la vignette pour votre cluster explosion (si vous l’épinglées à la startboard). Vous pouvez également accéder à votre cluster sous **Rechercher tout** > **Clusters HDInsight**.   

2. À partir de la carte de cluster explosion, cliquez sur **Tableau de bord Cluster**, puis cliquez sur **Bloc-notes Jupyter**. Si vous y êtes invité, entrez les informations d’identification d’administration pour le cluster.

    > [AZURE.NOTE] Le bloc-notes Jupyter peut également atteindre pour votre cluster en ouvrant l’URL suivante dans votre navigateur. Remplacez __NOMDUCLUSTER__ par le nom de votre cluster :
    >
    > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Créer un bloc-notes dont le nouveau. Cliquez sur **Nouveau**, puis cliquez sur **Pyspark** ou **explosion**. Vous devez utiliser noyau explosion pour les applications Scala et noyau PySpark pour les applications Python.

    ![Créer un bloc-notes Jupyter] (./media/hdinsight-apache-spark-jupyter-notebook-kernels/jupyter-kernels.png "Créer un bloc-notes Jupyter") 

3. Cela doit ouvrir un bloc-notes avec le noyau que vous avez sélectionné.

## <a name="why-should-i-use-the-pyspark-or-spark-kernels"></a>Pourquoi dois-je utiliser les noyaux PySpark ou explosion ?

Voici quelques avantages de l’utilisation des nouvelle noyaux.

1. **Prédéfinis contextes**. Dans le **PySpark** ou noyaux **explosion** fournis avec Jupyter blocs-notes, vous n’avez pas besoin de définir les contextes explosion ou Hive explicitement avant que vous puissiez travailler avec l’application que vous développez ; ils sont disponibles pour vous par défaut. Les contextes sont :

    * **sc** - pour contexte d’explosion
    * **sqlContext** - Hive contexte


    Par conséquent, vous n’êtes pas obligé exécuter des instructions comme suit pour définir les contextes :

        ###################################################
        # YOU DO NOT NEED TO RUN THIS WITH THE NEW KERNELS
        ###################################################
        sc = SparkContext('yarn-client')
        sqlContext = HiveContext(sc)

    À la place, vous pouvez utiliser directement les contextes prédéfinis dans votre application.
    
2. **Magics de cellule**. Le noyau PySpark fournit certaines prédéfinis « magics », qui sont des commandes spéciales que vous pouvez appeler avec `%%` (par exemple, `%%MAGIC` <args>). La commande magie doit être le premier mot dans une cellule de code et autoriser plusieurs lignes de contenu. Le mot magic doit être le premier mot dans la cellule. Ajout rien avant la magie, même commentaires, entraîne une erreur.   Pour plus d’informations sur magics, voir [ici](http://ipython.readthedocs.org/en/stable/interactive/magics.html).

    Le tableau ci-dessous répertorie les différentes magics disponibles via les noyaux.

  	| Magie     | Exemple                         | Description  |
  	|-----------|---------------------------------|--------------|
  	| Aide      | `%%help`                            | Génère une table de tous les magics disponibles avec exemple et la description |
  	| informations      | `%%info`                          | Informations sur la session sorties du point de terminaison Livy en cours |
  	| configurer | `%%configure -f`<br>`{"executorMemory": "1000M"`,<br>`"executorCores": 4`} | Configure les paramètres de création d’une session. L’indicateur force (-f) est obligatoire si une session a déjà été créée et la session est supprimée et recréée. Observez [billet /sessions de Livy corps demander](https://github.com/cloudera/livy#request-body) pour une liste des paramètres valides. Paramètres doivent être passés dans sous forme de chaîne JSON et doivent se trouver sur la ligne suivante après la magie, comme illustré dans l’exemple, la colonne. |
  	| SQL       |  `%%sql -o <variable name>`<br> `SHOW TABLES`    | Exécute une requête Hive par rapport à la sqlContext. Si la `-o` paramètre est passé, le résultat de la requête est conservé dans le %% contexte Python local comme un dataframe [Pandas](http://pandas.pydata.org/) .   |
  	| local     |     `%%local`<br>`a=1`              | Tout le code dans les lignes suivantes sera exécuté localement. Code doit être code Python valide. |
  	| journaux      | `%%logs`                        | Les journaux de la session active Livy des sorties.  |
  	| supprimer    | `%%delete -f -s <session number>` | Supprime une session spécifique du point de terminaison Livy en cours. Notez que vous ne pouvez pas supprimer la session est lancée pour le noyau lui-même. |
  	| nettoyage de disque   | `%%cleanup -f`                    | Supprime toutes les sessions du point de terminaison de Livy en cours, y compris la session de ce bloc-notes. L’indicateur force -f est obligatoire.  |

    >[AZURE.NOTE] Outre magics ajoutés par le noyau PySpark, vous pouvez également utiliser la [magics IPython intégrés](https://ipython.org/ipython-doc/3/interactive/magics.html#cell-magics), y compris `%%sh`. Vous pouvez utiliser la `%%sh` magie à exécuter des scripts et bloc de code dans la headnode cluster. 

3. **Visualisation automatique**. Le noyau **Pyspark** visualise automatiquement la sortie de requêtes Hive et SQL. Vous avez la possibilité de choisir entre différents types de visualisations, y compris la Table, en secteurs, en courbes, zone, barre.

## <a name="parameters-supported-with-the-sql-magic"></a>Paramètres pris en charge avec la %% magie sql

La %% magie sql prend en charge les différents paramètres que vous pouvez utiliser pour contrôler le type de sortie que vous recevez lorsque vous exécutez des requêtes. Le tableau suivant répertorie la sortie.

| Paramètre     | Exemple                         | Description  |
|-----------|---------------------------------|--------------|
| + o      | `-o <VARIABLE NAME>`                          | Utilisez ce paramètre pour conserver le résultat de la requête, dans le %% contexte Python local, comme un dataframe [Pandas](http://pandas.pydata.org/) . Le nom de la variable dataframe est le nom de la variable que vous spécifiez. |
| q :      | `-q`                          | Utilisez cette option pour désactiver les visualisations de la cellule. Si vous ne voulez pas automatique-visualiser le contenu d’une cellule et voulez simplement définissez-le comme un dataframe, puis utilisez `-q -o <VARIABLE>`. Si vous souhaitez désactiver visualisations sans capturer les résultats (par exemple, pour exécuter une requête SQL avec effets secondaires, comme une `CREATE TABLE` instruction), il suffit d’utiliser `-q` sans spécifier un `-o` argument. |
| -m       |  `-m <METHOD>`    | **Méthode** étant **prendre** ou **échantillon** (valeur par défaut est **prendre**). Si la méthode est **prendre**, le noyau choisit des éléments à partir de la partie supérieure de l’ensemble de données de résultat spécifié par le nombre maximal de lignes (décrites plus loin dans cette table). Si la méthode est **exemple**, le noyau sera aléatoirement exemples d’éléments du jeu de données en fonction de `-r` paramètre, décrite ci-après dans ce tableau.   |
| -r     |     `-r <FRACTION>`            | **FRACTION** Voici un nombre à virgule flottante entre 0,0 et 1,0. Si la méthode d’exemple pour la requête SQL est `sample`, puis le noyau exemples aléatoirement la fraction spécifiée des éléments du jeu de résultats pour vous ; par exemple, si vous exécutez une requête SQL avec les arguments `-m sample -r 0.01`, puis échantillonnage aléatoirement 1 % des lignes de résultat. |
| -n      | `-n <MAXROWS>`                        | **Nombre maximal de lignes** est une valeur entière. Le noyau limite le nombre de lignes de sortie à **nombre maximal de lignes**. Si le **nombre maximal de lignes** est un nombre négatif tel que **-1**, le nombre de lignes dans le jeu de résultats ne sera pas limité. |

**Exemple :**

    %%sql -q -m sample -r 0.1 -n 500 -o query2 
    SELECT * FROM hivesampletable

L’instruction ci-dessus effectue les opérations suivantes :

* Sélectionne tous les enregistrements à partir de **hivesampletable**.
* Étant donné que nous utilisons - q, il désactive les automatique visualisation.
* Étant donné que nous utilisons `-m sample -r 0.1 -n 500` il exemples de 10 % des lignes dans la hivesampletable et les limites de la taille de l’ensemble de résultats à 500 lignes de manière aléatoire.
* Enfin, étant donné que nous avons utilisé `-o query2` enregistre la sortie dans un dataframe appelé **query2**.
    

## <a name="considerations-while-using-the-new-kernels"></a>Considérations lors de l’utilisation des nouvelle noyaux

Quelle que soit noyau que vous utilisez (PySpark ou explosion), en laissant les blocs-notes en cours d’exécution utilisera les ressources de votre cluster.  Dont ces, étant donné que les contextes soient prédéfinis, quitté le programme simplement les blocs-notes ne pas supprimer le contexte et, par conséquent, les ressources du cluster continuent à être en cours d’utilisation. Une bonne pratique dont la PySpark et explosion serait d’utiliser l’option **Fermer et arrêter** à partir du menu **fichier** du bloc-notes. Cela supprime le contexte et puis a quitté le bloc-notes.    


## <a name="show-me-some-examples"></a>Me montrer quelques exemples

Lorsque vous ouvrez un bloc-notes Jupyter, vous verrez deux dossiers disponibles au niveau de la racine.

* Le dossier **PySpark** contient des exemples d’agendas qui utilisent le nouveau noyau **Python** .
* Le dossier **Scala** contient des exemples d’agendas qui utilisent le nouveau noyau **explosion** .

Vous pouvez ouvrir le bloc-notes **00 - [lire ME première] explosion magie noyau fonctionnalités** à partir du dossier **PySpark** ou **explosion** pour en savoir plus sur les différentes magics disponibles. Vous pouvez également utiliser les autres blocs-notes exemple disponibles sous les deux dossiers pour apprendre à atteindre différents scénarios à l’aide de blocs-notes Jupyter avec HDInsight Spark clusters.

## <a name="where-are-the-notebooks-stored"></a>Où se trouvent les blocs-notes ?

Blocs-notes Jupyter sont enregistrées sur le compte de stockage associé au cluster sous le dossier **/HdiNotebooks** .  Blocs-notes, des fichiers texte et des dossiers que vous créez à partir de Jupyter seront accessibles à partir de WASB.  Par exemple, si vous utilisez Jupyter pour créer un dossier **MonDossier** et un bloc-notes **myfolder/mynotebook.ipynb**, vous pouvez accéder au bloc-notes `wasbs:///HdiNotebooks/myfolder/mynotebook.ipynb`.  L’inverse est également vrai, autrement dit, si vous téléchargez un bloc-notes directement à votre compte de stockage sur `/HdiNotebooks/mynotebook1.ipynb`, le bloc-notes sera visible à partir de Jupyter également.  Blocs-notes reste dans le compte de stockage même une fois que le cluster est supprimé.

La façon dont les blocs-notes sont enregistrées dans le compte de stockage est compatible avec HADOOP. Par conséquent, si vous SSH dans le cluster que vous pouvez utiliser des fichiers commandes gestion comme suit :

    hdfs dfs -ls /HdiNotebooks                            # List everything at the root directory – everything in this directory is visible to Jupyter from the home page
    hdfs dfs –copyToLocal /HdiNotebooks                 # Download the contents of the HdiNotebooks folder
    hdfs dfs –copyFromLocal example.ipynb /HdiNotebooks   # Upload a notebook example.ipynb to the root folder so it’s visible from Jupyter


En cas de problèmes d’accès du compte de stockage pour le cluster, les blocs-notes sont également enregistrés sur le headnode `/var/lib/jupyter`.

## <a name="supported-browser"></a>Navigateur pris en charge
Blocs-notes Jupyter en cours d’exécution par rapport à HDInsight Spark clusters sont pris en charge uniquement sur Google Chrome.

## <a name="feedback"></a>Commentaires

Les nouveaux noyaux sont en pleine évolution étape et arrivent au fil du temps. Cela signifie également qu’API peut être modifiée ces noyaux arrivent. Nous aimerions recevoir vos commentaires que vous avez lors de l’utilisation de ces noyaux. Il s’agit très utile dans la version finale de ces grains de mise en forme. Vous pouvez laisser vos commentaires/commentaires sous la section **commentaires** en bas de cet article.


## <a name="seealso"></a>Voir aussi


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

* [Utiliser les packages externes avec Jupyter blocs-notes](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

* [Installer Jupyter sur votre ordinateur et vous connecter à un cluster HDInsight Spark](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gérer les ressources

* [Gestion des ressources pour le cluster Apache explosion dans Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

* [Suivre et débogage travaux s’exécutant sur un cluster Apache explosion dans HDInsight](hdinsight-apache-spark-job-debugging.md)
