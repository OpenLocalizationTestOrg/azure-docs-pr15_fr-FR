1. Dans le portail, à partir de **toutes les ressources**, cliquez sur **+ Ajouter**. Dans la **tout** carte zone de recherche, tapez **passerelle réseau Local**, puis cliquez sur pour effectuer une recherche. Cela renverra une liste. Cliquez sur **passerelle réseau Local** pour ouvrir la carte, puis cliquez sur **créer** pour ouvrir la carte de **passerelle du réseau local créer** .

    ![créer la passerelle réseau local](./media/vpn-gateway-add-lng-rm-portal-include/addlng250.png)

2. Sur la **carte de passerelle créer réseau local**, attribuez un **nom** à votre objet passerelle réseau local.
 
3. Spécifiez une **adresse IP** publique valide pour le périphérique VPN ou passerelle réseau virtuel auquel vous voulez vous connecter.<br>Si ce réseau local représente un emplacement local, il s’agit de l’adresse IP du périphérique VPN que vous voulez vous connecter. Il ne peut pas être derrière NAT et doit être accessible par Azure.<br>Si ce réseau local représente un autre VNet, vous spécifierez l’adresse IP qui a été affectée à la passerelle réseau virtuel pour cette VNet.<br>

4. **Espace d’adressage** fait référence aux plages d’adresses réseau correspondant à ce réseau local. Vous pouvez ajouter plusieurs plages d’adresses d’espace. Vérifiez que les plages que vous spécifiez ici ne se chevauchent pas avec les plages d’autres réseaux que vous voulez vous connecter.
 
5. Pour l' **abonnement**, vérifiez que l’abonnement approprié s’affiche.

6. **Groupe de ressources**, sélectionnez le groupe de ressources que vous voulez utiliser. Vous pouvez créer un nouveau groupe de ressources, ou sélectionnez une que vous avez déjà créé.

7. Pour un **emplacement**, sélectionnez l’emplacement où cet objet est créé dans. Vous souhaiterez peut-être sélectionner l’emplacement résidant dans votre VNet, mais vous n’êtes pas obligé de le faire.

8. Cliquez sur **créer** pour créer la passerelle réseau local.
