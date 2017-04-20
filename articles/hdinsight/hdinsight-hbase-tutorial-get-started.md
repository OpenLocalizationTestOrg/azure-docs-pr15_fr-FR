<properties
    pageTitle="Didacticiel HBase : prise en main HBase dans Hadoop | Microsoft Azure"
    description="Suivez ce didacticiel HBase pour commencer à utiliser HBase Apache avec Hadoop dans HDInsight. Créer des tables à partir du shell HBase et les interroger à l’aide de Hive."
    keywords="Apache hbase, hbase, hbase shell, didacticiel hbase"
    services="hdinsight"
    documentationCenter=""
    authors="mumian"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/21/2016"
    ms.author="jgao"/>



# <a name="hbase-tutorial-get-started-using-apache-hbase-with-windows-based-hadoop-in-hdinsight"></a>Didacticiel HBase : commencer à utiliser HBase Apache avec Hadoop fonctionnant sous Windows dans HDInsight

[AZURE.INCLUDE [hbase-selector](../../includes/hdinsight-hbase-selector.md)]

Apprenez à créer des clusters HBase dans HDInsight, créer des tables HBase et les tables de requête à l’aide de la ruche Apache. Pour des informations générales HBase, voir [vue d’ensemble de HDInsight HBase][hdinsight-hbase-overview].

Les informations de ce document sont spécifiques aux clusters HDInsight fonctionnant sous Windows. Pour plus d’informations sur les clusters fonctionnant sous Windows, utilisez le sélecteur d’onglet en haut de la page pour basculer.

> [AZURE.NOTE] HBase (version 0.98.0) sur HDInsight fonctionnant sous Windows n’est disponible que pour une utilisation avec des clusters HDInsight 3.1 (en se basant sur Apache Hadoop et fils 2.4.0). Pour plus d’informations sur la version, consultez [Quelles sont les nouveautés dans les versions de cluster Hadoop fournies par HDInsight ?][hdinsight-versions]

## <a name="before-you-begin"></a>Avant de commencer

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Avant de commencer ce didacticiel HBase, vous devez disposer des éléments suivants :

