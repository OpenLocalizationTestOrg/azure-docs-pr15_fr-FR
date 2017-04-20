<properties
   pageTitle="Surveiller et gérer les HDInsight à l’aide de l’API REST de Ambari Apache | Microsoft Azure"
   description="Découvrez comment utiliser Ambari pour surveiller et gérer des clusters HDInsight basé sur Linux. Dans ce document, vous allez apprendre à utiliser l’API REST Ambari inclus avec clusters HDInsight."
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
   ms.date="09/20/2016"
   ms.author="larryfr"/>

#<a name="manage-hdinsight-clusters-by-using-the-ambari-rest-api"></a>Gérer les clusters HDInsight à l’aide de l’API REST Ambari

[AZURE.INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Apache Ambari simplifie la gestion et la surveillance d’un cluster Hadoop en fournissant un facile à utiliser l’interface utilisateur web et des API REST. Ambari est inclus sur les clusters basés sur Linux HDInsight et sert à surveiller le cluster et apporter des modifications de configuration. Dans ce document, vous apprenez les bases de l’utilisation de l’API REST Ambari en effectuant des tâches courantes à l’aide d’ourlée.

##<a name="prerequisites"></a>Conditions préalables

* [coin](http://curl.haxx.se/): cURL est un utilitaire de disponibilité sur plusieurs plateformes pouvant être utilisées pour travailler avec des API REST à partir de la ligne de commande. Dans ce document, il est utilisé pour communiquer avec l’API REST Ambari.
* [jq](https://stedolan.github.io/jq/): jq est un utilitaire de ligne de commande de disponibilité sur plusieurs plateformes pour utiliser les documents JSON. Dans ce document, il est utilisé pour analyser les documents JSON retournées par l’API REST Ambari.
* [Azure infrastructure du langage commun](../xplat-cli-install.md): un utilitaire de ligne de commande plate-forme pour l’utilisation de services Azure.

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)] 

##<a id="whatis"></a>Quelles sont les Ambari ?

[Apache Ambari](http://ambari.apache.org) simplifie la gestion des Hadoop grâce à un site web facile à utiliser l’interface utilisateur pouvant être utilisées pour la mise en service, gérer et analyser les clusters Hadoop. Les développeurs peuvent intégrer ces fonctions dans leurs applications à l’aide de l' [API REST de Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

Ambari est fourni par défaut avec clusters HDInsight basé sur Linux.

##<a name="rest-api"></a>API REST

URI de base pour l’API REST Ambari sur HDInsight est https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME, où __CLUSTERNAME__ est le nom de votre cluster.

> [AZURE.IMPORTANT] Tandis que le nom du cluster dans la partie du nom (FQDN) de domaine complet de l’URI (CLUSTERNAME.azurehdinsight.net) respecte la casse, d’autres occurrences dans l’URI respectent la casse. Par exemple, si votre cluster est nommé Mon_cluster, les éléments suivants sont des URI valides :
>
> `https://mycluster.azurehdinsight.net/api/v1/clusters/MyCluster`
> `https://MyCluster.azurehdinsight.net/api/v1/clusters/MyCluster`
>
> URI suivants renvoie une erreur, car la deuxième occurrence du nom n’est pas la casse.
>
> `https://mycluster.azurehdinsight.net/api/v1/clusters/mycluster`
> `https://MyCluster.azurehdinsight.net/api/v1/clusters/mycluster`

Connexion à Ambari sur HDInsight nécessite HTTPS. Lors de l’authentification de la connexion, vous devez utiliser le nom du compte d’administrateur (la valeur par défaut est __admin__) et mot de passe fourni lorsque le cluster a été créé.

L’exemple suivant utilise ourlée pour effectuer une demande GET par rapport à l’API REST. Remplacez le __mot de passe__ par le mot de passe d’administrateur pour le cluster. Remplacez __NOMDUCLUSTER__ par le nom du cluster :

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME"
    
La réponse est un document JSON qui contient des informations similaires à ce qui suit :

    {
    "href" : "http://10.0.0.10:8080/api/v1/clusters/CLUSTERNAME",
    "Clusters" : {
        "cluster_id" : 2,
        "cluster_name" : "CLUSTERNAME",
        "health_report" : {
        "Host/stale_config" : 0,
        "Host/maintenance_state" : 0,
        "Host/host_state/HEALTHY" : 7,
        "Host/host_state/UNHEALTHY" : 0,
        "Host/host_state/HEARTBEAT_LOST" : 0,
        "Host/host_state/INIT" : 0,
        "Host/host_status/HEALTHY" : 7,
        "Host/host_status/UNHEALTHY" : 0,
        "Host/host_status/UNKNOWN" : 0,
        "Host/host_status/ALERT" : 0

Dans la mesure où il s’agit de JSON, il est plus facile à utiliser un analyseur JSON pour travailler avec les données. Par exemple, l’exemple suivant utilise jq pour afficher uniquement les `health_report` élément.

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME" | jq '.Clusters.health_report'

##<a name="example-get-the-fqdn-of-cluster-nodes"></a>Exemple : Obtenir le nom de domaine complet des nœuds de cluster

Lorsque vous travaillez avec HDInsight, vous devrez peut-être connaître le nom de domaine complet (FQDN) d’un nœud de cluster. Vous pouvez facilement extraire le nom de domaine complet pour différents nœuds dans le cluster en utilisant les éléments suivants :

* __Nœuds de tête__:`curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/NAMENODE" | jq '.host_components[].HostRoles.host_name'`
* __Nœuds de travail__:`curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/DATANODE" | jq '.host_components[].HostRoles.host_name'`
* __Nœuds soigneur__:`curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | jq '.host_components[].HostRoles.host_name'`

Remarque que chacun de ces exemples suivre le même modèle :

1. Un composant à notre connaissance de requête s’exécute sur les nœuds.
2. Récupérer les `host_name` éléments qui contiennent le nom de domaine complet pour ces nœuds.

La `host_components` élément du document retour contient plusieurs éléments. À l’aide de `.host_components[]`, puis spécifiez un chemin d’accès au sein de l’élément va boucle sur chaque élément et extraire la valeur du chemin d’accès spécifique. Si vous voulez uniquement une valeur, comme la première entrée de nom de domaine complet, vous pouvez renvoyer les éléments sous la forme d’une collection de sites, puis sélectionnez une entrée spécifique :

    jq '[.host_components[].HostRoles.host_name][0]'

Cela renvoie le premier nom de domaine complet de la collection.

##<a name="example-get-the-default-storage-account-and-container"></a>Exemple : Obtenir le compte de stockage par défaut et le conteneur

Lorsque vous créez un cluster de HDInsight, vous devez utiliser un compte de stockage Azure et un conteneur blob comme le stockage par défaut pour le cluster. Vous pouvez utiliser Ambari pour récupérer ces informations une fois que le cluster a été créé. Par exemple, si vous souhaitez par programme écrire des données directement dans le conteneur.

Récupère WASB URI de l’espace de stockage par défaut clusters les éléments suivants :

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
    
> [AZURE.NOTE] Retourne la première configuration appliquée au serveur (`service_config_version=1`,) qui contient ces informations. Si vous récupérez une valeur qui a été modifiée après la création d’un cluster, vous devrez répertorier les versions de configuration et de récupérer l’option dernière.

Cet exemple renvoie une valeur semblable à l’exemple suivant, où __conteneur__ est le conteneur par défaut et __nom de compte__ est le nom du compte de stockage Azure :

    wasbs://CONTAINER@ACCOUNTNAME.blob.core.windows.net

Vous pouvez ensuite utiliser ces informations avec l' [Infrastructure du langage commun Azure](../xplat-cli-install.md) pour charger ou télécharger des données à partir du conteneur.

1. Obtenir le groupe de ressources pour le compte de stockage. Remplacez le __nom de compte__ avec le nom du compte de stockage extrait Ambari :

        azure storage account list --json | jq '.[] | select(.name=="ACCOUNTNAME").resourceGroup'
    
    Retourne le nom du groupe de ressources pour le compte.
    
    > [AZURE.NOTE] Si rien n’est renvoyé à partir de cette commande, vous devrez peut-être modifier l’infrastructure du langage commun Azure en mode directeur des ressources Azure et exécutez à nouveau la commande. Pour passer en mode directeur des ressources Azure, utilisez la commande suivante :
    >
    > `azure config mode arm`
    
2. Obtenir la clé pour le compte de stockage. Remplacez le __nom de groupe__ par le groupe de ressources à partir de l’étape précédente. Remplacez le __nom de compte__ avec le nom du compte de stockage :

        azure storage account keys list -g GROUPNAME ACCOUNTNAME --json | jq '.storageAccountKeys.key1'

    Cet exemple renvoie la clé primaire pour le compte.
    
3. Pour stocker un fichier dans le conteneur, utilisez la commande de téléchargement :

        azure storage blob upload -a ACCOUNTNAME -k ACCOUNTKEY -f FILEPATH --container __CONTAINER__ -b BLOBPATH
        
    Remplacez le __nom de compte__ avec le nom du compte de stockage. Remplacez __ACCOUNTKEY__ par la clé récupérée précédemment. __Chemin d’accès__ est le chemin d’accès au fichier que vous voulez télécharger, tandis que __BLOBPATH__ est le chemin d’accès dans le conteneur.

    Par exemple, si vous souhaitez que le fichier apparaisse dans HDInsight en wasbs://example/data/filename.txt, __BLOBPATH__ être `example/data/filename.txt`.

##<a name="example-update-ambari-configuration"></a>Exemple : Configuration d’Ambari mise à jour

1. Obtenir la configuration en cours, qui stocke les Ambari comme étant la « configuration souhaitée » :

        curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME?fields=Clusters/desired_configs"
        
    Cet exemple renvoie un document JSON contenant la configuration en cours (identifiée par la valeur de _balise_ ) pour les composants installés sur le cluster. L’exemple suivant est un extrait les données renvoyées à partir d’un type de cluster explosion.
    
        "spark-metrics-properties" : {
            "tag" : "INITIAL",
            "user" : "admin",
            "version" : 1
        },
        "spark-thrift-fairscheduler" : {
            "tag" : "INITIAL",
            "user" : "admin",
            "version" : 1
        },
        "spark-thrift-sparkconf" : {
            "tag" : "INITIAL",
            "user" : "admin",
            "version" : 1
        }

    Dans cette liste, vous devez copier le nom du composant (par exemple, __explosion\_thrift\_sparkconf__ et la valeur de __balise__ .
    
2. Récupérer la configuration pour le composant et la balise à l’aide de la commande suivante. Remplacez __explosion-thrift-sparkconf__ et __initiale__ avec le composant et la balise que vous souhaitez récupérer la configuration de.

        curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations?type=spark-thrift-sparkconf&tag=INITIAL" | jq --arg newtag $(echo version$(date +%s%N)) '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
    
    Curl récupère le document JSON, puis jq est utilisé pour apporter des modifications aux données afin de créer un modèle. Le modèle est, utilisé pour ajouter/modifier des valeurs de configuration. Plus précisément, elle effectue les opérations suivantes :
    
    * Crée une valeur unique qui contient la chaîne « version » et la date, qui est stockée dans __newtag__.
    * Crée un document racine pour la nouvelle configuration de votre choix.
    * Obtenir le contenu de la `.items[]` de tableau et ajoute sous l’élément __desired_config__ .
    * Supprime la __href__, la __version__et éléments de __configuration__ , que ces éléments ne sont pas nécessaires à l’envoi d’une nouvelle configuration.
    * Ajoute un nouvel élément de __balise__ et lui affecte la valeur __version ###__. La partie numérique est basée sur la date du jour. Chaque configuration doit avoir une balise unique.
    
    Enfin, les données sont enregistrées dans le document __newconfig.json__ . La structure du document doit ressembler à l’exemple suivant :
    
        {
            "Clusters": {
                "desired_config": {
                "tag": "version1459260185774265400",
                "type": "spark-thrift-sparkconf",
                "properties": {
                    ....
                 },
                 "properties_attributes": {
                     ....
                 }
            }
        }

3. Ouvrez les valeurs de document et ajouter/modifier __newconfig.json__ dans l’objet de __Propriétés__ . L’exemple suivant modifie la valeur de __« spark.yarn.am.memory »__ de __« 1 »__ à __« 3 g »__ et et ajoute un nouvel élément pour __« spark.kryoserializer.buffer.max »__ avec une valeur de __« 256 m »__.

        "spark.yarn.am.memory": "3g",
        "spark.kyroserializer.buffer.max": "256m",

    Enregistrez le fichier une fois que vous avez terminé d’apporter de modifications.

4. Utilisez la commande suivante pour envoyer la configuration de mise à jour à Ambari.

        cat newconfig.json | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME"
        
    Cette commande injecte le contenu du fichier __newconfig.json__ à la demande ourlée, qui a été envoyé au cluster en tant que la nouvelle configuration de votre choix. La demande ourlée renvoie un document JSON. L’élément __versionTag__ dans ce document doit correspondre à la version que vous avez envoyé, et l’objet de __configurations__ contient les modifications de configuration souhaitée.

###<a name="example-restart-a-service-component"></a>Exemple : Redémarrer un composant de service

À ce stade, si vous observez l’interface utilisateur de Ambari web, le service d’explosion indiquera qu’il doit être redémarré pour que la nouvelle configuration prennent effet. Procédez comme suit pour redémarrer le service.

1. Pour activer le mode maintenance pour le service d’explosion, utilisez ce qui suit :

        echo '{"RequestInfo": {"context": "turning on maintenance mode for SPARK"},"Body": {"ServiceInfo": {"maintenance_state":"ON"}}}' | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SPARK"

    Cette commande envoie un document JSON au serveur (contenues dans les `echo` instruction,) qui transforme en mode maintenance.
    Vous pouvez vérifier que le service est maintenant en mode maintenance à l’aide de la requête suivante :
    
        curl -u admin:PASSWORD -H "X-Requested-By: ambari" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SPARK" | jq .ServiceInfo.maintenance_state
        
    Cela renverra une valeur de `"ON"`.

3. Ensuite, utilisez ce qui suit pour désactiver le service :

        echo '{"RequestInfo": {"context" :"Stopping the Spark service"}, "Body": {"ServiceInfo": {"state": "INSTALLED"}}}' | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SPARK"
        
    Cette commande renvoie une réponse semblable à celui-ci.
    
        {
            "href" : "http://10.0.0.18:8080/api/v1/clusters/CLUSTERNAME/requests/29",
            "Requests" : {
                "id" : 29,
                "status" : "Accepted"
            }
        }
    
    La `href` valeur renvoyée par cette URL est à l’aide de l’adresse IP interne du nœud cluster. Pour l’utiliser à partir de l’extérieur du cluster, remplacez la partie « 10.0.0.18:8080 » par le nom de domaine complet du cluster. Par exemple, la commande suivante extrait l’état de la demande.
    
        curl -u admin:PASSWORD -H "X-Requested-By: ambari" "https://CLUSTERNAME/api/v1/clusters/CLUSTERNAME/requests/29" | jq .Requests.request_status
    
    Si elle retourne une valeur de `"COMPLETED"` puis la demande est terminée.

4. Une fois la demande précédente terminée, utilisez ce qui suit pour démarrer le service.

        echo '{"RequestInfo": {"context" :"Restarting the Spark service"}, "Body": {"ServiceInfo": {"state": "STARTED"}}}' | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SPARK"

    Une fois que le service redémarre, il s’agit de nouveaux paramètres de configuration.

5. Enfin, utilisez les éléments suivants pour désactiver le mode maintenance.

        echo '{"RequestInfo": {"context": "turning off maintenance mode for SPARK"},"Body": {"ServiceInfo": {"maintenance_state":"OFF"}}}' | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SPARK"

##<a name="next-steps"></a>Étapes suivantes

Pour une référence complète de l’API REST, voir [Ambari API référence V1](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

> [AZURE.NOTE] Certaines fonctionnalités Ambari sont désactivée, il est géré par le service de nuage HDInsight ; par exemple, ajouter ou supprimer des hôtes dans le cluster ou ajout de nouveaux services.
