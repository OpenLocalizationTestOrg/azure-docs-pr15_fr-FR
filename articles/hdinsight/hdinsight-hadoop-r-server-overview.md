<properties
    pageTitle="Qu’est-R sur HDInsight ? Introduction au serveur R sur HDInsight (preview) | Microsoft Azure"
    description="Qu’est R Server sur HDInsight (preview) et l’utilisation de serveur R pour créer des applications pour l’analyse de données volumineux."
    services="hdinsight"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="08/17/2016"
   ms.author="jeffstok"/>


# <a name="overview-of-r-server-on-hdinsight-preview"></a>Vue d’ensemble du serveur R sur HDInsight \(preview\)

Avec Microsoft Azure HDInsight Premium, Microsoft R Server est désormais disponible en tant qu’option lorsque vous créez HDInsight clusters dans Azure. Cette nouvelle fonctionnalité fournit des données scientifiques, les statistiques et les développeurs R ayant accès à la demande au format SVG, distributed méthodes d’analytique sur HDInsight.

Clusters peuvent être redimensionnées aux projets et tâches à portée de main et supprimés dès qu’ils ne sont plus nécessaires. Dans la mesure où ils appartiennent Azure HDInsight, ces groupes fournis avec prise en charge de niveau de l’entreprise 24/7, accord de 99,9 % et la possibilité d’intégrer avec d’autres composants dans le réseau Azure.

>[AZURE.NOTE] Serveur R est disponible uniquement avec HDInsight Premium. Pour l’instant, HDInsight Premium est disponible uniquement pour les clusters Hadoop et explosion. Par conséquent, actuellement vous pouvez utiliser R serveur uniquement avec des clusters Hadoop et explosion sur HDInsight. Pour plus d’informations, voir [Quels sont les différents niveaux de service et les composants Hadoop disponibles avec HDInsight ?](hdinsight-component-versioning.md).

Serveur R sur HDInsight fournit les fonctionnalités plus récentes pour analytique basée sur R de grands volumes de données qui sont chargés de stockage d’objets Blob Azure. Dans la mesure où Server R est basé sur Ouvrir la source R, les applications R que vous créez peuvent tirer parti des packages 8000 + libres R, ainsi que les procédures de DETARTREUR, package analytique données volumineuses de Microsoft inclus avec serveur R.

Le nœud de bord de clusters Premium fournit un endroit pratique pour vous connecter au cluster et exécuter vos scripts R. Avec un nœud de bord, vous avez la possibilité de l’exécution de fonctions distribuées parallélisées de DETARTREUR sur les cœurs du serveur edge nœud. Vous avez également la possibilité pour les exécuter sur les nœuds du cluster à l’aide Hadoop carte réduire de DETARTREUR ou explosion calculer contextes.

