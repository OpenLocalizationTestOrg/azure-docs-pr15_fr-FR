<properties
   pageTitle="Mise en œuvre une Architecture réseau hybride avec Azure ExpressRoute | Architecture de référence | Microsoft Azure"
   description="Comment mettre en œuvre une architecture de réseau de site à sécurisée qui s’étend sur un réseau virtuel Azure et un réseau local connecté à l’aide de Azure ExpressRoute."
   services=""
   documentationCenter="na"
   authors="telmosampaio"
   manager="christb"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="telmos"/>

# <a name="implementing-a-hybrid-network-architecture-with-azure-expressroute"></a>Mise en œuvre une architecture réseau hybride avec Azure ExpressRoute

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

Cet article décrit les meilleures pratiques pour la connexion d’un réseau local à réseaux virtuels sur Azure à l’aide de ExpressRoute. ExpressRoute connexions sont apportées à l’aide d’une connexion dédiée privée via un fournisseur tiers connectivity. La connexion privée étend votre réseau local dans Azure permettant d’accéder à votre propre infrastructure IaaS dans Azure, publics points de terminaison utilisés dans les services PaaS et services Office 365 SaaS. Ce document se concentre sur l’utilisation de ExpressRoute pour vous connecter à un seul Azure virtuel réseau (VNet) à l’aide de ce qui est appelé peering privé.

> [AZURE.NOTE] Azure comporte deux modèles de déploiement différents : [Le Gestionnaire de ressources] [ resource-manager-overview] et classique. Ce plan utilise le Gestionnaire de ressources, Microsoft recommande pour les nouveaux déploiements.

Scénarios d’utilisation classiques pour cette architecture sont les suivantes :

- Applications hybrides où les charges de travail sont répartis entre un réseau local et Azure.

- Applications qui s’exécutent les charges de travail à grande échelle, critiques nécessitant un haut degré d’extensibilité élevées.

- Grandes installations de sauvegarde et de restauration des données qui doivent être enregistrées hors du site.

- Gestion des charges de travail Big Data.

- Utilisation d’Azure comme un site de reprise.

> [AZURE.NOTE] [Présentation technique ExpressRoute] [ expressroute-technical-overview] fournit une introduction à ExpressRoute.

## <a name="architecture-diagram"></a>Diagramme d’architecture

Le diagramme suivant met en évidence les éléments importants de cette architecture :

> Un document Visio incluant ce diagramme architecture est disponible au téléchargement sur le [Centre de téléchargement Microsoft][visio-download]. Ce diagramme est dans la page « Hybride réseau - SQL Server ».

![[0]][0]

- **Réseaux virtuels Azure (VNets).** Chaque VNet réside dans une seule région Azure et peut héberger plusieurs niveaux d’applications. Niveaux de l’application peut être placés à l’aide de sous-réseaux dans chaque VNet et/ou réseau de groupes de sécurité (NSGs). 

- **Services publics Azure.** Il s’agit des services Azure qui peuvent être utilisés dans une application hybride. Ces services sont également disponibles via Internet public, mais y accéder via un circuit ExpressRoute fournit une latence faible et des performances plus prévisibles dans la mesure où le trafic ne sont pas acheminés via Internet. Connexions sont effectuées à l’aide de **peering public**, avec des adresses qui sont appartenant à votre organisation ou fournis par votre fournisseur de connectivité. 

- **Services Office 365.** Il s’agit des applications Office 365 disponibles publiquement et services fournis par Microsoft. Connexions sont effectuées à l’aide de **Microsoft peering**, avec des adresses qui sont appartenant à votre organisation ou fournis par votre fournisseur de connectivité.

>[AZURE.NOTE] Vous pouvez également vous connecter directement à Microsoft CRM Online via un peering Microsoft.

- **Réseau d’entreprise en local.** Il s’agit d’un réseau d’ordinateurs et appareils, connectés à un réseau local privé en cours d’exécution au sein d’une organisation.

- **Routeurs locaux.** Il s’agit des routeurs qui se connecter au réseau local au circuit géré par le fournisseur. Selon la façon dont votre connexion est configurée, vous devez fournir les adresses IP publiques utilisés par les routeurs. 

