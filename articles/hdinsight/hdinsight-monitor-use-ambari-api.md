<properties
    pageTitle="Surveiller les groupes Hadoop dans un HDInsight à l’aide de l’API Ambari | Microsoft Azure"
    description="Utilisez des Apache Ambari APIs pour créer, gérer et analyse Hadoop clusters. Opérateur intuitif outils et API masquer la complexité de Hadoop."
    services="hdinsight"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian"
    editor="cgronlun"
    manager="jhubbard"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/10/2016"
    ms.author="jgao"/>

# <a name="monitor-hadoop-clusters-in-hdinsight-using-the-ambari-api"></a>Surveiller les groupes Hadoop dans un HDInsight à l’aide de l’API Ambari

Apprenez à analyser les HDInsight clusters à l’aide de Ambari APIs.

> [AZURE.NOTE] Les informations contenues dans cet article concerne principalement clusters HDInsight fonctionnant sous Windows, qui fournit une version de l’API REST Ambari en lecture seule. Pour les clusters basés sur Linux, voir [clusters Hadoop gérer à l’aide de Ambari](hdinsight-hadoop-manage-ambari.md).

## <a name="what-is-ambari"></a>Quelles sont les Ambari ?

[Apache Ambari] [ ambari-home] est utilisé pour la mise en service, la gestion et l’analyse des clusters Hadoop Apache. Il inclut une collection de sites intuitive des outils de l’opérateur et un ensemble puissant d’API masquer la complexité de Hadoop, ce qui simplifie le fonctionnement des clusters. Pour plus d’informations sur les API, voir [Référence de l’API Ambari][ambari-api-reference]. 

HDInsight prend actuellement en charge uniquement la fonctionnalité de surveillance Ambari. Ambari API 1.0 est pris en charge par les clusters de version 3.0 et 2.1 HDInsight. Cet article décrit l’accès aux APIs Ambari sur les clusters de version 3.1 et 2.1 HDInsight. La différence entre les deux clée est que certains composants ont changé avec l’introduction de nouvelles fonctionnalités (par exemple, le serveur de l’historique des travaux). 

**Conditions préalables**

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- **Un poste de travail avec Azure PowerShell**.

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

- (Facultatif) [coin] [curl]. Pour l’installer, voir [coin versions et téléchargements][curl-download].

    >[AZURE.NOTE] Quand utiliser la commande cURL dans Windows, utilisez des guillemets doubles au lieu de guillemets simples pour les valeurs d’option.

- **Cluster un Azure HDInsight**. Pour obtenir des instructions sur la mise en service de cluster, voir [prise en main à l’aide de HDInsight] [ hdinsight-get-started] ou [groupes de mise en service HDInsight][hdinsight-provision]. Vous devez les données suivantes pour parcourir le didacticiel :

    Propriété cluster|Nom de la variable de PowerShell Azure|Valeur|Description
    ---|---|---|---
    Nom du cluster HDInsight|$clusterName||Le nom de votre cluster HDInsight.
    Nom d’utilisateur de cluster|$clusterUsername||Nom d’utilisateur cluster spécifiée quand le cluster a été créé.
    Mot de passe cluster|$clusterPassword||Mot de passe utilisateur cluster.

    >[AZURE.NOTE] Remplir les valeurs du tableau. Il s’agit utile pour passant par ce didacticiel.

## <a name="jump-start"></a>Début de renvoi

Il existe plusieurs moyens d’utiliser Ambari pour analyser les clusters HDInsight.

**Utiliser PowerShell Azure**

Voici un script PowerShell Azure pour récupérer les informations de suivi du travail MapReduce *dans un cluster 3.1 HDInsight.*  La différence clée est que nous en continu ces détails par le service fils (plutôt que MapReduce).

    $clusterName = "<HDInsightClusterName>"
    $clusterUsername = "<HDInsightClusterUsername>"
    $clusterPassword = "<HDInsightClusterPassword>"

    $ambariUri = "https://$clusterName.azurehdinsight.net:443/ambari"
    $uriJobTracker = "$ambariUri/api/v1/clusters/$clusterName.azurehdinsight.net/services/yarn/components/resourcemanager"

    $passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
    $creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)

    $response = Invoke-RestMethod -Method Get -Uri $uriJobTracker -Credential $creds -OutVariable $OozieServerStatus

    $response.metrics.'yarn.queueMetrics'

Voici un script PowerShell Azure pour obtenir la MapReduce travail suivi des informations *dans un cluster HDInsight 2.1*:

    $clusterName = "<HDInsightClusterName>"
    $clusterUsername = "<HDInsightClusterUsername>"
    $clusterPassword = "<HDInsightClusterPassword>"

    $ambariUri = "https://$clusterName.azurehdinsight.net:443/ambari"
    $uriJobTracker = "$ambariUri/api/v1/clusters/$clusterName.azurehdinsight.net/services/mapreduce/components/jobtracker"

    $passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
    $creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)

    $response = Invoke-RestMethod -Method Get -Uri $uriJobTracker -Credential $creds -OutVariable $OozieServerStatus

    $response.metrics.'mapred.JobTracker'