Les modèles ou les prévisions résultant des analyses peut être téléchargé pour utilisent en local. Ils peuvent également être operationalized ailleurs dans Azure, tels que via un [Azure Machine apprentissage Studio](http://studio.azureml.net) [service web](../machine-learning/machine-learning-publish-a-machine-learning-web-service.md).

## <a name="get-started-with-r-on-hdinsight"></a>Prise en main R sur HDInsight

Pour inclure R serveur dans un cluster de HDInsight, vous devez créer un Hadoop ou explosion cluster avec l’option Premium lorsque vous créez un cluster à l’aide du portail Azure. Les deux types de cluster utilisent la même configuration, qui inclut les R Server sur les nœuds de données d’un cluster et un nœud bord comme une zone de destination pour les règles serveur R analytique. Pour une vue d’ensemble détaillée sur la création d’un cluster, consultez [Prise en main serveur R sur HDInsight](hdinsight-hadoop-r-server-get-started.md) .

## <a name="learn-about-data-storage-options"></a>En savoir plus sur les options de stockage de données

Stockage par défaut pour les clusters HDInsight est stockage d’objets Blob avec le système de fichiers HADOOP mappé vers un conteneur blob. Cela permet de garantir que toutes les données sont téléchargées dans le stockage de cluster ou écrites au stockage de cluster au cours de l’analyse, est rendu permanente. Vous pouvez utiliser l’utilitaire [AzCopy](../storage/storage-use-azcopy.md) pour copier les données vers et à partir du blob.

Outre l’utilisation du stockage Blob, vous avez la possibilité de l’utilisation de [Stockage Lake des données Azure](https://azure.microsoft.com/services/data-lake-store/) avec votre cluster. Si vous utilisez Lake de données, vous pouvez utiliser stockage Blob et Lake de données pour le stockage HADOOP.

Vous pouvez également utiliser des [Fichiers Azure](../storage/storage-how-to-use-files-linux.md) comme option de stockage pour une utilisation sur le nœud de bord. Fichiers Azure vous permet de monter un partage de fichiers qui a été créé dans le stockage Azure au système de fichiers Linux. Pour plus d’informations sur les options de stockage de données de serveur R sur cluster HDInsight, voir [options de stockage pour le serveur R sur clusters HDInsight](hdinsight-hadoop-r-server-storage.md).

## <a name="access-r-server-on-the-cluster"></a>Serveur R d’accès sur le cluster

Une fois que vous avez créé un cluster avec serveur R, vous pouvez vous connecter à la console R sur le nœud de bord du cluster via SSH/PuTTY. Vous pouvez également vous connecter via un navigateur si vous choisissez d’installer l’IDE Server RStudio facultatif sur le nœud de bord. Pour plus d’informations sur l’installation de RStudio Server, voir [Installation RStudio Server sur clusters HDInsight](hdinsight-hadoop-r-server-install-r-studio.md).   

## <a name="develop-and-run-r-scripts"></a>Développer et exécuter des scripts R

Créer et exécuter les scripts de R aucun des modules 8000 + R libres outre les procédures de parallélisée et distribués des permet dans la bibliothèque ScaleR. En règle générale, qui est exécuté dans R Server sur le nœud de bord s’exécute dans le relais R sur ce nœud. L’exception est ces étapes nécessitant un DETARTREUR fonctionnent avec un cluster contexte défini Hadoop carte réduire (RxHadoopMR) ou d’explosion (RxSpark).

Dans ce cas, la fonction s’exécute en mode distribué sur ces données nœuds (tâche) du cluster qui sont associées à des données référencées. Pour plus d’informations sur les options de contexte cluster différentes, voir [options de contexte pour le serveur R sur HDInsight Premium de calcul](hdinsight-hadoop-r-server-compute-contexts.md).

## <a name="operationalize-a-model"></a>Mettre un modèle

Lorsque votre modélisation de données est terminée, vous pouvez effectuent le modèle afin de réaliser des prévisions pour de nouvelles données à la fois dans Azure et en local. Ce processus est connu sous le score. Voici quelques exemples.

### <a name="score-in-hdinsight"></a>Résultat de HDInsight

Pour le score dans HDInsight, écrivez une fonction R qui appelle votre modèle pour réaliser des prévisions pour un nouveau fichier de données que vous avez chargé à votre compte de stockage. Enregistrer puis les prévisions dans le compte de stockage. Vous pouvez exécuter la routine à la demande sur le nœud de bord de votre cluster ou à l’aide d’une tâche planifiée.  

### <a name="score-in-azure-machine-learning"></a>Score dans l’apprentissage automatique Azure

Pour le score à l’aide d’un service web d’apprentissage automatique Azure, utilisez le package de Azure Machine apprentissage R libres appelé [AzureML](https://cran.r-project.org/web/packages/AzureML/vignettes/getting_started.html) pour publier votre modèle comme un service web Azure. Pour faciliter la tâche, ce package est préinstallé sur le nœud de bord. Ensuite, utilisez les fonctionnalités dans l’apprentissage automatique pour créer une interface utilisateur pour le service web, puis appelez le service web selon vos besoins pour le score.

Si vous choisissez cette option, vous devez convertir les objets de modèle ScaleR aux objets de modèle open source équivalent à utiliser avec le service web. Pour ce faire à l’aide de fonctions de contrainte ScaleR, tel que `as.randomForest()` pour les modèles ensemble.


### <a name="score-on-premises"></a>Score en local

Pour le score local après la création de votre modèle, vous pouvez sérialiser le modèle dans R, téléchargez-le, pu le vérifier et puis utilisez-le pour notation de nouvelles données. Vous pouvez score de nouvelles données à l’aide de l’approche décrite plus haut dans [score dans HDInsight](#scoring-in-hdinsight) ou à l’aide de [DeployR](https://deployr.revolutionanalytics.com/).

## <a name="maintain-the-cluster"></a>Mettre à jour le cluster

### <a name="install-and-maintain-r-packages"></a>Installation et la maintenance des packages R

La plupart des packages R que vous utilisez doivent sur le nœud de bord étant donné que la plupart de vos scripts R s’exécutera il. Pour installer des packages R supplémentaires sur le nœud de bord, vous pouvez utiliser les commandes `install.packages()` méthode dans R.

Dans la plupart des cas, vous n’avez pas besoin d’installer des packages R supplémentaires sur les nœuds de données si vous utilisez simplement routines de la bibliothèque ScaleR au sein du cluster. Toutefois, vous devrez peut-être packages supplémentaires pour permettre l’utilisation de **rxExec** ou de l’exécution de **RxDataStep** sur les nœuds de données.

Dans ce cas, les packages supplémentaires doivent être spécifiés par l’utilisation d’une action de script après avoir créé le cluster. Pour plus d’informations, voir [Création d’un cluster HDInsight avec serveur R](hdinsight-hadoop-r-server-get-started.md).   

### <a name="change-hadoop-map-reduce-memory-settings"></a>Modifier les paramètres de mémoire Hadoop carte réduire

Un cluster peut être modifié pour modifier la quantité de mémoire disponible au serveur R lorsqu’elle s’exécute un travail carte réduire. Pour modifier un cluster, utilisez l’interface utilisateur Ambari Apache disponibles via la carte portail Azure pour votre cluster. Pour savoir comment accéder à la Ambari UI pour votre cluster, voir [clusters HDInsight gérer à l’aide de l’interface utilisateur Web Ambari](hdinsight-hadoop-manage-ambari.md).

Il est également possible de modifier la quantité de mémoire disponible au serveur R en utilisant les commutateurs Hadoop dans l’appel à **RxHadoopMR** comme suit :

    hadoopSwitches = "-libjars /etc/hadoop/conf -Dmapred.job.map.memory.mb=6656"  

### <a name="scale-your-cluster"></a>Mettre à l’échelle votre cluster

Un cluster existant peut être mise à l’échelle vers le haut ou vers le bas via le portail. Mise à l’échelle, vous pouvez améliorer la capacité supplémentaire que vous devrez peut-être pour les tâches de traitement importantes ou vous pouvez mettre à l’échelle revenir un cluster lorsqu’il est inactif. Pour obtenir des instructions sur la façon de mettre à l’échelle un cluster, consultez [Gérer HDInsight clusters](hdinsight-administer-use-portal-linux.md).

### <a name="maintain-the-system"></a>Mettre à jour le système

Opération de maintenance sur les ordinateurs virtuels Linux sous-jacentes dans un cluster HDInsight au cours des heures de fermeture pour appliquer d’autres mises à jour et correctifs de système d’exploitation. En règle générale, maintenance effectuée à 3 h 30 (basé sur l’heure locale de la machine virtuelle) chaque lundi et le jeudi. Mises à jour sont effectuées de façon à ce qu’ils n’ont une incidence sur plus d’un quart du cluster à la fois.  

Dans la mesure où les nœuds têtes sont redondantes et pas tous les nœuds de données sont affectés, toutes les tâches en cours d’exécution peuvent ralentir. Ils doivent toujours exécuter toutefois jusqu'à la fin. Les logiciels personnalisés ou les données locales que vous avez préservées dans ces événements de maintenance, sauf si une panne se produit qui doit être recréée cluster.

## <a name="learn-about-ide-options-for-r-server-on-an-hdinsight-cluster"></a>Découvrez les options IDE pour R Server sur un cluster HDInsight

Le nœud de bord Linux d’un cluster HDInsight Premium est la zone destination pour analyse basée sur R. Après vous être connecté au cluster, vous pouvez lancer l’interface console au serveur R en tapant **R** à l’invite Linux. Utilisation de l’interface console a été améliorée si vous exécutez un éditeur de texte pour le développement de script R dans une autre fenêtre et coupez et collez des sections de votre script dans la console R selon vos besoins.

Un outil plus sophistiqué pour le développement de votre script R est l’IDE basée sur R pour une utilisation sur le bureau, tels que Microsoft a récemment annoncé [R Tools pour Visual Studio](https://www.visualstudio.com/en-us/features/rtvs-vs.aspx) (RTV). Il s’agit d’une gamme d’outils de bureau et de serveur de [RStudio](https://www.rstudio.com/products/rstudio-server/). Vous pouvez également utiliser basés sur Eclipse de Walware [StatET](http://www.walware.de/goto/statet).

Une autre option consiste à installer un IDE sur le nœud de bord Linux lui-même.  Un choix populaires est [RStudio Server](https://www.rstudio.com/products/rstudio-server/), qui fournit une IDE via un navigateur à utiliser par les clients à distance. Installez RStudio Server sur le nœud de bord d’un cluster HDInsight Premium offre une expérience IDE complet pour le développement et l’exécution de scripts R avec serveur R sur le cluster. Il peut être beaucoup plus productif celui de la console R.  Si vous souhaitez utiliser RStudio Server, voir [Installation RStudio Server sur clusters HDInsight](hdinsight-hadoop-r-server-install-r-studio.md).

## <a name="learn-about-pricing"></a>En savoir plus sur les tarifs

Les frais qui sont associées à un cluster HDInsight Premium avec R serveur sont structurés de la même façon pour les taxes pour les groupes HDInsight standards. Ils sont basés sur le dimensionnement des ordinateurs virtuels sous-jacent sur le nom, les données et les nœuds de bord, avec l’ajout d’une majoration core heures pour Premium. Pour plus d’informations sur HDInsight Premium tarifs, y compris les tarifs lors de la version d’évaluation et la disponibilité d’une version d’évaluation gratuite de 30 jours, voir [HDInsight tarifs](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="next-steps"></a>Étapes suivantes

Suivez les liens ci-dessous pour en savoir plus sur l’utilisation de serveur R avec clusters HDInsight.

- [Prise en main serveur R sur HDInsight](hdinsight-hadoop-r-server-get-started.md)

- [Ajouter RStudio serveur à HDInsight Premium](hdinsight-hadoop-r-server-install-r-studio.md)

- [Calculer les options de contexte pour R Server sur HDInsight (preview)](hdinsight-hadoop-r-server-compute-contexts.md)

- [Azure options de stockage pour serveur R HDInsight Premium](hdinsight-hadoop-r-server-storage.md)
