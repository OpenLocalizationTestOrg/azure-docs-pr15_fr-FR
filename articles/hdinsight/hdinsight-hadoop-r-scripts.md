<properties
    pageTitle="Utiliser R dans HDInsight personnaliser clusters | Microsoft Azure"
    description="Découvrez comment installer R à l’aide de Script Action et utilisez R sur clusters HDInsight."
    services="hdinsight"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/14/2016"
    ms.author="jgao"/>

# <a name="install-and-use-r-on-hdinsight-hadoop-clusters"></a>Installer et utiliser R sur HDInsight Hadoop clusters

Découvrez comment personnaliser Windows basée cluster HDInsight avec R à l’aide de Script Action, et comment utiliser R sur HDInsight clusters. La [couche premium](https://azure.microsoft.com/pricing/details/hdinsight/) offrant pour HDInsight inclut serveur R dans le cadre de votre cluster HDInsight. Cela permet aux scripts R permet d’exécuter des calculs distribués MapReduce et explosion. Pour plus d’informations, voir [prise en main à l’aide de serveur R sur HDInsight](hdinsight-hadoop-r-server-get-started.md). Pour plus d’informations sur l’utilisation de R avec un cluster Linux, voir [installer et utiliser R sur clusters HDinsight Hadoop (Linux)](hdinsight-hadoop-r-scripts-linux.md).
 
Vous pouvez installer R sur n’importe quel type de cluster (Hadoop, vague de, HBase, commandé) sur Azure HDInsight à l’aide de *Script Action*. Un exemple de script pour installer R sur un cluster HDInsight est disponible à partir d’un blob de stockage Azure en lecture seule en [https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1). 

**Articles connexes**

- [Installer et utiliser R sur clusters HDinsight Hadoop (Linux)](hdinsight-hadoop-r-scripts-linux.md)
- [Hadoop créer des groupes dans un HDInsight](hdinsight-provision-clusters.md): obtenir des informations générales sur la création de clusters HDInsight
- [Personnaliser cluster HDInsight à l’aide de Script Action][hdinsight-cluster-customize]: obtenir des informations générales sur la personnalisation des clusters HDInsight à l’aide de Script Action
- [Développer des scripts d’Action de Script pour HDInsight](hdinsight-hadoop-script-actions.md)

## <a name="what-is-r"></a>Qu’est-R ?

Le <a href="http://www.r-project.org/" target="_blank">Projet R pour le calcul statistique</a> constitue un environnement pour calculer des statistiques et le langage source ouvert. R fournit des centaines de fonctions statistiques intégré et sa propre langage de programmation qui combine les aspects de la programmation orientée objet et fonctionnelle. Il fournit également des fonctions graphiques étendues. R est l’environnement de programmation préféré pour les plus professionnel statistiques et scientifiques dans un large éventail de champs.

R est compatible avec Azure Blob Storage (WASB) afin que les données stockées à cet emplacement peuvent être traitées avec R sur HDInsight.  

## <a name="install-r"></a>Installer R

Un [exemple de script](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1) pour installer R sur un cluster HDInsight est disponible à partir d’un blob en lecture seule dans le stockage Azure. Cette section fournit des instructions sur l’utilisation de l’exemple de script lors de la création du cluster à l’aide du portail Azure.

> [AZURE.NOTE] L’exemple de script a été introduite avec la version de cluster HDInsight 3.1. Pour plus d’informations sur les versions cluster HDInsight, voir [les versions cluster HDInsight](hdinsight-component-versioning.md).

1. Lorsque vous créez un cluster HDInsight à partir du portail, cliquez sur **Configuration facultative**, puis cliquez sur **Actions de Script**.
2. Dans la page **Actions de Script** , entrez les valeurs suivantes :

    ![Action de Script d’utilisation pour personnaliser un cluster] (./media/hdinsight-hadoop-r-scripts/hdi-r-script-action.png "Action de Script d’utilisation pour personnaliser un cluster")

    <table border='1'>
        <tr><th>Propriété</th><th>Valeur</th></tr>
        <tr><td>Nom</td>
            <td>Attribuez un nom à l’action de script, par exemple, <b>R installer</b>.</td></tr>
        <tr><td>Script URI</td>
            <td>Spécifier l’URI pour le script qui est appelé pour personnaliser le cluster, par exemple, <i>https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1</i></td></tr>
        <tr><td>Type de nœud</td>
            <td>Spécifier les nœuds sur lequel s’exécute le script de personnalisation. Vous pouvez choisir <b>Tous les nœuds</b>, <b>uniquement les nœuds de tête</b>ou <b>nœuds de travail</b> uniquement.
        <tr><td>Paramètres</td>
            <td>Spécifier les paramètres, le cas échéant par le script. Toutefois, le script installer R ne nécessite pas tous les paramètres, afin que vous pouvez laisser cette est vide.</td></tr>
    </table>

    Vous pouvez ajouter plusieurs actions de script pour installer plusieurs composants sur le cluster. Une fois que vous avez ajouté les scripts, cliquez sur la case à cocher pour démarrer crating le cluster.

Vous pouvez également utiliser le script installer R sur HDInsight à l’aide de PowerShell Azure ou le Kit de développement .NET HDInsight. Vous trouverez des instructions pour ces procédures plus loin dans cet article.

## <a name="run-r-scripts"></a>Exécuter des scripts R
Cette section décrit comment exécuter un script R sur le cluster Hadoop avec HDInsight.

1. **Établir une connexion Bureau à distance pour le cluster**: dans le portail, activez Bureau à distance pour le cluster que vous avez créé avec R installé et connectez-vous au cluster. Pour obtenir des instructions, voir [se connecter à clusters HDInsight à l’aide de RDP](hdinsight-administer-use-management-portal.md#rdp).

2. **Ouvrez la console R**: installation R la place un lien vers la console R sur le bureau du nœud principal. Cliquez sur pour ouvrir la console R.

3. **Exécuter le script R**: R le script peut être exécuté directement à partir de la console R, collez-le, sélectionnez-la, puis en appuyant sur ENTRÉE. Voici un exemple simple de script qui génère les numéros de 1 à 100 et puis les multiplie par 2.

        library(rmr2)
        library(rhdfs)
        ints = to.dfs(1:100)
        calc = mapreduce(input = ints, map = function(k, v) cbind(v, 2*v))
        from.dfs(calc)

Les deux premières lignes appellent les bibliothèques RHadoop installées avec les R. La dernière ligne affiche les résultats à la console. La sortie doit ressembler à ceci :

    [1,]  1 2
    [2,]  2 4
    .
    .
    .
    [98,]  98 196
    [99,]  99 198
    [100,] 100 200


## <a name="install-r-using-aure-powershell"></a>Installer R à l’aide de Aure PowerShell

Voir [clusters HDInsight personnaliser à l’aide de Script Action](hdinsight-hadoop-customize-cluster.md#call_scripts_using_powershell).  L’exemple montre comment installer explosion à l’aide de PowerShell Azure. Vous devez personnaliser le script pour utiliser [https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1).

## <a name="install-r-using-net-sdk"></a>Installer R à l’aide du Kit de développement .NET

Voir [clusters HDInsight personnaliser à l’aide de Script Action](hdinsight-hadoop-customize-cluster.md#call_scripts_using_azure_powershell). L’exemple montre comment installer explosion à l’aide du Kit de développement .NET. Vous devez personnaliser le script pour utiliser [https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps11).


## <a name="see-also"></a>Voir aussi

- [Installer et utiliser R sur clusters HDinsight Hadoop (Linux)](hdinsight-hadoop-r-scripts-linux.md)
- [Hadoop créer des groupes dans un HDInsight](hdinsight-provision-clusters.md): obtenir des informations générales sur la création de clusters HDInsight
- [Personnaliser cluster HDInsight à l’aide de Script Action][hdinsight-cluster-customize]: obtenir des informations générales sur la personnalisation des clusters HDInsight à l’aide de Script Action
- [Développer des scripts d’Action de Script pour HDInsight](hdinsight-hadoop-script-actions.md)
- [Installer et utiliser explosion sur clusters HDInsight][hdinsight-install-spark]: exemple de Script Action sur l’installation d’explosion
- [Installer Giraph sur les clusters HDInsight](hdinsight-hadoop-giraph-install.md): exemple de Script Action sur l’installation de Giraph
- [Installer mode série sur LAN.r sur clusters HDInsight](hdinsight-hadoop-solr-install-linux.md): exemple de Script Action sur l’installation du mode série sur LAN.r.

[powershell-install-configure]: powershell-install-configure.md
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
[hdinsight-install-spark]: hdinsight-apache-spark-jupyter-spark-sql.md
