<properties
   pageTitle="Configurer une passerelle d’application pour déchargement SSL à l’aide du Gestionnaire de ressources Azure | Microsoft Azure"
   description="Cette page fournit des instructions pour créer une passerelle d’application avec SSL déchargement en utilisant le Gestionnaire de ressources Azure"
   documentationCenter="na"
   services="application-gateway"
   authors="georgewallace"
   manager="carmonm"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/09/2016"
   ms.author="gwallace"/>

# <a name="configure-an-application-gateway-for-ssl-offload-by-using-azure-resource-manager"></a>Configurer une passerelle d’application pour déchargement SSL à l’aide du Gestionnaire de ressources Azure

> [AZURE.SELECTOR]
-[Portail Azure](application-gateway-ssl-portal.md)
-[Azure Gestionnaire de ressources PowerShell](application-gateway-ssl-arm.md)
-[Azure PowerShell classique](application-gateway-ssl.md)

 Azure passerelle d’Application peut être configuré pour mettre fin à la session calque SSL (Secure Sockets) au niveau de la passerelle pour éviter les tâches de déchiffrement coûteuses SSL pour se produire lors de la batterie de serveurs web. Déchargement SSL simplifie également la configuration du serveur frontal et la gestion de l’application web.

## <a name="before-you-begin"></a>Avant de commencer

