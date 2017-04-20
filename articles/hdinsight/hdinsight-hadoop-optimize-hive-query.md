<properties
   pageTitle="Optimiser vos requêtes Hive pour une exécution plus rapide dans HDInsight | Microsoft Azure"
   description="Découvrez comment optimiser vos requêtes Hive pour Hadoop dans HDInsight."
   services="hdinsight"
   documentationCenter=""
   authors="rashimg"
   manager="mwinkle"
   editor="cgronlun"
   tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="07/28/2015"
   ms.author="rashimg"/>


# <a name="optimize-hive-queries-for-hadoop-in-hdinsight"></a>Optimiser les requêtes Hive pour Hadoop dans HDInsight

Par défaut, les clusters Hadoop ne sont pas optimisées pour les performances. Cet article décrit quelques-unes des méthodes optimisation de performances Hive plus courantes que vous pouvez appliquer à notre requêtes.

##<a name="scale-out-worker-nodes"></a>Mettre à l’échelle des nœuds de travail

Augmenter le nombre de nœuds de travail d’un cluster peut exploiter plus mappeurs et REDUCTEURS à exécuter en parallèle. Il existe deux façons, vous pouvez augmenter évolution dans HDInsight :

- Dans le temps de mise en service, vous pouvez spécifier le nombre de nœuds de travail à un portail Azure, PowerShell Azure ou interface de ligne de commande disponibilité sur plusieurs plateformes.  Pour plus d’informations, voir [clusters HDInsight de mise en service](hdinsight-provision-clusters.md). L’écran suivant affichent la configuration du nœud collaborateur sur le portail Azure :

    ![scaleout_1][image-hdi-optimize-hive-scaleout_1]

- Dans le temps d’exécution, vous pouvez évoluer également un cluster sans avoir à recréer un. Cela est indiqué ci-dessous.
![scaleout_1][image-hdi-optimize-hive-scaleout_2]

