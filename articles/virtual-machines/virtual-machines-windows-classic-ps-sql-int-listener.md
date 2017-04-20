<properties
    pageTitle="Configurer un récepteur ILB pour toujours sur les groupes de disponibilité | Microsoft Azure"
    description="Ce didacticiel utilise les ressources créées avec le modèle de déploiement classique et crée un toujours sur Disponibilité groupe récepteur dans Azure à l’aide d’un programme d’équilibrage de charge interne (ILB)."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="MikeRayMSFT"
    manager="jhubbard"
    editor=""
    tags="azure-service-management"/>
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="08/19/2016"
    ms.author="MikeRayMSFT" />

# <a name="configure-an-ilb-listener-for-always-on-availability-groups-in-azure"></a>Configurer un récepteur ILB pour toujours sur les groupes de disponibilité dans Azure

> [AZURE.SELECTOR]
- [Récepteur interne](virtual-machines-windows-classic-ps-sql-int-listener.md)
- [Récepteur externes](virtual-machines-windows-classic-ps-sql-ext-listener.md)

## <a name="overview"></a>Vue d’ensemble

Cette rubrique vous montre comment configurer un récepteur pour un groupe toujours sur disponibilité à l’aide d’un **Programme d’équilibrage de charge interne (ILB)**.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Pour configurer un récepteur ILB pour un groupe de disponibilité toujours dans le Gestionnaire de ressources modèle, voir [configurer un équilibrage de charge interne pour un groupe de disponibilité toujours sur dans Azure](virtual-machines-windows-portal-sql-alwayson-int-listener.md).


Votre groupe de disponibilité peuvent contenir des réplicas qui sont en local uniquement, Azure uniquement, ou s’étalent en local et Azure configurations hybrides. Réplicas Azure peuvent résider au sein de la même région ou entre plusieurs régions à l’aide de plusieurs réseaux virtuels (VNets). La procédure ci-dessous part du principe que vous avez déjà [configuré un groupe de disponibilité](virtual-machines-windows-classic-portal-sql-alwayson-availability-groups.md) mais que vous n’avez pas configuré un récepteur.

## <a name="guidelines-and-limitations-for-internal-listeners"></a>Instructions et limitations pour les récepteurs internes
Notez les instructions suivantes sur le récepteur de groupe disponibilité dans Azure à l’aide de ILB :

- Le récepteur de groupe disponibilité est pris en charge sur Windows Server 2008 R2, Windows Server 2012 et Windows Server 2012 R2.

- Un seul récepteur de groupe disponibilité interne est pris en charge par le service cloud, car le récepteur est configuré pour la ILB, et il y n'a qu’un seul ILB par service cloud. Toutefois, il est possible de créer plusieurs récepteurs externes. Pour plus d’informations, voir [configurer un récepteur externe pour toujours sur les groupes de disponibilité dans Azure](virtual-machines-windows-classic-ps-sql-ext-listener.md).

- Il n’est pas pris en charge pour créer un récepteur interne dans le service cloud même où vous disposez également d’un récepteur externe à l’aide de VIP publique du service cloud.

## <a name="determine-the-accessibility-of-the-listener"></a>Déterminer l’accessibilité de que l’auditeur

