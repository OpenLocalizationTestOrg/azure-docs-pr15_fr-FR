<properties 
   pageTitle="Accélérer la mise en réseau pour une machine virtuelle - PowerShell | Microsoft Azure"
   description="Découvrez comment configurer accélérée de mise en réseau pour une machine virtuelle Azure à l’aide de PowerShell."
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
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
   ms.author="jdial" />

# <a name="accelerated-networking-for-a-virtual-machine"></a>Mise en réseau accélérée pour une machine virtuelle

> [AZURE.SELECTOR]
- [Portail Azure](virtual-network-accelerated-networking-portal.md)
- [PowerShell](virtual-network-accelerated-networking-powershell.md)

Réseau accélérée permet de virtualisation d’e/s unique racine (SR IOV) pour une machine virtuelle (), considérablement améliorer ses performances réseau. Ce chemin d’accès High performance ignore l’hôte à partir de la datapath réduisant latence, gigue et l’utilisation de l’UC pour une utilisation avec les charges de travail réseau exigeants sur les types de machine virtuelle pris en charge. Cet article explique comment utiliser PowerShell Azure pour configurer l’accélération réseau dans le modèle de déploiement d’Azure le Gestionnaire de ressources. Vous pouvez également créer une machine virtuelle accélérée réseau à l’aide du portail Azure. Pour savoir comment procéder, cliquez sur la zone portail Azure en haut de cet article.

L’image suivante montre la communication entre deux machines virtuelles (machine virtuelle) avec et sans accélérée de mise en réseau :

![Comparaison](./media/virtual-network-accelerated-networking-powershell/image1.png)

