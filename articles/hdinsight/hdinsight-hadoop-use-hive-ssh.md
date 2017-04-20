<properties
   pageTitle="Utiliser le shell Hive dans HDInsight (Hadoop) | Microsoft Azure"
   description="Découvrez comment utiliser le shell Hive avec un cluster basé sur Linux HDInsight. Vous allez apprendre à vous connecter au cluster HDInsight à l’aide de SSh, puis utiliser Shell ruche pour exécuter des requêtes de façon interactive."
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
   ms.date="10/04/2016"
   ms.author="larryfr"/>

# <a name="use-hive-with-hadoop-in-hdinsight-with-ssh"></a>Utiliser Hive avec Hadoop dans HDInsight avec SSH

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

Dans cet article, vous allez apprendre à utiliser SSH (Secure Shell) pour se connecter à un sur Azure HDInsight cluster et envoyez-le interactive Hive requêtes à l’aide de l’interface de ligne de Hive (commande).

> [AZURE.IMPORTANT] Bien que la commande Hive soit disponible sur les clusters basés sur Linux HDInsight, envisagez d’utiliser Beeline. Beeline est un client plus récent pour l’utilisation de Hive et est fourni avec votre cluster HDInsight. Pour plus d’informations sur l’utilisation de cette, voir [Utiliser ruche avec Hadoop dans HDInsight avec Beeline](hdinsight-hadoop-use-hive-beeline.md).

##<a id="prereq"></a>Conditions préalables

Pour effectuer les étapes décrites dans cet article, vous devez les éléments suivants :

* Hadoop basé sur Linux sur cluster HDInsight.

* Un clientSSH. Linux, Unix et Mac OS doivent être accompagnée d’un clientSSH. Utilisateurs Windows doivent télécharger un client, tel que [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

##<a id="ssh"></a>Se connecter avec SSH

Se connecter au nom de domaine complet (FQDN) de votre cluster HDInsight à l’aide de la commande SSH. Le nom de domaine complet sera le nom que vous avez attribué le cluster, puis **. azurehdinsight.net**. Par exemple, les éléments suivants seraient se connecter à un cluster nommé **myhdinsight**:

    ssh admin@myhdinsight-ssh.azurehdinsight.net

**Si vous avez fourni une clé de certificat pour l’authentification SSH** lorsque vous avez créé le cluster HDInsight, vous devrez peut-être spécifier l’emplacement de la clé privée sur votre système client :

    ssh -i ~/mykey.key admin@myhdinsight-ssh.azurehdinsight.net

**Si vous disposez d’un mot de passe pour l’authentification SSH** lorsque vous avez créé le cluster HDInsight, vous devrez fournir le mot de passe lorsque vous y êtes invité.

Pour plus d’informations sur l’utilisation de SSH avec HDInsight, voir [Utiliser SSH avec basé sur Linux Hadoop sur HDInsight de Linux, OS X et Unix](hdinsight-hadoop-linux-use-ssh-unix.md).

###<a name="putty-windows-based-clients"></a>PuTTY (clients fonctionnant sous Windows)

Windows ne fournit pas un clientSSH intégré. Nous vous recommandons d’utiliser **PuTTY**, qui peut être téléchargé à partir de [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Pour plus d’informations sur l’utilisation de PuTTY, voir [Utiliser SSH avec basé sur Linux Hadoop sur HDInsight à partir de Windows ](hdinsight-hadoop-linux-use-ssh-windows.md).

##<a id="hive"></a>Utilisez la commande Hive

2. Une fois connecté, démarrez l’infrastructure du langage commun ruche en utilisant la commande suivante :

        hive

3. À l’aide de l’infrastructure du langage commun, entrez les instructions suivantes pour créer une table nommée **log4jLogs** en utilisant les exemples de données :

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
    * **INPUT__FILE__NAME comme '%.log'** - indique qui nous devons renvoient uniquement des données à partir de fichiers se terminant par ruche. journal. Cela restreint la recherche dans le fichier exemple.log qui contient les données et conserve de renvoyer des données à partir d’autres exemple fichiers de données qui ne correspondent pas au schéma que nous avons définie.

    > [AZURE.NOTE] Tables externes doivent être utilisés lorsque vous pensez que les données sous-jacentes pour être mis à jour par une source externe, par exemple, un processus de téléchargement automatique des données, ou par une autre opération MapReduce, mais toujours Hive requêtes à utiliser les données les plus récentes.
    >
    > Suppression d’une table externe signifie **pas** supprimer les données, la définition de la table.

4. Utilisez les instructions suivantes pour créer une nouvelle table « interne » nommée **journaux d’erreurs**:

        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';

    Ces instructions effectuent les opérations suivantes :

    * **Créer tableau IF NOT EXISTS** - crée une table, s’il n’existe pas déjà. Dans la mesure où le mot-clé **externes** n’est pas utilisé, il s’agit d’une table interne, qui est stockée dans le data warehouse Hive et est gérée complètement par Hive.
    * **Stockées en tant que ORC** - stocke les données au format optimisé ligne en colonnes (ORC). Il s’agit d’un format hautement optimisé et efficace pour le stockage de données Hive.
    * REMPLACER **Insertion... Sélectionnez** - sélectionne les lignes dans la table **log4jLogs** qui contiennent **[erreur]**, puis insère les données dans la table des **journaux d’erreurs** .

    Pour vérifier que seules les lignes contenant **[erreur]** dans la colonne t4 ont été stockées dans la table des **journaux d’erreurs** , utilisez l’instruction suivante pour renvoyer toutes les lignes des **journaux d’erreurs**:

        SELECT * from errorLogs;

    Trois lignes de données doit être retournée, contenant tous les **[erreur]** dans la colonne t4.

    > [AZURE.NOTE] Contrairement aux tables externes, vous supprimez une table interne pour supprimer ainsi que les données sous-jacentes.

##<a id="summary"></a>Résumé

Comme vous pouvez le voir, la commande Hive fournit un moyen facile de façon interactive exécuter des requêtes Hive sur un cluster de HDInsight, surveiller l’état du travail et récupérer la sortie.

##<a id="nextsteps"></a>Étapes suivantes

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


[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png