- **Composants Microsoft Router bord.** Il s’agit des deux routeurs dans une configuration hautement disponible actif. Ces routeurs activent un fournisseur de connexion pour vous connecter leurs circuits directement à leur centre de données. Selon la façon dont votre connexion est configurée, vous devez fournir les adresses IP publiques utilisés par les routeurs.

- **ExpressRoute circuit.** Il s’agit d’un calque 2 ou circuit layer 3 fournis par le fournisseur de connectivité ce réseau jointures les locales avec Azure via les routeurs bord. Le circuit utilise l’infrastructure matérielle géré par le fournisseur de connectivité.

- **Fournisseurs de connectivité.** Il s’agit des sociétés qui fournissent une connexion à l’aide de connectivité de couche 3 entre votre centre de données et un centre de données Azure ou calque 2.

## <a name="recommendations"></a>Recommandations

Azure offre de nombreuses ressources différentes et des types de ressources, de sorte que cette architecture de référence peut être mis en service de différentes manières. Nous vous fournissons un modèle Azure le Gestionnaire de ressources pour installer l’architecture de référence qui suit ces recommandations. Si vous choisissez de créer votre propre architecture de référence, vous devez suivre ces recommandations à moins d’avoir un besoin spécifique une recommandation ne tient pas.

### <a name="connectivity-providers"></a>Fournisseurs de connectivité

Sélectionnez un fournisseur de connectivité ExpressRoute approprié pour votre emplacement. Pour obtenir une liste de fournisseurs de connectivité disponibles sur votre site, utilisez la commande Azure PowerShell suivante :

```powershell
Get-AzureRmExpressRouteServiceProvider
```

Fournisseurs de connectivité ExpressRoute connecter votre centre de données à Microsoft des façons suivantes :

- **Co-création situé à un échange de cloud.** Si vous vous trouvez co-création dans un emplacement avec un exchange cloud, vous pouvez trier des connexions entre virtuelles sur le cloud Microsoft via exchange de Ethernet du fournisseur emplacement co-création. Fournisseurs de localisation co-création peuvent proposer de connexions entre Layer 2 ou gérées Layer 3 entre-connexions entre votre infrastructure dans la fonctionnalité de co-création emplacement et le cloud Microsoft...

- **Connexions Ethernet point à point.** Vous pouvez vous connecter à vos centres de données locale/bureaux dans le cloud Microsoft par le biais des liens Ethernet point à point. Fournisseurs de Ethernet point à point peuvent proposer des connexions Layer 2, ou géré Layer 3 connexions entre votre site et le cloud Microsoft.

- **Réseaux (IPVPN) pour tout.** Vous pouvez intégrer votre réseau étendu avec le cloud Microsoft. Des fournisseurs IPVPN (généralement MPLS VPN) offrent une connectivité à tout entre vos succursales et centres de données. Le cloud Microsoft peut être interconnecté à votre réseau étendu pour lui donner un aspect comme n’importe quel autre succursale. ACCÉLÉRATEURS fournisseurs offrent généralement gérée connectivité Layer 3.

Pour plus d’informations sur les fournisseurs de connectivité, voir [ExpressRoute circuits et les domaines de routage][connectivity-providers].

### <a name="expressroute-circuit"></a>ExpressRoute circuit

Vous assurer que votre organisation a répondu aux [conditions prérequises ExpressRoute] [ expressroute-prereqs] pour vous connecter à Azure.

Si vous n’avez pas déjà fait, ajoutez un sous-réseau nommé `GatewaySubnet` à votre VNet Azure et créer une passerelle réseau virtuel ExpressRoute au moyen du service passerelle VPN Azure. Pour plus d’informations sur ce processus, voir [flux de travail ExpressRoute pour circuit États et de mise en service de circuit][ExpressRoute-provisioning].

Créer un circuit ExpressRoute comme suit :

