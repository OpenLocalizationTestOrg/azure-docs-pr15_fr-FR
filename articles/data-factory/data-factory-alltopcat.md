<properties
    pageTitle="Toutes les rubriques de service de données Factory | Microsoft Azure"
    description="Table de toutes les rubriques pour le service Azure nommée Data Factory qui existent sur http://azure.microsoft.com/documentation/articles/, titre et description."
    services="data-factory"
    documentationCenter=""
    authors="spelluru"
    manager="jhubbard"
    editor="MightyPen"/>

<tags
    ms.service="data-factory"
    ms.workload="data-factory"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/05/2016"
    ms.author="spelluru"/>


# <a name="all-topics-for-azure-data-factory-service"></a>Toutes les rubriques de service Factory de données Azure

Cette rubrique répertorie toutes les rubriques qui s’applique directement au service de **Données usine** d’Azure. Vous pouvez rechercher cette page Web pour les mots clés à l’aide de **Ctrl + F**, pour trouver les sujets qui intéressent.




## <a name="new"></a>Nouveau

| &nbsp; | Titre | Description |
| --: | :-- | :-- |
| 1 | [Déplacer des données à partir d’Amazon Redshift à l’aide d’usine de données Azure](data-factory-amazon-redshift-connector.md) | Découvrez comment déplacer des données d’Amazon Redshift à l’aide d’Azure Data Factory. |
| 2 | [Déplacer des données à partir d’Amazon Simple Storage Service à l’aide d’usine de données Azure](data-factory-amazon-simple-storage-service-connector.md) | Découvrez comment déplacer des données à partir d’Amazon Simple Storage Service (S3) à l’aide d’Azure Data Factory. |
| 3 | [Données Azure Factory - Assistant copie](data-factory-azure-copy-wizard.md) | Découvrez comment utiliser l’Assistant données usine Azure copier pour copier des données à partir de sources de données prises en charge dans les récepteurs. |
| 4 | [Didacticiel : Créer votre première usine données Azure à l’aide de l’API REST de données par défaut](data-factory-build-your-first-pipeline-using-rest-api.md) | Dans ce didacticiel, vous créez un pipeline de Azure Data Factory exemple à l’aide de l’API REST de données par défaut. |
| 5 | [Didacticiel : Créer un pipeline avec une activité copie à l’aide de l’API .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md) | Dans ce didacticiel, vous créez un pipeline Azure Data Factory avec une activité de copie à l’aide de l’API .NET. |
| 6 | [Didacticiel : Créer un pipeline avec une activité copie à l’aide de l’API REST](data-factory-copy-activity-tutorial-using-rest-api.md) | Dans ce didacticiel, vous créez un pipeline Azure Data Factory avec une activité de copie à l’aide de l’API REST. |
| 7 | [Assistant de copie de données par défaut](data-factory-copy-wizard.md) | Découvrez comment utiliser l’Assistant données usine copier pour copier des données à partir de sources de données prises en charge dans les récepteurs. |
| 8 | [Passerelle de gestion des données](data-factory-data-management-gateway.md) | Configurer une passerelle de données pour déplacer des données entre en local et le nuage. Utiliser la passerelle de gestion des données dans Azure Data Factory pour déplacer vos données. |
| 9 | [Déplacer des données d’une base de données locale Cassandra à l’aide d’usine de données Azure](data-factory-onprem-cassandra-connector.md) | Découvrez comment déplacer des données d’une base de données locale Cassandra à l’aide d’Azure Data Factory. |
| 10 | [Déplacer des données à partir de MongoDB à l’aide d’usine de données Azure](data-factory-on-premises-mongodb-connector.md) | Découvrez comment déplacer des données d’une base de données MongoDB avec Azure Data Factory. |
| 11 | [Déplacer des données depuis Salesforce à l’aide d’usine de données Azure](data-factory-salesforce-connector.md) | Découvrez comment déplacer des données depuis Salesforce à l’aide de Azure Data Factory. |


