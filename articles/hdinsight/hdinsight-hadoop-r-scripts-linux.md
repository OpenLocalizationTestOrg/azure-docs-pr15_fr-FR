<properties
    pageTitle="Installer R sur basé sur Linux HDInsight | Microsoft Azure"
    description="Découvrez comment installer et R permet de personnaliser clusters Hadoop basé sur Linux."
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/20/2016"
    ms.author="larryfr"/>

# <a name="install-and-use-r-on-hdinsight-hadoop-clusters"></a>Installer et utiliser R sur HDInsight Hadoop clusters

Vous pouvez installer R sur n’importe quel type de cluster dans Hadoop sur HDInsight en utilisant la personnalisation de cluster **Action de Script** . Cela permet scientifiques de données et aux analystes de R permet de déployer la structure de programmation MapReduce/fils puissante traiter les grandes quantités de données sur les clusters Hadoop déployées dans HDInsight.

> [AZURE.IMPORTANT] La [couche premium](https://azure.microsoft.com/pricing/details/hdinsight/) offrant pour HDInsight inclut serveur R dans le cadre de votre cluster HDInsight. Cela permet aux scripts R permet d’exécuter des calculs distribués MapReduce et explosion. Pour plus d’informations, voir [prise en main à l’aide de serveur R sur HDInsight](hdinsight-hadoop-r-server-get-started.md). 


## <a name="what-is-r"></a>Qu’est R ?

Le <a href="http://www.r-project.org/" target="_blank">Projet R pour le calcul statistique</a> constitue un environnement pour calculer des statistiques et le langage source ouvert. R fournit des centaines de fonctions statistiques intégré et sa propre langage de programmation qui combine les aspects de la programmation orientée objet et fonctionnelle. Il fournit également des fonctions graphiques étendues. R est l’environnement de programmation préféré pour les plus professionnel statistiques et scientifiques dans un large éventail de champs.

R scripts peuvent être exécutés sur les groupes de Hadoop dans un HDInsight qui ont été personnalisées à l’aide de Script Action lors de la création d’installer l’environnement R. R est compatible avec Azure Blob Storage (WASB) afin que les données stockées à cet emplacement peuvent être traitées avec R sur HDInsight.

## <a name="what-the-script-does"></a>Que signifie le script

L’action de script utilisée pour installer R sur votre cluster HDInsight installe les packages Ubuntu suivants, qui fournit une installation R base :

* [r-base](http://packages.ubuntu.com/precise/r-base): package de Base GNU R
* [r-base-développement](http://packages.ubuntu.com/precise/r-base-dev): packages auxiliaire GNU R

Les packages RHadoop suivants sont également installés, qui fournissent l’intégration avec MapReduce et HADOOP :

* [rmr2](https://github.com/RevolutionAnalytics/rmr2): permet aux développeurs R utiliser Hadoop MapReduce
* [rhdfs](https://github.com/RevolutionAnalytics/rhdfs): permet aux développeurs R utiliser Hadoop HDFS (WASB pour HDInsight)

En outre, les modules R suivants sont installés :

| Package R | Il fournit |
| --------- | ---------------- |
| [rJava](https://cran.r-project.org/web/packages/rJava/index.html) | Un faible niveau R interface Java. |
| [RCPP](https://cran.r-project.org/web/packages/Rcpp/index.html) | Intégration R et C++. |
| [RJSONIO](https://cran.r-project.org/web/packages/RJSONIO/index.html) | Objets sérialiser/désérialiser R JSON |
| [bitops](https://cran.r-project.org/web/packages/bitops/index.html) | Fonctions pour les opérations de bits sur vecteurs entier. |
| [Résumé](https://cran.r-project.org/web/packages/digest/index.html) | Créer des résumés hachage de chiffrement d’objets R. |
| [fonctionnel](https://cran.r-project.org/web/packages/functional/index.html) | Curry, composition et autres fonctions d’ordre supérieur |
| [reshape2](https://cran.r-project.org/web/packages/reshape2/index.html) | Souplesse restructurer et agréger des données. |
| [stringr](https://cran.r-project.org/web/packages/stringr/index.html) | Wrappers simples et cohérents pour les opérations de chaînes courantes. |
| [plyr](https://cran.r-project.org/web/packages/plyr/index.html) | Outils de fractionnement, l’application et combiner des données. |
| [caTools](https://cran.r-project.org/web/packages/caTools/index.html) | Outils de déplacement des statistiques de la fenêtre, GIF, en base 64, AUC ROC, etc.. |
| [stringdist](https://cran.r-project.org/web/packages/stringdist/index.html) | Approximative correspondance des chaînes et Distance fonctions de chaîne. |

## <a name="install-r-using-script-actions"></a>Installer R à l’aide des Actions de Script

L’action de script suivante est utilisée pour installer R sur un cluster HDInsight. https://hdiconfigactions.BLOB.Core.Windows.NET/linuxrconfigactionv01/r-installer-v01.sh
    
Cette section fournit des instructions sur la façon d’utiliser le script lors de la création d’un nouveau cluster à l’aide du portail Azure. 

> [AZURE.NOTE] PowerShell Azure, l’infrastructure du langage commun Azure, le Kit de développement .NET HDInsight ou modèles Azure le Gestionnaire de ressources peuvent également être utilisés pour appliquer des actions de script. Vous pouvez également appliquer des actions de script à clusters en cours d’exécution. Pour plus d’informations, voir [Personnaliser HDInsight clusters avec les Actions de Script](hdinsight-hadoop-customize-cluster-linux.md).

1. Démarrer un cluster de mise en service à l’aide de la procédure dans [clusters HDInsight basé sur Linux de mise en service](hdinsight-hadoop-provision-linux-clusters.md#portal), mais ne se terminent pas mise en service.

2. Dans la carte de **Configuration facultatives** , sélectionnez les **Actions de Script**et fournir les informations ci-dessous :

    * __Nom__: entrez un nom convivial pour l’action de script.
    * __SCRIPT URI__: https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh
    * __Tête__: Activez cette option
    * __Employé CONSULTANT__: Activez cette option
    * __SOIGNEUR__: Activez cette option pour installer sur le nœud soigneur.
    * __Paramètres__: laissez ce champ vide

3. En bas des **Actions de Script**, utilisez le bouton **Sélectionner** pour enregistrer la configuration. Enfin, utilisez le bouton **Sélectionner** en bas de la cuillère **Configuration facultatives** pour enregistrer les informations de configuration facultatif.

4. Continuez à mise en service le cluster comme décrit dans les [clusters HDInsight basé sur Linux de mise en service](hdinsight-hadoop-provision-linux-clusters.md#portal).

## <a name="run-r-scripts"></a>Exécuter des scripts R

Une fois que le cluster a terminé la mise en service, procédez comme suit pour utiliser R pour effectuer une opération MapReduce sur le cluster.

1. Connectez-vous au cluster HDInsight à l’aide de SSH :

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    Pour plus d’informations sur l’utilisation de SSH avec HDInsight, voir les rubriques suivantes :

    * [Utiliser le protocole SSH avec basé sur Linux Hadoop sur HDInsight de Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

    * [Utiliser le protocole SSH avec basé sur Linux Hadoop sur HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

2. À partir de la `username@hn0-CLUSTERNAME:~$` invite, entrez la commande suivante pour démarrer une session R interactive :

        R

3. Entrez le programme R suivant. Cela génère les numéros de 1 à 100 et puis les multiplie par 2.

        library(rmr2)
        ints = to.dfs(1:100)
        calc = mapreduce(input = ints, map = function(k, v) cbind(v, 2*v))

    La première ligne appelle la rmr2 bibliothèque RHadoop, qui est utilisé pour les opérations MapReduce.

    La deuxième ligne génère des valeurs 1-100, puis les stocke sur le système de fichier Hadoop à l’aide `to.dfs`.

    La troisième ligne crée un processus MapReduce à l’aide de fonctionnalités fournies par rmr2 et commence le traitement. Vous devriez voir plusieurs lignes parcouru que le traitement commence.

4. Ensuite, utilisez ce qui suit pour afficher le chemin d’accès temporaire qui la sortie MapReduce ont été stockée à :

        print(calc())

    Il s’agit d’un écran semblable à `/tmp/file5f615d870ad2`. Pour afficher le résultat réel, procédez comme suit :

        print(from.dfs(calc))

    La sortie doit ressembler à ceci :

        [1,]  1 2
        [2,]  2 4
        .
        .
        .
        [98,]  98 196
        [99,]  99 198
        [100,] 100 200

5. Pour quitter R, entrez les informations suivantes :

        q()


## <a name="next-steps"></a>Étapes suivantes

- [Installer et utiliser les clusters teinte sur HDInsight](hdinsight-hadoop-hue-linux.md). La nuance est une interface utilisateur qui vous permettent de créer, exécuter et enregistrer des travaux cochon et Hive, ainsi que de parcourir le stockage par défaut pour votre HDInsight cluster web.

- [Installer Giraph sur les clusters HDInsight](hdinsight-hadoop-giraph-install.md). Personnalisation de cluster permet d’installer Giraph sur HDInsight Hadoop clusters. Giraph vous permet d’effectuer le traitement de graphique à l’aide de Hadoop, et il peut être utilisé avec Azure HDInsight.

- [Installer mode série sur LAN.r sur clusters HDInsight](hdinsight-hadoop-solr-install.md). Personnalisation de cluster permet d’installer mode série sur LAN.r sur HDInsight Hadoop clusters. Mode série sur LAN.r vous permet d’effectuer des opérations de recherche puissante sur les données stockées.

- [Installer teinte sur clusters HDInsight](hdinsight-hadoop-hue-linux.md). Personnalisation de cluster permet d’installer teinte sur HDInsight Hadoop clusters. Teinte est un ensemble d’applications Web utilisé pour interagir avec un cluster Hadoop.

[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
