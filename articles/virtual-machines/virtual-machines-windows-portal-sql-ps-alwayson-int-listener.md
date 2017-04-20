<properties
   pageTitle="Configurer toujours dans le groupe de disponibilité récepteurs – Microsoft Azure"
   description="Configurer la disponibilité groupe récepteurs sur le modèle Azure le Gestionnaire de ressources, à l’aide d’un programme d’équilibrage de charge interne avec une ou plusieurs adresses IP."
   services="virtual-machines"
   documentationCenter="na"
   authors="MikeRayMSFT"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows-sql-server"
   ms.workload="infrastructure-services"
   ms.date="10/20/2016"
   ms.author="MikeRayMSFT"/>

# <a name="configure-one-or-more-always-on-availability-group-listeners---resource-manager"></a>Configurer un ou plusieurs toujours sur Disponibilité groupe récepteurs - Gestionnaire de ressources 

Cette rubrique vous explique comment effectuer les deux actions :

- Créer un équilibrage de charge interne pour les groupes de disponibilité de SQL Server à l’aide des applets de commande PowerShell.

- Ajouter des adresses IP supplémentaires à un équilibrage de charge pour prendre en charge plusieurs groupes de disponibilité de SQL Server. 

Dans SQL Server un récepteur de groupe disponibilité est un nom de réseau virtuel clients se connectent à afin d’accéder à une base de données du réplica principal ou secondaire. Sur des machines virtuelles Azure, un équilibrage de charge conserve l’adresse IP que l’auditeur. L’équilibrage de charge achemine le trafic vers l’instance de SQL Server est à l’écoute sur le port sonde. Dans la plupart des cas, un groupe de disponibilité utilise un programme d’équilibrage de charge interne. Un programme d’équilibrage de charge interne Azure peut héberger un ou plusieurs adresses IP. Chaque adresse IP utilise un port sonde spécifique. Ce document montre comment utiliser PowerShell pour créer un nouveau équilibrage de charge ou ajouter des adresses IP à un équilibrage de charge existant pour les groupes de disponibilité de SQL Server. 

La fonctionnalité permettant d’affecter plusieurs adresses IP pour un programme d’équilibrage de charge interne est une nouveauté de Azure et est disponible uniquement dans le Gestionnaire de ressources du modèle. Pour effectuer cette tâche, vous devez disposer d’un groupe de disponibilité de SQL Server déployé sur Azure machines virtuelles dans le Gestionnaire de ressources modèle. Les deux machines virtuelles SQL Server doit appartenir au même jeu de disponibilité. Vous pouvez utiliser le [modèle Microsoft](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) crée automatiquement le groupe de disponibilité dans le Gestionnaire de ressources Azure. Ce modèle crée automatiquement le groupe de disponibilité, y compris l’équilibrage de charge interne à votre place. Si vous préférez, vous pouvez [configurer manuellement un groupe de disponibilité AlwaysOn](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md).

Cette rubrique nécessite que vos groupes de disponibilité sont déjà configurées.  

Rubriques connexes sont les suivantes :

- [Configurer les groupes de disponibilité AlwaysOn dans Azure machine virtuelle (graphique)](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)   

