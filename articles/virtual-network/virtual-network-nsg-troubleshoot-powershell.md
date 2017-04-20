<properties 
   pageTitle="Résoudre les problèmes de groupes de sécurité réseau - PowerShell | Microsoft Azure"
   description="Découvrez comment résoudre les problèmes réseau les groupes de sécurité dans le modèle de déploiement d’Azure le Gestionnaire de ressources à l’aide de PowerShell Azure."
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

# <a name="troubleshoot-network-security-groups-using-azure-powershell"></a>Résoudre les problèmes de groupes de sécurité réseau à l’aide de PowerShell Azure

> [AZURE.SELECTOR]
- [Portail Azure](virtual-network-nsg-troubleshoot-portal.md)
- [PowerShell](virtual-network-nsg-troubleshoot-powershell.md)

Si vous configuré les groupes de sécurité réseau (NSGs) sur votre machine virtuelle () et que vous rencontrez des problèmes de connectivité machine virtuelle, cet article fournit une vue d’ensemble des fonctionnalités de diagnostic pour NSGs afin de résoudre les autres.

NSGs vous permettent de contrôler les types de trafic activer ce flux entrant et sortant vos machines virtuelles (machines virtuelles). NSGs peuvent être appliqués aux sous-réseaux dans un réseau virtuel Azure (VNet), les interfaces réseau (carte réseau) ou les deux. Les règles efficaces appliqués à une carte réseau sont une agrégation des règles qui existent dans les NSGs appliqués à une carte réseau et le sous-réseau à qu'il est connecté. Les règles sur ces NSGs peuvent parfois entrent en conflit et avoir un impact sur la connectivité réseau d’un ordinateur virtuel.  

Vous pouvez afficher toutes les règles de sécurité efficaces à partir de votre NSGs, comme appliqué sur cartes réseau de votre ordinateur virtuel. Cet article vous explique comment résoudre les problèmes de connectivité machine virtuelle à l’aide de ces règles dans le modèle de déploiement d’Azure le Gestionnaire de ressources. Si vous n’êtes pas familiarisé avec les concepts VNet et NSG, lisez les articles de vue d’ensemble du [réseau virtuelles](virtual-networks-overview.md) et des [groupes de sécurité réseau](virtual-networks-nsg.md) .

## <a name="using-effective-security-rules-to-troubleshoot-vm-traffic-flow"></a>À l’aide de règles de sécurité efficaces pour résoudre les problèmes de flux de trafic machine virtuelle

Le scénario qui suit est un exemple d’un problème de connexion courants :

Un ordinateur virtuel nommé *VM1* fait partie d’un sous-réseau nommé *Subnet1* au sein d’un VNet nommé *WestUS VNet1*. Une tentative de se connecter à la machine virtuelle à l’aide de RDP sur le port TCP 3389 échoue. NSGs sont appliquées à la carte réseau *VM1 NIC1* et le sous-réseau *Subnet1*. Le trafic vers le port TCP 3389 est autorisé dans le NSG associé à l’interface réseau *VM1 NIC1*, mais TCP ping vers échoue de Port3389 de VM1.

Bien que cet exemple utilise le port TCP 3389, les étapes suivantes peuvent servir à déterminer les échecs de connexion entrant et sortant sur n’importe quel port.

## <a name="detailed-troubleshooting-steps"></a>Étapes détaillées de dépannage
Effectuez les étapes suivantes pour résoudre les problèmes de NSGs pour une machine virtuelle :

1. Démarrer une session PowerShell Azure et connectez-vous à Azure. Si vous n’êtes pas familiarisé avec l’utilisation de PowerShell Azure, consultez l’article [comment installer et configurer Azure PowerShell](../powershell-install-configure.md) .

