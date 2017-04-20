<properties
   pageTitle="Configuration requise NAT pour circuits ExpressRoute | Microsoft Azure"
   description="Cette page contient des exigences en détail configurer et gérer des NAT pour circuits ExpressRoute."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carmonm"
   editor=""/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/10/2016"
   ms.author="cherylmc"/>

# <a name="expressroute-nat-requirements"></a>Configuration requise ExpressRoute NAT

Pour vous connecter aux services cloud Microsoft à l’aide de ExpressRoute, vous devez configurer et gérer des NAT. Certains fournisseurs de connectivité offrent la configuration et la gestion NAT un service géré. Contactez votre fournisseur de connectivité pour savoir s’il offre tel qu’un service. Si ce n’est pas le cas, vous devez respecter les exigences décrites ci-dessous. 

Examinez la page [domaines routage et circuits ExpressRoute](expressroute-circuit-peerings.md) pour avoir un aperçu des différents domaines routage. Fonction des besoins adresse IP publics pour Azure public et Microsoft peering, nous vous recommandons de configurer NAT entre votre réseau et Microsoft. Cette section fournit une description détaillée de l’infrastructure NAT que vous devez configurer.

## <a name="nat-requirements-for-azure-public-peering"></a>Configuration requise NAT pour peering public Azure

Le chemin homologation public Azure permet de vous connecter à tous les services hébergés dans Azure sur les adresses IP publiques. Ceux-ci incluent des services figurant dans le [Forum aux questions ExpessRoute](expressroute-faqs.md) et tous les services hébergés par les éditeurs de logiciels sur Microsoft Azure. Connexion aux services Microsoft Azure sur peering public est toujours initiée à partir de votre réseau dans le réseau de Microsoft. Le trafic destiné à Microsoft Azure sur peering public doit être SNATed aux adresses IPv4 publiques valides avant d’assister le réseau Microsoft. L’illustration suivante fournit une image globale de comment le NAT peut être configuré pour répondre à l’exigence ci-dessus.

![](./media/expressroute-nat/expressroute-nat-azure-public.png) 

### <a name="nat-ip-pool-and-route-advertisements"></a>Annonces pool et itinéraire IP NAT

Vous devez vous assurer que le trafic est Entrez le chemin d’accès homologation public Azure avec des adresses IPv4 public valide. Microsoft doit être en mesure de valider le propriétaire du pool d’adresses IPv4 NAT par rapport à un registre Internet routage régionaux (relative) ou par un Registre routage Internet (tri). Une vérification sera effectuée à partir du nombre en tant que ressources en cours avec et les adresses IP utilisées pour le NAT. Reportez-vous à la page [Configuration requise pour le routage ExpressRoute](expressroute-routing.md) pour plus d’informations sur le routage bureaux d’ordre.
 
Il n’existe aucune restriction sur la longueur du préfixe IP NAT publié par le biais cette peering. Vous devez surveiller le pool NAT et vous assurer que vous ne pas manque de sessions NAT.

>[AZURE.IMPORTANT] Le pool IP NAT publié vers Microsoft ne doit pas annoncé à Internet. Cela sauts de page connectivité à d’autres services Microsoft.

## <a name="nat-requirements-for-microsoft-peering"></a>Configuration requise NAT pour Microsoft peering

Le chemin d’accès homologation Microsoft vous permet de vous connecter aux services cloud Microsoft qui ne sont pas prises en charge indiquent le chemin homologation public Azure. La liste des services comprend des services Office 365, tels que Skype entreprise et CRM Online, Exchange Online, SharePoint Online. Microsoft s’attend à prendre en charge une connectivité bidirectionnelle sur le Microsoft peering. Le trafic destiné aux services cloud Microsoft doit être SNATed aux adresses IPv4 publiques valides avant d’assister le réseau Microsoft. Le trafic destiné à votre réseau à partir de services cloud Microsoft doit être SNATed avant d’assister votre réseau. L’illustration suivante fournit une image globale de la façon dont le NAT doit être configuré pour Microsoft peering.
 
![](./media/expressroute-nat/expressroute-nat-microsoft.png) 


#### <a name="traffic-originating-from-your-network-destined-to-microsoft"></a>Trafic provenant de votre réseau destiné à Microsoft

- Vous devez vous assurer que le trafic est Entrez le chemin d’accès homologation Microsoft avec une adresse IPv4 publique valide. Microsoft doit être en mesure de valider le propriétaire du pool d’adresses IPv4 NAT contre le Registre internet routage régionaux (relative) ou par un Registre routage internet (tri). Une vérification sera effectuée à partir du nombre en tant que ressources en cours avec et les adresses IP utilisées pour le NAT. Reportez-vous à la page [Configuration requise pour le routage ExpressRoute](expressroute-routing.md) pour plus d’informations sur le routage bureaux d’ordre.

- Adresses IP utilisées pour la configuration homologation publique Azure et les autres circuits ExpressRoute ne doivent pas être publiés dans Microsoft via la session BGP. Il n’existe aucune restriction sur la longueur du préfixe IP NAT publié par le biais cette peering.

    >[AZURE.IMPORTANT] Le pool IP NAT publié vers Microsoft ne doit pas annoncé à Internet. Cela sauts de page connectivité à d’autres services Microsoft.

#### <a name="traffic-originating-from-microsoft-destined-to-your-network"></a>Trafic provenant de Microsoft destiné à votre réseau

- Certains scénarios nécessitent Microsoft pour démarrer une conversation de connectivité aux points de terminaison de service hébergé au sein de votre réseau. Un exemple classique du scénario serait connectivité aux serveurs ADFS hébergé dans votre réseau à partir d’Office 365. Dans ce cas, vous devez perdre de préfixes appropriés à partir de votre réseau dans le Microsoft peering. 

- Vous devez le trafic SNAT destinés à des adresses IP au sein de votre réseau à partir de Microsoft. 

## <a name="next-steps"></a>Étapes suivantes

- Reportez-vous à la configuration requise pour le [routage](expressroute-routing.md) et la [qualité de service](expressroute-qos.md).
- Pour plus d’informations de flux de travail, voir [circuit ExpressRoute mise en service circuit États et flux de travail](expressroute-workflows.md).
- Configurer votre connexion ExpressRoute.

    - [Créer un circuit ExpressRoute](expressroute-howto-circuit-classic.md)
    - [Configurer le routage](expressroute-howto-routing-classic.md)
    - [Lier un VNet à un circuit ExpressRoute](expressroute-howto-linkvnet-classic.md)

