<properties
   pageTitle="Créer un équilibrage de charge interne à l’aide de PowerShell dans le Gestionnaire de ressources | Microsoft Azure"
   description="Apprenez à créer un équilibrage de charge interne à l’aide de PowerShell dans le Gestionnaire de ressources"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/24/2016"
   ms.author="sewhee" />

# <a name="create-an-internal-load-balancer-using-powershell"></a>Créer un programme d’équilibrage de charge interne à l’aide de PowerShell

[AZURE.INCLUDE [load-balancer-get-started-ilb-arm-selectors-include.md](../../includes/load-balancer-get-started-ilb-arm-selectors-include.md)]
<BR>
[AZURE.INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)][modèle de déploiement classique](load-balancer-get-started-ilb-classic-ps.md).

[AZURE.INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]


Les étapes suivantes expliquent comment créer un équilibrage de charge interne à l’aide du Gestionnaire de ressources Azure avec PowerShell. Avec le Gestionnaire de ressources Azure, les éléments pour créer un programme d’équilibrage de charge interne sont configurés individuellement et puis combinées afin de créer un programme d’équilibrage de charge.

Vous devez créer et configurer les objets suivants pour déployer un équilibrage de charge :

- Avant la configuration d’IP fin - configurera l’adresse IP privée pour le trafic réseau entrant
- Pool d’adresses principal - configure les interfaces réseau qui recevront le trafic d’équilibrage de charge en provenance de pool frontal d’IP
- Équilibrage de charge règles - source et configuration de voie locale pour l’équilibrage de charge.
- Teste - configure la sonde de l’état d’intégrité pour les instances de Machine virtuelle.
- NAT règles de trafic entrant - configure les règles de port pour accéder directement à une des instances Machine virtuelle.

Vous pouvez obtenir plus d’informations sur la charge composants équilibrage avec le Gestionnaire de ressources Azure au [Gestionnaire de ressources Azure prend en charge pour l’équilibrage de charge](load-balancer-arm.md).

Les étapes suivantes expliquent comment configurer un programme d’équilibrage de charge entre deux machines virtuelles.


## <a name="setup-powershell-to-use-resource-manager"></a>Le programme d’installation PowerShell pour utiliser le Gestionnaire de ressources

Vérifiez que vous avez la dernière version de production du module Azure pour PowerShell et que le programme d’installation PowerShell correctement accéder à votre abonnement Azure.

### <a name="step-1"></a>Étape 1

        Login-AzureRmAccount

### <a name="step-2"></a>Étape 2

Vérifier les abonnements du compte

        Get-AzureRmSubscription

Vous devrez authentifier avec vos informations d’identification.<BR>

### <a name="step-3"></a>Étape 3

Sélectionner les options de vos abonnements Azure à utiliser. <BR>


        Select-AzureRmSubscription -Subscriptionid "GUID of subscription"

### <a name="create-resource-group-for-load-balancer"></a>Créer un groupe ressource pour équilibrage de charge

### <a name="step-4"></a>Étape 4

Créer un nouveau groupe de ressources (ignorer cette étape si vous utilisez un groupe de ressources existant)

        New-AzureRmResourceGroup -Name NRP-RG -location "West US"

Le Gestionnaire de ressources Azure requiert que tous les groupes ressources spécifient un emplacement. Il est utilisé comme emplacement par défaut pour les ressources dans ce groupe de ressources. Vérifiez que toutes les commandes permettant de créer un programme d’équilibrage de charge utilisera le même groupe de ressources.

Dans l’exemple ci-dessus, nous avons créé un groupe de ressources appelé « NRP routage » et un emplacement « Ouest nous ».

## <a name="create-virtual-network-and-a-private-ip-address-for-front-end-ip-pool"></a>Créer le réseau virtuel et une adresse IP privée pour pool frontal d’IP


### <a name="step-1"></a>Étape 1

Crée un sous-réseau pour le réseau virtuel et l’assigne à variable $backendSubnet

    $backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24

Créer un réseau virtuel :

    $vnet= New-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet

Crée le réseau virtuel et ajoute le sous-réseau kg sous-réseau être au réseau virtuel NRPVNet et assigne à variable $vnet



## <a name="create-front-end-ip-pool-and-backend-address-pool"></a>Créer avant fin IP pool et pool d’adresses serveur principal

La configuration d’un pool frontal d’IP du entrants charge équilibrage réseau le trafic et serveur principal pool d’adresses recevoir la charge équilibrée le trafic.