[AZURE.INCLUDE [ag-listener-accessibility](../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

Cet article se concentre sur la création d’un récepteur qui utilise un **Équilibrage de charge interne (ILB)**. Si vous avez besoin d’un récepteur public/externe, voir la version de cet article décrit les étapes nécessaires pour configurer un [récepteur externe](virtual-machines-windows-classic-ps-sql-ext-listener.md)

## <a name="create-load-balanced-vm-endpoints-with-direct-server-return"></a>Créer des points de terminaison de machine virtuelle équilibrage avec serveur direct retour

Pour ILB, vous devez d’abord créer l’équilibrage de charge interne. Pour cela, utilisez le script ci-dessous.

[AZURE.INCLUDE [load-balanced-endpoints](../../includes/virtual-machines-ag-listener-load-balanced-endpoints.md)]

1. Pour **ILB**, vous devez affecter une adresse IP statique. Tout d’abord, examinez la configuration actuelle de VNet en exécutant la commande suivante :

        (Get-AzureVNetConfig).XMLConfiguration

1. Notez le nom de **sous-réseau** pour le sous-réseau qui contient les ordinateurs virtuels hébergeant les réplicas. Cela sera utilisé dans le paramètre **$SubnetName** dans le script.

1. Puis notez le nom **VirtualNetworkSite** et le **AddressPrefix** départ pour le sous-réseau qui contient les ordinateurs virtuels hébergeant les réplicas. Recherchez une adresse IP disponible en passant les deux valeurs à la commande **AzureStaticVNetIP de Test** et en examinant **AvailableAddresses**. Par exemple, si le VNet a été nommé *MyVNet* et une adresse de plage qui ont démarré à *172.16.0.128*, la commande suivante aurait répertorier les adresses disponibles :

        (Test-AzureStaticVNetIP -VNetName "MyVNet"-IPAddress 172.16.0.128).AvailableAddresses

1. Choisissez une des adresses disponibles et l’utiliser dans le paramètre **$ILBStaticIP** du script suivant.

3. Copiez le script PowerShell ci-dessous dans un éditeur de texte et définissez les valeurs de variable en fonction de votre environnement (Notez que les valeurs par défaut ont été fournis pour certains paramètres). Notez que les déploiements existants qui utilisent affinités ne peut pas ajouter ILB. Pour plus d’informations sur la configuration requise ILB, voir [Équilibrage de charge interne](../load-balancer/load-balancer-internal-overview.md). En outre, si votre groupe de disponibilité s’étend sur Azure régions, vous devez exécuter une fois le script dans chaque centre de données pour le service cloud et les nœuds qui résident dans ce centre de données.

        # Define variables
        $ServiceName = "<MyCloudService>" # the name of the cloud service that contains the availability group nodes
        $AGNodes = "<VM1>","<VM2>","<VM3>" # all availability group nodes containing replicas in the same cloud service, separated by commas
        $SubnetName = "<MySubnetName>" # subnet name that the replicas use in the VNet
        $ILBStaticIP = "<MyILBStaticIPAddress>" # static IP address for the ILB in the subnet
        $ILBName = "AGListenerLB" # customize the ILB name or use this default value

        # Create the ILB
        Add-AzureInternalLoadBalancer -InternalLoadBalancerName $ILBName -SubnetName $SubnetName -ServiceName $ServiceName -StaticVNetIPAddress $ILBStaticIP

        # Configure a load balanced endpoint for each node in $AGNodes using ILB
        ForEach ($node in $AGNodes)
        {
            Get-AzureVM -ServiceName $ServiceName -Name $node | Add-AzureEndpoint -Name "ListenerEndpoint" -LBSetName "ListenerEndpointLB" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -InternalLoadBalancerName $ILBName -DirectServerReturn $true | Update-AzureVM
        }

1. Une fois que vous avez défini les variables, copiez le script à partir de l’éditeur de texte dans votre session PowerShell Azure pour l’exécuter. Si l’invite contient toujours >>, tapez entrer à nouveau pour vous assurer que le script démarre en cours d’exécution. Remarque

>[AZURE.NOTE] Le portail classique Azure ne reconnaît pas l’équilibrage de charge interne à ce stade, vous ne verrez pas le ILB ou les points de terminaison dans le portail classique Azure. Toutefois, **Get-AzureEndpoint** renvoie une adresse IP interne si l’équilibrage de charge est en cours d’exécution dessus. Sinon, elle retourne null.

## <a name="verify-that-kb2854082-is-installed-if-necessary"></a>Vérifiez que KB2854082 est installé si nécessaire

[AZURE.INCLUDE [kb2854082](../../includes/virtual-machines-ag-listener-kb2854082.md)]

## <a name="open-the-firewall-ports-in-availability-group-nodes"></a>Ouvrez les ports de pare-feu dans regrouper des nœuds disponibilité

[AZURE.INCLUDE [firewall](../../includes/virtual-machines-ag-listener-open-firewall.md)]

## <a name="create-the-availability-group-listener"></a>Créer le récepteur de groupe de disponibilité

[AZURE.INCLUDE [firewall](../../includes/virtual-machines-ag-listener-create-listener.md)]

1. Pour ILB, vous devez utiliser l’adresse IP de l’équilibrage de charge interne (ILB) créé précédemment. Utilisez le script suivant pour obtenir cette adresse IP dans PowerShell.

        # Define variables
        $ServiceName="<MyServiceName>" # the name of the cloud service that contains the AG nodes
        (Get-AzureInternalLoadBalancer -ServiceName $ServiceName).IPAddress

1. Sur l’un des ordinateurs virtuels, copiez le script PowerShell pour votre système d’exploitation dans un éditeur de texte et les variables comme les valeurs que vous avez noté précédemment.

    Pour Windows Server 2012 ou une version ultérieure, utilisez le script suivant :

        # Define variables
        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP Address resource name
        $ILBIP = “<X.X.X.X>” # the IP Address of the Internal Load Balancer (ILB)

        Import-Module FailoverClusters

        Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
        
    Windows Server 2008 R2, utilisez le script suivant :

        # Define variables
        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP Address resource name
        $ILBIP = “<X.X.X.X>” # the IP Address of the Internal Load Balancer (ILB)

        Import-Module FailoverClusters

        cluster res $IPResourceName /priv enabledhcp=0 address=$ILBIP probeport=59999  subnetmask=255.255.255.255
    

1. Une fois vous avez défini les variables, ouvrez une fenêtre de Windows PowerShell avec élévation de privilèges, puis copiez le script de l’éditeur de texte et collez dans votre session PowerShell Azure pour l’exécuter. Si l’invite contient toujours >>, tapez entrer à nouveau pour vous assurer que le script démarre en cours d’exécution.

2. Répétez cette procédure pour chaque machine virtuelle. Ce script configure la ressource adresse IP avec l’adresse IP du service cloud et affecte les autres paramètres tels que le port sonde. Lorsque la ressource adresse IP est mises en ligne, il peut ensuite répondre à l’interrogation sur le port sonde du point de terminaison équilibrage créé précédemment dans ce didacticiel.

## <a name="bring-the-listener-online"></a>Mettre le récepteur en ligne

[AZURE.INCLUDE [Bring-Listener-Online](../../includes/virtual-machines-ag-listener-bring-online.md)]

## <a name="follow-up-items"></a>Éléments de suivi

[AZURE.INCLUDE [Follow-up](../../includes/virtual-machines-ag-listener-follow-up.md)]

## <a name="test-the-availability-group-listener-within-the-same-vnet"></a>Tester le récepteur de groupe de disponibilité (au sein de la même VNet)

[AZURE.INCLUDE [Test-Listener-Within-VNET](../../includes/virtual-machines-ag-listener-test.md)]

## <a name="next-steps"></a>Étapes suivantes

[AZURE.INCLUDE [Listener-Next-Steps](../../includes/virtual-machines-ag-listener-next-steps.md)]
