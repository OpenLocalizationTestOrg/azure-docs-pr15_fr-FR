<properties
    pageTitle="Installer et utiliser Giraph sur groupes Hadoop dans un HDInsight | Microsoft Azure"
    description="Découvrez comment personnaliser cluster HDInsight avec Giraph et l’utilisation de Giraph."
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
    ms.date="02/05/2016"
    ms.author="nitinme"/>

# <a name="install-and-use-giraph-in-hdinsight"></a>Installer et utiliser Giraph dans HDInsight


Découvrez comment personnaliser cluster HDInsight en fonction de Windows avec Giraph à l’aide de Script Action et l’utilisation de Giraph pour traiter les graphiques à grande échelle. Pour plus d’informations sur l’utilisation de Giraph avec un cluster Linux, voir [Installer Giraph sur les clusters HDInsight Hadoop (Linux)](hdinsight-hadoop-giraph-install-linux.md).
 
Vous pouvez installer Giraph sur n’importe quel type de cluster (Hadoop, vague de, HBase, commandé) sur Azure HDInsight à l’aide de *Script Action*. Un exemple de script pour installer Giraph sur un cluster HDInsight est disponible à partir d’un blob de stockage Azure en lecture seule en [https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1). L’exemple de script fonctionne uniquement avec la version de cluster HDInsight 3.1. Pour plus d’informations sur les versions de cluster HDInsight, voir [HDInsight cluster versions](hdinsight-component-versioning.md).

**Articles connexes**

- [Installer Giraph sur HDInsight Hadoop clusters (Linux)](hdinsight-hadoop-giraph-install-linux.md)
- [Hadoop créer des groupes dans un HDInsight](hdinsight-provision-clusters.md): obtenir des informations générales sur la création de clusters HDInsight.
- [Personnaliser cluster HDInsight à l’aide de Script Action][hdinsight-cluster-customize]: obtenir des informations générales sur la personnalisation des clusters HDInsight à l’aide de Script Action.
- [Action de Script développer des scripts pour HDInsight](hdinsight-hadoop-script-actions.md).

## <a name="what-is-giraph"></a>Quelles sont les Giraph ?

<a href="http://giraph.apache.org/" target="_blank">Apache Giraph</a> , vous pouvez exécuter graph traitement à l’aide Hadoop et peut être utilisé avec Azure HDInsight. Diagrammes de modèle de relations entre les objets, tels que les connexions routeur sur un réseau de grande taille tels qu’Internet ou relations entre les personnes sur des réseaux sociaux (parfois appelées sous forme de graphique social) un autre. Traitement de graphique vous permet de motif sur les relations entre des objets dans un graphique, tels que :

- Identification des amis potentiels en fonction de vos relations actuelles.
- Identifier l’itinéraire le plus court entre deux ordinateurs dans un réseau.
- Calcule le rang de page des pages Web.


## <a name="install-giraph-using-portal"></a>Installer Giraph à l’aide du portail