1. Exécutez la commande PowerShell suivante :

    ```powershell
    New-AzureRmExpressRouteCircuit -Name <<circuit-name>> -ResourceGroupName <<resource-group>> -Location <<location>> -SkuTier <<sku-tier>> -SkuFamily <<sku-family>> -ServiceProviderName <<service-provider-name>> -PeeringLocation <<peering-location>> -BandwidthInMbps <<bandwidth-in-mbps>>
    ```

2. Envoyer la `ServiceKey` pour le nouveau circuit au fournisseur de services.

3. Attendez que le fournisseur de mise en service du circuit. Vous pouvez vérifier l’état d’un circuit de mise en service à l’aide de la commande PowerShell suivante :

    ```powershell
    Get-AzureRmExpressRouteCircuit -Name <<circuit-name>> -ResourceGroupName <<resource-group>>
    ```

La `Provisioning state` champ dans la `Service Provider` section de la sortie ne pourra `NotProvisioned` à `Provisioned` lorsque le circuit est prêt.

>[AZURE.NOTE]Si vous utilisez une connexion layer 3, le fournisseur doit configurer et gérer le routage pour vous ; vous fournissez les informations nécessaires pour activer le fournisseur mettre en œuvre les itinéraires appropriés.

Si vous utilisez une connexion layer 2, procédez comme suit :

1. Réserver deux/30 sous-réseaux composé d’adresses IP publiques valides pour chaque type de peering vous voulez mettre en œuvre. Ces/30 sous-réseaux permet de fournir des adresses IP pour les routeurs utilisés pour le circuit. Si vous implémentez privé, public et peering Microsoft, vous devez sous-réseaux 6 /30 avec des adresses IP publiques valides.     

2. Configurer le routage pour le circuit ExpressRoute. Vous devez exécuter la commande ci-dessous pour chaque type de peering que vous souhaitez configurer (privé, public et Microsoft).

    >[AZURE.NOTE]Voir [créer et modifier la gamme pour un circuit ExpressRoute] [ configure-expressroute-routing] pour plus d’informations. Utilisez les commandes PowerShell suivantes pour ajouter un réseau peering pour acheminer le trafic :

    ```powershell
    Set-AzureRmExpressRouteCircuitPeeringConfig -Name <<peering-name>> -Circuit <<circuit-name>> -PeeringType <<peering-type>> -PeerASN <<peer-asn>> -PrimaryPeerAddressPrefix <<primary-peer-address-prefix>> -SecondaryPeerAddressPrefix <<secondary-peer-address-prefix>> -VlanId <<vlan-id>>

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit <<circuit-name>>
    ```

3. Réserver un autre pool d’adresses IP publiques valides à utiliser pour NAT public et Microsoft peering. Il est recommandé d’avoir un pool différent pour chaque peering. Spécifier le pool à votre fournisseur de connectivité, afin qu’ils peuvent configurer publications BGP pour les plages.

[Créer un lien votre VNet(s) privé dans le cloud vers le circuit ExpressRoute][link-vnet-to-expressroute]. Utiliser les commandes PowerShell suivantes :

```
$circuit = Get-AzureRmExpressRouteCircuit -Name <<circuit-name>> -ResourceGroupName <<resource-group>>
$gw = Get-AzureRmVirtualNetworkGateway -Name <<gateway-name>> -ResourceGroupName <<resource-group>>
New-AzureRmVirtualNetworkGatewayConnection -Name <<connection-name>> -ResourceGroupName <<resource-group>> -Location <<location> -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute
```

Notez les points suivants :

- ExpressRoute utilise la bordure BGP (Gateway Protocol) pour échanger des informations de routage entre votre réseau et Azure.

- Vous pouvez vous connecter plusieurs VNets situés dans des régions différentes pour le même circuit ExpressRoute dans la mesure où tous les VNets et le circuit ExpressRoute sont situés dans la même région géopolitique.

## <a name="scalability-considerations"></a>Considérations relatives à l’extensibilité élevées

Circuits ExpressRoute fournissent un chemin d’accès de bande passante élevée entre les réseaux. En règle générale, plus la bande passante plus le coût. Bien que certains fournisseurs permettent de modifier votre bande passante, assurez-vous que vous avez choisi une bande initial dépasse vos besoins et fournit salle pour la croissance. Au cas où vous avez besoin augmenter la bande passante à l’avenir, vous vous retrouvez avec deux options.

