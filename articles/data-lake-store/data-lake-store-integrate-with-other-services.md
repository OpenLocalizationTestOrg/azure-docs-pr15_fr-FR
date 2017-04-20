<properties
   pageTitle="Intégration de données Lake Store avec d’autres Services Azure | Microsoft Azure"
   description="Comprendre comment les données Lake Store s’intègre à d’autres services Azure"
   documentationCenter=""
   services="data-lake-store"
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

# <a name="integrating-data-lake-store-with-other-azure-services"></a>Intégration de données Lake Store avec d’autres Services Azure

Azure données Lake magasin peut être utilisé en association avec d’autres services Azure pour activer un large éventail de scénarios. L’article suivant répertorie les services qui données Lake Store peut être intégré à.

## <a name="use-data-lake-store-with-azure-hdinsight"></a>Utiliser le magasin de données Lake avec Azure HDInsight

Vous pouvez configurer un cluster [Azure HDInsight](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/) qui utilise des données Lake Store comme le stockage HADOOP compatibles. Pour cette version, pour les clusters Hadoop et vague de sous Windows et Linux, vous pouvez utiliser Data Lake Store uniquement comme un espace de stockage supplémentaire. Ces clusters toujours utiliseront stockage Azure (WASB) comme le stockage par défaut. Toutefois, pour les clusters HBase sous Windows et Linux, vous pouvez utiliser données Lake Store comme le stockage par défaut, ou espace de stockage supplémentaire ou les deux.

Pour obtenir des instructions sur la mise en service un cluster HDInsight avec données Lake banque d’informations, voir :

* [Mise en service un cluster HDInsight avec données Lake Store à l’aide du portail Azure](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Mise en service un cluster HDInsight avec données Lake Store à l’aide de PowerShell Azure](data-lake-store-hdinsight-hadoop-use-powershell.md)

**Vous préférez vidéos ?** Suivez les liens ci-dessous pour regarder des vidéos avec des instructions sur l’utilisation des données Lake Store avec clusters HDInsight.

* [Créer un cluster HDInsight avec accès aux données Lake Store](https://mix.office.com/watch/l93xri2yhtp2)
* Une fois que le cluster a été configurée, [accéder aux données de magasin de Lake de données à l’aide des scripts Hive et cochon](https://mix.office.com/watch/1n9g5w0fiqv1q)


## <a name="use-data-lake-store-with-azure-data-lake-analytics"></a>Utiliser le magasin de Lake données avec des données Azure Lake Analytique

[Azure données Lake Analytique](../data-lake-analytics/data-lake-analytics-overview.md) vous permet d’utiliser des données volumineux à l’échelle de cloud. Il dynamiquement dispositions ressources et vous permet d’effectuer analytique sur to ou même exaoctets de données pouvant être stockés dans un nombre de sources de données prises en charge, un d’eux étant données Lake Store. Données Lake Analytique est spécialement optimisé pour fonctionner avec Azure données Lake Store - assurant le niveau le plus élevé de performances, débit et parallélisation pour vous charges de données volumineux.

Pour obtenir des instructions sur l’utilisation des données Lake Analytique avec données Lake banque d’informations, voir [Prise en main données Lake Analytique à l’aide de données Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md).

**Vous préférez vidéos ?** Suivez les liens ci-dessous pour regarder des vidéos avec des instructions sur l’utilisation des données Lake Store avec clusters HDInsight.

* [Connecter des données Azure Lake Analytique au magasin Lake des données Azure](https://mix.office.com/watch/qwji0dc9rx9k)
* [Accès Azure données Lake Store via données Lake Analytique](https://mix.office.com/watch/1n0s45up381a8)


## <a name="use-data-lake-store-with-azure-data-factory"></a>Utiliser le magasin de Lake données avec des données Azure usine

Vous pouvez utiliser [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) pour ajouter des données à partir de tables Azure, base de données SQL Azure, Data Warehouse SQL Azure, objets BLOB Azure stockage et bases de données locale. Un citoyen première classe dans le réseau Azure, Azure Data Factory peut être utilisé pour organiser la réception de données à partir de ces source au magasin Lake des données Azure.

Pour obtenir des instructions sur l’utilisation d’Azure Data Factory avec données Lake banque d’informations, voir [déplacer des données vers et à partir de données Lake Store à l’aide de données par défaut](../data-factory/data-factory-azure-datalake-connector.md).

**Vidéos à nouveau !** Voir [Orchestration de données à l’aide d’Azure Data Factory pour Azure données Lake Store](https://mix.office.com/watch/1oa7le7t2u4ka). 

## <a name="copy-data-from-azure-storage-blobs-into-data-lake-store"></a>Copiez les données des objets BLOB Azure stockage dans données Lake magasin

Magasin de Lake données Azure fournit un outil de ligne de commande, AdlCopy, qui vous permet de copier des données depuis le stockage Blob Azure dans un compte de données Lake Store. Pour plus d’informations, voir [copier des données à partir d’Azure stockage BLOB au magasin Lake des données](data-lake-store-copy-data-azure-storage-blob.md).

## <a name="copy-data-between-azure-sql-database-and-data-lake-store"></a>Copier des données entre la base de données SQL Azure et données Lake Store

Vous pouvez utiliser Sqoop Apache pour importer et exporter des données entre la base de données SQL Azure et données Lake Store. Pour plus d’informations, voir [copier des données entre des données Lake Store et base de données SQL Azure à l’aide de Sqoop](data-lake-store-data-transfer-sql-sqoop.md).

**Regardez cette vidéo** sur [à l’aide de Apache Sqoop pour déplacer des données entre sources relationnelles et Azure données Lake sécurisée](https://mix.office.com/watch/1butcdjxmu114).

## <a name="use-data-lake-store-with-stream-analytics"></a>Utiliser le magasin de données Lake avec Analytique de flux de données

Vous pouvez utiliser des données Lake Store dans l’un des sorties pour stocker les données transmises à l’aide d’Azure flux Analytique. Pour plus d’informations, voir [données de flux de données à partir d’Azure stockage Blob dans banque Lake de données à l’aide de Azure flux Analytique](data-lake-store-stream-analytics.md).

## <a name="use-data-lake-store-with-power-bi"></a>Utiliser le magasin de Lake données avec Power BI

Vous pouvez utiliser Power BI pour importer des données à partir d’un compte données Lake Store pour analyser et visualiser les données. Pour plus d’informations, voir [analyse des données dans le magasin de Lake de données à l’aide de Power BI](data-lake-store-power-bi.md).

## <a name="use-data-lake-store-with-data-catalog"></a>Utiliser le magasin de données Lake avec le catalogue de données

Vous pouvez enregistrer des données à partir de données Lake Store dans le catalogue de données Azure afin de les rendre détectable pour la recherche dans toute l’organisation. Pour plus d’informations, voir [Enregistrer des données à partir de données Lake Store dans le catalogue de données Azure](data-lake-store-with-data-catalog.md).


## <a name="see-also"></a>Voir aussi

- [Vue d’ensemble Azure Lake de magasin de données](data-lake-store-overview.md)
- [Prise en main du Store Lake de données à l’aide du portail](data-lake-store-get-started-portal.md)
- [Prise en main du Store Lake de données à l’aide de PowerShell](data-lake-store-get-started-powershell.md)  
