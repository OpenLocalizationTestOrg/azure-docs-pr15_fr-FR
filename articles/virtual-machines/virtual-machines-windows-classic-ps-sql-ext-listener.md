<properties
    pageTitle="Configurer un récepteur externe pour toujours sur les groupes disponibilité | Microsoft Azure"
    description="Ce didacticiel vous guide tout au long des étapes de création d’un toujours sur Disponibilité groupe récepteur dans Azure est accessible en externe à l’aide de l’adresse IP virtuelle publique du service cloud associé."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="MikeRayMSFT"
    manager="jhubbard"
    editor=""
    tags="azure-service-management" />
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="07/12/2016"
    ms.author="MikeRayMSFT" />

# <a name="configure-an-external-listener-for-always-on-availability-groups-in-azure"></a>Configurer un récepteur externe pour toujours sur les groupes de disponibilité dans Azure

> [AZURE.SELECTOR]
- [Récepteur interne](virtual-machines-windows-classic-ps-sql-int-listener.md)
- [Récepteur externes](virtual-machines-windows-classic-ps-sql-ext-listener.md)

Cette rubrique vous montre comment configurer un récepteur pour un toujours sur groupe de disponibilité avec l’extérieur accessibles sur internet. Cela est possible en associant public Virtual (adresse du service cloud IP **VIP)** à l’écoute.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


Votre groupe de disponibilité peuvent contenir des réplicas qui sont en local uniquement, Azure uniquement, ou s’étalent en local et Azure configurations hybrides. Réplicas Azure peuvent résider au sein de la même région ou entre plusieurs régions à l’aide de plusieurs réseaux virtuels (VNets). La procédure ci-dessous part du principe que vous avez déjà [configuré un groupe de disponibilité](virtual-machines-windows-classic-portal-sql-alwayson-availability-groups.md) mais que vous n’avez pas configuré un récepteur.

## <a name="guidelines-and-limitations-for-external-listeners"></a>Instructions et limitations pour les récepteurs externes

Notez les instructions suivantes sur le récepteur de groupe disponibilité dans Azure lorsque vous déployez à l’aide de l’adresse VIP baise du cloud service :

- Le récepteur de groupe disponibilité est pris en charge sur Windows Server 2008 R2, Windows Server 2012 et Windows Server 2012 R2.

- L’application cliente doit se trouver sur un service cloud autre que celle qui contient votre groupe de disponibilité des machines virtuelles. Azure ne reconnaît pas retour serveur direct avec client et serveur dans le même service cloud.

- Par défaut, les étapes décrites dans cet article montrent comment configurer un récepteur pour utiliser l’adresse IP virtuelle du service cloud. Toutefois, il est possible réserver et créer plusieurs adresses VIP pour votre service cloud. Ainsi, utilisez les étapes décrites dans cet article pour créer plusieurs récepteurs qui sont associées à un autre VIP. Pour plus d’informations sur la création de plusieurs adresses VIP, voir [Plusieurs VIP par service cloud](../load-balancer/load-balancer-multivip.md).

- Si vous créez un récepteur pour un environnement hybride, le réseau local doit être connectés à l’Internet public outre le réseau privé virtuel de site à avec le réseau virtuel Azure. Dans le sous-réseau Azure, le récepteur de groupe disponibilité est accessible uniquement par l’adresse IP publique du service cloud correspondantes.

- Il n’est pas pris en charge pour créer un récepteur externe dans le service cloud même où vous disposez également d’un récepteur interne à l’aide de l’équilibrage de charge interne (ILB).

## <a name="determine-the-accessibility-of-the-listener"></a>Déterminer l’accessibilité de que l’auditeur

