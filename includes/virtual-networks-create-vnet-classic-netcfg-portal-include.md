## <a name="how-to-create-a-vnet-using-a-network-config-file-in-the-azure-portal"></a>Comment créer un VNet à l’aide d’un fichier de configuration réseau dans le portail Azure

Azure utilise un fichier xml pour définir toutes les VNets disponibles à un abonnement. Vous pouvez télécharger ce fichier et modifier pour modifier ou supprimer des VNets existant et créer de nouveaux. Dans ce document, vous apprenez à télécharger ce fichier, appelé fichier de configuration (ou netcgf) réseau et modifiez-le pour créer un nouveau VNet. Vérifiez le [schéma de configuration du réseau virtuel Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx) pour en savoir plus sur le fichier de configuration réseau.

Pour créer un VNet à l’aide d’un fichier netcfg via le portail d’Azure, suivez les étapes ci-dessous.

1. Dans un navigateur, accédez à http://manage.windowsazure.com et, si nécessaire, connectez-vous avec votre compte Azure.
2. Faites défiler vers le bas dans la liste des services, puis cliquez sur les **réseaux** , comme indiqué ci-dessous.

    ![Réseaux virtuels Azure](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure1.gif)

3. Au bas de la page, cliquez sur le bouton **Exporter** , comme illustré ci-dessous.

    ![Bouton Exporter](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure2.png)

4. Dans la page **Exporter la configuration réseau** , sélectionnez l’abonnement que vous voulez exporter la configuration réseau virtuel à partir de, puis cliquez sur le bouton de coche dans le coin inférieur gauche de la page.
5. Suivez les instructions de votre navigateur pour enregistrer le fichier **NetworkConfig.xml** . Vérifiez que vous notez l’endroit où vous enregistrez le fichier.
6. Ouvrez le fichier que vous avez enregistré à l’étape 5 ci-dessus à l’aide de n’importe quelle application éditeur XML ou du texte, puis recherchez la **<VirtualNetworkSites>** élément. Si vous avez déjà créés tous les réseaux, chaque réseau s’affichera en tant que sa propre **<VirtualNetworkSite>** élément.
7. Pour créer le réseau virtuel décrit dans ce scénario, ajoutez le code XML suivant juste sous la **<VirtualNetworkSites>** élément :

        <VirtualNetworkSite name="TestVNet" Location="Central US">
          <AddressSpace>
            <AddressPrefix>192.168.0.0/16</AddressPrefix>
          </AddressSpace>
          <Subnets>
            <Subnet name="FrontEnd">
              <AddressPrefix>192.168.1.0/24</AddressPrefix>
            </Subnet>
            <Subnet name="BackEnd">
              <AddressPrefix>192.168.2.0/24</AddressPrefix>
            </Subnet>
          </Subnets>
        </VirtualNetworkSite>

8.  Enregistrez le fichier de configuration réseau.
9.  Dans le portail Azure, dans le coin inférieur gauche de la page, cliquez sur **Nouveau**, puis cliquez sur **SERVICES de réseau**, puis cliquez sur **Réseau virtuel**, puis cliquez sur **Importer la CONFIGURATION** comme indiqué dans l’illustration ci-dessous.

    ![Configuration de l’importation](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure3.gif)

10.  Dans la page **Importer le fichier de configuration réseau** , cliquez sur **Rechercher un fichier...**, puis naviguez vers le dossier que vous avez enregistré votre fichier à l’étape 8, sélectionnez le fichier, puis sur **Ouvrir**. La page web doit ressembler à l’illustration ci-dessous. Dans le coin inférieur droit de la page, cliquez sur la flèche pour passer à l’étape suivante.

    ![Page de fichier de configuration de réseau importer](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure4.png)

11.   Dans la page **créer votre réseau** , observez l’entrée pour votre nouvelle VNet, comme illustré dans l’illustration ci-dessous.

    ![Création de page de votre réseau](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure5.png)

12.   Cliquez sur le bouton de coche dans le coin inférieur droit de la page pour créer la VNet. Après quelques secondes votre VNet est affiché dans la liste des VNets disponibles, comme le montre l’illustration suivante.

    ![Réseau virtuel](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure6.png)