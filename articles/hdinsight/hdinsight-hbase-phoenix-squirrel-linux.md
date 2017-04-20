<properties 
   pageTitle="Utilisez Apache Phoenix et non dans HDInsight | Microsoft Azure" 
   description="Découvrez comment utiliser Apache Phoenix dans HDInsight et comment installer et configurer non sur votre poste de travail pour vous connecter à un cluster HBase dans HDInsight." 
   services="hdinsight" 
   documentationCenter="" 
   authors="mumian" 
   manager="jhubbard" 
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="09/02/2016"
   ms.author="jgao"/>

# <a name="use-apache-phoenix-with-linux-based-hbase-clusters-in-hdinsight"></a>Utiliser Phoenix Apache avec groupes basés sur Linux HBase dans un HDInsight  

Découvrez comment utiliser [Apache Phoenix](http://phoenix.apache.org/) dans HDInsight et comment utiliser SQLUltraLite. Pour plus d’informations sur Phoenix, voir [Phoenix en 15 minutes ou moins](http://phoenix.apache.org/Phoenix-in-15-minutes-or-less.html). Pour la grammaire Phoenix, voir [Phoenix grammaire](http://phoenix.apache.org/language/index.html).

>[AZURE.NOTE] Pour plus d’informations de version Phoenix dans HDInsight, voir [Quelles sont les nouveautés dans les versions de cluster Hadoop fournies par HDInsight ?] [hdinsight-versions].

##<a name="use-sqlline"></a>Utiliser SQLUltraLite
[SQLUltraLite](http://sqlline.sourceforge.net/) est un utilitaire de ligne de commande pour exécuter SQL. 

###<a name="prerequisites"></a>Conditions préalables
Avant de pouvoir utiliser SQLUltraLite, vous devez disposer des éléments suivants :

- **Cluster A HBase dans HDInsight**. Pour plus d’informations sur la mise en service HBase cluster, voir [prise en main HBase Apache dans HDInsight][hdinsight-hbase-get-started].
- **Se connecter au cluster HBase via le protocole Bureau à distance**. Pour plus d’informations, voir [Hadoop gérer les groupes dans un HDInsight à l’aide du portail classique Azure][hdinsight-manage-portal].


Lorsque vous vous connectez à un cluster HBase, vous avez besoin pour vous connecter à un de l’envieront. Chaque cluster HDInsight a 3 envieront. 

**Pour déterminer le nom d’hôte soigneur**

1. Ouvrez Ambari en Parcourir pour **https://<ClusterName>. azurehdinsight.net**.
2. Entrez le nom d’utilisateur HTTP (cluster) et le mot de passe pour vous connecter.
3. Dans le menu de gauche, cliquez sur **soigneur** . Vous sont voir 3 **Soigneur Server** répertoriés.
4. Cliquez sur le **Serveur soigneur** répertorié. Dans le volet Résumé, recherchez le **nom d’hôte**. Il est semblable à *zk1 jdolehb.3lnng4rcvp5uzokyktxs4a5dhd.bx.internal.cloudapp.net*.

**Utiliser SQLUltraLite**

1. Connectez-vous au cluster à l’aide de SSH. Pour plus d’informations, voir [Utiliser SSH avec basé sur Linux Hadoop sur HDInsight de Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md) ou [Utiliser SSH avec basé sur Linux Hadoop sur HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md) en fonction de votre ordinateur client du système d’exploitation.

2. À partir de SSH, exécutez les commandes suivantes pour exécuter SQLUltraLite :

        cd /usr/hdp/2.2.9.1-7/phoenix/bin
        ./sqlline.py <ClusterName>:2181:/hbase-unsecure

2. Exécutez les commandes suivantes pour créer une table HBase et insérer des données :

        CREATE TABLE Company (COMPANY_ID INTEGER PRIMARY KEY, NAME VARCHAR(225));
    
        !tables
        
        UPSERT INTO Company VALUES(1, 'Microsoft');
        
        SELECT * FROM Company;
        
        !quit

Pour plus d’informations, voir [Phoenix grammaire](http://phoenix.apache.org/language/index.html)et orthographe [SQLUltraLite manuel](http://sqlline.sourceforge.net/#manual) .


 
##<a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez appris à utiliser Apache Phoenix dans HDInsight.  Pour plus d’informations, voir

- [Vue d’ensemble de HDInsight HBase][hdinsight-hbase-overview]: HBase est une base de données open source, Apache, NoSQL basé sur Hadoop qui fournit un accès aléatoire et forte cohérence de grandes quantités de données structurées et semi-structurées.
- [Mise en service de clusters HBase sur réseau virtuel Azure][hdinsight-hbase-provision-vnet]: avec l’intégration des réseaux virtuel, HBase clusters peuvent être déployés sur le même réseau virtuel que vos applications afin que les applications puissent communiquer avec HBase directement.
- [Configurer HBase réplication dans HDInsight](hdinsight-hbase-geo-replication.md): Découvrez comment configurer la réplication HBase sur deux centres de données Azure. 
- [Analyser opinion Twitter avec HBase dans HDInsight][hbase-twitter-sentiment]: Découvrez comment effectuer en temps réel [analyse opinion](http://en.wikipedia.org/wiki/Sentiment_analysis) de données volumineux à l’aide de HBase dans un cluster Hadoop dans HDInsight.

[azure-portal]: https://portal.azure.com
[vnet-point-to-site-connectivity]: https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNETPT

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-hbase-get-started]: hdinsight-hbase-tutorial-get-started.md
[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md#connect-to-hdinsight-clusters-by-using-rdp
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md
[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md

[hdinsight-hbase-phoenix-sqlline]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-phoenix-sqlline.png
[img-certificate]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-vpn-certificate.png
[img-vnet-diagram]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-vnet-point-to-site.png
[img-squirrel-driver]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-driver.png
[img-squirrel-alias]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-alias.png
[img-squirrel]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel.png
[img-squirrel-sql]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-sql.png


 
