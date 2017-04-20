<properties
   pageTitle="Utiliser Beeline avec Hive sur HDInsight (Hadoop) | Microsoft Azure"
   description="Découvrez comment utiliser SSH pour vous connecter à un cluster Hadoop dans HDInsight et envoyez-le interactive Hive requêtes à l’aide de Beeline. Beeline est un utilitaire pour l’utilisation de HiveServer2 sur JDBC."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"
    tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/10/2016"
   ms.author="larryfr"/>

#<a name="use-hive-with-hadoop-in-hdinsight-with-beeline"></a>Utiliser Hive avec Hadoop dans HDInsight avec Beeline

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

Dans cet article, vous allez apprendre à utiliser SSH (Secure Shell) pour vous connecter à un cluster basé sur Linux HDInsight et envoyez-le interactive Hive requêtes à l’aide de l’outil de ligne de commande [Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) .

> [AZURE.NOTE] Beeline JDBC pour se connecter à Hive. Pour plus d’informations sur l’utilisation de JDBC avec Hive, voir [se connecter à Hive sur Azure HDInsight à l’aide du pilote JDBC ruche](hdinsight-connect-hive-jdbc-driver.md).

##<a id="prereq"></a>Conditions préalables

Pour effectuer les étapes décrites dans cet article, vous devez les éléments suivants :

* Hadoop basé sur Linux sur cluster HDInsight.

