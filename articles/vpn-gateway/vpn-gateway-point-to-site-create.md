<properties
   pageTitle="Configurer une connexion de passerelle Point-à-Site VPN à un réseau virtuel Azure à l’aide du portail classique | Microsoft Azure"
   description="Connecter de façon sécurisée à votre réseau virtuel Azure en créant une connexion de passerelle VPN Point-à-Site."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/17/2016"
   ms.author="cherylmc"/>

# <a name="configure-a-point-to-site-connection-to-a-vnet-using-the-classic-portal"></a>Configurer une connexion Point-à-Site à un VNet à l’aide du portail classique

> [AZURE.SELECTOR]
- [Responsable de ressources - portail Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
- [Responsable de ressources - PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
- [Classique - portail Azure](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
- [Classique - portail classique](vpn-gateway-point-to-site-create.md)

Configuration d’un Point de Site (P2S) vous permet de créer une connexion sécurisée à partir d’un ordinateur client à un réseau virtuel. Une connexion P2S est utile lorsque vous voulez vous connecter à votre VNet depuis un emplacement distant, tels que d’accueil ou d’une conférence, ou lorsque vous n’avez que quelques clients qui doivent se connecter à un réseau virtuel.

Cet article vous guide dans la création d’un VNet avec une connexion Point-à-Site dans le **modèle de déploiement classique** à l’aide du **portail classique**.

Connexions point-à-Site ne nécessitent pas un périphérique VPN ou une adresse IP au public pour l’utiliser. Une connexion VPN est établie en démarrant la connexion à partir de l’ordinateur client. Pour plus d’informations sur les connexions Point-à-Site, voir le [Forum aux questions de la passerelle VPN](vpn-gateway-vpn-faq.md#point-to-site-connections) et la [planification et conception](vpn-gateway-plan-design.md).


### <a name="deployment-models-and-methods-for-p2s-connections"></a>Modèles de déploiement et méthodes pour les connexions P2S

[AZURE.INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)] 

Le tableau suivant montre les deux modèles de déploiement et les méthodes de déploiement disponibles pour les configurations P2S. Lorsqu’un article avec des étapes de configuration est disponible, nous attacher directement à partir de ce tableau.

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-table-point-to-site-include.md)] 



## <a name="basic-workflow"></a>Flux de travail

![Point-à-Site-diagramme] (./media/vpn-gateway-point-to-site-create/p2sclassic.png "point-à-site")
 
Les étapes suivantes vous passez en revue les étapes pour créer une connexion Point-à-Site sécurisée à un réseau virtuel. 

La configuration d’une connexion Point-à-Site se décompose en quatre sections. L’ordre dans lequel vous configurez chacune de ces sections est important. Ne pas ignorer les étapes ou passer directement.

- **Section 1** Créez un réseau virtuel et passerelle VPN.
- **Section 2** Créez les certificats utilisés pour l’authentification et les télécharger.
- **Section 3** Exporter et installez vos certificats client.
- **Section 4** Configurer votre client VPN.

## <a name="vnetvpn"></a>Section 1 : créer un réseau virtuel et une passerelle VPN

### <a name="part-1-create-a-virtual-network"></a>Partie 1 : Créer un réseau virtuel

1. Connectez-vous au [portail classique Azure](https://manage.windowsazure.com/). Ces étapes utilisent le portail classique, pas le portail Azure. Pour l’instant, vous ne pouvez pas créer une connexion P2S à l’aide du portail Azure.

2. Dans le coin inférieur gauche de l’écran, cliquez sur **Nouveau**. Dans le volet de navigation, cliquez sur **Services de réseau**, puis cliquez sur **Réseau virtuel**. Cliquez sur **Créer personnalisée** pour lancer l’Assistant de configuration.

3. Dans la page **Détails du réseau virtuel** , entrez les informations suivantes, puis cliquez sur la flèche suivant dans le coin inférieur droit.
    - **Nom**: nommez votre réseau virtuel. Par exemple, « VNet1 ». Il s’agit le nom que vous devez faire lorsque vous déployez machines virtuelles cette VNet.
    - **Emplacement**: l’emplacement est directement lié à l’emplacement physique (région) où vous souhaitez vos ressources (machines virtuelles) réside. Par exemple, si vous souhaitez que les ordinateurs virtuels que vous déployez sur ce réseau virtuel doit être physiquement situé dans le fuseau horaire, sélectionnez cet emplacement. Vous ne pouvez pas modifier la zone associée à votre réseau virtuel après sa création.

4. Dans la page **DNS Servers et connectivité VPN** , entrez les informations suivantes, puis cliquez sur la flèche suivant dans le coin inférieur droit.
    - **Serveurs DNS**: entrez le nom du serveur DNS et l’adresse IP, ou sélectionnez un serveur DNS précédemment enregistré dans le menu contextuel. Ce paramètre ne crée pas un serveur DNS. Il vous permet de spécifier les serveurs DNS que vous souhaitez utiliser pour la résolution de nom pour ce réseau virtuel. Si vous souhaitez utiliser le service de résolution de noms par défaut Azure, laissez cette section vide.
    - **Configurer Point-à-Site VPN**: activez la case à cocher.

5. Dans la page **Connectivité Point-à-Site** , spécifiez la plage d’adresses IP à partir de laquelle vos clients VPN recevront une adresse IP lorsque connecté. Il existe quelques règles concernant les plages d’adresses que vous spécifiez. Il est important de vérifier que la plage que vous spécifiez ne se chevauche pas avec n’importe lequel des plages situés sur votre réseau local.

6. Entrez les informations suivantes, puis cliquez sur la flèche suivant.
 - **Espace d’adressage**: inclure l’adresse IP de début et CIDR (nombre d’adresses).
 - **Espace d’adressage ajouter**: ajouter espace d’adressage uniquement si elle est requis pour la conception de votre réseau.

7. Dans la page **Des espaces d’adressage réseau virtuel** , spécifiez la plage d’adresses que vous souhaitez utiliser pour votre réseau virtuel. Voici les adresses IP dynamiques (DIP) doivent être affectées pour les ordinateurs virtuels et d’autres instances de rôles que vous déployez sur ce réseau virtuel.<br><br>Il est particulièrement important sélectionner une plage qui ne se chevauche pas avec n’importe lequel des plages qui sont utilisés pour votre réseau local. Vous devez coordonner avec votre administrateur réseau, qui peut-être imaginer une plage d’adresses IP à partir de votre espace d’adressage réseau local pour pouvoir utiliser pour votre réseau virtuel.

8. Entrez les informations suivantes, puis cliquez sur la case à cocher pour commencer à créer votre réseau virtuel.
 - **Espace d’adressage**: ajouter la plage d’adresses IP interne que vous souhaitez utiliser pour ce réseau virtuel, y compris IP de début et de nombre. Il est important de sélectionner une plage qui ne se chevauche pas avec n’importe lequel des plages qui sont utilisés pour votre réseau local. 
 - **Ajouter sous-réseau**: sous-réseaux supplémentaires ne sont pas obligatoires, mais vous voudrez peut-être créer un sous-réseau séparé pour les machines virtuelles qui auront DIP statique. Ou vous souhaiterez peut-être avoir vos ordinateurs virtuels dans un sous-réseau distinct de vos autres instances de rôle.
 - **Sous-réseau de passerelle ajouter**: la passerelle constitue requis pour un réseau VPN Point-à-Site. Cliquez pour ajouter le sous-réseau passerelle. Le sous-réseau passerelle est utilisé uniquement pour la passerelle réseau virtuel.

9. Une fois que votre réseau virtuel a été créé, vous verrez **que créé** répertorié sous **statut** sur la page réseaux dans le portail classique Azure. Une fois que votre réseau virtuel a été créé, vous pouvez créer votre passerelle routage dynamique.

### <a name="part-2-create-a-dynamic-routing-gateway"></a>Partie 2 : Créer une passerelle routage dynamique

Le type de passerelle doit être configuré comme dynamique. Les passerelles routage statiques ne fonctionnent pas avec cette fonctionnalité.

1. Dans le portail classique Azure, dans la page **réseaux** , cliquez sur le réseau virtuel que vous avez créé et accédez à la page de **tableau de bord** .

2. Cliquez sur **Créer une passerelle**, située au bas de la page de **tableau de bord** . Un message s’affiche et **vous souhaitez créer une passerelle de réseau virtuel « VNet1 »**. Cliquez sur **Oui** pour commencer la création de la passerelle. Il peut prendre environ 15 minutes pour que la passerelle à créer.

## <a name="generate"></a>Section 2 : générer et télécharger des certificats

Certificats sont utilisés pour authentifier les clients VPN pour Point-à-Site VPN. Vous pouvez utiliser un certificat racine généré par une solution certificat d’entreprise, ou vous pouvez utiliser un certificat auto-signé. Vous pouvez télécharger jusqu'à 20 certificats racine sur Azure. Une fois le fichier .cer est téléchargé, Azure pouvez utiliser les informations qu’il contient pour authentifier les clients disposer d’un certificat client. Le certificat client doit être généré à partir du même certificat qui représente le fichier .cer.

Dans cette section, vous exécuterez les étapes suivantes :

- Obtenir le fichier .cer pour un certificat racine. Il peut s’agir d’un certificat auto-signé, ou vous pouvez utiliser le système de certificat de votre entreprise.
- Télécharger le fichier .cer dans Azure.
- Générer des certificats client.

### <a name="root"></a>Partie 1 : Obtenir le fichier .cer pour le certificat racine

Si vous utilisez un système de certificat d’entreprise, procurez-vous le fichier .cer pour le certificat racine que vous voulez utiliser. Dans [la partie 3](#createclientcert), générer les certificats client à partir du certificat racine.

Si vous n’utilisez pas une solution certificat d’entreprise, vous devez créer un certificat auto-signé. Pour obtenir la procédure Windows 10, vous pouvez faire référence à [l’utilisation des certificats racine auto-signé configurations Point-à-Site](vpn-gateway-certificates-point-to-site.md). L’article vous guide dans l’aide de makecert pour générer un certificat auto-signé, puis exporter le fichier .cer.

### <a name="upload"></a>Partie 2 : Télécharger le fichier de .cer de certificat racine sur le portail classique Azure

Ajouter un certificat approuvé à Azure. Lorsque vous ajoutez un fichier codé en base 64 X.509 (.cer) vers Azure, vous indiquez à Azure le certificat racine qui représente le fichier.

1. Dans le portail classique Azure, sur la page de **certificats** pour votre réseau virtuel, cliquez sur **télécharger un certificat racine**.

2. Dans la page **Certificat télécharger** , recherchez le certificat racine .cer, puis cliquez sur la coche.

### <a name="createclientcert"></a>3e partie : Générer un certificat client

Ensuite, générer les certificats clients. Vous pouvez soit générer un certificat unique pour chaque client qui va se connecter ou vous pouvez utiliser le même certificat sur plusieurs clients. La génération de certificats client unique a la possibilité de révoquer un certificat unique si nécessaire. Dans le cas contraire, si tout le monde utilise le même certificat client et que vous deviez révoquer le certificat pour un client, vous devrez générer et installer de nouveaux certificats pour tous les clients qui utilisent le certificat s’authentifier.

- Si vous utilisez une solution certificat d’entreprise, générer un certificat client avec le format de valeur nom courant 'name@yourdomain.com', plutôt que le format NetBIOS 'Domaine\nom_utilisateur'. 

- Si vous utilisez un certificat auto-signé, voir [utilisation des certificats racine auto-signé configurations Point-à-Site](vpn-gateway-certificates-point-to-site.md) pour générer un certificat client.

## <a name="installclientcert"></a>Section 3 : exporter et installez le certificat client

Installer un certificat client sur chaque ordinateur sur lequel vous voulez vous connecter au réseau virtuel. Un certificat client est requis pour l’authentification. Vous pouvez automatiser l’installation du certificat client, ou vous pouvez l’installer manuellement. Les étapes suivantes vous aide à l’exportation et d’installer le certificat client manuellement.

1. Pour exporter un certificat client, vous pouvez utiliser *certmgr.msc*. Clic droit sur le certificat client que vous voulez exporter, cliquez sur **toutes les tâches**, puis cliquez sur **Exporter**.
2. Exportez le certificat client avec la clé privée. Il s’agit d’un fichier *.pfx* . Veillez à enregistrer ou de mémoriser le mot de passe (clé) que vous avez défini pour ce certificat.
3. Copiez le fichier *.pfx* sur l’ordinateur client. Sur l’ordinateur client, double-cliquez sur le fichier *.pfx* pour l’installer. Entrez le mot de passe lors d’une demande. Ne modifiez pas l’emplacement d’installation.

## <a name="vpnclientconfig"></a>Section 4 : configurer votre client VPN

Pour vous connecter au réseau virtuel, vous devez également configurer un client VPN. Le client requiert un certificat client et la configuration correcte du client VPN pour se connecter. Pour configurer un client VPN, effectuez les opérations suivantes, dans l’ordre.

### <a name="part-1-create-the-vpn-client-configuration-package"></a>Partie 1 : Créer un package de configuration de client VPN

1. Dans le portail classique Azure, dans la page **tableau de bord** de votre réseau virtuel, accédez au menu coup de œil rapide dans le coin droit. Pour la liste des systèmes d’exploitation pris en charge, consultez les connexions [Point-à-Site](vpn-gateway-vpn-faq.md#point-to-site-connections) section le Forum aux questions de la passerelle VPN. Le package de client VPN contient des informations de configuration pour configurer le logiciel client VPN intégré à Windows. Le package n’installe pas de logiciel supplémentaire. Les paramètres sont spécifiques au réseau virtuel que vous voulez vous connecter.<br><br>Sélectionnez le package de téléchargement qui correspond au système d’exploitation client sur lequel il sera installé :
 - Pour les clients 32 bits, sélectionnez **Télécharger le Package 32 bits Client VPN**.
 - Pour les clients 64 bits, sélectionnez **Télécharger le Package 64 bits Client VPN**.

2. Il faut quelques minutes pour créer votre package client. Une fois que le package est terminé, vous pouvez télécharger le fichier. Le fichier *.exe* que vous téléchargez pouvant être stocké en toute sécurité sur votre ordinateur local.

3. Après avoir généré et télécharger le package de client VPN à partir du portail Azure classique, vous pouvez installer le module client sur l’ordinateur client à partir de laquelle vous voulez vous connecter à votre réseau virtuel. Si vous envisagez d’installer le module client VPN sur plusieurs ordinateurs client, vérifiez que chaque également qu’un certificat client installé.

### <a name="part-2-install-the-vpn-configuration-package-on-the-client"></a>Partie 2 : Installer le package de configuration VPN sur le client

1. Copiez le fichier de configuration localement sur l’ordinateur que vous voulez vous connecter à votre réseau virtuel et double-cliquez sur le fichier .exe. 

2. Une fois que le package a installé, vous pouvez démarrer la connexion VPN. Le package de configuration n’est pas signé par Microsoft. Vous pouvez pour signer le package à l’aide du service de signature de votre organisation ou se vous-même à l’aide de [SignTool]( http://go.microsoft.com/fwlink/p/?LinkId=699327). Il est OK à utiliser le package sans vous connecter. Toutefois, si le package n’est pas connecté, un avertissement s’affiche lorsque vous installez le package.

3. Sur l’ordinateur client, accédez aux **Paramètres du réseau** et cliquez sur **VPN**. Vous verrez la connexion répertoriée. Il affiche le nom du réseau virtuel qu’il doit se connecter et doit ressembler à ceci : 

    ![Client VPN] (./media/vpn-gateway-point-to-site-create/vpn.png "Client VPN")


### <a name="part-3-connect-to-azure"></a>3e partie : Se connecter à Azure

1. Pour vous connecter à votre VNet, sur l’ordinateur client, accédez à connexions VPN, puis recherchez la connexion VPN que vous avez créé. Il porte le même nom que votre réseau virtuel. Cliquez sur **se connecter**. Affichage d’un message peut s’afficher qui fait référence à l’aide du certificat. Dans ce cas, cliquez sur **Continuer** pour utiliser des privilèges élevés. 

2. Dans la page d’état de **connexion** , cliquez sur **se connecter** pour démarrer la connexion. Si vous voyez un écran **Sélectionner un certificat** , vérifiez que le certificat client affichant est celle que vous souhaitez utiliser pour vous connecter. Si elle n’est pas le cas, utilisez la flèche déroulante pour sélectionner le certificat approprié, puis cliquez sur **OK**.

    ![Client VPN 2] (./media/vpn-gateway-point-to-site-create/clientconnect.png "Connexion du client VPN")

3. Votre connexion doit maintenant être établie.

    ![Client VPN 3] (./media/vpn-gateway-point-to-site-create/connected.png "Connexion du client VPN 2")

### <a name="part-4-verify-the-vpn-connection"></a>4e partie : Vérifiez la connexion VPN

1. Pour vérifier que votre connexion VPN est active, ouvrez une invite de commandes avec élévation de privilèges et exécutez *ipconfig/all*.
2. Afficher les résultats. Notez que l’adresse IP que vous avez reçu est une des adresses dans la plage d’adresses de connectivité Point-à-Site que vous avez spécifié lorsque vous avez créé votre VNet. Les résultats doivent être doit ressembler à ceci :

Exemple :



    PPP adapter VNet1:
        Connection-specific DNS Suffix .:
        Description.....................: VNet1
        Physical Address................:
        DHCP Enabled....................: No
        Autoconfiguration Enabled.......: Yes
        IPv4 Address....................: 192.168.130.2(Preferred)
        Subnet Mask.....................: 255.255.255.255
        Default Gateway.................:
        NetBIOS over Tcpip..............: Enabled

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez ajouter des machines virtuelles à votre réseau virtuel. Découvrez [comment créer une machine virtuelle personnalisée](../virtual-machines/virtual-machines-windows-classic-createportal.md).

Si vous souhaitez plus d’informations sur les réseaux virtuels, consultez la page de [Documentation de réseau virtuel](https://azure.microsoft.com/documentation/services/virtual-network/) .
