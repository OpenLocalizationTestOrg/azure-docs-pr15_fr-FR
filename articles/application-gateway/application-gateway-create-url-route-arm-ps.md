<properties
   pageTitle="Créer une passerelle d’application à l’aide de règles de routage URL | Microsoft Azure"
   description="Cette page fournit des instructions pour créer, de configurer une passerelle d’application Azure à l’aide de règles de routage d’URL"
   documentationCenter="na"
   services="application-gateway"
   authors="georgewallace"
   manager="jdial"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/25/2016"
   ms.author="gwallace"/>


# <a name="create-an-application-gateway-using-path-based-routing"></a>Créer une passerelle d’application à l’aide de routage basé sur le chemin d’accès 

> [AZURE.SELECTOR]
- [Portail Azure](application-gateway-create-url-route-portal.md)
- [Azure PowerShell Gestionnaire de ressources](application-gateway-create-url-route-arm-ps.md)

Routage basé sur le chemin d’URL vous permet d’associer itinéraires basés sur le chemin d’URL de demande Http. Il vérifie s’il existe un itinéraire à un pool principale configuré pour les listes d’URL dans une Application passerelle et envoyer le trafic réseau vers le pool de principale défini. Souvent utilisée pour le routage des URL consiste à charger solde demandes de différents types de contenu pour des pools autre serveur principal.

Routage basé sur une URL présente un nouveau type de règle passerelle d’application. La passerelle application propose deux types de règles : base et PathBasedRouting. Type de règle base fournit un service cyclique pour les pools principale lors de la PathBasedRouting en plus de distribution alternées, prend également motif chemin d’accès de l’URL de demande en considération lors du choix du pool principal.

