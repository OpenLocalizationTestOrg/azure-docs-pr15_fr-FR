<properties
   pageTitle="Se connecter VNets en utilisant le modèle de déploiement du Gestionnaire de ressources et le portail Azure | Microsoft Azure"
   description="Créer une connexion de passerelle VPN entre VNets en utilisant le Gestionnaire de ressources et le portail Azure."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/25/2016"
   ms.author="cherylmc" />

# <a name="configure-a-vnet-to-vnet-connection-using-the-azure-portal"></a>Configurer une connexion VNet-VNet à l’aide du portail Azure

> [AZURE.SELECTOR]
- [Responsable de ressources - portail Azure](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
- [Responsable de ressources - PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
- [Classique - portail classique](virtual-networks-configure-vnet-to-vnet-connection.md)


Cet article vous explique comment procéder pour créer une connexion entre VNets dans le modèle de déploiement Gestionnaire de ressources à l’aide de la passerelle VPN et le portail Azure.

Lorsque vous utilisez le portail Azure pour connecter des réseaux virtuels, la VNets doit être placé dans le même abonnement. Si vos réseaux virtuel se trouvent dans différents abonnements, vous pouvez toujours les connecter à l’aide de la procédure de [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md) .

![diagramme V2V](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/v2vrmps.png)

### <a name="deployment-models-and-methods-for-vnet-to-vnet-connections"></a>Modèles de déploiement et méthodes pour les connexions VNet-VNet

[AZURE.INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

Le tableau suivant indique les modèles de déploiement actuellement disponibles et les méthodes pour les configurations VNet-VNet. Lorsqu’un article avec des étapes de configuration est disponible, nous attacher directement à partir de ce tableau.

[AZURE.INCLUDE [vpn-gateway-table-vnet-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

#### <a name="vnet-peering"></a>VNet peering

[AZURE.INCLUDE [vpn-gateway-vnetpeeringlink](../../includes/vpn-gateway-vnetpeeringlink-include.md)]

## <a name="about-vnet-to-vnet-connections"></a>À propos des connexions VNet-VNet

Connexion d’un réseau virtuel à un autre réseau virtuel (VNet-VNet) est similaire à connecter un VNet à un emplacement de site local. Les deux types de connectivité permet de fournir un tunnel sécurisé à l’aide de IPsec/IKE une passerelle VPN Azure. La VNets que vous vous connectez peut être dans différentes régions ou dans différents abonnements.

Vous pouvez même combiner VNet-VNet communication avec des configurations sur plusieurs sites. Cela vous permet de vous établissez topologies réseau combiner croisée locaux connectivité avec la connectivité réseau entre virtuel, comme le montre l’illustration suivante :

![À propos des connexions] (./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/aboutconnections.png "À propos des connexions")

### <a name="why-connect-virtual-networks"></a>Pourquoi connecter réseaux virtuels ?

Vous souhaiterez peut-être connecter des réseaux virtuels pour les raisons suivantes :

- **Région croisée geo-redondance et geo présence**
    - Vous pouvez configurer votre propre geo-réplication ou de synchronisation avec une connexion sécurisée sans passer par les points de terminaison via Internet.
    - Avec Azure le trafic responsable et équilibrage de charge, vous pouvez configurer la charge de travail hautement disponible avec geo redondance dans plusieurs régions Azure. Exemple importantes, vous pouvez configurer SQL toujours dans des groupes de disponibilité répartition sur plusieurs régions Azure.

- **Applications à plusieurs niveaux régionale avec isolement ou limite administrative**
    - Dans la zone de même, vous pouvez configurer des applications multicouches avec plusieurs réseaux virtuels interconnectés en raison d’isolement ou besoins d’administration.

Pour plus d’informations sur les connexions VNet-VNet, consultez le [Forum aux questions VNet-VNet](#faq) à la fin de cet article.

### <a name="values"></a>Exemples de paramètres

Lorsque vous utilisez ces étapes en guise d’exercice, vous pouvez utiliser les exemples de valeurs de configuration. À titre d’exemple, nous utilisons plusieurs espaces d’adressage pour chaque VNet. Toutefois, VNet-VNet configurations ne nécessitent pas plusieurs espaces d’adressage.

**Valeurs pour TestVNet1 :**

- Nom de VNet : TestVNet1
- Espace d’adressage : 10.11.0.0/16
    - Nom du sous-réseau : FrontEnd
    - Plage d’adresses sous-réseau : 10.11.0.0/24
- Groupe de ressources : TestRG1
- Emplacement : États-Unis l'
- Espace d’adressage : 10.12.0.0/16
    - Nom du sous-réseau : principal
    - Plage d’adresses sous-réseau : 10.12.0.0/24
- Nom de la passerelle sous-réseau : GatewaySubnet (ce sera la recopie incrémentée dans le portail)
    - Plage d’adresses passerelle sous-réseau : 10.11.255.0/27
- Serveur DNS : Utiliser l’adresse IP de votre serveur DNS
- Nom de la passerelle réseau virtuel : TestVNet1GW
- Type de passerelle : VPN
- Type de VPN : basée sur l’itinéraire
- Référence (SKU) : Sélectionnez la référence (SKU) passerelle à utiliser
- Nom de l’adresse IP public : TestVNet1GWIP
- Valeurs de connexion :
    - Nom : TestVNet1toTestVNet4
    - Clé partagée : vous pouvez créer la clé partagée vous-même. Dans cet exemple, nous allons utiliser abc123. Il est important que lorsque vous créez la connexion entre le VNets, la valeur doit correspondre.

**Valeurs pour TestVNet4 :**

- Nom de VNet : TestVNet4
- Espace d’adressage : 10.41.0.0/16
    - Nom du sous-réseau : FrontEnd
    - Plage d’adresses sous-réseau : 10.41.0.0/24
- Groupe de ressources : TestRG1
- Emplacement : États-Unis Ouest
- Espace d’adressage : 10.42.0.0/16
    - Nom du sous-réseau : principal
    - Plage d’adresses sous-réseau : 10.42.0.0/24
- Nom de GatewaySubnet : GatewaySubnet (ce sera la recopie incrémentée dans le portail)
    - Plage d’adresses GatewaySubnet : 10.41.255.0/27
- Serveur DNS : Utiliser l’adresse IP de votre serveur DNS
- Nom de la passerelle réseau virtuel : TestVNet4GW
- Type de passerelle : VPN
- Type de VPN : basée sur l’itinéraire
- Référence (SKU) : Sélectionnez la référence (SKU) passerelle à utiliser
- Nom de l’adresse IP public : TestVNet4GWIP
- Valeurs de connexion :
    - Nom : TestVNet4toTestVNet1
    - Clé partagée : vous pouvez créer la clé partagée vous-même. Dans cet exemple, nous allons utiliser abc123. Il est important que lorsque vous créez la connexion entre le VNets, la valeur doit correspondre.


## <a name="CreatVNet"></a>1. créer et configurer TestVNet1

Si vous disposez déjà d’un VNet, vérifiez que les paramètres sont compatibles avec votre conception de passerelle VPN. Une attention particulière à n’importe quel sous-réseaux qui peuvent se superposer avec d’autres réseaux. Si vous avez superposés sous-réseaux, votre connexion ne fonctionne pas correctement. Si votre VNet est configuré avec les paramètres corrects, vous pouvez commencer les étapes décrites dans la section [spécifier un serveur DNS](#dns) .

### <a name="to-create-a-virtual-network"></a>Pour créer un réseau virtuel

[AZURE.INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]  

## <a name="subnets"></a>2. Ajouter espace d’adressage supplémentaires et créer des sous-réseaux

Vous pouvez ajouter d’espace d’adressage supplémentaires et créer des sous-réseaux une fois que votre VNet a été créé.
[AZURE.INCLUDE [vpn-gateway-additional-address-space](../../includes/vpn-gateway-additional-address-space-include.md)]

## <a name="gatewaysubnet"></a>3. créer un sous-réseau passerelle

Avant de vous connecter à votre réseau virtuel à une passerelle, vous devez tout d’abord créer le sous-réseau passerelle pour le réseau virtuel auquel vous voulez vous connecter. Si possible, il est conseillé de créer un sous-réseau passerelle à l’aide d’un bloc CIDR de /28 ou /27 afin de fournir suffisamment d’adresses IP pour répondre aux exigences de configuration futures supplémentaires.

Si vous créez cette configuration en guise d’exercice, reportez-vous à ces [exemples de paramètres](#values) lorsque vous créez votre sous-réseau passerelle.

[AZURE.INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="to-create-a-gateway-subnet"></a>Pour créer un sous-réseau passerelle

[AZURE.INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

## <a name="DNSServer"></a>4. Spécifiez un serveur DNS (facultatif)

Si vous souhaitez que résolution de noms pour machines virtuelles déployées à votre VNets, vous devez spécifier un serveur DNS.

[AZURE.INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]


## <a name="VNetGateway"></a>5. créer une passerelle réseau virtuel

Dans cette étape, vous créez la passerelle réseau virtuel pour votre VNet. Cette étape peut prendre jusqu'à 45 minutes. Si vous créez cette configuration en guise d’exercice, vous pouvez consulter les [exemples de paramètres](#values).

### <a name="to-create-a-virtual-network-gateway"></a>Pour créer une passerelle réseau virtuel

[AZURE.INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]


## <a name="CreateTestVNet4"></a>6. créer et configurer TestVNet4

Une fois que vous avez configuré TestVNet1, créez TestVNet4 en répétant les étapes précédentes, remplacer les valeurs avec celles de TestVNet4. Vous n’avez pas besoin d’attendre que la passerelle réseau virtuel pour TestVNet1 a terminé la création avant de configurer TestVNet4. Si vous utilisez vos propres valeurs, assurez-vous que les espaces d’adressage ne se chevauchent d’un des VNets que vous voulez vous connecter.


## <a name="TestVNet1Connection"></a>7. configurer la connexion TestVNet1

Lorsque les passerelles réseau virtuel pour TestVNet1 et TestVNet4 ont terminé, vous pouvez créer votre réseau virtuel connexions passerelle. Dans cette section, vous allez créer une connexion à partir de VNet1 à VNet4.

1. Dans **toutes les ressources**, accédez à la passerelle réseau virtuel pour votre VNet. Par exemple, **TestVNet1GW**. Cliquez sur **TestVNet1GW** pour ouvrir la carte de passerelle réseau virtuel.

    ![Carte de connexions] (./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/settings_connection.png "Carte de connexions")

2. Cliquez sur **+ Ajouter** pour ouvrir la carte de **Ajouter une connexion** .

3. Sur la carte **Ajouter une connexion** , dans le champ nom, tapez un nom pour votre connexion. Par exemple, **TestVNet1toTestVNet4**.

    ![Nom de la connexion] (./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/v1tov4.png "Nom de la connexion")

4. Pour le **type de connexion**. Sélectionnez **VNet-VNet** dans la liste déroulante.

5. La valeur du champ **première passerelle réseau virtuel** est automatiquement remplie parce que vous créez cette connexion à partir de la passerelle réseau virtuel spécifié.

6. Le champ **deuxième passerelle réseau virtuel** est VNet que vous voulez créer une connexion à la passerelle réseau virtuel. Cliquez sur **Choisir une autre passerelle réseau virtuel** pour ouvrir la carte **Sélectionnez-en réseau virtuel** .

    ![Ajouter une connexion] (./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/add_connection.png "Ajouter une connexion")

7. Afficher les passerelles réseau virtuel qui figurent sur cette carte. Remarquez que seules les passerelles réseau virtuel qui se trouvent dans votre abonnement sont répertoriés. Si vous voulez vous connecter à une passerelle réseau virtuel qui n’est pas dans votre abonnement, utilisez l' [article PowerShell](vpn-gateway-vnet-vnet-rm-ps.md). 

8. Cliquez sur la passerelle réseau virtuel que vous voulez vous connecter.
 
9. Dans le champ de **clé partagée** , tapez une clé partagée pour votre connexion. Vous pouvez générer ou créer cette clé vous-même. Dans une connexion à un site, la clé que vous utilisez serait exactement le même pour votre appareil local et votre connexion de passerelle réseau virtuel. Le concept est semblable ici, à l’exception qui plutôt que de se connecter à un dispositif VPN, vous vous connectez à une autre passerelle réseau virtuel.

    ![Clé partagée] (./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/sharedkey.png "Clé partagée")

10. Cliquez sur **OK** dans la partie inférieure de la cuillère pour enregistrer vos modifications.

## <a name="TestVNet4Connection"></a>8. configurer la connexion TestVNet4

Ensuite, créer une connexion entre TestVNet4 et TestVNet1. Utilisez la méthode qui vous permet de créer la connexion entre TestVNet1 et TestVNet4. Assurez-vous que vous utilisez la même clé partagée.

## <a name="VerifyConnection"></a>9. Vérifiez votre connexion

Vérifiez la connexion. Pour chaque passerelle réseau virtuel, procédez comme suit :

1. Recherchez la carte de la passerelle réseau virtuel. Par exemple, **TestVNet4GW**. 
2. Dans la carte de passerelle réseau virtuel, cliquez sur **connexions** pour afficher la carte de connexions de la passerelle réseau virtuel.

Afficher les connexions et vérifier l’état. Une fois la connexion est créée, vous verrez **a réussi** et **connecté** en tant que les valeurs d’état.

![A réussi.] (./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/connected.png "A réussi.")

Vous pouvez double-cliquer sur chaque connexion séparément pour afficher plus d’informations sur la connexion.

![Essentials] (./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/essentials.png "Essentials")

## <a name="faq"></a>Forum aux questions VNet-VNet

Afficher les détails du Forum aux questions pour plus d’informations sur les connexions VNet-VNet.

[AZURE.INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-vnet-vnet-faq-include.md)]

## <a name="next-steps"></a>Étapes suivantes

Une fois que votre connexion est terminée, vous pouvez ajouter des machines virtuelles à vos réseaux virtuel. Pour obtenir la procédure, voir [créer une Machine virtuelle](../virtual-machines/virtual-machines-windows-hero-tutorial.md) .
