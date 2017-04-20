<properties
   pageTitle="Se connecter Excel à avec le pilote ODBC Hive | Microsoft Azure"
   description="Découvrez comment configurer et utiliser le pilote ODBC ruche Microsoft pour Excel pour interroger les données dans un cluster HDInsight."
   services="hdinsight"
   documentationCenter=""
   authors="mumian"
   manager="jhubbard"
   tags="azure-portal"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/19/2016"
   ms.author="jgao"/>

#<a name="connect-excel-to-hadoop-with-the-microsoft-hive-odbc-driver"></a>Se connecter Excel à avec le pilote ODBC ruche Microsoft

[AZURE.INCLUDE [ODBC-JDBC-selector](../../includes/hdinsight-selector-odbc-jdbc.md)]

Solution Big Data de Microsoft intègre des composants Microsoft Business Intelligence (BI) clusters Apache Hadoop déployées par la Azure HDInsight. La possibilité de se connecter Excel à Hive data warehouse d’un cluster Hadoop dans HDInsight en utilisant le pilote Microsoft ruche connectivité ODBC (Open Database) est un exemple de cette intégration.

Il est également possible de connecter les données associées à un cluster de HDInsight et d’autres sources de données, y compris autre cluster Hadoop (non HDInsight), à partir d’Excel à l’aide du complément Microsoft Power Query pour Excel. Pour plus d’informations sur l’installation et à l’aide de Power Query, voir [Connecter Excel à HDInsight avec Power Query][hdinsight-power-query].

> [AZURE.NOTE] Si les étapes décrites dans cet article peuvent être utilisés avec cluster un Linux ou HDInsight fonctionnant sous Windows, Windows est requis pour le poste de travail client.

**Conditions préalables**:

Avant de commencer cet article, vous devez disposer des éléments suivants :

- **HDInsight un cluster**. Pour créer une, voir [prise en main Azure HDInsight][hdinsight-get-started].
- **Poste de travail A** avec Office Professionnel Plus 2013, Office 365 ProPlus, version autonome d’Excel 2013 ou Office 2010 Professionnel Plus.


##<a name="install-microsoft-hive-odbc-driver"></a>Installer le pilote ODBC ruche Microsoft

Téléchargez et installez Microsoft le pilote ODBC ruche à partir du [Centre de téléchargement][hive-odbc-driver-download].

Ce pilote peut être installé sur les versions 32 bits ou 64 bits de Windows 7, Windows 8, Windows 10, Windows Server 2008 R2 et Windows Server 2012 et permettra de connexion à Azure HDInsight (version 1,6 et versions ultérieure) et Azure HDInsight émulateur (v.1.0.0.0 et versions ultérieures). Vous devez installer la version qui correspond à la version de l’application dans laquelle vous allez utiliser le pilote ODBC. Pour ce didacticiel, le pilote est utilisé à partir d’Office Excel.

##<a name="create-hive-odbc-data-source"></a>Créer la source de données ODBC ruche

Les étapes suivantes montrent comment créer une Source de données ODBC ruche.

1. À partir de Windows 8 ou Windows 10, appuyez sur la touche Windows pour ouvrir l’écran d’accueil et tapez **les sources de données**.
2. Cliquez sur **configurer des données ODBC sources (32 bits)** ou **configurer des Sources de données ODBC (64 bits)** selon votre version d’Office. Si vous utilisez Windows 7, cliquez sur **Sources de données ODBC (32 bits)** ou des **Sources de données ODBC (64 bits)** dans **Les outils d’administration**. Cette action lance la boîte de dialogue **Administrateur de sources de données ODBC** .

    ![Administrateur de source de données ODBC][img-hdi-simbahiveodbc-datasource-admin]

3. À partir de l’utilisateur DNS, cliquez sur **Ajouter** pour ouvrir l’Assistant **Créer une nouvelle Source de données** .
4. Sélectionnez **Microsoft le pilote ODBC ruche**, puis cliquez sur **Terminer**. Cette action lance la boîte de dialogue **Microsoft ruche DNS configuration du pilote ODBC** .

5. Tapez ou sélectionnez les valeurs suivantes :

    Propriété|Description
    ---|---
    Nom Source de données|Donner un nom à votre source de données
    Hôte|Entrez &lt;HDInsightClusterName >. azurehdinsight.net. Par exemple, myHDICluster.azurehdinsight.net
    Port|Utilisez <strong>443</strong>. (Ce port ont été modifié à partir de 563 443.)
    Base de données|Utiliser <strong>par défaut</strong>.
    Type de serveur Hive|Sélectionnez <strong>la ruche Server 2</strong>
    Mécanisme|Sélectionnez <strong>Azure HDInsight Service</strong>
    Chemin d’accès HTTP|Laissez ce champ vide.
    Nom d’utilisateur|Entrez le nom d’utilisateur HDInsight cluster. Il s’agit du nom d’utilisateur créé lors du processus de mise en service cluster. Si vous avez utilisé l’option Créer rapide, le nom d’utilisateur par défaut est <strong>admin</strong>.
    Mot de passe|Entrez le mot de passe utilisateur HDInsight cluster.
    </table>

    Il existe certains paramètres importants importants lorsque vous cliquez sur **Options avancées**:

    Paramètre|Description
    ---|---
    Utiliser la requête Native|Lorsqu’elle est sélectionnée, le pilote ODBC essaiera pas convertir TSQL HiveQL. Vous doit utiliser uniquement si vous êtes que vous soumettez instructions HiveQL intégral à 100 %. Lorsque vous vous connectez à SQL Server ou de base de données SQL Azure, vous devez laisser désactivée.
    Lignes lues par bloc|Lors de la lecture d’une grande quantité d’enregistrements, réglage ce paramètre pourrez être amené à garantir des performances optimales.
    Longueur de colonne par défaut de la chaîne, longueur de la colonne binaire, échelle d’une colonne décimale|Le type de données longueurs et précisions peuvent affecter la façon dont les données sont renvoyées. Ils entraînera des informations incorrectes à retourner en raison de perte de précision et/ou troncature.


    ![Options avancées][img-HiveOdbc-DataSource-AdvancedOptions]