2. Entrez la commande suivante pour renvoyer toutes les règles NSG appliquées à une carte réseau nommée *VM1 NIC1* dans le groupe de ressources *RG1*:

        Get-AzureRmEffectiveNetworkSecurityGroup -NetworkInterfaceName VM1-NIC1 -ResourceGroupName RG1

    >[AZURE.TIP] Si vous ne connaissez pas le nom d’une carte réseau, entrez la commande suivante pour récupérer les noms de toutes les cartes réseau dans un groupe de ressources : 
    
    >`Get-AzureRmNetworkInterface -ResourceGroupName RG1 | Format-Table Name`

    Le texte suivant est un exemple du résultat règles efficaces retourné pour la *VM1 NIC1* carte réseau :

        NetworkSecurityGroup   : {
                                   "Id": "/subscriptions/[Subscription ID]/resourceGroups/RG1/providers/Microsoft.Network/networkSecurityGroups/VM1-NIC1-NSG"
                                 }
        Association            : {
                                   "NetworkInterface": {
                                     "Id": "/subscriptions/[Subscription ID]/resourceGroups/RG1/providers/Microsoft.Network/networkInterfaces/VM1-NIC1"
                                   }
                                 }
        EffectiveSecurityRules : [
                                 {
                                 "Name": "securityRules/allowRDP",
                                 "Protocol": "Tcp",
                                 "SourcePortRange": "0-65535",
                                 "DestinationPortRange": "3389-3389",
                                 "SourceAddressPrefix": "Internet",
                                 "DestinationAddressPrefix": "0.0.0.0/0",
                                 "ExpandedSourceAddressPrefix": [… ],
                                 "ExpandedDestinationAddressPrefix": [],
                                 "Access": "Allow",
                                 "Priority": 1000,
                                 "Direction": "Inbound"
                                 },
                                 {
                                 "Name": "defaultSecurityRules/AllowVnetInBound",
                                 "Protocol": "All",
                                 "SourcePortRange": "0-65535",
                                 "DestinationPortRange": "0-65535",
                                 "SourceAddressPrefix": "VirtualNetwork",
                                 "DestinationAddressPrefix": "VirtualNetwork",
                                 "ExpandedSourceAddressPrefix": [
                                  "10.9.0.0/16",
                                  "168.63.129.16/32",
                                  "10.0.0.0/16",
                                  "10.1.0.0/16"
                                  ],
                                 "ExpandedDestinationAddressPrefix": [
                                  "10.9.0.0/16",
                                  "168.63.129.16/32",
                                  "10.0.0.0/16",
                                  "10.1.0.0/16"
                                  ],
                                  "Access": "Allow",
                                  "Priority": 65000,
                                  "Direction": "Inbound"
                                  },…
                         ]
        
        NetworkSecurityGroup   : {
                                   "Id": 
                                 "/subscriptions/[Subscription ID]/resourceGroups/RG1/providers/Microsoft.Network/networkSecurityGroups/Subnet1-NSG"
                                 }
        Association            : {
                                   "Subnet": {
                                     "Id": 
                                 "/subscriptions/[Subscription ID]/resourceGroups/RG1/providers/Microsoft.Network/virtualNetworks/WestUS-VNet1/subnets/Subnet1"
                                 }
                                 }
        EffectiveSecurityRules : [
                                 {
                                "Name": "securityRules/denyRDP",
                                "Protocol": "Tcp",
                                "SourcePortRange": "0-65535",
                                "DestinationPortRange": "3389-3389",
                                "SourceAddressPrefix": "Internet",
                                "DestinationAddressPrefix": "0.0.0.0/0",
                                "ExpandedSourceAddressPrefix": [
                                   ... ],
                                "ExpandedDestinationAddressPrefix": [],
                                "Access": "Deny",
                                "Priority": 1000,
                                "Direction": "Inbound"
                                },
                                {
                                "Name": "defaultSecurityRules/AllowVnetInBound",
                                "Protocol": "All",
                                "SourcePortRange": "0-65535",
                                "DestinationPortRange": "0-65535",
                                "SourceAddressPrefix": "VirtualNetwork",
                                "DestinationAddressPrefix": "VirtualNetwork",
                                "ExpandedSourceAddressPrefix": [
                                "10.9.0.0/16",
                                "168.63.129.16/32",
                                "10.0.0.0/16",
                                "10.1.0.0/16"
                                ],
                                "ExpandedDestinationAddressPrefix": [
                                "10.9.0.0/16",
                                "168.63.129.16/32",
                                "10.0.0.0/16",
                                "10.1.0.0/16"
                                ],
                                "Access": "Allow",
                                "Priority": 65000,
                                "Direction": "Inbound"
                                },...
                                ]

    Notez les informations suivantes dans le résultat :

    - Il existe deux sections **NetworkSecurityGroup** : aucun est associé à un sous-réseau (*Subnet1*) et une est associée à une carte réseau (*VM1 NIC1*). Dans cet exemple, un NSG a été appliqué à chacun.
    - **Association** affiche la ressource (sous-réseau ou carte réseau) est associé à un NSG donnée. Si la ressource NSG est déplacé/dissocié immédiatement avant d’exécuter cette commande, vous devrez peut-être patienter quelques secondes pour que la modification en fonction de la sortie de commande. 
    - Les noms des règles préface avec *defaultSecurityRules*: lorsque NSG un est créé, plusieurs règles de sécurité par défaut sont créées qu’il contient. Règles par défaut ne peut pas être supprimés, mais elles peuvent être remplacées avec les règles de priorité plus élevés.
     Consultez l’article [présentation NSG](virtual-networks-nsg.md#default-rules) pour en savoir plus sur les règles de sécurité par défaut NSG.
    - **ExpandedAddressPrefix** développe les préfixes d’adresse des balises par défaut NSG. Balises représentent plusieurs préfixes d’adresse. Extension des balises peut être utile lors de la résolution des problèmes de connectivité machine virtuelle vers ou à partir de préfixes d’adresses spécifique. Par exemple, avec VNET peering, balise VIRTUAL_NETWORK se développe pour afficher les préfixes VNet ressources dans la sortie précédente.

        >[AZURE.NOTE] Les commande uniquement affiche efficaces des règles si un NSG est associé à un sous-réseau, une carte réseau ou les deux. Une machine virtuelle peut avoir plusieurs cartes réseau avec différents NSGs appliqués. Lors de la résolution du problème, exécutez la commande pour chaque carte réseau.
        
3. Pour faciliter le filtrage le plus grand nombre de règles NSG, entrez les commandes suivantes pour résoudre les problèmes supplémentaires : 

        $NSGs = Get-AzureRmEffectiveNetworkSecurityGroup -NetworkInterfaceName VM1-NIC1 -ResourceGroupName RG1
        $NSGs.EffectiveSecurityRules | Sort-Object Direction, Access, Priority | Out-GridView

    Application d’un filtre pour le trafic RDP (TCP Port3389), à l’affichage de la grille, comme le montre l’illustration suivante :

    ![Liste des règles](./media/virtual-network-nsg-troubleshoot-powershell/rules.png)
    
4. Comme vous pouvez le voir dans l’affichage de la grille, il existe deux autoriser et refuser des règles pour RDP. Le résultat de l’étape 2 indique que la règle *DenyRDP* se trouve dans la NSG appliqué au sous-réseau. Pour les règles de trafic entrant, NSGs appliqués au sous-réseau sont traitées en premier. Si une correspondance, le NSG appliqué à l’interface réseau n’est pas traité. Dans ce cas, la règle *DenyRDP* à partir du sous-réseau bloque RDP de la machine virtuelle (**VM1**).

    >[AZURE.NOTE] Une machine virtuelle peut avoir plusieurs cartes réseau est jointe à. Chacun peut être connecté à en choisir un. Dans la mesure où les commandes dans les étapes précédentes exécutées sur une carte réseau, il est important de s’assurer que vous spécifiez la carte réseau que vous rencontrez l’échec de connectivité. Si vous n’êtes pas sûr, vous pouvez toujours exécuter les commandes sur chaque carte réseau connectée à la machine virtuelle.

5. Pour RDP dans VM1, modifiez la règle *Refuser RDP (3389)* *RDP(3389) autoriser* dans la **Subnet1 NSG** NSG. Vérifiez que le port TCP 3389 est ouvert en ouvrant une connexion RDP à la machine virtuelle ou à l’aide de l’outil PsPing. Vous pouvez en savoir plus sur PsPing en lisant la [page de téléchargement PsPing](https://technet.microsoft.com/sysinternals/psping.aspx)

    Vous pouvez ou supprimer des règles à partir d’un NSG en utilisant les informations dans le résultat de la commande suivante :

        Get-Help *-AzureRmNetworkSecurityRuleConfig
        

## <a name="considerations"></a>Considérations relatives à la

Lors de la résolution des problèmes de connectivité, tenez compte des points suivants :

- Règles NSG par défaut seront bloquer l’accès entrant à partir d’internet et autoriser uniquement VNet le trafic entrant. Règles doivent être ajoutés explicitement pour autoriser l’accès entrant à partir d’Internet, selon les besoins.
- S’il n’y a aucune règle de sécurité NSG à l’origine de la connectivité réseau d’un ordinateur virtuel échec, le problème peut être dû à :
    - Logiciel pare-feu qui s’exécutent dans le système d’exploitation de la machine virtuelle
    - Itinéraires configurés pour l’authentification multifacteur ou le trafic local. Le trafic Internet peut être redirigé vers local via forcé-tunnel. Une connexion RDP/SSH à partir d’Internet vers votre ordinateur virtuel peut ne pas fonctionne avec ce paramètre, selon la façon dont le matériel réseau local gère ce trafic. Consultez l’article [Itinéraires de résolution des problèmes](virtual-network-routes-troubleshoot-powershell.md) pour apprendre à diagnostiquer les problèmes d’itinéraire qui peuvent être empêchant le flux du trafic entrant et sortant de la machine virtuelle. 
- Si vous avez ressources VNets, par défaut, la balise VIRTUAL_NETWORK se développe automatiquement pour inclure les préfixes de ressources VNets. Vous pouvez afficher ces préfixes dans la liste **ExpandedAddressPrefix** , pour résoudre les problèmes liés à la connectivité homologation VNet. 
- Règles de sécurité efficace sont affichent uniquement s’il existe une NSG associé de la machine virtuelle carte réseau et ou sous-réseau. 
- Si il n’y a aucune NSGs associées à la carte réseau ou sous-réseau et que vous avez une adresse IP publique affectée à votre ordinateur virtuel, tous les ports seront ouverts pour un accès entrant et sortant. Si la machine virtuelle comporte une adresse IP publique, appliquer NSGs à la carte réseau ou sous-réseau est fortement recommandé.  
