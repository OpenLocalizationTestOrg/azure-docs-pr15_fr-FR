<properties
   pageTitle="Configuration requise pour le routage pour ExpressRoute | Microsoft Azure"
   description="Cette page contient des exigences en détail pour la configuration et la gestion du routage de circuits ExpressRoute."
   documentationCenter="na"
   services="expressroute"
   authors="osamazia"
   manager="ganesr"
   editor=""/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/12/2016"
   ms.author="osamazia"/>


# <a name="expressroute-routing-requirements"></a>Configuration requise pour le routage ExpressRoute  

Pour vous connecter aux services cloud Microsoft à l’aide de ExpressRoute, vous devez configurer et gérer le routage. Certains fournisseurs de connectivité offrent la configuration et la gestion du routage un service géré. Contactez votre fournisseur de connectivité pour savoir s’il offre ce service. Si ce n’est pas le cas, vous devez respecter les conditions suivantes. 

Consultez l’article [Circuits et les domaines de routage](expressroute-circuit-peerings.md) pour obtenir une description des sessions routage devant être configuré pour faciliter la connectivité.

>[AZURE.NOTE] Microsoft ne reconnaît pas les protocoles redondance routeur (par exemple, HSRP, VRRP) pour les configurations haute disponibilité. Nous s’appuient sur une paire de sessions BGP par peering de disponibilité redondants.

## <a name="ip-addresses-used-for-peerings"></a>Adresses IP utilisées pour peerings

Vous devez réserver quelques blocs d’adresses IP pour configurer le routage entre votre réseau et les composants entreprise bord (MSEEs) Router de Microsoft. Cette section fournit une liste de conditions et décrit les règles concernant le ces adresses IP doivent être acquis et son utilisation.

### <a name="ip-addresses-used-for-azure-private-peering"></a>Adresses IP utilisées pour peering privé Azure

