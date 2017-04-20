<properties
   pageTitle="Comment configurer BGP sur passerelles VPN Azure à l’aide du Gestionnaire de ressources Azure et PowerShell | Microsoft Azure"
   description="Cet article vous guide dans la configuration BGP avec des passerelles VPN Azure à l’aide du Gestionnaire de ressources Azure et PowerShell."
   services="vpn-gateway"
   documentationCenter="na"
   authors="yushwang"
   manager="rossort"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/15/2016"
   ms.author="yushwang"/>

# <a name="how-to-configure-bgp-on-azure-vpn-gateways-using-azure-resource-manager-and-powershell"></a>Comment configurer BGP sur passerelles VPN Azure à l’aide du Gestionnaire de ressources Azure et PowerShell

Cet article vous explique comment procéder pour activer BGP sur une connexion VPN de Site à Site (S2S) entre locaux et un VNet-VNet à l’aide du modèle de déploiement Gestionnaire de ressources et PowerShell.


**À propos des modèles de déploiement d’Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

## <a name="about-bgp"></a>À propos de BGP

BGP est le protocole de routage standard fréquemment utilisé dans Internet pour échanger des informations de routage et à l’accessibilité entre deux ou plusieurs réseaux. BGP autorise les passerelles VPN Azure et vos périphériques VPN en local, appelés BGP homologues ou voisin, pour échanger des « achemine » qui informe les deux passerelles sur la disponibilité et l’accessibilité de ces préfixes traitée les passerelles ou routeurs impliquées. BGP pouvez également activer voies routage entre plusieurs réseaux par propagation itinéraires qu'une passerelle BGP apprend à partir d’un homologue BGP à tous les autres homologues BGP.

Pour plus d’informations sur les avantages de BGP et à comprendre les exigences techniques et les considérations relatives à l’aide de BGP, voir [Vue d’ensemble de BGP avec des passerelles VPN Azure](./vpn-gateway-bgp-overview.md) .

## <a name="getting-started-with-bgp-on-azure-vpn-gateways"></a>Prise en main BGP sur passerelles VPN Azure

Cet article vous guidera tout au long de la procédure à effectuer les tâches suivantes :

