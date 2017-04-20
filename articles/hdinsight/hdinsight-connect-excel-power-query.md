<properties
    pageTitle="Se connecter Excel à Power Query | Microsoft Azure"
    description="Découvrez comment tirer parti des composants business intelligence et utiliser Power Query pour Excel pour accéder aux données stockées dans Hadoop sur HDInsight."
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
    ms.date="10/19/2016"
    ms.author="jgao"/>


#<a name="connect-excel-to-hadoop-by-using-power-query"></a>Se connecter Excel à l’aide de Power Query

L’intégration de composants Microsoft business intelligence (BI) avec les groupes de Hadoop dans Azure HDInsight est une fonctionnalité clé de la solution de données volumineuses Microsoft. Un exemple de cette intégration principal est la possibilité de se connecter Excel sur le compte de stockage Azure qui contient les données associées à votre cluster Hadoop à l’aide de Microsoft Power Query pour Excel. Cet article décrit comment configurer et utiliser Power Query pour interroger des données associées à un cluster Hadoop géré avec HDInsight.

> [AZURE.NOTE] Si les étapes décrites dans cet article peuvent être utilisés avec cluster un Linux ou HDInsight fonctionnant sous Windows, Windows est requis pour le poste de travail client.

### <a name="prerequisites"></a>Conditions préalables

Avant de commencer cet article, vous devez disposer des éléments suivants :

- **HDInsight un cluster**. Pour configurer une, voir [prise en main Azure HDInsight][hdinsight-get-started].
- **Poste de travail A** exécutant Windows 7, Windows Server 2008 R2 ou une version ultérieure de système d’exploitation.
- **Office Professionnel Plus 2013, Office 365 ProPlus, version autonome d’Excel 2013 ou Office 2010 Professional Plus**.


## <a name="install-power-query"></a>Installation de Power Query

Power Query peut être utilisé pour importer des données à partir d’une variété de sources dans Microsoft Excel, où il peut faire fonctionner outils de décisionnel tels que PowerPivot et Power View. En particulier, Power Query peut importer des données qui a été sortie ou qui a été généré par un travail Hadoop exécuté sur un cluster HDInsight.

Télécharger Microsoft Power Query pour Excel à partir du [Centre de téléchargement Microsoft] [ powerquery-download] et installez-le.

## <a name="import-hdinsight-data-into-excel"></a>Importer des données HDInsight dans Excel

Le complément Power Query pour Excel facilite importer des données à partir de votre cluster HDInsight dans Excel, où outils de décisionnel tels que PowerPivot et Power Map peuvent servir à inspecter, analyser et présentent les données.

**Pour importer des données à partir d’un cluster HDInsight**

1. Ouvrez Excel.

2. Créer un nouveau classeur vide.

3. Cliquez sur le menu de **Power Query** , cliquez sur **à partir d’Azure**, puis cliquez sur **à partir de Microsoft Azure HDInsight**.

    ![HDI. PowerQuery.SelectHdiSource][image-hdi-powerquery-hdi-source]

    **Remarque :** Si vous ne voyez pas le menu de **Power Query** , accédez au **fichier** > **Options** > **Compléments**et sélectionnez **compléments COM** à partir de la zone **Gérer** de liste déroulante dans la partie inférieure de la page. Sélectionnez le bouton **Aller...** , vérifiez que la zone de la requête Power pour le complément Excel a été activée.

    **Remarque :** Power Query permet d’importer des données d’HADOOP en cliquant sur **à partir d’autres Sources**.

3. **Nom du compte**, entrez le nom du compte de stockage Blob Azure associé à votre cluster, puis cliquez sur **OK**. Cela peut être le [compte de stockage par défaut](hdinsight-administer-use-management-portal.md#find-the-default-storage-account) ou un compte de stockage liées.  Le format est *https://<StorageAccountName>.blob.core.windows.net/*.

4. Pour la **Clé de compte**, entrez la clé pour le compte de stockage Blob, puis cliquez sur **Enregistrer**. (Vous devez faire ceci uniquement la première fois que vous accédez à cette banque d’informations).

5. Dans le volet de **navigation** gauche de l’éditeur de requête, double-cliquez sur le nom du conteneur stockage Blob. Par défaut, le nom du conteneur est le même nom que le nom du cluster.

6. Recherchez **HiveSampleData.txt** dans la colonne **nom** (le chemin du dossier est **... / ruche/entrepôt/hivesampletable/**), puis cliquez sur **binaire** à gauche de HiveSampleData.txt. HiveSampleData.txt est fourni avec tout le cluster. Si vous le souhaitez, vous pouvez utiliser votre propre fichier.

    ![HDI. PowerQuery.ImportData][image-hdi-powerquery-importdata]

7. Si vous le souhaitez, vous pouvez renommer les noms de colonne. Lorsque vous êtes prêt, cliquez sur **Fermer et charger**.  Les données ont été chargées à votre classeur :

    ![HDI. PowerQuery.ImportedTable][image-hdi-powerquery-imported-table]

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris comment utiliser Power Query pour extraire des données à partir de HDInsight dans Excel. De même, vous pouvez récupérer des données à partir de HDInsight dans la base de données SQL Azure. Il est également possible de charger les données dans HDInsight. Pour plus d’informations, consultez les articles suivants :

* [Se connecter Excel à HDInsight avec le pilote ODBC Microsoft Hive][hdinsight-ODBC]
* [Télécharger des données à HDInsight][hdinsight-upload-data]

[hdinsight-ODBC]: hdinsight-connect-excel-hive-odbc-driver.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-upload-data]: hdinsight-upload-data.md

[image-hdi-powerquery-hdi-source]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.SelectHdiSource.png
[image-hdi-powerquery-importdata]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.ImportData.png
[image-hdi-powerquery-imported-table]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.ImportedTable.PNG

[powerquery-download]: http://go.microsoft.com/fwlink/?LinkID=286689
