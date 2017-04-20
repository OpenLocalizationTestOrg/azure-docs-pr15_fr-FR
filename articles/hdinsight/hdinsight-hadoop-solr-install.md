<properties
    pageTitle="Action de Script permet d’installer mode série sur LAN.r sur cluster Hadoop | Microsoft Azure"
    description="Découvrez comment personnaliser cluster HDInsight avec mode série sur LAN.r à l’aide d’Action de Script."
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

# <a name="install-and-use-solr-on-hdinsight-hadoop-clusters"></a>Installer et utiliser le mode série sur LAN.r sur HDInsight Hadoop clusters

Découvrez comment personnaliser cluster HDInsight en fonction de Windows avec mode série sur LAN.r à l’aide de Script Action et comment utiliser le mode série sur LAN.r pour rechercher des données. Pour plus d’informations sur l’utilisation du mode série sur LAN.r avec un cluster Linux, voir [installer et utiliser mode série sur LAN.r sur clusters HDinsight Hadoop (Linux)](hdinsight-hadoop-solr-install-linux.md).
 
Vous pouvez installer mode série sur LAN.r sur n’importe quel type de cluster (Hadoop, vague de, HBase, commandé) sur Azure HDInsight à l’aide de *Script Action*. Un exemple de script pour installer le mode série sur LAN.r sur un cluster HDInsight est disponible à partir d’un blob de stockage Azure en lecture seule en [https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1). 

L’exemple de script fonctionne uniquement avec la version de cluster HDInsight 3.1. Pour plus d’informations sur les versions de cluster HDInsight, voir [HDInsight cluster versions](hdinsight-component-versioning.md).

L’exemple de script utilisé dans cette rubrique crée un cluster mode série sur LAN.r fonctionnant sous Windows avec une configuration spécifique. Si vous souhaitez configurer le cluster mode série sur LAN.r avec différentes collections, milieu des fragments, schémas, réplicas, etc., vous devez modifier le script et le mode série sur LAN.r binaires en conséquence.

**Articles connexes**

- [Installer et utiliser le mode série sur LAN.r sur clusters HDinsight Hadoop (Linux)](hdinsight-hadoop-solr-install-linux.md)
- [Hadoop créer des groupes dans un HDInsight](hdinsight-provision-clusters.md): obtenir des informations générales sur la création de clusters HDInsight.
- [Personnaliser cluster HDInsight à l’aide de Script Action][hdinsight-cluster-customize]: obtenir des informations générales sur la personnalisation des clusters HDInsight à l’aide de Script Action.
- [Action de Script développer des scripts pour HDInsight](hdinsight-hadoop-script-actions.md).


## <a name="what-is-solr"></a>Quel est le mode série sur LAN.r ?

<a href="http://lucene.apache.org/solr/features.html" target="_blank">Mode série sur LAN.r Apache</a> est une plateforme de recherche d’entreprise qui permet de recherche en texte intégral puissante sur les données. Tandis que Hadoop permet de stocker et gérer de grandes quantités de données, mode série sur LAN.r Apache fournit les fonctionnalités de recherche pour récupérer rapidement les données. 

## <a name="install-solr-using-portal"></a>Installer mode série sur LAN.r à l’aide du portail

