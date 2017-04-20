<properties 
   pageTitle="Comment se connecter classiques réseaux virtuels à Gestionnaire de ressources de réseaux virtuels dans le portail | Microsoft Azure"
   description="Apprenez à créer une connexion VPN entre classique VNets et VNets Gestionnaire de ressources à l’aide de la passerelle VPN et le portail"
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
   ms.date="10/03/2016"
   ms.author="cherylmc" />

# <a name="connect-virtual-networks-from-different-deployment-models-in-the-portal"></a>Connecter des réseaux virtuels à partir de modèles de déploiement différentes dans le portail

> [AZURE.SELECTOR]
- [Portail](vpn-gateway-connect-different-deployment-models-portal.md)
- [PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)


Azure a actuellement des modèles de gestion des deux : classique et ressources Manager (RM). Si vous utilisez Azure depuis un certain temps, vous avez probablement Azure machines virtuelles et des rôles d’instance en cours d’exécution dans un VNet classique. Votre plus récentes machines virtuelles et des instances de rôle peuvent être exécuté dans un VNet créé dans le Gestionnaire des ressources. Cet article vous guide tout au long de connexion classique VNets à VNets le Gestionnaire de ressources pour autoriser les ressources situées dans les modèles de déploiement séparé pour communiquer avec eux via une connexion de passerelle. 