[AZURE.INCLUDE [ag-listener-accessibility](../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

Cet article se concentre sur la création d’un récepteur qui utilise **l’équilibrage de charge externe**. Si vous souhaitez un récepteur est privé à votre réseau virtuel, voir la version de cet article décrit les étapes nécessaires pour configurer un [récepteur à l’aide ILB](virtual-machines-windows-classic-ps-sql-int-listener.md)

## <a name="create-load-balanced-vm-endpoints-with-direct-server-return"></a>Créer des points de terminaison de machine virtuelle équilibrage avec serveur direct retour

Équilibrage de charge externe utilise le serveur virtuel l’adresse IP virtuelle publique du service cloud qui héberge vos ordinateurs virtuels. Si vous n’avez pas besoin créer ou configurer l’équilibrage de charge dans ce cas.

[AZURE.INCLUDE [load-balanced-endpoints](../../includes/virtual-machines-ag-listener-load-balanced-endpoints.md)]

1. Copiez le script PowerShell ci-dessous dans un éditeur de texte et définissez les valeurs de variable en fonction de votre environnement (les valeurs par défaut ont été fournies pour certains paramètres). Notez que si votre groupe de disponibilité s’étend sur Azure régions, vous devez exécuter le script une fois dans chaque centre de données pour le service cloud et les nœuds qui résident dans ce centre de données.

        # Define variables
        $ServiceName = "<MyCloudService>" # the name of the cloud service that contains the availability group nodes
        $AGNodes = "<VM1>","<VM2>","<VM3>" # all availability group nodes containing replicas in the same cloud service, separated by commas

        # Configure a load balanced endpoint for each node in $AGNodes, with direct server return enabled
        ForEach ($node in $AGNodes)
        {
            Get-AzureVM -ServiceName $ServiceName -Name $node | Add-AzureEndpoint -Name "ListenerEndpoint" -Protocol "TCP" -PublicPort 1433 -LocalPort 1433 -LBSetName "ListenerEndpointLB" -ProbePort 59999 -ProbeProtocol "TCP" -DirectServerReturn $true | Update-AzureVM
        }

1. Une fois que vous avez défini les variables, copiez le script à partir de l’éditeur de texte dans votre session PowerShell Azure pour l’exécuter. Si l’invite contient toujours >>, tapez entrer à nouveau pour vous assurer que le script démarre en cours d’exécution.

## <a name="verify-that-kb2854082-is-installed-if-necessary"></a>Vérifiez que KB2854082 est installé si nécessaire

[AZURE.INCLUDE [kb2854082](../../includes/virtual-machines-ag-listener-kb2854082.md)]

## <a name="open-the-firewall-ports-in-availability-group-nodes"></a>Ouvrez les ports de pare-feu dans regrouper des nœuds disponibilité

[AZURE.INCLUDE [firewall](../../includes/virtual-machines-ag-listener-open-firewall.md)]

## <a name="create-the-availability-group-listener"></a>Créer le récepteur de groupe de disponibilité

[AZURE.INCLUDE [firewall](../../includes/virtual-machines-ag-listener-create-listener.md)]

1. Pour l’équilibrage de charge externe, vous devez obtenir l’adresse IP virtuelle publique du service cloud qui contient vos duplications. Connectez-vous au portail classique Azure. Accédez au service de nuage qui contient votre groupe de disponibilité machine virtuelle. Ouvrez l’affichage **tableau de bord** .

3. Notez l’adresse indiquée sous **adresse IP virtuel Public (VIP)**. Si votre solution s’étend sur VNets, répétez cette étape pour chaque service en nuage qui contient un ordinateur virtuel qui héberge un réplica.

4. Sur l’un des ordinateurs virtuels, copiez le script PowerShell ci-dessous dans un éditeur de texte et les variables comme les valeurs que vous avez noté précédemment.

        # Define variables
        $ClusterNetworkName = "<ClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP Address resource name
        $CloudServiceIP = "<X.X.X.X>" # Public Virtual IP (VIP) address of your cloud service

        Import-Module FailoverClusters

        # If you are using Windows Server 2012 or higher, use the Get-Cluster Resource command. If you are using Windows Server 2008 R2, use the cluster res command. Both commands are commented out. Choose the one applicable to your environment and remove the # at the beginning of the line to convert the comment to an executable line of code.

        # Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$CloudServiceIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"OverrideAddressMatch"=1;"EnableDhcp"=0}
        # cluster res $IPResourceName /priv enabledhcp=0 overrideaddressmatch=1 address=$CloudServiceIP probeport=59999  subnetmask=255.255.255.255


1. Une fois vous avez défini les variables, ouvrez une fenêtre de Windows PowerShell avec élévation de privilèges, puis copiez le script de l’éditeur de texte et collez dans votre session PowerShell Azure pour l’exécuter. Si l’invite contient toujours >>, tapez entrer à nouveau pour vous assurer que le script démarre en cours d’exécution.

1. Répétez cette procédure pour chaque machine virtuelle. Ce script configure la ressource adresse IP avec l’adresse IP du service cloud et affecte les autres paramètres tels que le port sonde. Lorsque la ressource adresse IP est mises en ligne, il peut ensuite répondre à l’interrogation sur le port sonde du point de terminaison équilibrage créé précédemment dans ce didacticiel.

## <a name="bring-the-listener-online"></a>Mettre le récepteur en ligne

[AZURE.INCLUDE [Bring-Listener-Online](../../includes/virtual-machines-ag-listener-bring-online.md)]

## <a name="follow-up-items"></a>Éléments de suivi

[AZURE.INCLUDE [Follow-up](../../includes/virtual-machines-ag-listener-follow-up.md)]

## <a name="test-the-availability-group-listener-within-the-same-vnet"></a>Tester le récepteur de groupe de disponibilité (au sein de la même VNet)

[AZURE.INCLUDE [Test-Listener-Within-VNET](../../includes/virtual-machines-ag-listener-test.md)]

## <a name="test-the-availability-group-listener-over-the-internet"></a>Tester le récepteur de groupe de disponibilité (via internet)

Pour accéder à l’écoute d’en dehors du réseau virtuel, vous devez utiliser externe/public l’équilibrage de charge (décrites dans cette rubrique) plutôt que d’ILB, qui n’est accessible uniquement au sein de la même VNet. Dans la chaîne de connexion, vous spécifiez le nom du service cloud. Par exemple, si vous aviez un service cloud avec le nom *mycloudservice*, l’instruction sqlcmd se présentent comme suit :

    sqlcmd -S "mycloudservice.cloudapp.net,<EndpointPort>" -d "<DatabaseName>" -U "<LoginId>" -P "<Password>"  -Q "select @@servername, db_name()" -l 15

Contrairement à l’exemple précédent, l’authentification SQL doit être utilisée, car l’appelant ne peut pas utiliser l’authentification windows par le biais d’internet. Pour plus d’informations, voir [toujours dans le groupe de disponibilité dans Azure machine virtuelle : scénarios de connectivité Client](http://blogs.msdn.com/b/sqlcat/archive/2014/02/03/alwayson-availability-group-in-windows-azure-vm-client-connectivity-scenarios.aspx). Lorsque vous utilisez l’authentification SQL, assurez-vous que vous créez la connexion même sur les deux doubles. Pour plus d’informations sur la résolution des noms d’accès des groupes de disponibilité, voir [comment mapper les connexions ou utiliser contenues utilisateur de base de données SQL pour vous connecter à d’autres réplicas et établir une correspondance entre les bases de données de disponibilité](http://blogs.msdn.com/b/alwaysonpro/archive/2014/02/19/how-to-map-logins-or-use-contained-sql-database-user-to-connect-to-other-replicas-and-map-to-availability-databases.aspx).

Si les doubles toujours sur sont dans différents sous-réseaux, les clients doivent spécifier **MultisubnetFailover = True** dans la chaîne de connexion. Le résultat de tentatives de connexion en parallèle à réplicas dans différents sous-réseaux. Notez que ce scénario inclut un déploiement toujours dans le groupe de disponibilité entre région.

## <a name="next-steps"></a>Étapes suivantes

[AZURE.INCLUDE [Listener-Next-Steps](../../includes/virtual-machines-ag-listener-next-steps.md)]
