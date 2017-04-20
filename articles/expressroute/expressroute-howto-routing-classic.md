<properties
   pageTitle="Comment faire pour configurer le routage pour un circuit ExpressRoute pour le modèle de déploiement classique à l’aide de PowerShell | Microsoft Azure"
   description="Cet article décrit les étapes de création et de mise en service privé, public et Microsoft peering d’un circuit ExpressRoute. Cet article vous indique également comment vérifier l’état, mettre à jour ou supprimer des peerings pour votre circuit."
   documentationCenter="na"
   services="expressroute"
   authors="ganesr"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/10/2016"
   ms.author="ganesr"/>

# <a name="create-and-modify-routing-for-an-expressroute-circuit"></a>Créer et modifier la gamme pour un circuit ExpressRoute

> [AZURE.SELECTOR]
[Portail Azure - Gestionnaire de ressources](expressroute-howto-routing-portal-resource-manager.md)
[PowerShell - Gestionnaire de ressources](expressroute-howto-routing-arm.md)
[PowerShell - classique](expressroute-howto-routing-classic.md)



Cet article vous explique comment procéder pour créer et gérer la configuration du routage pour un circuit ExpressRoute à l’aide de PowerShell et le modèle de déploiement classique. Les étapes ci-dessous également vous vous montrent comment créer peerings pour un circuit ExpressRoute et vérifier le statut, la mise à jour ou la supprimer.


**À propos des modèles de déploiement d’Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

## <a name="configuration-prerequisites"></a>Conditions préalables de configuration

