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
|**Amérique du Nord**|États-Unis Extrême-Orient, US ouest, Extrême-Orient US 2, États-Unis centre, États-Unis centre sud, États-Unis centre nord, Canada centrale, Canada orientale|Atlanta, Chicago, Dallas, Orléans, Los Angeles, New York, Seattle, Silicon Valley, Washington DC, Montréal +, Québec +, Toronto|
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

| **Fournisseur de services**  |**Microsoft Azure** | **Office 365 et CRM en ligne** | **Emplacements** |
|-----------------------|--------------------|----------------|---------------|
| **AARNet** | Prise en charge | Prise en charge | Melbourne, Sydney |
| **[Réseaux Aryaka]( http://www.aryaka.com/)** | Prise en charge | Prise en charge | Amsterdam, Silicon Valley, Singapour, Tokyo, Washington DC |
| **[AT & T NetBond]( https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** | Prise en charge | Prise en charge | Amsterdam, Chicago, Dallas, Londres, Silicon Valley, Singapour, Sydney, Washington DC |
| **[British Telecom]( http://www.globalservices.bt.com/uk/en/news/bt_to_provide_connectivity_to_microsoft_azure)** | Prise en charge | Prise en charge | Amsterdam, Hong Kong, Londres, Silicon Valley, Singapour, Sydney, Tokyo, Washington DC |
|**CenturyLink** | À venir | À venir| Silicon Valley |
|**Chine Telecom Global** | Prise en charge | Non pris en charge | Hong Kong |
|**[Cologix](http://www.cologix.com/solutions/cloud-connect/public-clouds/microsoft-cloud/)** | Prise en charge | À venir | Toronto Dallas, Montréal +, |
| **[COLT]( http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)**  |  Prise en charge | Prise en charge | Amsterdam, Dublin, Londres, Tokyo |
| **Comcast** | Prise en charge | Prise en charge | Chicago, Silicon Valley, Washington DC |
| **[CoreSite](http://www.coresite.com/solutions/cloud-services/public-cloud-providers/microsoft-azure-expressroute)** | Prise en charge | Prise en charge | Toulouse | 
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** | Prise en charge | Prise en charge | Amsterdam, Atlanta, Chicago, Dallas, Hong Kong, Londres, Los Angeles, Melbourne, New York, Osaka, Paris +, Sao Paulo, Seattle, Silicon Valley, Singapour, Sydney, Tokyo, Toronto, Washington DC |
| **euNetworks** |  Prise en charge | Prise en charge | Amsterdam |
| **GÉANT** | Prise en charge | Prise en charge | Amsterdam |
| **[Internet Initiative Japon Inc - IIJ](http://www.iij.ad.jp/en/news/pressrelease/2015/1216-2.html)** |  Prise en charge | Prise en charge | Osaka, Tokyo |
| **[InterCloud]( https://www.intercloud.com/)** | Prise en charge | Prise en charge | Amsterdam, Londres, Singapour, Washington DC |
| **Solutions Internet - Cloud se connecter** | Prise en charge | Prise en charge | Amsterdam, Londres |
| **[Interxion](http://www.interxion.com/why-interxion/colocate-with-the-clouds/colocated-hybrid-cloud/microsoft-azure/)**  | Prise en charge | Prise en charge | Amsterdam, Londres, Paris |
| **Jisc** | Prise en charge | Prise en charge | Londres | 
| **[Communications de niveau 3]( http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** | Prise en charge | Prise en charge | Amsterdam, Chicago, Dallas, Orléans +, Londres, Seattle, Silicon Valley, Washington DC |
| **Megaport** | Prise en charge | Prise en charge | Dallas, Hong Kong, Las Vegas, Los Angeles, Melbourne, New York Seattle, Singapour, Sydney, Washington DC |
| **MTN** | Prise en charge | Prise en charge | Londres |
| **Données de génération suivante** | À venir | À venir | Newport(Wales) + |
| **NEXTDC** | Prise en charge | Prise en charge | Melbourne, Sydney |
| **Communications NTT** | Prise en charge | Prise en charge | Londres, Los Angeles, Osaka, Tokyo |
| **[Orange]( http://www.orange-business.com/en/products/business-vpn-galerie)** | Prise en charge | Prise en charge | Amsterdam, Hong Kong, Londres, Silicon Valley, Singapour, Sydney, Washington DC |
| **Limité PCCW Global** | Prise en charge | Prise en charge | Hong Kong |
| **[SingTel]( http://info.singtel.com/about-us/news-releases/singtel-provide-secure-private-access-microsoft-azure-public-cloud)** |  Prise en charge | Prise en charge | Singapour |
| **Softbank** | Prise en charge | Prise en charge | Osaka, Tokyo | 
| **[Communications TATA](http://www.tatacommunications.com/lp/izo/azure/azure_index.html)** | Prise en charge | Prise en charge | Amsterdam, Chennai, Hong Kong, Londres, Mumbai, Silicon Valley, Singapour, Washington DC |
| **[Groupe TeleCity]( http://www.telecitygroup.com/investor-centre/news_details.htm?locid=03100500400b00d&xml)** | Prise en charge | Prise en charge | Amsterdam, Dublin, Londres |
| **Telefonica** | Prise en charge | Prise en charge | Sao Paulo |
| **Telenor** | Prise en charge | Prise en charge | Amsterdam, Londres |
| **[Corporation Telstra.]( http://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** | Prise en charge | Prise en charge | Melbourne, Sydney |
| **[Verizon](http://www.verizonenterprise.com/products/networking/secure-cloud-interconnect/)** | Prise en charge | Prise en charge | Amsterdam, Hong Kong, Londres, Silicon Valley, Singapour, Sydney, Tokyo, Washington DC |
| **Vodafone** | Prise en charge | Non pris en charge | Londres | 
| **[Groupe Zayo]( http://www.zayo.com/solutions/industries/connect-to-cloud-data-centers/cloud-connectivity/microsoft-expressroute/)** | Prise en charge | Prise en charge | Chicago, Los Angeles, New York Silicon Valley, Toronto, Washington DC |

 **+**Indique bientôt disponible

### <a name="national-cloud-environments"></a>Environnements de cloud national

#### <a name="us-government-cloud"></a>Nuage de pour le gouvernement américain

| **Fournisseur de services**  |**Microsoft Azure** | **Office 365** | **Emplacements** |
|-----------------------|--------------------|----------------|---------------|
| **[AT & T NetBond]( https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** | Prise en charge | Prise en charge | Chicago, Washington DC |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** | Prise en charge | Prise en charge | Chicago, Dallas, New York, Washington DC |
| **[Communications de niveau 3]( http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** | Prise en charge | Prise en charge | Chicago, New York +, Washington DC |
| **[Verizon](http://news.verizonenterprise.com/2014/04/secure-cloud-interconnect-solutions-enterprise/)** | Prise en charge | Prise en charge | Chicago, Dallas +, New York, Washington DC |

#### <a name="china"></a>Chine

| **Fournisseur de services**  |**Microsoft Azure** | **Office 365** | **Emplacements** |
|-----------------------|--------------------|----------------|---------------|
| **Telecom Chine** | Prise en charge | Non pris en charge | Pékin, Shanghai|
Pour plus d’informations, voir [ExpressRoute en Chine](http://www.windowsazure.cn/home/features/expressroute/).

#### <a name="germany"></a>Allemagne

| **Fournisseur de services**  |**Microsoft Azure** | **Office 365** | **Emplacements** |
|-----------------------|--------------------|----------------|---------------|
| **[COLT]( http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** | Prise en charge | Non pris en charge | Berlin +, Francfort|
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** | Prise en charge | Non pris en charge | Francfort|
| **e-abri** | Prise en charge | Non pris en charge | Berlin|
| **Interxion** | Prise en charge | Non pris en charge | Francfort|

## <a name="nonpartners"></a>Connectivité via ne pas dans la liste de fournisseurs de services

Si votre fournisseur de connectivité ne figure pas dans les sections précédentes, vous pouvez toujours créer une connexion.

- Contactez votre fournisseur de connectivité pour voir si elles sont connectés à un des échanges dans le tableau ci-dessus. Vous pouvez vérifier les liens suivants pour obtenir davantage d’informations sur les services proposés par les fournisseurs exchange. Plusieurs fournisseurs de connectivité sont déjà connectés à échanges Ethernet.

    - [Exchange Equinix Cloud](http://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
    - [TeleCity CloudIX](http://www.telecitygroup.com/colocation-services/cloud-ix.htm)
    - [Interxion](http://www.interxion.com/why-interxion/colocate-with-the-clouds/colocated-hybrid-cloud/microsoft-azure/)
    - [NextDC](http://www.nextdc.com/)
    - [CoreSite](http://www.coresite.com/)
    - [Cologix](http://www.cologix.com/)
- Avoir fournisseur connectivité étendre votre réseau à l’emplacement homologation de choix.
    - Assurez-vous que votre fournisseur de connectivité s’étend votre connectivité de manière hautement disponible afin qu’il n’existe aucun point unique de panne.
- Commandez un circuit ExpressRoute avec change comme fournisseur de connexion pour vous connecter à Microsoft.
    - Suivez les étapes décrites dans [créer un circuit ExpressRoute](expressroute-howto-circuit-classic.md) pour configurer la connectivité.

|**Fournisseur de connectivité**|**Exchange**|**Emplacements**|
|---|---|---|
|**[1CLOUDSTAR](http://www.1cloudstar.com/service/cloudconnect-azure-expressroute/)**|Equinix|Singapour|
|**Communications Alaska**|Equinix|Seattle|
|**[Lightower](http://www.lightower.com/network-solutions/cloud-connect/#microsoft-azure )**|Equinix|New York, Washington DC|
|**[Communications XO](http://www.xo.com/)**|Equinix|Silicon Valley|


## <a name="expressroute-system-integrators"></a>ExpressRoute intégrateurs

Activation de la connectivité privée à vos besoins peut être difficile, en fonction de l’échelle de votre réseau. Vous pouvez travailler avec n’importe lequel des intégrateurs répertoriées dans le tableau suivant pour vous aider à d’intégration à ExpressRoute.

|**Intégration de système**|**Continent**|
|---|---|
|**[Avanade Inc.](http://www.avanade.com/)**| Pays d’Asie, Europe, États-Unis |
|**[Solutions dotnet](http://www.dotnetsolutions.co.uk/)**| Europe |
|**[Services professionnels Equinix](http://www.equinix.com/services/consulting/)**|CONTACTEZ-NOUS|
|**[OneAs1a](http://www.oneas1a.com/express-connect-any-cloud-ecac)** | Pays d’Asie |
|**[Perficient](http://www.perficient.com/Partners/Microsoft/Cloud/Azure-ExpressRoute)** | CONTACTEZ-NOUS |
|**[Leadership de projet](http://www.projectleadership.net/azure)** | CONTACTEZ-NOUS |

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur ExpressRoute, consultez le [Forum aux questions sur ExpressRoute](expressroute-faqs.md).
- Assurez-vous que toutes les conditions préalables sont remplies. Voir [conditions préalables ExpressRoute](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Carte d’emplacements"