Vous pouvez utiliser les adresses IP privées ou des adresses IP publiques pour configurer le peerings. La plage d’adresses utilisée pour la configuration de route doit se chevauchent pas avec les plages d’adresses utilisées pour créer des réseaux virtuels dans Azure. 

 - Vous devez réserver une /29 ou les deux /30 sous-réseaux pour les interfaces de routage.
 - Les sous-réseaux utilisés pour le routage peuvent être des adresses IP privées ou des adresses IP publiques.
 - Sous-réseaux ne doivent pas entrer en conflit avec la plage réservée par le client à utiliser dans le cloud Microsoft.
 - Si un /29 sous-réseau est utilisée, il sera fractionnée en deux /30 sous-réseaux. 
     - La première /30 sous-réseau sera utilisé pour le lien principal et le second/30 sous-réseau sera utilisé pour le lien secondaire.
     - Pour chacun de la /30 sous-réseaux, vous devez utiliser la première adresse IP de le /30 sous-réseau sur votre routeur. Microsoft utilise la deuxième adresse IP de le /30 sous-réseau pour configurer une session BGP.
     - Vous devez configurer les deux sessions BGP pour notre [disponibilité SLA](https://azure.microsoft.com/support/legal/sla/) soit valide.  

#### <a name="example-for-private-peering"></a>Exemple de peering privé

Si vous choisissez d’utiliser a.b.c.d/29 pour configurer le peering, il est scindé en deux /30 sous-réseaux. Dans l’exemple ci-dessous, nous allons examiner la manière dont le sous-réseau a.b.c.d/29 est utilisé. 

a.b.c.d/29 sera fractionner a.b.c.d/30 et a.b.c.d+4/30 et transmises à Microsoft via les API de mise en service. Vous allez utiliser a.b.c.d+1 comme la VRF IP pour le PE principal et Microsoft consomme a.b.c.d+2 en tant que la VRF IP pour la MSEE principal. Vous allez utiliser a.b.c.d+5 comme la VRF IP pour le PE secondaire et Microsoft utilise a.b.c.d+6 comme la VRF IP pour la MSEE secondaire.

Pensez à un cas où vous sélectionnez 192.168.100.128/29 configurer peering privé. 192.168.100.128/29 inclut les adresses de 192.168.100.128 à 192.168.100.135, parmi lesquels :

- 192.168.100.128/30 doivent être affectées à link1, avec un fournisseur à l’aide de 192.168.100.129 et Microsoft à l’aide de 192.168.100.130.
- 192.168.100.132/30 doivent être affectées à link2, avec un fournisseur à l’aide de 192.168.100.133 et Microsoft à l’aide de 192.168.100.134.

### <a name="ip-addresses-used-for-azure-public-and-microsoft-peering"></a>Adresses IP utilisées pour public Azure et Microsoft peering

Vous devez utiliser des adresses IP publiques que vous possédez permettant de configurer les sessions BGP. Microsoft doit être en mesure de vérifier la propriété des adresses IP par le biais bureaux d’ordre Internet Routage et bureaux d’ordre routage Internet. 

- Vous devez utiliser une valeur unique/29 ou les deux /30 sous-réseaux pour configurer le BGP peering pour chaque peering par ExpressRoute circuit (si vous avez plusieurs). 
- Si un /29 sous-réseau est utilisée, il sera fractionnée en deux /30 sous-réseaux. 
    - La première /30 sous-réseau sera utilisé pour le lien principal et le second/30 sous-réseau sera utilisé pour le lien secondaire.
    - Pour chacun de la /30 sous-réseaux, vous devez utiliser la première adresse IP de le /30 sous-réseau sur votre routeur. Microsoft utilise la deuxième adresse IP de le /30 sous-réseau pour configurer une session BGP.
    - Vous devez configurer les deux sessions BGP pour notre [disponibilité SLA](https://azure.microsoft.com/support/legal/sla/) soit valide.

## <a name="public-ip-address-requirement"></a>Configuration requise des adresses IP public 

### <a name="private-peering"></a>Peering privé 

Vous pouvez choisir d’utiliser des adresses IPv4 publics ou privés pour peering privé. Nous fournissons isolement de bout en bout de votre trafic superposés d’adresses avec d’autres clients n’est pas possible en cas de peering privé. Ces adresses ne sont pas publiés sur Internet. 

### <a name="public-peering"></a>Peering public

Le chemin homologation public Azure permet de vous connecter à tous les services hébergés dans Azure sur les adresses IP publiques. Ceux-ci incluent des services figurant dans le [Forum aux questions ExpessRoute](expressroute-faqs.md) et tous les services hébergés par les éditeurs de logiciels sur Microsoft Azure. Connexion aux services Microsoft Azure sur peering public est toujours initiée à partir de votre réseau dans le réseau de Microsoft. Vous devez utiliser des adresses IP publiques pour le trafic destiné au réseau de Microsoft.

### <a name="microsoft-peering"></a>Microsoft Peering

Le chemin d’accès homologation Microsoft vous permet de vous connecter aux services cloud Microsoft qui ne sont pas prises en charge indiquent le chemin homologation public Azure. La liste des services comprend des services Office 365, tels que Skype entreprise et CRM Online, Exchange Online, SharePoint Online. Microsoft prend en charge une connectivité bidirectionnelle sur le Microsoft peering. Le trafic destiné aux services cloud Microsoft doit utiliser des adresses IPv4 publiques valides avant d’assister le réseau Microsoft.

Assurez-vous que votre adresse IP et en tant que nombre sont enregistrés sous les bureaux d’ordre présentées ci-après.

- [ARIN](https://www.arin.net/)
- [APNIC](https://www.apnic.net/)
- [AFRINIC](https://www.afrinic.net/)
- [LACNIC](http://www.lacnic.net/)
- [RIPENCC](https://www.ripe.net/)
- [RADB](http://www.radb.net/)
- [ALTDB](http://altdb.net/)

>[AZURE.IMPORTANT] Adresses IP publiques publiés vers Microsoft sur ExpressRoute ne doivent pas annoncés à Internet. Ceci peut interrompre connectivité à d’autres services Microsoft. Toutefois, les adresses IP publiques utilisés par les serveurs de votre réseau communiquent avec des points de terminaison Office 365 au sein de Microsoft peuvent faire l’objet sur ExpressRoute. 

## <a name="dynamic-route-exchange"></a>Exchange itinéraire dynamique

Routage exchange sera via le protocole eBGP. Sessions EBGP sont établies entre les MSEEs et vos routeurs. Authentification de sessions BGP n’est pas obligatoire. Si nécessaire, un hachage MD5 peut être configuré. Voir la [configurer Routage](expressroute-howto-routing-classic.md) et [Circuit de mise en service des flux de travail et les États circuit](expressroute-workflows.md) pour plus d’informations sur la configuration des sessions BGP.

## <a name="autonomous-system-numbers"></a>Numéros de système autonome

Microsoft utilise 12076 en tant que public, Azure Azure privé et Microsoft peering. Nous avons réservée APE à partir de 65515 à 65520 pour un usage interne. 16 et 32 bits sous forme de nombres sont pris en charge.

Il n’existe aucune exigence autour de symétrie de transfert de données. Les chemins d’accès aller et retour peuvent parcourir les paires de routeur différents. Itinéraires identiques doivent être publiés à partir de deux côtés sur plusieurs paires circuit vous appartenant. Métrique de routage n'êtes pas obligé d’être identiques.

## <a name="route-aggregation-and-prefix-limits"></a>Regroupement des itinéraires et les limites de préfixe

Nous prenons en charge jusqu'à 4 000 préfixes publiés nous via peering privé Azure. Elle peut être augmentée jusqu'à 10 000 préfixes si le module complémentaire premium ExpressRoute est activé. Nous allons accepter jusqu'à 200 préfixes par session BGP pour Azure public et Microsoft peering. 

La session BGP est supprimée si le nombre de préfixes dépasse la limite. Nous allons accepter itinéraires par défaut sur le lien d’homologation privé uniquement. Fournisseur doit filtrer itinéraire par défaut et les adresses IP privées (RFC 1918) l’Azure public et les chemins d’accès homologation Microsoft. 

## <a name="transit-routing-and-cross-region-routing"></a>Voies et entre région de routage

ExpressRoute ne peut pas être configuré comme routeurs lors des transferts. Vous devrez dépendent de votre fournisseur de connectivité pour les services de routage lors des transferts.

## <a name="advertising-default-routes"></a>Itinéraires par défaut publicitaires

Itinéraires par défaut sont autorisés uniquement sur Azure sessions homologation privées. Dans ce cas, nous achemine tout le trafic des réseaux virtuels associés à votre réseau. Itinéraires par défaut de publicité dans peering privé se traduit par le chemin d’accès internet à partir d’Azure bloqué. Vous devez compter sur votre périphérie de l’entreprise pour acheminer le trafic d’et à internet pour les services hébergé dans Azure. 

 Pour activer la connectivité à d’autres services Azure et services d’infrastructure, vous devez vous assurer d’un des éléments suivants est en place :

 - Azure peering public est activé pour acheminer le trafic vers les points de terminaison publics
 - Vous utilisez routage définies par l’utilisateur pour autoriser la connectivité internet pour chaque sous-réseau nécessitant la connexion à Internet.
 
>[AZURE.NOTE] Itinéraires par défaut de publicité sauts de page Windows et autres activation de licence machine virtuelle. Suivez les instructions [ici](http://blogs.msdn.com/b/mast/archive/2015/05/20/use-azure-custom-routes-to-enable-kms-activation-with-forced-tunneling.aspx) pour contourner ce problème.

## <a name="support-for-bgp-communities-preview"></a>Prise en charge des Communautés BGP (Preview)


Cette section fournit une vue d’ensemble de l’utilisation des Communautés BGP avec ExpressRoute. Microsoft annonce itinéraires dans le public et les chemins d’accès homologation Microsoft avec des itinéraires marqués avec valeurs communautaires appropriées. La raison d’être pour ce faire, et les détails sur les valeurs de la Communauté sont décrites ci-dessous. Microsoft, cependant, pas respecte toutes les valeurs de communauté balisés pour itinéraires publiés à Microsoft.

Si vous vous connectez à Microsoft par le biais ExpressRoute à n’importe quel une endroit homologation au sein d’une région géopolitique, vous aurez accès à tous les services de cloud Microsoft dans toutes les régions dans les limites géopolitiques. 

Par exemple, si vous connecté à Microsoft à Amsterdam via ExpressRoute, vous aurez accès à tous les services de cloud Microsoft hébergé en Europe du Nord et Europe ouest. 

Reportez-vous à la page [ExpressRoute partenaires et homologation emplacements](expressroute-locations.md) pour une liste détaillée des régions géopolitiques, régions Azure associées et ExpressRoute correspondante peering emplacements.

Vous pouvez acheter plusieurs circuit ExpressRoute par région géopolitique. Connexions multiples vous offre des avantages considérables sur haute disponibilité en raison de geo redondance. Dans les cas où vous disposez de plusieurs circuits ExpressRoute, vous recevrez le même jeu de préfixes publié par Microsoft sur la peering public et Microsoft peering chemins d’accès. Cela signifie que vous avez plusieurs chemins d’accès à partir de votre réseau dans Microsoft. Cela peut potentiellement entraîner des décisions de routage optimale à apporter au sein de votre réseau. Par conséquent, vous pouvez rencontrer expériences connectivité optimale pour différents services. 

Microsoft permet d’identifier préfixes publiés par le biais peering public et Microsoft homologue avec les valeurs de communauté BGP appropriées indiquant la région les préfixes sont hébergés dans. Vous pouvez appuyer sur les valeurs de la Communauté à prendre des décisions de routage appropriées pour proposer [routage optimal pour les clients](expressroute-optimize-routing.md).

| **Région géopolitique** | **Région Microsoft Azure** | **Valeur de communauté BGP** |
|---|---|---|
| **Amérique du Nord** |    |  |
|    | États-Unis Extrême-Orient | 12076:51004 |
|    | États-Unis Extrême-Orient 2 | 12076:51005 |
|    | États-Unis Ouest | 12076:51006 |
|    | États-Unis Ouest 2 | 12076:51026 |
|    | États-Unis centre ouest | 12076:51027 |
|    | États-Unis centre nord | 12076:51007 |
|    | États-Unis centre sud | 12076:51008 |
|    | États-Unis centre | 12076:51009 |
|    | Canada Central | 12076:51020 |
|    | Canada Extrême-Orient | 12076:51021 |
| **Amérique du Sud** |  |  |
|    | Brésil sud | 12076:51014 |
| **Europe** |    |  |
|    | Europe du Nord | 12076:51003 |
|    | Europe occidentale | 12076:51002 |
| **Asie-Pacifique** |    |   |
|    | Asie de l’est | 12076:51010 |
|    | Pays d’Asie du Sud-est | 12076:51011 |
| **Japon** |     |   |
|    | Orient Japon | 12076:51012 |
|    | Japon ouest | 12076:51013 |
| **Australie** |    |   | 
|    | Australie est | 12076:51015 |
|    | Australie sud-est. | 12076:51016 |
| **Inde** |    |   |
|    | Inde sud | 12076:51019 |
|    | Inde ouest | 12076:51018 |
|    | Inde centrale | 12076:51017 |

Tous les itinéraires publiés par Microsoft seront marqués avec la valeur de la Communauté approprié. 

>[AZURE.IMPORTANT] Les préfixes globaux seront marqués avec une valeur de communauté approprié et seront annoncés que lorsque le module complémentaire ExpressRoute premium est activé.


En plus de ce qui précède, Microsoft permet également d’identifier les préfixes basés sur le service auquel ils appartiennent. Cela s’applique uniquement à la peering Microsoft. Le tableau suivant fournit un mappage de valeur communautaire BGP du service.

| **Service** | **Valeur de communauté BGP** |
|---|---|
| **Exchange** | 12076:5010 |
| **SharePoint** | 12076:5020 |
| **Skype entreprise** | 12076:5030 |
| **CRM en ligne** | 12076:5040 |
| **D’autres Services Office 365** | 12076:5100 |

>[AZURE.NOTE] Microsoft ne respecte pas les valeurs de communauté BGP définis sur les itinéraires publiés à Microsoft.

## <a name="next-steps"></a>Étapes suivantes

- Configurer votre connexion ExpressRoute.

    - [Créer un circuit ExpressRoute pour le modèle de déploiement classique](expressroute-howto-circuit-classic.md) ou [créer et modifier un circuit ExpressRoute à l’aide du Gestionnaire de ressources Azure](expressroute-howto-circuit-arm.md)
    - [Configurer le routage pour le modèle de déploiement classique](expressroute-howto-routing-classic.md) ou [configurer routage pour le modèle de déploiement du Gestionnaire de ressources](expressroute-howto-routing-arm.md)
    - [Lier un VNet classique à un circuit ExpressRoute](expressroute-howto-linkvnet-classic.md) ou [lier une VNet Gestionnaire de ressources à un circuit ExpressRoute](expressroute-howto-linkvnet-arm.md)


