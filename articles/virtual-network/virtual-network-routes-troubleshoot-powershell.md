<properties 
   pageTitle="Résoudre les problèmes de route - PowerShell | Microsoft Azure"
   description="Découvrez comment résoudre les problèmes des itinéraires dans le modèle de déploiement d’Azure le Gestionnaire de ressources à l’aide de PowerShell Azure."
   services="virtual-network"
   documentationCenter="na"
   authors="AnithaAdusumilli"
   manager="narayan"
   editor=""
   tags="azure-resource-manager"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/23/2016"
   ms.author="anithaa" />

# <a name="troubleshoot-routes-using-azure-powershell"></a>Résoudre les problèmes de gammes à l’aide de PowerShell Azure

> [AZURE.SELECTOR]
- [Portail Azure](virtual-network-routes-troubleshoot-portal.md)
- [PowerShell](virtual-network-routes-troubleshoot-powershell.md)

Si vous rencontrez des problèmes de connectivité réseau vers ou à partir de votre Azure Machine virtuelle (machine virtuelle), itinéraires peuvent être ayant un impact sur votre flux de trafic machine virtuelle. Cet article fournit une vue d’ensemble des fonctionnalités de diagnostic pour les itinéraires afin de résoudre les autres.

Acheminer tables sont associées à des sous-réseaux et sont effectives toutes les interfaces réseau (carte réseau) de ce sous-réseau. Les types d’itinéraires suivants peuvent être appliqués à chaque interface réseau :