- Vous avez besoin de la dernière version des modules PowerShell Azure. Vous pouvez télécharger le dernier module PowerShell de la section PowerShell de la [page de téléchargements Azure](https://azure.microsoft.com/downloads/). Suivez les instructions dans la page [comment installer et configurer Azure PowerShell](../powershell-install-configure.md) pour des instructions détaillées sur la façon de configurer votre ordinateur pour utiliser les modules PowerShell Azure. 
- Vérifiez que vous avez vérifié la page [Configuration requise](expressroute-prerequisites.md) , la page [Configuration requise pour le routage](expressroute-routing.md) et la page [flux de travail](expressroute-workflows.md) avant de commencer la configuration.
- Vous devez avoir un circuit ExpressRoute actif. Suivez les instructions pour [créer un circuit ExpressRoute](expressroute-howto-circuit-classic.md) et avoir le circuit activé par votre fournisseur de connectivité avant de poursuivre. Le circuit ExpressRoute doit être placé dans un état généré et activé pour pouvoir exécuter les applets de commande décrite ci-dessous.

>[AZURE.IMPORTANT] Ces instructions s’appliquent uniquement à circuits créés avec les fournisseurs de service proposant des services de connectivité Layer 2. Si vous utilisez un fournisseur de services proposant des services Layer 3 gérées (généralement un IPVPN, comme MPLS), votre fournisseur de connectivité sera configurer et gérer le routage pour vous.

Vous pouvez configurer un, deux ou tous les trois peerings (public privé, Azure Azure et Microsoft) pour un circuit ExpressRoute. Vous pouvez configurer peerings dans n’importe quel ordre que vous choisissez. Toutefois, vous devez vous assurer que vous terminer la configuration de chacun d’eux homologation à la fois. 

## <a name="azure-private-peering"></a>Azure peering privé

Cette section fournit des instructions sur la façon de créer, obtenir, mettre à jour et supprimer la configuration homologation privée Azure pour un circuit ExpressRoute. 

### <a name="to-create-azure-private-peering"></a>Pour créer peering privé Azure

1. **Importer le module PowerShell pour ExpressRoute.**
    
    Vous devez importer les modules Azure et ExpressRoute dans la session PowerShell afin de commencer à utiliser les applets de commande ExpressRoute. Exécutez les commandes suivantes pour importer les modules Azure et ExpressRoute dans la session PowerShell.  

        Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
        Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

2. **Créer un circuit ExpressRoute.**
    
    Suivez les instructions pour créer un [circuit ExpressRoute](expressroute-howto-circuit-classic.md) et sa mise en service par le fournisseur de connectivité. Si votre fournisseur de connectivité propose des services Layer 3 gérés, vous pouvez demander votre fournisseur de connectivité à activer peering privé Azure pour vous. Dans ce cas, vous n’aurez pas besoin de suivre les instructions figurant dans les sections suivantes. Toutefois, si votre fournisseur de connectivité ne parvient pas routage pour vous, après avoir créé votre circuit, suivez les instructions ci-dessous. 

3. **Vérifier le circuit ExpressRoute pour vous assurer qu’il est mis en service.**

    Vous devez tout d’abord vérifier si le circuit ExpressRoute est mis en service et également activé. Consultez l’exemple ci-dessous.

        PS C:\> Get-AzureDedicatedCircuit -ServiceKey "*********************************"

        Bandwidth                        : 200
        CircuitName                      : MyTestCircuit
        Location                         : Silicon Valley
        ServiceKey                       : *********************************
        ServiceProviderName              : equinix
        ServiceProviderProvisioningState : Provisioned
        Sku                              : Standard
        Status                           : Enabled

    Assurez-vous que le circuit affiche comme Provisioned et de l’activation. Si elle n’est pas, collaborer avec votre fournisseur de connectivité pour obtenir votre circuit à l’état requises et l’état.

        ServiceProviderProvisioningState : Provisioned
        Status                           : Enabled


4. **Configurer peering privé Azure pour le circuit.**

    Assurez-vous de disposer des éléments suivants avant de procéder aux étapes suivantes :

    - Un /30 sous-réseau pour le lien principal. Cela ne doit pas faire partie d’un espace d’adressage réservé pour les réseaux virtuels.
    - Un /30 sous-réseau du lien secondaire. Cela ne doit pas faire partie d’un espace d’adressage réservé pour les réseaux virtuels.
    - Un ID de réseau local virtuel valide pour établir cette peering sur. S’assurer qu’aucune peering du circuit n’utilise le même ID de réseau local virtuel.
    - En tant que nombre pour peering. Vous pouvez utiliser 2 octets et 4 octets sous forme de nombres. Vous pouvez utiliser un privé en tant que nombre pour cette peering. Vérifiez que vous n’utilisez pas 65515.
    - Un hachage MD5 si vous choisissez d’utiliser une. **Cette étape est facultative**.
    
    Vous pouvez exécuter l’applet de commande suivante pour configurer peering privé Azure pour votre circuit.

        New-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 100

    Vous pouvez utiliser l’applet de commande ci-dessous si vous choisissez d’utiliser un hachage MD5.

        New-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 100 -SharedKey "A1B2C3D4"

    >[AZURE.IMPORTANT] Assurez-vous d’avoir indiqué votre numéro AS comme ASN homologation, pas des clients ASN.

### <a name="to-view-azure-private-peering-details"></a>Pour afficher les détails homologation privés Azure

Vous pouvez obtenir des détails sur la configuration à l’aide de l’applet de commande suivante

    Get-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"
    
    AdvertisedPublicPrefixes       : 
    AdvertisedPublicPrefixesState  : Configured
    AzureAsn                       : 12076
    CustomerAutonomousSystemNumber : 
    PeerAsn                        : 1234
    PrimaryAzurePort               : 
    PrimaryPeerSubnet              : 10.0.0.0/30
    RoutingRegistryName            : 
    SecondaryAzurePort             : 
    SecondaryPeerSubnet            : 10.0.0.4/30
    State                          : Enabled
    VlanId                         : 100


### <a name="to-update-azure-private-peering-configuration"></a>Mettre à jour Azure configuration homologation privée

Vous pouvez mettre à jour une partie quelconque de la configuration à l’aide de l’applet de commande suivante. Dans l’exemple ci-dessous, l’ID de circuit est mise à jour comprise entre 100 et 500.

    Set-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 500 -SharedKey "A1B2C3D4"

### <a name="to-delete-azure-private-peering"></a>Pour supprimer peering privé Azure

Vous pouvez supprimer votre configuration homologation en exécutant l’applet de commande suivante.

>[AZURE.WARNING] Vous devez vous assurer que tous les réseaux virtuels sont liés à partir du circuit ExpressRoute avant d’exécuter cette applet de commande. 

    Remove-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"


## <a name="azure-public-peering"></a>Peering public Azure

Cette section fournit des instructions sur la façon de créer, obtenir, mettre à jour et supprimer la configuration homologation publique Azure pour un circuit ExpressRoute.

### <a name="to-create-azure-public-peering"></a>Pour créer peering public Azure

1. **Importer le module PowerShell pour ExpressRoute.**
    
    Vous devez importer les modules Azure et ExpressRoute dans la session PowerShell afin de commencer à utiliser les applets de commande ExpressRoute. Exécutez les commandes suivantes pour importer les modules Azure et ExpressRoute dans la session PowerShell. 

        Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
        Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

2. **Créer un circuit ExpressRoute**
    
    Suivez les instructions pour créer un [circuit ExpressRoute](expressroute-howto-circuit-classic.md) et sa mise en service par le fournisseur de connectivité. Si votre fournisseur de connectivité propose des services Layer 3 gérés, vous pouvez demander votre fournisseur de connectivité à activer peering public Azure pour vous. Dans ce cas, vous n’aurez pas besoin de suivre les instructions figurant dans les sections suivantes. Toutefois, si votre fournisseur de connectivité ne parvient pas routage pour vous, après avoir créé votre circuit, suivez les instructions ci-dessous.

3. **Vérifier circuit ExpressRoute pour vous assurer qu’il est mis en service**

    Vous devez tout d’abord vérifier si le circuit ExpressRoute est mis en service et également activé. Consultez l’exemple ci-dessous.

        PS C:\> Get-AzureDedicatedCircuit -ServiceKey "*********************************"

        Bandwidth                        : 200
        CircuitName                      : MyTestCircuit
        Location                         : Silicon Valley
        ServiceKey                       : *********************************
        ServiceProviderName              : equinix
        ServiceProviderProvisioningState : Provisioned
        Sku                              : Standard
        Status                           : Enabled

    Assurez-vous que le circuit affiche comme Provisioned et de l’activation. Si elle n’est pas, collaborer avec votre fournisseur de connectivité pour obtenir votre circuit à l’état requises et l’état.

        ServiceProviderProvisioningState : Provisioned
        Status                           : Enabled

    

4. **Configurer peering public Azure pour le circuit**

    Vous assurer que les informations suivantes avant de procéder davantage.

    - Un /30 sous-réseau pour le lien principal. Ce doit être un préfixe IPv4 public valide.
    - Un /30 sous-réseau du lien secondaire. Ce doit être un préfixe IPv4 public valide.
    - Un ID de réseau local virtuel valide pour établir cette peering sur. S’assurer qu’aucune peering du circuit n’utilise le même ID de réseau local virtuel.
    - En tant que nombre pour peering. Vous pouvez utiliser 2 octets et 4 octets sous forme de nombres.
    - Un hachage MD5 si vous choisissez d’utiliser une. **Cette étape est facultative**.
    
    Vous pouvez exécuter l’applet de commande suivante pour configurer peering public Azure pour votre circuit

        New-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 200

    Vous pouvez utiliser l’applet de commande ci-dessous si vous choisissez d’utiliser un hachage MD5

        New-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 200 -SharedKey "A1B2C3D4"

    >[AZURE.IMPORTANT] Assurez-vous d’avoir indiqué votre numéro AS comme ASN homologation et non customer ASN.

### <a name="to-view-azure-public-peering-details"></a>Pour afficher les détails homologation publics Azure

Vous pouvez obtenir des détails sur la configuration à l’aide de l’applet de commande suivante

    Get-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"
    
    AdvertisedPublicPrefixes       : 
    AdvertisedPublicPrefixesState  : Configured
    AzureAsn                       : 12076
    CustomerAutonomousSystemNumber : 
    PeerAsn                        : 1234
    PrimaryAzurePort               : 
    PrimaryPeerSubnet              : 131.107.0.0/30
    RoutingRegistryName            : 
    SecondaryAzurePort             : 
    SecondaryPeerSubnet            : 131.107.0.4/30
    State                          : Enabled
    VlanId                         : 200


### <a name="to-update-azure-public-peering-configuration"></a>Mettre à jour configuration homologation publique Azure

Vous pouvez mettre à jour une partie quelconque de la configuration à l’aide de l’applet de commande suivante

    Set-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 600 -SharedKey "A1B2C3D4"

L’ID de circuit est mis à jour à partir de 200 600 dans l’exemple ci-dessus.

### <a name="to-delete-azure-public-peering"></a>Pour supprimer peering public Azure

Vous pouvez supprimer votre configuration homologation en exécutant l’applet de commande suivante

    Remove-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"

## <a name="microsoft-peering"></a>Microsoft peering

Cette section fournit des instructions sur la façon de créer, obtenir, mettre à jour et supprimer la configuration homologation Microsoft pour un circuit ExpressRoute. 

### <a name="to-create-microsoft-peering"></a>Pour créer Microsoft peering

1. **Importer le module PowerShell pour ExpressRoute.**
    
    Vous devez importer les modules Azure et ExpressRoute dans la session PowerShell afin de commencer à utiliser les applets de commande ExpressRoute. Exécutez les commandes suivantes pour importer les modules Azure et ExpressRoute dans la session PowerShell.  

        Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
        Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

2. **Créer un circuit ExpressRoute**
    
    Suivez les instructions pour créer un [circuit ExpressRoute](expressroute-howto-circuit-classic.md) et sa mise en service par le fournisseur de connectivité. Si votre fournisseur de connectivité propose des services Layer 3 gérés, vous pouvez demander votre fournisseur de connectivité à activer peering privé Azure pour vous. Dans ce cas, vous n’aurez pas besoin de suivre les instructions figurant dans les sections suivantes. Toutefois, si votre fournisseur de connectivité ne parvient pas routage pour vous, après avoir créé votre circuit, suivez les instructions ci-dessous.

3. **Vérifier circuit ExpressRoute pour vous assurer qu’il est mis en service**

    Vous devez tout d’abord vérifier si le circuit ExpressRoute est dans l’état Provisioned et activé.

        PS C:\> Get-AzureDedicatedCircuit -ServiceKey "*********************************"

        Bandwidth                        : 200
        CircuitName                      : MyTestCircuit
        Location                         : Silicon Valley
        ServiceKey                       : *********************************
        ServiceProviderName              : equinix
        ServiceProviderProvisioningState : Provisioned
        Sku                              : Standard
        Status                           : Enabled

    Assurez-vous que le circuit affiche comme Provisioned et de l’activation. Si elle n’est pas, collaborer avec votre fournisseur de connectivité pour obtenir votre circuit à l’état requises et l’état.

        ServiceProviderProvisioningState : Provisioned
        Status                           : Enabled


4. **Configurer Microsoft peering pour le circuit**

    Assurez-vous que les informations suivantes avant de poursuivre.

    - Un /30 sous-réseau pour le lien principal. Cela doit être un préfixe de IPv4 public valide dont vous êtes propriétaire et enregistré dans un relative / tri.
    - Un /30 sous-réseau du lien secondaire. Cela doit être un préfixe de IPv4 public valide dont vous êtes propriétaire et enregistré dans un relative / tri.
    - Un ID de réseau local virtuel valide pour établir cette peering sur. S’assurer qu’aucune peering du circuit n’utilise le même ID de réseau local virtuel.
    - En tant que nombre pour peering. Vous pouvez utiliser 2 octets et 4 octets sous forme de nombres.
    - Publiés doivent être respectées : vous devez fournir une liste de tous les préfixes vous envisagez de publier sur la session BGP. Seuls les préfixes d’adresse IP publics sont acceptées. Vous pouvez envoyer une liste séparée par des virgules si vous envisagez d’envoyer un jeu de préfixes. Ces préfixes doivent être enregistrés pour vous dans un relative / tri.
    - Client ASN : Si vous êtes préfixes publicitaires qui ne sont pas enregistrés pour la peering sous forme de nombre, vous pouvez spécifier le nombre en tant qu’auquel ils sont enregistrés. **Cette étape est facultative**.
    - Nom du Registre routage : Vous pouvez spécifier la relative / tri par rapport à laquelle le nombre et les préfixes sont enregistrés.
    - Un hachage MD5, si vous choisissez d’utiliser une. **Cette étape est facultative.**
    
    Vous pouvez exécuter l’applet de commande suivante pour configurer pering Microsoft pour votre circuit

        New-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -VlanId 300 -PeerAsn 1234 -CustomerAsn 2245 -AdvertisedPublicPrefixes "123.0.0.0/30" -RoutingRegistryName "ARIN" -SharedKey "A1B2C3D4"


### <a name="to-view-microsoft-peering-details"></a>Pour afficher les détails homologation Microsoft

Vous pouvez obtenir des détails sur la configuration à l’aide de l’applet de commande suivante.

    Get-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"
    
    AdvertisedPublicPrefixes       : 123.0.0.0/30
    AdvertisedPublicPrefixesState  : Configured
    AzureAsn                       : 12076
    CustomerAutonomousSystemNumber : 2245
    PeerAsn                        : 1234
    PrimaryAzurePort               : 
    PrimaryPeerSubnet              : 10.0.0.0/30
    RoutingRegistryName            : ARIN
    SecondaryAzurePort             : 
    SecondaryPeerSubnet            : 10.0.0.4/30
    State                          : Enabled
    VlanId                         : 300


### <a name="to-update-microsoft-peering-configuration"></a>Mettre à jour Microsoft homologation configuration

Vous pouvez mettre à jour une partie quelconque de la configuration à l’aide de l’applet de commande suivante.

        Set-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -VlanId 300 -PeerAsn 1234 -CustomerAsn 2245 -AdvertisedPublicPrefixes "123.0.0.0/30" -RoutingRegistryName "ARIN" -SharedKey "A1B2C3D4"

### <a name="to-delete-microsoft-peering"></a>Pour supprimer Microsoft peering

Vous pouvez supprimer votre configuration homologation en exécutant l’applet de commande suivante.

    Remove-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"

## <a name="next-steps"></a>Étapes suivantes

Ensuite, [lien un VNet à un circuit ExpressRoute](expressroute-howto-linkvnet-classic.md).


-  Pour plus d’informations sur les flux de travail, voir [flux de travail ExpressRoute](expressroute-workflows.md).
-  Pour plus d’informations sur circuit peering, voir [ExpressRoute circuits et les domaines de routage](expressroute-circuit-peerings.md).