- [Partie 1 : activer BGP sur votre passerelle VPN Azure](#enablebgp)

- [Partie 2 - établir une connexion cross locaux avec BGP](#crossprembgp)

- [Partie 3 - établir une connexion VNet-VNet avec BGP](#v2vbgp)

Chaque partie des instructions constitue un bloc de construction de base d’activation BGP dans votre connectivité réseau. Si vous effectuez toutes les trois parties, vous allez créer la topologie comme indiqué dans le diagramme suivant :

![Topologie BGP](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

Vous pouvez combiner ces ensemble pour créer un réseau lors des transferts plus complexes, espoir multiples, qui répondent à vos besoins.

## <a name ="enablebgp"></a>Partie 1 : configurer BGP sur la passerelle VPN Azure

Les étapes de configuration suivantes seront définir les paramètres de BGP de la passerelle VPN Azure comme indiqué dans le diagramme suivant :

![Passerelle BGP](./media/vpn-gateway-bgp-resource-manager-ps/bgp-gateway.png)

### <a name="before-you-begin"></a>Avant de commencer

- Vérifiez que vous avez un abonnement Azure. Si vous n’avez pas un abonnement Azure, vous pouvez activer votre [avantages d’abonné MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrivez-vous vers le haut pour un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/).
    
- Vous devez installer les applets de commande PowerShell de gestionnaire de ressources Azure. Découvrez [comment installer et configurer Azure PowerShell](../powershell-install-configure.md) pour plus d’informations sur l’installation les applets de commande PowerShell.

### <a name="step-1---create-and-configure-vnet1"></a>Étape 1 : créer et configurer VNet1 

#### <a name="1-declare-your-variables"></a>1. déclarer vos variables

Pour cet exercice, nous allons commencer par la déclaration de notre variables. L’exemple ci-dessous déclare les variables en utilisant les valeurs pour cet exercice. N’oubliez pas de remplacer les valeurs par votre propre lors de la configuration de production. Vous pouvez utiliser ces variables si vous exécutez les étapes pour vous familiariser avec ce type de configuration. Modifiez les variables, puis copiez et collez dans votre console PowerShell.

    $Sub1          = "Replace_With_Your_Subcription_Name"
    $RG1           = "TestBGPRG1"
    $Location1     = "East US"
    $VNetName1     = "TestVNet1"
    $FESubName1    = "FrontEnd"
    $BESubName1    = "Backend"
    $GWSubName1    = "GatewaySubnet"
    $VNetPrefix11  = "10.11.0.0/16"
    $VNetPrefix12  = "10.12.0.0/16"
    $FESubPrefix1  = "10.11.0.0/24"
    $BESubPrefix1  = "10.12.0.0/24"
    $GWSubPrefix1  = "10.12.255.0/27"
    $VNet1ASN      = 65010
    $DNS1          = "8.8.8.8"
    $GWName1       = "VNet1GW"
    $GWIPName1     = "VNet1GWIP"
    $GWIPconfName1 = "gwipconf1"
    $Connection12  = "VNet1toVNet2"
    $Connection15  = "VNet1toSite5"

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2. Connectez-vous à votre abonnement et créez un nouveau groupe de ressources

Vérifiez que vous passez en mode de PowerShell pour utiliser les applets de commande Gestionnaire de ressources. Pour plus d’informations, voir [Utilisation de Windows PowerShell avec le Gestionnaire de ressources](../powershell-azure-resource-manager.md).

Ouvrez votre console PowerShell et connectez-vous à votre compte. Pour vous aider à vous connecter, utilisez l’exemple suivant :

    Login-AzureRmAccount
    Select-AzureRmSubscription -SubscriptionName $Sub1
    New-AzureRmResourceGroup -Name $RG1 -Location $Location1

#### <a name="3-create-testvnet1"></a>3. créer des TestVNet1

L’exemple ci-dessous crée un réseau virtuel nommé TestVNet1 et trois sous-réseaux, une GatewaySubnet appelée, FrontEnd appelée une et un serveur principal appelé. Remplacer les valeurs, il est important que vous nommez toujours votre sous-réseau passerelle spécifiquement GatewaySubnet. Si vous nommez autre chose, la création de votre passerelle échouera. 

    $fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
    $besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
    $gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

    New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

### <a name="step-2---create-the-vpn-gateway-for-testvnet1-with-bgp-parameters"></a>Étape 2 : créer la passerelle VPN pour TestVNet1 avec des paramètres BGP

#### <a name="1-create-the-ip-and-subnet-configurations"></a>1. créer les configurations autre adresse IP

Demander une adresse IP publique à allouer à la passerelle, que vous allez créer pour votre VNet. Vous pouvez également définir le sous-réseau et les configurations IP requises. 

    $gwpip1    = New-AzureRmPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
    
    $vnet1     = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
    $subnet1   = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
    $gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 -Subnet $subnet1 -PublicIpAddress $gwpip1

#### <a name="2-create-the-vpn-gateway-with-the-as-number"></a>2. créer la passerelle VPN avec le numéro en tant que

Créer la passerelle réseau virtuel pour TestVNet1. Notez que BGP nécessite une passerelle VPN basée sur l’itinéraire, ainsi que le paramètre addition, - Asn, pour définir l’ASN (en tant que nombre) pour TestVNet1. Création d’une passerelle peut prendre un certain temps (30 minutes ou plus).

    New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku HighPerformance -Asn $VNet1ASN

#### <a name="3-obtain-the-azure-bgp-peer-ip-address"></a>3. obtenir l’adresse IP de homologue BGP Azure

Une fois que la passerelle est créée, vous devrez obtenir l’adresse IP de homologue BGP sur la passerelle VPN Azure. Cette adresse est nécessaire pour configurer la passerelle VPN Azure comme un homologue BGP pour vos appareils VPN en local.

    $vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
    $vnet1gw.BgpSettingsText

La dernière commande s’affichent les configurations BGP correspondantes sur la passerelle VPN Azure ; par exemple :

    $vnet1gw.BgpSettingsText
    {
        "Asn": 65010,
        "BgpPeeringAddress": "10.12.255.30",
        "PeerWeight": 0
    }

Une fois que la passerelle est créée, vous pouvez utiliser cette passerelle pour établir la connexion cross locaux ou VNet-VNet avec BGP. Les sections suivantes guidera dans les étapes pour terminer l’exercice.

## <a name ="crossprembbgp"></a>Partie 2 - établir une connexion cross locaux avec BGP

Pour établir une connexion entre local, vous devez créer une passerelle réseau Local pour représenter votre périphérique VPN en local et une connexion pour connecter la passerelle VPN Azure avec la passerelle réseau local. La différence entre les instructions fournies dans cet article est les propriétés supplémentaires requises pour spécifier les paramètres de configuration BGP.

![BGP pour croisée locaux](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crossprem.png)

Avant de commencer, vérifiez que vous avez terminé la [partie 1](#enablebgp) de cet exercice.

### <a name="step-1---create-and-configure-the-local-network-gateway"></a>Étape 1 : créer et configurer la passerelle réseau local

#### <a name="1-declare-your-variables"></a>1. déclarer vos variables

Cet exercice continueront à générer la configuration présentée dans l’illustration. N’oubliez pas de remplacer les valeurs par celles que vous souhaitez utiliser pour votre configuration.

    $RG5           = "TestBGPRG5"
    $Location5     = "East US 2"
    $LNGName5      = "Site5"
    $LNGPrefix50   = "10.52.255.254/32"
    $LNGIP5        = "Your_VPN_Device_IP"
    $LNGASN5       = 65050
    $BGPPeerIP5    = "10.52.255.254"

Quelques éléments à noter en ce qui concerne les paramètres de la passerelle réseau local :

- La passerelle réseau local peut se trouver dans la même ou autre emplacement et un groupe de ressources en tant que la passerelle VPN. Cet exemple montre les dans différents groupes de ressources à des endroits différents.

- Le préfixe minimal, que vous devez déclarer pour la passerelle réseau local est l’adresse de l’hôte de votre adresse IP de homologue BGP sur votre appareil VPN. Dans ce cas, il est un /32 préfixe « 10.52.255.254/32 ».

- En tant que rappel, vous devez utiliser différents APE BGP entre vos réseaux en local et Azure VNet. Si elles sont identiques, vous devez modifier votre VNet ASN si votre appareil VPN local déjà en utilisant l’ASN pour pair avec d’autres voisin BGP.
    
Avant de poursuivre, vérifiez que vous êtes toujours connecté à 1 de l’abonnement.

#### <a name="2-create-the-local-network-gateway-for-site5"></a>2. créer la passerelle réseau local pour Site5

Veillez à créer le groupe de ressources si elle n'est pas créée, avant de créer la passerelle réseau local. Remarquez les deux paramètres supplémentaires pour la passerelle réseau local : Asn et BgpPeerAddress.

    New-AzureRmResourceGroup -Name $RG5 -Location $Location5

    New-AzureRmLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix50 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5

### <a name="step-2---connect-the-vnet-gateway-and-local-network-gateway"></a>Étape 2 : se connecter la passerelle VNet et la passerelle de réseau local

#### <a name="1-get-the-two-gateways"></a>1. optez pour les deux passerelles

        $vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
        $lng5gw  = Get-AzureRmLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG5

#### <a name="2-create-the-testvnet1-to-site5-connection"></a>2. créer le TestVNet1 à Site5 connexion

Dans cette étape, vous allez créer la connexion à partir de TestVNet1 à Site5. Vous devez spécifier «-EnableBGP $True » pour activer BGP pour cette connexion. Comme indiqué précédemment, il est possible d’avoir BGP et non BGP connexions pour la même passerelle VPN Azure. À moins d’avoir BGP est activée dans la propriété de connexion, Azure ne permettra pas BGP pour cette connexion même si les paramètres BGP sont déjà configurés sur les deux passerelles.

    New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True


L’exemple ci-dessous répertorie les paramètres que vous entrerez dans la section configuration BGP sur votre appareil VPN en local pour cet exercice :

    - Site5 ASN : 65050
    - Site5 BGP IP : 10.52.255.254
    - Les préfixes d’annoncer : (par exemple) 10.51.0.0/16 et 10.52.0.0/16
    - Azure ASN VNet : 65010
    - VNet Azure BGP IP : 10.12.255.30
    - Itinéraire statique : ajouter un itinéraire pour 10.12.255.30/32, avec saut suivant en cours de l’interface de tunnel VPN sur votre appareil
    - eBGP sauts multiples : Vérifiez que l’option « sauts multiples » eBGP est activé sur votre appareil si nécessaire

La connexion doit être établie après quelques minutes, et la session homologation BGP démarre une fois la connexion IPsec établie.
 
## <a name ="v2vbgp"></a>Partie 3 - établir une connexion VNet-VNet avec BGP

Cette section ajoute une connexion VNet-VNet avec BGP, comme le montre l’illustration ci-dessous. 

![BGP pour VNet-VNet](./media/vpn-gateway-bgp-resource-manager-ps/bgp-vnet2vnet.png)

Les instructions ci-dessous continuer à partir de la procédure précédente décrite ci-dessus. Vous devez effectuer [partie I](#enablebgp) pour créer et configurer TestVNet1 et la passerelle VPN avec BGP. 

### <a name="step-1---create-testvnet2-and-the-vpn-gateway"></a>Étape 1 : créer TestVNet2 et la passerelle VPN

Il est important de s’assurer que l’espace d’adressage IP du nouveau réseau virtuel, TestVNet2, ne chevauche pas d’un de vos plages VNet.

Dans cet exemple, les réseaux virtuels appartiennent à la même abonnement. Vous pouvez configurer les connexions VNet-VNet entre différents abonnements ; reportez-vous à [configurer une connexion VNet-VNet](./vpn-gateway-vnet-vnet-rm-ps.md) pour en savoir plus. Assurez-vous que vous ajoutez la «-EnableBgp $True » lorsque vous créez les connexions pour activer BGP.

#### <a name="1-declare-your-variables"></a>1. déclarer vos variables

N’oubliez pas de remplacer les valeurs par celles que vous souhaitez utiliser pour votre configuration.

    $RG2           = "TestBGPRG2"
    $Location2     = "West US"
    $VNetName2     = "TestVNet2"
    $FESubName2    = "FrontEnd"
    $BESubName2    = "Backend"
    $GWSubName2    = "GatewaySubnet"
    $VNetPrefix21  = "10.21.0.0/16"
    $VNetPrefix22  = "10.22.0.0/16"
    $FESubPrefix2  = "10.21.0.0/24"
    $BESubPrefix2  = "10.22.0.0/24"
    $GWSubPrefix2  = "10.22.255.0/27"
    $VNet2ASN      = 65020
    $DNS2          = "8.8.8.8"
    $GWName2       = "VNet2GW"
    $GWIPName2     = "VNet2GWIP"
    $GWIPconfName2 = "gwipconf2"
    $Connection21  = "VNet2toVNet1"
    $Connection12  = "VNet1toVNet2"

#### <a name="2-create-testvnet2-in-the-new-resource-group"></a>2. créer TestVNet2 dans le nouveau groupe de ressources

    New-AzureRmResourceGroup -Name $RG2 -Location $Location2
    
    $fesub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName2 -AddressPrefix $FESubPrefix2
    $besub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName2 -AddressPrefix $BESubPrefix2
    $gwsub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName2 -AddressPrefix $GWSubPrefix2

    New-AzureRmVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2 -Location $Location2 -AddressPrefix $VNetPrefix21,$VNetPrefix22 -Subnet $fesub2,$besub2,$gwsub2

#### <a name="3-create-the-vpn-gateway-for-testvnet2-with-bgp-parameters"></a>3. créer la passerelle VPN pour TestVNet2 avec des paramètres BGP

Demander une adresse IP publique à allouer à la passerelle, que vous allez créer pour votre VNet. Vous pouvez également définir le sous-réseau et les configurations IP requises. 

    $gwpip2    = New-AzureRmPublicIpAddress -Name $GWIPName2 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic

    $vnet2     = Get-AzureRmVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
    $subnet2   = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
    $gwipconf2 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName2 -Subnet $subnet2 -PublicIpAddress $gwpip2

Créer la passerelle VPN avec le nombre en tant que. Notez que vous devez remplacer la valeur par défaut ASN vos passerelles VPN Azure. Les APE pour la VNets connecté doivent être différents pour activer BGP et le routage de voies.

    New-AzureRmVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gwipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku Standard -Asn $VNet2ASN

### <a name="step-2---connect-the-testvnet1-and-testvnet2-gateways"></a>Étape 2 : connecter les passerelles TestVNet1 et TestVNet2

Dans cet exemple, les deux passerelles sont dans le même abonnement. Vous pouvez effectuer cette étape de la même session PowerShell.

#### <a name="1-get-both-gateways"></a>1. optez pour les deux passerelles

Vérifiez que vous connecterez et se connectez à 1 de l’abonnement.

    $vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
    $vnet2gw = Get-AzureRmVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2
    
#### <a name="2-create-both-connections"></a>2. créer les deux connexions

Dans cette étape, vous allez créer la connexion entre TestVNet1 et TestVNet2 et la connexion à partir de TestVNet2 à TestVNet1.

    New-AzureRmVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True

    New-AzureRmVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 -VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True

>[AZURE.IMPORTANT] Veillez à activer BGP pour les deux connexions.

Une fois ces étapes effectuées, la connexion va établir en quelques minutes, et la session homologation BGP sera une fois la connexion VNet-VNet est terminée.

Si vous avez effectué toutes les trois parties de cet exercice, vous avez sera établie une topologie de réseau comme indiqué ci-dessous :

![BGP pour VNet-VNet](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

## <a name="next-steps"></a>Étapes suivantes

Une fois que votre connexion est terminée, vous pouvez ajouter des machines virtuelles à vos réseaux virtuel. Pour obtenir la procédure, voir [créer une Machine virtuelle](../virtual-machines/virtual-machines-windows-hero-tutorial.md) .

