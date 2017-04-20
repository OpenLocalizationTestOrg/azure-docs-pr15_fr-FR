<properties
    pageTitle="Créer des clusters HBase dans un réseau virtuel | Microsoft Azure"
    description="Commencer à utiliser HBase dans Azure HDInsight. Apprenez à créer des clusters HDInsight HBase sur réseau virtuel Azure."
    keywords=""
    services="hdinsight,virtual-network"
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
   ms.date="10/18/2016"
   ms.author="jgao"/>

# <a name="create-hbase-clusters-in-azure-virtual-network"></a>Créer des clusters HBase dans réseau virtuel Azure 

Apprenez à créer des clusters Azure HDInsight HBase dans un [Réseau virtuel Azure][1].

Avec l’intégration des réseaux virtuels, clusters HBase peuvent être déployés au même réseau virtuel en tant que vos applications afin que les applications puissent communiquer avec HBase directement. Les avantages suivants :

- Connectivité directe de l’application web vers les nœuds du cluster HBase, qui permet de communication via la procédure distante HBase Java appeler API (RPC).
- Amélioration des performances en n’ayant ne pas le trafic de votre accédez sur plusieurs passerelles et des programmes d’équilibrage de charge.
- La possibilité de traitement des informations sensibles de manière plus sécurisée sans exposer un point de terminaison public.

