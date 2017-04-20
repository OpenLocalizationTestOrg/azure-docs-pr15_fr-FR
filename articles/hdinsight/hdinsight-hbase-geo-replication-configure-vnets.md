<properties 
   pageTitle="Configurer la connexion VPN entre deux réseaux virtuels | Microsoft Azure" 
   description="Découvrez comment configurer les connexions VPN et résolution de noms de domaine entre deux réseaux virtuels Azure et comment configurer HBase geo-réplication." 
   services="hdinsight,virtual-network" 
   documentationCenter="" 
   authors="mumian" 
   manager="jhubbard" 
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="06/28/2016"
   ms.author="jgao"/>

# <a name="configure-a-vpn-connection-between-two-azure-virtual-networks"></a>Configurer une connexion VPN entre deux réseaux virtuels Azure  

> [AZURE.SELECTOR]
- [Configurer la connectivité VPN](hdinsight-hbase-geo-replication-configure-vnets.md)
- [Configurer le système DNS](hdinsight-hbase-geo-replication-configure-dns.md)
- [Configurer la réplication HBase](hdinsight-hbase-geo-replication.md) 

La connectivité réseau virtuel Azure site à utilise une passerelle VPN pour fournir un tunnel sécurisé à l’aide de Ipsec/IKE. La VNets peut se trouver dans différents abonnements et des régions différentes. Vous pouvez même combiner VNet VNet communication avec des configurations sur plusieurs sites. Il existe plusieurs raisons pour VNet VNet la connectivité :

- Région croisée geo-redondance et geo présence 
- Applications à plusieurs niveaux régionale avec la limite d’isolement forte 
- Croisées abonnement, communication entre organisation dans Azure

Pour plus d’informations, voir [configurer un VNet à VNet connexion](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md). 

Pour l’afficher sur la vidéo :

> [AZURE.VIDEO configure-the-vpn-connectivity-between-two-azure-virtual-networks]

Ce didacticiel est une partie de la [série] [ hdinsight-hbase-replication] sur la création de réplication geo HBase. 

- Configurer une connectivité VPN entre deux réseaux virtuels (ce didacticiel)
- [Configurer le système DNS pour les réseaux virtuels][hdinsight-hbase-geo-replication-dns]
- [Configurer la réplication de geo HBase][hdinsight-hbase-geo-replication]

Le diagramme suivant illustre les deux réseaux virtuels que vous allez créer dans ce didacticiel :

![Diagramme de réseau virtuel la réplication HDInsight HBase][img-vnet-diagram]
 

##<a name="prerequisites"></a>Conditions préalables
Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- **Azure un abonnement**. Voir [Azure obtenir la version d’évaluation gratuite](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Un poste de travail avec Azure PowerShell**.

    Avant d’exécuter des scripts PowerShell, vérifiez que vous êtes connecté à votre abonnement Azure à l’aide de l’applet de commande suivante :

        Add-AzureAccount

    Si vous avez plusieurs abonnements Azure, utilisez l’applet de commande suivante pour définir l’abonnement actif :

        Select-AzureSubscription <AzureSubscriptionName>
        
    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]


>[AZURE.NOTE] Noms de service Azure et machine virtuelle doit être unique. Le nom utilisé dans ce didacticiel est Contoso-[nom Azure Service/machine virtuelle]-[UE / États-Unis]. Par exemple, Contoso-VNet-UE est le réseau virtuel Azure dans le centre de données Europe du Nord ; Contoso-DNS-US est le serveur DNS machine virtuelle dans le centre de données américain Extrême-Orient. Vous devez parvenir à vos propres noms.
 

##<a name="create-two-azure-vnets"></a>Créer deux VNets Azure



**Pour créer un réseau virtuel appelé Contoso-VNet-UE en Amérique du Nord Europe**

1.  Connectez-vous au [portail classique Azure][azure-portal].
2.  Cliquez sur **Nouveau**, **SERVICES réseau**, **réseau virtuel**, **créer personnalisé**.
3.  Entrez :

    - **Nom**: Contoso-VNet-UE
    - **Emplacement**: Europe du Nord

        Ce didacticiel utilise Europe du Nord et US Extrême-Orient centres de données. Vous pouvez choisir votre propre centres de données.
4.  Entrez :

    - **Serveur DNS**: (laissez ce champ vide) 
    
        Vous avez besoin de votre serveur DNS pour la résolution de noms dans les réseaux virtuels. Pour plus d’informations sur le moment pour utiliser la résolution nom Azure fourni et quand utiliser votre propre serveur DNS, voir [Résolution de noms (DNS)](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md). Pour obtenir des instructions configurer la résolution de noms entre VNets, voir [Configurer DNS entre deux réseaux virtuels Azure][hdinsight-hbase-dns].
  
    - **Configurer un réseau privé virtuel point-à-site**: (désactivée)

        Point-à-site ne s’applique pas à ce scénario.

    - **Configurer un réseau privé virtuel de site à**: (désactivée)
    
        Vous allez configurer la connexion VPN de site au réseau virtuel Azure dans le centre de données américain Extrême-Orient.
