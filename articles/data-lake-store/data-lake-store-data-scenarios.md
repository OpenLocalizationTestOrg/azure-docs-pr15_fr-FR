<properties 
   pageTitle="Scénarios de données impliquant des données Lake Store | Microsoft Azure" 
   description="Comprendre les différents scénarios et les outils à l’aide des données peuvent ingérée, traitées, téléchargé et affiché dans un magasin Lake de données" 
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
   ms.date="09/06/2016"
   ms.author="nitinme"/>

# <a name="using-azure-data-lake-store-for-big-data-requirements"></a>À l’aide de magasin de Lake données Azure aux exigences de données volumineux

Il existe quatre étapes clés dans volumineux traitement des données :

* Traiter les grandes quantités de données dans un magasin de données en temps réel ou par lots
* Traitement des données
* Télécharger les données
* Visualiser les données

Dans cet article, nous examinons ces étapes en ce qui concerne Azure données Lake Store pour comprendre les options et les outils disponibles pour répondre à vos besoins en matière de données volumineux.

## <a name="ingest-data-into-data-lake-store"></a>Transfert de données dans le magasin de Lake de données

Cette section présente les différentes sources de données et les différentes façons dans lesquelles ces données peuvent être transférées dans un compte de données Lake Store.

![Acquisition des données dans données Lake Store] (./media/data-lake-store-data-scenarios/ingest-data.png "Acquisition des données dans données Lake Store")

### <a name="ad-hoc-data"></a>Données ad hoc

Il s’agit plus petits ensembles de données qui sont utilisés pour le prototypage une application de données volumineux. Il existe différentes façons de données ad hoc réception en fonction de la source de données.