Vous pouvez créer une connexion entre VNets qui se trouvent dans différents abonnements et dans différentes régions. Vous pouvez également connecter VNets qui ont déjà des connexions à des réseaux en local, dans la mesure où la passerelle qu’ils ont été configurés avec est itinéraire ou dynamique. Pour plus d’informations sur les connexions VNet-VNet, consultez le [Forum aux questions VNet-VNet](#faq) à la fin de cet article.

### <a name="deployment-models-and-methods-for-vnet-to-vnet-connections"></a>Modèles de déploiement et méthodes pour les connexions VNet-VNet

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

Nous mettre à jour le tableau suivant en tant que nouveaux articles et outils supplémentaires sont disponibles pour cette configuration. Lorsqu’un article est disponible, nous attacher directement à partir de la table.<br><br>

**VNet-VNet**

[AZURE.INCLUDE [vpn-gateway-table-vnet-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)] 

#### <a name="vnet-peering"></a>VNet peering

[AZURE.INCLUDE [vpn-gateway-vnetpeeringlink](../../includes/vpn-gateway-vnetpeeringlink-include.md)]

## <a name="before-beginning"></a>Avant de commencer

Les étapes suivantes vous guident dans les paramètres nécessaires pour configurer une passerelle itinéraire ou dynamique pour chaque VNet et créer une connexion VPN entre les passerelles. Cette configuration ne prend pas en charge les passerelles statiques ou basée sur des règles. 

Dans cet article, nous utilisons le portail classique, le portail Azure et PowerShell. Pour l’instant, il n’est pas possible de créer cette configuration uniquement le portail Azure.

### <a name="prerequisites"></a>Conditions préalables

 - Les deux VNets ont déjà été créées.
 - Les plages d’adresses pour la VNets ne se chevauchent mutuellement, ou se chevauchent avec n’importe lequel des plages pour les autres connexions passerelles peuvent être connectés à.
 - Vous avez installé les dernières applets de commande PowerShell (1.0.2 ou version ultérieure). Pour plus d’informations, voir [comment installer et configurer Azure PowerShell](../powershell-install-configure.md) . Assurez-vous que vous installez la gestion du Service (p) et les applets de commande ressource Manager (RM). 

### <a name="values"></a>Exemples de paramètres

Vous pouvez utiliser les paramètres d’exemple comme référence.

**Paramètres VNet classiques**

Nom VNet = ClassicVNet <br>
Emplacement = fr ouest <br>
Les espaces d’adressage réseau virtuel = 10.0.0.0/24 <br>
Sous-réseau-1 = 10.0.0.0/27 <br>
GatewaySubnet = 10.0.0.32/29 <br>
Nom du réseau local = RMVNetLocal <br>

**Paramètres du Gestionnaire de ressources VNet**

Nom VNet = RMVNet <br>
Groupe de ressources = RG1 <br>
Espaces d’adresse IP de réseau virtuel = 192.168.0.0/16 <br>
Sous-réseau-1 = 192.168.1.0/24 <br>
GatewaySubnet = 192.168.0.0/26 <br>
Emplacement = fr Extrême-Orient <br>
Nom de la passerelle réseau virtuel = RMGateway <br>
Nom IP public passerelle = gwpip <br>
Type de passerelle = VPN <br>
Type de VPN = basée sur l’itinéraire <br>
Passerelle réseau local = ClassicVNetLocal <br>

## <a name="createsmgw"></a>Section 1 : Configurer les paramètres VNet classiques

Dans cette section, nous créons le réseau local et la passerelle pour votre VNet classique. Les instructions fournies dans cette section utilisent le portail classique. Pour l’instant, le portail Azure n’offre pas tous les paramètres relatifs à un VNet classique.

### <a name="part-1---create-a-new-local-network"></a>Partie 1 : créer un nouveau réseau local

Ouvrez le [portail classique](https://manage.windowsazure.com) et vous connecter avec votre compte Azure.

1. Dans le coin inférieur gauche de l’écran, cliquez sur **Nouveau** > **Services de réseau** > **Réseau virtuel** > **réseau local ajouter**.

2. Dans la fenêtre **spécifier les détails de votre réseau local** , tapez un nom pour la VNet RM vous voulez vous connecter. Dans la zone **adresse IP de périphérique VPN (facultatif)** , tapez une adresse IP publique valide. Il s’agit qu’un espace réservé temporaire. Vous modifiez cette adresse IP ultérieurement. Dans le coin inférieur droit de la fenêtre, cliquez sur la flèche.
 
3. Dans la page **spécifier l’espace d’adressage** , dans la zone de texte **IP de début** , tapez le préfixe réseau et bloc CIDR pour la VNet Gestionnaire de ressources que vous voulez vous connecter. Ce paramètre permet de spécifier l’espace d’adressage pour acheminer les messages vers le VNet RM.

### <a name="part-2---associate-the-local-network-to-your-vnet"></a>Partie 2 : associer le réseau local pour votre VNet

1. Cliquez sur les **Réseaux virtuels** en haut de la page pour passer à l’écran de réseaux virtuels, puis cliquez sur pour sélectionner votre VNet classique. Dans la page de votre VNet, cliquez sur **configurer** pour accéder à la page de configuration.

2. Dans la section de connexion **site à connectivity** , activez la case à cocher **se connecter au réseau local** . Sélectionnez ensuite le réseau local que vous avez créé. Si vous avez plusieurs réseaux locaux que vous avez créé, veillez à sélectionner celui que vous avez créé pour représenter votre VNet Gestionnaire de ressources dans la liste déroulante.

3. Cliquez sur **Enregistrer** dans la partie inférieure de la page.

### <a name="part-3---create-the-gateway"></a>Partie 3 - créer la passerelle

1. Après avoir enregistré les paramètres, cliquez sur **tableau de bord** en haut de la page pour modifier la page tableau de bord. Au bas de la page de tableau de bord, cliquez sur **Créer une passerelle**, puis cliquez sur **Le routage dynamique**. Cliquez sur **Oui** pour commencer à créer votre passerelle. Une passerelle routage dynamique est nécessaire pour cette configuration.

2. Attendez que la passerelle à créer. Cela peut parfois prendre 45 minutes ou plus pour terminer.

### <a name="ip"></a>4e partie : afficher l’adresse IP passerelle

Une fois que la passerelle a été créée, vous pouvez afficher l’adresse IP de passerelle dans la page **tableau de bord** . Il s’agit de l’adresse IP de votre passerelle. Notez ou copiez l’adresse IP. Vous l’utiliser dans les étapes suivantes lorsque vous créez le réseau local pour la configuration de votre gestionnaire de ressources VNet.


## <a name="creatermgw"></a>Section 2 : Configurer les paramètres de VNet Gestionnaire de ressources

Dans cette section, nous créons la passerelle réseau virtuel et le réseau local pour votre VNet Gestionnaire de ressources. Ne commencez pas les étapes suivantes jusqu'à une fois que vous avez extrait l’adresse IP pour la passerelle de VNet classique.

Les captures d’écran sont fournies comme exemples. N’oubliez pas de remplacer les valeurs par votre propre. Si vous créez cette configuration en guise d’exercice, reportez-vous à ces [valeurs](#values).


### <a name="part-1---create-a-gateway-subnet"></a>Partie 1 : créer un sous-réseau passerelle

Avant de vous connecter à votre réseau virtuel à une passerelle, vous devez tout d’abord créer le sous-réseau passerelle pour le réseau virtuel auquel vous voulez vous connecter. Créer un sous-réseau passerelle avec count CIDR de /28 ou plus grand (/ 27, / 26, etc..)

[AZURE.INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

À partir d’un navigateur, accédez au [portail Azure](http://portal.azure.com) et connectez-vous avec votre compte Azure.

[AZURE.INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

### <a name="part-2---create-a-virtual-network-gateway"></a>Partie 2 : créer une passerelle réseau virtuel


[AZURE.INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]


### <a name="part-3---create-a-local-network-gateway"></a>Partie 3 : créer une passerelle réseau local

La passerelle de réseau local signifie généralement votre emplacement local. Il signale Azure plages quelle adresse IP à l’itinéraire à l’emplacement et l’adresse IP du périphérique de cet emplacement. Toutefois, dans ce cas, il fait référence à la plage d’adresses et adresses IP public associé à votre VNet classique et passerelle réseau virtuel.

Donnez un nom par lequel Azure y référer à la passerelle réseau local. Vous pouvez créer votre passerelle réseau local lors de la création de votre passerelle réseau virtuel. Pour cette configuration, vous utilisez l’adresse IP qui a été affectée à votre passerelle VNet classique dans la [section précédente](#ip).

[AZURE.INCLUDE [vpn-gateway-add-lng-rm-portal](../../includes/vpn-gateway-add-lng-rm-portal-include.md)]


### <a name="part-4---copy-the-public-ip-address"></a>4e partie : copier l’adresse IP publique

Une fois que la passerelle réseau virtuel création est terminée, copiez l’adresse IP associé à la passerelle. Utilisez-la lorsque vous configurez les paramètres du réseau local pour votre VNet classique. 


## <a name="section-3-modify-the-local-network-for-the-classic-vnet"></a>Section 3 : Modifier le réseau local pour la VNet classique

Ouvrez le [portail classique](https://manage.windowsazure.com).

2. Dans le portail classique, faites défiler vers le bas sur le côté gauche, puis cliquez sur **réseaux**. Dans la page **réseaux** , cliquez sur les **Réseaux locaux** en haut de la page. 

3. Cliquez pour sélectionner le réseau local que vous avez configuré dans la partie 1. Dans la partie inférieure de la page, cliquez sur **Modifier**.

4. Dans la page **spécifier les détails de votre réseau local** , remplacez l’adresse IP espace réservé par l’adresse IP de la passerelle Gestionnaire de ressources que vous avez créé dans la section précédente. Cliquez sur la flèche pour accéder à la section suivante. Vérifiez que l' **Espace d’adressage** est correct, puis cliquez sur la case à cocher pour accepter les modifications.

## <a name="connect"></a>Section 4 : Créer la connexion

Dans cette section, nous créons la connexion entre le VNets. La procédure pour cela nécessite PowerShell. Vous ne pouvez pas créer cette connexion dans une des portails. Vérifiez que vous avez téléchargé et installé la classique (p) et les applets de commande PowerShell ressource Manager (RM).


1. Connectez-vous à votre compte Azure dans la console PowerShell. L’applet de commande suivante vous demande les informations d’identification pour votre compte Azure. Après la connexion, les paramètres de votre compte sont téléchargés afin qu’ils soient disponibles à Azure PowerShell.

        Login-AzureRmAccount 

    Obtenir une liste de vos abonnements Azure si vous avez plusieurs abonnements.

        Get-AzureRmSubscription

    Spécifier l’abonnement que vous voulez utiliser. 

        Select-AzureRmSubscription -SubscriptionName "Name of subscription"


2. Ajouter votre compte Azure pour utiliser les applets de commande PowerShell classique. Pour ce faire, vous pouvez utiliser la commande suivante :

        Add-AzureAccount

3. Définir votre clé partagé en exécutant l’exemple suivant. Dans cet exemple, `-VNetName` est le nom de la VNet classique et `-LocalNetworkSiteName` est le nom que vous avez spécifié pour le réseau local lorsque vous l’avez configurée dans le portail classique. La `-SharedKey` est une valeur que vous pouvez générer et spécifier. La valeur que vous spécifiez ici doit être la même valeur que vous spécifiez dans l’étape suivante lorsque vous créez votre connexion.

        Set-AzureVNetGatewayKey -VNetName ClassicVNet `
        -LocalNetworkSiteName RMVNetLocal -SharedKey abc123

4. Créer la connexion VPN en exécutant les commandes suivantes :
    
    **Définir les variables**

        $vnet01gateway = Get-AzureRMLocalNetworkGateway -Name ClassicVNetLocal -ResourceGroupName RG1
        $vnet02gateway = Get-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1

    **Créer la connexion**<br> Notez que la `-ConnectionType` est « IPsec », pas sur Vnet2Vnet. Dans cet exemple, `-Name` est le nom que vous souhaitez appeler votre connexion. L’exemple suivant crée une connexion intitulée «*connexion rm-classique*».
        
        New-AzureRmVirtualNetworkGatewayConnection -Name rm-to-classic-connection -ResourceGroupName RG1 `
        -Location "East US" -VirtualNetworkGateway1 `
        $vnet02gateway -LocalNetworkGateway2 `
        $vnet01gateway -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

## <a name="verify-your-connection"></a>Vérifiez votre connexion

Vous pouvez vérifier votre connexion à l’aide du portail classique, le portail Azure ou PowerShell. Vous pouvez utiliser les étapes suivantes pour vérifier votre connexion. Remplacer les valeurs par votre propre.

[AZURE.INCLUDE [vpn-gateway-verify connection](../../includes/vpn-gateway-verify-connection-rm-include.md)] 

## <a name="faq"></a>Forum aux questions VNet-VNet

Afficher les détails du Forum aux questions pour plus d’informations sur les connexions VNet-VNet.

[AZURE.INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-vnet-vnet-faq-include.md)] 


