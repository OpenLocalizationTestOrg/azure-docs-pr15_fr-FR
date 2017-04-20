<properties
   pageTitle="Copier des données vers et depuis WASB dans données Lake Store à l’aide de Distcp | Microsoft Azure"
   description="Utilisez l’outil Distcp pour copier des données à partir d’objets BLOB Azure stockage au magasin Lake des données"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/28/2016"
   ms.author="nitinme"/>

# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-data-lake-store"></a>Permet de copier des données entre Azure stockage BLOB et données Lake Store Distcp

> [AZURE.SELECTOR]
- [À l’aide de DistCp](data-lake-store-copy-data-wasb-distcp.md)
- [À l’aide de AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)


Une fois que vous avez créé un cluster HDInsight qui a accès à un compte de données Lake Store, vous pouvez utiliser les outils de réseau Hadoop comme Distcp pour copier les données **vers et depuis** un stockage de cluster HDInsight (WASB) dans un compte de données Lake Store. Cet article fournit des instructions sur la façon d’y parvenir.

##<a name="prerequisites"></a>Conditions préalables

Avant de commencer cet article, vous devez disposer des éléments suivants :

- **Azure un abonnement**. Voir [Azure obtenir la version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/).
- **Activer votre abonnement Azure** pour la présentation publique de données Lake Store. Consultez les [instructions](data-lake-store-get-started-portal.md#signup).
- **Cluster azure HDInsight** avec l’accès à un compte de données Lake Store. Voir [créer un cluster HDInsight avec données Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md). Vérifiez que vous activez Bureau à distance pour le cluster.

## <a name="do-you-learn-fast-with-videos"></a>Vous découvrez rapidement avec vidéos ?

[Regardez cette vidéo](https://mix.office.com/watch/1liuojvdx6sie) sur la façon de copier des données entre Azure stockage BLOB et données Lake Store à l’aide de DistCp.

## <a name="use-distcp-from-remote-desktop-windows-cluster-or-ssh-linux-cluster"></a>Utiliser Distcp à partir d’un bureau à distance (cluster Windows) ou SSH (cluster Linux)

Un cluster HDInsight est fourni avec l’utilitaire Distcp, qui peut être utilisé pour copier des données issues de différentes sources dans un cluster HDInsight. Si vous avez configuré le cluster HDInsight pour utiliser des données Lake Store comme un espace de stockage supplémentaire, l’utilitaire Distcp peut être utilisée de la-prédéfinies pour copier des données vers et à partir d’un compte de données Lake Store également. Dans cette section nous examinons comment utiliser l’utilitaire Distcp.

1. Si vous disposez d’un cluster de Windows, à distance dans un cluster HDInsight qui a accès à un compte de données Lake Store. Pour obtenir des instructions, voir [se connecter à clusters à l’aide de RDP](../hdinsight/hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp). À partir du bureau cluster, ouvrez la ligne de commande Hadoop.

    Si vous avez un cluster Linux, utiliser SSH pour vous connecter au cluster. Voir [se connecter à un cluster basé sur Linux HDInsight](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-linux-based-hdinsight-cluster). Exécuter les commandes à partir de l’invite SSH.

3. Vérifiez si vous pouvez accéder à l’objets BLOB de stockage Azure (WASB). Exécutez la commande suivante :

        hdfs dfs –ls wasb://<container_name>@<storage_account_name>.blob.core.windows.net/

    Il doit fournir une liste du contenu dans le blob de stockage.

4. De même, vérifiez si vous pouvez accéder au compte de données Lake Store du groupe de. Exécutez la commande suivante :

        hdfs dfs -ls adl://<data_lake_store_account>.azuredatalakestore.net:443/

    Il doit fournir une liste de fichiers/dossiers dans le compte données Lake.

5. Utilisez Distcp pour copier les données à partir de WASB à un compte de données Lake Store.

        hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_store_account>.azuredatalakestore.net:443/myfolder

    Cela copie le contenu du **** dossier/exemple/données/gutenberg/dans WASB à **/myfolder** dans le compte données Lake.

6. De même, utilisez Distcp pour copier les données à partir de compte données Lake Store dans WASB.

        hadoop distcp adl://<data_lake_store_account>.azuredatalakestore.net:443/myfolder wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg

    Cela copie le contenu de **/myfolder** dans le compte données Lake au **** dossier/exemple/données/gutenberg/dans WASB.

## <a name="see-also"></a>Voir aussi

- [Copier des données d’objets BLOB Azure stockage au magasin Lake des données](data-lake-store-copy-data-azure-storage-blob.md)
- [Sécuriser les données dans les données Lake Store](data-lake-store-secure-data.md)
- [Utiliser des données Azure Lake Analytique avec données Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Utiliser Azure HDInsight avec données Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)
