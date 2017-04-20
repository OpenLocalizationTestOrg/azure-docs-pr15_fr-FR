<properties 
   pageTitle="Comment créer des NSGs en mode processeur à l’aide du portail Azure | Microsoft Azure"
   description="Découvrez comment créer et déployer NSGs dans processeur à l’aide du portail Azure"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/04/2016"
   ms.author="jdial" />

# <a name="how-to-manage-nsgs-using-the-azure-portal"></a>Comment gérer les NSGs à l’aide du portail Azure

[AZURE.INCLUDE [virtual-networks-create-nsg-selectors-arm-include](../../includes/virtual-networks-create-nsg-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Cet article décrit le modèle de déploiement du Gestionnaire de ressources. Vous pouvez également [créer NSGs dans le modèle de déploiement classique](virtual-networks-create-nsg-classic-ps.md).

[AZURE.INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

L’exemple PowerShell commandes ci-dessous attendent un environnement simple déjà créé en fonction du scénario ci-dessus. Si vous souhaitez exécuter les commandes qu’ils s’affichent dans ce document, tout d’abord créer l’environnement de test en déployant [ce modèle](http://github.com/telmosampaio/azure-templates/tree/master/201-IaaS-WebFrontEnd-SQLBackEnd), cliquez sur **déployer vers Azure**, remplacer les valeurs de paramètre par défaut si nécessaire et suivez les instructions dans le portail. Les étapes ci-dessous utilisent **Routage NSG** en tant que le nom du groupe de ressources sur que le modèle a été déployé.

## <a name="create-the-nsg-frontend-nsg"></a>Créer la NSG NSG FrontEnd

Pour créer le **Site Web frontal NSG** NSG comme indiqué dans le scénario ci-dessus, suivez les étapes ci-dessous.

1. Dans un navigateur, accédez à http://portal.azure.com et, si nécessaire, connectez-vous avec votre compte Azure.
2. Cliquez sur **Parcourir >** > **groupes de sécurité réseau**.

    ![Portail Azure - NSGs](./media/virtual-networks-create-nsg-arm-pportal/figure11.png)

3. Dans la carte de **groupes de sécurité réseau** , cliquez sur **Ajouter**.
  
    ![Portail Azure - NSGs](./media/virtual-networks-create-nsg-arm-pportal/figure12.png)

4. Dans la carte de **créer un groupe sécurité réseau** , créez un NSG nommé *NSG FrontEnd* dans le groupe de ressources *Routage NSG* et puis cliquez sur **créer**.

    ![Portail Azure - NSGs](./media/virtual-networks-create-nsg-arm-pportal/figure13.png)

## <a name="create-rules-in-an-existing-nsg"></a>Créer des règles dans un NSG existant

Pour créer des règles dans un NSG existant à partir du portail Azure, suivez les étapes ci-dessous.

2. Cliquez sur **Parcourir >** > **groupes de sécurité réseau**.

3. Dans la liste des NSGs, cliquez sur **NSG FrontEnd** > **les règles de sécurité entrant**

    ![Portail Azure - NSG FrontEnd](./media/virtual-networks-create-nsg-arm-pportal/figure2.png)

4. Dans la liste des **règles de sécurité entrant**, cliquez sur **Ajouter**.

    ![Portail Azure - ajouter une règle](./media/virtual-networks-create-nsg-arm-pportal/figure3.png)

5. Dans la carte de **Ajouter une règle sécurité entrants** , créer une règle nommée *règle web* avec une priorité de *200* autoriser la connexion via *TCP* vers le port *80* à toute machine virtuelle à partir de n’importe quelle source, puis cliquez sur **OK**. Notez que la plupart de ces paramètres est déjà les valeurs par défaut.

    ![Portail Azure - paramètres de la règle](./media/virtual-networks-create-nsg-arm-pportal/figure4.png)

6. Après quelques secondes, vous verrez la nouvelle règle dans la NSG.

    ![Portail Azure - nouvelle règle](./media/virtual-networks-create-nsg-arm-pportal/figure5.png)

7. Répétez les étapes 6 pour créer une règle de trafic entrant nommée *rdp règle* avec une priorité de *250* autoriser la connexion via *TCP* port *3389* à toute machine virtuelle à partir de n’importe quelle source.

## <a name="associate-the-nsg-to-the-frontend-subnet"></a>Associer le NSG au sous-réseau FrontEnd

1. Cliquez sur **Parcourir >** > **groupes de ressources** > **Routage NSG**.
2. Dans la carte **Routage NSG** , cliquez sur **...**  >  **TestVNet**.

    ![Portail Azure - TestVNet](./media/virtual-networks-create-nsg-arm-pportal/figure14.png)

3. Dans la carte de **paramètres** , cliquez sur **sous-réseaux** > **FrontEnd** > **groupe de sécurité réseau** > **NSG FrontEnd**.

    ![Portail Azure - paramètres de sous-réseau](./media/virtual-networks-create-nsg-arm-pportal/figure15.png)

4. Dans la carte **FrontEnd** , cliquez sur **Enregistrer**.

    ![Portail Azure - paramètres de sous-réseau](./media/virtual-networks-create-nsg-arm-pportal/figure16.png)

## <a name="create-the-nsg-backend-nsg"></a>Créer la NSG NSG principal

Pour créer la **Version serveur NSG** NSG et associer au sous-réseau **principal** , suivez les étapes ci-dessous.

1. Répétez les étapes décrites dans [créer la NSG NSG FrontEnd](#Create-the-NSG-FrontEnd-NSG) pour créer un NSG nommé *NSG principal*
2. Répétez les étapes décrites dans [créer des règles dans un NSG existant](#Create-rules-in-an-existing-NSG) pour créer des règles de **trafic entrant** dans le tableau ci-dessous.

  	|Règle de trafic entrant|Règle sortante|
  	|---|---|
  	|![Portail Azure - règle de trafic entrant](./media/virtual-networks-create-nsg-arm-pportal/figure17.png)|![Portail Azure - règle sortante](./media/virtual-networks-create-nsg-arm-pportal/figure18.png)|

3. Répétez les étapes [associer la NSG au sous-réseau FrontEnd](#Associate-the-NSG-to-the-FrontEnd-subnet) associer la **Version serveur NSG** NSG au sous-réseau **principal** .

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [Gérer les NSGs existants](virtual-network-manage-nsg-arm-portal.md)
- [Activer la journalisation](virtual-network-nsg-manage-log.md) pour NSGs.