### <a name="step-1"></a>Étape 1

Créer un pool frontal d’IP à l’aide de l’adresse IP privée 10.0.2.5 pour la 10.0.2.0/24 sous-réseau qui sera le point de terminaison le trafic réseau entrant.

    $frontendIP = New-AzureRmLoadBalancerFrontendIpConfig -Name LB-Frontend -PrivateIpAddress 10.0.2.5 -SubnetId $vnet.subnets[0].Id

### <a name="step-2"></a>étape 2

Configurer un pool d’adresses serveur principal utilisé pour recevoir le trafic entrant pool frontal d’IP :

    $beaddresspool= New-AzureRmLoadBalancerBackendAddressPoolConfig -Name "LB-backend"


## <a name="create-lb-rules-nat-rules-probe-and-load-balancer"></a>Créer des règles kg, règles NAT, sonde et l’équilibrage de charge

Après avoir créé le pool frontal d’IP et du pool d’adresses serveur principal, vous devez créer des règles qui appartiennent à la ressource d’équilibrage de charge :

### <a name="step-1"></a>Étape 1

    $inboundNATRule1= New-AzureRmLoadBalancerInboundNatRuleConfig -Name "RDP1" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389

    $inboundNATRule2= New-AzureRmLoadBalancerInboundNatRuleConfig -Name "RDP2" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3442 -BackendPort 3389

    $healthProbe = New-AzureRmLoadBalancerProbeConfig -Name "HealthProbe" -RequestPath "HealthProbe.aspx" -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

     $lbrule = New-AzureRmLoadBalancerRuleConfig -Name "HTTP" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80


L’exemple ci-dessus consiste à créer les éléments suivants :

- Règle NAT qui tout le trafic entrant sur le port 3441 est dirigés vers le port 3389.
- deuxième règle NAT qui tout le trafic entrant sur le port 3442 est dirigés vers le port 3389.
- une règle d’équilibrage de charge qui entraîne un chargement équilibrer le trafic tout trafic entrant sur le port public 80 au port local 80 dans le pool d’adresses principal.
- une règle de sonde qui doit vérifier l’état d’intégrité de chemin d’accès « HealthProbe.aspx »



### <a name="step-2"></a>Étape 2

Créer l’équilibrage de charge additionner tous les objets (règles NAT, règles d’équilibrage de charge, les configurations sonde) :

    $NRPLB = New-AzureRmLoadBalancer -ResourceGroupName "NRP-RG" -Name "NRP-LB" -Location "West US" -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule1,$inboundNatRule2 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe


## <a name="create-network-interfaces"></a>Créer des interfaces réseau

Après avoir créé l’équilibrage de charge internes, vous devez définir les interfaces réseau recevront le trafic réseau équilibré de charge règles NAT et sonde entrants. Dans ce cas, l’interface réseau est configuré individuellement et peut être affectée à une machine virtuelle ultérieurement.


### <a name="step-1"></a>Étape 1


Obtenir les ressources virtuel réseau et sous-réseau pour créer des interfaces réseau :

    $vnet = Get-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG

    $backendSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet


Cette étape permet de créer une interface de réseau qui appartiennent au pool de serveur principal équilibrage de la charge et associer la première règle NAT pour RDP pour cette interface réseau :

    $backendnic1= New-AzureRmNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic1-be -Location "West US" -PrivateIpAddress 10.0.2.6 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[0]

### <a name="step-2"></a>Étape 2

Créer une deuxième interface réseau appelée être kg-carte réseau 2 :

Cette étape crée une seconde interface réseau, affecter vers le même pool de serveur principal équilibrage de charge et associer la deuxième règle NAT créées pour RDP :

     $backendnic2= New-AzureRmNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic2-be -Location "West US" -PrivateIpAddress 10.0.2.7 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[1]

Le résultat final affiche les éléments suivants :

    $backendnic1