5.  Entrez :

    -   **Adresse IP de départ espace**: 10.1.0.0
    -   **Adresse espace CIDR**: / 16
    -   **Démarrage sous-réseau 1 IP**: 10.1.0.0
    -   **CIDR sous-réseau-1**: / 24

    L’espace d’adressage pouvez chevauche pas le réseau virtuel US.  

**Pour créer un réseau virtuel appelé Contoso-VNet-UE dans ouest Europe**

- Répéter la dernière procédure avec les valeurs suivantes :

    - **Nom**: Contoso-VNet-US
    - **Emplacement**: US Extrême-Orient
     
    - **Serveur DNS**: (laissez ce champ vide)
    - **Configurer un réseau privé virtuel point-à-site**: (désactivée)
    - **Configurer un réseau privé virtuel de site à**: (désactivée)
     
    - **Adresse IP de départ espace**: 10.2.0.0
    - **Adresse espace CIDR**: / 16
    - **Démarrage sous-réseau 1 IP**: 10.2.0.0
    - **CIDR sous-réseau-1**: / 24

















##<a name="configure-a-vpn-connection-between-the-two-vnets"></a>Configurer une connexion VPN entre les deux VNets

###<a name="create-local-networks"></a>Créer des réseaux locaux

Lorsque vous créez un VNet VNet configuration, vous devez configurer chaque VNet pour identifier les uns des autres comme site de réseau local. Dans cette section, vous devez configurer chaque VNet comme un réseau local. Les réseaux locales partagent les espaces d’adresses IP mêmes avec la VNet correspondante.

![Configuration des VPN Azure site-à - azure réseaux locaux][img-vnet-lnet-diagram]


**Pour créer un réseau local appelée Contoso-LNet-UE correspondant à l’espace d’adressage réseau Contoso-VNet-UE**

1. À partir du portail classique Azure, cliquez sur **Nouveau**, **SERVICES réseau**, **Réseau virtuel**, **Ajouter un réseau LOCAL**.
3. Entrez :

    - **Nom**: Contoso-LNet-UE
    - **Adresse IP du périphérique VPN**: 192.168.0.1 (cette adresse est mise à jour ultérieurement)

        En règle générale, vous utiliserez l’adresse IP externe réel pour un périphérique VPN. Pour VNet VNet configurations, vous allez utiliser l’adresse IP de passerelle VPN. Étant donné que vous n’avez créé les passerelles VPN pour les deux VNets encore, vous entrez une adresse IP arbitraires et revenez à fix it.
4.  Entrez :

    - **Adresse IP de départ espace :** 10.1.0.0
    - **Adresse espace CIDR :** /16
    
    Cela doit correspondre exactement à la plage que vous avez spécifiée précédemment pour Contoso-VNet-EU.

**Pour créer un réseau local appelée Contoso-LNet-US correspondant à l’espace d’adressage réseau Contoso-VNet-US**

- Répéter la dernière procédure avec les paramètres suivants :

    - **Nom**: Contoso-LNet-US
    - **Adresse IP du périphérique VPN**: 192.168.0.1 (cette adresse est mise à jour ultérieurement)
     
    - **Adresse IP de départ espace**: 10.2.0.0
    - **Adresse espace CIDR**: / 16


###<a name="create-vpn-gateways"></a>Créer des passerelles VPN

Il existe deux parties dans cette configuration. Tout d’abord vous configurez une connexion de site à VNet à un réseau local, puis vous créez un réseau privé virtuel routage dynamique. VNet à VNet nécessite des passerelles VPN Azure avec VPN routage dynamiques. Azure VPN routage statiques ne sont pas pris en charge.

**Pour configurer la connexion de site à Contoso-VNet-UE Contoso-LNet-US**

1.  À partir du portail classique Azure, cliquez sur **les réseaux** dans le volet gauche,
2.  Cliquez sur **Contoso-VNet-UE**.
3.  Cliquez sur l’onglet **CONFIGUE** .
4.  Consultez **se connecter au réseau local**.
5.  Dans le **Réseau LOCAL**, sélectionnez **Contoso-LNet-US**.
6.  Cliquez sur **sous-réseau de passerelle ajouter** dans la section réseau virtuel adresse espaces.
7.  Cliquez sur **Enregistrer**.
8.  Cliquez sur **OK** pour confirmer.


**Pour créer une passerelle VPN pour Contoso-VNet-EU**

