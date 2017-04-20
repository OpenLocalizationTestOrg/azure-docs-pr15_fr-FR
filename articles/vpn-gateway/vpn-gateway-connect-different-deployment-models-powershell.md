<properties 
   pageTitle="Comment se connecter classiques réseaux virtuels à réseaux virtuels Gestionnaire de ressources à l’aide de PowerShell | Microsoft Azure"
   description="Apprenez à créer une connexion VPN entre classique VNets et VNets Gestionnaire de ressources à l’aide de la passerelle VPN et PowerShell"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management,azure-resource-manager"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/23/2016"
   ms.author="cherylmc" />

# <a name="connect-virtual-networks-from-different-deployment-models-using-powershell"></a>Connecter des réseaux virtuels à partir de modèles de déploiement différentes à l’aide de PowerShell

> [AZURE.SELECTOR]
- [Portail](vpn-gateway-connect-different-deployment-models-portal.md)
- [PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)

Azure a actuellement des modèles de gestion des deux : classique et ressources Manager (RM). Si vous utilisez Azure depuis un certain temps, vous avez probablement Azure machines virtuelles et des rôles d’instance en cours d’exécution dans un VNet classique. Votre plus récentes machines virtuelles et des instances de rôle peuvent être exécuté dans un VNet créé dans le Gestionnaire des ressources. Cet article vous guide tout au long de connexion classique VNets à VNets le Gestionnaire de ressources pour autoriser les ressources situées dans les modèles de déploiement séparé pour communiquer avec eux via une connexion de passerelle.

