<properties
   pageTitle="Conseils pour l’utilisation de Hadoop sur basé sur Linux HDInsight | Microsoft Azure"
   description="Obtenir des conseils de mise en œuvre pour l’utilisation de clusters basé sur Linux HDInsight (Hadoop) dans un environnement Linux familier en cours d’exécution dans le cloud Azure."
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
   ms.date="09/13/2016"
   ms.author="larryfr"/>

# <a name="information-about-using-hdinsight-on-linux"></a>Informations sur l’utilisation de HDInsight sous Linux

Basé sur Linux Azure HDInsight clusters indiquez Hadoop dans un environnement Linux familier, en cours d’exécution dans le cloud Azure. Pour la plupart des éléments, elle doit fonctionner exactement comme n’importe quelle autre installation Hadoop sur Linux. Ce document présente les différences spécifiques que vous devez tenir compte des.

##<a name="prerequisites"></a>Conditions préalables

La plupart des étapes décrites dans ce document utilisent les utilitaires suivants, devant être installé sur votre système.

* [coin](https://curl.haxx.se/) - utilisé pour communiquer avec les services sur le web
* [jq](https://stedolan.github.io/jq/) - utilisé pour analyser des documents JSON
* [Azure infrastructure du langage commun](../xplat-cli-install.md) - utilisée pour gérer à distance les services Azure

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-powershell-and-cli.md)] 

## <a name="domain-names"></a>Noms de domaine

Le nom de domaine complet (FQDN) à utiliser lors de la connexion au cluster à partir d’internet est ** &lt;nomcluster >. azurehdinsight.net** ou (pour SSH) ** &lt;nomducluster-ssh >. azurehdinsight.net**.

En interne, chaque nœud dans le cluster a un nom qui est attribué lors de la configuration de cluster. Pour rechercher les noms de cluster, vous pouvez visiter la page __hôtes__ dans l’interface utilisateur Web Ambari ou pour renvoyer une liste des hôtes de l’API REST Ambari, utilisez ce qui suit :

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts" | jq '.items[].Hosts.host_name'

Remplacez le __mot de passe__ par le mot de passe du compte d’administrateur et __NOMDUCLUSTER__ avec le nom de votre cluster. Ceci renverra un document JSON qui contient la liste des hôtes dans le cluster, puis jq sortir le `host_name` valeur de l’élément pour chaque hôte du cluster.

Si vous avez besoin trouver le nom du nœud d’un service spécifique, vous pouvez interroger Ambari pour ce composant. Par exemple, pour rechercher les hôtes pour le nœud de nom HADOOP, utilisez les éléments suivants.

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/NAMENODE" | jq '.host_components[].HostRoles.host_name'

Renvoie un document JSON décrivant le service, et puis jq sortir uniquement la `host_name` valeur pour les hôtes.

## <a name="remote-access-to-services"></a>Accès à distance aux services

* **Ambari (web)** - https://&lt;nomcluster >. azurehdinsight.net

    S’authentifier à l’aide de l’administrateur cluster et mot de passe et puis se connecter à Ambari. Il utilise également l’administrateur cluster et mot de passe.

    L’authentification est en texte clair : toujours utiliser HTTPS pour vous assurer que la connexion est sécurisée.

    > [AZURE.IMPORTANT] Bien que Ambari pour votre cluster est accessible directement via Internet, certaines fonctionnalités repose sur l’accès aux nœuds selon le nom de domaine interne utilisé par le cluster. Dans la mesure où il s’agit d’un nom de domaine internes, et non public, vous recevrez les erreurs « serveur non trouvé » lorsque vous essayez d’accéder à certaines fonctionnalités via Internet.
    >
    > Pour utiliser toutes les fonctionnalités de l’interface utilisateur du web Ambari, utilisez un tunnel SSH pour le trafic web de proxy vers le nœud de tête cluster. Voir [Utiliser SSH tunnel pour accéder à l’interface utilisateur Ambari web, ResourceManager, JobHistory, NameNode, Oozie et autres web l’interface utilisateur](hdinsight-linux-ambari-ssh-tunnel.md)

