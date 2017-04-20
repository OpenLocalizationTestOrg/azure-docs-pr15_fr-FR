<properties 
   pageTitle="Lier un réseau virtuel pour un circuit ExpressRoute à l’aide de PowerShell | Microsoft Azure"
   description="Ce document fournit une vue d’ensemble de la façon d’établir un lien réseaux virtuels (VNets) aux circuits ExpressRoute en utilisant le modèle de déploiement Gestionnaire de ressources et PowerShell."
   services="expressroute"
   documentationCenter="na"
   authors="ganesr"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/10/2016"
   ms.author="ganesr" />

# <a name="link-a-virtual-network-to-an-expressroute-circuit"></a>Lier un réseau virtuel à un circuit ExpressRoute

> [AZURE.SELECTOR]
- [Portail Azure - Gestionnaire de ressources](expressroute-howto-linkvnet-portal-resource-manager.md)
- [PowerShell - Gestionnaire de ressources](expressroute-howto-linkvnet-arm.md)
- [PowerShell - classique](expressroute-howto-linkvnet-classic.md)


Cet article vous aidera à lier réseaux virtuels (VNets) aux circuits Azure ExpressRoute à l’aide du modèle de déploiement de gestionnaire de ressources et PowerShell. Réseaux virtuels peuvent être dans le même abonnement ou une partie d’un autre abonnement.

**À propos des modèles de déploiement d’Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

## <a name="configuration-prerequisites"></a>Conditions préalables de configuration

- Vous avez besoin de la dernière version des modules PowerShell Azure (au moins version 1.0). Découvrez [comment installer et configurer Azure PowerShell](../powershell-install-configure.md) pour plus d’informations sur l’installation les applets de commande PowerShell.
- Vous devez passer en revue les [conditions préalables](expressroute-prerequisites.md), [exigences de gamme](expressroute-routing.md)et [flux de travail](expressroute-workflows.md) avant de commencer la configuration.
- Vous devez avoir un circuit ExpressRoute actif. 
    - Suivez les instructions pour [créer un circuit ExpressRoute](expressroute-howto-circuit-arm.md) et avoir le circuit activé par votre fournisseur de connectivité. 
    - Assurez-vous d’avoir Azure peering privé configuré pour votre circuit. Voir l’article [configurer le routage](expressroute-howto-routing-arm.md) pour obtenir des instructions routage. 
    - Vérifiez que Azure peering privé est configuré et la peering BGP entre votre réseau et Microsoft est l’afin que vous pouvez activer la connectivité de bout en bout.
    - Assurez-vous d’avoir un réseau virtuel et une passerelle réseau virtuel créé et entièrement mis en service. Suivez les instructions pour créer une [passerelle VPN](../articles/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md), mais vous devez utiliser `-GatewayType ExpressRoute`.

Vous pouvez lier jusqu'à 10 réseaux virtuels à un circuit ExpressRoute standard. Tous les réseaux virtuels doivent être placé dans la même région géopolitique lorsque vous utilisez un circuit ExpressRoute standard. 

Vous pouvez lier un réseaux virtuel en dehors de la région géopolitique du circuit ExpressRoute, ou connectez un grand nombre de réseaux virtuels votre circuit ExpressRoute si vous avez activé le module complémentaire premium ExpressRoute. Consultez le [Forum aux questions](expressroute-faqs.md) pour plus d’informations sur le module complémentaire premium.

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Connectez un réseau virtuel dans le même abonnement à un circuit

Vous pouvez vous connecter à une passerelle réseau virtuel vers un circuit ExpressRoute à l’aide de l’applet de commande suivante. Assurez-vous que la passerelle réseau virtuel est créée et est prête pour la liaison avant d’exécuter l’applet de commande :

    $circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
    $gw = Get-AzureRmVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
    $connection = New-AzureRmVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "MyRG" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Connectez un réseau virtuel dans un autre abonnement à un circuit

Vous pouvez partager un circuit ExpressRoute entre plusieurs abonnements. La figure suivante illustre une simple principe de fonctionnement du partage de circuits ExpressRoute plusieurs abonnements.