Augmenter la bande passante. N’oubliez pas que tous les fournisseurs vous permettent pas de faire dynamiquement. Et d’éviter d’avoir à effectuer ce autant que possible. Mais, si nécessaire, après l’archivage chez votre fournisseur, exécutez les commandes ci-dessous.

```powershell
$ckt = Get-AzureRmExpressRouteCircuit -Name <<circuit-name>> -ResourceGroupName <<resource-group>>
$ckt.ServiceProviderProperties.BandwidthInMbps = <<bandwidth-in-mbps>>
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

Vous pouvez augmenter la bande passante sans perte de la connectivité. Mise à niveau la bande passante générera interruption de connectivité. Vous devez supprimer le circuit et recréer avec la nouvelle configuration.

Si vous utilisez la référence (SKU) Standard pour ExpressRoute et qu’il soit nécessaire pour mettre à niveau vers Premium ou modifier les vous êtes votre tarifs planifier (limitée ou illimité), puis exécutez les commandes ci-dessous. La `Sku.Tier` propriété peut être `Standard` ou `Premium`; la `Sku.Name` propriété peut être `MeteredData` ou `UnlimitedData`.

```powershell
$ckt = Get-AzureRmExpressRouteCircuit -Name <<circuit-name>> -ResourceGroupName <<resource-group>>

$ckt.Sku.Tier = "Premium"
$ckt.Sku.Family = "MeteredData"
$ckt.Sku.Name = "Premium_MeteredData"

 Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

>[AZURE.IMPORTANT] Vérifiez que la `Sku.Name` propriété correspond à la `Sku.Tier` et `Sku.Family`. Si vous changez la famille et couche, mais pas le nom, votre connexion est désactivée.

Vous pouvez mettre à jour la référence (SKU) sans interruption de service, mais vous ne pouvez pas passer le plan de tarification illimité à limitées. Lors de la mise à niveau la référence (SKU), votre consommation de bande passante doit rester dans la limite par défaut de la référence (SKU) standard.

> [AZURE.NOTE] ExpressRoute propose deux modes de tarification aux clients, en fonction des données de mesure ou illimitées. Consultez [ExpressRoute tarification] [ expressroute-pricing] pour plus d’informations. Les tarifs varient en fonction de la bande passante circuit. La bande passante disponible probablement peuvent varier d’un fournisseur. Utiliser la `Get-AzureRmExpressRouteServiceProvider` applet de commande pour afficher les fournisseurs disponibles dans votre région et la bande passante dont ils offrent.

Un seul circuit ExpressRoute peut prendre en charge un nombre de peerings et des liens VNet. Consultez [limites ExpressRoute] [ expressroute-limits] pour plus d’informations.

Pour des frais supplémentaires, module complémentaire ExpressRoute Premium fournit :

- Jusqu'à 10 000 itinéraires par circuit. Sans ExpressRoute Premium module complémentaire, la limite est de 4 000 itinéraires par circuit.

- Connectivité globale, l’activation d’un circuit ExpressRoute situé n’importe où dans le monde pour accéder aux ressources dans n’importe quel région plutôt que de régions simples dans le même continent.

- Augmente le nombre de liens VNet par circuit comprise entre 10 à une limite supérieure, en fonction de la bande passante du circuit.

Circuits ExpressRoute sont conçus pour permettre la rupture de réseau temporaires jusqu'à deux fois la limite de bande passante que vous achetées pour sans frais supplémentaires. Pour ce faire, à l’aide de liens redondants. Cependant, tous les fournisseurs de connectivité prend en charge cette fonctionnalité ; Vérifiez que votre fournisseur de connectivité Active cette fonctionnalité avant en fonction de celle-ci.

## <a name="availability-considerations"></a>Considérations relatives à la disponibilité