Vous pouvez créer une connexion entre VNets qui se trouvent dans différents abonnements et dans différentes régions. Vous pouvez également connecter VNets qui ont déjà des connexions à des réseaux en local, dans la mesure où la passerelle qu’ils ont été configurés avec est itinéraire ou dynamique. Pour plus d’informations sur les connexions VNet-VNet, consultez le [Forum aux questions VNet-VNet](#faq) à la fin de cet article.

### <a name="deployment-models-and-methods-for-connecting-vnets-in-different-deployment-models"></a>Modèles de déploiement et les méthodes de connexion VNets dans les modèles de déploiement différentes

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

Nous mettre à jour le tableau suivant en tant que nouveaux articles et outils supplémentaires sont disponibles pour cette configuration. Lorsqu’un article est disponible, nous attacher directement à partir de la table.<br><br>

[AZURE.INCLUDE [vpn-gateway-table-vnet-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)] 

#### <a name="vnet-peering"></a>VNet peering

[AZURE.INCLUDE [vpn-gateway-vnetpeeringlink](../../includes/vpn-gateway-vnetpeeringlink-include.md)]


## <a name="before-beginning"></a>Avant de commencer

Les étapes suivantes vous guident dans les paramètres nécessaires pour configurer une passerelle itinéraire ou dynamique pour chaque VNet et créer une connexion VPN entre les passerelles. Cette configuration ne prend pas en charge les passerelles statiques ou basée sur des règles.

### <a name="prerequisites"></a>Conditions préalables

 - Les deux VNets ont déjà été créées.
 - Les plages d’adresses pour la VNets ne se chevauchent mutuellement, ou se chevauchent avec n’importe lequel des plages pour les autres connexions passerelles peuvent être connectés à.
 - Vous avez installé les dernières applets de commande PowerShell (1.0.2 ou version ultérieure). Pour plus d’informations, voir [comment installer et configurer Azure PowerShell](../powershell-install-configure.md) . Assurez-vous que vous installez la gestion du Service (p) et les applets de commande ressource Manager (RM). 

### <a name="exampleref"></a>Exemples de paramètres

Vous pouvez utiliser les paramètres d’exemple comme une référence.

**Paramètres VNet classiques**

Nom VNet = ClassicVNet <br>
Emplacement = fr ouest <br>
Les espaces d’adressage réseau virtuel = 10.0.0.0/24 <br>
Sous-réseau-1 = 10.0.0.0/27 <br>
GatewaySubnet = 10.0.0.32/29 <br>
Nom du réseau local = RMVNetLocal <br>
GatewayType = DynamicRouting

**Paramètres du Gestionnaire de ressources VNet**

Nom VNet = RMVNet <br>
Groupe de ressources = RG1 <br>
Espaces d’adresse IP de réseau virtuel = 192.168.0.0/16 <br>
Sous-réseau-1 = 192.168.1.0/24 <br>
GatewaySubnet = 192.168.0.0/26 <br>
Emplacement = fr Extrême-Orient <br>
Nom IP public passerelle = gwpip <br>
Réseau local passerelle = ClassicVNetLocal <br>
Nom de la passerelle de réseau virtuel = RMGateway <br>
Configuration d’adressage IP passerelle = gwipconfig


## <a name="createsmgw"></a>Section 1 : configurer le VNet classique

### <a name="part-1---download-your-network-configuration-file"></a>Partie 1 : télécharger votre fichier de configuration du réseau

1. Connectez-vous à votre compte Azure dans la console PowerShell avec des droits élevés. L’applet de commande suivante vous demande les informations d’identification pour votre compte Azure. Après la connexion, il télécharge vos paramètres de compte afin qu’ils soient disponibles à Azure PowerShell. Vous utiliserez les applets de commande PowerShell OILE dans cette partie de la configuration.

        Add-AzureAccount

2. Exporter votre fichier de configuration réseau Azure en exécutant la commande suivante. Vous pouvez modifier l’emplacement du fichier à exporter vers un autre emplacement, le cas échéant. Vous modifiez le fichier et importez-le sur Azure.

        Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml

3. Ouvrez le fichier .xml que vous avez téléchargé pour le modifier. Pour obtenir un exemple du fichier de configuration réseau, voir le [Schéma de Configuration du réseau](https://msdn.microsoft.com/library/jj157100.aspx).
 

### <a name="part-2--verify-the-gateway-subnet"></a>Partie 2 - Vérifiez le sous-réseau passerelle

Dans l’élément **VirtualNetworkSites** , ajoutez un sous-réseau passerelle à votre VNet si aucune n’a pas déjà été créée. Lorsque vous travaillez avec le fichier de configuration réseau, le sous-réseau passerelle doit être nommé « GatewaySubnet » ou Azure ne peut pas reconnaître et utiliser un sous-réseau passerelle.

[AZURE.INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

**Exemple :**
    
    <VirtualNetworkSites>
      <VirtualNetworkSite name="ClassicVNet" Location="West US">
        <AddressSpace>
          <AddressPrefix>10.0.0.0/24</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Subnet-1">
            <AddressPrefix>10.0.0.0/27</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.0.0.32/29</AddressPrefix>
          </Subnet>
        </Subnets>
      </VirtualNetworkSite>
    </VirtualNetworkSites>
       
### <a name="part-3---add-the-local-network-site"></a>Partie 3 - ajouter le site de réseau local

Le site de réseau local que vous ajoutez représente le VNet RM auquel vous voulez vous connecter. Vous devrez peut-être ajouter un élément **LocalNetworkSites** vers le fichier s’il n’existe pas déjà. À ce stade dans la configuration, le VPNGatewayAddress peut être n’importe quelle adresse IP public valide, car nous n’avons pas encore créé la passerelle pour le Gestionnaire de ressources VNet. Après avoir créé la passerelle, nous remplacez cette adresse IP espace réservé par l’adresse IP publique correcte qui vous a été affectée à la passerelle RM.

    <LocalNetworkSites>
      <LocalNetworkSite name="RMVNetLocal">
        <AddressSpace>
          <AddressPrefix>192.168.0.0/16</AddressPrefix>
        </AddressSpace>
        <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>
      </LocalNetworkSite>
    </LocalNetworkSites>

### <a name="part-4---associate-the-vnet-with-the-local-network-site"></a>4e partie : associer la VNet avec le site de réseau local

Dans cette section, nous spécifiez le site de réseau local auquel vous souhaitez vous connecter la VNet à. Dans ce cas, il est VNet le Gestionnaire de ressources que vous avez référencé plus haut. Assurez-vous que les noms correspondent. Cette étape ne crée pas une passerelle. Il indique le réseau local la passerelle doit se connecter.

        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="RMVNetLocal">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>

### <a name="part-5---save-the-file-and-upload"></a>Partie 5 - enregistrer le fichier et télécharger

Enregistrer le fichier, puis l’importer dans Azure en exécutant la commande suivante. Vérifiez que vous modifiez le chemin d’accès de fichier en tant que nécessaire pour votre environnement.

        Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml

Vous devriez voir doit ressembler à ce résultat en indiquant que l’importation a réussi.

        OperationDescription        OperationId                      OperationStatus                                                
        --------------------        -----------                      ---------------                                                
        Set-AzureVNetConfig        e0ee6e66-9167-cfa7-a746-7casb9    Succeeded 

### <a name="part-6---create-the-gateway"></a>Partie 6 - créer la passerelle 

Vous pouvez créer la passerelle VNet à l’aide du portail classique ou à l’aide de PowerShell.

L’exemple suivant permet de créer une passerelle routage dynamique :

    New-AzureVNetGateway -VNetName ClassicVNet -GatewayType DynamicRouting

Vous pouvez vérifier l’état de la passerelle à l’aide de la `Get-AzureVNetGateway` applet de commande.


## <a name="creatermgw"></a>Section 2 : Configurer la passerelle RM VNet

Pour créer une passerelle VPN pour la VNet RM, suivez les instructions ci-après. Ne pas démarrer la procédure jusqu'à une fois que vous avez extrait l’adresse IP pour la passerelle de VNet classique. 

1. **Se connecter à votre compte Azure** dans la console PowerShell. L’applet de commande suivante vous demande les informations d’identification pour votre compte Azure. Après la connexion, les paramètres de votre compte sont téléchargés afin qu’ils soient disponibles à Azure PowerShell.

        Login-AzureRmAccount 

    Obtenir une liste de vos abonnements Azure si vous avez plusieurs abonnements.

        Get-AzureRmSubscription

    Spécifier l’abonnement que vous voulez utiliser. 

        Select-AzureRmSubscription -SubscriptionName "Name of subscription"


2.  **Créer une passerelle réseau local**. Dans un réseau virtuel, la passerelle réseau local signifie généralement votre emplacement local. Dans ce cas, la passerelle réseau local fait référence à votre VNet classique. Donnez-lui un nom par lequel Azure peut faire et également spécifier le préfixe d’espace adresse. Azure utilise le préfixe d’adresse IP que vous spécifiez pour identifier le trafic pour envoyer à votre emplacement local. Si vous avez besoin ajuster les informations ici ultérieurement, avant de créer votre passerelle, vous pouvez modifier les valeurs et exécutez à nouveau l’échantillon.<br><br>
    - `-Name`est le nom que vous voulez affecter pour faire référence à la passerelle réseau local.<br>
    - `-AddressPrefix`est l’espace d’adressage pour votre VNet classique.<br>
    - `-GatewayIpAddress`est l’adresse IP de la passerelle de VNet classique. Veillez à modifier l’exemple suivant pour refléter l’adresse IP correcte.
    
            New-AzureRmLocalNetworkGateway -Name ClassicVNetLocal `
            -Location "West US" -AddressPrefix "10.0.0.0/24" `
            -GatewayIpAddress "n.n.n.n" -ResourceGroupName RG1

3. **Demander une adresse IP publique** à allouer à la passerelle réseau virtuel pour le Gestionnaire de ressources VNet. Vous ne pouvez pas spécifier l’adresse IP que vous souhaitez utiliser. L’adresse IP est affectée dynamiquement à la passerelle réseau virtuel. Toutefois, cela signifie pas que l’adresse IP changera. Uniquement les modifications d’adresses IP réseau virtuel passerelle est lorsque la passerelle est supprimée et recréée. Il ne change pas dans l’ensemble de redimensionnement, la réinitialisation ou autres maintenance/mises à niveau internes de la passerelle.<br>Dans cette étape, nous avons également définir une variable qui est utilisée dans une étape ultérieure.


        $ipaddress = New-AzureRmPublicIpAddress -Name gwpip `
        -ResourceGroupName RG1 -Location 'EastUS' `
        -AllocationMethod Dynamic

4. **Vérifiez que votre réseau virtuel dispose d’un sous-réseau passerelle**. Si aucun sous-réseau passerelle n’existe, ajoutez-en une. Vérifiez que le sous-réseau passerelle est nommé *GatewaySubnet*.

5. **Récupérer le sous-réseau** utilisé pour la passerelle en exécutant la commande suivante. Dans cette étape, nous avons également définir une variable à utiliser dans l’étape suivante.
    - `-Name`est le nom de votre gestionnaire de ressources VNet.
    - `-ResourceGroupName`est le groupe de ressources du VNet est associé. Le sous-réseau passerelle doit déjà exister pour ce VNet et doit être nommé *GatewaySubnet* fonctionne correctement.


            $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet `
            -VirtualNetwork (Get-AzureRmVirtualNetwork -Name RMVNet -ResourceGroupName RG1) 

6. **Créer la configuration d’adressage IP passerelle**. La configuration de passerelle définit le sous-réseau et l’adresse IP à utiliser. L’exemple suivant permet de créer votre configuration de la passerelle.<br>Dans cette étape, la `-SubnetId` et `-PublicIpAddressId` paramètres doivent être passés la propriété id à partir du sous-réseau et objets d’adresse IP, respectivement. Vous ne pouvez pas utiliser une chaîne simple. Ces variables sont définis dans l’étape pour demander une adresse IP publique et l’étape pour récupérer le sous-réseau.

        $gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig `
        -Name gwipconfig -SubnetId $subnet.id `
        -PublicIpAddressId $ipaddress.id
    
7. **Créer la passerelle réseau virtuel Gestionnaire de ressources** en exécutant la commande suivante. La `-VpnType` doit être *RouteBased*. 45 minutes ou plus pour cette opération peut prendre.

        New-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1 `
        -Location "EastUS" -GatewaySKU Standard -GatewayType Vpn `
        -IpConfigurations $gwipconfig `
        -EnableBgp $false -VpnType RouteBased

8. **Copiez l’adresse IP public** une fois la passerelle VPN a été créée. Utilisez-la lorsque vous configurez les paramètres du réseau local pour votre VNet classique. Vous pouvez utiliser l’applet de commande suivante pour récupérer l’adresse IP. L’adresse IP est répertorié dans le retour sous *adresse IP*.

        Get-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName RG1

## <a name="section-3-modify-the-local-network-for-the-classic-vnet"></a>Section 3 : Modifier le réseau local pour la VNet classique

Vous pouvez effectuer cette étape en exportant le fichier de configuration du réseau, modifier, puis l’enregistrement et l’importation du fichier en retour vers Azure. Ou, vous pouvez modifier ce paramètre dans le portail classique. 

### <a name="to-modify-in-the-portal"></a>Pour modifier le portail

1. Ouvrez le [portail classique](https://manage.windowsazure.com).

2. Dans le portail classique, faites défiler vers le bas sur le côté gauche, puis cliquez sur **réseaux**. Dans la page **réseaux** , cliquez sur les **Réseaux locaux** en haut de la page. 

3. Dans la page **Réseaux locaux** , cliquez sur pour sélectionner le réseau local que vous configuré dans la partie 1, puis accédez à la partie inférieure de la page et cliquez sur **Modifier**.

4. Dans la page **spécifier les détails de votre réseau local** , remplacez l’adresse IP espace réservé par l’adresse IP de la passerelle Gestionnaire de ressources que vous avez créé dans la section précédente. Cliquez sur la flèche pour accéder à la section suivante. Vérifiez que l' **Espace d’adressage** est correct, puis cliquez sur la case à cocher pour accepter les modifications.

### <a name="to-modify-using-the-network-configuration-file"></a>Pour modifier à l’aide du fichier de configuration du réseau

1. Exporter le fichier de configuration réseau.
2. Dans un éditeur de texte, modifiez l’adresse IP de passerelle VPN.

        <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>

3. Enregistrez vos modifications et importer le fichier modifié dans Azure.


## <a name="connect"></a>Section 4 : Créer une connexion entre les passerelles

Création d’une connexion entre les passerelles nécessite PowerShell. Vous devrez peut-être ajouter votre compte Azure pour utiliser les applets de commande PowerShell classique. Pour ce faire, vous pouvez utiliser l’applet de commande suivante : 
        
    Add-AzureAccount

1. **Définir votre clé partagé** en exécutant la commande suivante. Dans cet exemple, `-VNetName` est le nom de la VNet classique et `-LocalNetworkSiteName` est le nom que vous avez spécifié pour le réseau local lorsque vous l’avez configurée dans le portail classique. La `-SharedKey` est une valeur que vous pouvez générer et spécifier. La valeur que vous spécifiez ici doit être la même valeur que vous spécifiez dans l’étape suivante lorsque vous créez votre connexion. Au retour de cet exemple doit figurer **état : réussie**.

        Set-AzureVNetGatewayKey -VNetName ClassicVNet `
        -LocalNetworkSiteName RMVNetLocal -SharedKey abc123

2. Créer la connexion VPN en exécutant les commandes suivantes.

    **Définir les variables**

        $vnet01gateway = Get-AzureRMLocalNetworkGateway -Name ClassicVNetLocal -ResourceGroupName RG1
        $vnet02gateway = Get-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1

    **Créer la connexion**<br> Notez que la `-ConnectionType` IPSec, pas Vnet2Vnet.
        
        New-AzureRmVirtualNetworkGatewayConnection -Name RM-Classic -ResourceGroupName RG1 `
        -Location "East US" -VirtualNetworkGateway1 `
        $vnet02gateway -LocalNetworkGateway2 `
        $vnet01gateway -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

3. Vous pouvez afficher votre connexion soit portail, ou à l’aide de la `Get-AzureRmVirtualNetworkGatewayConnection` applet de commande.

        Get-AzureRmVirtualNetworkGatewayConnection -Name RM-Classic -ResourceGroupName RG1

## <a name="faq"></a>Forum aux questions VNet-VNet

Afficher les détails du Forum aux questions pour plus d’informations sur les connexions VNet-VNet.

[AZURE.INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-vnet-vnet-faq-include.md)] 