* **Ambari (REST)** - https://&lt;nomcluster >.azurehdinsight.net/ambari

    > [AZURE.NOTE] S’authentifier à l’aide de l’administrateur cluster et mot de passe.
    >
    > L’authentification est en texte clair : toujours utiliser HTTPS pour vous assurer que la connexion est sécurisée.

* **WebHCat (Templeton)** - https://&lt;nomcluster >.azurehdinsight.net/templeton

    > [AZURE.NOTE] S’authentifier à l’aide de l’administrateur cluster et mot de passe.
    >
    > L’authentification est en texte clair : toujours utiliser HTTPS pour vous assurer que la connexion est sécurisée.

* **SSH** - &lt;nomcluster >-ssh.azurehdinsight.net sur le port 22 ou 23. Port 22 est utilisé pour vous connecter à la headnode principal, tandis que 23 est utilisé pour vous connecter à l’image secondaire. Pour plus d’informations sur les nœuds de tête, voir [la disponibilité et la fiabilité des groupes Hadoop dans un HDInsight](hdinsight-high-availability-linux.md).

    > [AZURE.NOTE] Vous pouvez accéder uniquement les nœuds de tête cluster via SSH à partir d’un ordinateur client. Une fois connecté, vous pouvez accéder puis les nœuds de travail à l’aide de SSH à partir d’un headnode.

## <a name="file-locations"></a>Emplacements des fichiers

Fichiers liés Hadoop sont accessibles sous les nœuds de cluster en `/usr/hdp`. Ce répertoire contient les sous-répertoires suivants :

* __2.2.4.9-1__: ce répertoire est nommé pour la version de la plateforme de données Hortonworks utilisé par HDInsight, afin que le numéro sur votre cluster peut être différent de celui répertorié ici.
* __en cours__: ce répertoire contient des liens vers des répertoires sous le répertoire __2.2.4.9-1__ et existe afin que vous n’êtes pas obligé de taper un numéro de version (variables), chaque fois que vous voulez accéder à un fichier.

FichiersJAR et les données d’exemple sont disponibles sur le système de fichier distribué Hadoop (HDFS) ou des objets Blob Azure stockage en ' / exemple » ou « wasbs : / / / exemple ».

## <a name="hdfs-azure-blob-storage-and-storage-best-practices"></a>HADOOP, le stockage Blob Azure et meilleures pratiques de stockage

Dans la plupart des répartitions Hadoop, HADOOP est sauvegardé par stockage local sur les ordinateurs du cluster. Bien que ce soit efficace, il peut être coûteux pour une solution sur le nuage l’endroit où vous êtes responsable de toutes les heures ou en minutes pour les ressources de calcul.

HDInsight utilise le stockage Blob Azure banque par défaut, qui offre les avantages suivants :

* Stockage à long terme bon marché

* Accessibilité à partir des services externes comme des sites Web, utilitaires de télécharger des fichiers, différents SDK langue et navigateurs web

Comme il est le magasin par défaut pour HDInsight, vous normalement n’avez rien à faire pour l’utiliser. Par exemple, la commande suivante permet de répertorier les fichiers dans le dossier **/example/data** , qui est stocké sur le stockage Blob Azure :

    hdfs dfs -ls /example/data

Certaines commandes peuvent nécessiter vous permettent de spécifier que vous utilisez stockage d’objets Blob. Dans ce cas, vous pouvez faire précéder la commande avec **wasb : / /**, ou **wasbs : / /**.

HDInsight vous permet également d’associer plusieurs comptes de stockage Blob à un cluster. Pour accéder aux données dans un compte de stockage Blob ceux par défaut, vous pouvez utiliser le format **wasbs : / /&lt;container-name>@&lt;nom du compte >.blob.core.windows.net/**. Par exemple, les éléments suivants pour afficher le contenu de l’annuaire **/example/data** pour le conteneur spécifié et le compte de stockage Blob :

    hdfs dfs -ls wasbs://mycontainer@mystorage.blob.core.windows.net/example/data

