<properties
    pageTitle="Didacticiel HBase : prise en main groupes basés sur Linux HBase dans un Hadoop | Microsoft Azure"
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
    ms.topic="get-started-article"
    ms.date="10/19/2016"
    ms.author="jgao"/>



# <a name="hbase-tutorial-get-started-using-apache-hbase-with-linux-based-hadoop-in-hdinsight"></a>Didacticiel HBase : commencer à utiliser HBase Apache avec Hadoop basé sur Linux dans HDInsight 

[AZURE.INCLUDE [hbase-selector](../../includes/hdinsight-hbase-selector.md)]

Découvrez comment créer un cluster HBase dans HDInsight, créer des tables HBase et des tables de requêtes à l’aide de Hive. Pour des informations générales HBase, voir [vue d’ensemble de HDInsight HBase][hdinsight-hbase-overview].

Les informations de ce document sont spécifiques aux clusters HDInsight basé sur Linux. Pour plus d’informations sur les clusters fonctionnant sous Windows, utilisez le sélecteur d’onglet en haut de la page pour basculer.

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

##<a name="prerequisites"></a>Conditions préalables

Avant de commencer ce didacticiel HBase, vous devez disposer des éléments suivants :

- **Azure un abonnement**. Voir [Azure obtenir la version d’évaluation gratuite](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- [Banque d’informations sécurisé Shell(SSH)](hdinsight-hadoop-linux-use-ssh-unix.md). 
- [coin](http://curl.haxx.se/download.html).

### <a name="access-control-requirements"></a>Exigences de contrôle d’accès

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-hbase-cluster"></a>Créer HBase cluster

La procédure suivante utilise un modèle Azure le Gestionnaire de ressources pour créer un cluster de HBase basé sur Linux version 3.4 et la valeur par défaut dépendante compte de stockage Azure. Pour mieux comprendre les paramètres utilisés dans la procédure et d’autres méthodes de création de cluster, consultez [Hadoop basé sur Linux créer des groupes dans un HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

1. Cliquez sur l’image suivante pour ouvrir le modèle dans le portail Azure. Le modèle se trouve dans un conteneur blob public. 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hbase-cluster-in-hdinsight.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. À partir de la carte de **déploiement personnalisée** , entrez les informations suivantes :

    - **Abonnement**: sélectionnez votre abonnement Azure permettant de créer le cluster.
    - **Groupe de ressources**: créer un nouveau groupe de gestion des ressources Azure ou utiliser une existante.
    - **Emplacement**: spécifiez l’emplacement du groupe de ressources. 
    - **Nomducluster**: entrez un nom pour le cluster HBase que vous allez créer.
    - **Mot de passe et le nom de connexion cluster**: le nom de connexion par défaut est **admin**.
    - **Mot de passe et nom d’utilisateur SSH**: le nom d’utilisateur par défaut est **sshuser**.  Vous pouvez la renommer.
     
    Autres paramètres sont facultatifs.  
    
    Chaque cluster a une dépendance de compte de stockage Blob Azure. Après avoir supprimé un cluster, les données conservent dans le compte de stockage. Le nom de compte de stockage cluster par défaut est le nom du cluster avec « store » ajouté. Il est codé en dur dans la section variables du modèle.
        
3. Sélectionnez **J’accepte les termes et conditions indiquées ci-dessus**, puis cliquez sur **acheter**. Il faut environ 20 min pour créer un cluster.


>[AZURE.NOTE] Une fois un cluster HBase est supprimé, vous pouvez créer un autre cluster HBase à l’aide du même conteneur blob par défaut. Le nouveau cluster chercher les tables HBase que vous avez créé dans le cluster d’origine. Pour éviter les incohérences, nous vous recommandons de désactiver les tables HBase avant de supprimer le cluster.

## <a name="create-tables-and-insert-data"></a>Créer des tables et insérer des données

Vous pouvez utiliser SSH pour vous connecter à HBase clusters, puis utilisez HBase Shell pour créer des tables HBase, insérer des données et les données de la requête. Pour plus d’informations sur l’utilisation de SSH, voir [Utiliser SSH avec basé sur Linux Hadoop sur HDInsight de Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md) et [Utiliser SSH avec basé sur Linux Hadoop sur HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md).
 

Pour la plupart des personnes, données apparaissent sous forme de tableau :

![Données tabulaires HDInsight HBase][img-hbase-sample-data-tabular]

Dans HBase qui est une implémentation de BigTable, les mêmes données ressemble à :

![HDInsight HBase bigtable données][img-hbase-sample-data-bigtable]

Il vous paraîtront plus lorsque vous avez terminé la procédure suivante.  


**Utilisation d’HBase shell**

1. À partir de SSH, exécutez la commande suivante :

        hbase shell

4. Créer un HBase aux familles de deux colonnes :

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

    Pour plus d’informations sur le schéma de table HBase, voir [Introduction à la création de schéma HBase][hbase-schema]. Pour plus de commandes HBase, voir le [guide de référence Apache HBase][hbase-quick-start].

6. Quitter le shell

        exit



**Pour en bloc charger les données dans la table HBase de contacts**

HBase inclut plusieurs méthodes de chargement des données dans des tables.  Pour plus d’informations, voir [chargement en bloc](http://hbase.apache.org/book.html#arch.bulk.load).


Un exemple de fichier de données a été téléchargé dans un conteneur blob publique, *wasbs://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt*.  Le contenu du fichier de données est la suivante :

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

> [AZURE.NOTE] Cette procédure utilise la table HBase de Contacts que vous avez créé dans la dernière procédure.

1. À partir de SSH, exécutez la commande suivante pour transformer le fichier de données StoreFiles et la banque d’informations à un chemin d’accès relatif spécifié par Dimporttsv.bulk.output :.  Si vous êtes en HBase Shell, utilisez la commande Quitter pour quitter.

        hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name,Personal:Phone,Office:Phone,Office:Address" -Dimporttsv.bulk.output="/example/data/storeDataFileOutput" Contacts wasbs://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt

4. Exécutez la commande suivante pour charger les données à partir de /example/data/storeDataFileOutput à la table HBase :

        hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /example/data/storeDataFileOutput Contacts

5. Vous pouvez ouvrir le shell HBase et utilisez la commande analyse pour indiquer le contenu de la table.



## <a name="use-hive-to-query-hbase"></a>Utilisent Hive pour interroger HBase

Vous pouvez interroger des données dans les tables HBase en utilisant Hive. Cette section crée une table Hive qui correspond à la table HBase et utilise pour interroger les données dans votre table HBase.

1. Ouvrez **PuTTY**, puis connectez-vous au cluster.  Consultez les instructions dans la procédure précédente.
2. Ouvrez le shell Hive.

       hive
3. Exécuter le script HiveQL suivant pour créer un tableau ruche qui correspond à la table HBase. Vérifiez que vous avez créé l’exemple de tableau référencée précédemment dans ce didacticiel à l’aide du shell HBase avant d’exécuter cette déclaration.

        CREATE EXTERNAL TABLE hbasecontacts(rowkey STRING, name STRING, homephone STRING, officephone STRING, officeaddress STRING)
        STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
        WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,Personal:Name,Personal:Phone,Office:Phone,Office:Address')
        TBLPROPERTIES ('hbase.table.name' = 'Contacts');

2. Exécuter le script HiveQL suivant pour interroger les données dans la table HBase :

        SELECT count(*) FROM hbasecontacts;

## <a name="use-hbase-rest-apis-using-curl"></a>Utiliser HBase reste API à l’aide de coin

> [AZURE.NOTE] Lorsque vous utilisez recourbées ou toute autre communication reste avec WebHCat, vous devez vous authentifier les demandes en fournissant le nom d’utilisateur et mot de passe pour l’administrateur de cluster HDInsight. Vous devez également utiliser le nom du cluster dans le cadre de la ressource identificateur URI (Uniform) utilisé pour envoyer les demandes sur le serveur.
>
> Pour les commandes dans cette section, remplacez le **nom d’utilisateur** par l’utilisateur s’authentifier au cluster et remplacez le **mot de passe** par le mot de passe pour le compte d’utilisateur. Remplacez **NOMDUCLUSTER** par le nom de votre cluster.
>
> L’API REST est sécurisé via [l’authentification de base](http://en.wikipedia.org/wiki/Basic_access_authentication). Vous devez toujours vous demandes à l’aide de HTTP sécurisé (HTTPS) pour vous assurer que vos informations d’identification sont envoyées en toute sécurité sur le serveur.

1. À partir d’une ligne de commande, utilisez la commande suivante pour vérifier que vous pouvez vous connecter à votre cluster HDInsight :

        curl -u <UserName>:<Password> \
        -G https://<ClusterName>.azurehdinsight.net/templeton/v1/status

    Vous recevrez une réponse similaire à ce qui suit :

        {"status":"ok","version":"v1"}

    Les paramètres utilisés dans cette commande sont les suivantes :

    * **-u** - le nom d’utilisateur et mot de passe utilisés pour authentifier la demande.
    * **+ G** - indique qu’il s’agit d’une requête GET.

2. La commande suivante permet de répertorier les tables HBase existantes :

        curl -u <UserName>:<Password> \
        -G https://<ClusterName>.azurehdinsight.net/hbaserest/

3. Utilisez la commande suivante pour créer une nouvelle table HBase avec deux familles de colonne :

        curl -u <UserName>:<Password> \
        -X PUT "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/schema" \
        -H "Accept: application/json" \
        -H "Content-Type: application/json" \
        -d "{\"@name\":\"Contact1\",\"ColumnSchema\":[{\"name\":\"Personal\"},{\"name\":\"Office\"}]}" \
        -v

    Le schéma est fourni au format JSon.

4. Pour insérer des données, utilisez la commande suivante :

        curl -u <UserName>:<Password> \
        -X PUT "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/false-row-key" \
        -H "Accept: application/json" \
        -H "Content-Type: application/json" \
        -d "{\"Row\":{\"key\":\"MTAwMA==\",\"Cell\":{\"column\":\"UGVyc29uYWw6TmFtZQ==\", \"$\":\"Sm9obiBEb2xl\"}}}" \
        -v

    Vous devez en base 64 encoder toutes les valeurs spécifiés dans le commutateur -d.  Dans l’exemple :

    - MTAwMA == : 1000
    - UGVyc29uYWw6TmFtZQ == : personnel : nom
    - Sm9obiBEb2xl : John Dole

    [faux clé de ligne](https://hbase.apache.org/apidocs/org/apache/hadoop/hbase/rest/package-summary.html#operation_cell_store_single) vous permet d’insérer plusieurs valeur (par lots).

5. Utilisez la commande suivante pour obtenir une ligne :

        curl -u <UserName>:<Password> \
        -X GET "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/1000" \
        -H "Accept: application/json" \
        -v

Pour plus d’informations sur HBase reste, voir le [Guide de référence HBase Apache](https://hbase.apache.org/book.html#_rest).

## <a name="check-cluster-status"></a>Vérifier le statut de cluster

HBase dans HDInsight est fourni avec une interface utilisateur Web pour l’analyse des clusters. À l’aide de l’interface utilisateur Web, vous pouvez demander des statistiques ou des informations sur les zones.

SSH peut également servir à tunnel demandes locales, telles que les requêtes web, vers le cluster HDInsight. La demande puis doivent être routée à la ressource demandée comme s’il avait a été créée dans le nœud de tête cluster HDInsight. Pour plus d’informations, voir [Utiliser SSH avec basé sur Linux Hadoop sur HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md#tunnel).

**Pour établir un SSH tunnel session**

1. Ouvrez **PuTTY**.  
2. Si vous avez fourni un code SSH lorsque vous avez créé votre compte d’utilisateur pendant le processus de création, vous devez effectuer l’opération suivante pour sélectionner la clé privée à utiliser lors de l’authentification pour le cluster :

    Dans **catégorie**, développez **connexion**, développez **SSH**, puis sélectionnez **Auth**. Enfin, cliquez sur **Parcourir** et sélectionnez le fichier .ppk qui contient votre clé privée.

3. Dans **catégorie**, cliquez sur **la Session**.
4. Parmi les options de base pour votre écran session mastic, entrez les valeurs suivantes :

    - **Nom d’hôte**: l’adresse SSH de votre champ serveur dans le nom d’hôte (ou l’adresse IP) HDInsight. L’adresse SSH est le nom de votre cluster, puis **-ssh.azurehdinsight.net**. Par exemple, *mon_cluster ssh.azurehdinsight.net*.
    - **Port**: 22. Le port sur le headnode principal ssh est 22.  
5. Dans la section **catégorie** à gauche de la boîte de dialogue, développez **connexion**, développez **SSH**, puis cliquez sur **tunnel**.
6. Les options de contrôle de formulaire de transfert de port SSH, fournissent les informations suivantes :

    - **Port source** : le port sur le client que vous souhaitez transférer. Par exemple, 9876.
    - **Dynamique** - routage du proxy SOCKS dynamique vous permettent de conserver.
7. Cliquez sur **Ajouter** pour ajouter les paramètres.
8. Cliquez sur **Ouvrir** dans la partie inférieure de la boîte de dialogue pour ouvrir une connexion SSH.
9. Lorsque vous y êtes invité, connectez-vous au serveur à l’aide d’un compte SSH. Vous établissez une session SSH et activer le tunnel.

**Pour trouver le nom de domaine complet de l’envieront à l’aide de Ambari**

1. Accédez à https://<ClusterName>.azurehdinsight.net/.
2. Entrez vos informations d’identification du compte d’utilisateur cluster à deux reprises.
3. Dans le menu de gauche, cliquez sur **soigneur**.
4. Cliquez sur un des trois liens **Soigneur Server** dans la liste de synthèse.
5. Copier le **nom d’hôte**. Par exemple, zk0-CLUSTERNAME.xxxxxxxxxxxxxxxxxxxx.cx.internal.cloudapp.net.

**Pour configurer un programme client (Firefox) et vérifier l’état de cluster**

1. Ouvrez Firefox.
2. Cliquez sur le bouton de **Menu Ouvrir** .
3. Cliquez sur **Options**.
4. Cliquez sur **Avancé**, cliquez sur **réseau**, puis cliquez sur **paramètres**.
5. Sélectionnez **configuration manuelle proxy**.
6. Entrez les valeurs suivantes :

    - **Hôte SOCKS**: hôte local
    - **Port**: utiliser le même port que vous avez configuré dans le Putty SSH tunnel.  Par exemple, 9876.
    - **SOCKS v5**: (sélectionnée)
    - **DNS distant**: (sélectionnée)
7. Cliquez sur **OK** pour enregistrer les modifications.
8. Accédez à http://&lt;nom de domaine complet d’un soigneur > : 60010/maître-état.

Dans un cluster de disponibilité, vous trouverez un lien vers active HBase maître nœud actuel qui héberge l’interface utilisateur Web.

##<a name="delete-the-cluster"></a>Supprimer le cluster

Pour éviter les incohérences, nous vous recommandons de désactiver les tables HBase avant de supprimer le cluster.

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel HBase pour HDInsight, vous avez appris comment créer un cluster HBase et comment créer des tables et afficher les données de ces tables à partir du shell HBase. Vous avez également appris comment utiliser une requête Hive sur les données de tables HBase et comment utiliser les HBase c# reste API pour créer une table HBase et extraire des données de la table.

Pour plus d’informations, voir :

- [Vue d’ensemble de HDInsight HBase][hdinsight-hbase-overview]: HBase est une base de données open source, Apache, NoSQL basé sur Hadoop qui fournit un accès aléatoire et la cohérence forte de grandes quantités de données structurées et semi-structurées.


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
[azure-portal]: https://portal.azure.com/
[azure-create-storageaccount]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/

[img-hdinsight-hbase-cluster-quick-create]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-quick-create.png
[img-hdinsight-hbase-hive-editor]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-hive-editor.png
[img-hdinsight-hbase-file-browser]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-file-browser.png
[img-hbase-shell]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-shell.png
[img-hbase-sample-data-tabular]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-tabular.png
[img-hbase-sample-data-bigtable]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-bigtable.png
