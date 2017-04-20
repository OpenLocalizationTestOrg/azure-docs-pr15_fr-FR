<properties 
   pageTitle="Connectez un réseau virtuel à plusieurs sites à l’aide de la passerelle VPN et PowerShell | Microsoft Azure"
   description="Cet article vous guidera connexion plusieurs sites locaux local à un réseau virtuel à l’aide d’une passerelle VPN pour le modèle de déploiement classique."
   services="vpn-gateway"
   documentationCenter="na"
   authors="yushwang"
   manager="rossort"
   editor=""
   tags="azure-service-management"/>

<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/11/2016"
   ms.author="yushwang" />

# <a name="add-a-site-to-site-connection-to-a-vnet-with-an-existing-vpn-gateway-connection"></a>Ajouter une connexion à un Site à un VNet avec une connexion de passerelle VPN existante

> [AZURE.SELECTOR]
- [Responsable de ressources - portail](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
- [Classique - PowerShell](vpn-gateway-multi-site.md)

Cet article décrit l’utilisation de PowerShell pour ajouter des connexions de Site à Site (S2S) pour une passerelle VPN qui a une connexion existante. Ce type de connexion est souvent appelé une configuration « comportant plusieurs sites ». 

Cet article s’applique aux réseaux virtuels créés à l’aide du modèle de déploiement classique (également appelé gestion des services). Ces étapes ne s’appliquent pas aux configurations de connexion pouvant coexistence ExpressRoute/Site-à-Site. Pour plus d’informations sur les connexions pouvant coexistence, voir [connexions pouvant coexistence ExpressRoute/S2S](../expressroute/expressroute-howto-coexist-classic.md) .

### <a name="deployment-models-and-methods"></a>Méthodes et modèles de déploiement

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

Nous mettre à jour ce tableau en tant que nouveaux articles et outils supplémentaires sont disponibles pour cette configuration. Lorsqu’un article est disponible, nous attacher directement à partir de ce tableau.

[AZURE.INCLUDE [vpn-gateway-table-multi-site](../../includes/vpn-gateway-table-multisite-include.md)] 



## <a name="about-connecting"></a>Sur la connexion

Vous pouvez vous connecter à plusieurs sites en local à un seul réseau virtuel. Il s’agit particulièrement attrayant pour créer des solutions de cloud hybride. Création d’une connexion de plusieurs sites à votre passerelle réseau virtuel Azure est très similaire à la création d’autres connexions de Site à. En fait, vous pouvez utiliser une passerelle VPN Azure existante, dans la mesure où la passerelle est dynamique (basée sur un itinéraire).

Si vous disposez déjà d’une passerelle statique connectée à votre réseau virtuel, vous pouvez modifier le type de passerelle dynamique sans avoir à recréer le réseau virtuel pour prendre en charge de plusieurs sites. Avant de modifier le type de routage, assurez-vous que votre passerelle VPN local prend en charge les configurations de VPN basées sur le routage. 

![diagramme de plusieurs sites] (./media/vpn-gateway-multi-site/multisite.png "plusieurs sites")

## <a name="points-to-consider"></a>Points à prendre en considération

**Vous ne pourrez pas utiliser le portail classique Azure pour apporter des modifications à ce réseau virtuel.** Pour cette version, vous devez apporter des modifications au fichier de configuration réseau au lieu d’utiliser le portail classique Azure. Si vous apportez des modifications dans le portail classique Azure, ils allez remplacer vos paramètres de référence de plusieurs sites pour ce réseau virtuel. 

Vous pouvez confortable à l’aide du fichier de configuration du réseau au moment où que vous avez terminé la procédure de plusieurs sites. Toutefois, si vous avez plusieurs personnes travaillent sur la configuration de votre réseau, vous devez vous assurer que tout le monde connaît cette limitation. Cela ne signifie que vous ne pouvez pas utiliser le portail classique Azure du tout. Vous pouvez l’utiliser pour le reste, à l’exception d’apporter des modifications de configuration à ce réseau virtuel particulier.

## <a name="before-you-begin"></a>Avant de commencer

Avant de commencer la configuration, vérifiez que vous disposez des éléments suivants :

- Un abonnement Azure. Si vous n’avez pas un abonnement Azure, vous pouvez activer votre [avantages d’abonné MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrivez-vous vers le haut pour un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/).

- Compatibilité du matériel VPN pour chaque local emplacement. Vérifiez que [Sur les périphériques VPN pour la connectivité réseau virtuel](vpn-gateway-about-vpn-devices.md) pour vérifier si le périphérique que vous souhaitez utiliser un élément est connue pour être compatibles.

- Adresse IP IPv4 publique tourné vers l’extérieur pour chaque périphérique VPN. L’adresse IP ne peut pas être situé derrière un NAT. Il s’agit d’exigence.

- Vous devez installer la dernière version des applets de commande PowerShell Azure. Découvrez [comment installer et configurer Azure PowerShell](../powershell-install-configure.md) pour plus d’informations sur l’installation les applets de commande PowerShell.

- Une personne qui est expert dans la configuration de votre matériel VPN. Vous ne pourrez pas utiliser les scripts VPN généré à partir du portail classique Azure pour configurer vos périphériques VPN. Cela signifie que vous devez avoir une bonne maîtrise de la façon de configurer votre périphérique VPN, ou de travailler avec un utilisateur.

- Les plages d’adresses IP que vous voulez utiliser pour votre réseau virtuel (si vous n’avez pas déjà créé un). 

- Les plages d’adresses IP pour chacun des sites réseau local que vous êtes connecté à. Vous devez vous assurer que l’adresse IP s’étend pour chacun des sites réseau local que vous voulez vous connecter pour ne se chevauchent ne pas. Dans le cas contraire, le portail classique Azure ou l’API REST refuse la configuration en cours de téléchargement. 

    Par exemple, si vous avez deux sites réseau local que les deux contiennent le 10.2.3.0/24 IP adresse plage et vous disposez d’un package avec une adresse de destination 10.2.3.3, Azure ne savez quel site que vous voulez envoyer le package, car les plages d’adresses sont chevauchent. Pour éviter des problèmes de routage, Azure ne vous permet de télécharger un fichier de configuration qui comporte les plages qui se chevauchent.



## <a name="1-create-a-site-to-site-vpn"></a>1. créer un réseau privé virtuel de Site à

Si vous avez déjà un réseau privé virtuel de Site à avec une passerelle routage dynamique, parfait ! Vous pouvez procéder à [Exporter les paramètres de configuration réseau virtuel](#export). Dans le cas contraire, procédez comme suit :

### <a name="if-you-already-have-a-site-to-site-virtual-network-but-it-has-a-static-policy-based-routing-gateway"></a>Si vous disposez déjà d’un réseau virtuel à un Site, mais il dispose d’une passerelle de routage statique (basée sur des règles) :

1. Modifier le type de passerelle pour le routage dynamique. Un réseau privé virtuel sur plusieurs sites requiert une passerelle de routage dynamique (également appelé itinéraire basées). Pour modifier le type de passerelle, vous devez d’abord supprimer la passerelle existante, puis créer un autre. Pour obtenir des instructions, voir [comment modifier le type de routage VPN de la passerelle](../vpn-gateway/vpn-gateway-configure-vpn-gateway-mp.md#how-to-change-the-vpn-routing-type-for-your-gateway).  

2. Configurez votre nouvelle passerelle et créez votre tunnel VPN. Pour plus d’informations, voir [configurer une passerelle VPN dans le portail classique Azure](vpn-gateway-configure-vpn-gateway-mp.md). Tout d’abord, modifiez le type de passerelle pour le routage dynamique. 

### <a name="if-you-dont-have-a-site-to-site-virtual-network"></a>Si vous n’avez pas un réseau virtuel de Site à :

1. Créer votre réseau virtuel de Site à utiliser ces instructions : [créer un réseau virtuel avec une connexion VPN Site à Site dans le portail classique Azure](vpn-gateway-site-to-site-create.md).  

2. Configurer une passerelle routage dynamique à l’aide de ces instructions : [configurer une passerelle VPN](vpn-gateway-configure-vpn-gateway-mp.md). Veillez à sélectionner **le routage dynamique** correspondant à votre type de passerelle.

## <a name="export"></a>2. exporter le fichier de configuration du réseau 

Exporter votre fichier de configuration réseau. Le fichier que vous exportez permettant de configurer vos nouveaux paramètres de plusieurs sites. Si vous avez besoin d’obtenir des instructions sur l’exportation d’un fichier, consultez la section dans l’article : [comment créer un VNet à l’aide d’un fichier de configuration réseau dans le portail Azure](../virtual-network/virtual-networks-create-vnet-classic-portal.md#how-to-create-a-vnet-using-a-network-config-file-in-the-azure-portal). 

## <a name="3-open-the-network-configuration-file"></a>3. Ouvrez le fichier de configuration du réseau

Ouvrez le fichier de configuration de réseau que vous avez téléchargé dans la dernière étape. Utilisez un éditeur xml que vous souhaitez. Le fichier doit ressembler à ce qui suit :

        <NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
          <VirtualNetworkConfiguration>
            <LocalNetworkSites>
              <LocalNetworkSite name="Site1">
                <AddressSpace>
                  <AddressPrefix>10.0.0.0/16</AddressPrefix>
                  <AddressPrefix>10.1.0.0/16</AddressPrefix>
                </AddressSpace>
                <VPNGatewayAddress>131.2.3.4</VPNGatewayAddress>
              </LocalNetworkSite>
              <LocalNetworkSite name="Site2">
                <AddressSpace>
                  <AddressPrefix>10.2.0.0/16</AddressPrefix>
                  <AddressPrefix>10.3.0.0/16</AddressPrefix>
                </AddressSpace>
                <VPNGatewayAddress>131.4.5.6</VPNGatewayAddress>
              </LocalNetworkSite>
            </LocalNetworkSites>
            <VirtualNetworkSites>
              <VirtualNetworkSite name="VNet1" AffinityGroup="USWest">
                <AddressSpace>
                  <AddressPrefix>10.20.0.0/16</AddressPrefix>
                  <AddressPrefix>10.21.0.0/16</AddressPrefix>
                </AddressSpace>
                <Subnets>
                  <Subnet name="FE">
                    <AddressPrefix>10.20.0.0/24</AddressPrefix>
                  </Subnet>
                  <Subnet name="BE">
                    <AddressPrefix>10.20.1.0/24</AddressPrefix>
                  </Subnet>
                  <Subnet name="GatewaySubnet">
                    <AddressPrefix>10.20.2.0/29</AddressPrefix>
                  </Subnet>
                </Subnets>
                <Gateway>
                  <ConnectionsToLocalNetwork>
                    <LocalNetworkSiteRef name="Site1">
                      <Connection type="IPsec" />
                    </LocalNetworkSiteRef>
                  </ConnectionsToLocalNetwork>
                </Gateway>
              </VirtualNetworkSite>
            </VirtualNetworkSites>
          </VirtualNetworkConfiguration>
        </NetworkConfiguration>

## <a name="4-add-multiple-site-references"></a>4. Ajoutez plusieurs références à des sites

Lorsque vous ajoutez ou supprimez des informations de référence de site, vous devez apporter des modifications de configuration pour la ConnectionsToLocalNetwork/LocalNetworkSiteRef. Ajout d’un nouveau site local référence déclenchée Azure pour créer un nouveau tunnel. Dans l’exemple ci-dessous, la configuration réseau est d’une connexion de site unique. Enregistrez le fichier une fois que vous avez terminé d’apporter vos modifications.

        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>

    To add additional site references (create a multi-site configuration), simply add additional "LocalNetworkSiteRef" lines, as shown in the example below: 

        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Site2"><Connection type="IPsec" /></LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>

## <a name="5-import-the-network-configuration-file"></a>5. importer le fichier de configuration du réseau

Importer le fichier de configuration réseau. Lorsque vous importez ce fichier avec les modifications, le nouveau tunnel sera ajouté. Le tunnel utilise la passerelle dynamique que vous avez créé précédemment. Si vous devez savoir comment importer le fichier, reportez-vous à la section dans l’article : [comment créer un VNet à l’aide d’un fichier de configuration réseau dans le portail Azure](../virtual-network/virtual-networks-create-vnet-classic-portal.md#how-to-create-a-vnet-using-a-network-config-file-in-the-azure-portal). 

## <a name="6-download-keys"></a>6. télécharger des touches

Après avoir ajouté votre nouveau tunnel, utilisez l’applet de commande PowerShell `Get-AzureVNetGatewayKey` pour obtenir les clés partagées IPsec/IKE pour chaque tunnel.

Par exemple :

    Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site1"

    Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site2"

Si vous préférez, vous pouvez également utiliser l’API REST *Obtenir virtuel réseau partagé clé de passerelle* pour obtenir les touches partagées.

## <a name="7-verify-your-connections"></a>7. Vérifiez que vos connexions

Vérifiez l’état de plusieurs sites tunnel. Après avoir téléchargé les clés pour chaque tunnel, vous souhaiterez vérifier les connexions. Utiliser `Get-AzureVnetConnection` pour obtenir une liste de tunnel réseau virtuel, comme illustré dans l’exemple ci-dessous. VNet1 est le nom de la VNet.

    Get-AzureVnetConnection -VNetName VNET1
        
    ConnectivityState         : Connected
    EgressBytesTransferred    : 661530
    IngressBytesTransferred   : 519207
    LastConnectionEstablished : 5/2/2014 2:51:40 PM
    LastEventID               : 23401
    LastEventMessage          : The connectivity state for the local network site 'Site1' changed from Not Connected to Connected.
    LastEventTimeStamp        : 5/2/2014 2:51:40 PM
    LocalNetworkSiteName      : Site1
    OperationDescription      : Get-AzureVNetConnection
    OperationId               : 7f68a8e6-51e9-9db4-88c2-16b8067fed7f
    OperationStatus           : Succeeded
        
    ConnectivityState         : Connected
    EgressBytesTransferred    : 789398
    IngressBytesTransferred   : 143908
    LastConnectionEstablished : 5/2/2014 3:20:40 PM
    LastEventID               : 23401
    LastEventMessage          : The connectivity state for the local network site 'Site2' changed from Not Connected to Connected.
    LastEventTimeStamp        : 5/2/2014 2:51:40 PM
    LocalNetworkSiteName      : Site2
    OperationDescription      : Get-AzureVNetConnection
    OperationId               : 7893b329-51e9-9db4-88c2-16b8067fed7f
    OperationStatus           : Succeeded

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les passerelles VPN, voir [à propos de VPN passerelles](../vpn-gateway/vpn-gateway-about-vpngateways.md).