Vous pouvez configurer haute disponibilité pour votre connexion Azure de différentes manières, selon le type de fournisseur que vous utilisez et le nombre de circuits ExpressRoute et vous êtes prêt à configurer des connexions de passerelle réseau virtuel. Les points ci-dessous résument vos options de disponibilité :

- ExpressRoute ne prend pas en charge les protocoles de redondance routeur comme HSRP et VRRP pour renforcer la disponibilité. Au lieu de cela, il utilise une paire de sessions BGP par peering redondants. Pour faciliter les connexions hautement disponible pour votre réseau, Microsoft Azure dispositions avec deux ports redondants sur les deux routeurs (partie du bord Microsoft) dans une configuration actif.

- Si vous utilisez une connexion layer 2, déployer des routeurs redondants dans votre réseau local dans une configuration actif. Se connecter à circuit primaire à un seul routeur et le circuit secondaire à l’autre. Cela vous donne une connexion hautement disponible aux deux extrémités de la connexion. Cela est nécessaire si vous avez besoin du SLA ExpressRoute. Voir [SLA pour Azure ExpressRoute] [ sla-for-expressroute] pour plus d’informations.

Le diagramme suivant affiche une configuration avec routeurs redondants local connecté aux circuits principales et secondaires. Chaque circuit gère le trafic pour un public peering et un peering privé (une paire de /30 chaque peering désigné les destinataires d’espaces, comme décrit dans la section précédente).

![[1]][1]

Si vous utilisez une connexion layer 3, vérifiez qu’il fournit BGP redondant sessions qui gèrent disponibilité pour vous.

Réseaux virtuels pouvant être connectés à plusieurs ExpressRoute circuits et chaque circuits peuvent être fourni par les fournisseurs de services différente. Cette stratégie fournit disponibilité supplémentaire et urgence capacités de récupération.

Configurer un réseau privé virtuel de Site à comme un chemin d’accès de basculement pour ExpressRoute. Il s’agit applicable aux peering privé. Pour les services Azure et Office 365, Internet est le chemin d’accès de basculement uniquement.

Par défaut, BGP sessions utilisent une valeur de délai d’inactivité de 60 secondes. Une fois une session arrivées à expiration 3 fois, l’itinéraire est marqué comme étant indisponible, et tout le trafic est redirigé vers le routeur restant. Ce délai d’attente 180 secondes peut-être être trop de temps pour les applications critiques. Dans ce cas, vous pouvez modifier vos paramètres de délai d’attente BGP sur le routeur local à une valeur inférieure.

## <a name="manageability-considerations"></a>Considérations relatives à la facilité de gestion

Vous pouvez utiliser le [Kit de ressources de connectivité Azure (AzureCT)] [ azurect] pour contrôler la connectivité entre votre centre de données en local et Azure.

## <a name="security-considerations"></a>Considérations sur la sécurité

Vous pouvez configurer les options de sécurité pour votre connexion Azure de différentes manières, selon vos besoins de conformité et les problèmes de sécurité. Les points ci-dessous résument vos options de sécurité.

ExpressRoute fonctionne en layer 3. Pour empêcher les menaces dans la couche d’Application, à l’aide d’un appareil de sécurité réseau qui restreint le trafic aux ressources légitimes. En outre, connexions ExpressRoute à l’aide de peering public peuvent uniquement être lancées locales. Ainsi, un service non autorisé d’accéder à et compromettre les données locales à partir de l’Internet public.

Pour optimiser la sécurité, ajouter des équipements de sécurité réseau entre le réseau local et les routeurs périphériques fournisseur. Cela vous aidera pour restreindre l’entrée de trafic non autorisés à partir de la VNet :

![[2]][2]

À des fins d’audit ou de conformité, il peut être nécessaire interdire l’accès direct à partir de composants qui s’exécutent dans le VNet à Internet et implémenter [forcé tunnel][forced-tuneling]. Dans ce cas, le trafic Internet doit être redirigé via un proxy en cours d’exécution en local où d’audit. Le proxy peut être configuré pour bloquer le trafic non autorisé s’étalant arrière et filtrer le trafic entrant potentiellement malveillant.

![[3]][3]

