<properties 
   pageTitle="Résoudre les problèmes de route - portail | Microsoft Azure"
   description="Découvrez comment résoudre les problèmes des itinéraires dans le modèle de déploiement d’Azure le Gestionnaire de ressources à l’aide du portail Azure."
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

# <a name="troubleshoot-routes-using-the-azure-portal"></a>Résoudre les problèmes de gammes à l’aide du portail Azure

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

1. Connectez-vous au portail Azure à https://portal.azure.com.
2. Cliquez sur **plusieurs services**, puis cliquez sur **machines virtuelles** dans la liste qui s’affiche.
3. Sélectionnez une machine virtuelle pour résoudre les problèmes dans la liste qui s’affiche et une carte machine virtuelle avec les options s’affiche.
4. Cliquez sur **diagnostiquer & résoudre les problèmes** , puis sélectionnez un problème courant. Dans cet exemple, **je ne parviens pas à se connecter à mon machine virtuelle Windows** est sélectionnée. 

    ![](./media/virtual-network-routes-troubleshoot-portal/image1.png)

5. Étapes apparaissent sous le problème, comme illustré dans l’image suivante : 

    ![](./media/virtual-network-routes-troubleshoot-portal/image2.png)

    Cliquez sur *itinéraires efficaces* dans la liste des étapes à suivre.

6. La carte **itinéraires efficaces** s’affiche, comme le montre l’illustration suivante :

    ![](./media/virtual-network-routes-troubleshoot-portal/image3.png)

    Si votre ordinateur virtuel comporte uniquement une carte réseau, il est sélectionné par défaut. Si vous avez plusieurs cartes réseau, sélectionnez la carte réseau pour laquelle vous souhaitez afficher les itinéraires efficaces.

    >[AZURE.NOTE] Si la machine virtuelle associée à la carte réseau n’est pas en cours d’exécution, itinéraires efficaces ne seront pas affichés. Uniquement les 200 premiers itinéraires efficaces sont affichées dans le portail. Pour la liste complète, cliquez sur **Télécharger**. Vous pouvez filtrer davantage sur les résultats à partir du fichier .csv téléchargé.

    Notez l’élément suivant dans le résultat :
    - **Source**: indique le type d’itinéraire. Itinéraires système apparaissent comme *par défaut*, UDRs sont affichés en tant *qu’utilisateur* et la passerelle itinéraires (statique ou BGP) apparaissent sous la forme *VPNGateway*.
    - **État**: indique l’état de l’itinéraire efficace. Les valeurs possibles sont *actif* ou *non valides*.
    - **AddressPrefixes**: Spécifie le préfixe d’adresse de l’itinéraire efficace en notation CIDR. 
    - **nextHopType**: indique le saut suivant pour l’itinéraire donnée. Valeurs possibles sont *VirtualAppliance*, *Internet*, *VNetLocal*, *VNetPeering*ou *Null*. Une valeur *Null* pour **nextHopType** dans un UDR peut indiquer l’itinéraire non valide. Par exemple, si **nextHopType** est *VirtualAppliance* et le réseau virtuel matériel machine virtuelle n’est pas dans un état de sa mise en service/en cours d’exécution. Si **nextHopType** est *VPNGateway* et qu’aucune passerelle sa mise en service/en cours d’exécution dans le VNet donné, l’itinéraire peut devenir non valide.
    
7. Il n’existe aucun itinéraire répertoriée vers la VNet *WestUS VNET3* (préfixe 10.10.0.0/16) à partir de la *WestUS VNet1* (préfixe 10.9.0.0/16) dans l’image à l’étape précédente. Dans l’illustration suivante, le lien d’homologation est dans un état *déconnecté* :
    
    ![](./media/virtual-network-routes-troubleshoot-portal/image4.png)

    Le lien bidirectionnel pour la peering est rompue, qui explique pourquoi VM1 pas pu se connecter à VM3 dans le VNet *WestUS VNet3* .

8. L’image suivante montre les itinéraires après avoir défini le lien d’homologation bidirectionnelle :

    ![](./media/virtual-network-routes-troubleshoot-portal/image5.png)

