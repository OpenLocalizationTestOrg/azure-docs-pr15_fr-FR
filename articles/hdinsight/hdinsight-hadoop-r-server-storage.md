
<properties
   pageTitle="Azure options de stockage pour serveur R HDInsight (preview) | Microsoft Azure"
   description="En savoir plus sur les différentes options de stockage disponibles pour les utilisateurs avec serveur R sur HDInsight (preview)"
   services="HDInsight"
   documentationCenter=""
   authors="jeffstokes72"
   manager="jhubbard"
   editor="cgronlun"
/>

<tags
   ms.service="HDInsight"
   ms.devlang="R"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-services"
   ms.date="09/01/2016"
   ms.author="jeffstok"
/>

# <a name="azure-storage-options-for-r-server-on-hdinsight-preview"></a>Azure options de stockage pour serveur R HDInsight (preview)

Microsoft R Server sur HDInsight (preview) ayant accès à ces deux objets Blob Azure ou [Stockage Lake des données Azure](https://azure.microsoft.com/services/data-lake-store/), comme un moyen de conserver les données, code, les objets d’analyse de résultats et ainsi de suite.

Lorsque vous créez un cluster Hadoop dans HDInsight, vous spécifiez un compte de stockage Azure. Un conteneur de stockage Blob spécifique de ce compte conserve le système de fichiers pour le cluster que vous créez (par exemple, le Hadoop Distributed File System). Pour des raisons de performances, le cluster HDInsight est créé dans le même centre de données en tant que le compte de stockage principal que vous spécifiez. Pour plus d’informations, voir [utiliser Azure Blob storage avec HDInsight](hdinsight-hadoop-use-blob-storage.md "stockage d’objets Blob Azure utilisation avec HDInsight").   


## <a name="use-multiple-azure-blob-storage-accounts"></a>Utiliser plusieurs comptes de stockage Blob Azure

Si nécessaire, vous pouvez accéder à plusieurs comptes de stockage Azure ou conteneurs avec votre cluster HDI. Pour ce faire, vous devez spécifier les comptes d’espace de stockage supplémentaire dans l’interface utilisateur lorsque vous créez le cluster, puis procédez comme suit pour les utiliser dans R.  

1.  Créer un cluster HDInsight avec un nom de compte de stockage de **stockage1** et un conteneur par défaut appelé **Conteneur1**.
2. Spécifiez un compte de stockage supplémentaire appelé **stockage2**.  
3. Copiez le fichier mycsv.csv dans le répertoire /share et effectuer une analyse sur ce fichier.  

    ````
    hadoop fs –mkdir /share
    hadoop fs –copyFromLocal myscsv.scv /share  
    ````

3.  Dans le code R, affectez la valeur **par défaut,** le nœud de nom et définissez votre répertoire et le fichier à traiter.  

    ````
    myNameNode <- "default"
    myPort <- 0
    ````

  Emplacement des données :  

    bigDataDirRoot <-« / partager »  

  Définir le contexte de cluster explosion :

    mySparkCluster <-RxSpark(consoleOutput=TRUE)

  Définir le contexte de calcul :

    rxSetComputeContext(mySparkCluster)

  Définir le système de fichiers système un fichier distribué Hadoop (HDFS) :

    hdfsFS <-RxHdfsFileSystem (nom d’hôte = myNameNode, port = myPort)

  Spécifiez le fichier d’entrée pour analyser dans HADOOP :

    fichier d’entrée <-file.path(bigDataDirRoot,"mycsv.csv")

Toutes les références répertoire et fichier point sur le compte de stockage wasbs://container1@storage1.blob.core.windows.net. Il s’agit du **compte de stockage par défaut** associé au cluster HDInsight.

À présent, supposons que vous souhaitez traiter un fichier nommé mySpecial.csv qui se trouve dans le /private répertoire de **container2** dans **stockage2**.

Dans votre code R, pointez sur la référence de nœud de nom pour le compte de stockage **stockage2** .

    myNameNode <- "wasbs://container2@storage2.blob.core.windows.net"
    myPort <- 0

  Emplacement des données :

    bigDataDirRoot <- "/private"

  Définir le contexte de cluster explosion :

    mySparkCluster <- RxSpark(consoleOutput=TRUE, nameNode=myNameNode, port=myPort)

  Définir le contexte de calcul :

    rxSetComputeContext(mySparkCluster)

  Définir un système de fichiers HADOOP :

    hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

  Spécifiez le fichier d’entrée pour analyser dans HADOOP :

    inputFile <-file.path(bigDataDirRoot,"mySpecial.csv")

Toutes les références répertoire et fichier point maintenant sur le compte de stockage wasbs://container2@storage2.blob.core.windows.net. Il s’agit du **Nom nœud** que vous avez spécifiés.

Notez que vous devrez configurer/User/RevoShare/<SSH username> répertoire **stockage2** comme suit :

    hadoop fs -mkdir wasbs://container2@storage2.blob.core.windows.net/user
    hadoop fs -mkdir wasbs://container2@storage2.blob.core.windows.net/user/RevoShare
    hadoop fs -mkdir wasbs://container2@storage2.blob.core.windows.net/user/RevoShare/<RDP username>

## <a name="use-an-azure-data-lake-store"></a>Utiliser un magasin Lake de données Azure

Pour utiliser des données Lake magasins avec votre compte HDInsight, vous devez accéder votre cluster à chaque magasin Azure données Lake que vous voulez utiliser. Vous utilisez le magasin dans votre script R bien que vous utilisez un compte de stockage secondaire (comme décrit dans la procédure précédente).

## <a name="add-cluster-access-to-your-azure-data-lake-stores"></a>Ajouter un cluster accès à vos banques Lake de données Azure

Pour accéder à un magasin Lake de données à l’aide d’un Service Principal Azure Active Directory (AD Azure) associée à votre cluster HDInsight.

### <a name="to-add-a-service-principal"></a>Pour ajouter un Service Principal
1. Lorsque vous créez votre cluster HDInsight, sélectionnez **Cluster AAD identité** à partir de l’onglet **Source de données** .
2. Dans la boîte de dialogue **Cluster AAD identité** , sous **Sélectionnez AD Principal du Service**, sélectionnez **Créer nouveau**.

Une fois que vous donnez un nom à l’entité de Service et créez un mot de passe pour qu’elle, un nouvel onglet s’ouvre dans laquelle vous pouvez associer l’entité de Service à vos banques de données Lake.

Notez que vous pouvez également ajouter l’accès à un magasin de données Lake ultérieurement en ouvrant le magasin de données Lake dans le portail Azure et en accédant à **l’Explorateur de données** > **Access**.  Voici un exemple d’une boîte de dialogue qui montre comment créer une entité de Service et associer avec le magasin de données Lake « rkadl11 ».

![Créer le magasin de données Lake Service principe 1](./media/hdinsight-hadoop-r-server-storage/hdinsight-hadoop-r-server-storage-adls-sp1.png)


![Créer le magasin de données Lake Service principe 2](./media/hdinsight-hadoop-r-server-storage/hdinsight-hadoop-r-server-storage-adls-sp2.png)

## <a name="use-the-data-lake-store-with-r-server"></a>Utiliser le magasin de données Lake avec serveur R
Une fois que vous avez donné l’accès à un magasin Lake de données, vous pouvez utiliser le magasin dans Server R HDInsight comme vous le feriez pour un compte de stockage Azure secondaire. La seule différence qui est le préfixe **wasb : / /** devient **adl : / /** comme suit :

````
# Point to the ADL store (e.g. ADLtest)
myNameNode <- "adl://rkadl1.azuredatalakestore.net"
myPort <- 0

# Location of the data (assumes a /share directory on the ADL account)
bigDataDirRoot <- "/share"  

# Define Spark compute context
mySparkCluster <- RxSpark(consoleOutput=TRUE, nameNode=myNameNode, port=myPort)

# Set compute context
rxSetComputeContext(mySparkCluster)

# Define HDFS file system
hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

# Specify the input file in HDFS to analyze
inputFile <-file.path(bigDataDirRoot,"AirlineDemoSmall.csv")

# Create factors for days of the week
colInfo <- list(DayOfWeek = list(type = "factor",
               levels = c("Monday", "Tuesday", "Wednesday", "Thursday",
                          "Friday", "Saturday", "Sunday")))

# Define the data source
airDS <- RxTextData(file = inputFile, missingValueString = "M",
                    colInfo  = colInfo, fileSystem = hdfsFS)

# Run a linear regression
model <- rxLinMod(ArrDelay~CRSDepTime+DayOfWeek, data = airDS)
````

Voici les commandes qui sont utilisés pour configurer le compte de stockage de données Lake avec l’annuaire RevoShare et ajoutez l’exemple de fichier .csv à partir de l’exemple précédent :

````
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare/<user>

hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/share

hadoop fs -copyFromLocal /usr/lib64/R Server-7.4.1/library/RevoScaleR/SampleData/AirlineDemoSmall.csv adl://rkadl1.azuredatalakestore.net/share

hadoop fs –ls adl://rkadl1.azuredatalakestore.net/share
````

## <a name="use-azure-files-on-the-edge-node"></a>Utiliser les fichiers Azure sur le nœud de bord

Il existe également une option de stockage de données pratique pour une utilisation sur le nœud de bord appelée [Fichiers Azure](../storage/storage-how-to-use-files-linux.md "Azure fichiers"). Il vous permet de monter un partage de fichiers de stockage Azure au système de fichiers Linux. Cela peut être pratique pour le stockage de fichiers de données, les scripts de R et objets de résultat qui peuvent être nécessaires ultérieurement s’il est préférable d’utiliser le système de fichiers natif sur le nœud de bord plutôt que HADOOP.

Le principal avantage de fichiers Azure est que les partages de fichiers peuvent être monter et utilisés par n’importe quel système comportant un système d’exploitation pris en charge tels que Windows ou Linux. Par exemple, il peut être utilisé par un autre HDInsight cluster dont vous ou une personne de votre équipe, par une machine virtuelle Azure ou même par un système local.


## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous connaissez les bases de l’utilisation de la console de R à partir d’une session SSH et comment créer un nouveau cluster HDInsight incluant serveur R, utilisez les liens suivants pour découvrir d’autres façons de travailler avec les serveur R sur HDInsight.

- [Vue d’ensemble du serveur R sur HDInsight](hdinsight-hadoop-r-server-overview.md)
- [Prise en main serveur R sur Hadoop](hdinsight-hadoop-r-server-get-started.md)
- [Ajouter RStudio serveur à HDInsight premium](hdinsight-hadoop-r-server-install-r-studio.md)
- [Calculer les options de contexte pour R Server sur HDInsight](hdinsight-hadoop-r-server-compute-contexts.md)