- [Configurer une connexion VNet à VNet en utilisant le Gestionnaire de ressources Azure et PowerShell](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

[AZURE.INCLUDE [Start your PowerShell session](../../includes/sql-vm-powershell.md)]

## <a name="configure-the-windows-firewall"></a>Configurer le pare-feu Windows

Configurer le pare-feu Windows pour autoriser l’accès de SQL Server. Vous devrez configurer le pare-feu pour autoriser les connexions TCP à l’utilisation de ports par l’instance SQL Server, ainsi que le port utilisé par la sonde récepteur. Pour obtenir des instructions détaillées, consultez [configurer un pare-feu Windows pour l’accès au moteur de base de données](http://msdn.microsoft.com/library/ms175043.aspx#Anchor_1). Créer une règle de trafic entrant pour le port SQL Server et pour le port sonde.

## <a name="example-script-create-an-internal-load-balancer-with-powershell"></a>Exemple de Script : Créer un programme d’équilibrage de charge interne avec PowerShell

> [AZURE.NOTE] Si vous avez créé votre groupe de disponibilité avec le [modèle Microsoft](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) la charge vous n’avez pas besoin d’effectuer cette étape. 

Le script PowerShell suivant crée un programme d’équilibrage de charge interne, configure la règles d’équilibrage de charge et jeux d’une adresse IP pour l’équilibrage de charge. Pour exécuter le script, ouvrez Windows PowerShell ISE et collez le script dans le volet de Script. Utiliser `Login-AzureRMAccount` pour vous connecter à PowerShell. Si vous avez plusieurs abonnements Azure, utilisez `Select-AzureRmSubscription ` pour définir l’abonnement. 

```powershell
# Login-AzureRmAccount
# Select-AzureRmSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>

$ResourceGroupName = "<Resource Group Name>" # Resource group name
$VNetName = "<Virtual Network Name>"         # Virtual network name
$SubnetName = "<Subnet Name>"                # Subnet name
$ILBName = "<Load Balancer Name>"            # ILB name
$Location = "<Azure Region>"                 # Azure location
$VMNames = "<VM1>","<VM2>"                   # Virtual machine names

$ILBIP = "<n.n.n.n>"                         # IP address
[int]$ListenerPort = "<nnnn>"                # AG listener port
[int]$ProbePort = "<nnnn>"                   # Probe port

$LBProbeName ="ILBPROBE_$ListenerPort"       # The Load balancer Probe Object Name              
$LBConfigRuleName = "ILBCR_$ListenerPort"    # The Load Balancer Rule Object Name

$FrontEndConfigurationName = "FE_SQLAGILB_1" # Object name for the Front End configuration 
$BackEndConfigurationName ="BE_SQLAGILB_1"   # Object name for the Back End configuration

$VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName 

$Subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName 

$FEConfig = New-AzureRMLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.id

$BEConfig = New-AzureRMLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName 

$SQLHealthProbe = New-AzureRmLoadBalancerProbeConfig -Name $LBProbeName -Protocol tcp -Port $ProbePort -IntervalInSeconds 15 -ProbeCount 2

$ILBRule = New-AzureRmLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -Probe $SQLHealthProbe -Protocol tcp -FrontendPort $ListenerPort -BackendPort $ListenerPort -LoadDistribution Default -EnableFloatingIP 

$ILB= New-AzureRmLoadBalancer -Location $Location -Name $ILBName -ResourceGroupName $ResourceGroupName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -LoadBalancingRule $ILBRule -Probe $SQLHealthProbe 

$bepool = Get-AzureRmLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB 

foreach($VMName in $VMNames)
    {
        $VM = Get-AzureRmVM -ResourceGroupName $ResourceGroupName -Name $VMName 
        $NICName = ($VM.NetworkInterfaceIDs[0].Split('/') | select -last 1)
        $NIC = Get-AzureRmNetworkInterface -name $NICName -ResourceGroupName $ResourceGroupName
        $NIC.IpConfigurations[0].LoadBalancerBackendAddressPools = $BEPool
        Set-AzureRmNetworkInterface -NetworkInterface $NIC
        start-AzureRmVM -ResourceGroupName $ResourceGroupName -Name $VM.Name 
    }
```

## <a name="example-script-add-an-ip-address-to-an-existing-load-balancer-with-powershell"></a>Exemple de script : ajouter une adresse IP à un équilibrage de charge existant PowerShell

Pour utiliser plusieurs groupes de disponibilité, utiliser PowerShell pour ajouter une adresse IP supplémentaire à un équilibrage de charge existant. Chaque adresse IP requiert sa propre règle, sonde port et le port avant d’équilibrage de charge.

Le port frontal est le port que les applications utilisent pour vous connecter à l’instance SQL Server. Adresses IP pour les groupes de disponibilité différentes peuvent utiliser la même port frontal.

>[AZURE.NOTE] Pour les groupes de disponibilité de SQL Server, chaque adresse IP requiert un port sonde spécifique. Par exemple, si une seule adresse IP sur un équilibrage de charge utilise le port sonde 59999, aucuns autres adresses IP sur ce programme d’équilibrage de charge ne peuvent utiliser sonde port 59999. 

- Pour plus d’informations sur l’équilibrage de charge limites voir **privé front-end IP par programme d’équilibrage de charge** sous [Limites de mise en réseau - Azure le Gestionnaire de ressources](../azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits).

- Pour plus d’informations sur la disponibilité des limites de groupe voir [Restrictions (disponibilité groupes)](http://msdn.microsoft.com/library/ff878487.aspx#RestrictionsAG).

Le script suivant ajoute une nouvelle adresse IP à un équilibrage de charge existant. Mettre à jour les variables pour votre environnement. La ILB utilise le port d’écoute pour le port frontal équilibrage de charge. Ce port peut être le port écoute SQL Server. Instances par défaut de SQL Server, il s’agit de port 1433. La règle pour un groupe de disponibilité équilibrage nécessite une adresse IP flottante (serveur direct retour), le port de serveur principal est différente de celle dans le port frontal.

```powershell
# Login-AzureRmAccount
# Select-AzureRmSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>

$ResourceGroupName = "<ResourceGroup>"          # Resource group name
$VNetName = "<VirtualNetwork>"                  # Virtual network name
$SubnetName = "<Subnet>"                        # Subnet name
$ILBName = "<ILBName>"                          # ILB name                      

$ILBIP = "<n.n.n.n>"                            # IP address
[int]$ListenerPort = "<nnnn>"                   # AG listener port
[int]$ProbePort = "<nnnnn>"                     # Probe port 

$ILB = Get-AzureRmLoadBalancer -Name $ILBName -ResourceGroupName $ResourceGroupName 

$count = $ILB.FrontendIpConfigurations.Count+1
$FrontEndConfigurationName ="FE_SQLAGILB_$count"  

$LBProbeName = "ILBPROBE_$count"
$LBConfigrulename = "ILBCR_$count"

$VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName 
$Subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName

$ILB | Add-AzureRmLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.Id 

$ILB | Add-AzureRmLoadBalancerProbeConfig -Name $LBProbeName  -Protocol Tcp -Port $Probeport -ProbeCount 2 -IntervalInSeconds 15  | Set-AzureRmLoadBalancer 

$ILB = Get-AzureRmLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName

$FEConfig = get-AzureRMLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB

$SQLHealthProbe  = Get-AzureRmLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

$BEConfig = Get-AzureRmLoadBalancerBackendAddressPoolConfig -Name $ILB.BackendAddressPools[0].Name -LoadBalancer $ILB 

$ILB | Add-AzureRmLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig  -BackendAddressPool $BEConfig -Probe $SQLHealthProbe -Protocol tcp -FrontendPort  $ListenerPort -BackendPort $ListenerPort -LoadDistribution Default -EnableFloatingIP | Set-AzureRmLoadBalancer   
```



## <a name="configure-the-cluster-to-use-the-load-balancer-ip-address"></a>Configurer le cluster pour utiliser l’adresse IP d’équilibrage de la charge 

L’étape suivante consiste à configurer le récepteur sur le cluster et accédez à l’écoute en ligne. Pour ce faire, procédez comme suit : 

1. Créer le récepteur de groupe de disponibilité sur le cluster de basculement  

2. Mettre le récepteur en ligne

## <a name="1-create-the-availability-group-listener-on-the-failover-cluster"></a>1. créer le récepteur de groupe de disponibilité sur le cluster de basculement

Dans cette étape, vous ajoutez un point d’accès client pour le cluster de basculement avec le Gestionnaire de Cluster basculement et ensuite utilisez PowerShell pour configurer la ressource de cluster pour écouter le port sonde. 

1. Utiliser RDP pour vous connecter à la machine virtuelle Azure qui héberge réplica principal. 

2. Ouvrez le Gestionnaire de Cluster de basculement.

3. Sélectionnez le nœud **réseaux** et notez le nom du réseau cluster. Utilisez ce nom dans la `$ClusterNetworkName` variables dans le script PowerShell.

4. Développez le nom du cluster, puis cliquez sur **rôles**.

5. Dans le volet **rôles** , double-cliquez sur le nom de groupe de disponibilité, puis sélectionnez **Ajouter une ressource** > **Point d’accès Client**.

6. Dans la zone **nom** , créez un nom pour ce nouveau récepteur, puis cliquez deux fois sur **suivant** , puis cliquez sur **Terminer**. Ne mettez pas l’écoute ou la ressource en ligne à ce stade.
 
    Le nom pour le nouveau récepteur est le nom de réseau applications utiliseront pour vous connecter à des bases de données dans le groupe de disponibilité de SQL Server.

7. Cliquez sur l’onglet **ressources** , puis développez le Point d’accès Client que vous venez de créer. Avec le bouton droit de la ressource IP et cliquez sur Propriétés. Notez le nom de l’adresse IP. Vous allez utiliser ce nom dans la `$IPResourceName` variables dans le script PowerShell.

8. Sous **Adresse IP** cliquez sur **Adresse IP statique** et définissez l’adresse IP statique à la même adresse que vous avez utilisé lorsque vous définissez l’adresse IP d’équilibrage de la charge sur le portail Azure. 

9. Désactiver NetBIOS pour cette adresse, puis cliquez sur **OK**. Répétez cette étape pour chaque ressource IP si votre solution s’étend sur plusieurs VNets Azure. 

10. Rendez la ressource de groupe de disponibilité de SQL Server dépendant de l’adresse IP. Clic droit sur la ressource dans le Gestionnaire de cluster, il s’agit sous l’onglet **ressources** sous **Autres ressources**. 

11. Sur le nœud de cluster qui héberge actuellement réplica principal, ouvrez une élevés PowerShell ISE et collez les commandes suivantes dans un nouveau script. Sous l’onglet **dépendances** , cliquez sur le nom du récepteur.
 
    ```PowerShell
    $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
    $IPResourceName = "<IPResourceName>" # the IP Address resource name
    $ILBIP = “<n.n.n.n>” # the IP Address of the Internal Load Balancer (ILB). This is the static IP address for the load balancer you configured in the Azure portal.
    [int]$ProbePort = <nnnnn>

    Import-Module FailoverClusters
    
    Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
    ```
 
6. Mettre à jour les variables et exécuter le script PowerShell pour configurer l’adresse IP et le port pour le nouveau récepteur.

 >[AZURE.NOTE] Si vos serveurs SQL se trouvent dans des zones distinctes, vous devez exécuter le script PowerShell à deux reprises. La première fois utilisent le nom de réseau cluster, nom de la ressource cluster IP et chargez adresse IP équilibrage du premier groupe de ressources. La deuxième fois utilisent le nom de réseau cluster, nom de la ressource cluster IP et chargez adresse IP équilibrage du deuxième groupe de ressources.

Maintenant le cluster a une ressource de récepteur de groupe de disponibilité.

## <a name="2-bring-the-listener-online"></a>2. Mettez le récepteur en ligne

Avec la ressource récepteur disponibilité groupe configurée, vous pouvez replacer que l’auditeur en ligne afin que les applications peuvent se connecter aux bases de données dans le groupe de disponibilité avec le récepteur.

1. Accédez à basculement Cluster gestionnaire. Développez **rôles** , puis sélectionnez votre groupe de disponibilité. Sous l’onglet **ressources** , cliquez sur le nom du récepteur, puis cliquez sur **Propriétés**.

1. Cliquez sur l’onglet **dépendances** . S’il existe plusieurs ressources répertoriées, vérifiez que les adresses IP disposent ou non AND, dépendances. Cliquez sur **OK**.

1. Cliquez sur le nom du récepteur, puis cliquez sur **Mettre en ligne**.

1. Une fois que l’auditeur online, à partir de l’onglet **ressources** , droit du groupe de disponibilité, puis cliquez sur **Propriétés**.

1. Créer une dépendance sur la ressource de nom de récepteur (et non la IP adresse ressources nom). Cliquez sur **OK**.

1. Démarrez SQL Server Management Studio, puis se connecter à réplica principal.

1. Accédez à **la disponibilité AlwaysOn** | **groupes de disponibilité** | **écoute de groupe de disponibilité**. 

1. Vous devez maintenant voir le nom du récepteur que vous avez créé dans le Gestionnaire de Cluster basculement. Cliquez sur le nom du récepteur, puis cliquez sur **Propriétés**.

1. Dans la zone **Port** , spécifiez le numéro de port pour le récepteur de groupe de disponibilité à l’aide de la $EndpointPort vous avez utilisé auparavant (1433 a la valeur par défaut), puis cliquez sur **OK**.

Vous avez maintenant un groupe de disponibilité de SQL Server dans Azure machines virtuelles en cours d’exécution en mode Gestionnaire de ressources. 

## <a name="test-the-connection-to-the-listener"></a>Tester la connexion à l’écoute

Pour tester la connexion :

1. RDP sur un serveur SQL qui se trouve dans le même réseau virtuel, mais ne possède pas le réplica. Cela peut être autres SQL Server dans le cluster.

2. Utilitaire **sqlcmd** permet de tester la connexion. Par exemple, le script suivant établit une connexion **sqlcmd** à réplica principal via le récepteur avec l’authentification Windows :

    ```
    sqlmd -S <listenerName> -E
    ```

    Si le récepteur utilise un port autre que la valeur par défaut le port (1433), spécifiez le port dans la chaîne de connexion. Par exemple, la commande sqlcmd suivante se connecte à un récepteur au port 1435 : 
    
    ```
    sqlcmd -S <listenerName>,1435 -E
    ```

La connexion SQLCMD se connecte automatiquement à n’importe quel instance de SQL Server héberge réplica principal. 

>[AZURE.NOTE] Vérifiez que le port que vous spécifiez est ouvert sur le pare-feu des deux serveurs SQL. Les deux serveurs nécessitent une règle de trafic entrant pour le port TCP que vous utilisez. Pour plus d’informations, voir [Ajouter ou modifier une règle de pare-feu](http://technet.microsoft.com/library/cc753558.aspx) . 

## <a name="guidelines-and-limitations"></a>Instructions et limitations

Notez les instructions suivantes écoute de groupe de disponibilité dans Azure à l’aide d’interne l’équilibrage de charge :

- Avec un programme d’équilibrage de charge interne vous accédez uniquement le récepteur au sein du même réseau virtuel.

## <a name="for-more-information"></a>Pour plus d’informations

Pour plus d’informations, voir [configurer toujours sur disponibilité Regrouper dans Azure machine virtuelle manuellement](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md).

### <a name="powershell-cmdlets"></a>Applets de commande PowerShell

Utiliser les applets de commande PowerShell suivante pour créer un équilibrage de charge interne pour les machines virtuelles Azure.

- `New-AzureRmLoadBalancer`Crée un programme d’équilibrage de charge. Pour plus d’informations, consultez [New-AzureRmLoadBalancer](http://msdn.microsoft.com/library/mt619450.aspx) . 

- `New-AzureRMLoadBalancerFrontendIpConfig`Crée une configuration IP frontale pour un équilibrage de charge. Pour plus d’informations, consultez [New-AzureRMLoadBalancerFrontendIpConfig](http://msdn.microsoft.com/library/mt603510.aspx) .

- `New-AzureRmLoadBalancerRuleConfig`Crée une configuration de la règle pour un programme d’équilibrage de charge. Pour plus d’informations, consultez [New-AzureRmLoadBalancerRuleConfig](http://msdn.microsoft.com/library/mt619391.aspx) . 

- `New-AzureRMLoadBalancerBackendAddressPoolConfig`Crée une configuration du pool adresse principal pour un équilibrage de charge. Pour plus d’informations, consultez [New-AzureRmLoadBalancerBackendAddressPoolConfig](http://msdn.microsoft.com/library/mt603791.aspx) . 

- `New-AzureRmLoadBalancerProbeConfig`Crée une configuration sonde pour un équilibrage de charge. Pour plus d’informations, consultez [New-AzureRmLoadBalancerProbeConfig](http://msdn.microsoft.com/library/mt603847.aspx) .

Si vous devez supprimer un équilibrage de charge d’un groupe de ressources Azure, utilisez `Remove-AzureRmLoadBalancer`. Pour plus d’informations, voir [Supprimer AzureRmLoadBalancer](http://msdn.microsoft.com/library/mt603862.aspx).
