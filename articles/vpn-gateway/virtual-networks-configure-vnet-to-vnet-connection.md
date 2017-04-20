<properties
   pageTitle="Configurer une connexion VNet-VNet pour le modèle de déploiement classique | Microsoft Azure"
   description="Comment se connecter Azure réseaux virtuels collaborer à l’aide de PowerShell et le portail classique Azure."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/31/2016"
   ms.author="cherylmc"/>


# <a name="configure-a-vnet-to-vnet-connection-for-the-classic-deployment-model"></a>Configurer une connexion VNet-VNet pour le modèle de déploiement classique

> [AZURE.SELECTOR]
- [Responsable de ressources - portail Azure](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
- [Responsable de ressources - PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
- [Classique - portail classique](virtual-networks-configure-vnet-to-vnet-connection.md)



Cet article vous explique comment procéder pour créer et connecter des réseaux virtuels collaborer à l’aide du modèle de déploiement classique (également appelé gestion des services). Les étapes suivantes utilisent le portail classique Azure pour créer la VNets et passerelles et PowerShell pour configurer la connexion VNet-VNet. Vous ne pouvez pas configurer la connexion dans le portail.

![VNet à VNet connectivité diagramme](./media/virtual-networks-configure-vnet-to-vnet-connection/v2vclassic.png)


### <a name="deployment-models-and-methods-for-vnet-to-vnet-connections"></a>Modèles de déploiement et méthodes pour les connexions VNet-VNet

[AZURE.INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

Le tableau suivant indique les modèles de déploiement actuellement disponibles et les méthodes pour les configurations VNet-VNet. Lorsqu’un article avec des étapes de configuration est disponible, nous attacher directement à partir de ce tableau.

[AZURE.INCLUDE [vpn-gateway-table-vnet-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

## <a name="about-vnet-to-vnet-connections"></a>À propos des connexions VNet-VNet

Connexion d’un réseau virtuel à un autre réseau virtuel (VNet-VNet) est similaire à la connexion d’un réseau virtuel à un emplacement de site local. Les deux types de connectivité permet de fournir un tunnel sécurisé à l’aide de IPsec/IKE une passerelle VPN. 

La VNets que vous vous connectez peut être dans différents abonnements et les différentes régions. Vous pouvez combiner VNet VNet communication avec des configurations sur plusieurs sites. Cela vous permet d’établir des topologies réseau qui combinent connectivité entre locaux avec la connectivité réseau entre virtuel.


### <a name="why-connect-virtual-networks"></a>Pourquoi connecter réseaux virtuels ?

Vous souhaiterez peut-être connecter des réseaux virtuels pour les raisons suivantes :

- **Région croisée geo-redondance et geo présence**
    - Vous pouvez configurer votre propre geo-réplication ou de synchronisation avec une connexion sécurisée sans passer par les points de terminaison via Internet.
    - Avec équilibrage de charge Azure et Microsoft ou technologie cluster tiers, vous pouvez configurer la charge de travail hautement disponible avec geo redondance dans plusieurs régions Azure. Exemple importantes, vous pouvez configurer SQL toujours dans des groupes de disponibilité répartition sur plusieurs régions Azure.

- **Applications à plusieurs niveaux régionale avec la limite d’isolement forte**
    - Dans la zone de même, vous pouvez configurer des applications multicouches avec plusieurs VNets connecté ainsi que d’isolement forte et sécuriser la communication entre niveau.

- **Croisées abonnement, communication entre organisation dans Azure**
    - Si vous avez plusieurs abonnements Azure, vous pouvez connecter les charges de travail à partir de différents abonnements ensemble en toute sécurité entre les réseaux virtuels.
    - Pour les entreprises ou les fournisseurs de services, vous pouvez activer les communications entre organisation avec la technologie VPN sécurisée dans Azure.

### <a name="vnet-to-vnet-faq-for-classic-vnets"></a>FAQ sur VNet-VNet VNets classique

- Les réseaux virtuels peuvent se trouver dans les abonnements identiques ou différents.

- Les réseaux virtuels peuvent se trouver dans les régions Azure identiques ou différentes (emplacements).

- Un service cloud ou une point de terminaison d’équilibrage de charge ne peut pas s’étendre sur les réseaux virtuels, même s’ils sont connectés ensemble.

- Les périphériques VPN ne nécessite pas de connexion plusieurs réseaux virtuels entre eux.

- VNet-VNet prend en charge les réseaux virtuels Azure qui se connecte. Il ne pas en charge des machines virtuelles qui se connecte ou cloud services qui ne sont pas déployées à un réseau virtuel.

- VNet-VNet nécessite des passerelles routage dynamiques. Azure passerelles routage statiques ne sont pas pris en charge.

- Connectivité réseau virtuelle peut être utilisée simultanément avec plusieurs sites VPN. Il existe un maximum de 10 tunnel VPN pour une passerelle VPN réseau virtuel connecter à d’autres réseaux virtuels, ou sites locaux.

- Les espaces d’adressage des sites réseau local virtuels réseaux et en local doivent se chevauchent pas. Chevauchement des espaces d’adressage entraîne la création de réseaux virtuels ou téléchargement de fichiers configuration netcfg échec.

- Tunnel redondants entre deux réseaux virtuels n’est pas pris en charge.

- Tous les tunnel VPN pour VNet, y compris les P2S VPN, partage la bande passante disponible pour la passerelle VPN et la disponibilité de passerelle VPN même SLA dans Azure.

- Le trafic VNet-VNet parcourt le réseau principal Azure.


## <a name="step1"></a>Étape 1 : planifier votre plages d’adresses IP

Il est important de décider les plages que vous utiliserez pour configurer vos réseaux virtuel. Pour cette configuration, vous devez vous assurer qu’aucun vos plages VNet se chevauchent avec eux, ou toute autre des réseaux locales auxquelles ils se connectent à.

Le tableau suivant montre un exemple de la définition de votre VNets. En règle générale uniquement, utilisez les plages. Notez les plages de vos réseaux virtuel. Vous avez besoin de ces informations pour les étapes ultérieures.

**Exemples de paramètres**

|Réseau virtuel  |Espace d’adressage               |Région      |Se connecte au site de réseau local|
|:----------------|:---------------------------|:-----------|:-----------------------------|
|VNet1            |VNet1 (10.1.0.0/16)         |États-Unis Ouest     |VNet2Local (10.2.0.0/16)      |
|VNet2            |VNet2 (10.2.0.0/16)         |Orient Japon  |VNet1Local (10.1.0.0/16)      |
  
## <a name="step-2---create-vnet1"></a>Étape 2 : créer VNet1

Dans cette étape, nous créons VNet1. Lorsque vous utilisez un des exemples, veillez à entrer vos propres valeurs. Si votre VNet existe déjà, vous n’avez pas besoin d’effectuer cette étape. Mais, vous n’avez pas besoin de vérifier que les plages d’adresses IP ne se chevauchent pas avec les plages pour votre VNet deuxième ou d’un des autres le VNets auquel vous voulez vous connecter.

1. Connectez-vous au [portail classique Azure](https://manage.windowsazure.com). Dans cet article, nous utilisons le portail classique, car certaines des paramètres de configuration requis ne sont pas encore disponibles dans le portail Azure.

2. Dans le coin inférieur gauche de l’écran, cliquez sur **Nouveau** > **Services de réseau** > **Réseau virtuel** > **Créer personnalisée** pour lancer l’Assistant de configuration. Lorsque vous naviguez dans l’Assistant, ajoutez les valeurs spécifiées à chaque page.

### <a name="virtual-network-details"></a>Détails du réseau virtuel

Dans la page Détails du réseau virtuel, entrez les informations suivantes :

  ![Détails du réseau virtuel](./media/virtual-networks-configure-vnet-to-vnet-connection/IC736055.png)

  - **Nom** : nom de votre réseau virtuel. Par exemple, VNet1.
  - **Emplacement** – lorsque vous créez un réseau virtuel, vous associez à un emplacement Azure (région). Par exemple, si vous souhaitez que vos ordinateurs virtuels déployés sur votre réseau virtuel se trouver physiquement ouest aux États-Unis, sélectionnez cet emplacement. Vous ne pouvez pas modifier l’emplacement associé à votre réseau virtuel après sa création.

### <a name="dns-servers-and-vpn-connectivity"></a>Serveurs DNS et connectivité VPN

Dans la page DNS Servers et connectivité VPN, entrez les informations suivantes, puis cliquez sur la flèche suivant dans le coin inférieur droit.

  ![Serveurs DNS et connectivité VPN](./media/virtual-networks-configure-vnet-to-vnet-connection/IC736056.jpg)  

- **Serveurs DNS** - Entrez le nom du serveur DNS et l’adresse IP, ou sélectionnez un serveur DNS préalablement enregistré dans la liste déroulante. Ce paramètre ne crée pas un serveur DNS. Il vous permet de spécifier les serveurs DNS que vous souhaitez utiliser pour la résolution de nom pour ce réseau virtuel. Si vous souhaitez que résolution de noms entre vos réseaux virtuel, vous devez configurer votre serveur DNS, plutôt que d’utiliser la résolution de noms qui est fournie par Azure.
- Ne sélectionnez pas un des cases à cocher pour la connectivité P2S ou S2S. Cliquez sur la flèche dans le coin inférieur droit pour passer à l’écran suivant.

### <a name="virtual-network-address-spaces"></a>Espaces d’adressage réseau virtuel

Dans la page des espaces d’adressage réseau virtuel, spécifiez la plage d’adresses que vous souhaitez utiliser pour votre réseau virtuel. Voici les adresses IP dynamiques (DIP) doivent être affectées pour les ordinateurs virtuels et d’autres instances de rôles que vous déployez sur ce réseau virtuel. 

Si vous créez un VNet qui aura également une connexion à votre réseau local, il est particulièrement important sélectionner une plage qui ne se chevauche pas avec n’importe lequel des plages qui sont utilisés pour votre réseau local. Dans ce cas, vous devez coordonner avec votre administrateur réseau. Votre administrateur réseau devra peut-être imaginer une plage d’adresses IP à partir de votre espace d’adressage réseau local pour pouvoir utiliser pour votre VNet.

  ![Page espaces d’adressage réseau virtuelle](./media/virtual-networks-configure-vnet-to-vnet-connection/IC736057.jpg)

  - **Espace d’adressage** - notamment IP de début et le nombre d’adresses. Vérifiez que les espaces d’adressage que vous spécifiez ne se chevauchent d’un des espaces d’adressage que vous avez sur votre réseau local. Dans cet exemple, nous utilisons 10.1.0.0/16 pour VNet1.
  - **Ajouter sous-réseau** - en incluant des IP de début et le nombre d’adresses. Sous-réseaux supplémentaires ne sont pas obligatoires, mais vous voudrez peut-être créer un sous-réseau séparé pour les machines virtuelles qui auront DIP statique. Ou vous souhaiterez peut-être avoir vos ordinateurs virtuels dans un sous-réseau distinct de vos autres instances de rôle.
 
**Cliquez sur la coche** sur le coin inférieur droit de la page et de votre réseau virtuel va commencer à créer. Lorsqu’elle est terminée, vous verrez « Créée » répertorié sous statut sur la page réseaux.

## <a name="step-3---create-vnet2"></a>Étape 3 : créer VNet2

Ensuite, répétez les étapes précédentes pour créer un autre VNet. Dans les étapes suivantes, vous devez connecter les deux VNets. Vous pouvez consulter les [exemples de paramètres](#step1) à l’étape 1. Si votre VNet existe déjà, vous n’avez pas besoin d’effectuer cette étape. Toutefois, vous devez vérifier que les plages d’adresses IP ne se chevauchent d’un des autres VNets ou en local réseaux que vous voulez vous connecter.

## <a name="step-4---add-the-local-network-sites"></a>Étape 4 : ajouter les sites de réseau local

Lorsque vous créez une configuration VNet-VNet, vous devez configurer les sites de réseau local, qui sont affichés dans la page **Réseaux locaux** du portail. Azure utilise les paramètres spécifiés dans chaque site réseau local pour déterminer comment router le trafic entre la VNets. Déterminer le nom que vous voulez utiliser pour faire référence à chaque site réseau local. Il est préférable d’utiliser un nom descriptif, lorsque vous sélectionnez la valeur dans une liste déroulante dans les étapes suivantes.

Par exemple, VNet1 se connecte à un site de réseau local que vous créez nommé « VNet2Local ». Les paramètres de VNet2Local contiennent les préfixes d’adresse pour VNet2 et une adresse IP publique de la passerelle VNet2. VNet2 se connecte à un site de réseau local vous créez nommé « VNet1Local » qui contient les préfixes d’adresse pour VNet1 et l’adresse IP de la passerelle VNet1.

### <a name="localnet"></a>Ajouter le site de réseau local VNet1Local

1. Dans le coin inférieur gauche de l’écran, cliquez sur **Nouveau** > **Services de réseau** > **Réseau virtuel** > **Ajouter un réseau Local**.

2. Dans la page **spécifier les détails de votre réseau local** , pour **nom**, entrez un nom que vous souhaitez utiliser pour représenter le réseau auquel vous voulez vous connecter. Dans cet exemple, vous pouvez utiliser « VNet1Local » pour faire référence aux plages d’adresses IP et passerelle pour VNet1.

3. Pour l' **adresse IP du périphérique VPN (facultatif)**, spécifiez une adresse IP publique valide. En règle générale, vous utiliserez l’adresse IP externe réel pour un périphérique VPN. Pour les configurations VNet-VNet, vous utilisez l’adresse IP publique est affectée à la passerelle pour votre VNet. Mais, étant donné que vous n’avez pas encore créé la passerelle, vous pouvez spécifier une adresse IP publique valide comme un espace réservé. Ne laissez ce champ vide : il n’est pas facultatif pour cette configuration. Dans une étape ultérieure, vous revenez à ces paramètres et les configurer avec les adresses IP passerelle correspondants une fois que Azure génère. Cliquez sur la flèche pour passer à l’écran suivant.

4. Dans la **page spécifier l’adresse**, entrez le IP adresse plage et l’adresse nombre pour VNet1. Cela doit correspondre exactement à la plage qui est configurée pour VNet1. Azure utilise les plages d’adresses IP que vous spécifiez pour acheminer le trafic destiné aux VNet1. Cliquez sur la case à cocher pour créer le réseau local.

### <a name="add-the-local-network-site-vnet2local"></a>Ajouter le site de réseau local VNet2Local

Suivez les étapes ci-dessus pour créer le site de réseau local « VNet2Local ». Vous pouvez faire référence aux valeurs dans les [exemples de paramètres](#step1) à l’étape 1, le cas échéant.

### <a name="configure-each-vnet-to-point-to-a-local-network"></a>Configurer chaque VNet pour pointer sur un réseau local

Chaque VNet doit pointer vers le réseau local correspondant à acheminer le trafic vers. 

#### <a name="for-vnet1"></a>Pour VNet1

1. Accédez à la page **configurer** pour réseau virtuel **VNet1**. 
2. Sous connectivité de site à, sélectionnez « Se connecter au réseau local », puis **VNet2Local** en tant que le réseau local dans la liste déroulante. 
3. Enregistrez vos paramètres.

#### <a name="for-vnet2"></a>Pour VNet2

1. Accédez à la page **configurer** pour réseau virtuel **VNet2**. 
2. Sous connectivité de site à, sélectionnez « Se connecter au réseau local », puis sélectionnez **VNet1Local** dans la liste déroulante en tant que le réseau local. 
3. Enregistrez vos paramètres.

## <a name="step-5---configure-a-gateway-for-each-vnet"></a>Étape 5 : configurer une passerelle pour chaque VNet

Configurer une passerelle routage dynamique pour chaque réseau virtuel. Cette configuration ne prend pas en charge les passerelles routage statique. Si vous utilisez VNets qui ont été précédemment configuré et qui ont déjà passerelles routage dynamique, vous n’avez pas besoin d’effectuer cette étape. Si vos passerelles sont routage statique, vous devez les supprimer et recréez-les comme des passerelles routage dynamique. Si vous supprimez une passerelle, l’adresse IP est affectée à obtient publiée et vous devez revenir en arrière et reconfigurer un de vos réseaux locaux et les appareils VPN avec la nouvelle adresse IP publique pour la nouvelle passerelle.

1. Dans la page **réseaux** , vérifiez que la colonne statut pour votre réseau virtuel est **créé**.

2. Dans la colonne **nom** , cliquez sur le nom de votre réseau virtuel. Dans cet exemple, nous utilisons « VNet1 ».

3. Dans la page **tableau de bord** , notez que cette VNet n’a pas une passerelle encore configurée. Vous voyez ce statut modifié lorsque vous accédez aux étapes pour configurer votre passerelle.

4. Dans la partie inférieure de la page, cliquez sur **Créer une passerelle** et le **Routage dynamique**. Lorsque le système vous invite à confirmer que vous souhaitez la passerelle créée, cliquez sur Oui.

    ![Type de passerelle](./media/virtual-networks-configure-vnet-to-vnet-connection/IC717026.png)  

5. Lors de la création de votre passerelle, notez le graphique de la passerelle dans la page change au jaune et indique « Création de la passerelle ». Il faut généralement environ 30 minutes pour que la passerelle créer.

6. Répétez les mêmes étapes pour VNet2. Vous n’avez pas besoin la première VNet passerelle pour terminer avant de commencer à créer la passerelle pour vos autres VNet.

7. Lorsque l’état de la passerelle passe à « Connexion », l’adresse IP pour chaque passerelle est visible dans le tableau de bord. Notez l’adresse IP correspondant à chaque VNet, en prenant soin de ne pas pour les mélanger. Voici les adresses IP qui sont utilisés lorsque vous modifiez vos adresses IP espace réservé pour le périphérique VPN pour chaque réseau local.

## <a name="step-6---edit-the-local-network"></a>Étape 6 : modifier le réseau local

1. Dans la page **Réseaux locaux** , cliquez sur le nom du réseau Local que vous voulez modifier, puis cliquez sur **Modifier** dans la partie inférieure de la page. Pour **adresse IP du périphérique VPN**, entrez l’adresse IP de la passerelle correspond à la VNet. Par exemple, pour VNet1Local, placez dans l’adresse IP de passerelle affectée à VNet1. Cliquez ensuite sur la flèche située en bas de la page.

2. Dans la page **spécifier l’espace d’adressage** , cliquez sur la coche dans le coin inférieur droit sans apporter de modifications.

## <a name="step-7---create-the-vpn-connection"></a>Étape 7 : créer la connexion VPN

Lorsque toutes les étapes précédentes ont été effectuées, définissez les touches partagées IPsec/IKE et créer la connexion. Cette série d’étapes utilise PowerShell et ne peut pas être configurée dans le portail. Découvrez [comment installer et configurer Azure PowerShell](../powershell-install-configure.md) pour plus d’informations sur l’installation les applets de commande PowerShell Azure. Veillez à télécharger la version la plus récente des applets de commande de gestion de Service (p). 

1. Ouvrez Windows PowerShell et se connecter.

        Add-AzureAccount

2. Sélectionnez l’abonnement résidant dans votre VNets.

        Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName
        Select-AzureSubscription -SubscriptionName "<Subscription Name>"

3. Créez les connexions. Dans les exemples, notez que la clé partagée est exactement identiques. La clé partagée doit toujours correspondre.


    VNet1 vers VNet2 connexion

        Set-AzureVNetGatewayKey -VNetName VNet1 -LocalNetworkSiteName VNet2Local -SharedKey A1b2C3D4

    VNet2 vers VNet1 connexion

        Set-AzureVNetGatewayKey -VNetName VNet2 -LocalNetworkSiteName VNet1Local -SharedKey A1b2C3D4

4. Attendez que les connexions initialisation. Une fois que la passerelle a initialisé, la passerelle ressemble à l’illustration suivante.

    ![État de la passerelle - connecté](./media/virtual-networks-configure-vnet-to-vnet-connection/IC736059.jpg)  

    [AZURE.INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)] 

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez ajouter des machines virtuelles à vos réseaux virtuel. Consultez la [documentation de Machines virtuelles](https://azure.microsoft.com/documentation/services/virtual-machines/) pour plus d’informations.



[1]: ../hdinsight-hbase-geo-replication-configure-vnets.md
[2]: http://channel9.msdn.com/Series/Getting-started-with-Windows-Azure-HDInsight-Service/Configure-the-VPN-connectivity-between-two-Azure-virtual-networks
 