Pour plus d’informations sur les différentes machines virtuelles pris en charge par HDInsight, voir [HDInsight tarifs](https://azure.microsoft.com/pricing/details/hdinsight/).

##<a name="enable-tez"></a>Activer Tez

[Tez Apache](http://hortonworks.com/hadoop/tez/) est un moteur d’exécution de remplacement au moteur MapReduce :

![tez_1][image-hdi-optimize-hive-tez_1]


Tez est plus rapide car :

- Exécuter dirigé acycliques Graph (DAG) en tant qu’une seule tâche dans le moteur MapReduce, DAG exprimé requiert chaque jeu de mappeurs à suivre par un ensemble des REDUCTEURS. Dans ce cas plusieurs tâches MapReduce à tourner pour chaque requête Hive. Tez n’a pas de telle contrainte et peut traiter DAG complexe comme une tâche et donc réduction des frais de démarrage de travail.
- **Écrit évite inutile** En raison de plusieurs tâches est lancés pour la même requête Hive dans le moteur MapReduce, le résultat de chaque tâche est destiné à HADOOP données intermédiaires. Dans la mesure où Tez réduit le nombre de tâches pour chaque requête Hive qu'il est possible d’éviter l’écriture inutile.
- **Retards de démarrage réduit** Tez parvient mieux réduire le délai de démarrage en réduisant le nombre de mappeurs qu'il doit démarrer et en améliorant l’optimisation dans l’ensemble.
- **Réutilise conteneurs** Chaque fois que possible Tez peut réutiliser conteneurs pour vous assurer que la latence en raison de démarrage de conteneurs est réduite.
- **Techniques d’optimisation continue** Traditionnellement optimisation effectuée pendant la phase de compilation. Toutefois, vous trouverez plus d’informations sur les entrées qui permettent la meilleure optimisation lors de l’exécution. Tez utilise des techniques d’optimisation continue qui permet d’optimiser davantage le planning à la phase de runtime.

Pour plus d’informations sur ces concepts, cliquez [ici](http://hortonworks.com/hadoop/tez/)

Vous pouvez rendre une requête Hive Tez activée en faisant précéder la requête avec le paramètre ci-dessous :

    set hive.execution.engine=tez;

Pour les clusters HDInsight fonctionnant sous Windows, Tez doit être activé simultanément la mise en service. Voici un exemple de script PowerShell Azure de mise en service un cluster Hadoop avec Tez activé :

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

    $clusterName = "[HDInsightClusterName]"
    $location = "[AzureDataCenter]" #i.e. West US
    $dataNodes = 32 # number of worker nodes in the cluster

    $defaultStorageAccountName = "[DefaultStorageAccountName]"
    $defaultStorageContainerName = "[DefaultBlobContainerName]"
    $defaultStorageAccountKey = $defaultStorageAccountKey = Get-AzureStorageKey $defaultStorageAccountName.ToLower() | %{ $_.Primary }

    $hdiUserName = "[HTTPUserName]"
    $hdiPassword = "[HTTPUserPassword]"

    $hdiSecurePassword = ConvertTo-SecureString $hdiPassword -AsPlainText -Force
    $hdiCredential = New-Object System.Management.Automation.PSCredential($hdiUserName, $hdiSecurePassword)

    $hiveConfig = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightHiveConfiguration'
    $hiveConfig.Configuration = @{ "hive.execution.engine"="tez" }

    New-AzureHDInsightClusterConfig -ClusterSizeInNodes $dataNodes -HeadNodeVMSize Standard_D14 -DataNodeVMSize Standard_D14 |
    Set-AzureHDInsightDefaultStorage -StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" -StorageAccountKey $defaultStorageAccountKey -StorageContainerName $defaultStorageContainerName |
    Add-AzureHDInsightConfigValues -Hive $hiveConfig |
    New-AzureHDInsightCluster -Name $clusterName -Location $location -Credential $hdiCredential

    
> [AZURE.NOTE] Basé sur Linux HDInsight clusters ont Tez activée par défaut.
    

## <a name="hive-partitioning"></a>Ruche partition

Opération e/s est comme élément critique performances principales pour exécuter des requêtes Hive. Les performances peuvent être améliorées si la quantité de données qui devant être lue peut être réduite. Par défaut, Hive requêtes analyse tables Hive entières. Il s’agit de requêtes tels que les analyses de tables, cependant pour les requêtes qui nécessitent uniquement pour numériser une petite quantité de données (par exemple, les requêtes avec filtrage), cela crée justifié. Partition Hive permet de requêtes Hive accéder aux uniquement le volume de données dans des tableaux Hive nécessaire.

Partition Hive est activée en réorganisant les données brutes en nouveaux répertoires avec chaque partition avoir son propre répertoire - où la partition est définie par l’utilisateur. Le diagramme suivant illustre partition une table Hive par la colonne *Year*. Un nouveau répertoire est créé pour chaque année.

![partition][image-hdi-optimize-hive-partitioning_1]

Quelques considérations partition :

- **Ne pas incomplète partition** - partition sur les colonnes uniquement quelques valeurs peuvent entraîner des partitions très peu. Par exemple, partition sexe sera uniquement créer deux partitions à créer (homme et Femme), ainsi que réduire la latence d’un maximum de moitié.

- **Ne pas trop partition** - sur l’autre extrême, créez une partition sur une colonne avec une valeur unique (par exemple, nom d’utilisateur) entraîne plusieurs partitions à l’origine d’un grand nombre de contrainte dans le namenode cluster comme il est nécessaire de gérer l’importance des répertoires.

- **Évitez de données incliner** - choisissez avec soin votre clé de partition afin que toutes les partitions sont de même longueur. Un exemple est partition sur *état* peut entraîner le nombre d’enregistrements sous Californie être presque 30 x qui du Vermont en raison de la différence de population.

Pour créer une table de partition, utilisez la clause *Partition par* :

    CREATE TABLE lineitem_part
        (L_ORDERKEY INT, L_PARTKEY INT, L_SUPPKEY INT,L_LINENUMBER INT,
         L_QUANTITY DOUBLE, L_EXTENDEDPRICE DOUBLE, L_DISCOUNT DOUBLE,
         L_TAX DOUBLE, L_RETURNFLAG STRING, L_LINESTATUS STRING,
         L_SHIPDATE_PS STRING, L_COMMITDATE STRING, L_RECEIPTDATE        STRING, L_SHIPINSTRUCT STRING, L_SHIPMODE STRING,
         L_COMMENT STRING)
    PARTITIONED BY(L_SHIPDATE STRING)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    STORED AS TEXTFILE;

Une fois que la table partitionnée est créée, vous pouvez créer une partition statique ou dynamique partition.

- **Partition statique** signifie que vous avez déjà sharded données dans les répertoires appropriés et vous pouvez demander des partitions Hive manuellement en fonction de l’emplacement du répertoire. Ce est présentée dans l’extrait de code ci-dessous.

        INSERT OVERWRITE TABLE lineitem_part
        PARTITION (L_SHIPDATE = ‘5/23/1996 12:00:00 AM’)
        SELECT * FROM lineitem 
        WHERE lineitem.L_SHIPDATE = ‘5/23/1996 12:00:00 AM’

        ALTER TABLE lineitem_part ADD PARTITION (L_SHIPDATE = ‘5/23/1996 12:00:00 AM’))
        LOCATION ‘wasbs://sampledata@ignitedemo.blob.core.windows.net/partitions/5_23_1996/'

- **Partition dynamique** signifie que vous voulez Hive pour créer des partitions automatiquement pour vous. Étant donné que nous avons déjà créé la table partition à partir de la table intermédiaire, il nous suffit est insérer des données dans la table partitionnée comme indiqué ci-dessous :

        SET hive.exec.dynamic.partition = true;
        SET hive.exec.dynamic.partition.mode = nonstrict;
        INSERT INTO TABLE lineitem_part
        PARTITION (L_SHIPDATE)
        SELECT L_ORDERKEY as L_ORDERKEY, L_PARTKEY as L_PARTKEY , 
             L_SUPPKEY as L_SUPPKEY, L_LINENUMBER as L_LINENUMBER,
             L_QUANTITY as L_QUANTITY, L_EXTENDEDPRICE as L_EXTENDEDPRICE,
             L_DISCOUNT as L_DISCOUNT, L_TAX as L_TAX, L_RETURNFLAG as       L_RETURNFLAG, L_LINESTATUS as L_LINESTATUS, L_SHIPDATE as       L_SHIPDATE_PS, L_COMMITDATE as L_COMMITDATE, L_RECEIPTDATE as   L_RECEIPTDATE, L_SHIPINSTRUCT as L_SHIPINSTRUCT, L_SHIPMODE as      L_SHIPMODE, L_COMMENT as L_COMMENT, L_SHIPDATE as L_SHIPDATE FROM lineitem;

Pour plus d’informations, voir [Partition Tables](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-PartitionedTables).

##<a name="use-the-orcfile-format"></a>Utilisez le format ORCFile

Hive prend en charge différents formats de fichier. Par exemple :

- **Texte**: il s’agit du format de fichier par défaut et fonctionne avec la plupart des scénarios
- **Avro**: fonctionne bien pour les scénarios d’interopérabilité
- **ORC/Parquet**: mieux pour les performances

Format ORC (optimisée ligne en colonnes) est un moyen très efficace pour stocker les données Hive. Comparaison avec d’autres formats, ORC offre les avantages suivants :

- prise en charge pour les types complexes, y compris les types DateTime et complexes structurées et semi-structurées
- accès à la compression est de 70 %
- index chaque 10 000 lignes permettant d’ignorer des lignes
- une baisse significative exécution

Pour activer le format ORC, commencez par créer un tableau avec la clause *stockées en tant que ORC*:

    CREATE TABLE lineitem_orc_part
        (L_ORDERKEY INT, L_PARTKEY INT,L_SUPPKEY INT, L_LINENUMBER INT,
         L_QUANTITY DOUBLE, L_EXTENDEDPRICE DOUBLE, L_DISCOUNT DOUBLE,
         L_TAX DOUBLE, L_RETURNFLAG STRING, L_LINESTATUS STRING,
         L_SHIPDATE_PS STRING, L_COMMITDATE STRING, L_RECEIPTDATE STRING,
         L_SHIPINSTRUCT STRING, L_SHIPMODE STRING, L_COMMENT     STRING)
    PARTITIONED BY(L_SHIPDATE STRING)
    STORED AS ORC;

Ensuite, vous insérez des données à la table ORC à partir de la table intermédiaire. Par exemple :

    INSERT INTO TABLE lineitem_orc
    SELECT L_ORDERKEY as L_ORDERKEY, 
           L_PARTKEY as L_PARTKEY , 
           L_SUPPKEY as L_SUPPKEY,
           L_LINENUMBER as L_LINENUMBER,
           L_QUANTITY as L_QUANTITY, 
           L_EXTENDEDPRICE as L_EXTENDEDPRICE,
           L_DISCOUNT as L_DISCOUNT,
           L_TAX as L_TAX,
           L_RETURNFLAG as L_RETURNFLAG,
           L_LINESTATUS as L_LINESTATUS,
           L_SHIPDATE as L_SHIPDATE,
           L_COMMITDATE as L_COMMITDATE,
           L_RECEIPTDATE as L_RECEIPTDATE, 
           L_SHIPINSTRUCT as L_SHIPINSTRUCT,
           L_SHIPMODE as L_SHIPMODE,
           L_COMMENT as L_COMMENT
    FROM lineitem;

Vous pouvez en savoir plus sur le format ORC [ici](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC).

##<a name="vectorization"></a>Vectorisation

Vectorisation permet Hive Traitement un lot de 1024 lignes solidaires au lieu de traitement d’une seule ligne à la fois. Cela signifie que les opérations simples sont effectuées plus rapidement, car moins de code interne doit s’exécuter.

Pour activer vectorisation préfixe votre requête Hive avec le paramètre suivant :

    set hive.vectorized.execution.enabled = true;

Pour plus d’informations, voir [l’exécution des requêtes Vectorized](https://cwiki.apache.org/confluence/display/Hive/Vectorized+Query+Execution).


##<a name="other-optimization-methods"></a>Autres méthodes d’optimisation

Il existe plusieurs méthodes d’optimisation que vous pouvez prendre en compte, par exemple :

- **Ruche la création de compartiments :** une technique qui permet à cluster ou segmenter les grands ensembles de données pour optimiser les performances de requête.
- **L’optimisation de jointure :** l’optimisation de l’exécution des requêtes de Hive planification améliorer l’efficacité de jointures et réduire la nécessité d’indicateurs de l’utilisateur. Pour plus d’informations, voir [l’optimisation de jointure](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+JoinOptimization#LanguageManualJoinOptimization-JoinOptimization).
- **augmenter REDUCTEURS**

##<a id="nextsteps"></a>Étapes suivantes
Dans cet article, vous avez appris plusieurs méthodes d’optimisation de requête Hive courantes. Pour plus d’informations, consultez les articles suivants :

- [Utiliser Hive Apache dans HDInsight](hdinsight-use-hive.md)
- [Analyser des données de vol retard à l’aide Hive dans HDInsight](hdinsight-analyze-flight-delay-data.md)
- [Analyser des données Twitter avec Hive dans HDInsight](hdinsight-analyze-twitter-data.md)
- [Analyser des données de capteur à l’aide de la Console de requête ruche sur Hadoop dans HDInsight](hdinsight-hive-analyze-sensor-data.md)
- [Utiliser Hive avec HDInsight pour analyser les journaux des sites Web](hdinsight-hive-analyze-website-log.md)


[image-hdi-optimize-hive-scaleout_1]: ./media/hdinsight-hadoop-optimize-hive-query/scaleout_1.png
[image-hdi-optimize-hive-scaleout_2]: ./media/hdinsight-hadoop-optimize-hive-query/scaleout_2.png
[image-hdi-optimize-hive-tez_1]: ./media/hdinsight-hadoop-optimize-hive-query/tez_1.png
[image-hdi-optimize-hive-partitioning_1]: ./media/hdinsight-hadoop-optimize-hive-query/partitioning_1.png