Chacune des nuages plus petits au sein du cloud volumineux est utilisé pour représenter les abonnements appartenant à différents départements au sein d’une organisation. Chacun de ces services au sein de l’organisation peut utiliser leur propre abonnement pour le déploiement de leurs services, mais ils peuvent partager un seul circuit ExpressRoute pour vous connecter à votre réseau local. Un seul service (dans cet exemple : IT) peut être propriétaire du circuit ExpressRoute. Autres abonnements au sein de l’organisation peuvent utiliser le circuit ExpressRoute.

>[AZURE.NOTE] Frais de connectivité et la bande passante pour le circuit dédié est appliquées au propriétaire circuit ExpressRoute. Tous les réseaux virtuels partagent la même bande passante.

![Connectivité entre-abonnement](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration"></a>Administration

Le *propriétaire de circuit* est un utilisateur power autorisés de la ressource de circuit ExpressRoute. Le propriétaire circuit peut créer des autorisations qui peuvent être utilisées par les *utilisateurs de circuit*. *Utilisateurs de circuit* sont des propriétaires des passerelles réseau virtuel (qui se ne trouvent pas dans le même abonnement en tant que le circuit ExpressRoute). *Les utilisateurs circuit* peuvent entrer les autorisations (une autorisation par réseau virtuel).

Le *propriétaire de circuit* a la possibilité de modifier ou révoquer des autorisations à tout moment. Révocation d’un résultats d’autorisation dans toutes les connexions de lien en cours de suppression de l’abonnement dont l’accès a été révoqué.

### <a name="circuit-owner-operations"></a>Opérations de propriétaire de circuit 

#### <a name="creating-an-authorization"></a>Création d’une autorisation
    
Le propriétaire circuit crée une autorisation. Cela entraîne la création d’une clé d’autorisation qui peut être utilisée par un utilisateur circuit pour vous connecter leurs passerelles réseau virtuel au circuit ExpressRoute. Une autorisation est valide pour une seule connexion.

L’extrait de l’applet de commande suivante montre comment créer une autorisation :

    $circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
    Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit

        $circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
    $auth1 = Get-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"
        

La réponse à cette contiendra la clé d’autorisation et l’état :

    Name                   : MyAuthorization1
    Id                     : /subscriptions/&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/CrossSubTest/authorizations/MyAuthorization1
    Etag                   : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&& 
    AuthorizationKey       : ####################################
    AuthorizationUseStatus : Available
    ProvisioningState      : Succeeded

        

#### <a name="reviewing-authorizations"></a>Examinez les autorisations

Le propriétaire circuit pouvez examiner toutes les autorisations qui sont émises sur un circuit particulier en exécutant l’applet de commande suivante :

    $circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
    $authorizations = Get-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit
    

#### <a name="adding-authorizations"></a>Ajout d’autorisations

Le propriétaire circuit peut ajouter des autorisations à l’aide de l’applet de commande suivante :

    $circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
    Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization2"
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit
    
    $circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
    $authorizations = Get-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit

    
#### <a name="deleting-authorizations"></a>Suppression d’autorisations

Le propriétaire de circuit pouvez révoquer/supprimer les autorisations pour l’utilisateur en exécutant l’applet de commande suivante :

    Remove-AzureRmExpressRouteCircuitAuthorization -Name "MyAuthorization2" -ExpressRouteCircuit $circuit
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit    

### <a name="circuit-user-operations"></a>Opérations de l’utilisateur circuit

L’utilisateur circuit doit l’ID de pair et une clé d’autorisation du propriétaire du circuit. La clé d’autorisation est un GUID.

ID de pair est, peuvent être vérifiées à partir de la commande suivante.

    Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"

#### <a name="redeeming-connection-authorizations"></a>En utilisant les autorisations de connexion

L’utilisateur circuit peut exécuter l’applet de commande suivante pour échanger une autorisation de lien :

    $id = "/subscriptions/********************************/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/MyCircuit"  
    $gw = Get-AzureRmVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
    $connection = New-AzureRmVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "RemoteResourceGroup" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $id -ConnectionType ExpressRoute -AuthorizationKey "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"

#### <a name="releasing-connection-authorizations"></a>Libérer des autorisations de connexion

Vous pouvez libérer une autorisation en supprimant la connexion qui lie le circuit ExpressRoute au réseau virtuel.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur ExpressRoute, consultez le [Forum aux questions sur ExpressRoute](expressroute-faqs.md).
