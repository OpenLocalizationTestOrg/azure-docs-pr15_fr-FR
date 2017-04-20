<properties
   pageTitle="Emplacements ExpressRoute | Microsoft Azure"
   description="Cet article fournit une vue d’ensemble détaillée des emplacements où les services sont proposés et comment se connecter aux régions Azure."
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor="" />
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/21/2016"
   ms.author="cherylmc" />

# <a name="expressroute-partners-and-peering-locations"></a>ExpressRoute partenaires et des emplacements homologation

Les tableaux de cet article fournissent des informations sur les fournisseurs de connectivité ExpressRoute, ExpressRoute couverture géographique, les services de cloud Microsoft pris en charge sur ExpressRoute et ExpressRoute (intégrateurs système).

## <a name="partners"></a>Fournisseurs de connectivité ExpressRoute

ExpressRoute est pris en charge dans toutes les régions Azure et emplacements. La liste suivante fournit une liste des régions Azure ExpressRoute emplacements. ExpressRoute emplacements se réfèrent à ceux où Microsoft homologues avec plusieurs fournisseurs de services.

![Carte d’emplacements][0]

Vous aurez accès aux services Azure dans toutes les régions au sein d’une région géopolitique si vous connecté au moins un emplacement ExpressRoute dans la zone géopolitique. Le tableau suivant fournit un mappage des régions Azure vers des emplacements ExpressRoute au sein d’une région géopolitique.

|**Région géopolitique**|**Régions Azure**|**ExpressRoute emplacements**|
|---|---|---|
|**Amérique du Nord**|États-Unis Extrême-Orient, US ouest, US Extrême-Orient 2, États-Unis centre, États-Unis centre sud, États-Unis centre nord, Canada centrale, Canada Moyen-Orient|Atlanta, Chicago, Dallas, Orléans, Los Angeles, New York, Seattle, Silicon Valley, Washington DC, Montréal +, Québec +, Toronto|
|**Amérique du Sud**|Brésil sud|Sao Paulo|
|**Europe**|Europe du Nord, Europe ouest, r-u ouest, Sud Royaume-Uni|Amsterdam, Dublin, Londres, Newport(Wales) +, Paris|
|**Pays d’Asie**|Extrême-Orient, Asie du Sud-est|Hong Kong, Singapour|
|**Japon**|Japon ouest, Japon orientale|Osaka, Tokyo|
|**Australie**|Australie sud-est, Moyen-Orient Australie|Melbourne, Sydney|
|**Inde**|Inde ouest, Inde centrale, Inde sud|Chennai, Mumbai|



Le tableau ci-dessous fournit des informations sur les régions et les limites géopolitiques pour nuages nationaux.

|**Région géopolitique**|**Régions Azure**|**ExpressRoute emplacements**|
|---|---|---|---|
|**Nuage de pour le gouvernement américain**|États-Unis et pour le gouvernement Iowa, États-Unis et pour le gouvernement Virginie|Chicago, Dallas, New York, Washington DC|
|**Chine**|Chine Nord, Moyen-Orient Chine|Pékin, Shanghai|
|**Allemagne**|Allemagne centrale, Allemagne Extrême-Orient|Berlin, Francfort|


Connectivité au sein de régions géopolitiques n’est pas pris en charge sur le SKU ExpressRoute standard. Vous devez activer le module complémentaire premium ExpressRoute prendre en charge de la connectivité globale. Connectivité aux environnements de cloud nationaux n’est pas pris en charge. Vous pouvez travailler avec votre fournisseur de connectivité en cas de besoin.


## <a name="connectivity-provider-locations"></a>Emplacements de fournisseur de connectivité

