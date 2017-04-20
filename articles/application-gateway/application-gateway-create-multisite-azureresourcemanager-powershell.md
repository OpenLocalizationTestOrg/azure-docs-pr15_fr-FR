<properties
   pageTitle="Créer une passerelle d’application pour l’hébergement de plusieurs sites | Microsoft Azure"
   description="Cette page fournit des instructions pour créer, de configurer une passerelle d’application Azure pour l’hébergement de plusieurs applications web sur la même passerelle."
   documentationCenter="na"
   services="application-gateway"
   authors="amsriva"
   manager="rossort"
   editor="amsriva"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/25/2016"
   ms.author="amsriva"/>


# <a name="create-an-application-gateway-for-hosting-multiple-web-applications"></a>Créer une passerelle d’application pour l’hébergement de plusieurs applications web

> [AZURE.SELECTOR]
- [Portail Azure](application-gateway-create-multisite-portal.md)
- [Azure PowerShell Gestionnaire de ressources](application-gateway-create-multisite-azureresourcemanager-powershell.md)

Hébergement de plusieurs sites vous permet de déployer plus d’une application web sur la même passerelle d’application. Il s’appuie sur la présence d’en-tête de l’hôte dans la demande HTTP entrante, afin de déterminer quel récepteur reçoit le trafic. Que l’auditeur dirige ensuite le trafic vers principaux appropriés pool tel que configuré dans la définition des règles de la passerelle. Dans les applications web SSL est activé, passerelle d’application dépend de l’extension Indication de nom de serveur (SNI) pour choisir le récepteur approprié pour le trafic web. Souvent utilisée pour l’hébergement de plusieurs sites consiste à charger des demandes de solde pour les domaines web différentes pour des pools autre serveur principal. De même plusieurs sous-domaines du domaine racine même pourraient également être hébergés sur la même passerelle d’application.

## <a name="scenario"></a>Scénario

Dans l’exemple suivant, passerelle d’application sert le trafic de contoso.com et fabrikam.com avec deux pools serveur principal : contoso serveur pool et pool de serveur de fabrikam. Le programme d’installation similaire peut servir à sous-domaines hôte comme app.contoso.com et blog.contoso.com.

![imageURLroute](./media/application-gateway-create-multisite-azureresourcemanager-powershell/multisite.png)

## <a name="before-you-begin"></a>Avant de commencer

