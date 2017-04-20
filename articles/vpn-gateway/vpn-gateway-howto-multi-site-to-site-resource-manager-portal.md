<properties
   pageTitle="Comment ajouter plusieurs connexions de Site à passerelle VPN à un réseau virtuel pour le modèle de déploiement Gestionnaire de ressources à l’aide du portail Azure | Microsoft Azure"
   description="Ajouter plusieurs sites S2S connexions à une passerelle VPN qui a une connexion existante"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/10/2016"
   ms.author="cherylmc"/>



# <a name="add-a-site-to-site-connection-to-a-vnet-with-an-existing-vpn-gateway-connection"></a>Ajouter une connexion à un Site à un VNet avec une connexion de passerelle VPN existante

> [AZURE.SELECTOR]
- [Responsable de ressources - portail](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
- [Classique - PowerShell](vpn-gateway-multi-site.md)

Cet article décrit à l’aide du portail Azure pour ajouter des connexions de Site à Site (S2S) pour une passerelle VPN qui a une connexion existante. Ce type de connexion est souvent appelé une configuration « comportant plusieurs sites ». 

Vous pouvez utiliser cet article pour ajouter une connexion S2S à un VNet qui comporte déjà un S2S connexion, une connexion Point-à-Site ou une connexion VNet-VNet. Il existe certaines limitations lors de l’ajout de connexions. Consultez la section [avant de commencer](#before) dans cet article pour vérifier avant de commencer votre configuration. 

Cet article s’applique aux VNets créé à l’aide du modèle de déploiement du Gestionnaire de ressources qui ont une passerelle RouteBased VPN. Ces étapes ne s’appliquent pas aux configurations de connexion pouvant coexistence ExpressRoute/Site-à-Site. Pour plus d’informations sur les connexions pouvant coexistence, voir [connexions pouvant coexistence ExpressRoute/S2S](../expressroute/expressroute-howto-coexist-resource-manager.md) .

### <a name="deployment-models-and-methods"></a>Méthodes et modèles de déploiement

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

Nous mettre à jour ce tableau en tant que nouveaux articles et outils supplémentaires sont disponibles pour cette configuration. Lorsqu’un article est disponible, nous attacher directement à partir de ce tableau.

[AZURE.INCLUDE [vpn-gateway-table-multi-site](../../includes/vpn-gateway-table-multisite-include.md)] 


## <a name="before"></a>Avant de commencer

Vérifiez les éléments suivants :

- Vous ne créez pas une connexion pouvant coexistence ExpressRoute/S2S.
- Vous disposez d’un réseau virtuel qui a été créé à l’aide du modèle de déploiement du Gestionnaire de ressources avec une connexion existante.
- La passerelle réseau virtuel pour votre VNet est RouteBased. Si vous avez une passerelle PolicyBased VPN, vous devez supprimer la passerelle réseau virtuel et création d’une passerelle VPN en tant que RoutBased.
- Aucune des plages d’adresses se chevauchent pour une des VNets que cette VNet se connecte à.
- Vous avez un appareil VPN compatible et une personne qui est en mesure de configurer. Voir [à propos des appareils VPN](vpn-gateway-about-vpn-devices.md). Si vous ne familiarisé avec la configuration de votre appareil VPN, ou que vous ne connaissez pas l’adresse IP plages situées dans vos locaux configuration du réseau, vous devez coordonner avec une personne qui pourra vous fournir ces informations.
- Vous avez une adresse IP publique tourné vers l’extérieur de votre périphérique VPN. Cette adresse IP ne peut pas être située derrière un NAT.


## <a name="part1"></a>Partie 1 : configurer une connexion

1. À partir d’un navigateur, accédez au [portail Azure](http://portal.azure.com) et, si nécessaire, connectez-vous avec votre compte Azure.
2. Cliquez sur **toutes les ressources** et recherchez votre **passerelle réseau virtuel** dans la liste des ressources et cliquez dessus.
3. Dans la carte de la **passerelle réseau virtuelle** , cliquez sur **connexions**.

    ![Carte de connexions](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/connectionsblade.png "Connections blade")<br>

4. Dans la carte de **connexions** , cliquez sur **+ Ajouter**.

    ![Ajouter le bouton de connexion](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/addbutton.png "Add connection button")<br>

5. Dans la carte **Ajouter une connexion** , remplissez les champs suivants :
    - **Nom :** Le nom que vous voulez donner au site que vous créez la connexion à.
    - **Type de connexion :** Sélectionnez le **Site à site (IPsec)**.

    ![Ajouter la carte de connexion](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/addconnectionblade.png "Add connection blade")<br>

## <a name="part2"></a>Partie 2 - ajouter une passerelle réseau local

1. Cliquez sur **passerelle réseau Local** ***Choisir une passerelle réseau local***. Cette action ouvre la carte de **passerelle du réseau local choisir** .

    ![Choisissez passerelle réseau local](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/chooselng.png "Choose local network gateway")<br>
2. Cliquez sur **créer** pour ouvrir la carte de **passerelle du réseau local créer** .

    ![Créer la carte de passerelle réseau local](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/createlngblade.png "Create local network gateway")<br>

3. Sur la carte de la **passerelle du réseau local créer** , renseignez les champs suivants :
    - **Nom :** Le nom que vous souhaitez donner à la ressource de passerelle réseau local.
    - **Adresse IP :** L’adresse IP du périphérique VPN sur le site que vous voulez vous connecter.
    - **Espace d’adressage :** L’espace d’adressage que vous souhaitez être routés vers le nouveau site de réseau local.
4. Dans la carte de **passerelle du réseau local créer** pour enregistrer les modifications, cliquez sur **OK** .

## <a name="part3"></a>Partie 3 - ajouter la clé partagée et créer la connexion

1. Sur la carte **Ajouter une connexion** , ajoutez la clé partagée que vous souhaitez utiliser pour créer votre connexion. Obtenir la clé partagée à partir de votre appareil VPN, ou en créer une ici et ensuite configurer votre appareil VPN pour utiliser la même clé partagée. Il est important que les clés sont exactement identiques.

    ![Clé partagée](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/sharedkey.png "Shared key")<br>
2. Dans la partie inférieure de la cuillère, cliquez sur **OK** pour créer la connexion.

## <a name="part4"></a>Partie 4 - Vérifiez la connexion VPN

Vous pouvez vérifier votre connexion VPN dans le portail ou à l’aide de PowerShell.

[AZURE.INCLUDE [vpn-gateway-verify-connection-rm](../../includes/vpn-gateway-verify-connection-rm-include.md)]


## <a name="next-steps"></a>Étapes suivantes

- Une fois que votre connexion est terminée, vous pouvez ajouter des machines virtuelles à vos réseaux virtuel. Voir les machines virtuelles [rubriques d’apprentissage](https://azure.microsoft.com/documentation/learning-paths/virtual-machines) pour plus d’informations.