> [AZURE.SELECTOR]
[Emplacements selon le fournisseur](expressroute-locations.md#connectivity-provider-locations)
[fournisseurs par emplacement](expressroute-locations-providers.md#connectivity-provider-locations)

### <a name="production-azure"></a>Azure de production
| **Emplacement**  | **Fournisseurs de services** |
|---------------|-----------------------|
| **Amsterdam** | Aryaka réseaux, AT & T NetBond, British Telecom, Colt, Equinix, euNetworks, GÉANT, InterCloud, Solutions Internet - Cloud vous connecter, Interxion, niveau 3 Communications, Orange, Tata Communications, TeleCity Group, Telenor, Verizon |
| **Atlanta** | Equinix |
| **Chennai** | Communications TATA |
| **Chicago** | AT & T NetBond, Comcast, Equinix, Zayo groupe de niveau 3 Communications |
| **Dallas** | AT & T NetBond, Cologix, Equinix, niveau 3 Communications, Megaport |
| **Dublin** | Groupe COLT, Telecity |
| **Hong Kong** | British Telecom, Chine Telecom Global, Equinix, Megaport, Orange, PCCW Global limitée, Tata Communications, Verizon |
| **Londres** | AT & T NetBond, British Telecom, Colt, Equinix, InterCloud, Solutions Internet - Cloud vous connecter, Interxion, Jisc, niveau 3 Communications, MTN, NTT Communications, Orange, Tata Communications, Telecity Group, Telenor, Verizon, Vodafone |
| **Las Vegas** | Niveau 3 Communications +, Megaport
| **Toulouse** | CoreSite, Equinix, Megaport, NTT, Zayo groupe |
| **Melbourne** | AARNet, Equinix, Megaport, NEXTDC, Telstra Corporation |
| **Saint-Étienne** | Equinix, Megaport, Zayo Group |
| **Newport(Wales) +** | Prochaine génération données + |
| **Montréal** | Cologix + |
| **Mumbai** | Communications TATA |
| **Osaka** | Equinix, Internet Initiative Japon Inc - IIJ, NTT Communications, Softbank |
| **Paris** | Interxion, Equinix + |
| **Sao Paulo** | Equinix, Telefonica |
| **Seattle** | Megaport Equinix, niveau 3 Communications, |
| **Silicon Valley** | Réseaux Aryaka, AT & T NetBond, British Telecom, CenturyLink +, Comcast, Equinix, niveau 3, Orange, Tata Communications, Verizon, Zayo groupe Communications |
| **Singapour** | Réseaux Aryaka, AT & T NetBond, British Telecom, Equinix, InterCloud, Megaport, Orange, SingTel, Tata Communications, Verizon |
| **Sydney** | AARNet, AT & T NetBond, British Telecom, Equinix, Megaport, NEXTDC, Orange, Telstra Corporation, Verizon |
| **Tokyo** | Aryaka réseaux, British Telecom, Colt, Equinix, Internet Initiative Japon Inc - IIJ, NTT Communications, Softbank, Verizon |
| **Toronto** | Cologix, Equinix, Zayo Group |
| **Washington DC** | Réseaux Aryaka, AT & T NetBond, British Telecom, Comcast, Equinix, InterCloud, niveau 3, Megaport, Orange, Tata Communications, Verizon, Zayo groupe Communications |

 **+**Indique bientôt disponible

### <a name="national-cloud-environments"></a>Environnements de cloud national

#### <a name="us-government-cloud"></a>Nuage de pour le gouvernement américain

| **Emplacement**  |**Fournisseurs de services** |
|---------------|--------------------|
| **Chicago** | AT & T NetBond, Equinix, nivellement 3 Communications, Verizon |
| **Dallas** |  Equinix, Verizon + |
| **Saint-Étienne** | Verizon Equinix, niveau 3 Communications +, |
| **Washington DC** | AT & T NetBond, Equinix, nivellement 3 Communications, Verizon |

#### <a name="china"></a>Chine

| **Emplacement**  | **Fournisseurs de services** |
|---------------|-----------------------|
| **Pékin** | Telecom Chine |
| **Shanghai** |  Telecom Chine |
Pour plus d’informations, voir [ExpressRoute en Chine](http://www.windowsazure.cn/home/features/expressroute/)

#### <a name="germany"></a>Allemagne

| **Emplacement**  | **Fournisseurs de services** |
|---------------|-----------------------|
| **Berlin** | COLT +, e-abri |
| **Francfort** | COLT, Equinix, Interxion |

## <a name="nonpartners"></a>Connectivité via ne pas dans la liste de fournisseurs de services

Si votre fournisseur de connectivité ne figure pas dans les sections précédentes, vous pouvez toujours créer une connexion.

- Contactez votre fournisseur de connectivité pour voir si elles sont connectés à un des échanges dans le tableau ci-dessus. Vous pouvez vérifier les liens suivants pour obtenir davantage d’informations sur les services proposés par les fournisseurs exchange. Plusieurs fournisseurs de connectivité sont déjà connectés à échanges Ethernet.

    - [Exchange Equinix Cloud](http://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
    - [TeleCity CloudIX](http://www.telecitygroup.com/colocation-services/cloud-ix.htm)
    - [InterXion](http://www.interxion.com/)
    - [NextDC](http://www.nextdc.com/)
    - [CoreSite](http://www.coresite.com/)
    - [Cologix](http://www.cologix.com/)
- Avoir fournisseur connectivité étendre votre réseau à l’emplacement homologation de choix.
    - Assurez-vous que votre fournisseur de connectivité s’étend votre connectivité de manière hautement disponible afin qu’il n’existe aucun point unique de panne.
- Commandez un circuit ExpressRoute avec change comme fournisseur de connexion pour vous connecter à Microsoft.
    - Suivez les étapes décrites dans [créer un circuit ExpressRoute](expressroute-howto-circuit-classic.md) pour configurer la connectivité.

|**Emplacement**|**Exchange**|**Fournisseurs de connectivité**|
|-------------|------------|-------------------------|
| **Saint-Étienne** | Equinix | Lightower |
| **Seattle** | Equinix | Communications Alaska |
| **Silicon Valley** | Equinix | Communications XO |
| **Singapour** | Equinix | 1CLOUDSTAR |
| **Washington DC** | Equinix | Lightower |

## <a name="expressroute-system-integrators"></a>ExpressRoute intégrateurs

Activation de la connectivité privée à vos besoins peut être difficile, en fonction de l’échelle de votre réseau. Vous pouvez travailler avec n’importe lequel des intégrateurs répertoriées dans le tableau suivant pour vous aider à d’intégration à ExpressRoute.

|**Continent**|**Intégrateurs**|
|-------------|---------------------|
| **Pays d’Asie** | Avanade Inc., OneAs1a|
| **Europe** | Avanade Inc., Dotnet Solutions|
| **CONTACTEZ-NOUS** | Avanade Inc., Services professionnels Equinix, Perficient, Leadership de projet|

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur ExpressRoute, consultez le [Forum aux questions sur ExpressRoute](expressroute-faqs.md).
- Assurez-vous que toutes les conditions préalables sont remplies. Voir [conditions préalables ExpressRoute](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Carte d’emplacements"
