<properties
    pageTitle="Installer et utiliser Giraph sur basé sur Linux HDInsight (Hadoop) | Microsoft Azure"
    description="Découvrez comment installer Giraph sur les clusters basés sur Linux HDInsight à l’aide des Actions de Script. Actions de script permettent de personnaliser le cluster lors de la création, en modifiant la configuration du cluster ou l’installation de services et utilitaires."
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/17/2016"
    ms.author="larryfr"/>

# <a name="install-giraph-on-hdinsight-hadoop-clusters-and-use-giraph-to-process-large-scale-graphs"></a>Installez Giraph sur clusters HDInsight Hadoop et utilisez Giraph pour traiter les graphiques à grande échelle

Vous pouvez installer Giraph sur n’importe quel type de cluster dans Hadoop sur Azure HDInsight à l’aide de **Script Action** pour personnaliser un cluster.

Dans cette rubrique, vous allez apprendre à installer Giraph à l’aide de Script Action. Une fois que vous avez installé Giraph, vous allez également découvrir comment utiliser Giraph pour les applications plus courantes, qui consiste à traiter les graphiques à grande échelle.

> [AZURE.NOTE] Les informations dans cet article sont spécifiques aux clusters HDInsight basé sur Linux. Pour plus d’informations sur l’utilisation des clusters fonctionnant sous Windows, voir [Installer Giraph sur HDInsight Hadoop clusters (Windows)](hdinsight-hadoop-giraph-install.md)

## <a name="whatis"></a>Quelles sont les Giraph ?

