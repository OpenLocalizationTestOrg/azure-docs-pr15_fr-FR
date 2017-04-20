<properties 
   pageTitle="Guide de dépannage ExpressRoute - obtention des tables ARP | Microsoft Azure"
   description="Cette page fournit des instructions sur l’obtention ARP des tables pour un circuit ExpressRoute"
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

#<a name="expressroute-troubleshooting-guide---getting-arp-tables-in-the-resource-manager-deployment-model"></a>Guide de dépannage de ExpressRoute - tables ARP prise dans le modèle de déploiement du Gestionnaire de ressources

> [AZURE.SELECTOR]
[PowerShell - Gestionnaire de ressources](expressroute-troubleshooting-arp-resource-manager.md)
[PowerShell - classique](expressroute-troubleshooting-arp-classic.md)

Cet article vous explique comment procéder pour découvrir les tables ARP pour votre circuit ExpressRoute. 

>[AZURE.IMPORTANT] Ce document est destiné à vous aider à diagnostiquer et corriger les problèmes simples. Il n’est pas destiné comme le remplacement du support Microsoft. Si vous ne parvenez pas à résoudre le problème en suivant les conseils ci-dessous, vous devez ouvrir une tickets de support avec [Microsoft prend en charge](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) .

## <a name="address-resolution-protocol-arp-and-arp-tables"></a>Tables de résolution Protocol () ARP et d’adresses
Résolution ARP (Address Protocol) est un protocole layer 2 défini dans la [RFC 826](https://tools.ietf.org/html/rfc826). ARP est utilisé pour mapper l’adresse Ethernet (adresse MAC) avec une adresse ip.

La table ARP fournit un mappage des adresses ipv4 et MAC pour un peering particulier. La table ARP d’un circuit ExpressRoute peering fournit les informations suivantes pour chaque interface (principal et secondaire)

1. Mappage d’adresse locale routeur interface ip à l’adresse MAC
2. Mappage d’adresse ExpressRoute routeur interface ip à l’adresse MAC
3. Âge du mappage

Tables ARP peuvent vous aider à valider la configuration de la couche 2 et résolution des problèmes de base layer 2 problèmes de connectivité. 

Exemple de tableau ARP : 

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


La section suivante fournit des informations sur la manière d’afficher les tables ARP vus par les routeurs périphériques ExpressRoute. 

## <a name="prerequisites-for-learning-arp-tables"></a>Conditions préalables pour les tables ARP d’apprentissage

Assurez-vous d’avoir les éléments suivants avant de vous avancez davantage

 - Un circuit ExpressRoute valide configuré avec au moins un peering. Le circuit doit être entièrement configuré par le fournisseur de connectivité. Vous (ou votre fournisseur de connectivité) doit avoir configuré au moins de la peerings (public privé, Azure Azure et Microsoft) sur ce circuit.
 - Plages d’adresses IP utilisées pour la configuration de la peerings (public privé, Azure Azure et Microsoft). Passez en revue les exemples de d’affectation d’adresses ip dans la [page Configuration requise pour le routage ExpressRoute](expressroute-routing.md) pour comprendre comment les adresses ip sont mappés aux interfaces sur la côté et sur le côté ExpressRoute. Vous pouvez obtenir des informations sur la configuration homologation en passant en revue la [page configuration homologation ExpressRoute](expressroute-howto-routing-arm.md).
 - Informations de votre équipe de mise en réseau / fournisseur de connectivité sur les adresses MAC des interfaces utilisé avec ces adresses IP.
 - Vous devez le dernier module PowerShell pour Azure (version 1,50 ou version ultérieure).

## <a name="getting-the-arp-tables-for-your-expressroute-circuit"></a>Obtention des tables ARP pour votre circuit ExpressRoute
Cette section fournit des instructions sur la façon dont vous pouvez afficher les tables ARP par peering à l’aide de PowerShell. Vous ou votre fournisseur de connectivité doit avoir configuré la peering avant de poursuivre. Chaque circuit comporte deux chemins d’accès (principales et secondaires). Vous pouvez vérifier la table ARP pour chaque chemin d’accès séparément.

### <a name="arp-tables-for-azure-private-peering"></a>Tables ARP de peering privé Azure
L’applet de commande suivante fournit des tables ARP pour peering privé Azure

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"
        
        # ARP table for Azure private peering - Primary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePrivatePeering -DevicePath Primary
        
        # ARP table for Azure private peering - Secodary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePrivatePeering -DevicePath Secondary 

Exemple de résultat est indiqué ci-dessous pour l’un des chemins d’accès

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


### <a name="arp-tables-for-azure-public-peering"></a>Tables ARP de peering public Azure
L’applet de commande suivante fournit des tables ARP pour peering public Azure

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"
        
        # ARP table for Azure public peering - Primary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePublicPeering -DevicePath Primary
        
        # ARP table for Azure public peering - Secodary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePublicPeering -DevicePath Secondary 


Exemple de résultat est indiqué ci-dessous pour l’un des chemins d’accès

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           64.0.0.1 ffff.eeee.dddd
          0 Microsoft         64.0.0.2 aaaa.bbbb.cccc


### <a name="arp-tables-for-microsoft-peering"></a>Tables ARP de Microsoft peering
L’applet de commande suivante fournit des tables ARP pour Microsoft peering

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"
        
        # ARP table for Microsoft peering - Primary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType MicrosoftPeering -DevicePath Primary
        
        # ARP table for Microsoft peering - Secodary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType MicrosoftPeering -DevicePath Secondary 


Exemple de résultat est indiqué ci-dessous pour l’un des chemins d’accès

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1 ffff.eeee.dddd
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc


## <a name="how-to-use-this-information"></a>Comment utiliser ces informations
La table ARP d’un peering peut être utilisée pour déterminer valider layer 2 configuration et la connectivité. Cette section fournit une vue d’ensemble des tables ARP aperçu sous différents scénarios.

### <a name="arp-table-when-a-circuit-is-in-operational-state-expected-state"></a>Table ARP lorsqu’un circuit est dans un état opérationnel (état attendu)

 - La table ARP aura une entrée pour la partie en local avec une adresse IP valide et l’adresse MAC et une entrée similaire pour le côté de Microsoft. 
 - Le dernier octet de l’adresse ip locale sera toujours un nombre impair.
 - Le dernier octet de l’adresse ip de Microsoft sera toujours un nombre pair.
 - La même adresse MAC s’affichent sur le côté de Microsoft pour tous les 3 peerings (principaux et secondaires). 


        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1 ffff.eeee.dddd
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc

### <a name="arp-table-when-on-premises--connectivity-provider-side-has-problems"></a>Table ARP lorsque locaux / connectivité fournisseur côté présente des problèmes

 - Une seule entrée s’affichent dans la table ARP. Le mappage entre l’adresse MAC et l’adresse IP dans la partie Microsoft s’affiche. 

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc

>[AZURE.NOTE] Ouvrez une demande de support chez votre fournisseur de connectivité à déboguer ces problèmes. 


### <a name="arp-table-when-microsoft-side-has-problems"></a>Table ARP lorsque côté Microsoft rencontre des problèmes

 - Vous ne verrez pas un tableau ARP affiché pour un peering si des problèmes affectent sur le côté de Microsoft. 
 -  Ouvrez une demande d’assistance avec [Microsoft prend en charge](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Spécifier que vous avez un problème avec la connectivité layer 2. 

## <a name="next-steps"></a>Étapes suivantes

 - Valider les configurations Layer 3 pour votre circuit ExpressRoute
     - Obtenir itinéraire synthèse pour déterminer l’état des sessions BGP 
     - Obtenir la table de route pour déterminer les préfixes sont publiés sur ExpressRoute
 - Valider le transfert de données en passant en revue octets en / arrière
 - Ouvrez une demande d’assistance avec [Microsoft prend en charge](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) si vous rencontrez toujours des problèmes.