Sans accélérée mise en réseau, tout le trafic réseau et déconnexion de la machine virtuelle doit parcourir l’hôte et le commutateur virtuel. Le commutateur virtuel offre toutes les stratégies, tels que les groupes de sécurité réseau, listes de contrôle d’accès, isolement et d’autres services de réseau virtualisé pour le trafic réseau. Pour plus d’informations, consultez l’article [virtualisation du réseau Hyper-V et le commutateur virtuel](https://technet.microsoft.com/library/jj945275.aspx) .

Avec accélérée de mise en réseau, le trafic réseau arrive à la carte réseau (carte réseau) et est ensuite transmis à la machine virtuelle. Toutes les stratégies de réseau le commutateur virtuel s’applique sans accélérée de mise en réseau sont décharger et appliqués au niveau du matériel. Application d’une stratégie au niveau du matériel permet de la carte réseau pour le trafic réseau transférer directement à la machine virtuelle, ignorer l’hôte et le commutateur virtuel, tout en conservant toutes les stratégies qu'il appliqué dans l’hôte.

Les avantages de l’accélération réseau s’appliquent uniquement à la machine virtuelle qui il est activé sur. Pour de meilleurs résultats, il est idéal activer cette fonctionnalité sur au moins deux machines virtuelles connecté à la même VNet.  Lors de la communication entre VNets ou connexion locale, cette fonctionnalité a un impact minime au latence globale.

[AZURE.INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

##<a name="benefits"></a>Avantages

- **Une latence inférieure / supérieures paquets par seconde (pps) :** Supprimer le commutateur virtuel la datapath supprime le temps de paquets de l’hôte pour le traitement des stratégies et augmente le nombre de paquets qui peut être traité à l’intérieur de la machine virtuelle.
- **Réduites gigue :** Traitement switch virtuel dépend de la quantité de stratégie qui doit être appliqué et la charge de travail de l’UC qui effectue le traitement. Décharger l’application de stratégie pour le matériel supprime cette variabilité en fournissant des paquets directement à la machine virtuelle, suppression de l’hôte de communication machine virtuelle et toutes les interruptions logiciel et les changements de contexte.
- **Réduite de l’UC :** Ignorer le commutateur virtuel dans l’hôte permet d’accéder à moins de l’UC pour traiter le trafic réseau.

## <a name="limitations"></a>Limitations

Les limitations suivantes lors de l’utilisation de cette fonctionnalité :
 
- **Création d’une interface de réseau :** Mise en réseau accélérée ne peut être activée pour une nouvelle interface réseau.  Il ne peut pas être activé sur une interface de réseau existante.
- **Création d’une machine virtuelle :** Une interface de réseau avec un réseau accélérée activé ne peut être attachée à une machine virtuelle lors de la création de la machine virtuelle. L’interface réseau ne peuvent pas être associé à une machine virtuelle existante.
- **Régions :** Proposée dans les régions ouest États-Unis et Ouest Europe Azure. L’ensemble des régions s’élargissent à l’avenir.
- **Système d’exploitation pris en charge :** Microsoft Windows Server 2012 R2 et Windows Server 2016 Technical Preview 5. Prise en charge Linux et Windows Server 2012 est bientôt ajouté.
- **Taille de mémoire virtuelle :** Standard_D15_v2 et Standard_DS15_v2 sont qu'uniquement pris en charge de tailles d’instance machine virtuelle. Pour plus d’informations, voir l’article [tailles machine virtuelle Windows](../virtual-machines/virtual-machines-windows-sizes.md) . Le jeu de tailles d’instance machine virtuelle pris en charge s’élargissent à l’avenir.

Modifications apportées à ces limitations seront annoncées via la page des [mises à jour de réseau virtuel Azure](https://azure.microsoft.com/updates/accelerated-networking-in-preview) .

## <a name="create-a-windows-vm-with-accelerated-networking"></a>Créer une machine virtuelle Windows avec mise en réseau accélérée

1. Ouvrez une invite de commande PowerShell et effectuez les étapes restantes dans cette section en une seule session PowerShell. Si vous n’avez pas installé et configuré de PowerShell, effectuez les étapes décrites dans l’article [comment installer et configurer Azure PowerShell](../powershell-install-configure.md) .
2. Pour vous inscrire pour l’aperçu, envoyer un message électronique aux [Abonnements de mise en réseau accélérée](mailto:axnpreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) avec votre ID d’abonnement et les utilisations. N’effectuez pas les étapes restantes jusqu'à une fois que vous recevez un message vous informant que vous avez été accepté dans l’aperçu.
3. S’inscrire la fonctionnalité avec votre abonnement en entrant les commandes suivantes :

        Register-AzureRmProviderFeature -FeatureName AllowAcceleratedNetworkingFeature -ProviderNamespace Microsoft.Network
        Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network

4. Remplacez *westcentralus* par le nom d’un autre emplacement pris en charge par cette fonctionnalité répertoriée dans la section [Limitations](#limitations) de cet article (le cas échéant). Entrez la commande suivante pour définir une variable à l’emplacement :

        $locName = "westcentralus"

5. Remplacez *RG1* par un nom pour le groupe de ressources qui contiendra la nouvelle interface de réseau et entrez les commandes suivantes pour créer votre document :

        $rgName = "RG1"
        New-AzureRmResourceGroup -Name $rgName -Location $locName

6. Modifier *VM1 NIC1* à ce que vous souhaitez nommer l’interface réseau, puis entrez la commande suivante :

        $NICName = "VM1-NIC1"

7. L’interface réseau doit est relié à un sous-réseau au sein d’un réseau virtuel Azure existant (VNet) dans le même emplacement et [abonnement](../azure-glossary-cloud-terminology.md#subscription) en tant que l’interface réseau. Apprenez-en davantage sur VNets en lisant l’article [vue d’ensemble du réseau virtuelle](virtual-networks-overview.md) si vous n’êtes pas familiarisé avec eux. Pour créer un VNet, suivez les étapes décrites dans l’article [créer une VNet](virtual-networks-create-vnet-arm-ps.md) . Modifier les valeurs « » de la $Variables suivant le nom de la VNet et sous-réseau que vous voulez vous connecter à l’interface du réseau.

        $VNetName   = "VNet1"
        $SubnetName = "Subnet1"

    Si vous ne connaissez pas le nom d’un VNet existant à l’emplacement que vous avez choisi à l’étape 3, entrez les commandes suivantes :
        
        $VirtualNetworks = Get-AzureRmVirtualNetwork
        $VirtualNetworks | Where-Object {$_.Location -eq $locName} | Format-Table Name, Location
        
    Si la liste retournée est vide, vous devez créer un VNet à l’emplacement. Pour créer un VNet, suivez les étapes décrites dans l’article [créer un réseau virtuel](virtual-networks-create-vnet-arm-ps.md) .

    Pour obtenir le nom des sous-réseaux au sein de la VNet, tapez les commandes suivantes et remplacez *Subnet1* ci-dessus par le nom d’un sous-réseau :
        
        $VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $rgName
        $VNet.Subnets | Format-Table Name, AddressPrefix

8. Entrez les commandes suivantes pour extraire les VNet et sous-réseau et les affecter à des variables.

        $VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $rgName
        $Subnet = $VNet.Subnets | Where-Object { $_.Name -eq $SubnetName }

9. Identifiez une existante ressource d’adresse IP publique pouvant être associée à l’interface réseau pour pouvoir vous connecter à celui-ci via Internet. Si vous ne voulez pas accéder à la machine virtuelle avec l’interface réseau via Internet, vous pouvez ignorer cette étape. Sans une adresse IP publique, vous devez vous connecter à la machine virtuelle à partir d’un autre ordinateur virtuel connecté à la même VNet. 

    Remplacez *PIP1* par le nom de ressource adresse IP existant public qui existe dans l’emplacement que vous créez dans l’interface du réseau et qui n’est pas associé à une autre interface réseau. Si nécessaire, modifier le nom du groupe de ressources la ressource d’adresse IP public $rgName existe dans et entrez la commande suivante :

        $PIP1 = Get-AzureRmPublicIPAddress -Name "PIP1" -ResourceGroupName $rgName

    Entrez les commandes suivantes si vous ne connaissez pas le nom d’une ressource d’adresse IP public existante :

        $PublicIPAddresses = Get-AzureRmPublicIPAddress
        $PublicIPAddresses | Where-Object { $_.Location -eq $locName } |Format-Table Name, Location, IPAddress, IpConfiguration

    Si la colonne **IPFichier** a aucune valeur dans le résultat retourné, la ressource d’adresse IP publique n’est pas associée à une interface de réseau existante et peut être utilisée. Si la liste est vide, ou aucune ressource d’adresse IP public disponible, vous pouvez créer un à l’aide de la commande New-AzureRmPublicIPAddress.

    >[AZURE.NOTE] Adresses IP publiques incluent des frais nominal. Découvrez les tarifs en lisant la page [adresse IP des prix](https://azure.microsoft.com/pricing/details/ip-addresses) .
10. Si vous avez choisi ne pas ajouter une ressource d’adresse IP publique à l’interface, supprimez *- PublicIPAddress $PIP1* à la fin de la commande suivante. Créer l’interface réseau avec mise en réseau accélérée en entrant la commande suivante :

        $nic = New-AzureRmNetworkInterface -Location $locName -Name $NICName -ResourceGroupName $rgName -Subnet $Subnet -EnableAcceleratedNetworking -PublicIpAddress $PIP1 

11. Affecter l’interface de réseau pour une machine virtuelle lors de la création de la machine virtuelle en suivant les instructions dans les étapes 3 et 6 de l’article [créer une machine virtuelle](../virtual-machines/virtual-machines-windows-ps-create.md) . Étape 6-2, remplacez *Standard_A1* par un des formats machine virtuelle affichés dans la section [Limitations](#limitations) de cet article.

    >[AZURE.NOTE] Si vous avez modifié le *nom* de la $locName, $rgName ou des variables $nic dans cet article, l’étape 6 de la créer un article de la machine virtuelle échouera. Cependant, vous pouvez modifier les *valeurs* des variables.

12. Une fois que la machine virtuelle est créée, téléchargez le [pilote accélérée de mise en réseau](https://gallery.technet.microsoft.com/Azure-Accelerated-471b5d84), se connecter à la machine virtuelle et exécutez le programme d’installation de pilote à l’intérieur de la machine virtuelle.

13. Cliquez sur le bouton Windows, puis cliquez sur **Gestionnaire de périphériques**. Vérifiez que la **Carte Mellanox ConnectX-3 virtuelle fonction Ethernet** apparaît sous l’option de **réseau** lorsqu’elle est développée, comme le montre l’illustration suivante :

    ![Gestionnaire de périphériques](./media/virtual-network-accelerated-networking-powershell/image2.png)