Par défaut, machines virtuelles Azure exposer des points de terminaison utilisées pour fournir un accès de connexion à des fins gestion - RDP et Remote Powershell pour Windows machines virtuelles et SSH pour les machines virtuelles basés sur Linux déployé via le portail d’Azure. Pour optimiser la sécurité, ne pas activer une adresse IP publique pour vos ordinateurs virtuels et utilisez NSGs pour vous assurer que ces ordinateurs virtuels ne sont pas accessibles au public. Machines virtuelles ne doivent être disponibles à l’aide de l’adresse IP interne. Ces adresses peuvent être accessibles via le réseau ExpressRoute, en local, le personnel DevOps effectuer une configuration nécessaires ou maintenance.

Si vous devez exposer des points de terminaison de gestion pour les machines virtuelles à un réseau externe, utilisez NSGs et/ou contrôle d’accès pour limiter la visibilité de ces ports à une liste d’autorisation des adresses IP ou des réseaux.

## <a name="troubleshooting-considerations"></a>Considérations relatives à la résolution des problèmes

Si un circuit ExpressRoute précédemment fonctionne maintenant ne parvient pas à vous connecter, en l’absence de toute configuration modifications local ou au sein de votre VNet privé, vous devrez peut-être contacter le fournisseur de connectivité et les utiliser pour corriger ce problème. Vous pouvez également utiliser les commandes PowerShell Azure suivantes pour effectuer une vérification limitée et vous aider à déterminer où potentielle des problèmes :

- Vérifiez que le circuit a été mis en service :

```powershell
Get-AzureRmExpressRouteCircuit -Name <<circuit-name>> -ResourceGroupName <<resource-group>>
```

Le résultat de cette commande montre diverses propriétés pour votre circuit, y compris `ProvisioningState`, `CircuitProvisioningState`, et `ServiceProviderProvisioningState` comme illustré ci-dessous.

```
ProvisioningState                : Succeeded
Sku                              : {
                                     "Name": "Standard_MeteredData",
                                     "Tier": "Standard",
                                     "Family": "MeteredData"
                                   }
CircuitProvisioningState         : Enabled
ServiceProviderProvisioningState : NotProvisioned
```

Si la `ProvisioningState` n’est pas définie `Succeeded` une fois que vous avez essayé de créer un nouveau circuit, supprimez le circuit à l’aide de la commande ci-dessous et essayez de créer à nouveau.

```powershell
Remove-AzureRmExpressRouteCircuit -Name <<circuit-name>> -ResourceGroupName <<resource-group>>
```

Si votre fournisseur avait déjà sa mise en service du circuit et la `ProvisioningState` est défini sur `Failed`, ou le `CircuitProvisioningState` n’est pas `Enabled`, contactez votre fournisseur pour obtenir une assistance supplémentaire.

## <a name="solution-deployment"></a>Déploiement de solution

Si vous avez une infrastructure locale existante déjà configurée avec un équipement réseau appropriée, vous pouvez déployer l’architecture de référence en procédant comme suit :

Si vous avez une infrastructure locale existante déjà configurée avec un appareil VPN, vous pouvez déployer l’architecture de référence en procédant comme suit :

