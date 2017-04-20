<properties
    pageTitle="Commandes courantes réseau PowerShell pour les machines virtuelles | Microsoft Azure"
    description="Commandes PowerShell courantes pour vous aider à commencer à créer un réseau virtuel et ressources associées pour les machines virtuelles."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="davidmu"/>

# <a name="common-network-azure-powershell-commands-for-vms"></a>Commandes PowerShell Azure réseau courantes pour les machines virtuelles

Si vous voulez créer une machine virtuelle, vous devez créer un [réseau virtuel](../virtual-network/virtual-networks-overview.md) ou savoir sur un réseau virtuel dans lequel la machine virtuelle peut être ajoutée. En règle générale, lorsque vous créez une machine virtuelle, vous devez également envisager de créer les ressources décrites dans cet article.

Découvrez [comment installer et configurer Azure PowerShell](../powershell-install-configure.md) pour plus d’informations sur l’installation de la dernière version d’Azure PowerShell, en sélectionnant votre abonnement et se connecter à votre compte.

## <a name="create-network-resources"></a>Créer des ressources de réseau

Tâche | Commande 
-------------- | -------------------------
Créer des configurations de sous-réseau | $subnet1 = [New-AzureRmVirtualNetworkSubnetConfig](https://msdn.microsoft.com/library/mt619412.aspx) -subnet_name « nom » AddressPrefix - XX. X.X.X/XX<BR>$subnet2 = New-AzureRmVirtualNetworkSubnetConfig-subnet_name « nom » AddressPrefix - XX. X.X.X/XX<BR><BR>Un réseau classique peut avoir un sous-réseau pour un [internet facing équilibrage de charge](../load-balancer/load-balancer-internet-overview.md) et un sous-réseau distinct pour un [programme d’équilibrage de charge interne](../load-balancer/load-balancer-internal-overview.md). |
Créer un réseau virtuel | $vnet = [New-AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt603657.aspx) -nom_groupe_ressource « nom « virtual_network_name » - ResourceGroupName »-emplacement « location_name » AddressPrefix - XX. X.X.X/XX-sous-réseau $subnet1, $subnet2
Tester la présence d’un nom de domaine unique | [Test-AzureRmDnsAvailability](https://msdn.microsoft.com/library/mt619419.aspx) - DomainQualifiedName « nom_domaine »-emplacement « location_name »<BR><BR>Vous pouvez spécifier un nom de domaine DNS pour une [ressource IP public](../virtual-network/virtual-network-ip-addresses-overview-arm.md), qui crée un mappage pour domainname.location.cloudapp.azure.com vers l’adresse IP dans les serveurs DNS géré Azure. Le nom peut contenir uniquement des lettres, des chiffres et des traits d’union. Les premier et dernier caractères doit être une lettre ou le numéro et le nom de domaine doivent être uniques dans son emplacement Azure. Si la **valeur True** est renvoyée, votre nom proposé est globalement unique.
Créer une adresse IP publique | $pip = [New-AzureRmPublicIpAddress](https://msdn.microsoft.com/library/mt603620.aspx) -nom_domaine « nom « ip_address_name » - ResourceGroupName « nom_groupe_ressource » - DomainNameLabel »-emplacement « location_name » - AllocationMethod dynamique<BR><BR>L’adresse IP publique utilise le nom de domaine déjà testé et est utilisé par la configuration frontend de l’équilibrage de charge.
Créer une configuration IP frontend | $frontendIP = [New-AzureRmLoadBalancerFrontendIpConfig](https://msdn.microsoft.com/library/mt603510.aspx) -frontend_ip_name « nom » - PublicIpAddress $pip<BR><BR>La configuration frontend inclut l’adresse IP que vous avez créé précédemment pour le trafic réseau entrant.
Créer un groupe d’adresses serveur principal | $beAddressPool = [New-AzureRmLoadBalancerBackendAddressPoolConfig](https://msdn.microsoft.com/library/mt603791.aspx) -backend_pool_name « nom »<BR><BR>Fournit des adresses internes pour le système principal de l’équilibrage de charge qui sont accessibles via une interface réseau.
Créer une sonde | $healthProbe = [New-AzureRmLoadBalancerProbeConfig](https://msdn.microsoft.com/library/mt603847.aspx) -probe_name « nom » - RequestPath 'HealthProbe.aspx'-protocole http-ports 80 - IntervalInSeconds 15 - ProbeCount 2<BR><BR>Contient des sondes santé utilisées pour vérifier la disponibilité d’instances de machines virtuelles dans le pool d’adresses principal.
Créer une règle d’équilibrage de charge | $lbRule = [New-AzureRmLoadBalancerRuleConfig](https://msdn.microsoft.com/library/mt619391.aspx) -HTTP nom - FrontendIpConfiguration $frontendIP - BackendAddressPool $beAddressPool-sonde $healthProbe-80 Tcp Protocol - FrontendPort - BackendPort 80<BR><BR>Contient des règles qui affecter un port public sur l’équilibrage de charge à un port du serveur principal pool d’adresses.
Créer une règle NAT entrante | $inboundNATRule = [New-AzureRmLoadBalancerInboundNatRuleConfig](https://msdn.microsoft.com/library/mt603606.aspx) -nom_règle « nom » - FrontendIpConfiguration $frontendIP-3441 TCP Protocol - FrontendPort - BackendPort 3389<BR><BR>Contient des règles de mappage d’un port public sur l’équilibrage de charge vers un port pour une machine virtuelle spécifique dans le pool d’adresses principal.
Créer un programme d’équilibrage de charge | $loadBalancer = « nom_groupe_ressource » [New-AzureRmLoadBalancer](https://msdn.microsoft.com/library/mt619450.aspx) - ResourceGroupName-load_balancer_name « nom »-emplacement « location_name » - FrontendIpConfiguration $frontendIP - InboundNatRule $inboundNATRule - LoadBalancingRule $lbRule - BackendAddressPool $beAddressPool-sonde $healthProbe
Créer une interface de réseau | $nic1 = « nom_groupe_ressource » [New-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619370.aspx) - ResourceGroupName-Nom_interface_réseau « nom »-emplacement « location_name » PrivateIpAddress - XX. X.X.X-sous-réseau subnet2 - LoadBalancerBackendAddressPool $loadBalancer.BackendAddressPools[0] - LoadBalancerInboundNatRule $loadBalancer.InboundNatRules[0]<BR><BR>Créer une interface de réseau à l’aide de l’adresse IP publique et sous-réseau réseau virtuel que vous avez créé précédemment.
    
## <a name="get-information-about-network-resources"></a>Obtenir des informations sur les ressources du réseau

Tâche | Commande 
-------------- | -------------------------
Liste des réseaux virtuels | [Get-AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt603515.aspx) - ResourceGroupName « nom_groupe_ressource »<BR><BR>Répertorie tous les réseaux virtuels dans le groupe de ressources.
Obtenir des informations sur un réseau virtuel | Get-AzureRmVirtualNetwork-nom_groupe_ressource « nom « virtual_network_name » - ResourceGroupName »
Liste sous-réseaux dans un réseau virtuel | Get-AzureRmVirtualNetwork-nommer nom_groupe_ressource » « virtual_network_name » - ResourceGroupName » & #124 ; Sélectionnez sous-réseaux
Obtenir des informations sur un sous-réseau | [Get-AzureRmVirtualNetworkSubnetConfig](https://msdn.microsoft.com/library/mt603817.aspx) -subnet_name « nom » - VirtualNetwork $vnet<BR><BR>Obtient des informations sur le sous-réseau dans le réseau virtuel spécifié. La valeur $vnet représente l’objet renvoyé par Get-AzureRmVirtualNetwork.
Liste des adresses IP | [Get-AzureRmPublicIpAddress](https://msdn.microsoft.com/library/mt619342.aspx) - ResourceGroupName « nom_groupe_ressource »<BR><BR>Répertorie les adresses IP publiques dans le groupe de ressources.
Liste des programmes d’équilibrage de charge | [Get-AzureRmLoadBalancer](https://msdn.microsoft.com/library/mt603668.aspx) - ResourceGroupName « nom_groupe_ressource »<BR><BR>Répertorie tous les programmes d’équilibrage de charge dans le groupe de ressources.
Interfaces réseau de liste | [Get-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619434.aspx) - ResourceGroupName « nom_groupe_ressource »<BR><BR>Répertorie toutes les interfaces réseau dans le groupe de ressources.
Obtenir des informations sur une interface réseau | Get-AzureRmNetworkInterface-nom_groupe_ressource « nom « Nom_interface_réseau » - ResourceGroupName »<BR><BR>Obtient des informations sur une interface réseau spécifique.
Obtenir la configuration IP d’une interface réseau | [Get-AzureRmNetworkInterfaceIPConfig](https://msdn.microsoft.com/library/mt732618.aspx) -ipconfiguration_name « nom » - NetworkInterface $nic<BR><BR>Obtient des informations sur la configuration IP de l’interface réseau spécifié. La valeur $nic représente l’objet renvoyé par Get-AzureRmNetworkInterface.

## <a name="manage-network-resources"></a>Gérer les ressources réseau

Tâche | Commande 
-------------- | -------------------------
Ajouter un sous-réseau à un réseau virtuel | [AzureRmVirtualNetworkSubnetConfig ajouter](https://msdn.microsoft.com/library/mt603722.aspx) - AddressPrefix XX. X.X.X/XX-nom « subnet_name » - VirtualNetwork $vnet<BR><BR>Ajoute un sous-réseau à un réseau virtuel. La valeur $vnet représente l’objet renvoyé par Get-AzureRmVirtualNetwork.
Supprimer un réseau virtuel | [Supprimer AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt619338.aspx) -nom_groupe_ressource « nom « virtual_network_name » - ResourceGroupName »<BR><BR>Supprime le réseau virtuel spécifié dans le groupe de ressources.
Supprimer une interface réseau | [Supprimer AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt603836.aspx) -nom_groupe_ressource « nom « Nom_interface_réseau » - ResourceGroupName »<BR><BR>Supprime l’interface réseau spécifié dans le groupe de ressources.
Supprimer un programme d’équilibrage de charge | [Supprimer AzureRmLoadBalancer](https://msdn.microsoft.com/library/mt603862.aspx) -nom_groupe_ressource « nom « load_balancer_name » - ResourceGroupName »<BR><BR>Supprime l’équilibrage de charge spécifié dans le groupe de ressources.
Supprimer une adresse IP publique | [Supprimer AzureRmPublicIpAddress](https://msdn.microsoft.com/library/mt619352.aspx)-nom_groupe_ressource « nom « ip_address_name » - ResourceGroupName »<BR><BR>Supprime l’adresse IP spécifiée dans le groupe de ressources.

## <a name="next-steps"></a>Étapes suivantes

- Utiliser l’interface de réseau que vous venez de créer lorsque vous [créez une machine virtuelle](virtual-machines-windows-ps-create.md).
- Découvrez comment vous pouvez [créer une machine virtuelle avec plusieurs interfaces réseau](../virtual-network/virtual-networks-multiple-nics.md).
