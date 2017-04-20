<properties 
   pageTitle="Configurer forcé tunnel pour les connexions de Site à l’aide du modèle de déploiement classique | Microsoft Azure"
   description="Comment rediriger ou « force » tout le trafic Internet liées aux revenir à votre emplacement local."
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
   ms.date="08/10/2016"
   ms.author="cherylmc" />

# <a name="configure-forced-tunneling-using-the-classic-deployment-model"></a>Configurer tunnel forcé à l’aide du modèle de déploiement classique

> [AZURE.SELECTOR]
- [PowerShell - classique](vpn-gateway-about-forced-tunneling.md)
- [PowerShell - Gestionnaire de ressources](vpn-gateway-forced-tunneling-rm.md)

Tunnel forcé vous permet de rediriger ou « force « tout le trafic Internet liées aux retourner à votre emplacement local via un tunnel VPN de Site à pour inspection et l’audit. Il s’agit d’une condition de sécurité critiques pour la plupart des entreprises informatiques stratégies. 

Sans tunnel forcé, le trafic Internet lié à partir de vos ordinateurs virtuels dans Azure parcourt toujours à partir de l’infrastructure réseau Azure directement à Internet, sans l’option vous permet d’inspecter ou de vérifier le trafic. Accès Internet non autorisés susceptibles d’entraîner divulgation d’informations ou d’autres types de violations de sécurité.

Cet article vous guidera configuration forcé tunnel pour les réseaux virtuels créés à l’aide du modèle de déploiement classique. 

**À propos des modèles de déploiement d’Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

**Modèles de déploiement et d’outils pour tunnel forcé**

Une connexion tunnel forcée peut être configurée pour le modèle de déploiement classique et le modèle de déploiement du Gestionnaire de ressources. Consultez le tableau suivant pour plus d’informations. Nous apportez ce tableau nouveaux articles, des nouveaux modèles de déploiement et des outils supplémentaires sont disponibles pour cette configuration. Lorsqu’un article est disponible, nous attacher directement à partir de la table.

[AZURE.INCLUDE [vpn-gateway-forcedtunnel](../../includes/vpn-gateway-table-forcedtunnel-include.md)] 


## <a name="requirements-and-considerations"></a>Configuration requise et remarques

Tunnel forcé dans Azure est configuré via les itinéraires réseau virtuel définis par l’utilisateur (UDR). Rediriger le trafic vers un site local est exprimé sous forme d’un itinéraire par défaut à la passerelle VPN Azure. La section suivante répertorie la limite actuelle de la table et des itinéraires routage pour un réseau virtuel Azure :


-  Chaque sous-réseau réseau virtuel a une table de routage système prédéfini. La table de routage système comporte les trois groupes d’itinéraires suivants :

    - **VNet local achemine :** Directement à la destination machines virtuelles dans le même réseau virtuel
    
    - **Sur itinéraires locaux :** À la passerelle VPN Azure
    
    - **Itinéraire par défaut :** Directement à Internet. Paquets destinés aux adresses IP privés non couverts par les deux itinéraires précédente seront supprimées.


-  Dans la version de route définis par l’utilisateur, vous pouvez créer une table de routage pour ajouter un itinéraire par défaut et puis associer la table de routage à votre sous-réseau VNet pour activer tunnel forcé sur ces sous-réseaux.

- Vous devez définir un « site par défaut « parmi les sites locaux croisée local connecté au réseau virtuel.

- Tunnel forcé doit être associée à un VNet qui dispose d’une passerelle VPN routage dynamique (pas une passerelle statique).
 
- ExpressRoute forcé tunnel n’est pas configuré grâce à ce mécanisme, mais à la place, est activé en publiant un itinéraire par défaut via les sessions homologation ExpressRoute BGP. Consultez la [Documentation ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/) pour plus d’informations.



## <a name="configuration-overview"></a>Présentation de la configuration

Dans l’exemple suivant, en tunnel Frontend sous-réseau n’est pas forcé. Les charges de travail dans le sous-réseau Frontend peuvent continuer à accepter et répondre aux demandes des clients directement à partir d’Internet. Les sous-réseaux milieu et le serveur principal sont forcés en tunnel. Toutes les connexions sortantes de ces deux sous réseaux à Internet doit être forcées ou redirigées vers un site local via une du tunnel VPN S2S.

Cela vous permet restreindre et inspecter accès à Internet à partir de vos machines virtuelles ou cloud services dans Azure, tout en continuant à activer votre architecture à plusieurs niveaux service obligatoire. Vous pouvez également appliquer tunnel forcé vers les réseaux virtuels entières si il n’y a aucune charges de travail avec accès Internet dans vos réseaux virtuel.


![Forcé tunnel](./media/vpn-gateway-about-forced-tunneling/forced-tunnel.png)



## <a name="before-you-begin"></a>Avant de commencer

Vérifiez que vous avez les éléments suivants avant de commencer la configuration.

- Un abonnement Azure. Si vous n’avez pas un abonnement Azure, vous pouvez activer votre [avantages d’abonné MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrivez-vous vers le haut pour un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/).