Le résultat est :

![Sortie Jobtracker][img-jobtracker-output]

**Utiliser cURL**

Voici un exemple d’obtenir des informations de cluster à l’aide d’ourlée :

    curl -u <username>:<password> -k https://<ClusterName>.azurehdinsight.net:443/ambari/api/v1/clusters/<ClusterName>.azurehdinsight.net

Le résultat est :

    {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/",
     "Clusters":{"cluster_name":"hdi0211v2.azurehdinsight.net","version":"2.1.3.0.432823"},
     "services"[
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/services/hdfs",
        "ServiceInfo":{"cluster_name":"hdi0211v2.azurehdinsight.net","service_name":"hdfs"}},
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/services/mapreduce",
        "ServiceInfo":{"cluster_name":"hdi0211v2.azurehdinsight.net","service_name":"mapreduce"}}],
     "hosts":[
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/hosts/headnode0",
        "Hosts":{"cluster_name":"hdi0211v2.azurehdinsight.net",
                 "host_name":"headnode0"}},
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/hosts/workernode0",
        "Hosts":{"cluster_name":"hdi0211v2.azurehdinsight.net",
                 "host_name":"headnode0.{ClusterDNS}.azurehdinsight.net"}}]}

**Pour le 10/8/2014 mise à jour**:

Lorsque vous utilisez le point de terminaison Ambari, « https://{clusterDns}.azurehdinsight.net/ambari/api/v1/clusters/{clusterDns}.azurehdinsight.net/services/{servicename}/components/{componentname} », le champ *host_name* renvoie le nom de domaine complet (FQDN) du nœud plutôt que le nom d’hôte. Avant que la version 8/10/2014, cet exemple remis simplement «**headnode0**». Après la publication de 8/10/2014, vous obtenez le nom de domaine complet «**headnode0. {} .Azurehdinsight ClusterDNS} .net**«, comme illustré dans l’exemple précédent. Cette modification a été nécessaire pour faciliter des scénarios où plusieurs types de cluster (par exemple, HBase et Hadoop) peuvent être déployées dans un réseau virtuel (VNET). Cela se produit, par exemple, lorsque vous utilisez HBase comme une plateforme principale pour Hadoop.

## <a name="ambari-monitoring-apis"></a>Ambari API d’analyse

Le tableau suivant répertorie certains de la plus courante Ambari surveillance des appels de l’API. Pour plus d’informations sur l’API, voir [Référence de l’API Ambari][ambari-api-reference].

Appel d’API du moniteur|URI|Description
---|---|---
Obtenir des clusters|`/api/v1/clusters`|
Obtenir des informations de cluster.|`/api/v1/clusters/<ClusterName>.azurehdinsight.net`|clusters, aux services, hosts
Obtenir des services|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services`|Incluant des services : Hadoop, mapreduce
Obtenir des informations de services.|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>`|
Obtenir des composants de service|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>/components`|HADOOP : namenode, datanode<br/>MapReduce : jobtracker ; tasktracker
Obtenir des informations de composant.|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>/components/<ComponentName>`|ServiceComponentInfo, composants d’hôte, les mesures
Obtenir des hôtes|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts`|headnode0, workernode0
Obtenir des informations d’hôte.|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName>`|
Obtenir des composants d’hôte|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName>/host_components`|namenode, resourcemanager
Obtenir des informations de composant hôte.|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName>/host_components/<ComponentName>`|HostRoles, composant, hôte, indicateurs
Obtenir les configurations|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/configurations`|Types de configuration de la : core site, site Hadoop, mapred site, hive site
Obtenir des informations de configuration.|`/api/v1/clusters/<ClusterName>.azurehdinsight.net/configurations?type=<ConfigType>&tag=<VersionName>`|Types de configuration de la : core site, site Hadoop, mapred site, hive site


##<a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à utiliser Ambari surveillance des appels de l’API. Pour plus d’informations, voir :

- [Gérer les HDInsight à l’aide du portail Azure][hdinsight-admin-portal]
- [Gérer les HDInsight à l’aide de PowerShell Azure][hdinsight-admin-powershell]
- [Gérer les HDInsight à l’aide de ligne de commande interface][hdinsight-admin-cli]
- [Documentation HDInsight][hdinsight-documentation]
- [Prise en main HDInsight][hdinsight-get-started]



[ambari-home]: http://ambari.apache.org/
[ambari-api-reference]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md

[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[microsoft-hadoop-SDK]: http://hadoopsdk.codeplex.com/wikipage?title=Ambari%20Monitoring%20Client

[powershell-install]: powershell-install-configure.md
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-admin-cli]: hdinsight-administer-use-command-line.md
[hdinsight-documentation]: /documentation/services/hdinsight/
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-provision]: hdinsight-provision-clusters.md

[img-jobtracker-output]: ./media/hdinsight-monitor-use-ambari-api/hdi.ambari.monitor.jobtracker.output.png
