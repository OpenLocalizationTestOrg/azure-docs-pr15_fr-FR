<properties
   pageTitle="Mise en œuvre une architecture de réseau hautement disponible hybride | Microsoft Azure"
   description="Comment mettre en œuvre une architecture de réseau de site à site sécurisé qui s’étend sur un réseau virtuel Azure et un réseau local connecté à l’aide de ExpressRoute avec VPN passerelle basculement."
   services="guidance,virtual-network,vpn-gateway,expressroute"
   documentationCenter="na"
   authors="telmosampaio"
   manager="christb"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="telmos"/>

# <a name="implementing-a-highly-available-hybrid-network-architecture"></a>Mise en œuvre une architecture de réseau hautement disponible hybride

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

Cet article décrit les meilleures pratiques pour connecter un réseau local à réseaux virtuels sur Azure à l’aide de ExpressRoute, avec un site à réseau privé virtuel (VPN) en tant que basculement connexion. Le trafic s’écoule entre le réseau local et un réseau virtuel Azure (VNet) via une connexion ExpressRoute.  S’il existe une perte de la connectivité du circuit ExpressRoute, le trafic puisse être routé via un tunnel VPN IPSec.

> [AZURE.NOTE] Azure comporte deux modèles de déploiement différents : [Le Gestionnaire de ressources] [ resource-manager-overview] et classique. Ce plan utilise le Gestionnaire de ressources, Microsoft recommande pour les nouveaux déploiements.

Scénarios d’utilisation classiques pour cette architecture sont les suivantes :

- Applications hybrides où les charges de travail sont répartis entre un réseau local et Azure.

- Applications qui s’exécutent les charges de travail à grande échelle, critiques nécessitant un haut degré d’extensibilité élevées.

- Grandes installations de sauvegarde et de restauration des données qui doivent être enregistrées hors du site.

- Gestion des charges de travail Big Data.

- Utilisation d’Azure comme un site de reprise.

Notez que si le circuit ExpressRoute n’est pas disponible, l’itinéraire VPN uniquement gérera les connexions homologation privées. Public peering et Microsoft peering connexions passera via Internet.

## <a name="architecture-diagram"></a>Diagramme d’architecture

>[AZURE.NOTE] Le [service passerelle VPN Azure] [ azure-vpn-gateway] mettent en œuvre, deux types de passerelles réseau virtuel ; Passerelles de réseau virtuel VPN et ExpressRoute réseau virtuel. Dans ce document, le terme *Passerelle VPN* fait référence au service Azure, tout en les phrases *passerelle réseau virtuel VPN* et la *passerelle de réseau virtuel ExpressRoute* sont utilisés pour faire référence aux mises en œuvre VPN et ExpressRoute de la passerelle respectivement.

Le diagramme suivant met en évidence les éléments importants de cette architecture :

> Un document Visio incluant ce diagramme architecture est disponible au téléchargement sur le [Centre de téléchargement Microsoft][visio-download]. Ce diagramme est sur le « hybride réseau - restauration d’urgence VPN » page.

![[0]][0]

- **Réseaux virtuels Azure (VNets).** Chaque VNet réside dans une seule région Azure et peut héberger plusieurs niveaux d’applications. Niveaux de l’application peut être placés à l’aide de sous-réseaux dans chaque VNet et/ou réseau de groupes de sécurité (NSGs).

- **Réseau d’entreprise en local.** Il s’agit d’un réseau d’ordinateurs et appareils, connectés à un réseau local privé en cours d’exécution au sein d’une organisation.

- **Appareil VPN.** Il s’agit d’un appareil ou un service qui fournit la connectivité externe au réseau local. L’appareil VPN peut être un périphérique matériel, ou elle peut être une solution logicielle tels que le routage et le Service d’accès distant (RRAS) de Windows Server 2012.

> [AZURE.NOTE] Pour obtenir la liste des appareils VPN pris en charge et des informations sur la configuration des appareils VPN sélectionnées pour vous connecter à une passerelle VPN Azure, consultez les instructions pour le périphérique approprié dans la [liste des appareils VPN pris en charge par Azure][vpn-appliance].

- **Passerelle réseau virtuel VPN.** La passerelle de réseau virtuel VPN permet la VNet pour vous connecter à l’appareil VPN sur le réseau local. La passerelle de réseau virtuel VPN est configurée pour accepter des demandes à partir du réseau local uniquement par le biais de l’appareil VPN. Pour plus d’informations, voir [se connecter un réseau local à un réseau virtuel Microsoft Azure][connect-to-an-Azure-vnet].