6. Cliquez sur **tester** pour tester la source de données. Lorsque la source de données est correctement configurée, il affiche *TESTS s’est terminée avec succès !*.
7. Cliquez sur **OK** pour fermer la boîte de dialogue de Test. La nouvelle source de données doit maintenant être affichée dans l' **Administrateur de sources de données ODBC**.
8. Cliquez sur **OK** pour quitter l’Assistant.

##<a name="import-data-into-excel-from-hdinsight"></a>Importer des données dans Excel à partir de HDInsight

Les étapes suivantes décrivent la façon d’importer des données à partir d’une table hive dans un classeur Excel à l’aide de la source de données ODBC que vous avez créé dans les étapes ci-dessus.

1. Ouvrez un classeur existant ou nouveau dans Excel.
2. Sous l’onglet **données** , cliquez sur **à partir d’autres Sources de données**, puis cliquez sur **Provenance : Assistant connexion de données** pour lancer l' **Assistant connexion de données**.

    ![Assistant connexion de données ouverte][img-hdi-simbahiveodbc.excel.dataconnection]

3. Sélectionnez **DSN ODBC** comme source de données, puis cliquez sur **suivant**.
4. À partir de sources de données ODBC, sélectionnez le nom de source de données que vous avez créé à l’étape précédente, puis cliquez sur **suivant**.
5. Retapez le mot de passe pour le cluster dans l’Assistant, puis cliquez sur **Test** pour vérifier la configuration de nouveau, si nécessaire.
6. Cliquez sur **OK** pour fermer la boîte de dialogue de test.
7. Cliquez sur **OK**. Attendez que la boîte de dialogue **Sélectionner une base de données et de Table** pour l’ouvrir. Cela peut prendre quelques secondes.
8. Sélectionnez la table que vous voulez importer, puis cliquez sur **suivant**. Le *hivesampletable* est un exemple de table hive fournie avec clusters HDInsight.  Vous pouvez choisir si vous n’avez pas créé un. Pour plus d’informations sur Exécuter ruche requêtes et créer des tables Hive, voir [Utiliser la ruche avec HDInsight][hdinsight-use-hive].
8. Cliquez sur **Terminer**.
9. Dans la boîte de dialogue **Importer des données** , vous pouvez modifier ou spécifier la requête. Pour ce faire, cliquez sur **Propriétés**. Cela peut prendre quelques secondes.
10. Cliquez sur l’onglet **définition** , puis ajoutez **200 limite** à l’instruction select Hive dans la zone de **texte de la commande** texte. La modification limite le jeu d’enregistrements retourné 200.

    ![Propriétés de connexion][img-hdi-simbahiveodbc-excel-connectionproperties]

11. Cliquez sur **OK** pour fermer la boîte de dialogue Propriétés de connexion.
12. Cliquez sur **OK** pour fermer la boîte de dialogue **Importer des données** .  
13. Retapez le mot de passe, puis cliquez sur **OK**. Il faut quelques secondes avant de données obtient importées dans Excel.

##<a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris comment utiliser le pilote ODBC ruche Microsoft pour récupérer des données à partir de HDInsight Service dans Excel. De même, vous pouvez récupérer des données à partir de HDInsight Service dans la base de données SQL. Il est également possible de charger les données dans un HDInsight Service. Pour plus d’informations, voir :

- [Analyser des données de retard aérienne à l’aide de HDInsight][hdinsight-analyze-flight-data]
- [Télécharger des données à HDInsight][hdinsight-upload-data]
- [Utiliser Sqoop avec HDInsight] [hdinsight-use-sqoop]


[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md
[hdinsight-get-started]: hdinsight-hadoop-tutorial-get-started-windows.md

[hive-odbc-driver-download]: http://go.microsoft.com/fwlink/?LinkID=286698

[img-hdi-simbahiveodbc-datasource-admin]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.SimbaHiveOdbc.DataSourceAdmin1.png
[img-HiveOdbc-DataSource-AdvancedOptions]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.HiveOdbc.DataSource.AdvancedOptions1.png
[img-hdi-simbahiveodbc-excel-connectionproperties]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.SimbaHiveODBC.Excel.ConnectionProperties1.png
[img-hdi-simbahiveodbc.excel.dataconnection]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.SimbaHiveOdbc.Excel.DataConnection1.png