1. Installer la dernière version des applets de commande PowerShell Azure à l’aide de Web Platform Installer. Vous pouvez télécharger et installer la dernière version de la section **Windows PowerShell** de la [page de téléchargement](https://azure.microsoft.com/downloads/).
2. Les serveurs ajoutées à la liste principale à utiliser la passerelle d’application doivent exister ou leurs points de terminaison créées dans le réseau virtuel dans un sous-réseau distinct ou avec un public IP/VIP affectées.

## <a name="requirements"></a>Configuration requise

- **Pool du serveur principal :** La liste d’adresses IP des serveurs principale. Présente les adresses IP doivent appartenir soit au sous-réseau virtuel ou doivent être un IP/VIP publique. Nom de domaine complet peut également être utilisé.
- **Paramètres du pool serveur principal :** Chaque pool inclut des paramètres, tels qu’affinité basée sur les cookies, port et protocole. Ces paramètres sont liés à un pool et sont appliquées à tous les serveurs au sein du pool.
- **Port frontal :** Ce port est le port public est ouvert sur la passerelle d’application. Le trafic appuie sur ce port et puis redirigé à un des serveurs principale.
- **Récepteur :** Le récepteur inclut un port frontal, un protocole (Http ou Https, ces valeurs respectent la casse) et le nom du certificat SSL (si la configuration de SSL décharge). Pour les passerelles application activée sur plusieurs sites, nom d’hôte et indicateurs SNI sont également ajoutées.
- **Règle :** La règle lie le récepteur, le pool de serveur principal et définit le pool de serveur principal le trafic doit être dirigé vers lorsqu’il rencontre un récepteur particulier.

## <a name="create-an-application-gateway"></a>Créer une passerelle d’application

Voici les étapes nécessaires pour créer une passerelle d’application :

1. Créer un groupe de ressources pour le Gestionnaire de ressources.
2. Créer un réseau virtuel, sous-réseaux et public IP pour la passerelle d’application.
3. Créer un objet de configuration de passerelle application.
4. Créer une ressource de passerelle d’application.

## <a name="create-a-resource-group-for-resource-manager"></a>Créer un groupe de ressources pour le Gestionnaire de ressources

Vérifiez que vous utilisez la dernière version d’Azure PowerShell. Informations supplémentaires sont disponibles dans la page [à l’aide de Windows PowerShell avec le Gestionnaire de ressources](../powershell-azure-resource-manager.md).

### <a name="step-1"></a>Étape 1

Se connecter à Azure

    Login-AzureRmAccount

Vous êtes invité à vous authentifier avec vos informations d’identification.

### <a name="step-2"></a>Étape 2

Vérifier les abonnements pour le compte.

    Get-AzureRmSubscription

### <a name="step-3"></a>Étape 3

Sélectionner les options de vos abonnements Azure à utiliser.

    Select-AzureRmSubscription -SubscriptionName "Name of subscription"

### <a name="step-4"></a>Étape 4

Créer un groupe de ressources (ignorer cette étape si vous utilisez un groupe de ressources existant).

    New-AzureRmResourceGroup -Name appgw-RG -location "West US"

Vous pouvez également vous pouvez également créer des balises pour un groupe de ressources pour la passerelle d’application :

    $resourceGroup = New-AzureRmResourceGroup -Name appgw-RG -Location "West US" -Tags @{Name = "testtag"; Value = "Application Gateway multiple site"}

Le Gestionnaire de ressources Azure requiert que tous les groupes ressources spécifient un emplacement. Cet emplacement est utilisé comme emplacement par défaut pour les ressources dans ce groupe de ressources. S’assurer que toutes les commandes permettant de créer une passerelle d’application utilisent le même groupe de ressources.

Dans l’exemple ci-dessus, nous avons créé un groupe de ressources appelé « appgw-routage » avec un emplacement de « Ouest nous ».

>[AZURE.NOTE] Si vous avez besoin configurer une sonde personnalisé pour votre passerelle d’application, voir [créer une passerelle d’application avec sondes personnalisé à l’aide de PowerShell](application-gateway-create-probe-ps.md). Visitez [sondes personnalisé et le contrôle d’état](application-gateway-probe-overview.md) pour plus d’informations.

## <a name="create-a-virtual-network-and-subnets"></a>Créer un réseau virtuel et sous-réseaux

L’exemple suivant montre comment créer un réseau virtuel à l’aide du Gestionnaire de ressources. Deux sous-réseaux sont créés dans cette étape. La première constitue pour la passerelle d’application elle-même. Application passerelle nécessite son propre sous-réseau à maintenir ses instances. Seules les autres passerelles application peuvent être déployées dans ce sous-réseau. Le deuxième sous-réseau est utilisé pour inclure les serveurs d’applications serveur principal.

### <a name="step-1"></a>Étape 1

Affecter la 10.0.0.0/24 plage adresse dans la variable sous-réseau à utiliser pour stocker la passerelle d’application.

    $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name appgatewaysubnet -AddressPrefix 10.0.0.0/24

### <a name="step-2"></a>Étape 2

Affecter la 10.0.1.0/24 plage adresse dans la variable subnet2 à utiliser pour les pools d’arrière-plan.

    $subnet2 = New-AzureRmVirtualNetworkSubnetConfig -Name backendsubnet -AddressPrefix 10.0.1.0/24

### <a name="step-3"></a>Étape 3

Créer un réseau virtuel nommé « appgwvnet » dans le groupe de ressources « appgw-routage » pour la région Ouest US à l’aide de la 10.0.0.0/16 préfixe avec sous-réseau 10.0.0.0/24 et 10.0.1.0/24.

    $vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet,$subnet2


### <a name="step-4"></a>Étape 4

Affecter une variable sous-réseau pour la procédure suivante, qui crée une passerelle d’application.

    $appgatewaysubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name appgatewaysubnet -VirtualNetwork $vnet
    $backendsubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name backendsubnet -VirtualNetwork $vnet

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Créer une adresse IP publique pour la configuration frontale

Créez une ressource IP publique « publicIP01 » dans la ressource groupe « appgw-routage » pour la région des États-Unis Ouest.

    $publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-RG -name publicIP01 -location "West US" -AllocationMethod Dynamic

Une adresse IP est affectée à la passerelle d’application au démarrage du service.

## <a name="create-application-gateway-configuration"></a>Créer la configuration de passerelle l’application

Vous devez configurer tous les éléments de configuration avant de créer la passerelle d’application. Les étapes suivantes créent les éléments de configuration sont nécessaires pour une ressource de passerelle d’application.

### <a name="step-1"></a>Étape 1

Créer une configuration IP de passerelle application nommée « gatewayIP01 ». Au démarrage de passerelle d’application, il sélectionne une adresse IP à partir du sous-réseau configuré et route le trafic réseau aux adresses IP du pool d’IP principale. N’oubliez pas que chaque instance prend une seule adresse IP.

    $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $appgatewaysubnet

### <a name="step-2"></a>Étape 2

Configurer le pool d’adresses IP principale nommé « pool01 » et « pool2 » avec des adresses IP « entre 10.0.1.100, 10.0.1.101,10.0.1.102 » pour « pool1 » et « 10.0.1.103, 10.0.1.104, 10.0.1.105 » pour « pool2 ».

    $pool1 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 10.0.1.100, 10.0.1.101, 10.0.1.102
    $pool2 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool02 -BackendIPAddresses 10.0.1.103, 10.0.1.104, 10.0.1.105

Dans cet exemple, il existe deux principaux des pools pour acheminer le trafic réseau basée sur le site demandé. Un pool reçoit le trafic de site « contoso.com » et autres pool reçoit le trafic de site « fabrikam.com ». Vous devez remplacer les adresses IP précédentes pour ajouter vos propres points de terminaison d’adresse IP application. À la place d’adresses IP internes, vous pouvez également utiliser des adresses IP publiques, nom de domaine complet ou carte réseau d’une machine virtuelle instances de serveur principal. Utilisez «-BackendFQDNs » paramètre dans PowerShell pour spécifier les noms de domaine complets au lieu d’adresses IP.

### <a name="step-3"></a>Étape 3

Configurer l’application passerelle paramètre « poolsetting01 » et « poolsetting02 » pour le trafic réseau avec équilibrage de charge dans la liste principale. Dans cet exemple, vous configurez les paramètres de pool principale différent pour les pools principale. Chaque pool principale peut avoir son propre paramètre pool principale.

    $poolSetting01 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting01" -Port 80 -Protocol Http -CookieBasedAffinity Disabled -RequestTimeout 120
    $poolSetting02 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting02" -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 240

### <a name="step-4"></a>Étape 4

Configurer l’adresse IP frontal avec point de terminaison IP public.

    $fipconfig01 = New-AzureRmApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip

### <a name="step-5"></a>Étape 5

Configurer le port frontaux pour une passerelle d’application.

    $fp01 = New-AzureRmApplicationGatewayFrontendPort -Name "fep01" -Port 443

### <a name="step-6"></a>Étape 6

Configurer les deux certificats SSL pour les deux sites Web que pour prendre en charge dans cet exemple, nous allons. Un certificat est pour le trafic contoso.com et l’autre est pour le trafic fabrikam.com. Ces certificats doivent être une autorité de certification émis des certificats pour vos sites Web. Les certificats auto-signé sont pris en charge, mais pas recommandées pour le trafic de production.

    $cert01 = New-AzureRmApplicationGatewaySslCertificate -Name contosocert -CertificateFile <file path> -Password <password>
    $cert02 = New-AzureRmApplicationGatewaySslCertificate -Name fabrikamcert -CertificateFile <file path> -Password <password>


### <a name="step-7"></a>Étape 7

Dans cet exemple, configurez deux écoute pour les deux sites web. Cette étape configure l’écoute pour public adresse IP, port et hôte utilisé pour recevoir le trafic entrant. Paramètre de nom d’hôte est requis pour la prise en charge de plusieurs sites et doit être définie sur le site Web approprié pour lesquelles le trafic est reçu. Paramètre de RequireServerNameIndication doit être définie sur true pour les sites Web qui ont besoin de prise en charge pour SSL dans un scénario hôte plusieurs. Si la prise en charge SSL est requise, vous devez également spécifier le certificat SSL qui est utilisé pour sécuriser le trafic de l’application web. La combinaison de FrontendIPConfiguration, FrontendPort et nom d’hôte doit être unique à un récepteur. Chaque récepteur peut prendre en charge un certificat.

    $listener01 = New-AzureRmApplicationGatewayHttpListener -Name "listener01" -Protocol Https -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -HostName "contoso11.com" -RequireServerNameIndication true  -SslCertificate $cert01
    $listener02 = New-AzureRmApplicationGatewayHttpListener -Name "listener02" -Protocol Https -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -HostName "fabrikam11.com" -RequireServerNameIndication true -SslCertificate $cert02

### <a name="step-8"></a>Étape 8

Créez deux paramètre de règle pour les applications deux web dans cet exemple. Une règle relie écoute, des pools d’arrière-plan et des paramètres http. Cette étape configure la passerelle d’application pour utiliser la règle de routage simple, une pour chaque site Web. Le trafic vers chacun des sites Web est reçu par son récepteur configuré et est alors dirigé vers son pool principal configuré, à l’aide des propriétés indiquées dans le BackendHttpSettings.

    $rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule01" -RuleType Basic -HttpListener $listener01 -BackendHttpSettings $poolSetting01 -BackendAddressPool $pool1
    $rule02 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule02" -RuleType Basic -HttpListener $listener02 -BackendHttpSettings $poolSetting02 -BackendAddressPool $pool2

### <a name="step-9"></a>Étape 9

Configurer le nombre d’instances et la taille de la passerelle d’application.

    $sku = New-AzureRmApplicationGatewaySku -Name "Standard_Medium" -Tier Standard -Capacity 2

## <a name="create-application-gateway"></a>Créer la passerelle d’application

Créer une passerelle d’application avec tous les objets de configuration des étapes précédentes.

    $appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-RG -Location "West US" -BackendAddressPools $pool1,$pool2 -BackendHttpSettingsCollection $poolSetting01, $poolSetting02 -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener01, $listener02 -RequestRoutingRules $rule01, $rule02 -Sku $sku -SslCertificates $cert01, $cert02


>[AZURE.IMPORTANT] Mise en service de passerelle application est une opération longue et peut prendre un certain temps pour terminer.

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

Découvrez comment protéger vos sites Web avec [Application passerelle - pare-feu pour applications Web](application-gateway-webapplicationfirewall-overview.md)