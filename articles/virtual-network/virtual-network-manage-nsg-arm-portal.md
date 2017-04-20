<properties 
   pageTitle="Gérer les NSGs à l’aide du portail de visualisation dans le Gestionnaire de ressources | Microsoft Azure"
   description="Découvrez comment gérer existant NSGs à l’aide du portail de visualisation dans le Gestionnaire de ressources"
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
   ms.date="03/14/2016"
   ms.author="jdial" />

# <a name="manage-nsgs-using-the-preview-portal"></a>Gérer les NSGs à l’aide du portail preview

> [AZURE.SELECTOR]
- [Portail](virtual-network-manage-nsg-arm-portal.md)
- [PowerShell](virtual-network-manage-nsg-arm-ps.md)
- [Azure infrastructure du langage commun](virtual-network-manage-nsg-arm-cli.md)

[AZURE.INCLUDE [virtual-network-manage-nsg-intro-include.md](../../includes/virtual-network-manage-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]modèle de déploiement classique.

[AZURE.INCLUDE [virtual-network-manage-nsg-arm-scenario-include.md](../../includes/virtual-network-manage-nsg-arm-scenario-include.md)]

## <a name="retrieve-information"></a>Récupérer des informations

Vous pouvez afficher votre NSGs existants, récupérer des règles pour un NSG existante et déterminer quelles ressources un NSG est associé à.

### <a name="view-existing-nsgs"></a>Affichage existants NSGs
Pour afficher toutes les NSGs existants dans un abonnement, suivez les étapes ci-dessous.

1. Dans un navigateur, accédez à http://portal.azure.com et, si nécessaire, connectez-vous avec votre compte Azure.
2. Cliquez sur **Parcourir >** > **groupes de sécurité réseau**.

![Portail Azure - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure1.png)

3. Consultez la liste des NSGs dans la carte de **groupes de sécurité réseau** .

![Portail Azure - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure2.png)

Pour afficher la liste des NSGs dans le groupe de ressources **Routage NSG** , suivez les étapes ci-dessous. 

1. Cliquez sur **groupes de ressources >** > **Routage NSG** > **...**.

![Portail Azure - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure3.png)

2. Dans la liste des ressources, recherchez les éléments affichant l’icône NSG, comme indiqué dans la carte de **ressources** ci-dessous.

![Portail Azure - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure4.png)
         
### <a name="list-all-rules-for-an-nsg"></a>Liste de toutes les règles pour un NSG

Pour afficher les règles d’un NSG nommés **NSG FrontEnd**, suivez les étapes ci-dessous. 

1. Dans la carte de **groupes de sécurité réseau** , ou la carte de **ressources** ci-dessus, cliquez sur **NSG FrontEnd**.
2. Dans l’onglet **paramètres** , cliquez sur **règles de sécurité entrante**.

![Portail Azure - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure5.png)

3. La carte de **sécurité règles de trafic entrant** s’affiche comme illustré ci-dessous.

![Portail Azure - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure6.png)

4. Dans l’onglet **paramètres** , cliquez sur **règles de sécurité sortante** pour afficher les règles de trafic sortant.

>[AZURE.NOTE] Pour afficher les règles par défaut, cliquez sur l’icône de **règles par défaut** en haut de la cuillère qui affiche les règles.

### <a name="view-nsgs-associations"></a>Afficher les associations NSGs

Pour afficher ce que ressources qu'est la NSG **NSG FrontEnd** associer à, suivez les étapes ci-dessous.

1. Dans la carte de **groupes de sécurité réseau** , ou la carte de **ressources** ci-dessus, cliquez sur **NSG FrontEnd**.
2. Dans l’onglet **paramètres** , cliquez sur **sous-réseaux** pour afficher les sous-réseaux sont associés à la NSG.

![Portail Azure - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure7.png)

3. Dans l’onglet **paramètres** , cliquez sur **interfaces réseau** pour afficher les cartes réseau est associés à la NSG.

## <a name="manage-rules"></a>Gérer les règles

Vous pouvez ajouter des règles à un NSG existant, modifier les règles existantes et supprimer des règles.

### <a name="add-a-rule"></a>Ajouter une règle

Pour ajouter une règle autorisant le trafic **entrant** pour le port **443** à partir de n’importe quel ordinateur à **NSG FrontEnd** NSG, suivez les étapes ci-dessous.

1. Dans la carte de **groupes de sécurité réseau** , ou la carte de **ressources** ci-dessus, cliquez sur **NSG FrontEnd**.
2. Dans l’onglet **paramètres** , cliquez sur **règles de sécurité entrante**.
3. Dans la carte de **sécurité règles de trafic entrant** , cliquez sur **Ajouter**. Puis, dans la carte de **Ajouter une règle sécurité entrante** , remplir les valeurs comme indiqué ci-dessous, puis cliquez sur **OK**.

![Portail Azure - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure8.png)

4. Après quelques secondes, notez la nouvelle règle dans la carte de **règles de sécurité entrante** .

![Portail Azure - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure9.png)

### <a name="change-a-rule"></a>Modifier une règle

Pour modifier la règle créée ci-dessus pour autoriser le trafic entrant à partir d' **Internet** uniquement, suivez les étapes ci-dessous.

1. Dans la carte de **groupes de sécurité réseau** , ou la carte de **ressources** ci-dessus, cliquez sur **NSG FrontEnd**.
2. Dans l’onglet **paramètres** , cliquez sur la règle créée ci-dessus.
3. Dans la carte **https autoriser** , modifier la propriété **Source** comme indiqué ci-dessous, puis cliquez sur **Enregistrer**.

![Portail Azure - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure10.png)

### <a name="delete-a-rule"></a>Supprimer une règle

Pour supprimer la règle créée ci-dessus, suivez les étapes ci-dessous.

1. Dans la carte de **groupes de sécurité réseau** , ou la carte de **ressources** ci-dessus, cliquez sur **NSG FrontEnd**.
2. Dans l’onglet **paramètres** , cliquez sur la règle créée ci-dessus.
3. Dans la carte **https autoriser** , cliquez sur **Supprimer**, puis cliquez sur **Oui**.

![Portail Azure - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure11.png)

## <a name="manage-associations"></a>Gérer les associations

Vous pouvez associer un NSG à sous-réseaux et cartes réseau. Vous pouvez également dissocier un NSG à partir de n’importe quelle ressource qu'il est associée.

### <a name="associate-an-nsg-to-a-nic"></a>Associer un NSG à une carte réseau

Pour associer **NSG FrontEnd** NSG à la **TestNICWeb1** carte réseau, suivez les étapes ci-dessous.

1. Dans la carte de **groupes de sécurité réseau** , ou la carte de **ressources** ci-dessus, cliquez sur **NSG FrontEnd**.
2. Dans l’onglet **paramètres** , cliquez sur **interfaces réseau** > **associer** > **TestNICWeb1**.

![Portail Azure - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure12.png)

### <a name="dissociate-an-nsg-from-a-nic"></a>Dissocier une NSG à partir d’une carte réseau

Pour dissocier **NSG FrontEnd** NSG à partir de la carte réseau **TestNICWeb1** , suivez les étapes ci-dessous.

1. À partir du portail Azure, cliquez sur **groupes de ressources >** > **Routage NSG** > **...**  >  **TestNICWeb1**.
2. Dans la carte **TestNICWeb1** , cliquez sur **sécurité modifier...**  > **None**.

![Portail Azure - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure13.png)

>[AZURE.NOTE] Vous pouvez également utiliser cette carte pour associer la carte réseau à n’importe quel NSG existant.

### <a name="dissociate-an-nsg-from-a-subnet"></a>Dissocier une NSG à partir d’un sous-réseau

Pour dissocier **NSG FrontEnd** NSG à partir du sous-réseau **FrontEnd** , suivez les étapes ci-dessous.

1. À partir du portail Azure, cliquez sur **groupes de ressources >** > **Routage NSG** > **...**  >  **TestVNet**.
2. Dans la carte de **paramètres** , cliquez sur **sous-réseaux** > **FrontEnd** > **groupe de sécurité réseau** > **Aucun**.

![Portail Azure - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure14.png)

3. Dans la carte **FrontEnd** , cliquez sur **Enregistrer**.

![Portail Azure - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure15.png)

### <a name="associate-an-nsg-to-a-subnet"></a>Associer un NSG à un sous-réseau

Pour associer **NSG FrontEnd** NSG au sous-réseau **FronEnd** à nouveau, suivez les étapes ci-dessous.

1. À partir du portail Azure, cliquez sur **groupes de ressources >** > **Routage NSG** > **...**  >  **TestVNet**.
2. Dans la carte de **paramètres** , cliquez sur **sous-réseaux** > **FrontEnd** > **groupe de sécurité réseau** > **NSG FrontEnd**.
3. Dans la carte **FrontEnd** , cliquez sur **Enregistrer**.

>[AZURE.NOTE] Vous pouvez également associer un NSG à un sous-réseau à partir de la carte de **paramètres** de thh NSG.

## <a name="delete-an-nsg"></a>Supprimer un NSG

Vous pouvez uniquement supprimer une NSG si elle n’a pas associé à une ressource. Pour supprimer un NSG, suivez les étapes ci-dessous.

1. À partir du portail Azure, cliquez sur **groupes de ressources >** > **Routage NSG** > **...**  >  **NSG FrontEnd**.
2. Dans la carte de **paramètres** , cliquez sur **interfaces réseau**.
3. S’il existe les cartes réseau répertoriées, cliquez sur la carte réseau et suivez l’étape 2 de [Dissociate un NSG à partir d’une carte réseau](#Dissociate-an-NSG-from-a-NIC).
4. Répétez l’étape 3 pour chaque carte réseau.
5. Dans la carte de **paramètres** , cliquez sur **sous-réseaux**.
6. S’il existe une sous-réseaux répertoriés, cliquez sur le sous-réseau et suivez les étapes 2 et 3 dans [Dissociate un NSG à partir d’un sous-réseau](#Dissociate-an-NSG-from-a-subnet).
7. Faire défiler vers la gauche pour la carte **NSG FrontEnd** , puis cliquez sur **Supprimer** > **Oui**.

[Portail Azure - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure16.png)

## <a name="next-steps"></a>Étapes suivantes

- [Activer la journalisation](virtual-network-nsg-manage-log.md) pour NSGs.