1. Cliquez avec le bouton droit sur le bouton ci-dessous, puis sélectionnez soit « ouvrir le lien dans un nouvel onglet » ou « Ouvrir le lien dans une nouvelle fenêtre » :  
[![Déploiement d’Azure](./media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-hybrid-network-er%2Fazuredeploy.json)

2. Attendez que le lien pour ouvrir le portail Azure, puis procédez comme suit : 
  - Le nom du **groupe de ressources** est déjà défini dans le fichier de paramètres : sélectionnez **Créer un nouveau** et entrez `ra-hybrid-er-rg` dans la zone de texte.
  - Sélectionnez la région dans la zone de liste déroulante **emplacement** .
  - Ne modifiez pas **Modèle racine Uri** ou les zones de texte **Paramètre racine Uri** .
  - Passez en revue les termes et conditions, puis cliquez sur la case à cocher **que j’accepte les termes et conditions indiquées ci-dessus** .
  - Cliquez sur le bouton **achat** .

3. Attendez que le déploiement terminer.

4. Cliquez avec le bouton droit sur le bouton ci-dessous, puis sélectionnez soit « ouvrir le lien dans un nouvel onglet » ou « Ouvrir le lien dans une nouvelle fenêtre » :  
[![Déploiement d’Azure](./media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-hybrid-network-er%2Fazuredeploy-expressRouteCircuit.json)

5. Attendez que le lien pour ouvrir le portail Azure, puis procédez comme suit : 
  - Sélectionnez **utiliser un existant** dans la section de **groupe de ressources** , puis entrez `ra-hybrid-er-rg` dans la zone de texte.
  - Sélectionnez la région dans la zone de liste déroulante **emplacement** .
  - Ne modifiez pas **Modèle racine Uri** ou les zones de texte **Paramètre racine Uri** .
  - Passez en revue les termes et conditions, puis cliquez sur la case à cocher **que j’accepte les termes et conditions indiquées ci-dessus** .
  - Cliquez sur le bouton **achat** .

6. Attendez que le déploiement terminer.

## <a name="next-steps"></a>Étapes suivantes

- Consultez [implémentation d’une architecture de réseau hautement disponible hybride] [ highly-available-network-architecture] pour plus d’informations sur l’augmentation de la disponibilité d’un réseau hybride en fonction de ExpressRoute à basculement vers une connexion VPN.

<!-- links -->
[expressroute-technical-overview]: ../expressroute/expressroute-introduction.md
[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md
[azure-powershell]: ../powershell-azure-resource-manager.md
[expressroute-prereqs]: ../expressroute/expressroute-prerequisites.md
[configure-expressroute-routing]: ../expressroute/expressroute-howto-routing-arm.md
[sla-for-expressroute]: https://azure.microsoft.com/support/legal/sla/expressroute/v1_0/
[link-vnet-to-expressroute]: ../expressroute/expressroute-howto-linkvnet-arm.md
[ExpressRoute-provisioning]: ../expressroute/expressroute-workflows.md
[expressroute-pricing]: https://azure.microsoft.com/pricing/details/expressroute/
[expressroute-limits]: ../azure-subscription-service-limits.md#networking-limits
[sample-script]: #sample-solution-script
[connectivity-providers]: ../expressroute/expressroute-introduction.md#how-can-i-connect-my-network-to-microsoft-using-expressroute
[azurect]: https://github.com/Azure/NetworkMonitoring/tree/master/AzureCT
[forced-tuneling]: ./guidance-iaas-ra-secure-vnet-hybrid.md
[highly-available-network-architecture]: ./guidance-hybrid-network-expressroute-vpn-failover.md
[arm-templates]: ../resource-group-authoring-templates.md
[naming-conventions]: ./guidance-naming-conventions.md
[solution-script]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-er/Deploy-ReferenceArchitecture.ps1
[solution-script-bash]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-er/deploy-reference-architecture.sh
[vnet-parameters]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-er/parameters/virtualNetwork.parameters.json
[virtualnetworkgateway-parameters]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-er/parameters/virtualNetworkGateway.parameters.json
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[er-circuit-parameters]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-er/parameters/expressRouteCircuit.parameters.json
[azure-powershell-download]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[azure-cli]: https://azure.microsoft.com/documentation/articles/xplat-cli-install/
[0]: ./media/guidance-hybrid-network-expressroute/figure1.png "Architecture de réseau hybride à l’aide de Azure ExpressRoute"
[1]: ./media/guidance-hybrid-network-expressroute/figure2.png "Utilisant aucun routeur redondants avec circuits principaux et secondaires ExpressRoute"
[2]: ./media/guidance-hybrid-network-expressroute/figure3.png "Ajout de périphériques de sécurité au réseau local"
[3]: ./media/guidance-hybrid-network-expressroute/figure4.png "À l’aide de forcé tunnel pour auditer le trafic Internet lié"
[4]: ./media/guidance-hybrid-network-expressroute/figure5.png "Vous avez trouvé la clé de service d’un circuit ExpressRoute"