1. Commencer à créer un cluster à l’aide de l’option **Créer personnalisés** , tels que décrits sur [Hadoop créer des groupes dans un HDInsight](hdinsight-provision-clusters.md#portal).
2. Dans la page **Actions de Script** de l’Assistant, cliquez sur **Ajouter une action de script** pour fournir des détails sur l’action script, comme indiqué ci-dessous :

    ![Action de Script d’utilisation pour personnaliser un cluster] (./media/hdinsight-hadoop-giraph-install/hdi-script-action-giraph.png "Action de Script d’utilisation pour personnaliser un cluster")

    <table border='1'>
        <tr><th>Propriété</th><th>Valeur</th></tr>
        <tr><td>Nom</td>
            <td>Attribuez un nom à l’action de script. Par exemple, <b>Giraph installer</b>.</td></tr>
        <tr><td>Script URI</td>
            <td>Spécifier l’identificateur URI (Uniform Resource) pour le script qui est appelé pour personnaliser le cluster. Par exemple, <i>https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1</i></td></tr>
        <tr><td>Type de nœud</td>
            <td>Spécifier les nœuds sur lequel s’exécute le script de personnalisation. Vous pouvez choisir <b>tous les nœuds</b>, <b>nœuds de tête uniquement</b>ou <b>uniquement les nœuds de travail</b>.
        <tr><td>Paramètres</td>
            <td>Spécifier les paramètres, le cas échéant par le script. Le script installer Giraph ne nécessite pas tous les paramètres, afin que vous pouvez laisser cette est vide.</td></tr>
    </table>

    Vous pouvez ajouter plusieurs actions de script pour installer plusieurs composants sur le cluster. Une fois que vous avez ajouté les scripts, cliquez sur la case à cocher pour commencer à créer le cluster.

## <a name="use-giraph"></a>Utiliser Giraph

Nous utilisons l’exemple SimpleShortestPathsComputation pour illustrer l’implémentation <a href = "http://people.apache.org/~edwardyoon/documents/pregel.pdf">Pregel</a> base pour trouver le chemin le plus court entre des objets dans un graphique. Suivez les étapes suivantes pour charger les exemples de données et le fichier jar exemple, exécutez une tâche à l’aide de l’exemple SimpleShortestPathsComputation et afficher les résultats.

1. Télécharger un fichier de données exemple sur stockage d’objets Blob Azure. Sur votre ordinateur local, créez un nouveau fichier nommé **tiny_graph.txt**. Il doit contenir les lignes suivantes :

        [0,0,[[1,1],[3,3]]]
        [1,0,[[0,1],[2,2],[3,1]]]
        [2,0,[[1,2],[4,4]]]
        [3,0,[[0,3],[1,1],[4,4]]]
        [4,0,[[3,4],[2,4]]]

    Téléchargez le fichier tiny_graph.txt sur le stockage principal pour votre cluster HDInsight. Pour obtenir des instructions sur la façon de télécharger des données, voir [télécharger des données pour les projets Hadoop de HDInsight](hdinsight-upload-data.md).

    Ces données décrivent une relation entre des objets dans un graphique orienté, en utilisant le format [source\_id, source\_valeur, [[dest\_id], [bord\_valeur],...]]. Chaque ligne représente une relation entre un **source\_id** objet et un ou plusieurs **dest\_id** objets. La **bord\_valeur** (ou poids) peut être considéré comme la rupture ou la distance de la connexion entre **source_id** et **dest\_id**.

    Dessiné, et à l’aide de la valeur (ou l’épaisseur) en tant que la distance entre les objets, les données ci-dessus peuvent ressembler à ceci :

    ![tiny_graph.txt dessiné sous forme de cercles avec lignes de variés distance entre](./media/hdinsight-hadoop-giraph-install/giraph-graph.png)



4. Exécutez l’exemple SimpleShortestPathsComputation. Utiliser les applets de commande PowerShell Azure pour exécuter l’exemple en utilisant le fichier tiny_graph.txt comme entrée. 

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

        $clusterName = "clustername"
        # Giraph examples jar
        $jarFile = "wasbs:///example/jars/giraph-examples.jar"
        # Arguments for this job
        $jobArguments = "org.apache.giraph.examples.SimpleShortestPathsComputation",
                        "-ca", "mapred.job.tracker=headnodehost:9010",
                        "-vif", "org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat",
                        "-vip", "wasbs:///example/data/tiny_graph.txt",
                        "-vof", "org.apache.giraph.io.formats.IdWithValueTextOutputFormat",
                        "-op",  "wasbs:///example/output/shortestpaths",
                        "-w", "2"
        # Create the definition
        $jobDefinition = New-AzureHDInsightMapReduceJobDefinition
          -JarFile $jarFile
          -ClassName "org.apache.giraph.GiraphRunner"
          -Arguments $jobArguments

        # Run the job, write output to the Azure PowerShell window
        $job = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $jobDefinition
        Write-Host "Wait for the job to complete ..." -ForegroundColor Green
        Wait-AzureHDInsightJob -Job $job
        Write-Host "STDERR"
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardError
        Write-Host "Display the standard output ..." -ForegroundColor Green
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardOutput

    Dans l’exemple ci-dessus, remplacez **nomducluster** par le nom de votre cluster HDInsight avec Giraph installé.

5. Afficher les résultats. Une fois que la tâche est terminée, le résultat sera stocké dans deux fichiers de sortie dans la __wasbs : / / / exemple/out/shotestpaths__ dossier. Les fichiers sont appelés __partie-m-00001__ et __partie-m-00002__. Procédez comme suit pour télécharger et afficher le résultat :

        $subscriptionName = "<SubscriptionName>"       # Azure subscription name
        $storageAccountName = "<StorageAccountName>"   # Azure Storage account name
        $containerName = "<ContainerName>"             # Blob storage container name

        # Select the current subscription
        Select-AzureSubscription $subscriptionName

        # Create the Storage account context object
        $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
        $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey

        # Download the job output to the workstation
        Get-AzureStorageBlobContent -Container $containerName -Blob example/output/shortestpaths/part-m-00001 -Context $storageContext -Force
        Get-AzureStorageBlobContent -Container $containerName -Blob example/output/shortestpaths/part-m-00002 -Context $storageContext -Force

    Cela crée la structure du répertoire __exemple/sortie/shortestpaths__ dans le répertoire actif sur votre poste de travail et télécharger les fichiers de deux sortie à cet emplacement.

    Utiliser l’applet de commande __Chat__ pour afficher le contenu des fichiers :

        Cat example/output/shortestpaths/part*

    La sortie doit ressembler à ce qui suit :


        0   1.0
        4   5.0
        2   2.0
        1   0.0
        3   1.0

    La SimpleShortestPathComputation exemple est codé en dur commence objet ID 1 et trouver le chemin le plus court vers d’autres objets. Pour que la sortie doit être lu comme `destination_id distance`, où distance correspond à la valeur (ou l’épaisseur) des bords parcourus entre objet ID 1 et l’ID cible.

    Cette visualisation, vous pouvez vérifier les résultats en voyager les chemins d’accès le plus court entre ID 1 et tous les autres objets. Notez que le chemin le plus court entre ID 1 et 4 ID est 5. Il s’agit de la distance totale entre <span style="color:orange">ID 1 et 3</span>, puis <span style="color:red">ID 3 et 4</span>.

    ![Dessiner des objets en tant que cercles avec les chemins d’accès le plus court tirées entre](./media/hdinsight-hadoop-giraph-install/giraph-graph-out.png)

## <a name="install-giraph-using-aure-powershell"></a>Installer Giraph avec Aure PowerShell

Voir [clusters HDInsight personnaliser à l’aide de Script Action](hdinsight-hadoop-customize-cluster.md#call_scripts_using_powershell).  L’exemple montre comment installer explosion à l’aide de PowerShell Azure. Vous devez personnaliser le script pour utiliser [https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1).

## <a name="install-giraph-using-net-sdk"></a>Installer Giraph à l’aide du Kit de développement .NET

Voir [clusters HDInsight personnaliser à l’aide de Script Action](hdinsight-hadoop-customize-cluster.md#call_scripts_using_azure_powershell). L’exemple montre comment installer explosion à l’aide du Kit de développement .NET. Vous devez personnaliser le script pour utiliser [https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1).


## <a name="see-also"></a>Voir aussi

- [Installer Giraph sur HDInsight Hadoop clusters (Linux)](hdinsight-hadoop-giraph-install-linux.md)
- [Hadoop créer des groupes dans un HDInsight](hdinsight-provision-clusters.md): obtenir des informations générales sur la création de clusters HDInsight.
- [Personnaliser cluster HDInsight à l’aide de Script Action][hdinsight-cluster-customize]: obtenir des informations générales sur la personnalisation des clusters HDInsight à l’aide de Script Action.
- [Action de Script développer des scripts pour HDInsight](hdinsight-hadoop-script-actions.md).
- [Installer et utiliser explosion sur clusters HDInsight][hdinsight-install-spark]: exemple de Script Action sur l’installation d’explosion.
- [Installer R sur clusters HDInsight][hdinsight-install-r]: exemple de Script Action sur l’installation R.
- [Installer mode série sur LAN.r sur clusters HDInsight](hdinsight-hadoop-solr-install.md): exemple de Script Action sur l’installation du mode série sur LAN.r.



[tools]: https://github.com/Blackmist/hdinsight-tools
[aps]: http://azure.microsoft.com/documentation/articles/install-configure-powershell/

[powershell-install]: ../powershell-install-configure.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md
