## <a name="how-to-create-a-classic-vnet-in-the-azure-portal"></a>Comment créer un VNet classique dans le portail Azure

Pour créer un VNet classique en fonction du scénario ci-dessus, suivez les étapes ci-dessous.

1. Dans un navigateur, accédez à http://portal.azure.com et, si nécessaire, connectez-vous avec votre compte Azure.
2. Cliquez sur **Nouveau** > **réseau** > **réseau virtuelle**, notez que la liste **Sélectionner un modèle de déploiement** déjà affiche **classique**, puis cliquez sur **créer**, comme illustré dans l’illustration suivante.

    ![Créer des VNet portail Azure](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure1.gif)

3. Sur la carte **réseau virtuelle** , tapez le **nom** de la VNet, puis cliquez sur **espace d’adressage**. Configurer vos paramètres d’espace pour la VNet et son sous-masque première adresse, puis cliquez sur **OK**. L’illustration suivante montre les paramètres de blocage CIDR pour notre scénario.

    ![Carte d’espace adresse](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure2.png)

4. Cliquez sur **Groupe de ressources** et sélectionnez un groupe de ressources pour ajouter la VNet à ou **créer un groupe de ressources** pour ajouter la VNet à un groupe de ressources. L’illustration suivante montre la ressource paramètres de groupe pour un nouveau groupe de ressources appelé **TestRG**. Pour plus d’informations sur les groupes de ressources, consultez [Vue d’ensemble du Gestionnaire de ressources Azure](../articles/virtual-network/resource-group-overview.md#resource-groups).

    ![Créer la carte de groupe de ressources](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure3.png)

5. Si nécessaire, modifiez les paramètres **d’abonnement** et un **emplacement** pour votre VNet. 

6. Si vous ne souhaitez pas voir le VNet comme une vignette dans la **Startboard**, désactivez **attacher aux Startboard**. 

7. Cliquez sur **créer** et notez la vignette appelée **Création virtuel réseau** comme indiqué dans l’illustration ci-dessous.

    ![Créer des VNet portail](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure4.png)

8. Attendre la VNet à créer et lorsque vous voyez la vignette ci-dessous, cliquez dessus pour ajouter plusieurs sous-réseaux.

    ![Créer des VNet portail](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure5.png)

9. Vous devriez voir la **Configuration** pour votre VNet comme illustré ci-dessous. 

    ![Créer des VNet portail](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure6.png)

10. Cliquez sur **sous-réseaux** > **Ajouter**, puis tapez un **nom** et spécifier une **plage d’adresses (bloc CIDR)** pour votre sous-réseau, puis cliquez sur **OK**. L’illustration suivante montre les paramètres de notre scénario en cours.

    ![Créer des VNet portail Azure](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure7.gif)