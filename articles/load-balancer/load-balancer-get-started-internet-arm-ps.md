<properties
   pageTitle="Créer un équilibrage de charge via Internet dans le Gestionnaire de ressources à l’aide de PowerShell | Microsoft Azure"
   description="Apprenez à créer un équilibrage de charge via Internet dans le Gestionnaire de ressources à l’aide de PowerShell"
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

# <a name="get-started"></a>Création d’un équilibrage de charge via Internet dans le Gestionnaire de ressources à l’aide de PowerShell

[AZURE.INCLUDE [load-balancer-get-started-internet-arm-selectors-include.md](../../includes/load-balancer-get-started-internet-arm-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Cet article décrit le modèle de déploiement du Gestionnaire de ressources. Vous pouvez également [découvrir comment créer un équilibrage de charge via Internet en utilisant le modèle de déploiement classique](load-balancer-get-started-internet-classic-cli.md).

[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## <a name="deploying-the-solution-by-using-azure-powershell"></a>Déploiement de la solution à l’aide de PowerShell Azure

Les procédures suivantes expliquent comment créer un équilibrage de charge via Internet à l’aide du Gestionnaire de ressources Azure avec PowerShell. Avec le Gestionnaire de ressources Azure, chaque ressource est créé et configuré individuellement et mettez ensuite ensemble pour créer un programme d’équilibrage de charge.

Vous devez créer et configurer les objets suivants pour déployer un équilibrage de charge :

- Configuration IP frontale : contient les adresses IP (PIP) publiques pour le trafic réseau entrant.
- Pool d’adresses principale : contient des interfaces réseau (cartes réseau) pour les ordinateurs virtuels recevoir le trafic réseau l’équilibrage de charge.
- Règles d’équilibrage de charge : contient des règles qui mapper un port public sur l’équilibrage de charge à un port du pool d’adresses principale.
- NAT règles de trafic entrant : contient des règles qui mapper un port public sur l’équilibrage de charge à un port pour une machine virtuelle spécifique du pool d’adresses principale.
- Sondes : contient sondes santé utilisées pour vérifier la disponibilité d’instances de machine virtuelle du pool d’adresses principale.

Pour plus d’informations, voir [Gestionnaire de ressources Azure prend en charge pour l’équilibrage de charge](load-balancer-arm.md).

## <a name="set-up-powershell-to-use-resource-manager"></a>Configurer PowerShell pour utiliser le Gestionnaire de ressources

Vérifiez que vous disposez de la dernière version de production du module Azure le Gestionnaire de ressources pour PowerShell :

1. Se connecter à Azure.

        Login-AzureRmAccount

    Entrez vos informations d’identification lorsque vous y êtes invité.

2. Vérifier les abonnements pour le compte.

        Get-AzureRmSubscription

3. Sélectionner les options de vos abonnements Azure à utiliser.

        Select-AzureRmSubscription -SubscriptionId 'GUID of subscription'

4. Créer un groupe de ressources. (Ignorer cette étape si vous utilisez un groupe de ressources existant).

        New-AzureRmResourceGroup -Name NRP-RG -location "West US"

## <a name="create-a-virtual-network-and-a-public-ip-address-for-the-front-end-ip-pool"></a>Créer un réseau virtuel et une adresse IP publique pour le pool frontal d’IP

1. Créer un sous-réseau et un réseau virtuel.

        $backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24
        New-AzureRmvirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet

2. Créer une Azure publique ressource adresse IP, nommée **PublicIP**, devant être utilisé par un pool frontal IP avec le nom DNS **loadbalancernrp.westus.cloudapp.azure.com**. La commande suivante utilise le type de répartition statique.

        $publicIP = New-AzureRmPublicIpAddress -Name PublicIp -ResourceGroupName NRP-RG -Location 'West US' –AllocationMethod Static -DomainNameLabel loadbalancernrp

    >[AZURE.IMPORTANT]L’équilibrage de charge utilise le nom de domaine de l’adresse IP publique comme un préfixe de son nom complet. Ceci est différent du modèle de déploiement classique, qui utilise le service en nuage comme l’équilibrage de charge nom de domaine complet.
    >Dans cet exemple, le nom de domaine complet est **loadbalancernrp.westus.cloudapp.azure.com**.

## <a name="create-a-front-end-ip-pool-and-a-back-end-address-pool"></a>Créer un pool frontal IP et un pool d’adresses principale

1. Créer un pool frontal d’IP nommé **Kg Frontend** qui utilise la ressource **PublicIp** .

        $frontendIP = New-AzureRmLoadBalancerFrontendIpConfig -Name LB-Frontend -PublicIpAddress $publicIP

2. Créer un pool principale adresse nommé **kg principal**.

        $beaddresspool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name LB-backend

## <a name="create-nat-rules-a-load-balancer-rule-a-probe-and-a-load-balancer"></a>Créer des règles NAT, une règle d’équilibrage de charge, une sonde et un équilibrage de charge

Cet exemple montre comment créer les éléments suivants :

- Une règle NAT pour traduire tout le trafic entrant sur le port 3441 vers Port3389
- Une règle NAT pour traduire tout le trafic entrant sur le port 3442 vers Port3389
- Une règle sonde pour vérifier l’état d’intégrité d’une page nommée **HealthProbe.aspx**
- Une règle d’équilibrage de charge pour équilibrer tout le trafic entrant sur le port 80 au port 80 sur les adresses dans la liste principale
- Un équilibrage de charge qui utilise tous ces objets

Suivez ces étapes :

1. Créez les règles NAT.

        $inboundNATRule1= New-AzureRmLoadBalancerInboundNatRuleConfig -Name RDP1 -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389

        $inboundNATRule2= New-AzureRmLoadBalancerInboundNatRuleConfig -Name RDP2 -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3442 -BackendPort 3389

2. Créer une sonde d’intégrité. Il existe deux façons de configurer une sonde :

    Sonde HTTP

        $healthProbe = New-AzureRmLoadBalancerProbeConfig -Name HealthProbe -RequestPath 'HealthProbe.aspx' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

    Sonde TCP

        $healthProbe = New-AzureRmLoadBalancerProbeConfig -Name HealthProbe -Protocol Tcp -Port 80 -IntervalInSeconds 15 -ProbeCount 2

3. Créer une règle d’équilibrage de charge.

        $lbrule = New-AzureRmLoadBalancerRuleConfig -Name HTTP -FrontendIpConfiguration $frontendIP -BackendAddressPool  $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80

4. Créer l’équilibrage de charge en utilisant les objets créés précédemment.

        $NRPLB = New-AzureRmLoadBalancer -ResourceGroupName NRP-RG -Name NRP-LB -Location 'West US' -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule1,$inboundNatRule2 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe

## <a name="create-nics"></a>Créer des cartes réseau

Créer des interfaces réseau (ou modifier des modèles existants), puis associez les règles, les règles d’équilibrage de charge et les sondes NAT :

1. Obtenir le réseau virtuel et un sous-masque réseau virtuel, où les cartes réseau doivent être créés.

        $vnet = Get-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG
        $backendSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet

2. Créer une carte réseau nommée **être kg-nic1**et l’associer à la première règle NAT et du pool d’adresses principale première (et uniquement).

        $backendnic1= New-AzureRmNetworkInterface -ResourceGroupName NRP-RG -Name lb-nic1-be -Location 'West US' -PrivateIpAddress 10.0.2.6 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[0]

3. Créer une carte réseau nommée **être kg-carte réseau 2**et l’associer à la deuxième règle NAT et du pool d’adresses principale première (et uniquement).

        $backendnic2= New-AzureRmNetworkInterface -ResourceGroupName NRP-RG -Name lb-nic2-be -Location 'West US' -PrivateIpAddress 10.0.2.7 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[1]

4. Vérifier les cartes réseau.

        $backendnic1

    Résultat attendu :

        Name                 : lb-nic1-be
        ResourceGroupName    : NRP-RG
        Location             : westus
        Id                   : /subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
        Etag                 : W/"d448256a-e1df-413a-9103-a137e07276d1"
        ResourceGuid         : 896cac4f-152a-40b9-b079-3e2201a5906e
        ProvisioningState    : Succeeded
        Tags                 :
        VirtualMachine       : null
        IpConfigurations     : [
                            {
                            "Name": "ipconfig1",
                            "Etag": "W/\"d448256a-e1df-413a-9103-a137e07276d1\"",
                            "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be/ipConfigurations/ipconfig1",
                            "PrivateIpAddress": "10.0.2.6",
                            "PrivateIpAllocationMethod": "Static",
                            "Subnet": {
                                "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/virtualNetworks/NRPVNet/subnets/LB-Subnet-BE"
                            },
                            "ProvisioningState": "Succeeded",
                            "PrivateIpAddressVersion": "IPv4",
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
                            "Primary": true,
                            "ApplicationGatewayBackendAddressPools": []
                            }
                        ]
        DnsSettings          : {
                            "DnsServers": [],
                            "AppliedDnsServers": [],
                            "InternalDomainNameSuffix": "prcwibzcuvie5hnxav0yjks2cd.dx.internal.cloudapp.net"
                        }
        EnableIPForwarding   : False
        NetworkSecurityGroup : null
        Primary              :

5. Utiliser la `Add-AzureRmVMNetworkInterface` applet de commande pour affecter les cartes réseau aux différentes machines virtuelles.

## <a name="create-a-virtual-machine"></a>Créer une machine virtuelle

Pour des instructions sur la création d’une machine virtuelle et en affectant une carte réseau, voir [créer une machine virtuelle Azure à l’aide de PowerShell](../virtual-machines/virtual-machines-windows-ps-create.md).

## <a name="add-the-network-interface-to-the-load-balancer"></a>Ajouter l’interface réseau à l’équilibrage de charge

1. Extraire l’équilibrage de charge Azure.

    Charger la ressource d’équilibrage de charge dans une variable (si vous n’avez pas fait). La variable est appelée **$lb**. Utiliser les mêmes noms à partir de la ressource d’équilibrage de charge que vous avez créé précédemment.

        $lb= get-azurermloadbalancer –name NRP-LB -resourcegroupname NRP-RG

2. Charger la configuration principale à une variable.

        $backend=Get-AzureRmLoadBalancerBackendAddressPoolConfig -name backendpool1 -LoadBalancer $lb

3. Charger l’interface réseau déjà créés dans une variable. Nom de la variable est **$nic**. Le nom de l’interface réseau est le même que celui de l’exemple précédent.

        $nic =get-azurermnetworkinterface –name lb-nic1-be -resourcegroupname NRP-RG

4. Modifier la configuration principale sur l’interface réseau.

        $nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$backend

5. Enregistrer l’objet interface réseau.

        Set-AzureRmNetworkInterface -NetworkInterface $nic

    Après l’ajout d’une interface réseau à la liste principale des équilibrage de la charge, il commence à recevoir le trafic réseau basé sur les règles d’équilibrage de charge de cette ressource d’équilibrage de charge.

## <a name="update-an-existing-load-balancer"></a>Mettre à jour un équilibrage de charge existant

1. À l’aide de l’équilibrage de charge de l’exemple précédent, attribuer un objet d’équilibrage de charge pour la variable **$slb** à l’aide de `Get-AzureLoadBalancer`.

        $slb = get-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG

2. Dans l’exemple suivant, ajouter une règle NAT entrante--à l’aide de port 81 dans le pool frontal et le port 8181 pour le pool principale--à un équilibrage de charge existant.

        $slb | Add-AzureRmLoadBalancerInboundNatRuleConfig -Name NewRule -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -FrontendPort 81  -BackendPort 8181 -Protocol TCP

3. Enregistrer la nouvelle configuration à l’aide de `Set-AzureLoadBalancer`.

        $slb | Set-AzureRmLoadBalancer

## <a name="remove-a-load-balancer"></a>Supprimer un programme d’équilibrage de charge

Utilisez la commande `Remove-AzureLoadBalancer` pour supprimer un équilibrage de charge créé précédemment nommé **NRP kg** dans un groupe de ressources appelé **NRP routage**.

    Remove-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG

>[AZURE.NOTE] Vous pouvez utiliser le commutateur facultatif **-Force** pour éviter l’invite pour la suppression.

## <a name="next-steps"></a>Étapes suivantes

[Démarrer la configuration d’un programme d’équilibrage de charge interne](load-balancer-get-started-ilb-arm-ps.md)

[Configurer un mode de distribution d’équilibrage de charge](load-balancer-distribution-mode.md)

[Configurer les paramètres de délai d’expiration TCP inactives pour votre équilibrage de charge](load-balancer-tcp-idle-timeout.md)