1.  À partir du portail classique Azure, cliquez sur l’onglet **tableau de bord** .
4.  Cliquez sur **Créer une passerelle** en bas de la page, puis cliquez sur **Le routage dynamique**.
5.  Cliquez sur **Oui** pour confirmer. Vous pouvez remarquer le graphique de la passerelle dans la page change au jaune indique la création d’une passerelle. Il faut généralement environ 15 minutes pour que la passerelle à créer.

    Lorsque l’état de la passerelle prend la connexion, l’adresse IP pour chaque passerelle seront visible dans le tableau de bord. Notez l’adresse IP correspondant à chaque VNet, en prenant soin de ne pas pour les mélanger. Il s’agit des adresses IP qui seront utilisés lorsque vous modifiez vos adresses IP espace réservé pour le périphérique VPN réseaux locaux.

6.  Créez une copie de l' **Adresse IP de passerelle**. Vous allez utiliser celle-ci pour configurer l’adresse IP de passerelle VPN pour Contoso-VNet-EU dans la section suivante.

**Pour créer une passerelle VPN pour Contoso-VNet-EU**

- Répétez cette procédure pour configurer la connectivité de site à Contoso-VNet-US à Contoso-LNet-Union européenne et la boîte une passerelle VPN pour Contoso-Vnet-US deux derniers. Lorsque vous avez terminé, vous disposerez de l’adresse IP de passerelle VPN pour Contoso-VNet-US.


### <a name="set-the-vpn-device-ip-addresses-for-local-networks"></a>Définir le périphérique VPN adresses IP pour les réseaux locaux
Dans la dernière section, vous créez une passerelle VPN pour chacun de la VNets. Vous voyez les adresses IP des passerelles VPN. Maintenant que vous pouvez revenir à configurer les adresses IP de réseau local VPN appareil.

**Pour configurer l’adresse IP du périphérique VPN pour Contoso-LNet-UE** 

1.  À partir du portail classique Azure, cliquez sur **les réseaux** dans le volet gauche.
2.  Cliquez sur **Réseaux locaux** à partir du haut.
3.  Cliquez sur **Contoso-LNet-UE**, puis cliquez sur **Modifier** en bas.
4.  Mettre à jour **adresse IP du périphérique VPN**.  Il s’agit de l’adresse que vous obtenez à partir de l’onglet tableau de bord de Contoso-VNET-UE.
5.  Cliquez sur le bouton droit.
6.  Cliquez sur le bouton de vérification.

**Pour configurer l’adresse IP du périphérique VPN pour Contoso-LNet-US** 

- Répéter la dernière procédure pour configurer l’adresse IP du périphérique VPN pour Contoso-LNet-US.

###<a name="set-vnet-gateway-keys"></a>Définir des clés de passerelle VNet

Les passerelles Vnet utilisent une clé partagée pour authentifier les connexions entre les réseaux virtuels. La clé ne peuvent pas être configurée à partir du portail classique Azure. Vous devez utiliser PowerShell ou kit de développement .NET.

**Pour définir les touches**

1. À partir de votre poste de travail, ouvrez **Windows PowerShell ISE** ou la console Windows PowerShell.
2. Mettre à jour les paramètres de ce script de suivi et exécutez-le :

        Add-AuzreAccount
        Select-AzureSubscription -[AzureSubscriptionName]
        Set-AzureVNetGatewayKey -VNetName ContosoVNet-EU -LocalNetworkSiteName Contoso-LNet-US -SharedKey A1b2C3D4
        Set-AzureVNetGatewayKey -VNetName ContosoVNet-US -LocalNetworkSiteName Contoso-LNet-EU -SharedKey A1b2C3D4 


##<a name="check-the-vpn-connection"></a>Vérifiez la connexion VPN 

Sans les ordinateurs virtuels déployés à la VNets, vous pouvez utiliser le diagramme visuel réseau virtuel la page tableau de bord VNet sur le portail classique Azure pour vérifier l’état de connexion :

![Réseau virtuel HDInsight HBase réplication état de la connexion VPN][img-vpn-status]
  



##<a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à configurer une connexion VPN entre deux réseaux virtuels Azure. Porter sur les deux autres articles de la série :

- [Configurer le système DNS entre deux réseaux virtuels Azure][hdinsight-hbase-geo-replication-dns]
- [Configurer la réplication de geo HBase][hdinsight-hbase-geo-replication]



[hdinsight-hbase-geo-replication-dns]: hdinsight-hbase-geo-replication-configure-dns.md
[hdinsight-hbase-geo-replication]: hdinsight-hbase-geo-replication.md


[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-portal]: https://portal.azure.com

[powershell-install]: ../install-configure-powershell.md



[hdinsight-hbase-replication]: hdinsight-hbase-geo-replication.md
[hdinsight-hbase-dns]: hdinsight-hbase-geo-replication-configure-dns.md


[img-vnet-diagram]: ./media/hdinsight-hbase-geo-replication-configure-vnets/hdinsight-hbase-vpn-diagram.png
[img-vnet-lnet-diagram]: ./media/hdinsight-hbase-geo-replication-configure-vnets/hdinsight-hbase-vpn-lnet-diagram.png
[img-vpn-status]: ./media/hdinsight-hbase-geo-replication-configure-vnets/hdinsight-hbase-vpn-status.png 
