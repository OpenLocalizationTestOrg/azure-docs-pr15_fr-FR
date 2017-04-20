Pour créer un VNet dans le modèle de déploiement du Gestionnaire de ressources à l’aide du portail Azure, suivez les étapes ci-dessous. Les captures d’écran sont fournies comme exemples. N’oubliez pas de remplacer les valeurs par votre propre. Pour plus d’informations sur l’utilisation des réseaux virtuels, consultez la [Vue d’ensemble du réseau virtuel](../articles/virtual-network/virtual-networks-overview.md).

1. À partir d’un navigateur, accédez au [portail Azure](http://portal.azure.com) et, si nécessaire, connectez-vous avec votre compte Azure.

2. Cliquez sur **Nouveau**. Dans le champ de **recherche sur le marché** , tapez « Réseau virtuel ». Recherchez **Réseau virtuel** de la liste retournée et cliquez sur pour ouvrir la carte **Réseau virtuel** .

    ![Carte de ressource réseau virtuel localiser] (./media/vpn-gateway-basic-vnet-rm-portal-include/newvnetportal700.png "Carte de ressource réseau virtuel localiser")

3. Près du bas de la carte réseau virtuel, dans la liste **Sélectionner un modèle de déploiement** , sélectionnez **Le Gestionnaire de ressources**, puis cliquez sur **créer**.


    ![Sélectionnez le Gestionnaire de ressources] (./media/vpn-gateway-basic-vnet-rm-portal-include/resourcemanager250.png "Sélectionnez le Gestionnaire de ressources")

4. Sur la carte **réseau virtuel créer** , configurer les paramètres VNet. Lorsque vous renseignez les champs, le point d’exclamation rouge devient une coche verte lorsque les caractères entrés dans le champ sont valides.

    ![Validation de champ] (./media/vpn-gateway-basic-vnet-rm-portal-include/checkmark300.png "Validation de champ")

5. La carte **réseau virtuel créer** est semblable à l’exemple suivant. Il peut y avoir des valeurs qui sont remplis automatique. Si c’est le cas, remplacez les valeurs par votre propre.

    ![Carte de réseau virtuel créer] (./media/vpn-gateway-basic-vnet-rm-portal-include/createvnet300.png "Carte de réseau virtuel créer")

6. **Nom**: entrez le nom de votre réseau virtuel.

7. **Espace d’adressage**: entrez l’espace d’adressage. Si vous avez plusieurs espaces d’adresse à ajouter, ajouter votre premier espace d’adressage. Vous pouvez ajouter des espaces d’adressage supplémentaires ultérieurement, après avoir créé le VNet.
 
8. **Nom du sous-réseau**: ajouter le nom de sous-réseau et la plage d’adresses sous-réseau. Vous pouvez ajouter des sous-réseaux supplémentaires ultérieurement, après avoir créé le VNet.

10. **Abonnement**: Vérifiez que l’abonnement répertorié est correct. Vous pouvez modifier les abonnements à l’aide de la liste déroulante.

11. **Groupe de ressources**: sélectionnez un groupe de ressources existant ou créez-en une en tapant un nom pour votre nouveau groupe de ressources. Si vous créez un nouveau groupe, nommez le groupe de ressources en fonction des valeurs de votre configuration planifiée. Pour plus d’informations sur les groupes de ressources, consultez [Vue d’ensemble du Gestionnaire de ressources Azure](resource-group-overview.md#resource-groups).

12. **Emplacement**: sélectionnez l’emplacement de votre VNet. L’emplacement détermine où les ressources que vous déployez sur cette VNet résideront.

13. Sélectionnez **Ajouter au tableau de bord** si vous voulez être en mesure de trouver facilement votre VNet du tableau de bord, puis cliquez sur **créer**.
    
    ![Épingler au tableau de bord] (./media/vpn-gateway-basic-vnet-rm-portal-include/pintodashboard150.png "épingler au tableau de bord")

14. Après avoir cliqué sur **créer**, vous verrez une vignette dans votre tableau de bord qui reflète la progression de votre VNet. La vignette change à mesure que le VNet est créé.

    ![Vignette de réseau virtuel création] (./media/vpn-gateway-basic-vnet-rm-portal-include/deploying150.png "Vignette de réseau virtuel création")