## <a name="updated-articles-data-factory"></a>Articles mis à jour, Data Factory

Cette section répertorie les articles qui ont été mis à jour récemment, où la mise à jour a été grandes ou significative. Pour chaque article mis à jour, un extrait de code approximative du texte ajouté démarque est affichée. Les articles ont été mis à jour dans la plage de dates de **2016-08-22** à **2016-10-05**.

| &nbsp; | Article | Texte mis à jour, l’extrait de code | Mise à jour lorsque |
| --: | :-- | :-- | :-- |
| 12 | [Azure Data Factory - journal des modifications de l’API .NET](data-factory-api-change-log.md) | Cet article fournit des informations sur les modifications apportées à Azure Data Factory SDK dans une version spécifique. Vous pouvez trouver le dernier package NuGet pour Azure Data Factory ici (https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories) **Version 4.11.0** fonctionnalité ajouts : / les types de service liées suivants ont été ajoutés : - OnPremisesMongoDbLinkedService (https://msdn.microsoft.com/library/mt765129.aspx) - AmazonRedshiftLinkedService (https://msdn.microsoft.com/library/mt765121.aspx) - AwsAccessKeyLinkedService (https://msdn.microsoft.com/library/mt765144.aspx) / les types de jeu de données suivants ont été ajoutés : - MongoDbCollectionDataset (https://msdn.microsoft.com/library/mt765145.aspx) - AmazonS3Dataset (https://msdn.microsoft.com/library/mt765112.aspx) / les types de source de copie suivants ont été ajoutés :-MongoDbSource (https://msdn.microsoft.com/en-US/library/mt765123.aspx) **Version 4.10.0** / les propriétés facultatives suivantes ont été ajoutées au format du texte :-Ski | 2016-09-22 |
| 13 | [Déplacer des données vers et à partir d’objets Blob Azure à l’aide d’usine de données Azure](data-factory-azure-blob-connector.md) |  / copyBehavior / définit le comportement de copie lorsque la source est BlobSource ou système de fichiers.  / **PreserveHierarchy :** conserve la hiérarchie des fichiers dans le dossier cible. Le chemin d’accès relatif du fichier source à dossier source est identique au chemin d’accès relatif du fichier cible dans le dossier cible... br /... br /. **FlattenHierarchy :** tous les fichiers à partir du dossier source se trouvent dans le premier niveau du dossier cible. Les fichiers cible ont nom générée automatiquement. .br /... br /. **MergeFiles : (valeur par défaut)** fusionne tous les fichiers à partir du dossier source vers un fichier. Si le nom de fichier/Blob n’est spécifié, le nom de fichier fusionné serait le nom spécifié ; dans le cas contraire, serait nom de fichier généré automatiquement.  / Ne / **BlobSource** prend également en charge ces deux propriétés pour la compatibilité descendante. / **treatEmptyAsNull**: indique s’il faut considérer null ou une chaîne vide comme valeur null. / **skipHeaderLineCount** - indique le nombre de lignes doit être ignoré. Elle s’applique uniquement lorsque dataset d’entrée utilise format du texte. De même, **BlobSink** prend en charge l | 2016-09-28 |
| 14 | [Créer des pipelines prédictives à l’aide d’activités d’apprentissage automatique Azure](data-factory-azure-ml-batch-execution-activity.md) | **Service Web requiert plusieurs entrées** Si le service web prend plusieurs entrées, utilisez la propriété **webServiceInputs** au lieu d’utiliser **webServiceInput**. Jeux de données qui est référencées par la **webServiceInputs** doit également être inclus dans les activités **entrées**. Dans votre expérience Azure ML, saisie de service web et les ports de sortie et les paramètres globaux portent des noms par défaut (« input1 », « input2 ») que vous pouvez personnaliser. Les noms que vous utilisez pour webServiceInputs, webServiceOutputs et globalParameters paramètres doivent correspondre exactement les noms dans les expériences. Vous pouvez afficher la charge utile de demande exemple sur la page aide de l’exécution du lot pour votre point de terminaison Azure ML vérifier le mappage attendu.    {« nom » : « PredictivePipeline », « propriétés » : {« description » : « utiliser AzureML modèle », « activités » : {« nom » : « MLActivity », « type » : « AzureMLBatchExecution », « description » : « analyse prédiction sur lot entrée », « entrées » : {« nom » : « inputDataset1 »}, {« nom » : « inputDatase | 2016-09-13 |
| 15 | [Copier l’activité guide des performances et réglage](data-factory-copy-activity-performance.md) | 1. **établir un planning de référence**. Pendant la phase de développement, testez votre pipeline en utilisant activité copie par rapport à un échantillon de données représentatives. Vous pouvez utiliser le Factory de données découpage modèle (données-usine-planification-et-execution.md time-series-datasets-and-data-slices) pour limiter la quantité de données que vous travaillez.  Recueillir les temps d’exécution et les caractéristiques de performance à l’aide **de surveillance et d’application de gestion**. Choisissez **un seul moniteur et gérer** dans votre page d’accueil par défaut de données. Dans l’arborescence, choisissez le **jeu de données de sortie**. Dans la liste **Activité Windows** , sélectionnez l’activité de copie exécuter. **Activité Windows** répertorie la durée d’activité de copie et la taille des données copiées. Le débit est répertorié dans **l’Explorateur Windows activité**. Pour en savoir plus sur l’application, voir moniteur et gérer les pipelines Azure Data Factory à l’aide de la surveillance et application de gestion (données-usine-moniteur-gérer-app.md).  ! Activité (./media/data-factory-copy-activity-performance/mmapp-activity-run-details.pn détails de l’exécution | 2016-09-27 |
| 16 | [Didacticiel : Créer un pipeline avec une activité copie à l’aide de Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) |   Notez les points suivants :-dataset **type** est défini sur **AzureBlob**.     - **linkedServiceName** est défini sur **AzureStorageLinkedService**. Vous avez créé ce service lié à l’étape 2.     - **folderPath** est défini sur le conteneur **adftutorial** . Vous pouvez également spécifier le nom d’un objet blob dans le dossier à l’aide de la propriété **nom de fichier** . Étant donné que vous ne spécifiez pas le nom de l’objet blob, données de tous les objets BLOB dans le conteneur sont considérées comme un d’entrée de données.    -le **type** de format est défini sur **format du texte** - il existe deux champs dans le texte fichier ΓÇô **prénom** et **nom** ΓÇô séparé par une virgule (**columnDelimiter**) - la **disponibilité** est défini sur **toutes les heures** (**fréquence** est définie sur **heure** et **intervalle** est égale à **1**). Par conséquent, Data Factory recherche les données d’entrée toutes les heures dans le dossier racine du conteneur blob (**adftutorial**) que vous avez spécifié.  Si vous ne spécifiez un **nom de fichier** pour un jeu de données **d’entrée** , tous les fichiers BLOB à partir du dossier d’entrée (**folderPath**) sont consid | 2016-09-29 |
| 17 | [Créer, surveiller et gérer les références de données Azure à l’aide de données usine .NET SDK](data-factory-create-data-factories-programmatically.md) | Notez l’ID de l’application et le mot de passe (secret client) et l’utiliser dans la procédure pas à pas. **Abonnement Azure obtenir et ID client** Si vous n’avez pas la version la plus récente de PowerShell installé sur votre ordinateur, suivez les instructions de la rubrique Comment installer et configurer PowerShell Azure (… / powershell-installer-configure.md) article pour l’installer. 1. Démarrez Azure PowerShell et exécutez la commande suivante 2. Exécutez la commande suivante, puis entrez le nom d’utilisateur et mot de passe que vous utilisez pour vous connecter au portail Azure.         Connexion AzureRmAccount si que vous avez un abonnement Azure associé à ce compte, vous n’avez pas besoin d’effectuer les deux étapes suivantes. 3. Exécutez la commande suivante pour afficher tous les abonnements pour ce compte.       Get-AzureRmSubscription 4. Exécutez la commande suivante pour sélectionner l’abonnement que vous souhaitez utiliser. Remplacez **NameOfAzureSubscription** par le nom de votre abonnement Azure.       Get-AzureRmSubscription - SubscriptionName NameOfAzureSubscription / Set-AzureRmCo | 2016-09-14 |
| 18 | [Pipelines et activités dans usine données Azure](data-factory-create-pipelines.md) |       « commencer » : « 2016-07-12T00:00:00Z », « fin » : « 2016-07-13T00:00:00Z »}} Notez les points suivants : / dans la section activités, il n'est qu’une seule activité dont le **type** est défini sur **Copier**. / Entrée pour l’activité est définie sur **InputDataset** et sortie de l’activité est définie sur **OutputDataset**. / Dans la section **typeProperties** , **BlobSource** est spécifiée comme type de source et **SqlSink** est spécifiée comme type de récepteur. Pour les différentes étapes de création de ce pipeline, consultez le didacticiel : copier des données depuis le stockage Blob de base de données SQL (data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). **Pipeline de transformation d’exemple** Dans le pipeline d’exemple ci-dessous, il existe une activité de type **HDInsightHive** dans la section **activités** . Dans cet exemple, l’activité HDInsight Hive (données-usine-hive-activity.md) transforme les données d’un stockage d’objets Blob Azure en exécutant un fichier de script Hive sur un cluster Azure HDInsight Hadoop.  {« nom » : « TransformPipeline », « p | 2016-09-27 |
| 19 | [Transformer des données dans une usine de données Azure](data-factory-data-transformation-activities.md) | Données usine prend en charge les activités de transformation de données suivantes peuvent être ajoutées à canalisations (données-usine-créer-pipelines.md) soit individuellement ou en chaîne avec une autre activité. .  AZURE. Remarque pour une procédure pas à pas des instructions détaillées, voir créer un pipeline avec l’article Hive transformation (data-factory-build-your-first-pipeline.md). **Activité HDInsight Hive** L’activité HDInsight Hive dans un pipeline Data Factory exécute des requêtes de Hive vos propres ou cluster de fonctionnant sous Windows/Linux HDInsight à la demande. Consultez l’article (données-usine-hive-activity.md) de Hive activité pour plus d’informations sur cette activité. **Cochon HDInsight activité** L’activité HDInsight cochon dans un pipeline Data Factory exécute des requêtes de cochon vos propres ou cluster de fonctionnant sous Windows/Linux HDInsight à la demande. Consultez l’article (données-usine-cochon-activity.md) de cochon activité pour plus d’informations sur cette activité. **Activité HDInsight MapReduce** L’activité HDInsight MapReduce dans un pipeline Data Factory exécute MapReduce programmes sur y | 2016-09-26 |
| 20 | [Planification de données par défaut et l’exécution](data-factory-scheduling-and-execution.md) | CopyActivity2 s’exécute uniquement si la CopyActivity1 a exécuté correctement et Dataset2 est disponible. Voici le pipeline exemple JSON : {« nom » : « ChainActivities », « propriétés » : {« description » : « Activités exécuter dans l’ordre », « activités » : {« type » : « Copier », « typeProperties » : {« source » : {« type » : « BlobSource »}, « récepteur » : {« type » : « BlobSink », « copyBehavior » : « PreserveHierarchy », « writeBatchSize » : 0, « writeBatchTimeout » : « 00 : 00:00 »}}, « entrées » : {« nom » : « Dataset1 »}, « sorties » : {« nom » : « Dataset2 »}, « stratégie » : {« délai de » : » 01 : 00:00 »}, « scheduler » : {« fréquence » : « Heure », « intervalle » : 1}, « nom » : « CopyFromBlob1ToBlob2 », « description » : « Copier des données à partir d’un objet blob à un autre »}, {« type » : « Copier », « typeProperties » : {« source » : {« type » : « BlobSource »}, « récepteur » : {« type » : « BlobSink », « writeBatchSize » : 0, « writeBatchTimeout » : « 00 : 00:00 »}}, « dans | 2016-09-28 |





## <a name="tutorials"></a>Didacticiels

| &nbsp; | Titre | Description |
| --: | :-- | :-- |
| 21 | [Didacticiel : Créer votre première pipeline pour traiter les données à l’aide de cluster Hadoop](data-factory-build-your-first-pipeline.md) | Ce didacticiel Azure Data Factory vous montre comment créer et planifier une usine de données qui traite les données à l’aide de script Hive sur un cluster Hadoop. |
| 22 | [Didacticiel : Créer votre première usine données Azure à l’aide du Gestionnaire de ressources Azure modèle](data-factory-build-your-first-pipeline-using-arm.md) | Dans ce didacticiel, vous créez un pipeline de Azure Data Factory exemple à l’aide d’un modèle Azure le Gestionnaire de ressources. |
| 23 | [Didacticiel : Créer votre première usine données Azure à l’aide du portail Azure](data-factory-build-your-first-pipeline-using-editor.md) | Dans ce didacticiel, vous créez un pipeline de Azure Data Factory exemple à l’aide de l’éditeur de données par défaut dans le portail Azure. |
| 24 | [Didacticiel : Créer votre première usine données Azure à l’aide de PowerShell Azure](data-factory-build-your-first-pipeline-using-powershell.md) | Dans ce didacticiel, vous créez un pipeline de Azure Data Factory exemple à l’aide de PowerShell Azure. |
| 25 | [Didacticiel : Usine de données créer votre première Azure à l’aide de Microsoft Visual Studio](data-factory-build-your-first-pipeline-using-vs.md) | Dans ce didacticiel, vous créez un pipeline de Azure Data Factory exemple à l’aide de Visual Studio. |
| 26 | [Didacticiel : Créer un pipeline avec une activité copie à l’aide du portail Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) | Dans ce didacticiel, vous créez un pipeline Azure Data Factory avec une activité de copie à l’aide de l’éditeur par défaut des données dans le portail Azure. |
| 27 | [Didacticiel : Créer un pipeline avec une activité copie à l’aide de PowerShell Azure](data-factory-copy-activity-tutorial-using-powershell.md) | Dans ce didacticiel, vous créez un pipeline Azure Data Factory avec une activité de copie à l’aide de PowerShell Azure. |
| 28 | [Didacticiel : Créer un pipeline avec une activité copie à l’aide de Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) | Dans ce didacticiel, vous créez un pipeline Azure Data Factory avec une activité de copie à l’aide de Visual Studio. |
| 29 | [Copier des données depuis le stockage Blob à l’aide de données par défaut de la base de données SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) | Ce didacticiel montre comment utiliser copie activité dans un pipeline Azure Data Factory pour copier des données depuis le stockage Blob dans la base de données SQL. |
| 30 | [Didacticiel : Créer un pipeline avec une activité copie à l’aide de l’Assistant copie de données par défaut](data-factory-copy-data-wizard-tutorial.md) | Dans ce didacticiel, vous créez un pipeline Azure Data Factory avec une activité de copie à l’aide de l’Assistant copie pris en charge par Data Factory |



## <a name="data-movement"></a>Déplacement de données

| &nbsp; | Titre | Description |
| --: | :-- | :-- |
| 31 | [Déplacer des données vers et à partir d’objets Blob Azure à l’aide d’usine de données Azure](data-factory-azure-blob-connector.md) | Apprenez à copier des données blob dans Azure Data Factory. Utiliser notre exemple : comment copier des données vers et depuis le stockage d’objets Blob Azure et base de données SQL Azure. |
| 32 | [Déplacer des données vers et depuis Azure données Lake Store à l’aide d’usine de données Azure](data-factory-azure-datalake-connector.md) | Découvrez comment déplacer des données vers ou à partir d’Azure données Lake Store à l’aide d’usine de données Azure |
| 33 | [Déplacer des données vers et depuis DocumentDB à l’aide d’usine de données Azure](data-factory-azure-documentdb-connector.md) | Découvrez comment déplacer des données vers/à partir de la collection de DocumentDB Azure à l’aide d’usine de données Azure |
| 34 | [Déplacer des données vers et à partir de la base de données SQL Azure à l’aide d’usine de données Azure](data-factory-azure-sql-connector.md) | Découvrez comment déplacer des données vers ou à partir de base de données SQL Azure à l’aide d’Azure Data Factory. |
| 35 | [Déplacer des données vers et depuis Azure SQL Data Warehouse à l’aide d’usine de données Azure](data-factory-azure-sql-data-warehouse-connector.md) | Découvrez comment déplacer des données vers ou à partir d’Azure SQL Data Warehouse à l’aide d’usine de données Azure |
| 36 | [Déplacer des données vers et depuis la Table Azure à l’aide d’usine de données Azure](data-factory-azure-table-connector.md) | Découvrez comment déplacer des données vers ou à partir d’Azure Table Storage à l’aide d’Azure Data Factory. |
| 37 | [Copier l’activité guide des performances et réglage](data-factory-copy-activity-performance.md) | Découvrez les principaux facteurs qui affectent les performances de déplacer des données dans Azure Data Factory lorsque vous utilisez activité de copie. |
| 38 | [Déplacer des données à l’aide d’activité de copie](data-factory-data-movement-activities.md) | En savoir plus sur le déplacement des données dans les pipelines Data Factory : migration des données entre banques cloud et entre un magasin local et un magasin cloud. Utilisez l’activité de copie. |
| 39 | [Notes de publication pour la passerelle de gestion des données](data-factory-gateway-release-notes.md) | Notes de publication de tory de passerelle de gestion des données |
| 40 | [Déplacer des données d’HADOOP locales à l’aide d’usine de données Azure](data-factory-hdfs-connector.md) | Découvrez comment déplacer des données d’HADOOP locales à l’aide d’Azure Data Factory. |
| 41 | [Surveiller et gérer les pipelines Azure Data Factory à l’aide de nouveaux de surveillance et d’application de gestion](data-factory-monitor-manage-app.md) | Découvrez comment utiliser de surveillance et d’application de gestion pour surveiller et gérer les pipelines et les références de données Azure. |
| 42 | [Déplacer des données entre sources locales et le cloud avec la passerelle de gestion des données](data-factory-move-data-between-onprem-and-cloud.md) | Configurer une passerelle de données pour déplacer des données entre en local et le nuage. Utiliser la passerelle de gestion des données dans Azure Data Factory pour déplacer vos données. |
| 43 | [Déplacer des données source à partir d’un OData à l’aide de Azure Data Factory](data-factory-odata-connector.md) | Découvrez comment déplacer des données à partir de sources OData à l’aide de Azure Data Factory. |
| 44 | [Déplacer des banques de données ODBC à partir de données à l’aide d’usine de données Azure](data-factory-odbc-connector.md) | Découvrez comment déplacer des données de banques de données ODBC à l’aide d’Azure Data Factory. |
| 45 | [Déplacer des données à partir de DB2 à l’aide d’usine de données Azure](data-factory-onprem-db2-connector.md) | Découvrez comment déplacer des données à partir de la base de données DB2 à l’aide d’usine de données Azure |
| 46 | [Déplacer des données vers et depuis un système de fichiers local à l’aide d’usine de données Azure](data-factory-onprem-file-system-connector.md) | Découvrez comment déplacer des données vers et depuis un système de fichiers local à l’aide de Azure Data Factory. |
| 47 | [Déplacer des données à partir de MySQL à l’aide d’usine de données Azure](data-factory-onprem-mysql-connector.md) | Découvrez comment déplacer des données à partir de la base de données MySQL avec Azure Data Factory. |
| 48 | [Déplacer des données vers ou à partir d’Oracle en local à l’aide de Azure Data Factory](data-factory-onprem-oracle-connector.md) | Découvrez comment déplacer des données vers ou à partir d’une base de données Oracle qui se trouve en local à l’aide d’Azure Data Factory. |
| 49 | [Déplacer des données de PostgreSQL à l’aide d’usine de données Azure](data-factory-onprem-postgresql-connector.md) | Découvrez comment déplacer des données à partir de la base de données PostgreSQL avec Azure Data Factory. |
| 50 | [Déplacer des données de Sybase à l’aide d’usine de données Azure](data-factory-onprem-sybase-connector.md) | Découvrez comment déplacer des données à partir de la base de données Sybase à l’aide d’Azure Data Factory. |
| 51 | [Déplacer des données de Teradata à l’aide d’usine de données Azure](data-factory-onprem-teradata-connector.md) | En savoir plus sur le connecteur Teradata pour le service de données par défaut qui vous permet de déplacer des données à partir de la base de données Teradata |
| 52 | [Déplacer des données vers et à partir de SQL Server en local ou sur IaaS (Azure machine virtuelle) à l’aide d’usine de données Azure](data-factory-sqlserver-connector.md) | Découvrez comment déplacer des données vers ou à partir de base de données SQL Server locale ou dans une machine virtuelle Azure à l’aide d’Azure Data Factory. |
| 53 | [Déplacer des données d’une source de table Web à l’aide d’usine de données Azure](data-factory-web-table-connector.md) | Découvrez comment déplacer des données locales un tableau dans une page Web à l’aide d’Azure Data Factory. |



## <a name="data-transformation"></a>Transformation de données

| &nbsp; | Titre | Description |
| --: | :-- | :-- |
| 54 | [Créer des pipelines prédictives à l’aide d’activités d’apprentissage automatique Azure](data-factory-azure-ml-batch-execution-activity.md) | Décrit comment créer créer prédictives pipelines à l’aide de Azure Data Factory et apprentissage automatique Azure |
| 55 | [Calculer des Services liés](data-factory-compute-linked-services.md) | En savoir plus sur cluster enviornments que vous pouvez utiliser dans Azure Data Factory pipelines pour transformation/traiter les données. |
| 56 | [Processus à grande échelle des groupes de données à l’aide de données par défaut et la feuille](data-factory-data-processing-using-batch.md) | Décrit comment traiter les grandes quantités de données dans un pipeline Azure Data Factory à l’aide de capacité de traitement en parallèle du lot Azure. |
| 57 | [Transformer des données dans une usine de données Azure](data-factory-data-transformation-activities.md) | Découvrez comment transformer les données ou processus dans Azure Data Factory à l’aide de Hadoop, apprentissage automatique ou Azure données Lake Analytique. |
| 58 | [Activité de diffusion en continu Hadoop](data-factory-hadoop-streaming-activity.md) | Découvrez comment vous pouvez utiliser l’activité de diffusion en continu Hadoop dans une usine de données Azure pour exécuter des programmes Hadoop diffusion en continu sur un cluster de HDInsight sur-à la demande/votre propre. |
| 59 | [Activité Hive](data-factory-hive-activity.md) | Découvrez comment vous pouvez utiliser l’activité ruche dans une usine de données Azure pour exécuter des requêtes Hive sur un cluster de HDInsight sur-à la demande/votre propre. |
| 60 | [Appeler des programmes MapReduce provenant d’une usine de données](data-factory-map-reduce.md) | Découvrez comment traiter les données en exécutant des programmes MapReduce sur un cluster Azure HDInsight à partir d’une usine de données Azure. |
| 61 | [Cochon activité](data-factory-pig-activity.md) | Découvrez comment vous pouvez utiliser l’activité cochon dans une usine de données Azure pour exécuter des scripts cochon sur un cluster de HDInsight sur-à la demande/votre propre. |
| 62 | [Appeler des programmes d’explosion provenant d’une usine de données](data-factory-spark.md) | Apprenez à appeler des programmes d’explosion provenant d’une usine de données Azure à l’aide de l’activité MapReduce. |
| 63 | [Activité de procédure stockée SQL Server](data-factory-stored-proc-activity.md) | Découvrez comment vous pouvez utiliser l’activité de la procédure stockée SQL Server pour appeler une procédure stockée dans une base de données SQL Azure ou SQL Azure Data Warehouse à partir d’un pipeline de données par défaut. |
| 64 | [Utiliser des activités personnalisées dans un pipeline Azure Data Factory](data-factory-use-custom-activities.md) | Découvrez comment créer des activités personnalisées et les utiliser dans un pipeline Azure Data Factory. |
| 65 | [Exécuter un script U SQL sur Azure données Lake Analytique provenant d’une usine de données Azure](data-factory-usql-activity.md) | Découvrez comment traiter les données en exécutant des scripts U-SQL Azure données Lake Analytique cluster service. |



## <a name="samples"></a>Exemples

| &nbsp; | Titre | Description |
| --: | :-- | :-- |
| 66 | [Données Azure Factory - exemples](data-factory-samples.md) | Fournit des détails sur les exemples fournis avec le service Azure Data Factory. |



## <a name="use-cases"></a>Exemples d’utilisation

| &nbsp; | Titre | Description |
| --: | :-- | :-- |
| 67 | [Études de cas client](data-factory-customer-case-studies.md) | Découvrez comment certains de nos clients ont été Azure Data Factory. |
| 68 | [Utiliser la casse - création de profils utilisateur](data-factory-customer-profiling-usecase.md) | Découvrez comment Azure Data Factory est utilisé pour créer un pilotées par les données du flux de travail (pipeline) pour les clients de jeux de profil. |
| 69 | [Utiliser la casse - recommandations de produits](data-factory-product-reco-usecase.md) | En savoir plus sur un cas d’utilisation implémenté à l’aide d’Azure Data Factory ainsi que d’autres services. |



## <a name="monitor-and-manage"></a>Surveiller et gérer

| &nbsp; | Titre | Description |
| --: | :-- | :-- |
| 70 | [Surveiller et gérer les pipelines Azure Data Factory](data-factory-monitor-manage-pipelines.md) | Découvrez comment utiliser Azure Portal et Azure PowerShell pour surveiller et gérer des références de données Azure et pipelines que vous avez créé. |



## <a name="sdk"></a>KIT DE DÉVELOPPEMENT

| &nbsp; | Titre | Description |
| --: | :-- | :-- |
| 71 | [Azure Data Factory - journal des modifications de l’API .NET](data-factory-api-change-log.md) | Décrit les modifications de dernière minute, des ajouts de fonctionnalités, correctifs etc.... dans une version spécifique d’API .NET pour le Factory de données Azure. |
| 72 | [Créer, surveiller et gérer les références de données Azure à l’aide de données usine .NET SDK](data-factory-create-data-factories-programmatically.md) | Découvrez comment créer, surveiller et gérer des références de données Azure à l’aide de données usine SDK par programme. |
| 73 | [Référence du développeur usine données Azure](data-factory-sdks.md) | Découvrez les différentes façons de créer, surveiller et gérer les références de données Azure |



## <a name="miscellaneous"></a>Divers

| &nbsp; | Titre | Description |
| --: | :-- | :-- |
| 74 | [Données Azure Factory - Forum aux Questions](data-factory-faq.md) | Forum aux questions sur Azure Data Factory. |
| 75 | [Données Azure Factory - Variables système et fonctions](data-factory-functions-variables.md) | Fournit la liste des variables système et les fonctions d’usine données Azure |
| 76 | [Données Azure Factory - règles de dénomination](data-factory-naming-rules.md) | Décrit les règles d’appellation pour les entités Data Factory. |
| 77 | [Résoudre les problèmes de données par défaut](data-factory-troubleshoot.md) | Découvrez comment résoudre les problèmes liés à l’aide d’Azure Data Factory. |

