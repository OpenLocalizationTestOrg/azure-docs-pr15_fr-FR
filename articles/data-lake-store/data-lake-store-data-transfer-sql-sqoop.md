<properties 
   pageTitle="Copier des données entre des données Lake Store et base de données SQL Azure avec Sqoop | Microsoft Azure"
   description="Permet de copier des données entre la base de données SQL Azure et données Lake Store Sqoop" 
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

# <a name="copy-data-between-data-lake-store-and-azure-sql-database-using-sqoop"></a>Copier des données entre des données Lake Store et à l’aide de Sqoop de la base de données SQL Azure

Découvrez comment utiliser Apache Sqoop pour importer et exporter des données entre la base de données SQL Azure et données Lake Store.
 

## <a name="what-is-sqoop"></a>Quelles sont les Sqoop ?

Applications de données long sont adaptées à des données structurées et semi-structurées, tels que les journaux et les fichiers de traitement. Toutefois, il peut également être nécessaire pour traiter les données structurées qui sont stockées dans des bases de données relationnelles.

[Apache Sqoop](https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html) est un outil conçu pour transférer des données entre les bases de données relationnelles et un référentiel de données volumineux, tels que des données Lake Store. Vous pouvez l’utiliser pour importer des données à partir d’un système de gestion de base de données relationnelle (SGBDR) comme base de données SQL Azure dans données Lake magasin. Vous pouvez ensuite transformer et analyser les données à l’aide de charges de travail de données volumineux et puis exporter les données dans un SGBDR. Dans ce didacticiel, vous utilisez une base de données SQL Azure en tant que votre base de données relationnelle pour importer/exporter à partir de.
 

## <a name="prerequisites"></a>Conditions préalables

Avant de commencer cet article, vous devez disposer des éléments suivants :