- **Abonnement A Microsoft Azure**. Voir [Azure obtenir la version d’évaluation gratuite](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **Un poste de travail** avec Visual Studio 2013 ou version ultérieure : pour plus d’informations, voir [Installer Visual Studio](http://msdn.microsoft.com/library/e2h7fzkw.aspx).

### <a name="access-control-requirements"></a>Exigences de contrôle d’accès

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-hbase-cluster"></a>Créer HBase cluster

[AZURE.INCLUDE [provisioningnote](../../includes/hdinsight-provisioning.md)]

**Créer un cluster HBase à l’aide du portail Azure**

1. Connectez-vous au [portail Azure][azure-management-portal].
2. Cliquez sur **Nouveau** ou **+** dans le coin supérieur gauche d’angle, puis cliquez sur **données + Analytique**, **HDInsight**.
3. Entrez les valeurs suivantes :

    - **Nom de cluster** : entrez un nom pour identifier ce cluster.
    - **Type de cluster** - Select **HBase**.
    - **Système d’exploitation cluster** - Select **Windows**.  Pour la création de HBase basé sur Linux cluster, voir [HBase didacticiel : commencer à utiliser HBase Apache avec Hadoop dans HDInsight (Linux)](hdinsight-hbase-tutorial-get-started-linux.md).
    - **Version** : sélectionnez une version HBase.
    - **Abonnement** , sélectionnez votre abonnement Azure utilisé pour la création de ce groupe.
    - **Groupe de ressources** - créer un nouveau groupe de ressources Azure ou sélectionnez-en un. Pour plus d’informations, voir [Vue d’ensemble du Gestionnaire de ressources Azure](azure-resource-manager/resource-group-overview.md)
    - **Informations d’identification** - cluster basés sur Windows, vous pouvez créer un utilisateur cluster (utilisateur connu HTTP, utilisateur du service web HTTP) et un utilisateur de bureau à distance. Cliquez sur **Activer le Bureau à distance** pour ajouter les informations d’identification utilisateur de bureau à distance. La section suivante requiert RDP.
    - La **Source de données** - créer un nouveau compte de stockage Azure ou sélectionnez un compte de stockage Azure existant à utiliser comme le système de fichiers par défaut pour le cluster. L’emplacement par défaut du compte de stockage détermine l’emplacement de l’emplacement de cluster. Le compte de stockage par défaut et le cluster doivent localiser co-création dans le même centre de données.
    - **Niveaux de prix nœud** - sélectionnez le nombre de serveurs de la région pour le cluster HBase

        > [AZURE.WARNING] Disponibilité des services HBase, vous devez créer un cluster qui contient au moins **trois** nœuds. Cela garantit que, si un nœud s’arrête, les régions de données HBase sont disponibles sur les autres nœuds.

        > Si vous apprenez HBase, toujours choisissez 1 pour la taille de cluster, puis supprimer le cluster après chaque utilisation pour réduire les coûts.

    - **Configuration facultatif** : Azure configurer réseau virtuel, et la configuration des actions de Script et ajouter des comptes d’espace de stockage supplémentaire.

4. Cliquez sur **créer**.

>[AZURE.NOTE] Une fois un cluster HBase est supprimé, vous pouvez créer un autre cluster HBase en utilisant le même compte de stockage par défaut et le conteneur blob par défaut. Le nouveau cluster chercher les tables HBase que vous avez créé dans le cluster d’origine. Pour éviter les incohérences, nous vous recommandons de désactiver les tables HBase avant de supprimer le cluster.

## <a name="create-tables-and-insert-data"></a>Créer des tables et insérer des données

Pour l’instant, il existe deux facteurs pour accéder aux HBase. Cette section décrit l’utilisation de l’interface HBase. La section suivante décrit l’utilisation du Kit de développement .NET.

Pour la plupart des personnes, données apparaissent sous forme de tableau :

![données tabulaires hdinsight hbase][img-hbase-sample-data-tabular]

Dans HBase qui est une implémentation de BigTable, les mêmes données ressemble à :

![hdinsight hbase bigtable données][img-hbase-sample-data-bigtable]

Cela vous fera préférable lorsque vous avez terminé la procédure suivante.  

**Utilisation d’HBase shell**

1. Utiliser RDP pour vous connecter à votre cluster HBase dans HDInsight. Pour les instructions RDP, voir [Hadoop gérer les groupes dans un HDInsight à l’aide du portail Azure][hdinsight-manage-portal].
2. Au sein de votre session RDP, cliquez sur le raccourci de **ligne de commande Hadoop** situé sur le bureau.
3. Ouvrez le shell HBase :

        cd %HBASE_HOME%\bin
        hbase shell

4. Créer un HBase avec deux familles de colonne :

        create 'Contacts', 'Personal', 'Office'
        list
5. Insérer des données :

        put 'Contacts', '1000', 'Personal:Name', 'John Dole'
        put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
        put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
        put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
        scan 'Contacts'

    ![hdinsight hadoop hbase shell][img-hbase-shell]

6. Obtenir une seule ligne

        get 'Contacts', '1000'

    Vous verrez le même résultat que l’utilisation de la commande analyse, car il existe une seule ligne.

    Pour plus d’informations sur le schéma de table Hbase, voir [Introduction à la création de schéma HBase][hbase-schema]. Pour plus de commandes HBase, voir le [guide de référence Apache HBase][hbase-quick-start].


6. Quitter le shell

        exit

**Pour en bloc charger les données dans la table HBase de contacts**

HBase inclut plusieurs méthodes de chargement des données dans des tables. Pour plus d’informations, voir [chargement en bloc](http://hbase.apache.org/book.html#arch.bulk.load).


Un exemple de fichier de données a été téléchargé dans un conteneur blob publique, wasbs://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt. Le contenu du fichier de données est la suivante :

    8396    Calvin Raji     230-555-0191    230-555-0191    5415 San Gabriel Dr.
    16600   Karen Wu        646-555-0113    230-555-0192    9265 La Paz
    4324    Karl Xie        508-555-0163    230-555-0193    4912 La Vuelta
    16891   Jonn Jackson    674-555-0110    230-555-0194    40 Ellis St.
    3273    Miguel Miller   397-555-0155    230-555-0195    6696 Anchor Drive
    3588    Osa Agbonile    592-555-0152    230-555-0196    1873 Lion Circle
    10272   Julia Lee       870-555-0110    230-555-0197    3148 Rose Street
    4868    Jose Hayes      599-555-0171    230-555-0198    793 Crawford Street
    4761    Caleb Alexander 670-555-0141    230-555-0199    4775 Kentucky Dr.
    16443   Terry Chander   998-555-0171    230-555-0200    771 Northridge Drive

Vous pouvez créer un fichier texte et téléchargez le fichier à votre compte de stockage si vous voulez. Pour obtenir des instructions, voir [télécharger des données pour les projets Hadoop de HDInsight][hdinsight-upload-data].

> [AZURE.NOTE] Cette procédure utilise la table Contacts HBase que vous avez créé dans la dernière procédure.

1. Au sein de votre session RDP, cliquez sur le raccourci de **ligne de commande Hadoop** situé sur le bureau.
2. Changer le répertoire :

        cd %HBASE_HOME%\bin

3. Exécutez la commande suivante pour transformer le fichier de données StoreFiles et la banque d’informations à un chemin d’accès relatif spécifié par Dimporttsv.bulk.output :

        hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name, Personal:Phone, Office:Phone, Office:Address" -Dimporttsv.bulk.output="/example/data/storeDataFileOutput" Contacts wasbs://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt

4. Exécutez la commande suivante pour charger les données à partir de /example/data/storeDataFileOutput à la table HBase :

        hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /example/data/storeDataFileOutput Contacts

5. Vous pouvez ouvrir le shell HBase et utilisez la commande analyse pour indiquer le contenu de la table.



## <a name="use-hive-to-query-hbase-tables"></a>Utiliser Hive pour les tables de requête HBase

Vous pouvez interroger des données stockées dans HBase à l’aide de Hive. Cette section crée une table Hive qui correspond à la table HBase et utilise pour interroger les données dans votre table HBase.

**Pour ouvrir le tableau de bord cluster**

1. Accédez à **https://<HDInsight Cluster Name>.azurehdinsight.net/**.
5. Entrez le nom d’utilisateur Hadoop compte et le mot de passe. Le nom d’utilisateur par défaut est **admin** et le mot de passe est ce que vous avez entré pendant le processus de création. Un nouvel onglet de navigateur s’ouvre.
6. Cliquez sur **Éditeur ruche** en haut de la page. L’éditeur ruche ressemble à ceci :

    ![Tableau de bord cluster HDInsight.][img-hdinsight-hbase-hive-editor]

**Pour exécuter des requêtes Hive**

1. Entrez le script HiveQL suivant dans l’éditeur de ruche et cliquez sur **Envoyer** pour créer une Table ruche qui correspond à la table HBase. Vérifiez que vous avez créé l’exemple de tableau référencée précédemment dans ce didacticiel à l’aide du shell HBase avant d’exécuter cette déclaration.

        CREATE EXTERNAL TABLE hbasecontacts(rowkey STRING, name STRING, homephone STRING, officephone STRING, officeaddress STRING)
        STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
        WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,Personal:Name,Personal:Phone,Office:Phone,Office:Address')
        TBLPROPERTIES ('hbase.table.name' = 'Contacts');

    Patientez jusqu'à ce que les mises à jour de **statut** **terminé**.

2. Entrez le script HiveQL suivant dans l’éditeur de la ruche, puis cliquez sur **Envoyer**. La requête Hive requêtes les données dans la table HBase :

        SELECT count(*) FROM hbasecontacts;

4. Pour extraire les résultats de la requête Hive, cliquez sur le lien **Afficher les détails** dans la fenêtre de **Session de travail** lorsque la tâche termine en cours d’exécution. Il sera qu’un seul fichier de copie de travail, car vous stockez un enregistrement dans la table HBase.




**Pour rechercher le fichier de copie**

1. Dans la Console de requête, cliquez sur **Explorateur de fichiers**.
2. Cliquez sur le compte de stockage Azure qui est utilisé comme le système de fichiers par défaut pour le cluster HBase.
3. Cliquez sur le nom du cluster HBase. Le conteneur de compte de stockage Azure par défaut utilise le nom du cluster.
4. Cliquez sur **l’utilisateur**, puis cliquez sur **administrateur**. (Cela est le nom d’utilisateur Hadoop.)
6. Cliquez sur le nom du travail avec l’heure **De dernière modification** qui correspond à la fois quand la requête sélectionnez ruche s’est exécutée.
4. Cliquez sur **stdout**. Enregistrez le fichier et ouvrez le fichier avec le bloc-notes. Il y a un fichier de sortie.

    ![Explorateur de fichiers HDInsight HBase Hive éditeur][img-hdinsight-hbase-file-browser]

## <a name="use-the-net-hbase-rest-api-client-library"></a>Utilisation de la bibliothèque de client de l’API REST .NET HBase

Vous devez télécharger la bibliothèque de client de l’API REST HBase pour .NET de GitHub et générer le projet afin que vous pouvez utiliser le Kit de développement .NET HBase. La procédure suivante inclut les instructions pour cette tâche.

1. Créer une nouvelle application Visual Studio Windows Desktop Console c#.
2. Ouvrez la Console Gestionnaire de Package NuGet en cliquant sur **Outils** > **Gestionnaire de Package NuGet** > **Console du Gestionnaire de Package**.
3. Exécutez la commande NuGet suivante dans la console :

        Install-Package Microsoft.HBase.Client

5. Ajoutez les instructions suivantes **à l’aide** en haut du fichier :

        using Microsoft.HBase.Client;
        using org.apache.hadoop.hbase.rest.protobuf.generated;

6. Remplacez la fonction **Main** avec les éléments suivants :

        static void Main(string[] args)
        {
            string clusterURL = "https://<yourHBaseClusterName>.azurehdinsight.net";
            string hadoopUsername= "<yourHadoopUsername>";
            string hadoopUserPassword = "<yourHadoopUserPassword>";

            string hbaseTableName = "sampleHbaseTable";

            // Create a new instance of an HBase client.
            ClusterCredentials creds = new ClusterCredentials(new Uri(clusterURL), hadoopUsername, hadoopUserPassword);
            HBaseClient hbaseClient = new HBaseClient(creds);

            // Retrieve the cluster version.
            var version = hbaseClient.GetVersion();
            Console.WriteLine("The HBase cluster version is " + version);

            // Create a new HBase table.
            TableSchema testTableSchema = new TableSchema();
            testTableSchema.name = hbaseTableName;
            testTableSchema.columns.Add(new ColumnSchema() { name = "d" });
            testTableSchema.columns.Add(new ColumnSchema() { name = "f" });
            hbaseClient.CreateTable(testTableSchema);

            // Insert data into the HBase table.
            string testKey = "content";
            string testValue = "the force is strong in this column";
            CellSet cellSet = new CellSet();
            CellSet.Row cellSetRow = new CellSet.Row { key = Encoding.UTF8.GetBytes(testKey) };
            cellSet.rows.Add(cellSetRow);

            Cell value = new Cell { column = Encoding.UTF8.GetBytes("d:starwars"), data = Encoding.UTF8.GetBytes(testValue) };
            cellSetRow.values.Add(value);
            hbaseClient.StoreCells(hbaseTableName, cellSet);

            // Retrieve a cell by its key.
            cellSet = hbaseClient.GetCells(hbaseTableName, testKey);
            Console.WriteLine("The data with the key '" + testKey + "' is: " + Encoding.UTF8.GetString(cellSet.rows[0].values[0].data));
            // with the previous insert, it should yield: "the force is strong in this column"

            //Scan over rows in a table. Assume the table has integer keys and you want data between keys 25 and 35.
            Scanner scanSettings = new Scanner()
            {
                batch = 10,
                startRow = BitConverter.GetBytes(25),
                endRow = BitConverter.GetBytes(35)
            };

            ScannerInformation scannerInfo = hbaseClient.CreateScanner(hbaseTableName, scanSettings);
            CellSet next = null;
            Console.WriteLine("Scan results");

            while ((next = hbaseClient.ScannerGetNext(scannerInfo)) != null)
            {
                foreach (CellSet.Row row in next.rows)
                {
                    Console.WriteLine(row.key + " : " + Encoding.UTF8.GetString(row.values[0].data));
                }
            }

            Console.WriteLine("Press ENTER to continue ...");
            Console.ReadLine();
        }

7. Définissez les trois premières variables dans la fonction **Main** .
8. Appuyez sur **F5** pour exécuter l’application.

## <a name="check-cluster-status"></a>Vérifier le statut de cluster

HBase dans HDInsight est fourni avec une interface utilisateur Web pour l’analyse des clusters. À l’aide de l’interface utilisateur Web, vous pouvez demander des statistiques ou des informations sur les zones.

Pour ouvrir l’interface utilisateur Web, vous devez RDP dans le cluster, puis cliquez sur le raccourci de l’interface utilisateur HMaster informations Web sur votre bureau ou utilisez l’URL suivante dans un navigateur web :

    http://zookeeper[0-2]:60010/master-status

Dans un cluster de disponibilité, vous trouverez un lien vers active HBase maître nœud actuel qui héberge l’interface utilisateur Web.

##<a name="delete-the-cluster"></a>Supprimer le cluster
Pour éviter les incohérences, nous vous recommandons de désactiver les tables HBase avant de supprimer le cluster.
[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


## <a name="whats-next"></a>Quoi d’autre ?
Dans ce didacticiel HBase pour HDInsight, vous avez appris comment créer un cluster HBase et comment créer des tables et afficher les données de ces tables à partir du shell HBase. Vous avez également appris comment utiliser une requête Hive avec des données dans des tables HBase et comment utiliser les HBase c# reste API pour créer une table HBase et extraire des données de la table.

Pour plus d’informations, voir :

- [Vue d’ensemble de HDInsight HBase][hdinsight-hbase-overview].
HBase est une base de données open source, Apache, NoSQL basé sur Hadoop qui fournit un accès aléatoire et la cohérence forte de grandes quantités de données structurées et semi-structurées.
- [Créer des clusters HBase sur réseau virtuel Azure][hdinsight-hbase-provision-vnet].
Avec l’intégration des réseaux virtuels, clusters HBase peuvent être déployés au même réseau virtuel en tant que vos applications afin que les applications puissent communiquer avec HBase directement.
- [Réplication HBase configurer dans HDInsight](hdinsight-hbase-geo-replication.md). Découvrez comment configurer la réplication HBase sur deux centres de données Azure.
- [Analyser opinion Twitter avec HBase dans HDInsight][hbase-twitter-sentiment].
Découvrez comment effectuer en temps réel [analyse opinion](http://en.wikipedia.org/wiki/Sentiment_analysis) de données volumineux à l’aide de HBase dans un cluster Hadoop dans HDInsight.

[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hbase-reference]: http://hbase.apache.org/book.html#importtsv
[hbase-schema]: http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf
[hbase-quick-start]: http://hbase.apache.org/book.html#quickstart





[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md
[hdinsight-versions]: hdinsight-component-versioning.md
[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://portal.azure.com/
[azure-create-storageaccount]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/

[img-hdinsight-hbase-cluster-quick-create]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-quick-create.png
[img-hdinsight-hbase-hive-editor]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-hive-editor.png
[img-hdinsight-hbase-file-browser]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-file-browser.png
[img-hbase-shell]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-shell.png
[img-hbase-sample-data-tabular]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-contacts-tabular.png
[img-hbase-sample-data-bigtable]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-contacts-bigtable.png
