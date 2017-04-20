<properties
   pageTitle="Créer et configurer une passerelle d’application avec un programme d’équilibrage de charge interne (ILB) à l’aide du Gestionnaire de ressources Azure | Microsoft Azure"
   description="Cette page fournit des instructions pour créer, configurer, démarrer et supprimer une passerelle d’application Azure avec équilibrage de charge interne (ILB) pour le Gestionnaire de ressources Azure"
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
   ms.date="08/19/2016"
   ms.author="gwallace"/>


# <a name="create-an-application-gateway-with-an-internal-load-balancer-ilb-by-using-azure-resource-manager"></a>Créer une passerelle d’application avec un programme d’équilibrage de charge interne (ILB) à l’aide du Gestionnaire de ressources Azure

> [AZURE.SELECTOR]
- [Étapes classiques Azure](application-gateway-ilb.md)
- [Étapes PowerShell Gestionnaire de ressources](application-gateway-ilb-arm.md)

Azure passerelle d’Application peut être configuré avec un VIP via Internet ou un point de terminaison interne qui n’est pas exposé à Internet, également connu sous un chargement interne équilibrage (ILB) point de terminaison. Configuration de la passerelle avec un ILB est utile pour les applications professionnelles internes qui ne sont pas exposées à Internet. Il est également utile pour les services et niveaux au sein d’une application à plusieurs niveaux qui se trouvent dans une limite de sécurité qui n’est pas visible sur Internet, mais nécessitent toujours cyclique chargement distribution, caractère collant session ou des frais de résiliation Secure Sockets Layer (SSL).

Cet article décrit les étapes de configuration d’une passerelle d’application avec un ILB.

## <a name="before-you-begin"></a>Avant de commencer

