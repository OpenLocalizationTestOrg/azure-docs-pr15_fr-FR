<properties 
    pageTitle="Installer Jupyter bloc-notes sur votre ordinateur et vous connecter à un cluster HDInsight Spark | Microsoft Azure" 
    description="Découvrez comment installer Jupyter bloc-notes localement sur votre ordinateur et vous connecter à un cluster explosion Apache sur Azure HDInsight." 
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
    ms.date="09/26/2016" 
    ms.author="nitinme"/>


# <a name="install-jupyter-notebook-on-your-computer-and-connect-to-apache-spark-cluster-on-hdinsight-linux"></a>Installez Jupyter bloc-notes sur votre ordinateur, connectez-vous à cluster explosion Apache sur HDInsight Linux

Dans cet article, vous allez apprendre à installer Jupyter bloc-notes, avec les noyaux explosion (pour Scala) magie explosion PySpark personnalisé (pour les Python) et se connecter le bloc-notes à un cluster HDInsight. Il peut y avoir plusieurs raisons à installer Jupyter sur votre ordinateur local, et il peut y avoir ainsi que certains problèmes. Pour une liste des raisons et défis, consultez la section [Pourquoi dois-je installer Jupyter sur mon ordinateur](#why-should-i-install-jupyter-on-my-computer) à la fin de cet article.

Il existe trois principales étapes d’installation de Jupyter et la magie explosion sur votre ordinateur.

* Installer Jupyter bloc-notes
* Installer les noyaux PySpark et explosion avec la magie d’explosion
* Configurer magie explosion pour accéder à explosion cluster sur HDInsight

Pour plus d’informations sur les noyaux personnalisés et la magie explosion disponible pour les blocs-notes Jupyter avec cluster HDInsight, voir [noyaux disponibles pour les blocs-notes Jupyter avec Apache explosion Linux clusters sur HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md).

##<a name="prerequisites"></a>Conditions préalables

Les conditions préalables répertoriés ici ne sont pas pour l’installation de Jupyter. Il s’agit de connexion le bloc-notes Jupyter à un cluster HDInsight une fois le bloc-notes est installé.

- Un abonnement Azure. Voir [Azure obtenir la version d’évaluation gratuite](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Un cluster explosion Apache sur HDInsight Linux. Pour plus d’informations, voir [groupes de créer Apache explosion dans Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).

## <a name="install-jupyter-notebook-on-your-computer"></a>Installer Jupyter bloc-notes sur votre ordinateur

Vous devez installer Python avant d’installer Jupyter blocs-notes. Python et Jupyter sont disponibles dans le cadre de la [distribution Ananconda](https://www.continuum.io/downloads). Lorsque vous installez Anaconda, vous installez pas réellement une distribution de Python. Une fois Anaconda est installé, vous ajoutez l’installation Jupyter en exécutant une commande. Cette section fournit les instructions que vous devez suivre.

1. Téléchargez [programme d’installation Anaconda](https://www.continuum.io/downloads) pour votre plateforme et exécutez le programme d’installation. Pendant l’exécution de l’Assistant de configuration, vérifiez que vous sélectionnez l’option d’ajout Anaconda à la variable du chemin d’accès.

2. Exécutez la commande suivante pour installer Jupyter.

        conda install jupyter

    Pour plus d’informations sur les éléments Jupyter, voir [Jupyter l’installation à l’aide de Anaconda](http://jupyter.readthedocs.io/en/latest/install.html).

## <a name="install-the-kernels-and-spark-magic"></a>Installer les noyaux et magie explosion

Pour obtenir des instructions sur la façon d’installer la magie explosion, les noyaux PySpark et explosion, consultez la [documentation de sparkmagic](https://github.com/jupyter-incubator/sparkmagic#installation) sur GitHub.

## <a name="configure-spark-magic-to-access-the-hdinsight-spark-cluster"></a>Configurer magie explosion pour accéder au cluster HDInsight Spark

Dans cette section, vous configurez la magie explosion que vous avez installé précédemment pour vous connecter à un cluster Apache explosion que vous devez déjà avoir créé dans Azure HDInsight.

1. Les informations de configuration Jupyter se trouve généralement dans le répertoire de base des utilisateurs. Pour localiser votre répertoire de base sur n’importe quelle plateforme de système d’exploitation, tapez les commandes suivantes.

    Démarrer l’interface de Python. Dans une fenêtre de commande, tapez les informations suivantes :

        python

    Dans l’interface de Python, entrez la commande suivante pour déterminer le répertoire de base.

        import os
        print(os.path.expanduser('~'))

2. Accédez au répertoire de base et créez un dossier nommé **.sparkmagic** s’il n’existe pas déjà.

3. Dans le dossier, créez un fichier appelé **config.json** et ajoutez l’extrait de JSON suivant qu’elle contient.

        {
          "kernel_python_credentials" : {
            "username": "{USERNAME}",
            "base64_password": "{BASE64ENCODEDPASSWORD}",
            "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
          },
          "kernel_scala_credentials" : {
            "username": "{USERNAME}",
            "base64_password": "{BASE64ENCODEDPASSWORD}",
            "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
          }
        }

4. Remplacez **{USERNAME}**, **{CLUSTERDNSNAME}**et **{BASE64ENCODEDPASSWORD}** avec les valeurs appropriées. Vous pouvez utiliser un certain nombre d’utilitaires dans votre langage de programmation favori ou en ligne pour générer un mot de passe en base 64 codé pour votre mot de passe actualy. Un extrait de code Python simple pour exécuter à partir de votre invite de commandes serait le suivant :

        python -c "import base64; print(base64.b64encode('{YOURPASSWORD}'))"

5. Démarrez Jupyter. Utilisez la commande suivante à partir de l’invite de commandes.

        jupyter notebook

6. Vérifiez que vous pouvez vous connecter au cluster en utilisant le bloc-notes Jupyter et que vous pouvez utiliser la magie explosion disponible dont la. Procédez comme suit.

    1. Créer un bloc-notes. Dans l’angle droit, cliquez sur **Nouveau**. Vous devriez voir le noyau par défaut **Python2** et les deux nouveaux noyaux que vous installez, **PySpark** et **explosion**.

        ![Créer un bloc-notes Jupyter] (./media/hdinsight-apache-spark-jupyter-notebook-install-locally/jupyter-kernels.png "Créer un bloc-notes Jupyter")

    
        Cliquez sur **PySpark**.


    2. Exécuter l’extrait de code suivantes.

            %%sql
            SELECT * FROM hivesampletable LIMIT 5

        Si vous pouvez récupérer correctement la sortie, votre connexion au cluster HDInsight est testée.

    >[AZURE.TIP] Si vous voulez mettre à jour de la configuration du bloc-notes pour vous connecter à un cluster différent, mettre à jour la config.json avec la nouvelle série de valeurs, comme indiqué à l’étape 3 ci-dessus. 

## <a name="why-should-i-install-jupyter-on-my-computer"></a>Pourquoi dois-je installer Jupyter sur mon ordinateur ?

Il peut être un nombre de raisons pourquoi vous souhaiterez peut-être installer Jupyter sur votre ordinateur et connectez-la à un cluster explosion sur HDInsight.

* Même si Jupyter blocs-notes sont déjà disponibles sur le cluster explosion dans Azure HDInsight, l’installation Jupyter sur votre ordinateur fournit vous permettent de créer vos blocs-notes localement, testez votre application par rapport à un cluster en cours d’exécution et puis téléchargez les blocs-notes dans le cluster. Pour télécharger les blocs-notes vers le cluster, vous pouvez les télécharger en utilisant le bloc-notes Jupyter exécutant ou le cluster, ou les enregistrer dans le dossier /HdiNotebooks dans le compte de stockage associé au cluster. Pour plus d’informations sur la façon dont les blocs-notes sont stockés sur le cluster, voir [où sont stockés les blocs-notes Jupyter](hdinsight-apache-spark-jupyter-notebook-kernels.md#where-are-the-notebooks-stored)?
* Avec les blocs-notes disponibles localement, vous pouvez vous connecter à différents clusters explosion selon vos besoins d’application.
* Vous pouvez utiliser GitHub pour avoir le contrôle de version pour les blocs-notes et mise en œuvre d’un système de contrôle de code source. Vous pouvez également avoir un environnement collaboratif où plusieurs utilisateurs peuvent travailler avec le même bloc-notes.
* Vous pouvez utiliser les blocs-notes localement sans avoir un cluster vers le haut. Il vous suffit d’un cluster pour tester vos blocs-notes contre, ne pas de gérer manuellement vos blocs-notes ou un environnement de développement.
* Il peut être plus facile à configurer votre propre environnement de développement local plutôt que configurer l’installation Jupyter sur le cluster.  Vous pouvez tirer parti de tous les logiciels que vous avez installé localement sans configurer un ou plusieurs clusters à distance.

>[AZURE.WARNING] Avec Jupyter installé sur votre ordinateur local, plusieurs utilisateurs peuvent exécuter le même bloc-notes sur le même cluster explosion en même temps. Dans ce cas, plusieurs Livy sessions sont créées. Si vous rencontrez un problème et que vous souhaitez déboguer qui, il sera une tâche complexe pour effectuer le suivi de session Livy appartient à l’utilisateur.




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

* [Noyaux disponibles pour Jupyter bloc-notes cluster explosion pour HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)

* [Utiliser les packages externes avec Jupyter blocs-notes](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

### <a name="manage-resources"></a>Gérer les ressources

* [Gestion des ressources pour le cluster Apache explosion dans Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

* [Suivre et débogage travaux s’exécutant sur un cluster Apache explosion dans HDInsight](hdinsight-apache-spark-job-debugging.md)