| Source de données        | Acquisition à l’aide                                                                        |
|--------------------|----------------------------------------------------------------------------------------|
| Ordinateur local     | <ul> <li>[Portail Azure](/data-lake-store-get-started-portal.md)</li> <li>[PowerShell Azure](data-lake-store-get-started-powershell.md)</li> <li>[Azure infrastructure du langage commun disponibilité sur plusieurs plateformes](data-lake-store-get-started-cli.md)</li> <li>[Utilisation de données Lake Tools pour Visual Studio](../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md#upload-source-data-files) </li></ul> |
| Stockage Azure Blob | <ul> <li>[Données Azure usine](../data-factory/data-factory-azure-datalake-connector.md#sample-copy-data-from-azure-blob-to-azure-data-lake-store)</li> <li>[Outil AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)</li><li>[DistCp s’exécutant sur cluster HDInsight](data-lake-store-copy-data-wasb-distcp.md)</li> </ul> |

 
### <a name="streamed-data"></a>Flux de données

Il s’agit de données qui peuvent être générées par diverses sources telles que des applications, appareils, capteurs, etc.. Ces données peuvent être transférées dans un magasin Lake données par les outils de diverses. Ces outils sont généralement capturer et traiter les données sur une base événement par événement dans en temps réel, puis rédigez les événements par lots dans le magasin de Lake données afin qu’ils peuvent être ensuite traités. 

Voici des outils que vous pouvez utiliser :
 
* [Flux azure Analytique] (.. / sortie du lake données stream analytique) - événements ingérés dans événement Hubs peuvent être écrites à Lake de données Azure à l’aide d’un Azure données Lake stocker les résultats.
* [Vague d’azure HDInsight](../hdinsight/hdinsight-storm-write-data-lake-store.md) - directement au magasin des Lake données écrire des données à partir du cluster vague.
* [EventProcessorHost](../event-hubs/event-hubs-csharp-ephcs-getstarted.md#receive-messages-with-eventprocessorhost) – vous pouvez recevoir des événements de l’événement Hubs et puis l’écrire au magasin Lake des données à l’aide du [Kit de développement .NET données Lake Store](data-lake-store-get-started-net-sdk.md).

### <a name="relational-data"></a>Données relationnelles

Vous pouvez également de source de données à partir de bases de données relationnelles. Sur une période donnée, bases de données relationnelles collectent de grandes quantités de données qui peuvent donner des informations clées si traitées dans un pipeline de données volumineux. Vous pouvez utiliser les outils suivants pour déplacer ce type de données vers des données Lake Store.

* [Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Données Azure usine](../data-factory/data-factory-data-movement-activities.md)

### <a name="web-server-log-data-upload-using-custom-applications"></a>Données du journal serveur Web (téléchargement à l’aide des applications personnalisées)

Ce type de jeu de données est spécifiquement entouré parce que l’analyse des données de journal de serveur web est un cas d’utilisation courants pour les applications de données volumineux et nécessite des volumes importants de fichiers journaux à être téléchargés au magasin Lake de données. Vous pouvez utiliser un des outils suivants pour écrire vos propres scripts ou des applications à télécharger ce type de données.

* [Azure infrastructure du langage commun disponibilité sur plusieurs plateformes](data-lake-store-get-started-cli.md)
* [PowerShell Azure](data-lake-store-get-started-powershell.md)
* [Kit de développement .NET Azure données Lake Store](data-lake-store-get-started-net-sdk.md)
* [Données Azure usine](../data-factory/data-factory-data-movement-activities.md)

Pour le téléchargement des données de journal de serveur web, mais également pour le téléchargement d’autres types de données (par exemple, éléments de mise en réseau), il est une bonne approche pour écrire vos propres scripts/applications personnalisées, car cela vous donne la possibilité d’inclure vos données chargement de composant dans le cadre de votre application de données volumineux plus grande. Dans certains cas, ce code peut prendre la forme d’un script ou un utilitaire de ligne de commande simple. Dans les autres cas, le code peut être utilisé pour intégrer volumineux traitement des données dans une application métier ou d’une solution.


### <a name="data-associated-with-azure-hdinsight-clusters"></a>Données associées aux clusters Azure HDInsight

La plupart des types de cluster HDInsight (vague d’Hadoop, HBase,) prend en charge les données Lake Store comme un référentiel de stockage de données. HDInsight clusters accéder aux données à partir d’Azure stockage BLOB (WASB). Pour améliorer les performances, vous pouvez copier les données à partir de WASB dans un compte de données Lake Store associé au cluster. Vous pouvez utiliser les outils suivants pour copier les données.

* [Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)
* [Service AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
* [Données Azure usine](../data-factory/data-factory-azure-datalake-connector.md#sample-copy-data-from-azure-blob-to-azure-data-lake-store)

### <a name="data-stored-in-on-premise-or-iaas-hadoop-clusters"></a>Données stockées dans locales ou IaaS Hadoop clusters

Grandes quantités de données peuvent être stockées dans clusters Hadoop existants, localement sur les ordinateurs à l’aide de HDFS. Les groupes Hadoop peuvent être dans un déploiement sur site ou au sein d’un cluster IaaS sur Azure. Configuration requise pour copier ces données au magasin Lake des données Azure pour une approche unique ou d’une manière périodique peuvent. Il existe différentes options que vous pouvez utiliser pour y parvenir. Vous trouverez ci-dessous une liste des alternatives et les compromis associés.

| Approche  | Plus d’informations | Avantages   | Considérations relatives à la  |
|-----------|---------|--------------|-----------------|
| Azure données usine (chargeur) permet de copier des données directement à partir de clusters Hadoop au magasin Lake des données Azure | [Chargeur prend en charge HADOOP comme source de données](../data-factory/data-factory-hdfs-connector.md) | Chargeur prend en charge l’emploi HADOOP première classe de bout en bout gestion et de surveillance | Nécessite la passerelle de gestion des données à déployer localement ou dans le cluster IaaS |
| Exporter des données à partir de Hadoop sous forme de fichiers. Copier les fichiers dans Azure données Lake Store à l’aide de mécanisme approprié.                                   | Vous pouvez copier des fichiers à l’aide de Azure données Lake Store : <ul><li>[Azure PowerShell pour système d’exploitation Windows](data-lake-store-get-started-powershell.md)</li><li>[Azure disponibilité sur plusieurs plateformes infrastructure du langage commun pour non - système d’exploitation Windows](data-lake-store-get-started-cli.md)</li><li>Application personnalisée à l’aide de n’importe quel SDK Data Lake Store</li></ul> | Prise en main rapide. Opérations possibles chargements personnalisées                                                   | Processus en plusieurs étapes impliquant plusieurs technologies d’assistance. Gestion et surveillance augmentera s’avérer difficile au fil du temps, étant donné la nature personnalisée des outils |
| Utilisez Distcp pour copier les données d’Hadoop au stockage Azure. Copier les données à partir du stockage Azure au magasin Lake des données à l’aide de mécanisme approprié. | Vous pouvez copier les données à partir du stockage Azure à l’utilisation de données Lake Store : <ul><li>[Données Azure usine](../data-factory/data-factory-data-movement-activities.md)</li><li>[Outil AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)</li><li>[DistCp Apache s’exécutant sur clusters HDInsight](data-lake-store-copy-data-wasb-distcp.md)</li></ul>| Vous pouvez utiliser les outils open source. | Processus en plusieurs étapes impliquant plusieurs technologies d’assistance |

### <a name="really-large-datasets"></a>Est-il vraiment grands ensembles de données

Pour télécharger des jeux de données comprises dans plusieurs to, en utilisant les méthodes ci-dessus peut parfois être lente et coûteuses. Dans ce cas, vous pouvez utiliser le cas échéant.

* **À l’aide d’Azure ExpressRoute**. Azure ExpressRoute vous permet de créer des connexions entre Azure centres de données et l’infrastructure dans vos locaux. Il propose une option fiable pour le transfert de grandes quantités de données. Pour plus d’informations, voir [documentation Azure ExpressRoute](../expressroute/expressroute-introduction.md).


* **« Hors connexion » télécharger des données**. Si à l’aide d’Azure ExpressRoute n’est pas possible pour une raison quelconque, vous pouvez utiliser le [service Azure importer/exporter](../storage/storage-import-export-service.md) à livrer des disques durs avec vos données à un centre de données Azure. Vos données tout d’abord téléchargées dans Azure stockage BLOB. Vous pouvez ensuite utiliser [Azure Data Factory](../data-factory/data-factory-azure-datalake-connector.md#sample-copy-data-from-azure-blob-to-azure-data-lake-store) ou [AdlCopy outil](data-lake-store-copy-data-azure-storage-blob.md) pour copier des données à partir d’objets BLOB Azure stockage au magasin Lake des données.

    >[AZURE.NOTE] Tandis qu’au moyen du service d’importation/exportation, la taille des fichiers sur les disques que vous avez effectuées du centre de données Azure ne doit pas être supérieur à 200 Go.


## <a name="process-data-stored-in-data-lake-store"></a>Traiter des données stockées dans le magasin de Lake de données

Une fois que les données sont disponibles dans les données Lake Store vous pouvez lancer une analyse dans données en utilisant les applications de données volumineux pris en charge. Pour l’instant, vous pouvez utiliser Azure HDInsight et Azure données Lake Analytique pour exécuter des tâches d’analyse des données sur les données stockées dans le magasin de Lake de données. 

![Analyse des données dans les données Lake Store] (./media/data-lake-store-data-scenarios/analyze-data.png "Analyse des données dans les données Lake Store")

Vous pouvez examiner les exemples suivants.

* [Créer un cluster HDInsight avec données Lake Store comme espace de stockage](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Utiliser des données Azure Lake Analytique avec données Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)



## <a name="download-data-from-data-lake-store"></a>Télécharger des données à partir de données Lake Store

Vous pouvez également vous télécharger ou déplacer des données d’Azure données Lake Store pour les scénarios tels que :

* Déplacer des données vers d’autres référentiels d’interagir avec vos pipelines de traitement des données existantes. Par exemple, vous souhaiterez peut-être déplacer des données à partir de données Lake Store sur base de données SQL Azure ou local SQL Server.
* Télécharger des données sur votre ordinateur local pour traitement dans les environnements IDE lors de la création des prototypes d’application.

![Données de sortie à partir de données Lake Store] (./media/data-lake-store-data-scenarios/egress-data.png "Données de sortie à partir de données Lake Store")

Dans ce cas, vous pouvez utiliser une des options suivantes :

* [Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Données Azure usine](../data-factory/data-factory-data-movement-activities.md)
* [Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)

Vous pouvez également utiliser les méthodes suivantes pour écrire votre propre script/application pour télécharger des données à partir de données Lake Store.

* [Azure infrastructure du langage commun disponibilité sur plusieurs plateformes](data-lake-store-get-started-cli.md)
* [PowerShell Azure](data-lake-store-get-started-powershell.md)
* [Kit de développement .NET Azure données Lake Store](data-lake-store-get-started-net-sdk.md)

## <a name="visualize-data-in-data-lake-store"></a>Visualiser des données dans les données Lake Store

Vous pouvez utiliser un mélange de services pour créer des représentations visuelles de données stockées dans données Lake Store.

![Visualiser les données dans les données Lake Store] (./media/data-lake-store-data-scenarios/visualize-data.png "Visualiser les données dans les données Lake Store")

* Vous pouvez commencer à l’aide de [Azure Data Factory pour déplacer des données à partir de données Lake Store Warehouse de données SQL Azure](../data-factory/data-factory-data-movement-activities.md#supported-data-stores)
* Après cela, vous pouvez [intégrer Power BI avec Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-integrate-power-bi.md) pour créer une représentation visuelle des données.
