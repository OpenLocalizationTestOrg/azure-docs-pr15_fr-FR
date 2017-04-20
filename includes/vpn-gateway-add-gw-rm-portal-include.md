1. Dans le portail, accédez à **Nouveau**. Tapez « Passerelle réseau virtuel » dans Rechercher. Recherchez **passerelle réseau virtuelle** dans la recherche retour et cliquez sur l’entrée. Cette action ouvre la carte de **passerelle réseau virtuel de créer** .
2. Cliquez sur **créer** en bas de la **passerelle réseau virtuelle** cuillère. La carte de la **passerelle de réseau virtuel créer** s’ouvre. Renseignez les valeurs de la passerelle réseau virtuel.

    ![Créer les champs de carte réseau virtuel passerelle] (./media/vpn-gateway-add-gw-rm-portal-include/createvnetgw300.png "Créer les champs de carte réseau virtuel passerelle")

3. **Nom**: nom de la passerelle. Il s’agit pas identique à la façon de nommer un sous-réseau passerelle. Il s’agit du nom de l’objet passerelle que vous créez.

4. **Type de passerelle**: sélectionnez **VPN**. Les passerelles VPN utilisent le type de passerelle réseau virtuel **VPN**. 

5. **Type de VPN**: sélectionnez le type VPN est spécifié pour votre configuration. La plupart des configurations requièrent un type VPN basée sur un itinéraire.

6. **Référence (SKU)**: sélectionnez la référence (SKU) de la passerelle dans le menu déroulant. Les références SKU répertoriés dans la liste déroulante dépendent du type de VPN sélectionné.

7. **Emplacement**: ajuster le champ **emplacement** pour qu’il pointe vers l’emplacement où se trouve votre réseau virtuel.
 
8. Choisissez le réseau virtuel auquel vous voulez ajouter cette passerelle. Cliquez sur **réseau virtuel** pour ouvrir la carte de **Choisir un réseau virtuel** . Sélectionnez le VNet. Si vous ne voyez pas votre VNet, vérifiez que le champ **emplacement** pointe vers la région dans lequel se trouve votre réseau virtuel.

9. Sélectionnez une adresse IP publique. Cliquez sur **adresse IP Public** pour ouvrir la carte de **Choisir les adresses IP public** . Cliquez sur **+ créer** pour ouvrir la **créer public carte d’adresse IP**. Entrez un nom pour votre adresse IP publique. Cette carte crée un objet d’adresses IP public à laquelle une adresse IP publique doivent être affectée dynamique.<br>Cliquez sur **OK** pour enregistrer vos modifications sur cette carte.

10. **Abonnement**: Vérifiez que l’abonnement correcte est sélectionnée.

11. **Groupe de ressources**: ce paramètre est déterminé par le réseau virtuel que vous sélectionnez. 

12. Ne pas ajuster l' **emplacement** une fois que vous avez spécifié les paramètres précédents.

13. Vérifiez les paramètres. Vous pouvez sélectionner **code confidentiel au tableau de bord** dans la partie inférieure de la cuillère si vous souhaitez que votre passerelle apparaisse dans le tableau de bord.

14. Cliquez sur **créer** pour commencer la création de la passerelle. Les paramètres sont validées et vous verrez la « passerelle réseau virtuelle déploiement » de mosaïque sur le tableau de bord. Création d’une passerelle peut prendre jusqu'à 45 minutes. Vous devrez peut-être actualiser la page de votre portail pour afficher le statut terminé.

    ![Déploiement virtuel passerelle réseau] (./media/vpn-gateway-add-gw-rm-portal-include/deployvnetgw150.png "Déploiement virtuel passerelle réseau")

11. Une fois la passerelle est créée, vous pouvez afficher l’adresse IP qui vous a été affectée en consultant le réseau virtuel dans le portail. La passerelle s’affichent sous forme d’un appareil connecté. Vous pouvez cliquer sur le périphérique connecté (votre passerelle réseau virtuel) pour afficher plus d’informations.