* Un clientSSH. Linux, Unix et Mac OS doivent être accompagnée d’un clientSSH. Utilisateurs Windows doivent télécharger un client, tel que [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

##<a id="ssh"></a>Se connecter avec SSH

Se connecter au nom de domaine complet (FQDN) de votre cluster HDInsight à l’aide de la commande SSH. Le nom de domaine complet sera le nom que vous avez attribué le cluster, puis **. azurehdinsight.net**. Par exemple, les éléments suivants seraient se connecter à un cluster nommé **myhdinsight**:

    ssh admin@myhdinsight-ssh.azurehdinsight.net

**Si vous avez fourni une clé de certificat pour l’authentification SSH** lorsque vous avez créé le cluster HDInsight, vous devrez peut-être spécifier l’emplacement de la clé privée sur votre système client :

    ssh admin@myhdinsight-ssh.azurehdinsight.net -i ~/mykey.key

**Si vous disposez d’un mot de passe pour l’authentification SSH** lorsque vous avez créé le cluster HDInsight, vous devrez fournir le mot de passe lorsque vous y êtes invité.

Pour plus d’informations sur l’utilisation de SSH avec HDInsight, voir [Utiliser SSH avec basé sur Linux Hadoop sur HDInsight de Linux, OS X et Unix](hdinsight-hadoop-linux-use-ssh-unix.md).

###<a name="putty-windows-based-clients"></a>PuTTY (clients fonctionnant sous Windows)

Windows ne fournit pas un clientSSH intégré. Nous vous recommandons d’utiliser **PuTTY**, qui peut être téléchargé à partir de [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Pour plus d’informations sur l’utilisation de PuTTY, voir [Utiliser SSH avec basé sur Linux Hadoop sur HDInsight à partir de Windows ](hdinsight-hadoop-linux-use-ssh-windows.md).

##<a id="beeline"></a>Utilisez la commande Beeline

1. Une fois connecté, utilisez les éléments suivants pour démarrer Beeline :

        beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin

    Cela démarre le client Beeline et vous connecter à l’url JDBC. Ici, `localhost` est utilisée dans la mesure où HiveServer2 s’exécute sur les deux nœuds dans le cluster têtes et nous utilisons Beeline directement sur le headnode principal.
    
    Une fois la commande terminée, vous vous arrivez à un `jdbc:hive2://localhost:10001/>` invite de commandes.

3. Généralement, les commandes beeline commencent par une `!` caractère, par exemple `!help` affiche l’aide. Toutefois, le `!` peuvent souvent être omis. Par exemple, `help` fonctionne également.

    Si vous affichez l’aide, vous remarquerez `!sql`, qui est utilisée pour exécuter les instructions HiveQL. Toutefois, HiveQL est donc fréquemment utilisée que vous pouvez omettre le précédent `!sql`. Les deux instructions suivantes ont exactement le même résultat ; afficher les tables actuellement disponibles via Hive :
    
        !sql show tables;
        show tables;
    
    Sur un cluster de nouveau, qu’une seule table doit être répertoriée : __hivesampletable__.

4. Utilisez ce qui suit pour afficher le schéma pour le hivesampletable :

        describe hivesampletable;
        
    Ceci renverra les informations suivantes :
    
        +-----------------------+------------+----------+--+
        |       col_name        | data_type  | comment  |
        +-----------------------+------------+----------+--+
        | clientid              | string     |          |
        | querytime             | string     |          |
        | market                | string     |          |
        | deviceplatform        | string     |          |
        | devicemake            | string     |          |
        | devicemodel           | string     |          |
        | state                 | string     |          |
        | country               | string     |          |
        | querydwelltime        | double     |          |
        | sessionid             | bigint     |          |
        | sessionpagevieworder  | bigint     |          |
        +-----------------------+------------+----------+--+

    Cela permet d’afficher les colonnes du tableau. Tandis que nous pourrions effectuer certaines requêtes sur ces données, à la place créer un tout nouveau tableau pour vous montrer comment charger les données dans Hive et appliquer un schéma.
    
5. Entrez les instructions suivantes pour créer une table nommée **log4jLogs** en utilisant les exemples de données fournis avec le cluster HDInsight :

        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    Ces instructions effectuent les opérations suivantes :

    * **DROP TABLE** - supprime la table et le fichier de données, au cas où la table existe déjà.
    * **Créer une TABLE externe** - crée une nouvelle table « externe » dans Hive. Tables externes ne stockent la définition de table Hive. Les données restent dans l’emplacement d’origine.
    * **FORMAT de ligne** indique la ruche mise en forme les données. Dans ce cas, les champs dans chaque journal sont séparées par un espace.
    * **Stockées en tant que fichier texte emplacement** - indique la ruche où les données sont stockées (le répertoire de données d’exemple), et qu’il est stocké en tant que texte.
    * **Sélectionnez** - sélectionne un compteur de toutes les lignes dont la colonne **t4** contient la valeur **[erreur]**. Cela doit renvoyer une valeur de **3** qu’il sont a trois lignes contenant cette valeur.
    * **INPUT__FILE__NAME comme '%.log'** - indique qui nous devons renvoient uniquement des données à partir de fichiers se terminant par ruche. journal. En règle générale, vous suffit données possédant le même schéma dans le même dossier lors de l’interrogation avec hive, mais cet exemple de fichier journal est stocké avec d’autres formats de données.

    > [AZURE.NOTE] Tables externes doivent être utilisés lorsque vous pensez que les données sous-jacentes pour être mis à jour par une source externe, par exemple, un processus de téléchargement automatique des données, ou par une autre opération MapReduce, mais toujours Hive requêtes à utiliser les données les plus récentes.
    >
    > Suppression d’une table externe signifie **pas** supprimer les données, la définition de la table.
    
    Le résultat de cette commande doit être semblable à ce qui suit :
    
        INFO  : Tez session hasn't been created yet. Opening session
        INFO  :
        
        INFO  : Status: Running (Executing on YARN cluster with App id application_1443698635933_0001)
        
        INFO  : Map 1: -/-      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
        INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
        INFO  : Map 1: 1/1      Reducer 2: 0/1
        INFO  : Map 1: 1/1      Reducer 2: 0(+1)/1
        INFO  : Map 1: 1/1      Reducer 2: 1/1
        +----------+--------+--+
        |   sev    | count  |
        +----------+--------+--+
        | [ERROR]  | 3      |
        +----------+--------+--+
        1 row selected (47.351 seconds)

4. Pour sortir Beeline, utilisez `!quit`.

##<a id="file"></a>Exécuter un fichier HiveQL

Beeline peut également servir à exécuter un fichier qui contient des instructions HiveQL. Procédez comme suit pour créer un fichier, puis exécutez-le à l’aide de Beeline.

1. Utilisez la commande suivante pour créer un nouveau fichier nommé __query.hql__:

        nano query.hql
        
2. Une fois que l’éditeur s’ouvre, utilisez ce qui suit en tant que le contenu du fichier. Cette requête crée une nouvelle table « interne » nommée **journaux d’erreurs**:

        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';

    Ces instructions effectuent les opérations suivantes :

    * **Créer tableau IF NOT EXISTS** - crée une table, s’il n’existe pas déjà. Dans la mesure où le mot-clé **externes** n’est pas utilisé, il s’agit d’une table interne, qui est stockée dans le data warehouse Hive et est gérée complètement par Hive.
    * **Stockées en tant que ORC** - stocke les données au format optimisé ligne en colonnes (ORC). Il s’agit d’un format hautement optimisé et efficace pour le stockage de données Hive.
    * REMPLACER **Insertion... Sélectionnez** - sélectionne les lignes dans la table **log4jLogs** qui contiennent **[erreur]**, puis insère les données dans la table des **journaux d’erreurs** .
    
    > [AZURE.NOTE] Contrairement aux tables externes, vous supprimez une table interne pour supprimer ainsi que les données sous-jacentes.
    
3. Pour enregistrer le fichier, maintenez la touche __Ctrl__enfoncée ___X__, puis entrez __Y__et enfin sur __entrée__.

4. Utiliser les éléments suivants pour exécuter le fichier à l’aide de Beeline. Remplacez le __nom d’hôte__ par le nom obtenu précédemment pour le nœud de tête et __mot de passe__ avec le mot de passe du compte d’administrateur :

        beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin -i query.hql

    > [AZURE.NOTE] La `-i` paramètre commence Beeline, exécute les instructions dans le fichier query.hql et est conservé dans Beeline à la `jdbc:hive2://localhost:10001/>` invite de commandes. Vous pouvez également exécuter un fichier en utilisant la `-f` paramètre qui vous renvoie à Bash une fois que le fichier a été traité.

5. Pour vérifier que la table des **journaux d’erreurs** a été créée, utilisez l’instruction suivante pour renvoyer toutes les lignes des **journaux d’erreurs**:

        SELECT * from errorLogs;

    Trois lignes de données doit être retournée, contenant tous les **[erreur]** dans la colonne t4 :
    
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | errorlogs.t1  | errorlogs.t2  | errorlogs.t3  | errorlogs.t4  | errorlogs.t5  | errorlogs.t6  | errorlogs.t7  |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | 2012-02-03    | 18:35:34      | SampleClass0  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 18:55:54      | SampleClass1  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 19:25:27      | SampleClass4  | [ERROR]       | incorrect     | id            |               |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        3 rows selected (1.538 seconds)

## <a name="more-about-beeline-connectivity"></a>En savoir plus sur la connectivité Beeline

Les étapes décrites dans ce document utilisation `localhost` pour vous connecter à HiveServer2 s’exécutant sur le cluster headnode. Vous pouvez également utiliser le nom d’hôte ou le nom de domaine complet de la headnode ceux requièrent des étapes supplémentaires au processus (étapes pour trouver le nom d’hôte ou le nom de domaine complet). À l’aide de `localhost` est suffisant lors de l’utilisation de Beeline à partir de la headnode.

Si vous avez un nœud dans votre cluster, avec Beeline installé, vous devrez utiliser le nom d’hôte ou le nom de domaine complet de la headnode pour vous connecter.

Si vous avez Beeline installée sur un client en dehors de votre cluster, vous pouvez vous connecter à l’aide de la commande suivante. Remplacez __NOMDUCLUSTER__ par le nom de votre cluster HDInsight. Remplacez le __mot de passe__ par le mot de passe du compte d’administrateur (connexion HTTP).

    beeline -u 'jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;ssl=true?hive.server2.transport.mode=http;hive.server2.thrift.http.path=hive2' -n admin -p PASSWORD

Notez que le paramètres/URI est différente de celle lorsque exécutant directement sur un headnode ou à partir d’un nœud de bord au sein du cluster. C’est parce que la connexion au cluster à partir d’internet utilise une passerelle publique qui achemine le trafic via le port 443. En outre, plusieurs autres services sont exposés via la passerelle publique sur le port 443, afin que l’URI est différent de celui lors de la connexion directement. Lorsque vous vous connectez à partir d’internet la session doit également s’authentifier en fournissant le mot de passe.

##<a id="summary"></a><a id="nextsteps"></a>Étapes suivantes

Comme vous pouvez le voir, la commande Beeline fournit un moyen facile de façon interactive exécuter des requêtes Hive sur un cluster HDInsight.

Pour obtenir des informations générales sur Hive dans HDInsight :

* [Utiliser Hive avec Hadoop sur HDInsight](hdinsight-use-hive.md)

Pour plus d’informations sur les autres façons, vous pouvez travailler avec Hadoop sur HDInsight :

* [Utiliser cochon avec Hadoop sur HDInsight](hdinsight-use-pig.md)

* [Utiliser MapReduce avec Hadoop sur HDInsight](hdinsight-use-mapreduce.md)

Si vous utilisez Tez avec Hive, consultez les documents suivants pour les informations de débogage :

* [Utiliser l’interface utilisateur Tez sur HDInsight fonctionnant sous Windows](hdinsight-debug-tez-ui.md)

* [Utiliser l’affichage Ambari Tez sur HDInsight basé sur Linux](hdinsight-debug-ambari-tez-view.md)

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md

[putty]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html


[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md


[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx

