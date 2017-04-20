<properties
    pageTitle="Utiliser Hadoop Sqoop dans basé sur Linux HDInsight | Microsoft Azure"
    description="Apprenez à exécuter Sqoop d’importation et exportation entre un Hadoop basé sur Linux sur cluster HDInsight et une base de données SQL Azure."
    editor="cgronlun"
    manager="jhubbard"
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="larryfr"/>

#<a name="use-sqoop-with-hadoop-in-hdinsight-ssh"></a>Utiliser Sqoop avec Hadoop dans HDInsight (SSH)

[AZURE.INCLUDE [sqoop-selector](../../includes/hdinsight-selector-use-sqoop.md)]

Découvrez comment utiliser Sqoop pour importer et exporter entre un cluster basé sur Linux HDInsight et la base de données de base de données SQL Azure ou SQL Server.

> [AZURE.NOTE] Les étapes décrites dans cet article utilisent SSH pour vous connecter à un cluster basé sur Linux HDInsight. Les clients Windows permet également Azure PowerShell et .NET HDInsight SDK pour travailler avec Sqoop sur clusters basés sur Linux. Utilisez le sélecteur de tabulation pour ouvrir ces articles.

##<a name="prerequisites"></a>Conditions préalables

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- **Hadoop A cluster dans HDInsight** et une __Base de données SQL Azure__: les étapes décrites dans ce document sont basés sur le cluster et la base de données créé à l’aide du document de [créer un cluster et base de données SQL](hdinsight-use-sqoop.md#create-cluster-and-sql-database) . Si vous avez déjà un cluster HDInsight et la base de données SQL, vous pouvez remplacer ceux pour les valeurs utilisées dans ce document.
- **Poste de travail**: un ordinateur sur lequel un clientSSH.

##<a name="install-freetds"></a>Installer FreeTDS

1. Utiliser le protocole SSH pour vous connecter au cluster HDInsight basé sur Linux. L’adresse à utiliser lors de la connexion est `CLUSTERNAME-ssh.azurehdinsight.net` et le port est `22`.

    Pour plus d’informations sur l’utilisation de SSH pour se connecter à HDInsight, voir les documents suivants :

    * **Les clients Linux, Unix ou OS X**: voir [se connecter à un cluster basé sur Linux HDInsight de Linux, OS X ou Unix](hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-linux-based-hdinsight-cluster)

    * **Les clients Windows**: voir [se connecter à un cluster basé sur Linux HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md#connect-to-a-linux-based-hdinsight-cluster)

3. Utilisez la commande suivante pour installer FreeTDS :

        sudo apt-get --assume-yes install freetds-dev freetds-bin

    FreeTDS seront utilisées en plusieurs étapes pour vous connecter à la base de données SQL.

##<a name="create-the-table-in-sql-database"></a>Créer une table de base de données SQL

> [AZURE.IMPORTANT] Si vous utilisez un cluster de HDInsight et base de données SQL créés à l’aide de la procédure dans [créer un cluster et base de données SQL](hdinsight-use-sqoop.md), ignorez les étapes décrites dans cette section comme la base de données et la table ont été créés dans le cadre de la procédure décrite dans ce document.

1. À partir de la connexion SSH à HDInsight, utilisez la commande suivante pour vous connecter au serveur de base de données SQL et Crète la table qui sera utilisée dans le reste des étapes suivantes :

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D sqooptest

    Vous recevrez une sortie similaire à ce qui suit :

        locale is "en_US.UTF-8"
        locale charset is "UTF-8"
        using default charset "UTF-8"
        Default database being set to sqooptest
        1>

5. À la `1>` invite, entrez les lignes suivantes :

        CREATE TABLE [dbo].[mobiledata](
        [clientid] [nvarchar](50),
        [querytime] [nvarchar](50),
        [market] [nvarchar](50),
        [deviceplatform] [nvarchar](50),
        [devicemake] [nvarchar](50),
        [devicemodel] [nvarchar](50),
        [state] [nvarchar](50),
        [country] [nvarchar](50),
        [querydwelltime] [float],
        [sessionid] [bigint],
        [sessionpagevieworder] [bigint])
        GO
        CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(clientid)
        GO

    Lorsque la `GO` instruction est entrée, les instructions précédentes seront évaluées. Tout d’abord, la table **mobiledata** est créée, puis un index cluster est ajouté à celle-ci (requis par base de données SQL).

    Pour vérifier que la table a été créée, utilisez ce qui suit :

        SELECT * FROM information_schema.tables
        GO

    Vous devriez voir sortie similaire à ce qui suit :

        TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
        sqooptest       dbo     mobiledata      BASE TABLE

8. Entrez `exit` à la `1>` invite pour quitter l’utilitaire tsql.

##<a name="sqoop-export"></a>Exportation de Sqoop

3. À partir de la connexion SSH à HDInsight, se la commande suivante pour vérifier que Sqoop peuvent voir votre base de données SQL :

        sqoop list-databases --connect jdbc:sqlserver://<serverName>.database.windows.net:1433 --username <adminLogin> --password <adminPassword>

    Cela doit renvoyer une liste des bases de données, y compris la base de données **sqooptest** que vous avez créée.

4. Pour exporter des données à partir de **hivesampletable** à la table **mobiledata** , utilisez la commande suivante :

        sqoop export --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --export-dir 'wasbs:///hive/warehouse/hivesampletable' --fields-terminated-by '\t' -m 1

    Cela indique Sqoop à se connecter à la base de données SQL, à la base de données **sqooptest** et exporter des données à partir de la **wasbs : / / / hive/entrepôt/hivesampletable** (fichiers physiques pour la *hivesampletable*,) à la table **mobiledata** .

5. Une fois la commande terminée, utilisez ce qui suit pour vous connecter à la base de données à l’aide de TSQL :

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D sqooptest

    Une fois connecté, utilisez les instructions suivantes pour vérifier que les données ont été exportées vers la table **mobiledata** :

        SELECT * FROM mobiledata
        GO

    Vous devriez voir une liste de données dans la table. Type de `exit` pour quitter l’utilitaire tsql.

##<a name="sqoop-import"></a>Importation Sqoop

1. Utilisez ce qui suit pour importer des données à partir de la table **mobiledata** dans la base de données SQL, à la **wasbs : / / / didacticiels/usesqoop/importeddata** répertoire HDInsight :

        sqoop import --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --target-dir 'wasbs:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1

    Les données importées comportera champs séparés par un caractère de tabulation et les lignes vont être fermées par un caractère de saut de ligne.

2. Une fois l’importation terminée, utilisez la commande suivante pour liste rejeté les données dans le nouveau répertoire :

        hadoop fs -text wasbs:///tutorials/usesqoop/importeddata/part-m-00000

##<a name="using-sql-server"></a>Utilisation de SQL Server

Vous pouvez également utiliser Sqoop pour importer et exporter des données à partir de SQL Server, dans votre centre de données ou sur un ordinateur virtuel hébergé dans Azure. Les différences entre l’utilisation de la base de données SQL et SQL Server sont :

* HDInsight et SQL Server doivent se trouver sur le même réseau virtuel Azure

    > [AZURE.NOTE] HDInsight prend en charge uniquement les réseaux virtuels basés sur un emplacement, et elle ne fonctionne pas avec réseaux virtuels affinité du groupe.

    Lorsque vous utilisez SQL Server dans votre centre de données, vous devez configurer le réseau virtuel comme *site à* ou *point-à-site*.

    > [AZURE.NOTE] Pour les réseaux virtuels **point-à-site** , SQL Server doit exécuter le client VPN application de configuration, qui est disponible à partir du **tableau de bord** de votre configuration réseau virtuel Azure.

    Pour plus d’informations réseau virtuel Azure, voir [Vue d’ensemble du réseau virtuel](../virtual-network/virtual-networks-overview.md).

* SQL Server doit être configuré pour autoriser l’authentification SQL. Pour plus d’informations, voir [Choisir un Mode d’authentification](https://msdn.microsoft.com/ms144284.aspx)

* Vous devrez peut-être configurer SQL Server pour accepter les connexions à distance. Découvrez [comment résoudre les problèmes de connexion au moteur de base de données SQL Server](http://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx) pour plus d’informations

* Vous devez créer la base de données **sqooptest** dans SQL Server à l’aide d’un utilitaire tel que **SQL Server Management Studio** ou **tsql** - la procédure d’utilisation de l’infrastructure du langage commun Azure fonctionne uniquement pour la base de données SQL Azure

    Les instructions TSQL pour créer la table **mobiledata** sont semblables ceux qui sont utilisés pour la base de données SQL, à l’exception de la création d’un clusterd index - Ceci n’est pas nécessaire pour SQL Server :

        CREATE TABLE [dbo].[mobiledata](
        [clientid] [nvarchar](50),
        [querytime] [nvarchar](50),
        [market] [nvarchar](50),
        [deviceplatform] [nvarchar](50),
        [devicemake] [nvarchar](50),
        [devicemodel] [nvarchar](50),
        [state] [nvarchar](50),
        [country] [nvarchar](50),
        [querydwelltime] [float],
        [sessionid] [bigint],
        [sessionpagevieworder] [bigint])

* Lorsque vous vous connectez à SQL Server à partir de HDInsight, vous devrez peut-être utiliser l’adresse IP de SQL Server, sauf si vous avez configuré un système DNS (Domain Name System) pour résoudre les noms sur le réseau virtuel Azure. Par exemple :

        sqoop import --connect 'jdbc:sqlserver://10.0.1.1:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --target-dir 'wasbs:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1

##<a name="limitations"></a>Limitations

* Exportation en bloc - basé sur Linux avec HDInsight, le connecteur Sqoop permet d’exporter des données vers Microsoft SQL Server ou de base de données SQL Azure ne prend pas en charge les insertions en bloc.

* Le traitement par lots - avec basé sur Linux HDInsight, lorsque vous utilisez la `-batch` commutateur lors de l’exécution d’insertions, Sqoop effectue plusieurs insertions au lieu du traitement par lots les opérations d’insertion.

##<a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à utiliser Sqoop. Pour plus d’informations, voir :

- [Utiliser Oozie avec HDInsight][hdinsight-use-oozie]: action Sqoop utilisation dans un flux de travail Oozie.
- [Analyser des données de retard aérienne à l’aide de HDInsight][hdinsight-analyze-flight-data]: utiliser la ruche pour analyser aérienne retarder des données et ensuite utiliser Sqoop pour exporter des données dans une base de données SQL Azure.
- [Télécharger des données à HDInsight][hdinsight-upload-data]: trouver d’autres méthodes pour le téléchargement des données à HDInsight/Azure Blob storage.



[hdinsight-versions]:  hdinsight-component-versioning.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md

[sqldatabase-get-started]: ../sql-database-get-started.md
[sqldatabase-create-configue]: ../sql-database-create-configure.md

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: powershell-install-configure.md
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html