>[AZURE.IMPORTANT] PathPattern : La liste des modèles de chemin d’accès pour mettre en correspondance. Chacun doit commencer par / et le seul endroit un «\*» est autorisé à la fin. Exemples valides sont /xyz, /xyz* ou /xyz/*. La chaîne MAS pour le traitement de chemin d’accès n’inclut pas de texte après le premier « ? » ou « # » et ces caractères ne sont pas autorisés. 

## <a name="scenario"></a>Scénario
Dans l’exemple suivant, Application Gateway sert le trafic de contoso.com avec deux pools serveur principal : pool de serveur vidéo et image serveur pool.

Demandes de http://contoso.com/image* sont routés au pool de serveur image (pool1) et http://contoso.com/video* sont routés vers le pool de serveur vidéo (pool2). Un pool de serveurs par défaut (pool1) est sélectionné si aucun des modèles de chemin d’accès correspond aux.

![itinéraire de l’URL](./media/application-gateway-create-url-route-arm-ps/figure1.png)

## <a name="before-you-begin"></a>Avant de commencer

1. Installer la dernière version des applets de commande PowerShell Azure à l’aide de Web Platform Installer. Vous pouvez télécharger et installer la dernière version de la section **Windows PowerShell** de la [page de téléchargement](https://azure.microsoft.com/downloads/).
2. Vous créez un réseau virtuel et sous-réseau pour passerelle d’Application. Vérifiez qu’aucune machines virtuelles ou des déploiements dans le cloud n’utilisent le sous-réseau. La passerelle d’application elle-même doit être dans un sous-réseau virtuel.
3. Les serveurs ajoutées à la liste principale à utiliser la passerelle d’application doivent exister ou leurs points de terminaison créées dans le réseau virtuel ou avec un public IP/VIP affectées.



## <a name="what-is-required-to-create-an-application-gateway"></a>Ce qui est requis pour créer une passerelle d’application ?


- **Pool du serveur principal :** La liste d’adresses IP des serveurs principale. Présente les adresses IP doivent appartenir soit au sous-réseau virtuel ou doivent être un IP/VIP publique.
- **Paramètres du pool serveur principal :** Chaque pool inclut des paramètres, tels qu’affinité basée sur les cookies, port et protocole. Ces paramètres sont liés à un pool et sont appliquées à tous les serveurs au sein du pool.
- **Port frontal :** Ce port est le port public est ouvert sur la passerelle d’application. Le trafic appuie sur ce port et puis redirigé à un des serveurs principale.
- **Récepteur :** Le récepteur inclut un port frontal, un protocole (Http ou Https, il s’agit des respectant la casse) et le nom du certificat SSL (si la configuration de SSL décharge).
- **Règle :** La règle lie le récepteur, le pool de serveur principal et définit le pool de serveur principal le trafic doit être dirigé vers lorsqu’il rencontre un récepteur particulier.

## <a name="create-an-application-gateway"></a>Créer une passerelle d’application

La différence entre l’utilisation classique Azure et Azure le Gestionnaire de ressources est l’ordre dans lequel vous créez la passerelle d’application et les éléments qui doivent être configurés.

Avec le Gestionnaire de ressources, tous les éléments qui en font une passerelle d’application sont configurés individuellement, puis entrez pour créer la ressource de passerelle d’application.


Voici les étapes sont nécessaires pour créer une passerelle d’application :

1. Créer un groupe de ressources pour le Gestionnaire de ressources.
2. Créer un réseau virtuel, sous-réseau et public IP pour la passerelle d’application.
3. Créer un objet de configuration de passerelle application.
4. Créer une ressource de passerelle d’application.

## <a name="create-a-resource-group-for-resource-manager"></a>Créer un groupe de ressources pour le Gestionnaire de ressources

Vérifiez que vous utilisez la dernière version d’Azure PowerShell. Plus d’informations est disponible à [l’Utilisation de Windows PowerShell avec le Gestionnaire de ressources](../powershell-azure-resource-manager.md).

### <a name="step-1"></a>Étape 1

Se connecter à Azure

    Login-AzureRmAccount

Vous êtes invité à vous authentifier avec vos informations d’identification.<BR>

### <a name="step-2"></a>Étape 2

Vérifier les abonnements pour le compte.

    Get-AzureRmSubscription

### <a name="step-3"></a>Étape 3

Sélectionner les options de vos abonnements Azure à utiliser. <BR>


    Select-AzureRmSubscription -Subscriptionid "GUID of subscription"

### <a name="step-4"></a>Étape 4

Créer un groupe de ressources (ignorer cette étape si vous utilisez un groupe de ressources existant).

    New-AzureRmResourceGroup -Name appgw-RG -Location "West US"

Vous pouvez également vous pouvez également créer des balises pour un groupe de ressources pour la passerelle d’application :
    
    $resourceGroup = New-AzureRmResourceGroup -Name appgw-RG -Location "West US" -Tags @{Name = "testtag"; Value = "Application Gateway URL routing"} 

Le Gestionnaire de ressources Azure requiert que tous les groupes ressources spécifient un emplacement. Il est utilisé comme emplacement par défaut pour les ressources dans ce groupe de ressources. S’assurer que toutes les commandes permettant de créer une passerelle d’application utilisent le même groupe de ressources.

Dans l’exemple ci-dessus, nous avons créé un groupe de ressources appelé « appgw-routage » et un emplacement « Ouest nous ».

>[AZURE.NOTE] Si vous avez besoin configurer une sonde personnalisé pour votre passerelle d’application, voir [créer une passerelle d’application avec sondes personnalisé à l’aide de PowerShell](application-gateway-create-probe-ps.md). Consultez [sondes personnalisé et le contrôle d’état](application-gateway-probe-overview.md) pour plus d’informations.

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Créer un réseau virtuel et un sous-réseau pour la passerelle d’application

L’exemple suivant montre comment créer un réseau virtuel à l’aide du Gestionnaire de ressources.

### <a name="step-1"></a>Étape 1

Affecter la 10.0.0.0/24 plage adresse dans la variable sous-réseau à utiliser pour créer un réseau virtuel.

    $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24


### <a name="step-2"></a>Étape 2

Créer un réseau virtuel nommé « appgwvnet » dans le groupe de ressources « appgw-routage » à l’aide de la 10.0.0.0/16 préfixe avec sous-réseau 10.0.0.0/24 votre région US ouest.

    $vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet


### <a name="step-3"></a>Étape 3

Affecter une variable sous-réseau pour la procédure suivante, qui crée une passerelle d’application.

    $subnet=$vnet.Subnets[0]

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Créer une adresse IP publique pour la configuration frontale

Créez une ressource IP publique « publicIP01 » dans la ressource groupe « appgw-routage » pour la région des États-Unis Ouest.

    $publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-RG -name publicIP01 -location "West US" -AllocationMethod Dynamic

Une adresse IP est affectée à la passerelle d’application au démarrage du service.

## <a name="create-application-gateway-configuration"></a>Créer la configuration de passerelle l’application

Tous les éléments de configuration doivent être configurés avant de créer la passerelle d’application. Les étapes suivantes créent les éléments de configuration sont nécessaires pour une ressource de passerelle d’application.

### <a name="step-1"></a>Étape 1

Créer une configuration IP de passerelle application nommée « gatewayIP01 ». Au démarrage de passerelle d’Application, il sélectionne une adresse IP à partir du sous-réseau configuré et route le trafic réseau aux adresses IP du pool d’IP principale. N’oubliez pas que chaque instance prend une seule adresse IP.


    $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet


### <a name="step-2"></a>Étape 2

Configurer le pool d’adresses IP principale nommé « pool01 » et « pool2 » avec des adresses IP « 134.170.185.46, 134.170.188.221,134.170.185.50 » pour « pool1 » et « 134.170.186.46, 134.170.189.221,134.170.186.50 » pour « pool2 ».


    $pool1 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50

    $pool2 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool02 -BackendIPAddresses 134.170.186.46, 134.170.189.221,134.170.186.50

Dans cet exemple, il existe deux principaux des pools pour acheminer le trafic réseau basée sur le chemin d’URL. Un pool reçoit le trafic de chemin d’URL « / vidéo » et autres pool de recevoir le trafic de chemin d’accès « / image ». Remplacer les adresses IP précédentes pour ajouter vos propres points de terminaison d’adresse IP application. 

### <a name="step-3"></a>Étape 3

Configurer l’application passerelle paramètre « poolsetting01 » et « poolsetting02 » pour le trafic réseau avec équilibrage de charge dans la liste principale. Dans cet exemple, vous configurez les paramètres de pool principale différent pour les pools principale. Chaque pool principale peut avoir son propre paramètre pool principale.

    $poolSetting01 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting01" -Port 80 -Protocol Http -CookieBasedAffinity Disabled -RequestTimeout 120

    $poolSetting02 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting02" -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 240

### <a name="step-4"></a>Étape 4

Configurer l’adresse IP frontal avec point de terminaison IP public.

    $fipconfig01 = New-AzureRmApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip

### <a name="step-5"></a>Étape 5 

Configurer le port frontaux pour une passerelle d’application.

    $fp01 = New-AzureRmApplicationGatewayFrontendPort -Name "fep01" -Port 80
### <a name="step-6"></a>Étape 6

Configurer le récepteur. Cette étape configure le récepteur pour l’adresse IP publique et le port utilisé pour recevoir le trafic réseau entrant. 
 
    $listener = New-AzureRmApplicationGatewayHttpListener -Name "listener01" -Protocol Http -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01

### <a name="step-7"></a>Étape 7 

Configurer les chemins d’accès de la règle URL pour les pools principale. Cette étape configure le chemin d’accès relatif utilisé par passerelle d’application pour définir le mappage entre chemin d’URL et quel pool principale est affectée à gérer le trafic entrant.

L’exemple suivant crée deux règles : une « image / » chemin d’accès le routage du trafic sur principale « pool1 » et une autre pour « / vidéo / » chemin d’accès acheminer le trafic vers principale « pool2 ».
    
    $imagePathRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "pathrule1" -Paths "/image/*" -BackendAddressPool $pool1 -BackendHttpSettings $poolSetting01

    $videoPathRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "pathrule2" -Paths "/video/*" -BackendAddressPool $pool2 -BackendHttpSettings $poolSetting02

La configuration de carte de chemin d’accès de règle configure également un pool d’adresses de principale par défaut si le chemin d’accès ne correspond à aucune des règles de chemin d’accès prédéfinis. 

    $urlPathMap = New-AzureRmApplicationGatewayUrlPathMapConfig -Name "urlpathmap" -PathRules $videoPathRule, $imagePathRule -DefaultBackendAddressPool $pool1 -DefaultBackendHttpSettings $poolSetting02

### <a name="step-8"></a>Étape 8

Créer un paramètre de la règle. Cette étape configure la passerelle d’application pour utiliser le routage basé sur le chemin d’URL.

    $rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType PathBasedRouting -HttpListener $listener -UrlPathMap $urlPathMap

### <a name="step-9"></a>Étape 9

Configurer le nombre d’instances et la taille de la passerelle d’application.

    $sku = New-AzureRmApplicationGatewaySku -Name "Standard_Small" -Tier Standard -Capacity 2

## <a name="create-application-gateway"></a>Créer la passerelle d’Application

Créer une passerelle d’application avec tous les objets de configuration des étapes précédentes.

    $appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-RG -Location "West US" -BackendAddressPools $pool1,$pool2 -BackendHttpSettingsCollection $poolSetting01, $poolSetting02 -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener -UrlPathMaps $urlPathMap -RequestRoutingRules $rule01 -Sku $sku

## <a name="get-application-gateway-dns-name"></a>Obtenir le nom de l’application passerelle DNS

Une fois que la passerelle est créée, l’étape suivante consiste à configurer le serveur frontal pour les communications. Lorsque vous utilisez une adresse IP publique, passerelle d’application nécessite un nom DNS affecté dynamiquement, qui n’est pas convivial. Pour vous assurer que les utilisateurs finaux pouvez appuyer sur la passerelle d’application un enregistrement CNAME peut être utilisé pour pointer au public point de terminaison de la passerelle d’application. [Configuration d’un nom de domaine personnalisé pour dans Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). Pour ce faire, récupérer les détails de la passerelle d’application et son nom IP/DNS associé à l’aide de l’élément PublicIPAddress attaché à la passerelle d’application. Nom DNS de la passerelle d’application doit être utilisé pour créer un enregistrement CNAME qui pointe les applications deux web à ce nom DNS. L’utilisation des enregistrements A n’est pas recommandée dans la mesure où l’adresse VIP peut changer lors du redémarrage de passerelle d’application.
    
    Get-AzureRmPublicIpAddress -ResourceGroupName appgw-RG -Name publicIP01
        
    Name                     : publicIP01
    ResourceGroupName        : appgw-RG
    Location                 : westus
    Id                       : /subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/publicIPAddresses/publicIP01
    Etag                     : W/"00000d5b-54ed-4907-bae8-99bd5766d0e5"
    ResourceGuid             : 00000000-0000-0000-0000-000000000000
    ProvisioningState        : Succeeded
    Tags                     : 
    PublicIpAllocationMethod : Dynamic
    IpAddress                : xx.xx.xxx.xx
    PublicIpAddressVersion   : IPv4
    IdleTimeoutInMinutes     : 4
    IpConfiguration          : {
                                 "Id": "/subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/applicationGateways/appgwtest/frontendIP
                               Configurations/frontend1"
                               }
    DnsSettings              : {
                                 "Fqdn": "00000000-0000-xxxx-xxxx-xxxxxxxxxxxx.cloudapp.net"
                               }

## <a name="next-steps"></a>Étapes suivantes

Si vous voulez en savoir déchargement couche SSL (Secure Sockets), consultez [configurer une passerelle d’application pour SSL décharger](application-gateway-ssl-arm.md).