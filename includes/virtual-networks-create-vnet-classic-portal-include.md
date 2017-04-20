## <a name="how-to-create-a-vnet-in-the-azure-portal"></a>Comment créer un VNet dans le portail Azure

Pour créer un VNet en fonction du scénario ci-dessus, suivez les étapes ci-dessous.

1. Dans un navigateur, accédez à http://manage.windowsazure.com et, si nécessaire, connectez-vous avec votre compte Azure.
2. Cliquez sur **Nouveau** > **SERVICES de réseau** > **Réseau virtuel** > **Personnalisé créer** comme indiqué dans l’illustration ci-dessous.

    ![Créer des VNet portail](./media/virtual-networks-create-vnet-classic-portal-include/vnet-create-portal-figure1.gif)

3. Dans la page **Détails du réseau virtuel** , tapez le **nom** de la VNet et sélectionnez son **emplacement**, puis cliquez sur la flèche dans le coin inférieur droit de la page pour passer à l’étape 2. L’illustration suivante montre les paramètres pour notre scénario.

    ![Page de détails de réseau virtuel](./media/virtual-networks-create-vnet-classic-portal-include/vnet-create-portal-figure2.png)

4. Dans la page **DNS Servers et connectivité VPN** , spécifiez le nom et l’adresse IP pour jusqu'à 9 serveurs DNS à utiliser. Si vous ne spécifiez pas un serveur DNS, votre VNet utilise la résolution résolution d’appellation interne fournie par Azure. Pour notre scénario, nous n'allons pas configurer des serveurs DNS.
5. Si vous voulez fournir un point-à-site VPN accès à votre VNet, activez la case à cocher **configurer un réseau privé virtuel point-à-site** . Si vous ne configurez pas un réseau privé virtuel point-à-site, vous pouvez l’ajouter à votre VNet à tout moment après sa création. Pour notre scénario, nous n'allons pas configurer un réseau privé virtuel point-à-site.
6. Si vous voulez fournir une connectivité VPN de site à entre votre VNet et un autre VNet ou votre réseau local, activez la case à cocher **configurer un réseau privé virtuel de site à** et spécifiez si vous souhaitez utiliser **ExpressRoute** ou une note et le nom du réseau pour vous connecter à. Si vous ne configurez pas un réseau VPN site à site, vous pouvez l’ajouter à votre VNet à tout moment après sa création. Pour notre scénario, nous n'allons pas configurer un réseau privé virtuel de site à.
7. Cliquez sur la flèche dans le coin inférieur droit de la page pour passer à l’étape 3 que l’illustration suivante présente les paramètres de notre scénario.

    ![Page de connectivité VPN et les serveurs DNS](./media/virtual-networks-create-vnet-classic-portal-include/vnet-create-portal-figure3.png)

8. Dans la page **Des espaces d’adressage réseau virtuel** , sous **IP de début**, cliquez sur *10.0.0.0* pour modifier l’espace d’adressage VNet et tapez l’espace d’adressage départ que vous souhaitez utiliser. Pour notre scénario, tapez *192.168.0.0*. 
9. Sous **CIDR (nombre d’adresses)** , sélectionnez le nombre de bits pour le masque. Pour notre scénario, sélectionnez *16 (65536)*.
10. Sous **sous-réseaux**, cliquez sur *sous-réseau 1* et renommez le sous-réseau si nécessaire. Pour notre scénario, renommez-le *FrontEnd*.

    >[AZURE.NOTE] Si vous cliquez en dehors de la zone de texte Nom d’un sous-réseau, vous ne pourrez pas modifier le nom si le sous-réseau à nouveau. Pour corriger cette erreur, vous devez supprimer le sous-réseau en cliquant sur le bouton X situé à sa droite, puis ajouter un nouveau sous-réseau comme décrit à l’étape 13 ci-dessous.

11. Sous **IP de début** pour le premier sous-réseau, spécifiez l’adresse IP de départ pour le sous-réseau. Pour notre scénario, tapez *192.168.1.0*.
12. Sous **CIDR (nombre d’adresses)** , sélectionnez le nombre de bits pour le masque le premier sous-réseau. Pour notre scénario, sélectionnez *24 (256)*.
13. Cliquez sur **Ajouter sous-réseau** pour ajouter un nouveau sous-réseau, si nécessaire. Dans notre scénario, ajouter un sous-réseau et répétez les étapes 10 à 12 pour configurer le VNet comme le montre l’illustration suivante.

    ![Page d’espaces réseau virtuel adresse](./media/virtual-networks-create-vnet-classic-portal-include/vnet-create-portal-figure4.png)

14. Cliquez sur le bouton de coche dans le coin inférieur droit de la page pour créer la VNet. Après quelques secondes votre VNet est affiché dans la liste des VNets disponibles, comme le montre l’illustration suivante.

    ![Réseau virtuel](./media/virtual-networks-create-vnet-classic-portal-include/vnet-create-portal-figure5.png)