1. Installer la dernière version des applets de commande PowerShell Azure à l’aide de Web Platform Installer. Vous pouvez télécharger et installer la dernière version de la section **Windows PowerShell** de la [page de téléchargement](https://azure.microsoft.com/downloads/).
2. Vous créez un réseau virtuel et un sous-réseau de passerelle d’Application. Vérifiez qu’aucune machines virtuelles ou des déploiements dans le cloud n’utilisent le sous-réseau. Application passerelle doit être en lui-même dans un sous-réseau virtuel.
3. Les serveurs que vous configurez pour utiliser la passerelle d’application doivent exister ou ont leurs points de terminaison créés dans le réseau virtuel ou avec un public IP/VIP affectées.

## <a name="what-is-required-to-create-an-application-gateway"></a>Ce qui est requis pour créer une passerelle d’application ?


- **Pool du serveur principal :** La liste d’adresses IP des serveurs principale. Présente les adresses IP doivent appartenir soit au réseau virtuel, mais dans un autre sous-réseau pour la passerelle d’application ou doivent être un IP/VIP publique.
- **Paramètres du pool serveur principal :** Chaque pool inclut des paramètres, tels qu’affinité basée sur les cookies, port et protocole. Ces paramètres sont liés à un pool et sont appliquées à tous les serveurs au sein du pool.
- **Port frontal :** Ce port est le port public est ouvert sur la passerelle d’application. Le trafic appuie sur ce port et puis redirigé à un des serveurs principale.
- **Récepteur :** Le récepteur inclut un port frontal, un protocole (Http ou Https, il s’agit des respectant la casse) et le nom du certificat SSL (si la configuration de SSL décharge).
- **Règle :** La règle lie le récepteur et au pool de serveur principal et définit le pool de serveur principal le trafic doit être dirigé vers lorsqu’il rencontre un récepteur particulier. Pour l’instant, uniquement la règle de *base* est pris en charge. La règle de *base* est la distribution de charge cyclique.



## <a name="create-an-application-gateway"></a>Créer une passerelle d’application

La différence entre l’utilisation classique Azure et Azure le Gestionnaire de ressources est l’ordre dans lequel vous créez la passerelle d’application et les éléments qui doivent être configurés.
Avec le Gestionnaire de ressources, tous les éléments qui en font une passerelle d’application est configuré individuellement et puis placer pour créer la ressource de passerelle d’application.


Voici les étapes sont nécessaires pour créer une passerelle d’application :

1. Créer un groupe de ressources pour le Gestionnaire de ressources
2. Créer un réseau virtuel et un sous-réseau pour la passerelle d’application
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

Créer un nouveau groupe de ressources (ignorer cette étape si vous utilisez un groupe de ressources existant).

    New-AzureRmResourceGroup -Name appgw-rg -location "West US"

Le Gestionnaire de ressources Azure requiert que tous les groupes ressources spécifient un emplacement. Il est utilisé comme emplacement par défaut pour les ressources dans ce groupe de ressources. Assurez-vous que toutes les commandes permettant de créer une passerelle d’application utilise le même groupe de ressources.

Dans l’exemple ci-dessus, nous avons créé un groupe de ressources appelé « appgw-routage » et un emplacement « Ouest nous ».

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Créer un réseau virtuel et un sous-réseau pour la passerelle d’application

L’exemple suivant montre comment créer un réseau virtuel à l’aide du Gestionnaire de ressources :

### <a name="step-1"></a>Étape 1

    $subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

Cela affecte la 10.0.0.0/24 plage adresse à une variable sous-réseau à utiliser pour créer un réseau virtuel.

### <a name="step-2"></a>Étape 2

    $vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnetconfig

Cela crée un réseau virtuel nommé « appgwvnet » dans le groupe de ressources « appgw-routage » à l’aide de la 10.0.0.0/16 préfixe avec sous-réseau 10.0.0.0/24 votre région US ouest.

### <a name="step-3"></a>Étape 3

    $subnet = $vnet.subnets[0]

Cela affecte l’objet sous-réseau à variable $subnet pour les étapes suivantes.

## <a name="create-an-application-gateway-configuration-object"></a>Créer un objet de configuration de passerelle application

### <a name="step-1"></a>Étape 1

    $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

Cela crée une configuration IP de passerelle application nommée « gatewayIP01 ». Démarrage d’Application passerelle, il capte une adresse IP à partir du sous-réseau configuré et route le trafic réseau aux adresses IP du pool d’IP principale. N’oubliez pas que chaque instance prend une seule adresse IP.


### <a name="step-2"></a>Étape 2

    $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50

Cela configure le pool d’adresses IP principale nommé « pool01 » avec des adresses IP « 134.170.185.46, 134.170.188.221,134.170.185.50 ». Ce sont les adresses IP recevoir le trafic réseau provenant du point de terminaison IP frontale. Vous remplacez les adresses IP ci-dessus pour ajouter vos propres points de terminaison d’adresse IP application.

### <a name="step-3"></a>Étape 3

    $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled

Cela configure le trafic réseau application passerelle paramètre « poolsetting01 » pour la charge équilibrée dans la liste principale.

### <a name="step-4"></a>Étape 4

    $fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 80

Cela configure le port IP frontal nommé « frontendport01 » pour la ILB.

### <a name="step-5"></a>Étape 5

    $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -Subnet $subnet

Cela crée la configuration IP frontale appelée « fipconfig01 » et associe à une adresse IP privé à partir du sous-réseau réseau virtuel en cours.

### <a name="step-6"></a>Étape 6

    $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

Cela crée le récepteur appelé « listener01 » et associe le port frontal à la configuration IP frontale.

### <a name="step-7"></a>Étape 7

    $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

Cela crée la règle de routage équilibrage de la charge appelée « rule01 » qui configure le comportement d’équilibrage de charge.

### <a name="step-8"></a>Étape 8

    $sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

Cela permet de configurer la taille d’instance de la passerelle d’application.

>[AZURE.NOTE]  La valeur par défaut pour *InstanceCount* est 2, avec une valeur maximale de 10. La valeur par défaut pour *GatewaySize* est adaptée au support. Vous pouvez choisir entre Standard_Small, Standard_Medium et Standard_Large.

## <a name="create-an-application-gateway-by-using-new-azureapplicationgateway"></a>Créer une passerelle d’application à l’aide de nouveau AzureApplicationGateway

Crée une passerelle d’application avec tous les éléments de configuration de la procédure ci-dessus. Dans cet exemple, la passerelle d’application est appelée « appgwtest ».


    $appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku

Cela crée une passerelle d’application avec tous les éléments de configuration de la procédure ci-dessus. Dans l’exemple, la passerelle d’application est appelée « appgwtest ».


## <a name="delete-an-application-gateway"></a>Supprimer une passerelle d’application

Pour supprimer une passerelle d’application, vous devez effectuer les opérations suivantes dans l’ordre :

1. Utiliser l’applet de commande **Stop AzureRmApplicationGateway** pour arrêter la passerelle.
2. Utiliser l’applet de commande **Supprimer AzureRmApplicationGateway** pour supprimer la passerelle.
3. Vérifiez que la passerelle a été supprimée à l’aide de l’applet de commande **Get-AzureApplicationGateway** .


### <a name="step-1"></a>Étape 1

Obtenir l’objet de passerelle d’application et l’associer à une variable « $getgw ».

    $getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

### <a name="step-2"></a>Étape 2

Utilisez **Stop AzureRmApplicationGateway** pour arrêter la passerelle d’application. Ceci montre l’applet de commande **Arrêter AzureRmApplicationGateway** sur la première ligne, suivi par la sortie.

    PS C:\> Stop-AzureRmApplicationGateway -ApplicationGateway $getgw  

    VERBOSE: 9:49:34 PM - Begin Operation: Stop-AzureApplicationGateway
    VERBOSE: 10:10:06 PM - Completed Operation: Stop-AzureApplicationGateway
    Name       HTTP Status Code     Operation ID                             Error
    ----       ----------------     ------------                             ----
    Successful OK                   ce6c6c95-77b4-2118-9d65-e29defadffb8

Une fois que la passerelle d’application est dans un état arrêté, utilisez l’applet de commande **AzureRmApplicationGateway de supprimer** pour supprimer le service.


    PS C:\> Remove-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Force

    VERBOSE: 10:49:34 PM - Begin Operation: Remove-AzureApplicationGateway
    VERBOSE: 10:50:36 PM - Completed Operation: Remove-AzureApplicationGateway
    Name       HTTP Status Code     Operation ID                             Error
    ----       ----------------     ------------                             ----
    Successful OK                   055f3a96-8681-2094-a304-8d9a11ad8301

>[AZURE.NOTE] La **-forcer** commutateur peut être utilisé pour supprimer le message de confirmation Supprimer.


Pour vérifier que le service a été supprimé, vous pouvez utiliser l’applet de commande **Get-AzureRmApplicationGateway** . Cette étape n’est pas obligatoire.


    PS C:\>Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

    VERBOSE: 10:52:46 PM - Begin Operation: Get-AzureApplicationGateway

    Get-AzureApplicationGateway : ResourceNotFound: The gateway does not exist.
    .....

## <a name="next-steps"></a>Étapes suivantes

Si vous voulez configurer déchargement SSL, voir [configurer une passerelle d’application pour SSL décharger](application-gateway-ssl.md).

Si vous souhaitez configurer une passerelle d’application à utiliser avec un ILB, voir [créer une passerelle d’application avec un programme d’équilibrage de charge interne (ILB)](application-gateway-ilb.md).

Si vous souhaitez plus d’informations sur Charger les options d’équilibrage de charge en général, voir :

- [Équilibrage de charge Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Gestionnaire de trafic Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)