### <a name="what-blob-storage-is-the-cluster-using"></a>Quel type de stockage Blob utilise le cluster ?

Lors de la création de cluster, vous avez sélectionné utilisez un conteneur et un compte de stockage Azure existant ou créez-en un. Ensuite, vous avez probablement oublié à ce sujet. Vous pouvez trouver le compte de stockage par défaut et le conteneur à l’aide de l’API REST Ambari.

1. Utilisez la commande suivante pour récupérer des informations de configuration HADOOP à l’aide d’ourlée et filtrer à l’aide de [jq](https://stedolan.github.io/jq/):

        curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
    
    > [AZURE.NOTE] Ceci renvoie la première configuration appliquée au serveur (`service_config_version=1`,) qui contient ces informations. Si vous extrayez une valeur qui a été modifiée après la création d’un cluster, vous devrez répertorier les versions de configuration et de récupérer l’option dernière.

    Cela renverra une valeur semblable au suivant, où __conteneur__ est le conteneur par défaut et __nom de compte__ est le nom du compte de stockage Azure :

        wasbs://CONTAINER@ACCOUNTNAME.blob.core.windows.net

1. Obtenir le groupe de ressources pour le compte de stockage, utilisez la [Azure infrastructure du langage commun](../xplat-cli-install.md). Dans la commande suivante, remplacez le __nom de compte__ avec le nom du compte de stockage extrait Ambari :

        azure storage account list --json | jq '.[] | select(.name=="ACCOUNTNAME").resourceGroup'
    
    Elle retournera le nom du groupe de ressources pour le compte.
    
    > [AZURE.NOTE] Si rien n’est renvoyé à partir de cette commande, vous devrez peut-être modifier l’infrastructure du langage commun Azure en mode directeur des ressources Azure et exécutez à nouveau la commande. Pour passer en mode directeur des ressources Azure, utilisez la commande suivante.
    >
    > `azure config mode arm`
    
2. Obtenir la clé pour le compte de stockage. Remplacez le __nom de groupe__ par le groupe de ressources à partir de l’étape précédente. Remplacez le __nom de compte__ avec le nom du compte de stockage :

        azure storage account keys list -g GROUPNAME ACCOUNTNAME --json | jq '.[0].value'

    Ceci renvoie la clé primaire pour le compte.

Vous pouvez également trouver les informations de stockage à l’aide du portail Azure :

1. Dans le [Portail Azure](https://portal.azure.com/), sélectionnez votre cluster HDInsight.

2. Dans la section __Essentials__ , sélectionnez __tous les paramètres__.

3. Dans __paramètres__, sélectionnez __Clés de stockage Azure__.

4. À partir des __Clés de stockage Azure__, sélectionnez un des comptes de stockage répertoriés. Ceci affiche des informations sur le compte de stockage.

5. Sélectionnez l’icône de clé. Touches permettant de ce compte de stockage s’affiche.

### <a name="how-do-i-access-blob-storage"></a>Comment accéder aux stockage Blob ?

Autre que via la commande Hadoop du cluster, il existe de différentes façons pour accéder aux objets BLOB :

* [Infrastructure du langage commun pour Mac, Linux et Windows Azure](../xplat-cli-install.md): commandes de l’interface de ligne de commande pour l’utilisation de Azure. Après avoir installé, utilisez la `azure storage` commande de l’aide sur l’utilisation du stockage, ou `azure blob` pour des commandes spécifiques au blob.

* [blobxfer.py](https://github.com/Azure/azure-batch-samples/tree/master/Python/Storage): un python script permettant de manipuler des objets BLOB dans le stockage Azure.

* Une variété de SDK :

    * [Java](https://github.com/Azure/azure-sdk-for-java)

    * [Node.js](https://github.com/Azure/azure-sdk-for-node)

    * [PHP](https://github.com/Azure/azure-sdk-for-php)

    * [Python](https://github.com/Azure/azure-sdk-for-python)

    * [Ruby](https://github.com/Azure/azure-sdk-for-ruby)

    * [.NET](https://github.com/Azure/azure-sdk-for-net)

* [API REST de stockage](https://msdn.microsoft.com/library/azure/dd135733.aspx)

##<a name="scaling"></a>Mise à l’échelle votre cluster

Le cluster mise à l’échelle fonctionnalité permet de modifier le nombre de nœuds de données utilisées par un cluster qui s’exécute dans Azure HDInsight sans avoir à supprimer et recréer le cluster.

Vous pouvez effectuer les opérations mise à l’échelle lors de calendrier des tâches ou des processus en cours d’exécution sur un cluster.

Les types de cluster différents sont affectés par la mise à l’échelle comme suit :

* __Hadoop__: lorsqu’elle est mise à l’échelle vers le bas le nombre de nœuds dans un cluster, certains des services dans le cluster redémarrage. Cela peut entraîner des travaux en cours d’exécution ou en attente échec à la fin de l’opération de mise à l’échelle. Vous pouvez renvoyer les tâches une fois l’opération terminée.

* __HBase__: serveurs régionaux sont réparties automatiquement après quelques minutes après l’exécution de l’opération de mise à l’échelle. Pour équilibrer manuellement les serveurs régionaux, procédez comme suit :

    1. Connectez-vous au cluster HDInsight à l’aide de SSH. Pour plus d’informations sur l’utilisation de SSH avec HDInsight, voir un des documents suivants :

        * [Utiliser le protocole SSH avec HDInsight de Linux, Unix et Mac OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

        * [Utiliser le protocole SSH avec HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

    1. Utilisez ce qui suit pour démarrer l’interface HBase :

            hbase shell

    2. Une fois que le shell HBase a chargé, utilisez les éléments suivants permettent d’équilibrer manuellement les serveurs régionaux :

            balancer

* __Vague de__: vous devez rééquilibrer les topologies vague en cours d’exécution après qu’une opération de mise à l’échelle a été effectuée. Cela permet de la topologie d’ajustez les paramètres de parallélisme basés sur le nouveau nombre de nœuds dans le cluster à nouveau. Pour rééquilibrer topologies en cours d’exécution, utilisez une des options suivantes :

    * __SSH__: se connecter au serveur et utilisez la commande suivante pour rééquilibrer une topologie :

            storm rebalance TOPOLOGYNAME

        Vous pouvez également spécifier des paramètres pour remplacer les indications parallélisme initialement fournies par la topologie. Par exemple, `storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10` vais reconfigurer la topologie de 5 processus de travail, 3 exécuteurs pour le composant bleu bec et 10 exécuteurs pour le composant jaune boulons.

    * __Interface utilisateur vague de__: suivez les étapes suivantes pour rééquilibrer une topologie à l’aide de l’interface utilisateur vague de.

        1. Ouvrez __https://CLUSTERNAME.azurehdinsight.net/stormui__ dans votre navigateur web, où CLUSTERNAME est le nom de votre cluster vague. Si vous y êtes invité, entrez le nom d’administrateur (administrateur) cluster HDInsight et le mot de passe spécifié lors de la création du cluster.

        3. Sélectionnez la topologie que vous souhaitez rééquilibrer, puis sélectionnez le bouton __rééquilibrer__ . Entrez le délai avant que l’opération rééquilibrage est effectuée.

Pour plus d’informations sur la mise à l’échelle votre cluster HDInsight, voir :

* [Gérer les groupes Hadoop dans un HDInsight à l’aide du portail Azure](hdinsight-administer-use-portal-linux.md#scaling)

* [Gérer les groupes de Hadoop dans un HDinsight à l’aide de PowerShell Azure](hdinsight-administer-use-command-line.md#scaling)

## <a name="how-do-i-install-hue-or-other-hadoop-component"></a>Comment installer teinte (ou un autre composant Hadoop) ?

HDInsight est un service géré, ce qui signifie que les nœuds dans un cluster peuvent être destruction et configurés automatiquement Azure si un problème est détecté. Pour cette raison, il n’est pas recommandé pour installer manuellement des éléments directement dans les nœuds de cluster. À la place, utilisez [Actions de Script HDInsight](hdinsight-hadoop-customize-cluster.md) lorsque vous devez installer les éléments suivants :

* Un service ou un site web tel qu’explosion ou teinte.
* Un composant qui nécessite des modifications de configuration sur plusieurs nœuds dans le cluster. Par exemple, une variable d’environnement obligatoire, création d’un répertoire de journalisation, ou la création d’un fichier de configuration.

Actions de script sont scripts Bash qui sont exécutées pendant la mise en service de cluster et peuvent être utilisées pour installer et configurer des composants supplémentaires sur le cluster. Exemples de scripts sont fournis pour l’installation les composants suivants :

* [Teinte](hdinsight-hadoop-hue-linux.md)
* [Giraph](hdinsight-hadoop-giraph-install-linux.md)
* [Mode série sur LAN.r](hdinsight-hadoop-solr-install-linux.md)

Pour plus d’informations sur le développement de vos propres Actions de Script, consultez [développement de Script Action avec HDInsight](hdinsight-hadoop-script-actions-linux.md).

###<a name="jar-files"></a>Fichiers JAR

Certaines technologies Hadoop sont fournies dans fichiersJAR autonome qui contiennent des fonctions utilisées dans le cadre d’un travail MapReduce ou à partir d’à l’intérieur cochon ou Hive. Tandis que celles-ci peuvent être installés à l’aide des Actions de Script, ils souvent ne nécessitent pas tout le programme d’installation et peuvent uniquement être téléchargés au cluster après la mise en service et utilisés directement. Si vous souhaitez que le composant survit à créer de nouvelles images du cluster makle, vous pouvez stocker le fichier jar dans WASB.

Par exemple, si vous souhaitez utiliser la dernière version de [DataFu](http://datafu.incubator.apache.org/), vous pouvez télécharger un fichier jar contenant le projet et téléchargez-le sur le cluster HDInsight. Suivez ensuite la documentation DataFu sur l’utilisation de cochon ou Hive.

> [AZURE.IMPORTANT] Certains composants sont fichiersJAR autonome sont fournis avec HDInsight, mais ne sont pas dans le chemin d’accès. Si vous recherchez pour un composant spécifique, vous pouvez utiliser le suivi pour rechercher sur votre cluster :
>
> ```find / -name *componentname*.jar 2>/dev/null```
>
> Ceci renvoie le chemin d’accès de tous les fichiers jar correspondant.

Si le cluster fournit déjà une version d’un composant comme un fichierJAR autonome, mais que vous souhaitez utiliser une autre version, vous pouvez télécharger une nouvelle version du composant au cluster et essayez d’utiliser dans vos tâches.

> [AZURE.WARNING] Composants fournis avec le cluster HDInsight entièrement prises en charge et Support Microsoft aidera à identifier et résoudre les problèmes liés à ces composants.
>
> Composants personnalisés recevront RCSI prise en charge pour vous aider à résoudre le problème. Cela peut provoquer de résoudre le problème ou vous demandant de s’engager canaux disponibles pour les technologies d’ouvrir la source où se trouve une grande expertise pour que la technologie. Par exemple, de nombreux sites communautaires qui peuvent être utilisés, comme : [forum MSDN pour HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com). Projets Apache contient des sites de projet sur [http://apache.org](http://apache.org), par exemple : [Hadoop](http://hadoop.apache.org/), [commandé](http://spark.apache.org/).

## <a name="next-steps"></a>Étapes suivantes

* [Migrer à partir de HDInsight fonctionnant sous Windows vers basé sur Linux](hdinsight-migrate-from-windows-to-linux.md)
* [Utiliser Hive avec HDInsight](hdinsight-use-hive.md)
* [Utiliser cochon avec HDInsight](hdinsight-use-pig.md)
* [Utiliser des travaux MapReduce avec HDInsight](hdinsight-use-mapreduce.md)
