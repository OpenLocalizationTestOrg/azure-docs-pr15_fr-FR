<properties
   pageTitle="Ouvrez les ports pour une machine virtuelle à l’aide du portail Azure | Microsoft Azure"
   description="Découvrez comment ouvrir un port / créer un point de terminaison de votre machine virtuelle Windows à l’aide du modèle de déploiement de gestionnaire de ressource dans le portail Azure"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="10/27/2016"
   ms.author="iainfou"/>

# <a name="opening-ports-to-a-vm-in-azure-using-the-azure-portal"></a>Ouverture de ports pour une machine virtuelle dans Azure à l’aide du portail Azure
[AZURE.INCLUDE [virtual-machines-common-nsg-quickstart](../../includes/virtual-machines-common-nsg-quickstart.md)]

## <a name="quick-commands"></a>Commandes rapides
Vous pouvez également [effectuer ces étapes à l’aide de PowerShell Azure](virtual-machines-windows-nsg-quickstart-powershell.md).

Commencez par créer votre groupe de sécurité réseau. Sélectionnez un groupe de ressources dans le portail, cliquez sur **Ajouter**, puis recherchez et sélectionnez « Groupe de sécurité du réseau » :

![Ajouter un groupe de sécurité réseau](./media/virtual-machines-windows-nsg-quickstart-portal/add-nsg.png)

Entrez un nom pour votre groupe de sécurité réseau, sélectionnez ou créer un groupe de ressources et sélectionnez un emplacement. Cliquez sur **créer** lorsque vous avez terminé :

![Créer un groupe de sécurité réseau](./media/virtual-machines-windows-nsg-quickstart-portal/create-nsg.png)

Sélectionnez votre nouveau groupe de sécurité réseau. Sélectionnez les règles de sécurité entrant, puis cliquez sur le bouton **Ajouter** pour créer une règle :

![Ajouter une règle de trafic entrant](./media/virtual-machines-windows-nsg-quickstart-portal/add-inbound-rule.png)

Indiquez un nom pour votre nouvelle règle. Port 80 est déjà entré par défaut. Cette carte est dans laquelle vous souhaitez modifier la source, le protocole et la destination lors de l’ajout des règles supplémentaires à votre groupe de sécurité réseau. Cliquez sur **OK** pour créer la règle :

![Créer une règle de trafic entrant](./media/virtual-machines-windows-nsg-quickstart-portal/create-inbound-rule.png)

La dernière étape consiste à associer un sous-réseau ou une interface réseau spécifique dans votre groupe de sécurité réseau. Nous allons associer le groupe de sécurité réseau à un sous-réseau. Sélectionnez « Sous-réseaux », puis cliquez sur **associer**:

![Associer un groupe de sécurité réseau un sous-réseau](./media/virtual-machines-windows-nsg-quickstart-portal/associate-subnet.png)

Sélectionnez votre réseau virtuel, puis sous-réseau appropriée :

![Associer un groupe de sécurité réseau à un réseau virtuel](./media/virtual-machines-windows-nsg-quickstart-portal/select-vnet-subnet.png)

Vous venez de créer un groupe de sécurité réseau, créé une règle de trafic entrant qui autorise le trafic sur le port 80 et associé à un sous-réseau. Les ordinateurs virtuels que vous vous connectez à ce sous-réseau sont accessibles sur le port 80.


## <a name="more-information-on-network-security-groups"></a>Plus d’informations sur les groupes de sécurité réseau
Les commandes rapides ici permettent d’être opérationnel avec le trafic vers votre ordinateur virtuel. Groupes de sécurité réseau fournissent des nombreuses nouvelles fonctionnalités et précision pour contrôler l’accès à vos ressources. Vous pouvez en savoir plus sur la [Création d’un groupe de sécurité réseau et les règles et ici](../virtual-network/virtual-networks-create-nsg-arm-ps.md).

Vous pouvez définir des groupes de sécurité réseau et les règles et dans le cadre de modèles Azure le Gestionnaire de ressources. En savoir plus sur la [Création de groupes de sécurité réseau avec les modèles](../virtual-network/virtual-networks-create-nsg-arm-template.md).

Si vous devez utiliser transfert de port pour mapper un port externe unique à un port interne sur votre ordinateur virtuel, utilisez un programme d’équilibrage de charge et les règles de la traduction d’adresses réseau (NAT). Par exemple, vous souhaiterez peut-être exposer le port TCP 8080 avec l’extérieur et que le trafic dirigé vers le port TCP 80 sur un ordinateur virtuel. Vous pouvez en savoir plus sur la [Création d’un équilibrage de charge via Internet](../load-balancer/load-balancer-get-started-internet-arm-ps.md).

## <a name="next-steps"></a>Étapes suivantes
Dans cet exemple, vous avez créé une règle simple pour autoriser le trafic HTTP. Vous pouvez trouver des informations sur la création des environnements plus détaillées dans les articles suivants :

- [Vue d’ensemble du Gestionnaire de ressources Azure](../azure-resource-manager/resource-group-overview.md)
- [Qu’est un groupe de sécurité réseau (NSG) ?](../virtual-network/virtual-networks-nsg.md)
- [Azure vue d’ensemble du Gestionnaire de ressources pour les programmes d’équilibrage de charge](../load-balancer/load-balancer-arm.md)