Résultat attendu :

    Name                 : lb-nic1-be
    ResourceGroupName    : NRP-RG
    Location             : westus
    Id                   : /subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
    Etag                 : W/"d448256a-e1df-413a-9103-a137e07276d1"
    ProvisioningState    : Succeeded
    Tags                 :
    VirtualMachine       : null
    IpConfigurations     : [
                         {
                           "PrivateIpAddress": "10.0.2.6",
                           "PrivateIpAllocationMethod": "Static",
                           "Subnet": {
                             "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/virtualNetworks/NRPVNet/subnets/LB-Subnet-BE"
                           },
                           "PublicIpAddress": {
                             "Id": null
                           },
                           "LoadBalancerBackendAddressPools": [
                             {
                               "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/backendAddressPools/LB-backend"
                             }
                           ],
                           "LoadBalancerInboundNatRules": [
                             {
                               "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/inboundNatRules/RDP1"
                             }
                           ],
                           "ProvisioningState": "Succeeded",
                           "Name": "ipconfig1",
                           "Etag": "W/\"d448256a-e1df-413a-9103-a137e07276d1\"",
                           "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be/ipConfigurations/ipconfig1"
                         }
                       ]
    DnsSettings          : {
                         "DnsServers": [],
                         "AppliedDnsServers": []
                       }
    AppliedDnsSettings   :
    NetworkSecurityGroup : null
    Primary              : False



### <a name="step-3"></a>Étape 3

Utilisez la commande Ajouter AzureRmVMNetworkInterface pour affecter la carte réseau pour une Machine virtuelle.

Vous trouverez les instructions étape par étape pour créer une machine virtuelle et attribuer à une carte réseau de la documentation : [créer une machine virtuelle Azure à l’aide de PowerShell](../virtual-machines/virtual-machines-windows-ps-create.md).

Si vous disposez déjà d’un ordinateur virtuel créé, vous pouvez ajouter l’interface du réseau avec les étapes suivantes :

#### <a name="step-1"></a>Étape 1

Charger la ressource d’équilibrage de charge dans une variable (si vous n’avez pas fait). La variable utilisée est appelée $lb et utiliser les mêmes noms à partir de la ressource équilibrage de charge créé ci-dessus.

    $lb= Get-AzureRmLoadBalancer –name NRP-LB -resourcegroupname NRP-RG

#### <a name="step-2"></a>Étape 2

Charger la configuration et à une variable.

    $backend= Get-AzureRmLoadBalancerBackendAddressPoolConfig -name backendpool1 -LoadBalancer $lb

#### <a name="step-3"></a>Étape 3

Charger l’interface réseau déjà créés dans une variable. le nom de la variable utilisé est $ carte réseau. Le nom d’interface réseau utilisé est celle de l’exemple ci-dessus.

    $nic=Get-AzureRmNetworkInterface –name lb-nic1-be -resourcegroupname NRP-RG

#### <a name="step-4"></a>Étape 4

Modifier la configuration du serveur principal dans l’interface réseau.

    $nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$backend

#### <a name="step-5"></a>Étape 5

Enregistrer l’objet interface réseau.

    Set-AzureRmNetworkInterface -NetworkInterface $nic

Après l’ajout d’une interface réseau au pool de serveur principal équilibrage de la charge, il commence à recevoir le trafic réseau basée sur la règles pour cette ressource d’équilibrage de charge d’équilibrage de charge.

## <a name="update-an-existing-load-balancer"></a>Mettre à jour un équilibrage de charge existant


### <a name="step-1"></a>Étape 1

À l’aide de l’équilibrage de charge de l’exemple ci-dessus, assigner objet équilibrage de charge à variable $slb à l’aide de Get-AzureRmLoadBalancer

    $slb=get-azureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG

### <a name="step-2"></a>Étape 2

Dans l’exemple suivant, vous allez ajouter une nouvelle règle NAT entrant à l’aide de port 81 affichée dans la partie frontale et port 8181 pour le pool principal à un équilibrage de charge existant

    $slb | Add-AzureRmLoadBalancerInboundNatRuleConfig -Name NewRule -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -FrontendPort 81  -BackendPort 8181 -Protocol Tcp


### <a name="step-3"></a>Étape 3

Enregistrer la nouvelle configuration à l’aide de jeu AzureLoadBalancer

    $slb | Set-AzureRmLoadBalancer

## <a name="remove-a-load-balancer"></a>Supprimer un programme d’équilibrage de charge

Utilisez la commande Supprimer AzureRmLoadBalancer pour supprimer un équilibrage de charge créé précédemment nommé « NRP kg » dans un groupe de ressources appelé « NRP routage »

    Remove-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG

>[AZURE.NOTE] Vous pouvez utiliser l’option Basculer - Force pour éviter l’invite pour la suppression.



## <a name="next-steps"></a>Étapes suivantes

[Configurer un mode de distribution d’équilibrage de charge](load-balancer-distribution-mode.md)

[Configurer les paramètres de délai d’expiration TCP inactives pour votre équilibrage de charge](load-balancer-tcp-idle-timeout.md)