- **Azure un abonnement**. Voir [Azure obtenir la version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/).
- **Activer votre abonnement Azure** pour la présentation publique de données Lake Store. Consultez les [instructions](data-lake-store-get-started-portal.md#signup). 
- **Cluster azure HDInsight** avec l’accès à un compte de données Lake Store. Voir [créer un cluster HDInsight avec données Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md). Cet article suppose que vous disposez d’un cluster HDInsight Linux avec accès au magasin Lake de données.
- **Base de données SQL azure**. Pour savoir comment en créer une, voir [créer une base de données SQL Azure](../sql-database/sql-database-get-started.md)

## <a name="do-you-learn-fast-with-videos"></a>Vous découvrez rapidement avec vidéos ?

[Regardez cette vidéo](https://mix.office.com/watch/1butcdjxmu114) sur la façon de copier des données entre Azure stockage BLOB et données Lake Store à l’aide de DistCp.

## <a name="create-sample-tables-in-the-azure-sql-database"></a>Créer des exemples de tables dans la base de données SQL Azure

1. Commencez par créer deux exemples de tables dans la base de données SQL Azure. Utilisez [SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md) ou Visual Studio pour vous connecter à la base de données SQL Azure, puis exécutez les requêtes suivantes.

    **Créer Table1**

        CREATE TABLE [dbo].[Table1]( 
        [ID] [int] NOT NULL, 
        [FName] [nvarchar](50) NOT NULL, 
        [LName] [nvarchar](50) NOT NULL, 
        CONSTRAINT [PK_Table_4] PRIMARY KEY CLUSTERED 
            ( 
                [ID] ASC 
            ) 
        ) ON [PRIMARY] 
        GO

    **Créer Table2**

        CREATE TABLE [dbo].[Table2]( 
        [ID] [int] NOT NULL, 
        [FName] [nvarchar](50) NOT NULL, 
        [LName] [nvarchar](50) NOT NULL, 
        CONSTRAINT [PK_Table_4] PRIMARY KEY CLUSTERED 
            ( 
                [ID] ASC 
            ) 
        ) ON [PRIMARY] 
        GO

2. De **Table1**, ajoutez des exemples de données. Laissez **Table2** vide. Nous allons importer des données à partir de **Table1** dans données Lake magasin. Ensuite, nous va exporter des données à partir de données Lake Store vers **Table2**. Exécuter l’extrait de code suivante.

         
        INSERT INTO [dbo].[Table1] VALUES (1,'Neal','Kell'), (2,'Lila','Fulton'), (3, 'Erna','Myers'), (4,'Annette','Simpson'); 
  

## <a name="use-sqoop-from-an-hdinsight-cluster-with-access-to-data-lake-store"></a>Utiliser Sqoop à partir d’un cluster HDInsight avec l’accès aux données Lake Store

Un cluster HDInsight a déjà les packages Sqoop disponibles. Si vous avez configuré le cluster HDInsight pour utiliser des données Lake Store comme un espace de stockage supplémentaire, vous pouvez utiliser Sqoop (sans les modifications de configuration) pour importer/exporter des données entre une base de données relationnel (dans cet exemple, base de données SQL Azure) et un compte de données Lake Store. 

1. Pour ce didacticiel, nous part du principe que vous avez créé un cluster Linux afin que vous devez utiliser SSH pour vous connecter au cluster. Voir [se connecter à un cluster basé sur Linux HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-linux-based-hdinsight-cluster).

2. Vérifiez si vous pouvez accéder au compte de données Lake Store du groupe de. Exécutez la commande suivante à partir de l’invite SSH :

        
        hdfs dfs -ls adl://<data_lake_store_account>.azuredatalakestore.net/

    Il doit fournir une liste de fichiers/dossiers dans le compte données Lake.

### <a name="import-data-from-azure-sql-database-into-data-lake-store"></a>Importer des données à partir de la base de données SQL Azure dans données Lake Store

3. Accédez au répertoire où Sqoop packages sont disponibles. En règle générale, il s’agit en `/usr/hdp/<version>/sqoop/bin`. 

4. Importer les données à partir de **Table1** dans le compte de données Lake Store. Utilisez la syntaxe suivante :

        
        sqoop-import --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table1 --target-dir adl://<data-lake-store-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1

    Notez que cet espace réservé **sql-base de données-server-name** correspond au nom du serveur exécutant la base de données SQL Azure. espace réservé du **nom de base de données SQL** correspond au nom réel de la base de données.

    Par exemple,

        
        sqoop-import --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=nitinme@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table1 --target-dir adl://myadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1

5. Vérifiez que les données a été transférées vers le compte de données Lake Store. Exécutez la commande suivante :

        
        hdfs dfs -ls adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/

    Vous devriez voir le résultat suivant.

        
        -rwxrwxrwx   0 sshuser hdfs          0 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/_SUCCESS
        -rwxrwxrwx   0 sshuser hdfs         12 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00000
        -rwxrwxrwx   0 sshuser hdfs         14 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00001
        -rwxrwxrwx   0 sshuser hdfs         13 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00002
        -rwxrwxrwx   0 sshuser hdfs         18 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00003

    Chaque **partie-m -** * fichier correspond à une ligne dans la table source, * *Table1**. Vous pouvez afficher le contenu de la partie-m -* fichiers afin de vérifier.


### <a name="export-data-from-data-lake-store-into-azure-sql-database"></a>Exporter des données à partir de données Lake Store dans la base de données SQL Azure

6. Exporter les données de compte données Lake Store à la table vide, **Table2**, dans la base de données SQL Azure. Utilisez la syntaxe suivante.

        
        sqoop-export --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table2 --export-dir adl://<data-lake-store-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","

    Par exemple,

        
        sqoop-export --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=nitinme@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table2 --export-dir adl://myadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","

6. Vérifiez que les données a été téléchargées dans la table de base de données SQL. Utilisez [SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md) ou Visual Studio pour vous connecter à la base de données SQL Azure, puis exécutez la requête suivante.

        
        SELECT * FROM TABLE2

    Il doit avoir le résultat suivant.

        ID  FName   LName
        ------------------
        1   Neal    Kell
        2   Lila    Fulton
        3   Erna    Myers
        4   Annette Simpson

## <a name="see-also"></a>Voir aussi

- [Copier des données d’objets BLOB Azure stockage au magasin Lake des données](data-lake-store-copy-data-azure-storage-blob.md)
- [Sécuriser les données dans les données Lake Store](data-lake-store-secure-data.md)
- [Utiliser des données Azure Lake Analytique avec données Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Utiliser Azure HDInsight avec données Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)
