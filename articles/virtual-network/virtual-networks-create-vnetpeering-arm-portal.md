<properties
   pageTitle="Créer VNet Peering à l’aide du portail Azure | Microsoft Azure"
   description="Apprenez à créer un réseau virtuel à l’aide du portail Azure dans le Gestionnaire de ressources."
   services="virtual-network"
   documentationCenter=""
   authors="NarayanAnnamalai"
   manager="jefco"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/14/2016"
   ms.author="narayanannamalai;annahar"/>

# <a name="create-a-virtual-network-peering-using-the-azure-portal"></a>Créer un réseau virtuel peering à l’aide du portail Azure

[AZURE.INCLUDE [virtual-networks-create-vnet-selectors-arm-include](../../includes/virtual-networks-create-vnetpeering-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnetpeering-intro-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-basic-include](../../includes/virtual-networks-create-vnetpeering-scenario-basic-include.md)]

Pour créer un VNet peering basée sur le scénario ci-dessus, à l’aide du portail Azure, suivez les étapes ci-dessous.

1. Dans un navigateur, accédez à http://portal.azure.com et, si nécessaire, connectez-vous avec votre compte Azure.
2. Pour établir VNET peering, vous devez créer deux liens, une pour chaque direction, entre deux VNets. Vous pouvez tout d’abord créer lien d’homologation VNET pour VNET1 à VNET2. Dans le portail, cliquez sur **Parcourir** > **Choisissez Virtual Networks**

    ![Créer VNet peering portail Azure](./media/virtual-networks-create-vnetpeering-arm-portal/figure01.png)

3. Dans la carte de réseaux virtuels, choisissez VNET1, cliquez sur Peerings, puis cliquez sur Ajouter

    ![Choisissez peering](./media/virtual-networks-create-vnetpeering-arm-portal/figure02.png)

4. Dans la carte ajouter Peering, donnez un nom de lien homologation LinkToVnet2, sélectionnez l’abonnement et l’homologue VNET2 réseau virtuel, cliquez sur OK.

    ![Lien vers VNet](./media/virtual-networks-create-vnetpeering-arm-portal/figure03.png)

5. Une fois que ce lien homologation VNET est créé. Vous pouvez consulter l’état de lien comme suit :

    ![État de la liaison](./media/virtual-networks-create-vnetpeering-arm-portal/figure04.png)

6. Ensuite, créez le lien d’homologation VNET pour VNET2 à VNET1. Dans la carte de réseaux virtuels, choisissez VNET2, cliquez sur Peerings, puis cliquez sur Ajouter

    ![Égal à partir d’autres VNet](./media/virtual-networks-create-vnetpeering-arm-portal/figure05.png)

7. Dans la carte ajouter Peering, donnez un nom de lien homologation LinkToVnet1, sélectionnez l’abonnement et l’homologue réseau virtuel, cliquez sur OK.

    ![Création de mosaïque réseau virtuel](./media/virtual-networks-create-vnetpeering-arm-portal/figure06.png)

8. Une fois que ce lien homologation VNET est créé. Vous pouvez consulter l’état de lien comme suit :

    ![État de la liaison finale](./media/virtual-networks-create-vnetpeering-arm-portal/figure07.png)

9. Vérifier l’état pour LinkToVnet2 et il passe maintenant à connecté également.  

    ![État de la liaison finale 2](./media/virtual-networks-create-vnetpeering-arm-portal/figure08.png)

    > [AZURE.NOTE] VNET peering est établie uniquement si les deux liens sont connectés.

Il existe quelques propriétés configurables pour chaque lien :

|Option|Description|Par défaut|
|:-----|:----------|:------|
|AllowVirtualNetworkAccess|Si l’espace de VNet égal à inclure dans le cadre de la balise Virtual_network d’adressage|Oui|
|AllowForwardedTraffic|Si le trafic ne proviennent ne pas une VNet ressources est accepté ou rejeté|N°|
|AllowGatewayTransit|Permet à l’hôte VNet à utiliser votre passerelle VNet|N°|
|UseRemoteGateways|Utiliser la passerelle de VNet de votre collègue. L’homologue VNet doit comporter une passerelle est configurée et AllowGatewayTransit est sélectionnée. Vous ne pouvez pas utiliser cette option si vous avez une passerelle est configurée|N°|

Chaque lien dans VNet peering possède un ensemble d’au-dessus de propriétés. À partir du portail, vous pouvez cliquer sur le lien Peering VNet et changer les options disponibles, cliquez sur Enregistrer pour que l’effet de modification.

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-crosssub-include](../../includes/virtual-networks-create-vnetpeering-scenario-crosssub-include.md)]

1. Dans un navigateur, accédez à http://portal.azure.com et, si nécessaire, connectez-vous avec votre compte Azure.
2. Dans cet exemple nous utiliserons deux abonnements A et B et deux utilisateurs utilisateur a et utilisateur b avec les privilèges dans les abonnements respectivement
3. Dans le portail, cliquez sur Parcourir, choisissez les réseaux virtuel. Cliquez sur le VNET et cliquez sur Ajouter.

    ![Scénario 2 Parcourir](./media/virtual-networks-create-vnetpeering-arm-portal/figure09.png)

