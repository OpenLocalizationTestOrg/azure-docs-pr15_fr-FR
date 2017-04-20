<properties 
   pageTitle="Configurer une connexion de passerelle Point-à-Site VPN au réseau virtuel en utilisant le modèle de déploiement du Gestionnaire de ressources et le portail Azure | Microsoft Azure"
   description="Connecter de façon sécurisée à votre réseau virtuel Azure en créant une connexion de passerelle VPN Point-à-Site à l’aide du Gestionnaire de ressources et le portail Azure."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/17/2016"
   ms.author="cherylmc" />

# <a name="configure-a-point-to-site-connection-to-a-vnet-using-the-azure-portal"></a>Configurer une connexion Point-à-Site à un VNet à l’aide du portail Azure

> [AZURE.SELECTOR]
- [Responsable de ressources - portail Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
- [Responsable de ressources - PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
- [Classique - portail Azure](vpn-gateway-howto-point-to-site-classic-azure-portal.md)

Configuration d’un Point de Site (P2S) vous permet de créer une connexion sécurisée à partir d’un ordinateur client à un réseau virtuel. Une connexion P2S est utile lorsque vous voulez vous connecter à votre VNet depuis un emplacement distant, tels que d’accueil ou d’une conférence, ou lorsque vous n’avez que quelques clients qui doivent se connecter à un réseau virtuel. 

Connexions point-à-Site ne nécessitent pas un périphérique VPN ou une adresse IP au public pour l’utiliser. Une connexion VPN est établie en démarrant la connexion à partir de l’ordinateur client. Pour plus d’informations sur les connexions Point-à-Site, voir le [Forum aux questions de la passerelle VPN](vpn-gateway-vpn-faq.md#point-to-site-connections) et la [planification et conception](vpn-gateway-plan-design.md).

Cet article vous guide dans la création d’un VNet avec une connexion Point-à-Site dans le modèle de déploiement Gestionnaire de ressources à l’aide du portail Azure.

### <a name="deployment-models-and-methods-for-p2s-connections"></a>Modèles de déploiement et méthodes pour les connexions P2S

[AZURE.INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)] 

Le tableau suivant montre les deux modèles de déploiement et les méthodes de déploiement disponibles pour les configurations P2S. Lorsqu’un article avec des étapes de configuration est disponible, nous attacher directement à partir de ce tableau.

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-table-point-to-site-include.md)] 

## <a name="basic-workflow"></a>Flux de travail 

![Point-à-Site-diagramme] (./media/vpn-gateway-howto-point-to-site-rm-ps/p2srm.png "point-à-site")

### <a name="example"></a>Exemples de valeurs

- **Nom : VNet1**
- **Espace d’adressage : 192.168.0.0/16**<br>Dans cet exemple, nous utilisons uniquement un espace d’adressage. Vous pouvez avoir plusieurs espace d’adressage pour votre VNet.
- **Nom du sous-réseau : FrontEnd**
- **Plage d’adresses sous-réseau : 192.168.1.0/24**
- **Abonnement :** Si vous avez plusieurs abonnements, vérifiez que vous utilisez celui qui convient.
- **Groupe de ressources : TestRG**
- **Emplacement : États-Unis l'**
- **GatewaySubnet : 192.168.200.0/24**
- **Nom de la passerelle réseau virtuel : VNet1GW**
- **Type de passerelle : VPN**
- **Type de VPN : basée sur l’itinéraire**
- **Adresse IP publique : VNet1GWpip**
- **Type de connexion : Point-à-site**
- **Pool d’adresses client : 172.16.201.0/24**<br>Clients VPN qui se connectent à la VNet à l’aide de cette connexion Point-à-Site reçoivent une adresse IP du client pool d’adresses.

## <a name="before-beginning"></a>Avant de commencer

- Vérifiez que vous avez un abonnement Azure. Si vous n’avez pas un abonnement Azure, vous pouvez activer votre [avantages d’abonné MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrivez-vous vers le haut pour un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/).
    
## <a name="createvnet"></a>Partie 1 : créer un réseau virtuel