[Apache Giraph](http://giraph.apache.org/) , vous pouvez exécuter graph traitement à l’aide Hadoop et peut être utilisé avec Azure HDInsight. Diagrammes de modèle de relations entre les objets, tels que les connexions routeur sur un réseau de grande taille tels qu’Internet ou relations entre les personnes sur des réseaux sociaux (parfois appelées sous forme de graphique social) un autre. Traitement de graphique vous permet de motif sur les relations entre des objets dans un graphique, tels que :

- Identification des amis potentiels en fonction de vos relations actuelles.
- Identifier l’itinéraire le plus court entre deux ordinateurs dans un réseau.
- Calcule le rang de page des pages Web.

> [AZURE.WARNING] Composants fournis avec le cluster HDInsight entièrement prises en charge et Support Microsoft aidera à identifier et résoudre les problèmes liés à ces composants.
>
> Composants personnalisés, tels que Giraph, recevront RCSI prise en charge pour vous aider à résoudre le problème. Cela peut provoquer de résoudre le problème ou vous demandant de s’engager canaux disponibles pour les technologies d’ouvrir la source où se trouve une grande expertise pour que la technologie. Par exemple, de nombreux sites communautaires qui peuvent être utilisés, comme : [forum MSDN pour HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com). Projets Apache contient des sites de projet sur [http://apache.org](http://apache.org), par exemple : [Hadoop](http://hadoop.apache.org/).

##<a name="what-the-script-does"></a>Que signifie le script

Ce script effectue les actions suivantes :

* Installe Giraph à`/usr/hdp/current/giraph`
* Copie la `giraph-examples.jar` fichier de stockage par défaut (WASB) pour votre cluster :`/example/jars/giraph-examples.jar`

## <a name="install"></a>Installer Giraph à l’aide des Actions de Script

Un exemple de script pour installer Giraph sur un cluster HDInsight est disponible à l’emplacement suivant.

    https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh

Cette section fournit des instructions sur l’utilisation de l’exemple de script lors de la création du cluster à l’aide du portail Azure. 

> [AZURE.NOTE] PowerShell Azure, l’infrastructure du langage commun Azure, le Kit de développement .NET HDInsight ou modèles Azure le Gestionnaire de ressources peuvent également être utilisés pour appliquer des actions de script. Vous pouvez également appliquer des actions de script à clusters en cours d’exécution. Pour plus d’informations, voir [Personnaliser HDInsight clusters avec les Actions de Script](hdinsight-hadoop-customize-cluster-linux.md).

1. Commencer la création d’un cluster en suivant les étapes de [clusters HDInsight basé sur Linux créer](hdinsight-hadoop-create-linux-clusters-portal.md), mais ne se terminent pas Création.

2. Dans la carte de **Configuration facultatives** , sélectionnez les **Actions de Script**et fournir les informations ci-dessous :

    * __Nom__: entrez un nom convivial pour l’action de script.
    * __SCRIPT URI__: https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh
    * __Tête__: Activez cette option
    * __Employé CONSULTANT__: laissez décochée
    * __SOIGNEUR__: laissez désactivée
    * __Paramètres__: laissez ce champ vide

3. En bas des **Actions de Script**, utilisez le bouton **Sélectionner** pour enregistrer la configuration. Enfin, utilisez le bouton **Sélectionner** en bas de la cuillère **Configuration facultatives** pour enregistrer les informations de configuration facultatif.

4. Continuer à créer le cluster comme décrit dans les [clusters basés sur Linux créer HDInsight](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="usegiraph"></a>Comment utiliser Giraph dans HDInsight ?

Une fois que le cluster a terminé de créer, procédez comme suit pour exécuter l’exemple SimpleShortestPathsComputation inclus avec Giraph. Pour ce faire implémenté l’implémentation <a href = "http://people.apache.org/~edwardyoon/documents/pregel.pdf">Pregel</a> simple pour trouver le chemin le plus court entre des objets dans un graphique.

1. Connectez-vous au cluster HDInsight à l’aide de SSH :

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    Pour plus d’informations sur l’utilisation de SSH avec HDInsight, voir les rubriques suivantes :

    * [Utiliser le protocole SSH avec basé sur Linux Hadoop sur HDInsight de Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

    * [Utiliser le protocole SSH avec basé sur Linux Hadoop sur HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

1. Utilisez ce qui suit pour créer un nouveau fichier nommé __tiny_graph.txt__:

        nano tiny_graph.txt

    Utilisez ce qui suit le contenu de ce fichier :

        [0,0,[[1,1],[3,3]]]
        [1,0,[[0,1],[2,2],[3,1]]]
        [2,0,[[1,2],[4,4]]]
        [3,0,[[0,3],[1,1],[4,4]]]
        [4,0,[[3,4],[2,4]]]

    Ces données décrivent une relation entre des objets dans un graphique orienté, en utilisant le format [source\_id, source\_valeur, [[dest\_id], [bord\_valeur],...]]. Chaque ligne représente une relation entre un **source\_id** objet et un ou plusieurs **dest\_id** objets. La **bord\_valeur** (ou poids) peut être considéré comme la rupture ou la distance de la connexion entre **source_id** et **dest\_id**.

    Dessiné, et à l’aide de la valeur (ou l’épaisseur) en tant que la distance entre les objets, les données ci-dessus peuvent ressembler à ceci :

    ![tiny_graph.txt dessiné sous forme de cercles avec lignes de variés distance entre](./media/hdinsight-hadoop-giraph-install-linux/giraph-graph.png)

2. Pour enregistrer le fichier, utilisez __Ctrl + X__, puis __Y__et enfin sur __entrée__ pour accepter le nom du fichier.

3. Pour stocker les données en stockage principal pour votre cluster HDInsight, utilisez ce qui suit :

        hdfs dfs -put tiny_graph.txt /example/data/tiny_graph.txt

4. Exécutez l’exemple SimpleShortestPathsComputation à l’aide de la commande suivante.

         yarn jar /usr/hdp/current/giraph/giraph-examples.jar org.apache.giraph.GiraphRunner org.apache.giraph.examples.SimpleShortestPathsComputation -ca mapred.job.tracker=headnodehost:9010 -vif org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat -vip /example/data/tiny_graph.txt -vof org.apache.giraph.io.formats.IdWithValueTextOutputFormat -op /example/output/shortestpaths -w 2

    Les paramètres utilisés avec cette commande sont décrites dans le tableau suivant.

  	| Paramètre | Fonction |
  	| --------- | ------------ |
  	| `jar /usr/hdp/current/giraph/giraph-examples.jar` | Le fichier jar contenant les exemples. |
  	| `org.apache.giraph.GiraphRunner` | Classe utilisée pour démarrer les exemples. |
  	| `org.apache.giraph.examples.SimpleShortestPathsCoputation` | L’exemple sera exécuté. Dans ce cas, il sera calculer le chemin le plus court entre ID 1 et tous les autres codes dans le graphique. |
  	| `-ca mapred.job.tracker=headnodehost:9010` | Headnode pour le cluster. |
  	| `-vif org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFromat` | Le format d’entrée à utiliser pour les données d’entrée. |
  	| `-vip /example/data/tiny_graph.txt` | Le fichier de données d’entrée. |
  	| `-vof org.apache.giraph.io.formats.IdWithValueTextOutputFormat` | Le format de sortie. Dans ce cas, l’ID et la valeur en tant que texte brut. |
  	| `-op /example/output/shortestpaths` | L’emplacement de sortie. |
  	| `-w 2` | Le nombre de collaborateurs à utiliser. Dans ce cas, 2. |

    Pour plus d’informations sur ces et les autres paramètres utilisés avec des exemples de Giraph, consultez le [démarrage rapide Giraph](http://giraph.apache.org/quick_start.html).

5. Une fois que la tâche est terminée, les résultats seront stockés dans le __wasbs : / / / exemple/out/shotestpaths__ répertoire. Les fichiers créés commence par __partie-m -__ et se terminent par un nombre indiquant le premier, deuxième, fichier etc.. Afficher le résultat, utilisez ce qui suit :

        hdfs dfs -text /example/output/shortestpaths/*

    La sortie doit ressembler à ce qui suit :

        0   1.0
        4   5.0
        2   2.0
        1   0.0
        3   1.0

    La SimpleShortestPathComputation exemple est codé en dur commence objet ID 1 et trouver le chemin le plus court vers d’autres objets. Pour que la sortie doit être lu comme `destination_id distance`, où distance correspond à la valeur (ou l’épaisseur) des bords parcourus entre objet ID 1 et l’ID cible.

    Cette visualisation, vous pouvez vérifier les résultats en voyager les chemins d’accès le plus court entre ID 1 et tous les autres objets. Notez que le chemin le plus court entre ID 1 et 4 ID est 5. Il s’agit de la distance totale entre <span style="color:orange">ID 1 et 3</span>, puis <span style="color:red">ID 3 et 4</span>.

    ![Dessiner des objets en tant que cercles avec les chemins d’accès le plus court tirées entre](./media/hdinsight-hadoop-giraph-install-linux/giraph-graph-out.png)


## <a name="next-steps"></a>Étapes suivantes

- [Installer et utiliser les clusters teinte sur HDInsight](hdinsight-hadoop-hue-linux.md). La nuance est une interface utilisateur qui vous permettent de créer, exécuter et enregistrer des travaux cochon et Hive, ainsi que de parcourir le stockage par défaut pour votre HDInsight cluster web.

- [Installer R sur clusters HDInsight](hdinsight-hadoop-r-scripts-linux.md): obtenir des Instructions sur l’utilisation de personnalisation pour installer et utiliser R sur clusters HDInsight Hadoop cluster. R est un environnement pour calculer des statistiques et langue open source. Il propose des centaines de fonctions statistiques intégrées et sa propre langage de programmation qui combine les aspects de la programmation orientée objet et fonctionnelle. Il fournit également des fonctions graphiques étendues.

- [Installer mode série sur LAN.r sur clusters HDInsight](hdinsight-hadoop-solr-install-linux.md). Personnalisation de cluster permet d’installer mode série sur LAN.r sur HDInsight Hadoop clusters. Mode série sur LAN.r vous permet d’effectuer des opérations de recherche puissante sur des données stockées.
