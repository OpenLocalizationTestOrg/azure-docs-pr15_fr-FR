<properties 
   pageTitle="Configurer une connexion de passerelle Point-à-Site VPN au réseau virtuel à l’aide du modèle de déploiement Gestionnaire de ressources | Microsoft Azure"
   description="Connecter de façon sécurisée à votre réseau virtuel Azure en créant une connexion de passerelle VPN Point-à-Site."
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

# <a name="configure-a-point-to-site-connection-to-a-vnet-using-powershell"></a>Configurer une connexion Point-à-Site à un VNet à l’aide de PowerShell

> [AZURE.SELECTOR]
- [Responsable de ressources - portail Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
- [Responsable de ressources - PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
- [Classique - portail Azure](vpn-gateway-howto-point-to-site-classic-azure-portal.md)

Configuration d’un Point de Site (P2S) vous permet de créer une connexion sécurisée à partir d’un ordinateur client à un réseau virtuel. Une connexion P2S est utile lorsque vous voulez vous connecter à votre VNet depuis un emplacement distant, tels que d’accueil ou d’une conférence, ou lorsque vous n’avez que quelques clients qui doivent se connecter à un réseau virtuel. 

Connexions point-à-Site ne nécessitent pas un périphérique VPN ou une adresse IP au public pour l’utiliser. Une connexion VPN est établie en démarrant la connexion à partir de l’ordinateur client. Pour plus d’informations sur les connexions Point-à-Site, voir le [Forum aux questions de la passerelle VPN](vpn-gateway-vpn-faq.md#point-to-site-connections) et la [planification et conception](vpn-gateway-plan-design.md). 

Cet article vous guide dans la création d’un VNet avec une connexion Point-à-Site dans le modèle de déploiement Gestionnaire de ressources à l’aide de PowerShell.

### <a name="deployment-models-and-methods-for-p2s-connections"></a>Modèles de déploiement et méthodes pour les connexions P2S

[AZURE.INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)] 

Le tableau suivant montre les deux modèles de déploiement et les méthodes de déploiement disponibles pour les configurations P2S. Lorsqu’un article avec des étapes de configuration est disponible, nous attacher directement à partir de ce tableau.

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-table-point-to-site-include.md)] 

## <a name="basic-workflow"></a>Flux de travail 

![Point-à-Site-diagramme] (./media/vpn-gateway-howto-point-to-site-rm-ps/p2srm.png "point-à-site")

Dans ce scénario, vous allez créer un réseau virtuel avec une connexion Point-à-Site. Les instructions vous aidera également à générer des certificats, qui sont nécessaires pour cette configuration. Une connexion P2S se compose des éléments suivants : A VNet avec une passerelle VPN, un fichier .cer du certificat racine (clé publique), un certificat client et la configuration de VPN sur le client. 