- **Passerelle ExpressRoute réseau virtuel.** La passerelle réseau virtuel ExpressRoute permet la VNet pour vous connecter à la circuit ExpressRoute utilisé pour la connectivité avec votre réseau local.

- **Sous-réseau passerelle.** Les passerelles réseau virtuel sont conservés dans le même sous-réseau.

- **Connexion VPN.** La connexion comporte des propriétés qui spécifient le type de connexion (IPSec) et la clé partagée avec l’appareil VPN en local pour chiffrer le trafic.

- **ExpressRoute circuit.** Il s’agit d’un calque 2 ou circuit layer 3 fournis par le fournisseur de connectivité ce réseau jointures les locales avec Azure via les routeurs de bord. Le circuit utilise l’infrastructure matérielle géré par le fournisseur de connectivité.

- **Application en nuage multicouches.** Il s’agit de l’application hébergée dans Azure. Il peut inclure plusieurs niveaux, avec plusieurs sous réseaux connectés via des programmes d’équilibrage de charge Azure. Le trafic dans chaque sous-réseau être facturés règles définies à l’aide de [Groupes de sécurité Azure réseau][azure-network-security-group](NSGs). Pour plus d’informations, voir [prise en main de Microsoft Azure sécurité][getting-started-with-azure-security].

## <a name="recommendations"></a>Recommandations

Azure offre de nombreuses ressources différentes et des types de ressources, de sorte que cette architecture de référence peut être mis en service de différentes manières. Nous vous fournissons un modèle Azure le Gestionnaire de ressources pour installer l’architecture de référence qui suit ces recommandations. Si vous choisissez de créer votre propre architecture de référence, vous devez suivre ces recommandations à moins d’avoir un besoin spécifique une recommandation ne tient pas.

### <a name="vnet-and-gatewaysubnet"></a>VNet et GatewaySubnet

Créer la passerelle réseau virtuel ExpressRoute et la passerelle de réseau virtuel VPN dans le même VNet. Cela signifie qu’ils doivent partager la même sous-réseau nommé **GatewaySubnet**

Si la VNet contient déjà un sous-réseau nommé **GatewaySubnet**, assurez-vous qu’il contient un /27 ou plus grand espace d’adressage. Si l’existant constitue trop petit, puis supprimez-la comme suit et créer un autre, comme le montre la puce suivante :

```powershell
$vnet = Get-AzureRmVirtualNetworkGateway -Name <yourvnetname> -ResourceGroupName <yourresourcegroup>
Remove-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet
```

Si le VNet ne contient pas un sous-réseau nommé **GatewaySubnet**, créez un nouvel identifiant comme suit :

```powershell
$vnet = Get-AzureRmVirtualNetworkGateway -Name <yourvnetname> -ResourceGroupName <yourresourcegroup>
Add-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet -AddressPrefix "10.200.255.224/27"
$vnet = Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```

### <a name="vpn-and-expressroute-gateways"></a>Passerelles VPN et ExpressRoute

Vérifiez que votre organisation remplit les [conditions prérequises ExpressRoute] [ expressroute-prereq] pour vous connecter à Azure.

Si vous disposez déjà d’une passerelle de réseau virtuel VPN dans votre VNet Azure, supprimez-le, comme illustré ci-dessous.

```powershell
Remove-AzureRmVirtualNetworkGateway -Name <yourgatewayname> -ResourceGroupName <yourresourcegroup>
```

Suivez les instructions de [mise en œuvre une architecture réseau hybride avec Azure ExpressRoute] [ implementing-expressroute] pour établir votre connexion ExpressRoute.

Suivez les instructions de [mise en œuvre une architecture réseau hybride avec Azure et en local VPN] [ implementing-vpn] d’établir votre connexion de passerelle VPN réseau virtuel.

Une fois que vous avez établi les connexions de passerelle réseau virtuel, tester l’environnement comme suit :

1. Vérifiez que vous pouvez vous connecter à partir de votre réseau local à votre VNet Azure.

2. Contactez votre fournisseur pour arrêter de connectivité ExpressRoute pour le test.

3. Vérifiez que vous pouvez toujours vous connecter à partir de votre réseau local pour votre VNet Azure à l’aide de la connexion de passerelle VPN réseau virtuel.

4. Contactez votre fournisseur pour reestabilish ExpressRoute connectivité.

## <a name="considerations"></a>Considérations relatives à la

Pour des raisons de ExpressRoute, consultez [mise en œuvre une Architecture réseau hybride avec Azure ExpressRoute] [ guidance-expressroute] recommandations.