###<a name="prerequisites"></a>Conditions préalables
Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- **Azure un abonnement**. Voir [Azure obtenir la version d’évaluation gratuite](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Un poste de travail avec Azure PowerShell**. Voir [installer et utiliser PowerShell Azure](https://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/). 

## <a name="create-hbase-cluster-into-virtual-network"></a>Créer HBase cluster en réseau virtuel

Dans cette section, vous allez créer un cluster basé sur Linux HBase avec le compte de stockage Azure dépendant dans un réseau virtuel Azure à l’aide d’un [modèle de gestionnaire de ressources Azure](../resource-group-template-deploy.md). Pour les autres méthodes de création de cluster et comprendre les paramètres, voir [créer HDInsight clusters](hdinsight-hadoop-provision-linux-clusters.md). Pour plus d’informations sur l’utilisation d’un modèle pour créer des groupes de Hadoop dans HDInsight, voir [Hadoop créer des groupes dans un HDInsight à l’aide de modèles Azure le Gestionnaire de ressources](hdinsight-hadoop-create-windows-clusters-arm-templates.md)

> [AZURE.NOTE] Certaines propriétés ont été codé en dur dans le modèle. Par exemple :
>
> * __Emplacement__: US Extrême-Orient 2
> * Version __Cluster : 3.4
> * __Nombre de nœuds cluster collaborateur__: 4
> * __Compte de stockage par défaut__: &lt;nom Cluster > stocker
> * __Nom du réseau virtuel__: &lt;nom Cluster >-vnet
> * __Espace d’adressage réseau virtuelle__: 10.0.0.0/16
> * __Nom du sous-réseau__: par défaut
> * __Plage d’adresses sous-réseau__: 10.0.0.0/24
>
> &lt;Nom de cluster > sera remplacé par le nom du cluster que vous avez défini lors de l’utilisation du modèle.

1. Cliquez sur l’image suivante pour ouvrir le modèle dans le portail Azure. Le modèle se trouve dans un conteneur blob public. 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hbase-cluster-in-vnet.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. À partir de la carte de **déploiement personnalisée** , entrez les informations suivantes :

    - **Abonnement**: sélectionnez un abonnement Azure utilisé pour créer le cluster HDInsight, le compte de stockage dépendant et le réseau virtuel Azure.
    - **Groupe de ressources**: sélectionnez **Créer nouveau**, puis spécifiez un nouveau nom de groupe de ressources.
    - **Emplacement**: sélectionnez un emplacement pour le groupe de ressources.
    - **Nomducluster**: entrez un nom pour le cluster Hadoop que vous allez créer.
    - **Mot de passe et le nom de connexion cluster**: le nom de connexion par défaut est **admin**.
    - **Mot de passe et nom d’utilisateur SSH**: le nom d’utilisateur par défaut est **sshuser**.  Vous pouvez la renommer. 
    - **J’accepte les termes et les conditions indiquées ci-dessus**: (sélectionnez)
    

3. Cliquez sur **acheter**. Il est nécessaire à environ 20 minutes pour créer un cluster. Une fois que le cluster est créé, vous pouvez cliquer sur la carte cluster dans le portail pour l’ouvrir.

Une fois que vous avez terminé le didacticiel, vous souhaiterez peut-être supprimer le cluster. Avec HDInsight, vos données sont stockées dans le stockage Azure, afin de supprimer en toute sécurité un cluster lorsqu’elle n’est pas en cours d’utilisation. Vous êtes également chargé pour un cluster de HDInsight, même lorsqu’elle n’est pas en cours d’utilisation. Dans la mesure où les frais pour le cluster sont plus autant de fois que les frais de stockage, il est préférable économique de supprimer clusters lorsqu’ils ne sont pas en cours d’utilisation. Pour obtenir des instructions de la suppression d’un cluster, voir [Hadoop gérer les groupes dans un HDInsight à l’aide du portail Azure](hdinsight-administer-use-management-portal.md#delete-clusters).

Pour commencer à utiliser avec votre nouveau cluster HBase, vous pouvez utiliser les procédures figurant dans [commencer à utiliser HBase avec Hadoop dans HDInsight](hdinsight-hbase-tutorial-get-started.md).

## <a name="connect-to-the-hbase-cluster-using-hbase-java-rpc-apis"></a>Connectez-vous au cluster HBase à l’aide des API de RPC HBase Java

1.  Créer une infrastructure comme une machine virtuelle service (IaaS) dans le même réseau virtuel Azure et le même sous-réseau. Pour obtenir des instructions sur la création d’un nouvel ordinateur virtuel IaaS, voir [créer une Machine virtuelle exécutant Windows Server](../virtual-machines/virtual-machines-windows-hero-tutorial.md). Lorsque vous suivez les étapes décrites dans ce document, vous devez utiliser ce qui suit pour la configuration du réseau :

    - __Réseau virtuel__: &lt;nom Cluster >-vnet
    - __Sous-réseau__: par défaut

    > [AZURE.IMPORTANT] Remplacer &lt;nom Cluster > avec le nom que vous avez utilisé lors de la création du cluster HDInsight des étapes précédentes.

    À l’aide de ces valeurs configurera la machine virtuelle pour utiliser le même réseau virtuel et sous-réseau que le cluster HDInsight. Cela leur permet de communiquer directement entre eux.

2.  Lorsque vous utilisez une application Java pour vous connecter à HBase à distance, vous devez utiliser le nom de domaine complet (FQDN). Pour cela, vous devez obtenir le suffixe DNS spécifique à la connexion du cluster HBase. Pour ce faire, vous pouvez utiliser une des méthodes suivantes :

    * Utiliser un navigateur Web pour passer un appel Ambari :
    
        Accédez à https://&lt;nom_cluster >.azurehdinsight.net/api/v1/clusters/&lt;nomcluster > / hosts?minimal_response = true. Il trouve un fichier JSON avec le suffixe DNS.

    * Utiliser le site Web Ambari :

        1. Accédez à https://&lt;nomcluster >. azurehdinsight.net.
        2. Dans le menu supérieur, cliquez sur **Hosts** .

    * Utiliser ourlée pour émettre des appels reste :

            curl -u <username>:<password> -k https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/hbase/components/hbrest

        Dans les données de la Notation JSON (JavaScript Object) renvoyées, recherchez l’entrée « host_name ». Ceci contient le nom de domaine complet pour les nœuds dans le cluster. Par exemple :

            ...
            "host_name": "wordkernode0.<clustername>.b1.cloudapp.net
            ...

        La partie de début de nom de domaine avec le nom du cluster est le suffixe DNS. Par exemple, mycluster.b1.cloudapp.net.

    * Utiliser PowerShell Azure
    
        Utilisez le script PowerShell Azure suivant pour inscrire la fonction **Get-ClusterDetail** , qui peut être utilisée pour renvoyer le suffixe DNS :

            function Get-ClusterDetail(
                [String]
                [Parameter( Position=0, Mandatory=$true )]
                $ClusterDnsName,
                [String]
                [Parameter( Position=1, Mandatory=$true )]
                $Username,
                [String]
                [Parameter( Position=2, Mandatory=$true )]
                $Password,
                [String]
                [Parameter( Position=3, Mandatory=$true )]
                $PropertyName
                )
            {
            <#
                .SYNOPSIS
                 Displays information to facilitate an HDInsight cluster-to-cluster scenario within the same virtual network.
                .Description
                 This command shows the following 4 properties of an HDInsight cluster:
                 1. ZookeeperQuorum (supports only HBase type cluster)
                    Shows the value of HBase property "hbase.zookeeper.quorum".
                 2. ZookeeperClientPort (supports only HBase type cluster)
                    Shows the value of HBase property "hbase.zookeeper.property.clientPort".
                 3. HBaseRestServers (supports only HBase type cluster)
                    Shows a list of host FQDNs that run the HBase REST server.
                 4. FQDNSuffix (supports all cluster types)
                    Shows the FQDN suffix of hosts in the cluster.
                .EXAMPLE
                 Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperQuorum
                 This command shows the value of HBase property "hbase.zookeeper.quorum".
                .EXAMPLE
                 Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperClientPort
                 This command shows the value of HBase property "hbase.zookeeper.property.clientPort".
                .EXAMPLE
                 Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName HBaseRestServers
                 This command shows a list of host FQDNs that run the HBase REST server.
                .EXAMPLE
                 Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName FQDNSuffix
                 This command shows the FQDN suffix of hosts in the cluster.
            #>

                $DnsSuffix = ".azurehdinsight.net"

                $ClusterFQDN = $ClusterDnsName + $DnsSuffix
                $webclient = new-object System.Net.WebClient
                $webclient.Credentials = new-object System.Net.NetworkCredential($Username, $Password)

                if($PropertyName -eq "ZookeeperQuorum")
                {
                    $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.quorum"
                    $Response = $webclient.DownloadString($Url)
                    $JsonObject = $Response | ConvertFrom-Json
                    Write-host $JsonObject.items[0].properties.'hbase.zookeeper.quorum'
                }
                if($PropertyName -eq "ZookeeperClientPort")
                {
                    $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.property.clientPort"
                    $Response = $webclient.DownloadString($Url)
                    $JsonObject = $Response | ConvertFrom-Json
                    Write-host $JsonObject.items[0].properties.'hbase.zookeeper.property.clientPort'
                }
                if($PropertyName -eq "HBaseRestServers")
                {
                    $Url1 = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.rest.port"
                    $Response1 = $webclient.DownloadString($Url1)
                    $JsonObject1 = $Response1 | ConvertFrom-Json
                    $PortNumber = $JsonObject1.items[0].properties.'hbase.rest.port'

                    $Url2 = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/hbase/components/hbrest"
                    $Response2 = $webclient.DownloadString($Url2)
                    $JsonObject2 = $Response2 | ConvertFrom-Json
                    foreach ($host_component in $JsonObject2.host_components)
                    {
                        $ConnectionString = $host_component.HostRoles.host_name + ":" + $PortNumber
                        Write-host $ConnectionString
                    }
                }
                if($PropertyName -eq "FQDNSuffix")
                {
                    $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/YARN/components/RESOURCEMANAGER"
                    $Response = $webclient.DownloadString($Url)
                    $JsonObject = $Response | ConvertFrom-Json
                    $FQDN = $JsonObject.host_components[0].HostRoles.host_name
                    $pos = $FQDN.IndexOf(".")
                    $Suffix = $FQDN.Substring($pos + 1)
                    Write-host $Suffix
                }
            }

        Après avoir exécuté le script PowerShell Azure, utilisez la commande suivante pour renvoyer le suffixe DNS à l’aide de la fonction **Get-ClusterDetail** . Indiquez votre nom de cluster HDInsight HBase, le nom et le mot de passe administrateur lorsque vous utilisez cette commande.

            Get-ClusterDetail -ClusterDnsName <yourclustername> -PropertyName FQDNSuffix -Username <clusteradmin> -Password <clusteradminpassword>

        Cette opération retourne le suffixe DNS. Par exemple, **yourclustername.b4.internal.cloudapp.net**.

    * Utiliser le protocole RDP
    
        Vous pouvez également utiliser Bureau à distance pour vous connecter au cluster HBase (vous serez connecté pour le nœud de tête) et exécutez **ipconfig** à partir d’une invite de commande pour obtenir le suffixe DNS. Pour des instructions sur l’activation de protocole RDP (Remote Desktop) et vous connecter au cluster en utilisant le protocole RDP, voir [Hadoop gérer les groupes dans un HDInsight à l’aide du portail Azure][hdinsight-admin-portal].
        
        ![hdinsight.hbase.DNS.surffix][img-dns-surffix]


<!--
3.  Change the primary DNS suffix configuration of the virtual machine. This enables the virtual machine to automatically resolve the host name of the HBase cluster without explicit specification of the suffix. For example, the *workernode0* host name will be correctly resolved to workernode0 of the HBase cluster.

    To make the configuration change:

    1. RDP into the virtual machine.
    2. Open **Local Group Policy Editor**. The executable is gpedit.msc.
    3. Expand **Computer Configuration**, expand **Administrative Templates**, expand **Network**, and then click **DNS Client**.
    - Set **Primary DNS Suffix** to the value obtained in step 2:

        ![hdinsight.hbase.primary.dns.suffix][img-primary-dns-suffix]
    4. Click **OK**.
    5. Reboot the virtual machine.
-->

Pour vérifier que la machine virtuelle peuvent communiquer avec le cluster HBase, utilisez la commande `ping headnode0.<dns suffix>` à partir de la machine virtuelle. Par exemple, ping headnode0.mycluster.b1.cloudapp.net.

Pour utiliser ces informations dans une application Java, vous pouvez suivre les étapes décrites dans [Utiliser Maven pour créer des applications Java qui utilisent HBase avec HDInsight (Hadoop)](hdinsight-hbase-build-java-maven.md) pour créer une application. Pour que l’application de se connecter à un serveur distant HBase, modifiez le fichier **hbase site.xml** dans cet exemple montre comment utiliser le nom de domaine complet pour soigneur. Par exemple :

    <property>
        <name>hbase.zookeeper.quorum</name>
        <value>zookeeper0.<dns suffix>,zookeeper1.<dns suffix>,zookeeper2.<dns suffix></value>
    </property>

> [AZURE.NOTE] Pour plus d’informations sur la résolution de noms dans Azure réseaux virtuels, y compris comment utiliser votre propre serveur DNS, voir [Résolution de noms (DNS)](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

##<a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris comment créer un cluster HBase. Pour plus d’informations, voir :

- [Prise en main HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md)
- [Configurer la réplication HBase dans HDInsight](hdinsight-hbase-geo-replication.md)
- [Créer des clusters Hadoop dans HDInsight](hdinsight-provision-clusters.md)
- [Commencer à utiliser HBase avec Hadoop dans HDInsight](hdinsight-hbase-tutorial-get-started.md)
- [Analyser opinion Twitter avec HBase dans HDInsight](hdinsight-hbase-analyze-twitter-sentiment.md)
- [Vue d’ensemble du réseau virtuel][vnet-overview]


[1]: http://azure.microsoft.com/services/virtual-network/
[2]: http://technet.microsoft.com/library/ee176961.aspx
[3]: http://technet.microsoft.com/library/hh847889.aspx

[hbase-get-started]: hdinsight-hbase-tutorial-get-started.md
[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
[vnet-overview]: ../virtual-network/virtual-networks-overview.md
[vm-create]: ../virtual-machines/virtual-machines-windows-hero-tutorial.md

[azure-portal]: https://portal.azure.com
[azure-create-storageaccount]: ../storage-create-storage-account.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md#rdp

[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx


[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter


[powershell-install]: powershell-install-configure.md


[hdinsight-customize-cluster]: hdinsight-hadoop-customize-cluster.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-storage-powershell]: ../hdinsight-hadoop-use-blob-storage.md#powershell
[hdinsight-analyze-flight-delay-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md
[hdinsight-hive-odbc]: hdinsight-connect-excel-hive-ODBC-driver.md
[hdinsight-hbase-replication-dns]: hdinsight-hbase-geo-replication-configure-DNS.md

[img-dns-surffix]: ./media/hdinsight-hbase-provision-vnet/DNSSuffix.png
[img-primary-dns-suffix]: ./media/hdinsight-hbase-provision-vnet/PrimaryDNSSuffix.png
[img-provision-cluster-page1]: ./media/hdinsight-hbase-provision-vnet/hbasewizard1.png "Détails de la mise en service pour le nouveau cluster HBase"
[img-provision-cluster-page5]: ./media/hdinsight-hbase-provision-vnet/hbasewizard5.png "Action de Script permet de personnaliser un cluster HBase"

[azure-preview-portal]: https://portal.azure.com