Nous utilisons les valeurs suivantes pour cette configuration. Nous définissez les variables dans la section [1](#declare) de l’article. Suivez les étapes présentées sous la forme d’une vue d’ensemble et utilisez les valeurs sans les modifier ou les modifier afin de refléter votre environnement. 

### <a name="example"></a>Exemples de valeurs

- **Nom : VNet1**
- **Espace d’adressage : 192.168.0.0/16** et **10.254.0.0/16**<br>Dans cet exemple, nous utilisons plus d’un espace d’adressage pour illustrer que cette configuration fonctionnent avec plusieurs espaces d’adressage. Toutefois, plusieurs espaces d’adressage ne sont pas requis pour cette configuration.
- **Nom du sous-réseau : FrontEnd**
    - **Plage d’adresses sous-réseau : 192.168.1.0/24**
- **Nom du sous-réseau : principal**
    - **Plage d’adresses sous-réseau : 10.254.1.0/24**
- **Nom du sous-réseau : GatewaySubnet**<br>Le nom de sous-réseau *GatewaySubnet* est obligatoire pour la passerelle VPN pour l’utiliser.
    - **Plage d’adresses sous-réseau : 192.168.200.0/24** 
- **Pool d’adresses VPN client : 172.16.201.0/24**<br>Les clients VPN qui se connectent à la VNet à l’aide de cette connexion Point-à-Site reçoivent une adresse IP à partir du pool d’adresses client VPN.
- **Abonnement :** Si vous avez plusieurs abonnements, vérifiez que vous utilisez celui qui convient.
- **Groupe de ressources : TestRG**
- **Emplacement : États-Unis l'**
- **Serveur DNS : adresse IP** du serveur DNS que vous souhaitez utiliser pour la résolution de noms.
- **Nom GW : Vnet1GW**
- **Nom IP public : VNet1GWPIP**
- **VpnType : RouteBased**

## <a name="before-beginning"></a>Avant de commencer

- Vérifiez que vous avez un abonnement Azure. Si vous n’avez pas un abonnement Azure, vous pouvez activer votre [avantages d’abonné MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrivez-vous vers le haut pour un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/).
    
- Installer la dernière version des applets de commande PowerShell de gestionnaire de ressources Azure. Découvrez [comment installer et configurer Azure PowerShell](../powershell-install-configure.md) pour plus d’informations sur l’installation les applets de commande PowerShell. Lorsque vous travaillez avec PowerShell pour cette configuration, vérifiez que vous exécutez en tant qu’administrateur. 

## <a name="declare"></a>1E partie-journal dans et variables de jeu

Dans cette section, vous connectez et déclarez les valeurs utilisées pour cette configuration. Les valeurs déclarées sont utilisés dans les exemples de scripts. Modifiez les valeurs pour refléter votre environnement. Ou bien, vous pouvez utiliser les valeurs déclarées et suivez les étapes en guise d’exercice.

1. Dans la console PowerShell, connectez-vous à votre compte Azure. Cette applet de commande vous demande les informations d’identification pour votre compte Azure. Après la connexion, il télécharge vos paramètres de compte afin qu’ils soient disponibles à Azure PowerShell.

        Login-AzureRmAccount 

2. Obtenir une liste de vos abonnements Azure.

        Get-AzureRmSubscription

3. Spécifier l’abonnement que vous voulez utiliser. 

        Select-AzureRmSubscription -SubscriptionName "Name of subscription"

4. Déclarer les variables que vous voulez utiliser. Utiliser l’exemple suivant, en remplaçant les valeurs de votre propre lorsque cela est nécessaire.

        $VNetName  = "VNet1"
        $FESubName = "FrontEnd"
        $BESubName = "Backend"
        $GWSubName = "GatewaySubnet"
        $VNetPrefix1 = "192.168.0.0/16"
        $VNetPrefix2 = "10.254.0.0/16"
        $FESubPrefix = "192.168.1.0/24"
        $BESubPrefix = "10.254.1.0/24"
        $GWSubPrefix = "192.168.200.0/26"
        $VPNClientAddressPool = "172.16.201.0/24"
        $RG = "TestRG"
        $Location = "East US"
        $DNS = "8.8.8.8"
        $GWName = "VNet1GW"
        $GWIPName = "VNet1GWPIP"
        $GWIPconfName = "gwipconf"
        
## <a name="ConfigureVNet"></a>Partie 2 : configurer un VNet 

1. Créer un groupe de ressources.

        New-AzureRmResourceGroup -Name $RG -Location $Location

2. Créer les configurations sous-réseau pour le réseau virtuel, leur attribuant *FrontEnd* *principal*et *GatewaySubnet*. Ces préfixes doivent faire partie de l’espace d’adressage VNet que vous avez déclaré.

        $fesub = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName -AddressPrefix $FESubPrefix
        $besub = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName -AddressPrefix $BESubPrefix
        $gwsub = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName -AddressPrefix $GWSubPrefix

3. Créer le réseau virtuel. Le serveur DNS spécifié doit être un serveur DNS qui peut résoudre les noms pour les ressources que vous vous connectez à. Dans cet exemple, nous avons utilisé une adresse IP publique. Veillez à utiliser vos propres valeurs.
    
        New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG -Location $Location -AddressPrefix $VNetPrefix1,$VNetPrefix2 -Subnet $fesub, $besub, $gwsub -DnsServer $DNS

4. Spécifier les variables pour le réseau virtuel que vous avez créé.

        $vnet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG
        $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet

5. Demander une adresse IP publique affectée dynamiquement. Cette adresse IP est nécessaire pour la passerelle de fonctionner correctement. Vous connecterez la passerelle vers la configuration de la passerelle IP.
        
        $pip = New-AzureRmPublicIpAddress -Name $GWIPName -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
        $ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip

## <a name="Certificates"></a>3e partie-certificats

Certificats sont utilisés par Azure pour authentifier les clients VPN pour Point-à-Site VPN. Vous exportez des données de certificat public (pas la clé privée) en tant que Base-64 codé X.509 .cer fichier à partir d’un certificat racine générés par une solution certificat d’entreprise, ou un certificat auto-signé. Vous ensuite importez les données du certificat public à partir du certificat racine vers Azure. En outre, vous devez générer un certificat client à partir du certificat racine pour les clients. Chaque client qui souhaite se connecter au réseau virtuel à l’aide d’une connexion P2S doit avoir un certificat client installé qui a été généré à partir du certificat racine.

### <a name="cer"></a>1. Procurez-vous le fichier .cer pour le certificat racine

Vous avez besoin obtenir les données de certificat public pour le certificat racine que vous voulez utiliser.

- Si vous utilisez un système de certificat d’entreprise, procurez-vous le fichier .cer pour le certificat racine que vous voulez utiliser. 

- Si vous n’utilisez pas une solution certificat d’entreprise, vous devez créer un certificat auto-signé. Pour obtenir la procédure Windows 10, vous pouvez faire référence à [l’utilisation des certificats racine auto-signé configurations Point-à-Site](vpn-gateway-certificates-point-to-site.md).


1. Pour obtenir un fichier .cer à partir d’un certificat, ouvrez **certmgr.msc** et recherchez le certificat racine. Cliquez sur le certificat auto-signé racine et cliquez sur **toutes les tâches**, puis cliquez sur **Exporter**. Cette action ouvre l' **Assistant Exportation de certificat**.

2. Dans l’Assistant, cliquez sur **suivant**, sélectionnez **non, ne pas exporter la clé privée**, puis sur **suivant**.

3. Dans la page **Format de fichier d’exportation** , sélectionnez **Base 64 codé X.509 (. Limitée).** Ensuite, cliquez sur **suivant**. 

4. Sur le **fichier à exporter**, **accédez** à l’emplacement auquel vous voulez exporter le certificat. Pour **nom de fichier**, nommez le fichier de certificat. Cliquez sur **suivant**.

5. Cliquez sur **Terminer** pour exporter le certificat.

### <a name="generate"></a>2. générer le certificat client

Ensuite, générer les certificats clients. Vous pouvez soit générer un certificat unique pour chaque client qui va se connecter ou vous pouvez utiliser le même certificat sur plusieurs clients. La génération de certificats client unique a la possibilité de révoquer un certificat unique si nécessaire. Dans le cas contraire, si tout le monde utilise le même certificat client et que vous deviez révoquer le certificat pour un client, vous devrez générer et installer de nouveaux certificats pour tous les clients qui utilisent le certificat s’authentifier. Les certificats clients sont installés sur chaque ordinateur client plus loin dans cet exercice.

- Si vous utilisez une solution certificat d’entreprise, générer un certificat client avec le format de valeur nom courant 'name@yourdomain.com', plutôt que le format NetBIOS 'Domaine\nom_utilisateur'. 

- Si vous utilisez un certificat auto-signé, voir [utilisation des certificats racine auto-signé configurations Point-à-Site](vpn-gateway-certificates-point-to-site.md) pour générer un certificat client.

### <a name="exportclientcert"></a>3. exporter le certificat client

Un certificat client est requis pour l’authentification. Après avoir généré le certificat client, exporter. Le certificat client que vous exportez sera plus tard être installé sur chaque ordinateur client.

1. Pour exporter un certificat client, vous pouvez utiliser *certmgr.msc*. Clic droit sur le certificat client que vous voulez exporter, cliquez sur **toutes les tâches**, puis cliquez sur **Exporter**.
2. Exportez le certificat client avec la clé privée. Il s’agit d’un fichier *.pfx* . Veillez à enregistrer ou de mémoriser le mot de passe (clé) que vous avez défini pour ce certificat.

### <a name="upload"></a>4. télécharger le fichier .cer de certificat racine

Déclarer la variable de votre nom de certificat, remplacez la valeur par votre propre :

        $P2SRootCertName = "Mycertificatename.cer"

Ajouter les données de certificat public pour le certificat racine à Azure. Vous pouvez télécharger des fichiers pour jusqu'à 20 certificats racine. Vous ne pas Téléchargez la clé privée pour le certificat racine vers Azure. Une fois le fichier .cer est téléchargé, Azure utilise pour authentifier les clients qui se connectent au réseau virtuel. 

Remplacez le chemin d’accès de fichier par votre propre, puis exécutez les applets de commande.
    
        $filePathForCert = "C:\cert\Mycertificatename.cer"
        $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($filePathForCert)
        $CertBase64 = [system.convert]::ToBase64String($cert.RawData)
        $p2srootcert = New-AzureRmVpnClientRootCertificate -Name $P2SRootCertName -PublicCertData $CertBase64

## <a name="creategateway"></a>Partie 4 : créer la passerelle VPN

Configurez et créez la passerelle réseau virtuel pour votre VNet. La *-GatewayType* doit être **Vpn** et la *-VpnType* doit être **RouteBased**. Cela peut prendre jusqu'à 45 minutes.

        New-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
        -Location $Location -IpConfigurations $ipconf -GatewayType Vpn `
        -VpnType RouteBased -EnableBgp $false -GatewaySku Standard `
        -VpnClientAddressPool $VPNClientAddressPool -VpnClientRootCertificates $p2srootcert

## <a name="clientconfig"></a>Partie 5 - télécharger le package de configuration du client VPN

Clients qui se connectent à Azure à l’aide de P2S doivent avoir un certificat client et un package de configuration du client VPN installé. Les packages de configuration de clients VPN sont disponibles pour les clients Windows. Le package de client VPN contient des informations permettant de configurer le logiciel client VPN intégré à Windows et spécifique au VPN que vous voulez vous connecter. Le package n’installe pas de logiciel supplémentaire. Consultez le [Forum aux questions de la passerelle VPN](vpn-gateway-vpn-faq.md#point-to-site-connections) pour plus d’informations.

1. Une fois que la passerelle a été créée, vous pouvez télécharger le package de configuration client. Cet exemple télécharge le package pour les clients 64 bits. Si vous voulez télécharger le client 32 bits, remplacez « Amd64 » par « x86 ». Vous pouvez également télécharger le client VPN à l’aide du portail Azure.

        Get-AzureRmVpnClientPackage -ResourceGroupName $RG `
        -VirtualNetworkGatewayName $GWName -ProcessorArchitecture Amd64

2. L’applet de commande PowerShell renvoie un lien URL. Il s’agit d’un exemple de quel la ressemble URL renvoyée à :

        "https://mdsbrketwprodsn1prod.blob.core.windows.net/cmakexe/4a431aa7-b5c2-45d9-97a0-859940069d3f/amd64/4a431aa7-b5c2-45d9-97a0-859940069d3f.exe?sv=2014-02-14&sr=b&sig=jSNCNQ9aUKkCiEokdo%2BqvfjAfyhSXGnRG0vYAv4efg0%3D&st=2016-01-08T07%3A10%3A08Z&se=2016-01-08T08%3A10%3A08Z&sp=r&fileExtension=.exe"

3. Copiez et collez le lien qui est renvoyé à un navigateur web pour télécharger le package. Puis l’installer sur l’ordinateur client. Si vous obtenez une fenêtre indépendante SmartScreen, cliquez sur **plus d’informations**, puis **malgré tout exécuter** pour installer le package.

4. Sur l’ordinateur client, accédez aux **Paramètres du réseau** et cliquez sur **VPN**. Vous verrez la connexion répertoriée. Il affiche le nom du réseau virtuel il se connecte à et recherche similaire à cet exemple : 

    ![Client VPN] (./media/vpn-gateway-howto-point-to-site-rm-ps/vpn.png "Client VPN")


## <a name="clientcertificate"></a>Partie 6 - installer le certificat client

Chaque ordinateur client doit disposer d’un certificat client afin de s’authentifier. Lorsque vous installez le certificat client, vous devez le mot de passe qui a été créé lorsque le certificat client ont été exporté.

1. Copiez le fichier .pfx sur l’ordinateur client.
2. Double-cliquez sur le fichier .pfx pour l’installer. Ne modifiez pas l’emplacement d’installation.

## <a name="connect"></a>Partie 7 - se connecter à Azure

1. Pour vous connecter à votre VNet, sur l’ordinateur client, accédez à connexions VPN, puis recherchez la connexion VPN que vous avez créé. Il porte le même nom que votre réseau virtuel. Cliquez sur **se connecter**. Affichage d’un message peut s’afficher qui fait référence à l’aide du certificat. Dans ce cas, cliquez sur **Continuer** pour utiliser des privilèges élevés. 

2. Dans la page d’état de **connexion** , cliquez sur **se connecter** pour démarrer la connexion. Si vous voyez un écran **Sélectionner un certificat** , vérifiez que le certificat client affichant est celle que vous souhaitez utiliser pour vous connecter. Si elle n’est pas le cas, utilisez la flèche déroulante pour sélectionner le certificat approprié, puis cliquez sur **OK**.

    ![Connexion du client VPN] (./media/vpn-gateway-howto-point-to-site-rm-ps/clientconnect.png "Connexion du client VPN")

3. Votre connexion doit maintenant être établie.

    ![Établir la connexion] (./media/vpn-gateway-howto-point-to-site-rm-ps/connected.png "Établir la connexion")

## <a name="verify"></a>Partie 8 - Vérifiez votre connexion

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

## <a name="addremovecert"></a>Pour ajouter ou supprimer un certificat racine de confiance

Certificats sont utilisés pour authentifier les clients VPN pour Point-à-Site VPN. Les étapes suivantes vous aide à ajouter et supprimer des certificats racine. Lorsque vous ajoutez un fichier codé en base 64 X.509 (.cer) vers Azure, vous indiquez à Azure le certificat racine qui représente le fichier. 

Vous pouvez ajouter ou supprimer des certificats racine de confiance à l’aide de PowerShell ou dans le portail Azure. Si vous voulez procéder à l’aide du portail Azure, accédez à votre **passerelle réseau virtuel > Paramètres > configuration du Point à site > certificats racine**. Les étapes suivantes vous guident dans ces tâches à l’aide de PowerShell. 

### <a name="add-a-trusted-root-certificate"></a>Ajouter un certificat racine de confiance

Vous pouvez ajouter jusqu'à 20 fichiers de .cer de certificat racine de confiance à Azure. Suivez les étapes ci-dessous pour ajouter un certificat racine.

1. Créer et préparer le nouveau certificat racine que vous ajouterez à Azure. Exporter la clé publique en tant que Base-64 codé X.509 (. Limitée) et ouvrez-le avec un éditeur de texte. Puis copier uniquement la section ci-dessous. 
 
    Copiez les valeurs, comme le montre l’exemple suivant :

    ![certificat] (./media/vpn-gateway-howto-point-to-site-rm-ps/copycert.png "certificat")
    
2. Spécifiez le nom de certificat et les informations de clé comme étant une variable. Remplacez les informations par exemple de votre choix, comme illustré ci-dessous :

        $P2SRootCertName2 = "ARMP2SRootCert2.cer"
        $MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"


3. Ajouter le nouveau certificat racine. Vous pouvez uniquement ajouter un certificat à la fois.

        Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayname "VNet1GW" -ResourceGroupName "TestRG" -PublicCertData $MyP2SCertPubKeyBase64_2


4. Vous pouvez vérifier que le nouveau certificat a été correctement ajouté à l’aide de l’applet de commande suivante.

        Get-AzureRmVpnClientRootCertificate -ResourceGroupName "TestRG" `
        -VirtualNetworkGatewayName "VNet1GW"

### <a name="to-remove-a-trusted-root-certificate"></a>Pour supprimer un certificat racine de confiance

Vous pouvez supprimer le certificat racine de confiance à partir d’Azure. Lorsque vous supprimez un certificat de confiance, les certificats de client qui ont été générés à partir du certificat racine n’est plus pourront pas se connecter à Azure via Point-à-Site. Si vous voulez que les clients pour vous connecter, dont elles ont besoin installer un nouveau certificat client qui est généré à partir d’un certificat approuvé dans Azure.

1. Pour supprimer un certificat racine de confiance, modifiez l’exemple suivant :

    Déclarer les variables.

        $P2SRootCertName2 = "ARMP2SRootCert2.cer"
        $MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"

2. Supprimer le certificat.

        Remove-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -PublicCertData $MyP2SCertPubKeyBase64_2
 
3. Utiliser l’applet de commande suivante pour vérifier que le certificat a été supprimé. 

        Get-AzureRmVpnClientRootCertificate -ResourceGroupName "TestRG" `
        -VirtualNetworkGatewayName "VNet1GW"

## <a name="revoke"></a>Pour gérer la liste des certificats client révoqué

Vous pouvez révoquer les certificats clients. La liste de révocation de certificats permet de manière sélective refuser connectivité Point-à-Site basée sur des certificats clients individuels. Si vous supprimez un .cer de certificat racine d’Azure, il retire le niveau d’accès pour tous les certificats client généré/signé par le certificat racine révoqué. Si vous souhaitez révoquer un certificat client particulier, pas la racine, vous pouvez le faire. Ainsi les autres certificats qui ont été générés à partir du certificat racine seront toujours valable.

Le principe consiste à utiliser le certificat racine pour gérer l’accès à des niveaux d’équipe ou de l’organisation, lors de l’utilisation de certificats client révoqué pour contrôler l’accès permissions sur les utilisateurs individuels.

### <a name="revoke-a-client-certificate"></a>Révoquer un certificat client

1. Obtenir l’empreinte numérique du certificat client pour révoquer.

        $RevokedClientCert1 = "ClientCert1"
        $RevokedThumbprint1 = "‎ef2af033d0686820f5a3c74804d167b88b69982f"

2. Ajouter l’empreinte numérique à la liste de révocation empreinte numérique.

        Add-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
        -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1

3. Vérifiez que l’empreinte numérique a été ajouté à la liste de révocation de certificats. Vous devez ajouter une empreinte numérique à la fois.

        Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG

### <a name="reinstate-a-client-certificate"></a>Restaurer un certificat client

Vous pouvez rétablir un certificat client en supprimant l’empreinte numérique dans la liste des certificats client révoqué.

1.  Supprimer l’empreinte numérique dans la liste des empreinte de certificat client révoqué.

        Remove-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
        -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1

2. Vérifier si l’empreinte numérique est supprimé de la liste révoquée.

        Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez ajouter une machine virtuelle à votre réseau virtuel. Pour obtenir la procédure, voir [créer une Machine virtuelle](../virtual-machines/virtual-machines-windows-hero-tutorial.md) .