Si vous créez cette configuration en guise d’exercice, vous pouvez faire référence aux [valeurs d’exemple](#example).

[AZURE.INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]  

### <a name="2-add-additional-address-space-and-subnets"></a>2. Ajoutez sous-réseaux et espace d’adressage supplémentaires

Vous pouvez ajouter des sous-réseaux et espace d’adressage supplémentaires à votre VNet une fois qu’il a été créé.

[AZURE.INCLUDE [vpn-gateway-additional-address-space](../../includes/vpn-gateway-additional-address-space-include.md)] 

### <a name="3-create-a-gateway-subnet"></a>3. créer un sous-réseau passerelle

Avant de vous connecter à votre réseau virtuel à une passerelle, vous devez tout d’abord créer le sous-réseau passerelle pour le réseau virtuel auquel vous voulez vous connecter. Si possible, il est conseillé de créer un sous-réseau passerelle à l’aide d’un bloc CIDR de /28 ou /27 afin de fournir suffisamment d’adresses IP pour répondre aux exigences de configuration futures supplémentaires.

Les captures d’écran dans cette section sont fournies comme un exemple de référence. Veillez à utiliser la plage d’adresses GatewaySubnet qui correspond avec les valeurs requises pour votre configuration.

#### <a name="to-create-a-gateway-subnet"></a>Pour créer un sous-réseau passerelle

[AZURE.INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

### <a name="dns"></a>4. Spécifiez un serveur DNS (facultatif)

[AZURE.INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="creategw"></a>Partie 2 : créer une passerelle réseau virtuel

Les connexions point-à-site requièrent les paramètres suivants :

- Type de passerelle : VPN
- Type de VPN : basée sur l’itinéraire

### <a name="to-create-a-virtual-network-gateway"></a>Pour créer une passerelle réseau virtuel

[AZURE.INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="generatecert"></a>Partie 3 - générer des certificats

Certificats sont utilisés par Azure pour authentifier les clients VPN pour Point-à-Site VPN. Vous exportez des données de certificat public (pas la clé privée) en tant que Base-64 codé X.509 .cer fichier à partir d’un certificat racine générés par une solution certificat d’entreprise, ou un certificat auto-signé. Vous ensuite importez les données du certificat public à partir du certificat racine vers Azure. En outre, vous devez générer un certificat client à partir du certificat racine pour les clients. Chaque client qui souhaite se connecter au réseau virtuel à l’aide d’une connexion P2S doit avoir un certificat client installé qui a été généré à partir du certificat racine.

### <a name="getcer"></a>1. Procurez-vous le fichier .cer pour le certificat racine

Si vous utilisez une solution d’entreprise, vous pouvez utiliser votre chaîne du certificat existant. Si vous n’utilisez pas une solution autorité de certification d’entreprise, vous pouvez créer un certificat auto-signé racine. Méthode de création d’un certificat auto-signé est makecert.

- Si vous utilisez un système de certificat d’entreprise, procurez-vous le fichier .cer pour le certificat racine que vous voulez utiliser. 

- Si vous n’utilisez pas une solution certificat d’entreprise, vous devez créer un certificat auto-signé. Pour obtenir la procédure Windows 10, vous pouvez faire référence à [l’utilisation des certificats racine auto-signé configurations Point-à-Site](vpn-gateway-certificates-point-to-site.md).

1. Pour obtenir un fichier .cer à partir d’un certificat, ouvrez **certmgr.msc** et recherchez le certificat racine. Cliquez sur le certificat auto-signé racine et cliquez sur **toutes les tâches**, puis cliquez sur **Exporter**. Cette action ouvre l' **Assistant Exportation de certificat**.

2. Dans l’Assistant, cliquez sur **suivant**, sélectionnez **non, ne pas exporter la clé privée**, puis sur **suivant**.

3. Dans la page **Format de fichier d’exportation** , sélectionnez **Base 64 codé X.509 (. Limitée).** Ensuite, cliquez sur **suivant**. 

4. Sur le **fichier à exporter**, **accédez** à l’emplacement auquel vous voulez exporter le certificat. Pour **nom de fichier**, nommez le fichier de certificat. Cliquez sur **suivant**.

5. Cliquez sur **Terminer** pour exporter le certificat.

### <a name="generateclientcert"></a>2. générer un certificat client

Vous pouvez soit générer un certificat unique pour chaque client qui va se connecter ou vous pouvez utiliser le même certificat sur plusieurs clients. La génération de certificats client unique a la possibilité de révoquer un certificat unique si nécessaire. Dans le cas contraire, si tout le monde utilise le même certificat client et que vous deviez révoquer le certificat pour un client, vous devrez générer et installer de nouveaux certificats pour tous les clients qui utilisent ce certificat s’authentifier.

- Si vous utilisez une solution certificat d’entreprise, générer un certificat client avec le format de valeur nom courant 'name@yourdomain.com', plutôt que la mise en forme « DOMAINE\nom d’utilisateur ». 

- Si vous utilisez un certificat auto-signé, voir [utilisation des certificats racine auto-signé configurations Point-à-Site](vpn-gateway-certificates-point-to-site.md) pour générer un certificat client.

### <a name="exportclientcert"></a>3. exporter le certificat client

Un certificat client est requis pour l’authentification. Après avoir généré le certificat client, exporter. Le certificat client que vous exportez sera plus tard être installé sur chaque ordinateur client.

1. Pour exporter un certificat client, vous pouvez utiliser *certmgr.msc*. Clic droit sur le certificat client que vous voulez exporter, cliquez sur **toutes les tâches**, puis cliquez sur **Exporter**.
2. Exportez le certificat client avec la clé privée. Il s’agit d’un fichier *.pfx* . Veillez à enregistrer ou de mémoriser le mot de passe (clé) que vous avez défini pour ce certificat.

## <a name="addresspool"></a>Partie 4 - ajouter le pool d’adresses client

1. Une fois que la passerelle réseau virtuel a été créée, accédez à la section **paramètres** de la cuillère de passerelle réseau virtuel. Dans la section **paramètres** , cliquez sur configuration **Point-à-site** pour ouvrir la carte de **Configuration** .

    ![pointez sur la carte de site] (./media/vpn-gateway-howto-point-to-site-resource-manager-portal/configuration.png "pointez sur la carte de site")

2. **Pool d’adresses** est le pool d’adresses IP à partir de laquelle les clients qui se connectent recevront une adresse IP. Ajouter du pool d’adresses, puis cliquez sur **Enregistrer**.

    ![pool d’adresses client] (./media/vpn-gateway-howto-point-to-site-resource-manager-portal/addresspool.png "pool d’adresses client")

## <a name="uploadfile"></a>Partie 5 - télécharger le fichier .cer de certificat racine

Une fois que la passerelle a été créée, vous pouvez télécharger le fichier .cer pour un certificat racine de confiance sur Azure. Vous pouvez télécharger des fichiers pour jusqu'à 20 certificats racine. Vous ne pas Téléchargez la clé privée pour le certificat racine vers Azure. Une fois le fichier .cer est téléchargé, Azure utilise pour authentifier les clients qui se connectent au réseau virtuel.

1. Accédez à la configuration **Point-à-site** carte. Vous allez ajouter les fichiers .cer dans la section de **certificat racine** de cette carte.

    ![pointez sur la carte de site] (./media/vpn-gateway-howto-point-to-site-resource-manager-portal/rootcert.png "pointez sur la carte de site")

2. Vérifiez que vous avez exporté le certificat racine en tant que Base-64 codé fichier X.509 (.cer). Vous devez exporter au format suivant afin que vous puissiez ouvrir le certificat avec l’éditeur de texte.
3. Ouvrez le certificat avec un éditeur de texte, tel que le bloc-notes. Copier uniquement la section suivante :

    ![données de certificat] (./media/vpn-gateway-howto-point-to-site-resource-manager-portal/copycert.png "données de certificat")

4. Coller les données de certificat dans la section **Des données publiques certificat** du portail. Placez le nom du certificat dans l’espace de **noms** , puis cliquez sur **Enregistrer**. Vous pouvez ajouter jusqu'à 20 certificats racine de confiance.

    ![télécharger des certificats] (./media/vpn-gateway-howto-point-to-site-resource-manager-portal/uploadcert.png "télécharger des certificats")

## <a name="clientconfig"></a>Partie 6 : Téléchargez et installez le module de configuration de client VPN

Clients qui se connectent à Azure à l’aide de P2S doivent avoir un certificat client et un package de configuration du client VPN installé. Les packages de configuration de clients VPN sont disponibles pour les clients Windows. 

Le package de client VPN contient des informations permettant de configurer le logiciel client VPN intégré à Windows. La configuration est spécifique au VPN que vous voulez vous connecter. Le package n’installe pas de logiciel supplémentaire. Consultez le [Forum aux questions de la passerelle VPN](vpn-gateway-vpn-faq.md#point-to-site-connections) pour plus d’informations.

1. Sur la configuration **Point-à-site** carte, cliquez sur le **client VPN télécharger** pour ouvrir la carte de **client VPN télécharger** .

    ![Télécharger client VPN] (./media/vpn-gateway-howto-point-to-site-resource-manager-portal/downloadclient.png "Télécharger client VPN")

2. Sélectionnez le lot approprié pour votre client, puis cliquez sur **Télécharger**. Pour les clients 64 bits, sélectionnez **AMD64**. Pour les clients 32 bits, sélectionnez **x86**.

3. Installez le package sur l’ordinateur client. Si vous obtenez une fenêtre indépendante SmartScreen, cliquez sur **plus d’informations**, puis **malgré tout exécuter** pour installer le package.

4. Sur l’ordinateur client, accédez aux **Paramètres du réseau** et cliquez sur **VPN**. Vous verrez la connexion répertoriée. Il affiche le nom du réseau virtuel il se connecte à et recherche similaire à cet exemple : 

    ![Client VPN] (./media/vpn-gateway-howto-point-to-site-resource-manager-portal/vpn.png "Client VPN")

## <a name="installclientcert"></a>Partie 7 - installer le certificat client

Chaque ordinateur client doit disposer d’un certificat client afin de s’authentifier. Lorsque vous installez le certificat client, vous devez le mot de passe qui a été créé lorsque le certificat client ont été exporté.

1. Copiez le fichier .pfx sur l’ordinateur client.
2. Double-cliquez sur le fichier .pfx pour l’installer. Ne modifiez pas l’emplacement d’installation.

## <a name="connect"></a>Partie 8 : se connecter à Azure

1. Pour vous connecter à votre VNet, sur l’ordinateur client, accédez à connexions VPN, puis recherchez la connexion VPN que vous avez créé. Il porte le même nom que votre réseau virtuel. Cliquez sur **se connecter**. Affichage d’un message peut s’afficher qui fait référence à l’aide du certificat. Dans ce cas, cliquez sur **Continuer** pour utiliser des privilèges élevés. 

2. Dans la page d’état de **connexion** , cliquez sur **se connecter** pour démarrer la connexion. Si vous voyez un écran **Sélectionner un certificat** , vérifiez que le certificat client affichant est celle que vous souhaitez utiliser pour vous connecter. Si elle n’est pas le cas, utilisez la flèche déroulante pour sélectionner le certificat approprié, puis cliquez sur **OK**.

    ![Client VPN 2] (./media/vpn-gateway-howto-point-to-site-rm-ps/clientconnect.png "Connexion du client VPN")

3. Votre connexion doit maintenant être établie.

    ![Client VPN 3] (./media/vpn-gateway-howto-point-to-site-rm-ps/connected.png "Connexion du client VPN 2")

## <a name="verify"></a>Partie 9 - Vérifiez votre connexion

1. Pour vérifier que votre connexion VPN est active, ouvrez une invite de commandes avec élévation de privilèges et exécutez *ipconfig/all*.

2. Afficher les résultats. Notez que l’adresse IP que vous avez reçu est une des adresses au sein du Pool adresse Client VPN Point-à-Site que vous avez spécifié dans votre configuration. Les résultats doivent être doit ressembler à ceci :
    
        PPP adapter VNet1:
            Connection-specific DNS Suffix .:
            Description.....................: VNet1
            Physical Address................:
            DHCP Enabled....................: No
            Autoconfiguration Enabled.......: Yes
            IPv4 Address....................: 172.16.201.3(Preferred)
            Subnet Mask.....................: 255.255.255.255
            Default Gateway.................:
            NetBIOS over Tcpip..............: Enabled

## <a name="add"></a>Pour ajouter ou supprimer des certificats racine de confiance

Vous pouvez supprimer le certificat racine de confiance à partir d’Azure. Lorsque vous supprimez un certificat de confiance, les certificats de client qui ont été générés à partir du certificat racine n’est plus pourront pas se connecter à Azure via Point-à-Site. Si vous voulez que les clients pour vous connecter, dont elles ont besoin installer un nouveau certificat client qui est généré à partir d’un certificat approuvé dans Azure.

Vous pouvez gérer la liste des certificats client révoqué sur la configuration **Point-à-site** carte. Il s’agit de la carte que vous avez utilisé pour [télécharger un certificat racine de confiance](#uploadfile).

## <a name="revokeclient"></a>Pour gérer la liste des certificats client révoqué

Vous pouvez révoquer les certificats clients. La liste de révocation de certificats permet de refuser sélectivement connectivité Point-à-Site basée sur des certificats clients individuels. Si vous supprimez un .cer de certificat racine d’Azure, il retire le niveau d’accès pour tous les certificats client généré/signé par le certificat racine révoqué. Si vous souhaitez révoquer un certificat client particulier, pas la racine, vous pouvez le faire. Ainsi les autres certificats qui ont été générés à partir du certificat racine seront toujours valable. 

Le principe consiste à utiliser le certificat racine pour gérer l’accès à des niveaux d’équipe ou de l’organisation, lors de l’utilisation de certificats client révoqué pour contrôler l’accès permissions sur les utilisateurs individuels.

Vous pouvez gérer la liste des certificats client révoqué sur la configuration **Point-à-site** carte. Il s’agit de la carte que vous avez utilisé pour [télécharger un certificat racine de confiance](#uploadfile).


## <a name="next-steps"></a>Étapes suivantes

Vous pouvez ajouter une machine virtuelle à votre réseau virtuel. Pour obtenir la procédure, voir [créer une Machine virtuelle](../virtual-machines/virtual-machines-windows-hero-tutorial.md) .