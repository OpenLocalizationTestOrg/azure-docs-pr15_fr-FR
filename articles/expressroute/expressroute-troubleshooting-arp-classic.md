<properties
   pageTitle="Guide de dépannage ExpressRoute : obtention des tables ARP | Microsoft Azure"
   description="Cette page fournit des instructions pour l’obtention des tables ARP pour un circuit ExpressRoute."
   documentationCenter="na"
   services="expressroute"
   authors="ganesr"
   manager="carolz"
   editor="tysonn"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/10/2016"
   ms.author="ganesr"/>

# <a name="expressroute-troubleshooting-guide-getting-arp-tables-in-the-classic-deployment-model"></a>Guide de dépannage ExpressRoute : l’obtention ARP des tables dans le modèle de déploiement classique

> [AZURE.SELECTOR]
[PowerShell - Gestionnaire de ressources](expressroute-troubleshooting-arp-resource-manager.md)
[PowerShell - classique](expressroute-troubleshooting-arp-classic.md)

Cet article vous explique comment procéder pour obtenir les tables résolution ARP (Address Protocol) pour votre circuit Azure ExpressRoute.

>[AZURE.IMPORTANT] Ce document est destiné à vous aider à diagnostiquer et corriger les problèmes simples. Il n’est pas destiné comme le remplacement du support Microsoft. Si vous ne pouvez pas résoudre le problème en les conseils ci-dessous, ouvrez une demande de support avec [l’aide de Microsoft Azure + prise en charge](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="address-resolution-protocol-arp-and-arp-tables"></a>Tables de résolution Protocol () ARP et d’adresses
ARP est un protocole Layer 2 qui est défini dans la [RFC 826](https://tools.ietf.org/html/rfc826). ARP est utilisé pour mapper une adresse Ethernet (adresse MAC) à une adresse IP.

Une table ARP fournit un mappage des adresses IPv4 et MAC pour un peering particulier. La table ARP d’un circuit ExpressRoute peering fournit les informations suivantes pour chaque interface (principal et secondaire) :

1. Mappage d’une adresse IP locale d’interface routeur à une adresse MAC
2. Mappage d’une adresse IP de ExpressRoute routeur interface à une adresse MAC
3. L’âge du mappage

Tables ARP peuvent vous aider avec validation configuration Layer 2 et sur la résolution des problèmes de connectivité base Layer 2.

Voici un exemple d’une table ARP :

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


La section suivante fournit des informations sur la façon d’afficher les tables ARP qui sont visibles par les routeurs bord ExpressRoute.

## <a name="prerequisites-for-using-arp-tables"></a>Conditions préalables pour l’utilisation de tables ARP

Assurez-vous d’avoir les éléments suivants avant de poursuivre :

 - Circuit ExpressRoute valide qui est configuré avec au moins un peering. Le circuit doit être entièrement configuré par le fournisseur de connectivité. Vous (ou votre fournisseur de connectivité) devez configurer au moins une de la peerings (public privé, Azure Azure ou Microsoft) sur ce circuit.

 - Plages d’adresses IP utilisées pour la configuration de la peerings (public privé, Azure Azure et Microsoft). Passez en revue les exemples de d’affectation d’adresses IP dans la [page Configuration requise pour le routage ExpressRoute](expressroute-routing.md) pour comprendre comment les adresses IP sont mappés aux interfaces votre aise et sur le côté ExpressRoute. Vous pouvez obtenir des informations sur la configuration homologation en passant en revue la [page configuration homologation ExpressRoute](expressroute-howto-routing-classic.md).

 - Informations de votre fournisseur d’équipe ou de connectivité réseau sur les adresses MAC des interfaces qui sont utilisés avec ces adresses IP.

 - Le dernier module Windows PowerShell pour Azure (1,50 ou version ultérieure).

## <a name="arp-tables-for-your-expressroute-circuit"></a>Tables ARP de votre circuit ExpressRoute
Cette section fournit des instructions sur la façon d’afficher les tables ARP pour chaque type de peering à l’aide de PowerShell. Avant de poursuivre, vous ou votre fournisseur de connectivité doit configurer la peering. Chaque circuit comporte deux chemins d’accès (principales et secondaires). Vous pouvez vérifier la table ARP pour chaque chemin d’accès séparément.

### <a name="arp-tables-for-azure-private-peering"></a>Tables ARP de peering privé Azure
L’applet de commande suivante fournit des tables ARP pour peering privé Azure :

        # Required variables
        $ckt = "<your Service Key here>

        # ARP table for Azure private peering--primary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Private -Path Primary

        # ARP table for Azure private peering--secondary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Private -Path Secondary

Voici un exemple de sortie pour l’un des chemins d’accès :

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


### <a name="arp-tables-for-azure-public-peering"></a>Tables ARP de peering public Azure :
L’applet de commande suivante fournit des tables ARP pour Azure peering public :

        # Required variables
        $ckt = "<your Service Key here>

        # ARP table for Azure public peering--primary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Public -Path Primary

        # ARP table for Azure public peering--secondary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Public -Path Secondary

Voici un exemple de sortie pour l’un des chemins d’accès :

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


Voici un exemple de sortie pour l’un des chemins d’accès :

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           64.0.0.1 ffff.eeee.dddd
          0 Microsoft         64.0.0.2 aaaa.bbbb.cccc


### <a name="arp-tables-for-microsoft-peering"></a>Tables ARP de Microsoft peering
L’applet de commande suivante fournit des tables ARP pour Microsoft peering :

    # ARP table for Microsoft peering--primary path
    Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Microsoft -Path Primary

    # ARP table for Microsoft peering--secondary path
    Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Microsoft -Path Secondary


Exemple de résultat est indiqué ci-dessous pour l’un des chemins d’accès :

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1 ffff.eeee.dddd
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc


## <a name="how-to-use-this-information"></a>Comment utiliser ces informations
La table ARP d’un peering peut être utilisée pour valider la connectivité et la configuration de la couche 2. Cette section fournit une vue d’ensemble des tables ARP dans différents scénarios.

### <a name="arp-table-when-a-circuit-is-in-an-operational-expected-state"></a>Table ARP lorsqu’un circuit est dans un état opérationnel (attendu)

 - Le tableau ARP comporte une entrée pour la partie en local avec une adresse IP et MAC valide, une entrée similaire pour le côté de Microsoft.
 - Le dernier octet de l’adresse IP locale est toujours un nombre impair.
 - Le dernier octet de l’adresse IP de Microsoft est toujours un nombre pair.
 - La même adresse MAC s’affiche sur le côté de Microsoft pour tous les trois peerings (principal et secondaire).


        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1 ffff.eeee.dddd
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc

### <a name="arp-table-when-its-on-premises-or-when-the-connectivity-provider-side-has-problems"></a>Table ARP lorsqu’elle est locale ou lorsque le côté de connectivité fournisseur rencontre des problèmes

 Une seule entrée s’affiche dans la table ARP. Il affiche le mappage entre l’adresse MAC et l’adresse IP qui est utilisée sur le côté de Microsoft.

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc

>[AZURE.NOTE] Si vous rencontrez un problème à ceci, ouvrez une demande de support chez votre fournisseur de connectivité pour résoudre ce problème.


### <a name="arp-table-when-the-microsoft-side-has-problems"></a>Table ARP lorsque le côté Microsoft rencontre des problèmes

 - Vous ne verrez pas une table ARP affichée pour un peering si des problèmes affectent sur le côté de Microsoft.
 -  Ouvrez une demande de support avec [l’aide de Microsoft Azure + prise en charge](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Spécifier que vous avez un problème avec la connectivité Layer 2.

## <a name="next-steps"></a>Étapes suivantes

 - Valider les configurations Layer 3 pour votre circuit ExpressRoute :
     - Obtenir un itinéraire synthèse pour déterminer l’état des sessions BGP.
     - Obtenir une table itinéraire afin de déterminer les préfixes sont publiés sur ExpressRoute.
 - Valider le transfert de données en passant en revue et arrière octets.
 - Ouvrez une demande de support avec [l’aide de Microsoft Azure + prise en charge](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) si vous rencontrez toujours des problèmes.
