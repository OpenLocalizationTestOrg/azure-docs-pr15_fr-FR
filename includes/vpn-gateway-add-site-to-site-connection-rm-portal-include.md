1. Recherchez votre passerelle réseau virtuel et cliquez sur **tous les paramètres** pour ouvrir la carte de **paramètres** .

2. Sur la carte de **paramètres** , cliquez sur **connexions**, puis cliquez sur **Ajouter** en haut de la carte pour ouvrir la carte de **Ajouter une connexion** .

    ![Créer la connexion à un Site](./media/vpn-gateway-add-site-to-site-connection-rm-portal-include/addconnection250.png)

3. Sur la carte **d’Ajouter une connexion** , le **nom de** votre connexion. 

4. Pour **type de connexion**, sélectionnez **Site-to-site(IPSec)**.

5. Pour la **passerelle réseau virtuelle**, la valeur est fixe, car vous vous connectez à partir de cette passerelle.

6. Pour la **passerelle du réseau Local**, cliquez sur **Choisir une passerelle réseau local** et sélectionnez la passerelle réseau local que vous voulez utiliser. 

7. **Clé partagée**, la valeur ici doit correspondre à la valeur que vous utilisez pour votre périphérique VPN local. Si votre appareil VPN sur votre réseau local ne fournit une clé partagée, vous pouvez en créer un et saisir ici et sur votre appareil local. Il est important que les deux correspondent.

8. Les valeurs restantes pour **l’abonnement**, **Groupe de ressources**et **l’emplacement** sont résolus.

9. Cliquez sur **OK** pour créer votre connexion. Vous verrez *Création d’une connexion* flash dans l’écran.

10. Lorsque la connexion est terminée, vous le verrez apparaître dans la carte de **connexions** de la passerelle.

    ![Créer la connexion à un Site](./media/vpn-gateway-add-site-to-site-connection-rm-portal-include/connectionstatus450.png)