1. Commencer à créer un cluster à l’aide de l’option **Créer personnalisés** , tels que décrits sur [Hadoop créer des groupes dans un HDInsight](hdinsight-provision-clusters.md#portal).
2. Dans la page **Actions de Script** de l’Assistant, cliquez sur **Ajouter une action de script** pour fournir des détails sur l’action script, comme indiqué ci-dessous :

    ![Action de Script d’utilisation pour personnaliser un cluster] (./media/hdinsight-hadoop-solr-install/hdi-script-action-solr.png "Action de Script d’utilisation pour personnaliser un cluster")

    <table border='1'>
        <tr><th>Propriété</th><th>Valeur</th></tr>
        <tr><td>Nom</td>
            <td>Attribuez un nom à l’action de script. Par exemple, <b>Mode série sur LAN.r installer</b>.</td></tr>
        <tr><td>Script URI</td>
            <td>Spécifier l’identificateur URI (Uniform Resource) pour le script qui est appelé pour personnaliser le cluster. Par exemple, <i>https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1</i></td></tr>
        <tr><td>Type de nœud</td>
            <td>Spécifier les nœuds sur lequel s’exécute le script de personnalisation. Vous pouvez choisir <b>tous les nœuds</b>, <b>nœuds de tête uniquement</b>ou <b>uniquement les nœuds de travail</b>.
        <tr><td>Paramètres</td>
            <td>Spécifier les paramètres, le cas échéant par le script. Le script pour installer le mode série sur LAN.r ne nécessite pas tous les paramètres, afin que vous pouvez laisser cette est vide.</td></tr>
    </table>

    Vous pouvez ajouter plusieurs actions de script pour installer plusieurs composants sur le cluster. Une fois que vous avez ajouté les scripts, cliquez sur la case à cocher pour commencer à créer le cluster.


## <a name="use-solr"></a>Utiliser le mode série sur LAN.r

Vous devez commencer par l’indexation mode série sur LAN.r avec certains fichiers de données. Vous pouvez ensuite utiliser mode série sur LAN.r pour exécuter des requêtes de recherche sur les données indexées. Procédez comme suit pour utiliser le mode série sur LAN.r dans un cluster HDInsight :

1. **Utiliser protocole RDP (Remote Desktop) à distance dans le cluster HDInsight avec mode série sur LAN.r installé**. À partir du portail Azure, activer le Bureau à distance pour le cluster que vous avez créé avec le mode série sur LAN.r installé et remote puis dans le cluster. Pour obtenir des instructions, voir [se connecter à clusters HDInsight à l’aide de RDP](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp).

2. **Mode série sur LAN.r Index par téléchargement de fichiers de données**. Lorsque vous indexez mode série sur LAN.r, vous y placez les documents que vous pouvez être amené à rechercher. Pour indexer mode série sur LAN.r, utiliser le protocole RDP à distance dans le cluster, accédez à l’application de bureau, ouvrez la ligne de commande Hadoop et accédez à **C:\apps\dist\solr-4.7.2\example\exampledocs**. Exécutez la commande suivante :

        java -jar post.jar solr.xml monitor.xml

    Vous verrez le résultat suivant sur la console :

        POSTing file solr.xml
        POSTing file monitor.xml
        2 files indexed.
        COMMITting Solr index changes to http://localhost:8983/solr/update..
        Time spent: 0:00:01.624

    L’utilitaire post.jar index mode série sur LAN.r avec des documents de deux échantillons, **solr.xml** et **monitor.xml**. L’utilitaire post.jar et les exemples de documents sont disponibles avec l’installation du mode série sur LAN.r.

3. **Utiliser le tableau de bord mode série sur LAN.r pour effectuer une recherche dans les documents indexés**. Dans la session RDP au cluster HDInsight, ouvrez Internet Explorer et lancer le tableau de bord mode série sur LAN.r en **http://headnodehost:8983/mode série sur LAN.r / #/**. Dans le volet gauche, dans le menu déroulant **Sélecteur de base** , sélectionnez **collection1**et de, cliquez sur **requête**. Par exemple, pour sélectionner et renvoyer tous les documents en mode série sur LAN.r, fournissent les valeurs suivantes :

    * Dans la zone de texte **q** , entrez ** \*:**\*. Ceci renvoie tous les documents qui sont indexés dans mode série sur LAN.r. Si vous souhaitez effectuer une recherche dans une chaîne spécifique dans les documents, vous pouvez entrer cette chaîne ici.
    
    * Dans la zone de texte **wt** , sélectionnez le format de sortie. Valeur par défaut est **json**. Cliquez sur **exécuter la requête**.

    ![Action de Script d’utilisation pour personnaliser un cluster] (./media/hdinsight-hadoop-solr-install/hdi-solr-dashboard-query.png "Exécuter une requête sur tableau de bord mode série sur LAN.r")
    
    La sortie renvoie les deux documents que nous avons utilisé pour l’indexation mode série sur LAN.r. La sortie ressemble à ceci :

            "response": {
                "numFound": 2,
                "start": 0,
                "maxScore": 1,
                "docs": [
                  {
                    "id": "SOLR1000",
                    "name": "Solr, the Enterprise Search Server",
                    "manu": "Apache Software Foundation",
                    "cat": [
                      "software",
                      "search"
                    ],
                    "features": [
                      "Advanced Full-Text Search Capabilities using Lucene",
                      "Optimized for High Volume Web Traffic",
                      "Standards Based Open Interfaces - XML and HTTP",
                      "Comprehensive HTML Administration Interfaces",
                      "Scalability - Efficient Replication to other Solr Search Servers",
                      "Flexible and Adaptable with XML configuration and Schema",
                      "Good unicode support: héllo (hello with an accent over the e)"
                    ],
                    "price": 0,
                    "price_c": "0,USD",
                    "popularity": 10,
                    "inStock": true,
                    "incubationdate_dt": "2006-01-17T00:00:00Z",
                    "_version_": 1486960636996878300
                  },
                  {
                    "id": "3007WFP",
                    "name": "Dell Widescreen UltraSharp 3007WFP",
                    "manu": "Dell, Inc.",
                    "manu_id_s": "dell",
                    "cat": [
                      "electronics and computer1"
                    ],
                    "features": [
                      "30\" TFT active matrix LCD, 2560 x 1600, .25mm dot pitch, 700:1 contrast"
                    ],
                    "includes": "USB cable",
                    "weight": 401.6,
                    "price": 2199,
                    "price_c": "2199,USD",
                    "popularity": 6,
                    "inStock": true,
                    "store": "43.17614,-90.57341",
                    "_version_": 1486960637584081000
                  }
                ]
              }


4. **Recommandé : sauvegarder les données indexées du mode série sur LAN.r au stockage d’objets Blob Azure associé au cluster HDInsight**. Comme une bonne pratique, vous devez sauvegarder les données indexées mode série sur LAN.r des nœuds de cluster sur stockage d’objets Blob Azure. Effectuez les opérations suivantes pour ce faire :

    1. À partir de la session RDP, ouvrez Internet Explorer, puis pointez sur l’URL suivante :

            http://localhost:8983/solr/replication?command=backup

        Vous devriez voir une réponse à ceci :

            <?xml version="1.0" encoding="UTF-8"?>
            <response>
              <lst name="responseHeader">
                <int name="status">0</int>
                <int name="QTime">9</int>
              </lst>
              <str name="status">OK</str>
            </response>

    2. Dans la session distante, accédez à {SOLR_HOME}\{Collection} \data. Pour le cluster créé à l’aide de l’exemple de script, ce doit être **C:\apps\dist\solr-4.7.2\example\solr\collection1\data**. À cet emplacement, vous devez voir un dossier instantané créé avec un nom semblable à * *instantané.* horodatage***.

    3. Compressez le dossier instantané et téléchargez-le sur stockage d’objets Blob Azure. Dans la ligne de commande Hadoop, accédez à l’emplacement du dossier de capture instantanée à l’aide de la commande suivante :

              hadoop fs -CopyFromLocal snapshot._timestamp_.zip /example/data

        Cette commande copie la capture instantanée à /example/data/sous le conteneur dans le compte de stockage associé au cluster par défaut.

## <a name="install-solr-using-aure-powershell"></a>Installer à l’aide de Aure PowerShell mode série sur LAN.r

Voir [clusters HDInsight personnaliser à l’aide de Script Action](hdinsight-hadoop-customize-cluster.md#call_scripts_using_powershell).  L’exemple montre comment installer explosion à l’aide de PowerShell Azure. Vous devez personnaliser le script pour utiliser [https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1).

## <a name="install-solr-using-net-sdk"></a>Installer mode série sur LAN.r à l’aide du Kit de développement .NET

Voir [clusters HDInsight personnaliser à l’aide de Script Action](hdinsight-hadoop-customize-cluster.md#call_scripts_using_azure_powershell). L’exemple montre comment installer explosion à l’aide du Kit de développement .NET. Vous devez personnaliser le script pour utiliser [https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1).



## <a name="see-also"></a>Voir aussi

- [Installer et utiliser le mode série sur LAN.r sur clusters HDinsight Hadoop (Linux)](hdinsight-hadoop-solr-install-linux.md)
- [Hadoop créer des groupes dans un HDInsight](hdinsight-provision-clusters.md): obtenir des informations générales sur la création de clusters HDInsight.
- [Personnaliser cluster HDInsight à l’aide de Script Action][hdinsight-cluster-customize]: obtenir des informations générales sur la personnalisation des clusters HDInsight à l’aide de Script Action.
- [Action de Script développer des scripts pour HDInsight](hdinsight-hadoop-script-actions.md).
- [Installer et utiliser explosion sur clusters HDInsight][hdinsight-install-spark]: exemple de Script Action sur l’installation d’explosion.
- [Installer R sur clusters HDInsight][hdinsight-install-r]: exemple de Script Action sur l’installation R.
- [Installer Giraph sur les clusters HDInsight](hdinsight-hadoop-giraph-install.md): exemple de Script Action sur l’installation de Giraph.


[powershell-install-configure]: powershell-install-configure.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md
