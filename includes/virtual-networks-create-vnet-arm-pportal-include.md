## <a name="how-to-create-a-vnet-in-the-azure-portal"></a>Comment créer un VNet dans le portail Azure

Pour créer un VNet basé sur le scénario ci-dessus, à l’aide du portail Azure preview, suivez les étapes ci-dessous.

1. Dans un navigateur, accédez à http://portal.azure.com et, si nécessaire, connectez-vous avec votre compte Azure.
2. Cliquez sur **Nouveau** > **réseau** > **réseau virtuel**, puis cliquez sur **Gestionnaire de ressources** dans la liste **Sélectionner un modèle de déploiement** , puis cliquez sur **créer**, comme illustré dans l’illustration suivante.

    ![Créer des VNet portail Azure](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure1.gif)

3. Sur la carte **réseau virtuel créer** , configurer les paramètres VNet comme le montre l’illustration suivante.

    ![Créer la carte réseau virtuel](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure2.png)

4. Cliquez sur **groupe de ressources** et sélectionnez un groupe de ressources pour ajouter la VNet à ou cliquez sur **Créer nouveau** pour ajouter la VNet à un groupe de ressources. L’illustration suivante affiche des paramètres de groupe pour un nouveau groupe de ressources appelé **TestRG**la ressource. Pour plus d’informations sur les groupes de ressources, consultez [Vue d’ensemble du Gestionnaire de ressources Azure](../articles/resource-group-overview.md#resource-groups).

    ![Groupe de ressources](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure3.png)

5. Si nécessaire, modifiez les paramètres **d’abonnement** et un **emplacement** pour votre VNet. 

6. Si vous ne souhaitez pas voir le VNet comme une vignette dans la **Startboard**, désactivez **attacher aux Startboard**. 

7. Cliquez sur **créer** et notez la vignette appelée **Création virtuel réseau** comme indiqué dans l’illustration ci-dessous.

    ![Création de mosaïque réseau virtuel](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure4.png)

8. Attendez que la VNet à créer, puis dans la carte **réseau virtuelle** , cliquez sur **tous les paramètres** > **sous-réseaux** > **Ajouter** comme indiqué ci-dessous.

    ![Ajout de sous-réseau dans le portail Azure](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure5.gif)

9. Spécifier les paramètres de sous-réseau pour le sous-réseau *principal* , comme indiqué ci-dessous, puis cliquez sur **OK**. 

    ![Paramètres de sous-réseau](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure6.png)

10. Notez la liste des sous-réseaux, comme indiqué dans l’illustration ci-dessous.

    ![Liste des sous-réseaux VNet](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure7.png)