- Un réseau virtuel configuré. 

- La dernière version des applets de commande PowerShell Azure. Découvrez [comment installer et configurer Azure PowerShell](../powershell-install-configure.md) pour plus d’informations sur l’installation les applets de commande PowerShell.


## <a name="configure-forced-tunneling"></a>Configurer tunnel forcé

La procédure suivante vous permettra de spécifier tunnel forcé pour un réseau virtuel. Les étapes de configuration correspondent au fichier de configuration réseau VNet.



    <VirtualNetworkSite name="MultiTier-VNet" Location="North Europe">
     <AddressSpace>
      <AddressPrefix>10.1.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Frontend">
            <AddressPrefix>10.1.0.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Midtier">
            <AddressPrefix>10.1.1.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Backend">
            <AddressPrefix>10.1.2.0/23</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.1.200.0/28</AddressPrefix>
          </Subnet>
        </Subnets>
        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="DefaultSiteHQ">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch1">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch2">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch3">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
        </Gateway>
      </VirtualNetworkSite>
    </VirtualNetworkSite>

Dans cet exemple, le réseau virtuel « MULTICOUCHE VNet » a trois sous-réseaux : sous-réseaux *Frontend*, *milieu de gamme*et *serveur principal* , avec les connexions de quatre locaux croisée : *DefaultSiteHQ*et trois *Branches*. 

Les étapes définir le *DefaultSiteHQ* en tant que la connexion de site par défaut pour forcé tunnel et configurer milieu de gamme et sous-réseaux principal à utiliser forcé tunnel.


1. Créer une table de routage. Utiliser l’applet de commande suivante pour créer votre table de routage.

        New-AzureRouteTable –Name "MyRouteTable" –Label "Routing Table for Forced Tunneling" –Location "North Europe"

2. Ajoutez un itinéraire par défaut pour la table de routage. 

    L’exemple suivant ajoute un itinéraire par défaut à la table de routage créée à l’étape 1. Notez que l’itinéraire uniquement pris en charge est le préfixe destination « 0.0.0.0/0 » pour le saut suivant « VPNGateway ».
 
        Get-AzureRouteTable -Name "MyRouteTable" | Set-AzureRoute –RouteTable "MyRouteTable" –RouteName "DefaultRoute" –AddressPrefix "0.0.0.0/0" –NextHopType VPNGateway

3. Associer la table de routage vers les sous-réseaux. 

    Après une table de routage est créée et un itinéraire ajouté, utilisez l’exemple suivant pour ajouter ou associer la table de routage vers un sous-réseau VNet. L’exemple ajoute la table d’itinéraires « MyRouteTable » vers les sous-réseaux du milieu de gamme et serveur principal de VNet multiniveau-VNet.

        Set-AzureSubnetRouteTable -VirtualNetworkName "MultiTier-VNet" -SubnetName "Midtier" -RouteTableName "MyRouteTable"

        Set-AzureSubnetRouteTable -VirtualNetworkName "MultiTier-VNet" -SubnetName "Backend" -RouteTableName "MyRouteTable"

4. Affecter un site par défaut pour forcé tunnel. 

    Dans l’étape précédente, les exemples de scripts applet de commande créé la table de routage et associé à la table d’itinéraires à deux des sous-réseaux VNet. La dernière étape consiste à sélectionner un site local parmi les connexions de plusieurs sites du réseau virtuel comme site par défaut ou tunnel.

        $DefaultSite = @("DefaultSiteHQ")
        Set-AzureVNetGatewayDefaultSite –VNetName "MultiTier-VNet" –DefaultSite "DefaultSiteHQ"

## <a name="additional-powershell-cmdlets"></a>Applets de commande PowerShell supplémentaires

### <a name="to-delete-a-route-table"></a>Pour supprimer une table de routage

    Remove-AzureRouteTable -Name <routeTableName>

### <a name="to-list-a-route-table"></a>À la liste une table de routage

    Get-AzureRouteTable [-Name <routeTableName> [-DetailLevel <detailLevel>]]

### <a name="to-delete-a-route-from-a-route-table"></a>Pour supprimer un itinéraire à partir d’une table de routage

    Remove-AzureRouteTable –Name <routeTableName>

### <a name="to-remove-a-route-from-a-subnet"></a>Pour supprimer un itinéraire à partir d’un sous-réseau

    Remove-AzureSubnetRouteTable –VirtualNetworkName <virtualNetworkName> -SubnetName <subnetName>

### <a name="to-list-the-route-table-associated-with-a-subnet"></a>Liste de la table de routage associée à un sous-réseau
    
    Get-AzureSubnetRouteTable -VirtualNetworkName <virtualNetworkName> -SubnetName <subnetName>

### <a name="to-remove-a-default-site-from-a-vnet-vpn-gateway"></a>Pour supprimer un site par défaut d’une passerelle VNet VPN

    Remove-AzureVnetGatewayDefaultSite -VNetName <virtualNetworkName>