4. Dans la carte d’access ajouter, cliquez sur Sélectionner un rôle et choisissez réseau collaborateurs et cliquez sur Ajouter des utilisateurs, tapez le signe utilisateur b dans la zone Nom, cliquez sur OK.

    ![RBAC](./media/virtual-networks-create-vnetpeering-arm-portal/figure10.png)

    Ce n’est pas obligatoire, peering peut être établie même si les utilisateurs déclenchent individuellement demandes homologation pour leur Vnets respectives dans la mesure où les demandes correspond à. Ajout dotés de privilèges utilisateur de l’autre VNet en tant qu’utilisateurs dans le VNet local facilite la configuration en portail.

5. Puis connectez-vous au portail Azure avec utilisateur b qui correspond à l’utilisateur de privilèges pour SubscriptionB. Suivez les étapes pour ajouter un utilisateur a comme collaborateurs réseau ci-dessus.

    ![RBAC2](./media/virtual-networks-create-vnetpeering-arm-portal/figure11.png)

    > [AZURE.NOTE] Vous pouvez fermer la session et ouvrez une session sur les deux sessions utilisateur dans le navigateur pour garantir que l’autorisation est activée avec succès.

6. Connexion au portail en tant que l’utilisateur a, accédez à la carte VNET3, cliquez sur Peering, vérifier ' savoir si mon ID ressource « ID de case à cocher et le type de la ressource pour VNET5 dans au-dessous de format.

    / subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.Network/VirtualNetwork/{VNETname}

    ![Nº ressource](./media/virtual-networks-create-vnetpeering-arm-portal/figure12.png)

7. Connectez-vous au portail en tant que l’utilisateur b et suivre au-dessus étape pour créer le lien d’homologation de VNET5 à VNet3.

    ![Nº ressource 2](./media/virtual-networks-create-vnetpeering-arm-portal/figure13.png)

8. Peering sera établie et toute machine virtuelle dans VNet3 doit être en mesure de communiquer avec n’importe quel ordinateur virtuel dans VNet5

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-transit-include](../../includes/virtual-networks-create-vnetpeering-scenario-transit-include.md)]

1. D’abord, VNET homologation des liens à partir de HubVnet vers VNET1. Notez qu’option Autoriser le trafic de transmis n’est pas activée pour le lien.

    ![Peering simple](./media/virtual-networks-create-vnetpeering-arm-portal/figure14.png)

2. Étape suivante, homologation de VNET1 des liens vers des HubVnet peuvent être créés. Notez qu’Autoriser transféré le trafic option est sélectionnée.

    ![Peering simple](./media/virtual-networks-create-vnetpeering-arm-portal/figure15a.png)

3. Une fois peering établie, vous pouvez faire référence à cet [article](virtual-network-create-udr-arm-ps.md) et définir Route(UDR) de définies par l’utilisateur afin de rediriger le trafic VNet1 via une application virtuelle à utiliser ses fonctions. Lorsque vous spécifiez l’adresse de saut suivant dans l’itinéraire, vous pouvez le configurer pour l’adresse IP du matériel virtuel dans homologue VNet HubVNet


[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-asmtoarm-include](../../includes/virtual-networks-create-vnetpeering-scenario-asmtoarm-include.md)]



1. Dans un navigateur, accédez à http://portal.azure.com et, si nécessaire, connectez-vous avec votre compte Azure.

2. Pour établir VNET peering dans ce scénario, vous devez ne créer qu’un seul lien, dans le réseau virtuel dans le Gestionnaire de ressources Azure à celui de classique. Autrement dit, à partir de **VNET1** à **VNET2**. Dans le portail, cliquez sur **Parcourir** > choisir **Les réseaux virtuels**

3. Dans la carte de réseaux virtuelle, choisissez **VNET1**. Cliquez sur **Peerings**, puis cliquez sur **Ajouter**.

4. Dans la carte ajouter Peering, nommez votre lien. Ici, il est appelé **LinkToVNet2**. Sous Détails homologue, sélectionnez **classique**.

5. Puis sélectionnez l’abonnement et le réseau virtuel **VNET2**pair. Cliquez ensuite sur OK.

    ![Attacher Vnet1 Vnet 2](./media/virtual-networks-create-vnetpeering-arm-portal/figure18.png)

6. Une fois ce lien homologation VNet est créé, les deux réseaux virtuels sont ressources et vous ne pourrez pas voir les rubriques suivantes :

    ![Vérification de la connexion homologation](./media/virtual-networks-create-vnetpeering-arm-portal/figure19.png)


## <a name="remove-vnet-peering"></a>Supprimer VNet Peering

1.  Dans un navigateur, accédez à http://portal.azure.com et, si nécessaire, connectez-vous avec votre compte Azure.
2.  Accédez à la carte réseau virtuel, cliquez sur Peerings, cliquez sur le lien que vous voulez supprimer, cliquez sur le bouton Supprimer.

    ![Delete1](./media/virtual-networks-create-vnetpeering-arm-portal/figure15.png)

3. Une fois que vous supprimez un lien VNET peering, l’état des liaisons homologue accède à déconnecté.

    ![Delete2](./media/virtual-networks-create-vnetpeering-arm-portal/figure16.png)

4. Dans cet état, vous ne pouvez pas recréer le lien jusqu'à ce que l’état de lien homologue devient initié. Nous vous recommandons de que vous supprimez les deux liens avant de recréer le VNET peering.
