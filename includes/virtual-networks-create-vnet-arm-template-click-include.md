## <a name="deploy-the-arm-template-by-using-click-to-deploy"></a>Déployez le modèle de processeur en déployant à l’aide de clic

Vous pouvez réutiliser le téléchargement de modèles prédéfini processeur à un référentiel github géré par Microsoft et ouvrez vers la Communauté. Ces modèles peuvent être déployés directement déconnecter github, ou téléchargés, modifiés pour répondre à vos besoins. Pour déployer un modèle qui crée un VNet avec deux sous-réseaux, suivez les étapes ci-dessous.

1. Dans un navigateur, accédez à [https://github.com/Azure/azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates).
2. Faites défiler la liste des modèles, puis cliquez sur **101 vnet deux sous-réseaux**. Vérifiez le fichier **README.md** , comme illustré ci-dessous.

    ![Fichier READEME.md dans github](./media/virtual-networks-create-vnet-arm-template-click-include/figure1.png)

3. Cliquez sur **déployer sur Azure**. Si nécessaire, entrez vos informations d’identification Azure. 
4. Dans la carte de **paramètres** , entrez les valeurs que vous voulez utiliser pour créer votre VNet nouveau, puis cliquez sur **OK**. L’illustration suivante affiche les valeurs pour notre scénario.

    ![Paramètres de modèle processeur](./media/virtual-networks-create-vnet-arm-template-click-include/figure2.png)

4. Cliquez sur **groupe de ressources** et sélectionnez un groupe de ressources pour ajouter la VNet à ou cliquez sur **Créer nouveau** pour ajouter la VNet à un groupe de ressources. L’illustration suivante montre la ressource paramètres de groupe pour un nouveau groupe de ressources appelé **TestRG**.

    ![Groupe de ressources](./media/virtual-networks-create-vnet-arm-template-click-include/figure3.png)

5. Si nécessaire, modifiez les paramètres **d’abonnement** et un **emplacement** pour votre VNet.
6. Si vous ne souhaitez pas voir le VNet comme une vignette dans la **Startboard**, désactivez **attacher aux Startboard**.
5. Cliquez sur **des termes Leagl**, lisez le termes du contrat, puis cliquez sur **acheter** pour accepter. 
6. Cliquez sur **créer** pour créer la VNet.

    ![Envoi mosaïque de déploiement dans le portail preview](./media/virtual-networks-create-vnet-arm-template-click-include/figure4.png)

7. Une fois que le déploiement est terminé, cliquez sur **TestVNet** > **tous les paramètres** > **sous-réseaux** pour voir les propriétés sous-réseau, comme illustré ci-dessous.

    ![Créer des VNet portail preview](./media/virtual-networks-create-vnet-arm-template-click-include/figure5.gif)