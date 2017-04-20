<properties
    pageTitle="Étendre HDInsight avec réseau virtuel | Microsoft Azure"  
    description="Découvrez comment utiliser le réseau virtuel Azure pour vous connecter HDInsight vers d’autres ressources de cloud ou ressources dans votre centre de données"
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/21/2016"
   ms.author="larryfr"/>


#<a name="extend-hdinsight-capabilities-by-using-azure-virtual-network"></a>Étendre les fonctionnalités de HDInsight à l’aide de réseau virtuel Azure

Azure réseau virtuel vous permet d’étendre vos solutions Hadoop pour incorporer des ressources locales telles que SQL Server, combiner plusieurs types de cluster HDInsight, ou pour créer des réseaux privés sécurisés entre les ressources dans le cloud.

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell-and-cli.md)]


##<a id="whatis"></a>Quel est le réseau virtuel Azure ?

[Azure réseau virtuel](https://azure.microsoft.com/documentation/services/virtual-network/) vous permet de créer un réseau sécurisé, permanent contenant les ressources que nécessaires pour votre solution. Un réseau virtuel vous permet de :

* Se connecter cloud des ressources dans un réseau privé (cloud uniquement).

    ![diagramme de configuration exclusivement le nuage](media/hdinsight-extend-hadoop-virtual-network/cloud-only.png)

    À l’aide de réseau virtuel pour lier des services Azure avec Azure HDInsight permet les scénarios suivants :

    * **Appel HDInsight services ou des tâches** à partir des sites Web Azure ou les services qui s’exécutent dans des machines virtuelles Azure.

    * **Transférer directement des données** entre HDInsight et base de données SQL Azure, SQL Server ou une autre solution de stockage de données s’exécutant sur une machine virtuelle.

    * **Combinaison de plusieurs serveurs HDInsight** en une solution. HDInsight clusters se présentent sous divers types, qui correspondent à la charge de travail ou la technologie réglé pour le cluster. Il n’est pas pris en charge de la méthode pour créer un cluster qui combine plusieurs types, tels que vague d’et HBase sur un cluster. L’utilisation d’un réseau virtuel permet multi-clusters directement communiquer avec eux.

* Se connecter à vos ressources cloud à votre réseau local centre de données (site à ou point-à-site) à l’aide d’un réseau privé virtuel (VPN).

    Configuration de site à vous permet de vous connecter plusieurs ressources à partir de votre centre de données au réseau virtuel Azure à l’aide d’un matériel VPN ou le service Routage et accès à distance.

    ![diagramme de configuration de site à](media/hdinsight-extend-hadoop-virtual-network/site-to-site.png)

    Configuration de point-à-site vous permet de vous connecter une ressource spécifique au réseau virtuel Azure à l’aide du logiciel VPN.

    ![diagramme de configuration du point-à-site](media/hdinsight-extend-hadoop-virtual-network/point-to-site.png)

    Réseau virtuel pour lier le cloud et votre centre de données permettent de scénarios similaires à la configuration exclusivement le nuage. Mais au lieu d’être limité à l’utilisation des ressources dans le cloud, vous pouvez également utiliser les ressources dans votre centre de données.

    * **Transférer directement des données** entre HDInsight et votre centre de données. Un exemple utilise Sqoop pour transférer des données vers ou à partir de SQL Server ou la lecture de données générées par une application (professionnelle)-professionnelles.

    * **Appel HDInsight services ou des tâches** à partir d’une application métier. Un exemple utilise HBase Java API pour stocker et récupérer des données à partir d’un cluster HDInsight HBase.

Pour plus d’informations sur les fonctionnalités, des avantages et des fonctions réseau virtuel, consultez la [vue d’ensemble du réseau virtuel Azure](../virtual-network/virtual-networks-overview.md).

> [AZURE.NOTE] Vous devez créer le réseau virtuel Azure avant la mise en service un cluster HDInsight. Pour plus d’informations, voir [tâches de configuration de réseau virtuel](https://azure.microsoft.com/documentation/services/virtual-network/).

## <a name="virtual-network-requirements"></a>Configuration requise de réseau virtuel

> [AZURE.IMPORTANT] Création d’un cluster HDInsight sur un réseau virtuel requiert des configurations réseau virtuel spécifiques, qui sont décrites dans cette section.

###<a name="location-based-virtual-networks"></a>Réseaux virtuelle emplacement

Azure HDInsight prend en charge uniquement les réseaux virtuels basés sur un emplacement et ne fonctionne pas avec les réseaux virtuels basées sur affinité du groupe.

###<a name="classic-or-v2-virtual-network"></a>Réseau virtuel classique ou v2

Clusters basés sur Windows nécessitent un réseau virtuel classique, tandis que clusters basés sur Linux requièrent un réseau virtuel du Gestionnaire de ressources Azure. Si vous n’avez pas le type correct d’un réseau, il ne sera pas utilisable lorsque vous créez le cluster.

Si vous disposez de ressources sur un réseau virtuel qui n’est pas utilisable par le cluster que vous prévoyez de création, vous pouvez créer un nouveau réseau virtuel utilisable par le cluster et vous connecter au réseau virtuel incompatible. Vous pouvez ensuite créer le cluster dans la version du réseau dont il a besoin, et il sera en mesure d’accéder aux ressources de l’autre réseau dans la mesure où les deux sont jointes. Pour plus d’informations sur la connexion classique et nouveaux réseaux virtuels, consultez [connexion classique VNets à nouveau VNets](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

###<a name="custom-dns"></a>Custom DNS

Lorsque vous créez un réseau virtuel, Azure fournit la résolution de noms par défaut pour Azure services tels que HDInsight qui sont installés sur le réseau. Toutefois, vous devrez peut-être utiliser votre propre système DNS (Domain Name) pour les situations telles que croisée résolution du nom de domaine réseau. Par exemple, lors de la communication entre les services situés dans deux jointes réseaux virtuels. HDInsight prend en charge à la fois la résolution de noms Azure par défaut ainsi que DNS personnalisé lorsqu’il est utilisé avec le réseau virtuel Azure.

Pour plus d’informations sur l’utilisation de votre serveur DNS avec réseau virtuel Azure, consultez la section __résolution de noms à l’aide de votre serveur DNS__ du document [Résolution de noms pour machines virtuelles et des Instances de rôle](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server) .

###<a name="secured-virtual-networks"></a>Réseaux virtuels sécurisées

Le service HDInsight est un service géré et requiert l’accès à Internet au cours de la mise en service et en cours d’exécution. Il s’agit donc ce Azure peut surveiller l’intégrité du cluster, déclencher le basculement des ressources de cluster, modifiez le nombre de nœuds dans le cluster à travers des opérations de mise à l’échelle et autres tâches de gestion.

Si vous avez besoin installer HDInsight dans un réseau virtuel sécurisé, vous devez autoriser l’accès entrant sur le port 443 pour les adresses IP suivantes, qui permettent de gérer le cluster HDInsight Azure.

* 168.61.49.99
* 23.99.5.239
* 168.61.48.131
* 138.91.141.162

Autoriser l’accès entrant à partir du port 443 pour ces adresses vous permettra d’installer correctement HDInsight dans un réseau virtuel sécurisé.

> [AZURE.IMPORTANT] HDInsight non prises en charge limiter le trafic sortant, que le trafic entrant. Lorsque vous définissez des règles de groupe de sécurité réseau pour le sous-réseau contenant HDInsight, utilisez uniquement les règles de trafic entrant.

Les exemples suivants montrent comment créer un nouveau groupe de sécurité réseau qui accorde les adresses nécessaires, puis s’applique au groupe de sécurité à un sous-réseau au sein de votre réseau virtuel. Ces étapes part du principe que vous avez déjà créé un réseau virtuel et sous-réseau que vous voulez installer HDInsight dans.

__À l’aide de PowerShell Azure__

    $vnetName = "Replace with your virtual network name"
    $resourceGroupName = "Replace with the resource group the virtual network is in"
    $subnetName = "Replace with the name of the subnet that HDInsight will be installed into"
    # Get the Virtual Network object
    $vnet = Get-AzureRmVirtualNetwork `
        -Name $vnetName `
        -ResourceGroupName $resourceGroupName
    # Get the region the Virtual network is in.
    $location = $vnet.Location
    # Get the subnet object
    $subnet = $vnet.Subnets | Where-Object Name -eq $subnetName
    # Create a new Network Security Group.
    # And add exemptions for the HDInsight health and management services.
    $nsg = New-AzureRmNetworkSecurityGroup `
        -Name "hdisecure" `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        | Add-AzureRmNetworkSecurityRuleConfig `
            -name "hdirule1" `
            -Description "HDI health and management address 168.61.49.99" `
            -Protocol "*" `
            -SourcePortRange "*" `
            -DestinationPortRange "443" `
            -SourceAddressPrefix "168.61.49.99" `
            -DestinationAddressPrefix "VirtualNetwork" `
            -Access Allow `
            -Priority 300 `
            -Direction Inbound `
        | Add-AzureRmNetworkSecurityRuleConfig `
            -Name "hdirule2" `
            -Description "HDI health and management 23.99.5.239" `
            -Protocol "*" `
            -SourcePortRange "*" `
            -DestinationPortRange "443" `
            -SourceAddressPrefix "23.99.5.239" `
            -DestinationAddressPrefix "VirtualNetwork" `
            -Access Allow `
            -Priority 301 `
            -Direction Inbound `
        | Add-AzureRmNetworkSecurityRuleConfig `
            -Name "hdirule3" `
            -Description "HDI health and management 168.61.48.131" `
            -Protocol "*" `
            -SourcePortRange "*" `
            -DestinationPortRange "443" `
            -SourceAddressPrefix "168.61.48.131" `
            -DestinationAddressPrefix "VirtualNetwork" `
            -Access Allow `
            -Priority 302 `
            -Direction Inbound `
        | Add-AzureRmNetworkSecurityRuleConfig `
            -Name "hdirule4" `
            -Description "HDI health and management 138.91.141.162" `
            -Protocol "*" `
            -SourcePortRange "*" `
            -DestinationPortRange "443" `
            -SourceAddressPrefix "138.91.141.162" `
            -DestinationAddressPrefix "VirtualNetwork" `
            -Access Allow `
            -Priority 303 `
            -Direction Inbound
    # Set the changes to the security group
    Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
    # Apply the NSG to the subnet
    Set-AzureRmVirtualNetworkSubnetConfig `
        -VirtualNetwork $vnet `
        -Name $subnetName `
        -AddressPrefix $subnet.AddressPrefix `
        -NetworkSecurityGroupId $nsg

__À l’aide de l’infrastructure du langage commun Azure__

1. Utilisez la commande suivante pour créer un nouveau groupe de sécurité réseau nommé `hdisecure`. Remplacez le groupe de ressources qui contient le réseau virtuel Azure et l’emplacement (région), le groupe créé dans __RESOURCEGROUPNAME__ et __l’emplacement__ .

        azure network nsg create RESOURCEGROUPNAME hdisecure LOCATION
    
    Une fois que le groupe a été créé, vous recevrez des informations sur le nouveau groupe. Recherchez une ligne similaire à ce qui suit et enregistrer le `/subscriptions/GUID/resourceGroups/RESOURCEGROUPNAME/providers/Microsoft.Network/networkSecurityGroups/hdisecure` informations. Il est utilisée dans une étape ultérieure.
    
        data:    Id                              : /subscriptions/GUID/resourceGroups/RESOURCEGROUPNAME/providers/Microsoft.Network/networkSecurityGroups/hdisecure

2. Utilisez ce qui suit pour ajouter des règles pour le nouveau groupe de sécurité réseau qui permettent la communication entrante sur le port 443 à partir du service de gestion et la santé Azure HDInsight. Remplacez __RESOURCEGROUPNAME__ par le nom du groupe de ressources qui contient le réseau virtuel Azure.

        azure network nsg rule create RESOURCEGROUPNAME hdisecure hdirule1 -p "*" -o "*" -u "443" -f "168.61.49.99" -e "VirtualNetwork" -c "Allow" -y 300 -r "Inbound"
        azure network nsg rule create RESOURCEGROUPNAME hdisecure hdirule2 -p "*" -o "*" -u "443" -f "23.99.5.239" -e "VirtualNetwork" -c "Allow" -y 301 -r "Inbound"
        azure network nsg rule create RESOURCEGROUPNAME hdisecure hdirule3 -p "*" -o "*" -u "443" -f "168.61.48.131" -e "VirtualNetwork" -c "Allow" -y 302 -r "Inbound"
        azure network nsg rule create RESOURCEGROUPNAME hdisecure hdirule4 -p "*" -o "*" -u "443" -f "138.91.141.162" -e "VirtualNetwork" -c "Allow" -y 303 -r "Inbound"

3. Une fois que la règle a été créée, utilisez ce qui suit pour appliquer le nouveau groupe de sécurité réseau à un sous-réseau. Remplacez __RESOURCEGROUPNAME__ par le nom du groupe de ressources qui contient le réseau virtuel Azure. Remplacez __VNETNAME__ et __SUBNETNAME__ par le nom du réseau virtuel Azure et le sous-réseau que vous utiliserez lors de l’installation HDInsight.

        azure network vnet subnet set RESOURCEGROUPNAME VNETNAME SUBNETNAME -w "/subscriptions/GUID/resourceGroups/RESOURCEGROUPNAME/providers/Microsoft.Network/networkSecurityGroups/hdisecure"
    
    Une fois cette commande terminée, vous pouvez installer HDInsight connecter au réseau virtuel sécurisé sur le sous-réseau utilisé dans ces étapes.

> [AZURE.IMPORTANT] À l’aide de l’accès uniquement ouvrir étapes ci-dessus pour le service de gestion et la santé HDInsight sur le nuage Azure. Cela vous permet d’installer correctement un cluster HDInsight dans le sous-réseau, alors que l’accès au cluster à partir de HDInsight en dehors du réseau virtuel est bloqué par défaut. Vous devrez ajouter des règles de groupe de sécurité réseau supplémentaires si vous souhaitez activer l’accès à partir de l’extérieur du réseau virtuel.
>
> Par exemple, pour autoriser l’accès SSH à partir d’internet, vous devez ajouter une règle similaire à ce qui suit : 
>
> * Azure PowerShell-```Add-AzureRmNetworkSecurityRuleConfig -Name "SSSH" -Description "SSH" -Protocol "*" -SourcePortRange "*" -DestinationPortRange "22" -SourceAddressPrefix "*" -DestinationAddressPrefix "VirtualNetwork" -Access Allow -Priority 304 -Direction Inbound```
> * Azure infrastructure du langage commun-```azure network nsg rule create RESOURCEGROUPNAME hdisecure hdirule4 -p "*" -o "*" -u "22" -f "*" -e "VirtualNetwork" -c "Allow" -y 304 -r "Inbound"```

Pour plus d’informations sur les groupes de sécurité réseau, voir [vue d’ensemble de groupes de sécurité réseau](../virtual-network/virtual-networks-nsg.md). Pour plus d’informations sur le contrôle de routage dans un réseau virtuel Azure, consultez [transfert IP et des itinéraires définies par l’utilisateur](../virtual-network/virtual-networks-udr-overview.md).

##<a id="tasks"></a>Tâches et des informations

Cette section contient des informations sur les tâches courantes et les informations que nécessaires lorsque vous utilisez HDInsight avec un réseau virtuel.

###<a name="determine-the-fqdn"></a>Déterminer le nom de domaine complet

Le cluster HDInsight doivent être affecté à un nom de domaine complet (FQDN) spécifique pour l’interface réseau virtuel. Il s’agit de l’adresse que vous devez utiliser lors de la connexion au cluster à partir d’autres ressources sur le réseau virtuel. Pour déterminer le nom de domaine complet, utilisez la suivante à URL pour interroger le service de gestion des Ambari :

    https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/<servicename>/components/<componentname>

> [AZURE.NOTE] Pour plus d’informations sur l’utilisation de Ambari avec HDInsight, voir [groupes Hadoop moniteur dans un HDInsight à l’aide de l’API Ambari](hdinsight-monitor-use-ambari-api.md).

Vous devez spécifier le nom du cluster et un service et le composant qui s’exécute sur le cluster, tels que le Gestionnaire de ressources fils.

> [AZURE.NOTE] Les données renvoyées sont un document Notation JSON (JavaScript Object) qui contient une grande quantité d’informations sur le composant. Pour extraire uniquement le nom de domaine complet, vous devez utiliser un analyseur JSON pour récupérer les `host_components[0].HostRoles.host_name` valeur.

Par exemple, pour renvoyer le nom de domaine complet à partir d’un cluster HDInsight Hadoop, vous pouvez utiliser une des méthodes suivantes pour extraire les données pour le Gestionnaire de ressources fils :

* [PowerShell Azure](../powershell-install-configure.md)

        $ClusterDnsName = <clustername>
        $Username = <cluster admin username>
        $Password = <cluster admin password>
        $DnsSuffix = ".azurehdinsight.net"
        $ClusterFQDN = $ClusterDnsName + $DnsSuffix

        $webclient = new-object System.Net.WebClient
        $webclient.Credentials = new-object System.Net.NetworkCredential($Username, $Password)

        $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/yarn/     components/resourcemanager"
        $Response = $webclient.DownloadString($Url)
        $JsonObject = $Response | ConvertFrom-Json
        $FQDN = $JsonObject.host_components[0].HostRoles.host_name
        Write-host $FQDN

* [coin](http://curl.haxx.se/) et [jq](http://stedolan.github.io/jq/)

        curl -G -u <username>:<password> https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/yarn/components/resourcemanager | jq .host_components[0].HostRoles.host_name

###<a name="connecting-to-hbase"></a>Connexion à HBase

Pour se connecter à HBase à distance à l’aide de l’API Java, vous devez déterminer les adresses de quorum soigneur pour le cluster HBase et indiquer dans votre application.

Pour obtenir la soigneur quorum adresse, utilisez une des méthodes suivantes pour interroger le service de gestion des Ambari :

* [PowerShell Azure](../powershell-install-configure.md)

        $ClusterDnsName = <clustername>
        $Username = <cluster admin username>
        $Password = <cluster admin password>
        $DnsSuffix = ".azurehdinsight.net"
        $ClusterFQDN = $ClusterDnsName + $DnsSuffix

        $webclient = new-object System.Net.WebClient
        $webclient.Credentials = new-object System.Net.NetworkCredential($Username, $Password)

        $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.quorum"
        $Response = $webclient.DownloadString($Url)
        $JsonObject = $Response | ConvertFrom-Json
        Write-host $JsonObject.items[0].properties.'hbase.zookeeper.quorum'

* [coin](http://curl.haxx.se/) et [jq](http://stedolan.github.io/jq/)

        curl -G -u <username>:<password> "https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.quorum" | jq .items[0].properties[]

> [AZURE.NOTE] Pour plus d’informations sur l’utilisation de Ambari avec HDInsight, voir [groupes Hadoop moniteur dans un HDInsight à l’aide de l’API Ambari](hdinsight-monitor-use-ambari-api.md).

Une fois que les informations de quorum, l’utiliser dans votre application cliente.

Par exemple, pour une application Java qui utilise l’API HBase, vous souhaitez ajouter un fichier **hbase site.xml** au projet et spécifier les informations de quorum dans le fichier comme suit :

```
<configuration>
  <property>
    <name>hbase.cluster.distributed</name>
    <value>true</value>
  </property>
  <property>
    <name>hbase.zookeeper.quorum</name>
    <value>zookeeper0.address,zookeeper1.address,zookeeper2.address</value>
  </property>
  <property>
    <name>hbase.zookeeper.property.clientPort</name>
    <value>2181</value>
  </property>
</configuration>
```

###<a name="verify-network-connectivity"></a>Vérifier la connectivité réseau

Certains services, telles que SQL Server, peuvent limiter les connexions réseau entrantes. Cela empêchera HDInsight de fonctionner correctement avec ces services.

Si vous rencontrez des problèmes d’accès à un service à partir de HDInsight, consultez la documentation de service pour vous assurer que vous avez activé l’accès réseau. Vous pouvez également vérifier l’accès réseau en créant une machine virtuelle Azure sur le même réseau virtuel et utilisez les utilitaires client pour vérifier que la machine virtuelle peut se connecter au service sur le réseau virtuel.

##<a id="nextsteps"></a>Étapes suivantes

Les exemples suivants montrent comment utiliser HDInsight avec réseau virtuel Azure :

* [Analyse des données capteur avec vague d’et HBase dans HDInsight](hdinsight-storm-sensor-data-analysis.md) - montre comment configurer un cluster vague d’et HBase dans un réseau virtuel, ainsi que comment écrire à distance vague de données sur HBase.

* [Groupes de mise en service Hadoop dans un HDInsight](hdinsight-hadoop-provision-linux-clusters.md) - fournit des informations sur la mise en service de clusters Hadoop, y compris des informations sur l’utilisation de réseau virtuel Azure.

* [Utiliser Sqoop avec Hadoop dans HDInsight](hdinsight-use-sqoop-mac-linux.md) - fournit des informations sur l’utilisation de Sqoop pour transférer des données avec SQL Server sur un réseau virtuel.

Pour en savoir plus sur les réseaux virtuels Azure, consultez la [vue d’ensemble du réseau virtuel Azure](../virtual-network/virtual-networks-overview.md).