1. Installer la dernière version des applets de commande PowerShell Azure à l’aide de Web Platform Installer. Vous pouvez télécharger et installer la dernière version de la section **Windows PowerShell** de la [page de téléchargement](https://azure.microsoft.com/downloads/).
2. Vous créez un réseau virtuel et un sous-réseau pour la passerelle d’application. Vérifiez qu’aucune machines virtuelles ou des déploiements dans le cloud n’utilisent le sous-réseau. Application passerelle doit être en lui-même dans un sous-réseau virtuel.
3. Les serveurs que vous configurez pour utiliser la passerelle d’application doivent exister ou ont leurs points de terminaison créés dans le réseau virtuel ou avec un public IP/VIP affectées.

## <a name="what-is-required-to-create-an-application-gateway"></a>Ce qui est requis pour créer une passerelle d’application ?


- **Pool du serveur principal :** La liste d’adresses IP des serveurs principale. Présente les adresses IP doivent appartenir soit au sous-réseau virtuel ou doivent être un IP/VIP publique.
- **Paramètres du pool serveur principal :** Chaque pool inclut des paramètres, tels qu’affinité basée sur les cookies, port et protocole. Ces paramètres sont liés à un pool et sont appliquées à tous les serveurs au sein du pool.
- **Port frontal :** Ce port est le port public est ouvert sur la passerelle d’application. Le trafic appuie sur ce port et puis redirigé à un des serveurs principale.
- **Récepteur :** Le récepteur inclut un port frontal, un protocole (Http ou Https, ces paramètres respectent la casse) et le nom du certificat SSL (si la configuration de SSL décharge).
- **Règle :** La règle lie le récepteur et au pool de serveur principal et définit le pool de serveur principal le trafic doit être dirigé vers lorsqu’il rencontre un récepteur particulier. Pour l’instant, uniquement la règle de *base* est pris en charge. La règle de *base* est la distribution de charge cyclique.

**Remarques sur la configuration supplémentaires**

Pour la configuration de certificats SSL, le protocole dans **HttpListener** doit changer en *Https* (respecter la casse). L’élément **Certificatssl** est ajouté à **HttpListener** avec la valeur de la variable configurée pour le certificat SSL. Le port frontal doit être mis à jour sur 443.

**Pour activer l’affinité basées sur des cookies**: une passerelle d’application peut être configurée pour s’assurer qu’une demande à partir d’une session client est toujours dirigée vers la même machine virtuelle dans la batterie de serveurs web. Ce scénario est émise par injection de cookies une session qui permet de la passerelle diriger le trafic de manière appropriée. Pour activer l’affinité basée sur les cookies, définissez **CookieBasedAffinity** sur *activé* dans l’élément **BackendHttpSettings** .


## <a name="create-an-application-gateway"></a>Créer une passerelle d’application

La différence entre l’utilisation du modèle de déploiement Azure classique et Azure le Gestionnaire de ressources est l’ordre dans lequel vous créez une passerelle d’application et les éléments qui doivent être configurés.

Avec le Gestionnaire de ressources, tous les composants d’une passerelle d’application sont configurés individuellement, puis entrez pour créer une ressource de passerelle d’application.


Voici les étapes nécessaires pour créer une passerelle d’application :

1. Créer un groupe de ressources pour le Gestionnaire de ressources
2. Créer des réseau virtuel, sous-réseau et public IP pour la passerelle d’application
3. Créer un objet de configuration de passerelle application
4. Créer une ressource de passerelle d’application


## <a name="create-a-resource-group-for-resource-manager"></a>Créer un groupe de ressources pour le Gestionnaire de ressources

Vérifiez que vous basculez en mode PowerShell pour utiliser les applets de commande Azure le Gestionnaire de ressources. Plus d’informations est disponible à [l’Utilisation de Windows PowerShell avec le Gestionnaire de ressources](../powershell-azure-resource-manager.md).

### <a name="step-1"></a>Étape 1

    Login-AzureRmAccount

### <a name="step-2"></a>Étape 2

Vérifier les abonnements pour le compte.

    Get-AzureRmSubscription

Vous êtes invité à vous authentifier avec vos informations d’identification.<BR>

### <a name="step-3"></a>Étape 3

Sélectionner les options de vos abonnements Azure à utiliser. <BR>


    Select-AzureRmSubscription -Subscriptionid "GUID of subscription"


### <a name="step-4"></a>Étape 4

Créer un groupe de ressources (ignorer cette étape si vous utilisez un groupe de ressources existant).

    New-AzureRmResourceGroup -Name appgw-rg -Location "West US"

Le Gestionnaire de ressources Azure requiert que tous les groupes ressources spécifient un emplacement. Ce paramètre est utilisé comme emplacement par défaut pour les ressources dans ce groupe de ressources. Assurez-vous que toutes les commandes permettant de créer une passerelle d’application utilise le même groupe de ressources.

Dans l’exemple ci-dessus, nous avons créé un groupe de ressources appelé « appgw-routage » et un emplacement « Ouest nous ».

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Créer un réseau virtuel et un sous-réseau pour la passerelle d’application

L’exemple suivant montre comment créer un réseau virtuel à l’aide du Gestionnaire de ressources :

### <a name="step-1"></a>Étape 1

    $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

Cet exemple affecte la 10.0.0.0/24 plage adresse à une variable sous-réseau à utiliser pour créer un réseau virtuel.

### <a name="step-2"></a>Étape 2

    $vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet

Cet exemple crée un réseau virtuel nommé « appgwvnet » dans le groupe de ressources « appgw-routage » à l’aide de la 10.0.0.0/16 préfixe avec sous-réseau 10.0.0.0/24 votre région US ouest.

### <a name="step-3"></a>Étape 3

    $subnet = $vnet.Subnets[0]

Cet exemple affecte l’objet sous-réseau à variable $subnet pour les étapes suivantes.

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Créer une adresse IP publique pour la configuration frontale

    $publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic

Cet exemple crée une ressource IP publique « publicIP01 » dans la ressource groupe « appgw-routage » pour la région des États-Unis Ouest.


## <a name="create-an-application-gateway-configuration-object"></a>Créer un objet de configuration de passerelle application

### <a name="step-1"></a>Étape 1

    $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

Cet exemple crée une configuration IP de passerelle application nommée « gatewayIP01 ». Démarrage d’Application passerelle, il capte une adresse IP à partir du sous-réseau configuré et route le trafic réseau aux adresses IP du pool d’IP principale. N’oubliez pas que chaque instance prend une seule adresse IP.

### <a name="step-2"></a>Étape 2

    $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50

Cet exemple configure le pool d’adresses IP principale nommé « pool01 » avec des adresses IP « 134.170.185.46, 134.170.188.221,134.170.185.50. » Ces valeurs correspondent aux adresses IP qui reçoivent le trafic réseau provenant du point de terminaison IP frontale. Remplacer les adresses IP de l’exemple précédent avec les adresses IP de vos points de terminaison application web.

### <a name="step-3"></a>Étape 3

    $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Enabled

Cet exemple configure application passerelle paramètre « poolsetting01 » au équilibrage de trafic réseau dans la liste principale.

### <a name="step-4"></a>Étape 4

    $fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 443

Cet exemple configure le port IP frontal nommé « frontendport01 » pour le point de terminaison IP public.

### <a name="step-5"></a>Étape 5

    $cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path for certificate file> -Password ‘<password>’

Cet exemple configure le certificat utilisé pour la connexion SSL. Le certificat doit être au format .pfx et le mot de passe doit être comprise entre 4 à 12 caractères.

### <a name="step-6"></a>Étape 6

    $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

Cet exemple crée la configuration IP frontale nommée « fipconfig01 » et associe l’adresse IP à la configuration IP frontale.

### <a name="step-7"></a>Étape 7

    $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert


Cet exemple crée le nom de récepteur « listener01 » et associe le port frontal à la configuration d’IP frontale et le certificat.

### <a name="step-8"></a>Étape 8

    $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

Cet exemple crée la règle de routage équilibrage de la charge nommée « rule01 » qui configure le comportement d’équilibrage de charge.

### <a name="step-9"></a>Étape 9

    $sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

Cet exemple configure la taille d’instance de la passerelle d’application.

>[AZURE.NOTE]  La valeur par défaut pour *InstanceCount* est 2, avec une valeur maximale de 10. La valeur par défaut pour *GatewaySize* est adaptée au support. Vous pouvez choisir entre Standard_Small, Standard_Medium et Standard_Large.

## <a name="create-an-application-gateway-by-using-new-azureapplicationgateway"></a>Créer une passerelle d’application à l’aide de nouveau AzureApplicationGateway

    $appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SslCertificates $cert

Cet exemple crée une passerelle d’application avec tous les éléments de configuration des étapes précédentes. Dans l’exemple, la passerelle d’application est appelée « appgwtest ».

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

Si vous souhaitez configurer une passerelle d’application à utiliser avec un programme d’équilibrage de charge interne (ILB), voir [créer une passerelle d’application avec un programme d’équilibrage de charge interne (ILB)](application-gateway-ilb.md).

Si vous souhaitez plus d’informations sur Charger les options d’équilibrage de charge en général, voir :

- [Équilibrage de charge Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Gestionnaire de trafic Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)
