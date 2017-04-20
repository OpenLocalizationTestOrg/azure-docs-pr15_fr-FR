<properties
    pageTitle="Qu’est HBase dans HDInsight ? | Microsoft Azure"
    description="Présentation de Apache HBase dans HDInsight, une base de données NoSQL empiler Hadoop. En savoir plus sur les cas d’utilisation et de comparer HBase à autre cluster Hadoop."
    keywords="bigtable, nosql, ce qui est hbase"
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
    ms.topic="get-started-article"
    ms.date="09/14/2016"
    ms.author="jgao"/>



# <a name="what-is-hbase-in-hdinsight-a-nosql-database-that-provides-bigtable-like-capabilities-for-hadoop"></a>Qu’est HBase dans HDInsight : base de données A NoSQL qui fournit les fonctionnalités j’aime BigTable pour Hadoop

Apache HBase est une base de données NoSQL open source, qui est basé sur Hadoop basé sur Google BigTable. HBase fournit un accès aléatoire et forte cohérence de grandes quantités de données structurées et semi-structurées dans une base de données schemaless organisé par familles de colonne.

Données sont stockées dans les lignes d’un tableau et regroupement des données dans une ligne par famille de colonne. HBase est une base de données schemaless dans le sens que le type de données stockées dans les ni les colonnes doivent être définies avant de les utiliser. Le code open source évolue linéaire pour gérer Po de données sur des milliers de nœuds. Il peut s’appuient sur redondance des données, traitement par lots et d’autres fonctionnalités qui sont fournies par les applications distribuées dans le réseau Hadoop.

## <a name="how-is-hbase-implemented-in-azure-hdinsight"></a>Comment HBase est activée dans Azure HDInsight ?

HDInsight HBase est proposée comme un cluster géré qui est intégré à l’environnement Azure. Les groupes sont configurés pour stocker les données directement dans le stockage Blob Azure, qui fournit une latence faible et élasticité accrue dans choix de performances et de coûts. Cela permet aux clients de créer des sites Web interactifs qui fonctionnent avec les grands ensembles de données, à créer des services qui stockent les données capteur et télémétrie des millions de points de terminaison et d’analyser ces données avec les tâches Hadoop. HBase et Hadoop sont bons points de départ pour projet de données volumineux dans Azure ; en particulier, ils permettent aux applications en temps réel pour l’utiliser avec les grands ensembles de données.

La mise en œuvre HDInsight exploite l’architecture horizontale de HBase pour fournir automatique ont des tables, la cohérence des forte des lectures et écritures et basculement automatique. Améliorez les performances en mettant en cache en mémoire pour la lecture et de diffusion en continu pour les écritures haut débit. Mise en service de réseau virtuel est également disponible pour HDInsight HBase. Pour plus d’informations, voir [clusters HDInsight de mise en service sur le réseau virtuel Azure] [hbase-provision-vnet].

## <a name="how-is-data-managed-in-hdinsight-hbase"></a>Comment les données sont gérées dans HDInsight HBase ?

Données pouvant être gérées dans HBase à l’aide de la `create`, `get`, `put`, et `scan` commandes à partir du shell HBase. Données sont écrites dans la base de données à l’aide de `put` et en lecture à l’aide de `get`. La `scan` commande est utilisée pour obtenir des données provenant de plusieurs lignes dans un tableau. Données peuvent également être gérées à l’aide de l’HBase API c#, qui fournit une bibliothèque de client en haut de l’API REST HBase. Une base de données HBase peut également être interrogée à l’aide de Hive. Pour une introduction à ces modèles de programmation, voir [commencer à utiliser HBase avec Hadoop dans HDInsight][hbase-get-started]. Co-création processeurs sont également disponibles, qui permettent de traitement des données dans les nœuds hébergeant la base de données.


## <a name="scenarios-use-cases-for-hbase"></a>Scénarios : Exemples d’utilisation pour HBase
Le cas d’utilisation canonique pour le BigTable (et par extension, HBase) a été créé a été recherche sur le web. Les moteurs de recherche créer à partir d’index correspondant termes à des pages web qui contiennent les. Mais il existe de nombreuses autres utilisations HBase est adaptée à — dont plusieurs sont répertoriés dans cette section.

- Valeur de clé store

    HBase peut être utilisé comme un magasin clé-valeur, et il convient pour la gestion des systèmes de messagerie. Facebook utilise HBase pour leur système de messagerie, et il est idéal pour le stockage et la gestion des communications Internet. WebTable utilise HBase pour rechercher et gérer les tables qui sont extraites des pages Web.

- Données capteur

    HBase est utile pour capturer les données collectées par incréments de diverses sources. Cela inclut social analytique, série chronologique, tout en conservant les tableaux de bord interactifs et des tendances compteurs et la gestion des systèmes de journal d’audit. Exemples commerçant Bloomberg terminal et l’heure série de base de données ouverte (OpenTSDB), qui stocke et permet d’accéder aux mesures collectées sur le fonctionnement des systèmes de serveur.

- Requête en temps réel

    [Phoenix](http://phoenix.apache.org/) est un moteur de requête SQL pour Apache HBase. Il est accessible en tant qu’un pilote JDBC, et il permet à l’interrogation et gestion des tables HBase à l’aide de SQL.

- HBase en tant que plate-forme

    Applications peuvent s’exécuter sur HBase en utilisant comme un magasin de données. Exemples Phoenix, OpenTSDB, Kiji et Titan. Applications peuvent également intégrer avec HBase. Exemples Hive cochon, mode série sur LAN.r, vague de, Flume, Impala, explosion, trigéminés et exploration des niveaux.


##<a name="next-steps"></a>Étapes suivantes

- [Commencer à utiliser HBase avec Hadoop dans HDInsight][hbase-get-started]
- [Mise en service de clusters HDInsight sur réseau virtuel Azure] [hbase-provision-vnet]
- [Configurer la réplication HBase dans HDInsight](hdinsight-hbase-geo-replication.md)
- [Analyser opinion Twitter avec HBase dans HDInsight][hbase-twitter-sentiment]
- [Maven permet de créer des applications Java qui utilisent HBase avec HDInsight (Hadoop)][hbase-build-java-maven]

##<a name="see-also"></a>Voir aussi

- [Apache HBase](https://hbase.apache.org/)
- [Bigtable : Un système de stockage distribué pour les données structurées](http://research.google.com/archive/bigtable.html)




[hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md

[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md

[hbase-build-java-maven]: hdinsight-hbase-build-java-maven.md

[hdinsight-use-hive]: hdinsight-use-hive.md

[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md

[hbase-get-started]: http://azure.microsoft.com/documentation/articles/hdinsight-hbase-get-started/

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://portal.azure.com/
[azure-create-storageaccount]: ../storage-create-storage-account.md

[apache-hadoop]: http://hadoop.apache.org/