- **Itinéraires système :** Par défaut, chaque sous-réseau créé dans un réseau virtuel Azure (VNet) contienne des tables d’itinéraire système qui autorisent le trafic VNet local, le trafic local via passerelles VPN et le trafic Internet. Il existe également itinéraires de système pour les ressources VNets.
- **Itinéraires BGP :** Propagée aux interfaces réseau via ExpressRoute ou connexions VPN site à site. En savoir plus sur le routage BGP, lisez les articles [BGP avec les passerelles VPN](../vpn-gateway/vpn-gateway-bgp-overview.md) et [ExpressRoute vue d’ensemble](../expressroute/expressroute-introduction.md) .
- **Itinéraires définies par l’utilisateur (UDR) :** Si vous utilisez l’authentification multifacteur réseau ou sont forcé-tunnel le trafic vers un réseau local via un réseau VPN site à site, vous devrez peut-être itinéraires définies par l’utilisateur (UDRs) associées à votre table de routage sous-réseau. Si vous ne connaissez pas UDRs, consultez l’article [itinéraires définies par l’utilisateur](virtual-networks-udr-overview.md#user-defined-routes) .

Avec les gammes de diverses qui peuvent être appliqués à une interface réseau, il peut être difficile à déterminer les itinéraires agrégation sont efficaces. Pour aider à résoudre les problèmes de connectivité réseau machine virtuelle, vous pouvez afficher tous les itinéraires efficaces pour une interface réseau dans le modèle de déploiement d’Azure le Gestionnaire de ressources.

## <a name="using-effective-routes-to-troubleshoot-vm-traffic-flow"></a>À l’aide de route efficace pour résoudre les problèmes de flux de trafic machine virtuelle

Cet article utilise le scénario suivant comme exemple pour illustrer comment résoudre les problèmes les itinéraires efficaces d’une interface réseau :

Une machine virtuelle (*VM1*) connecté à la VNet (*VNet1*, le préfixe : 10.9.0.0/16) ne parvient pas à se connecter à un VM(VM3) dans un VNet que vous venez de ressources (*VNet3*, préfixe 10.10.0.0/16). Il existe aucun UDRs ou BGP n’achemine appliqué à VM1 NIC1 réseau interface connecté à la machine virtuelle, seuls les itinéraires système sont appliquées.

Cet article explique comment déterminer l’origine de l’erreur de connexion, à l’aide de la fonctionnalité itinéraires efficaces dans le modèle de déploiement de la gestion des ressources Azure.
Tandis que l’exemple utilise uniquement système routes, les mêmes étapes peuvent servir pour déterminer les échecs de connexion entrant et sortant sur n’importe quel type d’itinéraire.

>[AZURE.NOTE] Si votre ordinateur virtuel comporte plus d’une carte réseau connectée, cochez itinéraires efficaces pour chacune des cartes réseau pour diagnostiquer les problèmes de connectivité réseau vers et depuis un ordinateur virtuel.

### <a name="view-effective-routes-for-a-virtual-machine"></a>Afficher les gammes efficaces pour une machine virtuelle

Pour afficher les itinéraires agrégation qui sont appliqués à une machine virtuelle, procédez comme suit :

### <a name="view-effective-routes-for-a-network-interface"></a>Afficher les gammes efficaces pour une interface réseau

Pour afficher les itinéraires agrégation qui sont appliqués à une interface réseau, procédez comme suit :

1.  Démarrer une session PowerShell Azure et connectez-vous à Azure. Si vous n’êtes pas familiarisé avec Azure PowerShell, consultez l’article [comment installer et configurer Azure PowerShell](../powershell-install-configure.md) .

2.  La commande suivante renvoie tous les itinéraires appliqués à une interface réseau nommée *VM1 NIC1* dans le groupe de ressources *RG1*.

        Get-AzureRmEffectiveRouteTable -NetworkInterfaceName VM1-NIC1 -ResourceGroupName RG1

    >[AZURE.TIP] Si vous ne connaissez pas le nom d’une interface réseau, tapez la commande suivante pour récupérer les noms de toutes les interfaces réseau dans un group.* de ressources

        Get-AzureRmNetworkInterface -ResourceGroupName RG1 | Format-Table Name

    Le résultat suivant est semblable à la sortie pour chaque itinéraire appliqué au sous-réseau à que la carte réseau est connectée :

        Name :
        State : Active
        AddressPrefix : {10.9.0.0/16}
        NextHopType : VNetLocal
        NextHopIpAddress : {}

        Name :
        State : Active
        AddressPrefix : {0.0.0.0/16}
        NextHopType : Internet
        NextHopIpAddress : {}

    Notez l’élément suivant dans le résultat :
    - **Nom**: nom de l’itinéraire efficace peut être vide, à moins d’avoir explicitement spécifié, pour les itinéraires définis par l’utilisateur. 
    - **État**: indique l’état de l’itinéraire efficace. Les valeurs possibles sont « Actif » ou « Non valide »
    - **AddressPrefixes**: Spécifie le préfixe d’adresse de l’itinéraire efficace en notation CIDR. 
    - **nextHopType**: indique le saut suivant pour l’itinéraire donnée. Valeurs possibles sont *VirtualAppliance*, *Internet*, *VNetLocal*, *VNetPeering*ou *Null*. Une valeur *Null* pour **nextHopType** dans un UDR peut indiquer l’itinéraire non valide. Par exemple, si **nextHopType** est *VirtualAppliance* et le réseau virtuel matériel machine virtuelle n’est pas dans un état de sa mise en service/en cours d’exécution. Si **nextHopType** est *VPNGateway* et qu’aucune passerelle sa mise en service/en cours d’exécution dans le VNet donné, l’itinéraire peut devenir non valide.
    - **NextHopIpAddress**: Spécifie l’adresse IP du saut suivant de l’itinéraire efficace.
    
    La commande suivante renvoie les itinéraires de plus facile à afficher la table :

        Get-AzureRmEffectiveRouteTable -NetworkInterfaceName VM1-NIC1 -ResourceGroupName RG1 | Format-Table

    Le résultat suivant est partie de la sortie reçue pour le scénario décrit précédemment :

        Name State AddressPrefix NextHopType NextHopIpAddress
        ---- ----- ------------- ----------- ----------------
        Active {10.9.0.0/16} VnetLocal {}
        Active {0.0.0.0/0} Internet {}
    

3. Il n’existe aucun itinéraire répertorié sur le *WestUS VNet3* VNet (préfixe 10.10.0.0/16)* * à partir de *WestUS VNet1* (préfixe 10.9.0.0/16) dans le résultat de l’étape précédente. Comme le montre l’illustration suivante, le lien d’homologation VNet avec la VNet *WestUS VNet3* est dans un état *déconnecté* .
    
    ![](./media/virtual-network-routes-troubleshoot-portal/image4.png)

    Le lien bidirectionnel pour la peering est rompue, qui explique pourquoi VM1 pas pu se connecter à VM3 dans le VNet *WestUS VNet3* . Configurer un lien d’homologation VNet bidirectionnelle à nouveau pour *WestUS VNet1* et *WestUS VNet3* VNets. Le résultat retourné une fois le lien d’homologation VNet correctement établi suit :

        Name State AddressPrefix NextHopType NextHopIpAddress
        ---- ----- ------------- ----------- ----------------
        Active {10.9.0.0/16} VnetLocal {}
        Active {10.10.0.0/16} VNetPeering {}
        Active {0.0.0.0/0} Internet {}
        
    Une fois que vous identifiez le problème, vous pouvez ajouter, supprimer, ou modifier des routes et acheminer des tables. Tapez la commande suivante pour afficher la liste des commandes utilisées pour le faire :

        Get-Help *-AzureRmRouteConfig

## <a name="considerations"></a>Considérations relatives à la

Quelques éléments à garder à l’esprit lorsque vous examinez la liste des itinéraires renvoyée :

- Routage est basé sur la plus longue préfixe EQUIV (locales) entre UDRs, itinéraires BGP et système. S’il existe plusieurs itinéraires avec la même correspondance locales, un itinéraire est sélectionné en fonction de son origine dans l’ordre suivant :
    - Acheminer définies par l’utilisateur
    - Acheminer BGP
    - Acheminer système (par défaut)

    Itinéraires efficace, vous ne voyez parcours efficaces de correspondance locales basée sur tous les itinéraires disponible. En affichant la façon dont les itinéraires sont en réalité évalués pour une carte réseau donnée, cela rend beaucoup plus facile à résoudre les problèmes des itinéraires spécifiques qui peuvent être ayant un impact sur la connectivité vers ou à partir de votre ordinateur virtuel.

- Si vous avez UDRs et envoyez le trafic vers un matériel virtuel de réseau (à couloirs), avec *VirtualAppliance* comme **nextHopType**, vérifiez que le transfert IP est activé sur l’à couloirs recevoir le trafic ou paquets sont supprimés. 
- Si requis tunnel est activée, tout le trafic Internet sortant doivent être routé vers local. RDP/SSH à partir d’Internet pour votre machine virtuelle peuvent ne pas fonctionner avec ce paramètre, selon la façon dont les locaux gère ce trafic. 
  Forcé-tunnel peut être activé :
    - Si vous utilisez un site à VPN, en définissant un itinéraire définies par l’utilisateur (UDR) avec nextHopType comme passerelle VPN
    - Si un itinéraire par défaut est annoncé sur BGP
- Pour le trafic homologation VNet fonctionne correctement, un itinéraire système avec **nextHopType** *VNetPeering* doit exister de plage de préfixe de VNet ressources. Si cet un itinéraire n’existe pas et le lien d’homologation VNet ressemble OK :
    - Patientez quelques secondes et réessayez s’il s’agit d’un lien d’homologation récemment établi. Il est parfois plus longue propager itinéraires à toutes les interfaces réseau dans un sous-réseau.
    - Règles de groupe de sécurité réseau (NSG) peuvent être ayant un impact sur les flux de trafic. Pour plus d’informations, voir l’article de [Résoudre les problèmes de groupes de sécurité réseau](virtual-network-nsg-troubleshoot-powershell.md) .