Pour plus de scénarios dépannage pour forcé-tunnel et itinéraire d’évaluation, lisez la section [Considérations](virtual-network-routes-troubleshoot-portal.md#Considerations) de cet article.

### <a name="view-effective-routes-for-a-network-interface"></a>Afficher les gammes efficaces pour une interface réseau

Si le flux du trafic réseau affectée pour une interface réseau particulière (carte réseau), vous pouvez afficher une liste complète des itinéraires efficaces sur une carte réseau directement. Pour afficher les itinéraires agrégation qui sont appliqués à une carte réseau, procédez comme suit :

1. Connectez-vous au portail Azure à https://portal.azure.com.
2. Cliquez sur **plusieurs services**, puis cliquez sur **interfaces réseau**
3. Recherche dans la liste pour le nom d’une carte réseau, ou sélectionnez-le dans la liste qui s’affiche. Dans cet exemple, **VM1 NIC1** est sélectionnée.
4. Sélectionnez **itinéraires efficaces** dans la carte **interface réseau** , comme le montre l’illustration suivante :
   
    ![](./media/virtual-network-routes-troubleshoot-portal/image6.png)

    L' **étendue** par défaut à l’interface réseau sélectionnée.

    ![](./media/virtual-network-routes-troubleshoot-portal/image7.png)


### <a name="view-effective-routes-for-a-route-table"></a>Afficher les gammes efficaces pour une table de routage

Lorsque vous modifiez itinéraires définies par l’utilisateur (UDRs) dans une table d’itinéraires, vous voudrez peut-être vérifier l’impact de la route ajouté sur un ordinateur virtuel particulier. Une table d’itinéraires peut être associée à un nombre quelconque de sous-réseaux. Vous pouvez à présent afficher tous les itinéraires efficaces pour toutes les cartes réseau une table d’itinéraires donnée s’applique, sans avoir à basculer le contexte de la carte de table itinéraire donnée.

Dans cet exemple, un UDR (*UDRoute*) est spécifiée dans une table d’itinéraires (*UDRouteTable*). Cet itinéraire envoie tout le trafic Internet à partir de *Subnet1* dans le VNet *WestUS VNet1* , via un réseau virtuel à (couloirs), dans *Subnet2* de la même VNet. L’itinéraire est présentée dans l’illustration suivante :

![](./media/virtual-network-routes-troubleshoot-portal/image8.png)

Pour afficher les itinéraires agrégation pour une table itinéraire, procédez comme suit :

1. Connectez-vous au portail Azure à https://portal.azure.com.
2. Cliquez sur **plusieurs services**, puis cliquez sur **tables de routage**
3. Recherche dans la liste pour la table d’itinéraires que vous voulez voir agrégation itinéraires pour et sélectionnez-le. Dans cet exemple, **UDRouteTable** est sélectionnée. Une carte de la table gamme sélectionnée s’affiche, comme le montre l’illustration suivante :

    ![](./media/virtual-network-routes-troubleshoot-portal/image9.png)

4. Sélectionnez **Itinéraires efficaces** dans la carte de la **table de routage** . L' **étendue** est défini sur la table itinéraire sélectionnée.
5. Une table d’itinéraires peut être appliquée à plusieurs sous réseaux. Sélectionnez le **sous-réseau** que vous souhaitez examiner dans la liste. Dans cet exemple, **Subnet1** est sélectionnée.
6. Sélectionnez une **Interface réseau**. Toutes les cartes réseau connectés au sous-réseau sélectionné sont répertoriés. Dans cet exemple, **VM1 NIC1** est sélectionnée.

    ![](./media/virtual-network-routes-troubleshoot-portal/image10.png)

    >[AZURE.NOTE] Si la carte réseau n’est pas associée à une machine virtuelle en cours d’exécution, aucun itinéraire efficaces n’est affichées.

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
    - Règles de groupe de sécurité réseau (NSG) peuvent être ayant un impact sur les flux de trafic. Pour plus d’informations, voir l’article de [Résoudre les problèmes de groupes de sécurité réseau](virtual-network-nsg-troubleshoot-portal.md) .