Pour des raisons de Site à VPN, voir l' [implémentation d’une Architecture réseau hybride avec Azure et en local VPN] [ guidance-vpn] recommandations.

Général Azure en matière de sécurité, consultez [services cloud Microsoft et la sécurité du réseau][best-practices-security].

## <a name="solution-deployment"></a>Déploiement de solution

Si vous avez une infrastructure locale existante déjà configurée avec un équipement réseau appropriée, vous pouvez déployer l’architecture de référence en procédant comme suit :

1. Cliquez avec le bouton droit sur le bouton ci-dessous, puis sélectionnez soit « ouvrir le lien dans un nouvel onglet » ou « Ouvrir le lien dans une nouvelle fenêtre » :  
[![Déploiement d’Azure](./media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-hybrid-network-vpn-er%2Fazuredeploy.json)

2. Attendez que le lien pour ouvrir le portail Azure, puis procédez comme suit : 
  - Le nom du **groupe de ressources** est déjà défini dans le fichier de paramètres : sélectionnez **Créer un nouveau** et entrez `ra-hybrid-vpn-er-rg` dans la zone de texte.
  - Sélectionnez la région dans la zone de liste déroulante **emplacement** .
  - Ne modifiez pas **Modèle racine Uri** ou les zones de texte **Paramètre racine Uri** .
  - Passez en revue les termes et conditions, puis cliquez sur la case à cocher **que j’accepte les termes et conditions indiquées ci-dessus** .
  - Cliquez sur le bouton **achat** .

3. Attendez que le déploiement terminer.

4. Cliquez avec le bouton droit sur le bouton ci-dessous, puis sélectionnez soit « ouvrir le lien dans un nouvel onglet » ou « Ouvrir le lien dans une nouvelle fenêtre » :  
[![Déploiement d’Azure](./media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-hybrid-network-vpn-er%2Fazuredeploy-expressRouteCircuit.json)

5. Attendez que le lien Ouvrir le portail Azure, puis entrez puis procédez comme suit : 
  - Sélectionnez **utiliser un existant** dans la section de **groupe de ressources** , puis entrez `ra-hybrid-vpn-er-rg` dans la zone de texte.
  - Sélectionnez la région dans la zone de liste déroulante **emplacement** .
  - Ne modifiez pas **Modèle racine Uri** ou les zones de texte **Paramètre racine Uri** .
  - Passez en revue les termes et conditions, puis cliquez sur la case à cocher **que j’accepte les termes et conditions indiquées ci-dessus** .
  - Cliquez sur le bouton **achat** .

<!-- links -->

[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md
[vpn-appliance]: ../vpn-gateway/vpn-gateway-about-vpn-devices.md
[azure-vpn-gateway]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[connect-to-an-Azure-vnet]: https://technet.microsoft.com/library/dn786406.aspx
[azure-network-security-group]: ../virtual-network/virtual-networks-nsg.md
[getting-started-with-azure-security]: ./../security/azure-security-getting-started.md
[expressroute-prereq]: ../expressroute/expressroute-prerequisites.md
[implementing-expressroute]: ./guidance-hybrid-network-expressroute.md#implementing-this-architecture
[implementing-vpn]: ./guidance-hybrid-network-vpn.md#implementing-this-architecture
[guidance-expressroute]: ./guidance-hybrid-network-expressroute.md
[guidance-vpn]: ./guidance-hybrid-network-vpn.md
[best-practices-security]: ../best-practices-network-security.md
[solution-script]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-vpn-er/Deploy-ReferenceArchitecture.ps1
[solution-script-bash]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-vpn-er/deploy-reference-architecture.sh
[vnet-parameters]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-vpn-er/parameters/virtualNetwork.parameters.json
[virtualnetworkgateway-vpn-parameters]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-vpn-er/parameters/virtualNetworkGateway-vpn.parameters.json
[virtualnetworkgateway-expressroute-parameters]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-vpn-er/parameters/virtualNetworkGateway-expressRoute.parameters.json
[er-circuit-parameters]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-vpn-er/parameters/expressRouteCircuit.parameters.json
[azure-powershell-download]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[naming conventions]: ./guidance-naming-conventions.md
[azure-cli]: https://azure.microsoft.com/documentation/articles/xplat-cli-install/
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[0]: ./media/blueprints/hybrid-network-expressroute-vpn-failover.png "Architecture d’architecture réseau hybride hautement disponible à l’aide de la passerelle VPN et ExpressRoute"
[ARM-Templates]: https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/
