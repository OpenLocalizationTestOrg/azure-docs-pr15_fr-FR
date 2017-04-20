<properties
   pageTitle="Configurer le pare-feu de Application Web existante ou nouvelle Application Gateway | Microsoft Azure"
   description="Cet article explique comment utiliser les pare-feu pour applications web sur une passerelle d’application existant ou nouveau."
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
   ms.date="10/25/2016"
   ms.author="gwallace"/>

# <a name="configure-web-application-firewall-on-a-new-or-existing-application-gateway"></a>Configurer les pare-feu pour applications Web sur une passerelle Application nouvelle ou existante

> [AZURE.SELECTOR]
- [Portail Azure](application-gateway-web-application-firewall-portal.md)
- [Azure PowerShell Gestionnaire de ressources](application-gateway-web-application-firewall-powershell.md)

Le pare-feu d’application web (WAF) dans Azure Application Gateway protège les applications web contre les attaques courantes sur le web comme injection SQL, les attaques de script intersites et détournements de session.

Azure Application passerelle est un équilibrage de charge de calque à 7. Il propose basculement, routage des performances des requêtes HTTP entre différents serveurs, qu’ils soient sur le nuage ou sur site. Application fournit de nombreuses fonctionnalités contrôleur de remise Application (ADC), y compris l’équilibrage de charge HTTP, affinité session basée sur les cookies, calque SSL (Secure Sockets) décharger, état personnalisé sondes, prise en charge de plusieurs sites et bien d’autres. Pour rechercher une liste complète des fonctionnalités prises en charge, consultez Vue d’ensemble de la passerelle Application

L’article suivant montre comment [Ajouter les pare-feu pour applications web à une passerelle d’application existant](#add-web-application-firewall-to-an-existing-application-gateway) et [créer une passerelle d’application qui utilise le pare-feu pour applications web](#create-an-application-gateway-with-web-application-firewall).

![image de scénario][scenario]

## <a name="waf-configuration-differences"></a>Différences de configuration WAF

Si vous avez pris connaissance de [créer une passerelle d’Application avec PowerShell](application-gateway-create-gateway-arm.md), vous comprenez les paramètres de référence (SKU) de configuration lors de la création d’une passerelle d’application. WAF fournit des paramètres supplémentaires pour définir lorsque vous configurez la référence (SKU) à une passerelle d’application. Il n’existe aucune modification supplémentaire que vous apportez sur la passerelle application elle-même.

**Référence (SKU)** – une passerelle application normale sans prend en charge WAF **Standard\_petite**, **Standard\_support**, et **Standard\_grande** tailles. Avec l’introduction de WAF, il existe deux références supplémentaires, **WAF\_moyen** et **WAF\_grande**. WAF n’est pas pris en charge sur les passerelles petite application.

**Niveau** - les valeurs disponibles sont **Standard** ou **WAF**. Lorsque vous utilisez un pare-feu pour applications Web, **WAF** doivent être choisis.

**Mode** - ce paramètre est le mode de WAF. les valeurs autorisées sont **détection** et **prévention**. Lorsque WAF est configuré en mode de détection, toutes les menaces sont stockés dans un fichier journal. En mode de prévention, toujours les événements sont enregistrés, mais le pirate reçoit une réponse non autorisé 403 à partir de la passerelle d’application.

## <a name="add-web-application-firewall-to-an-existing-application-gateway"></a>Ajouter les pare-feu pour applications web à une passerelle d’application existant

Vérifiez que vous utilisez la dernière version d’Azure PowerShell. Plus d’informations est disponible à [l’Utilisation de Windows PowerShell avec le Gestionnaire de ressources](../powershell-azure-resource-manager.md).

### <a name="step-1"></a>Étape 1

Connectez-vous à votre compte Azure.

    Login-AzureRmAccount

### <a name="step-2"></a>Étape 2

Sélectionnez l’abonnement à utiliser pour ce scénario.

    Select-AzureRmSubscription -SubscriptionName "<Subscription name>"

### <a name="step-3"></a>Étape 3

Récupérer la passerelle que vous ajoutez pare-feu pour applications Web à.

    $gw = Get-AzureRmApplicationGateway -Name "AdatumGateway" -ResourceGroupName "MyResourceGroup"


### <a name="step-4"></a>Étape 4

Configurer la référence (SKU) pare-feu d’application web. Les tailles disponibles sont **WAF\_grande** et **WAF\_support**. Lorsque le pare-feu pour applications web est utilisé la couche doit être **WAF**, la capacité doit être confirmée lors de la définition de la référence (SKU).

    $gw | Set-AzureRmApplicationGatewaySku -Name WAF_Large -Tier WAF -Capacity 2

### <a name="step-5"></a>Étape 5

Configurer les paramètres WAF, telle que définie dans l’exemple suivant :

Pour que le paramètre **WafMode** , les valeurs disponibles sont prévention et recherche.

    $gw | Set-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode Prevention

### <a name="step-6"></a>Étape 6

Mettre à jour la passerelle d’application avec les paramètres définis dans l’étape précédente.

    Set-AzureRmApplicationGateway -ApplicationGateway $gw

Cette commande Mettre à jour la passerelle d’application Web Application pare-feu. Il est recommandé d’afficher [Application passerelle Diagnostics](application-gateway-diagnostics.md) pour mieux comprendre comment afficher les journaux de votre passerelle d’application. En raison de la nature sécurité de WAF, les journaux doivent être examiné régulièrement pour mieux comprendre la position de sécurité de vos applications web.

## <a name="create-an-application-gateway-with-web-application-firewall"></a>Créer une passerelle d’Application avec pare-feu pour applications Web

Les étapes suivantes vous guident dans l’ensemble du processus à partir du début à la fin de création d’une passerelle d’Application avec pare-feu pour applications Web.

Vérifiez que vous utilisez la dernière version d’Azure PowerShell. Plus d’informations est disponible à [l’Utilisation de Windows PowerShell avec le Gestionnaire de ressources](../powershell-azure-resource-manager.md).

### <a name="step-1"></a>Étape 1

Se connecter à Azure

    Login-AzureRmAccount

Vous êtes invité à vous authentifier avec vos informations d’identification.

### <a name="step-2"></a>Étape 2

Vérifier les abonnements pour le compte.

    Get-AzureRmSubscription

### <a name="step-3"></a>Étape 3

Sélectionner les options de vos abonnements Azure à utiliser.

    Select-AzureRmsubscription -SubscriptionName "<Subscription name>"

### <a name="step-4"></a>Étape 4

Créer un groupe de ressources (ignorer cette étape si vous utilisez un groupe de ressources existant).

    New-AzureRmResourceGroup -Name appgw-rg -Location "West US"

Le Gestionnaire de ressources Azure requiert que tous les groupes ressources spécifient un emplacement. Cet emplacement est utilisé comme emplacement par défaut pour les ressources dans ce groupe de ressources. Assurez-vous que toutes les commandes permettant de créer une passerelle d’application utilise le même groupe de ressources.

Dans l’exemple précédent, nous avons créé un groupe de ressources appelé « appgw-routage » et un emplacement « Ouest nous ».

>[AZURE.NOTE] Si vous avez besoin configurer une sonde personnalisé pour votre passerelle d’application, voir [créer une passerelle d’application avec sondes personnalisé à l’aide de PowerShell](application-gateway-create-probe-ps.md). Consultez [sondes personnalisé et le contrôle d’état](application-gateway-probe-overview.md) pour plus d’informations.

### <a name="step-5"></a>Étape 5

Affecter une plage d’adresses pour le sous-réseau être utilisés pour la passerelle d’Application elle-même.

    $gwSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24

> [AZURE.NOTE] Un sous-réseau pour une application doit avoir un minimum de 28 bits du masque. Cette valeur laisse 10 adresses disponibles dans le sous-réseau instances de passerelle d’Application. Avec un sous-réseau plus petit, vous pouvez ne pas être en mesure d’ajouter plusieurs instances de votre application passerelle à l’avenir.

### <a name="step-6"></a>Étape 6

Affecter une plage d’adresses à utiliser pour le pool d’adresses principal.

    $nicSubnet = New-AzureRmVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24

### <a name="step-7"></a>Étape 7

Créer un réseau virtuel avec les sous-réseaux précédents dans le groupe de ressources créé à l’étape : [créer le groupe de ressources](#create-the-resource-group)

    $vnet = New-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet

### <a name="step-8"></a>Étape 8

Récupérer les ressources de réseau virtuel et les ressources de sous-réseau à utiliser dans les étapes suivantes :

    $vnet = Get-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg
    $gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -VirtualNetwork $vnet
    $nicSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appsubnet' -VirtualNetwork $vnet

### <a name="step-9"></a>Étape 9

Créez une ressource IP publique à utiliser pour la passerelle d’application. Cette adresse IP publique est utilisée dans une des opérations suivantes :

    $publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name 'appgwpip' -Location "West US" -AllocationMethod Dynamic

> [AZURE.IMPORTANT] Passerelle d’application ne prend pas en charge l’utilisation d’une adresse IP publique créée avec une étiquette de domaine définie. Uniquement une adresse IP publique avec un nom de domaine créés de façon dynamique est prise en charge. Si vous avez besoin d’un nom convivial dns pour la passerelle d’application, il est recommandé d’utiliser un enregistrement cname en tant qu’alias.

### <a name="step-10"></a>Étape 10

Vous devez configurer tous les éléments de configuration avant de créer la passerelle d’application. Les étapes suivantes créent les éléments de configuration sont nécessaires pour une ressource de passerelle d’application.

Créer une configuration d’application passerelle IP, cela configure quel sous-réseau utilise la passerelle d’Application. Au démarrage de passerelle d’Application, il sélectionne une adresse IP à partir du sous-réseau configuré et achemine le trafic réseau aux adresses IP du pool d’IP principale. N’oubliez pas que chaque instance prend une seule adresse IP.

    $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet

### <a name="step-11"></a>Étape 11

Configurer le pool d’adresses IP principale et les adresses IP des serveurs web principaux. Ces adresses IP sont les adresses IP recevoir le trafic réseau provenant du point de terminaison IP frontale. Vous remplacez les adresses IP suivantes pour ajouter vos propres points de terminaison d’adresse IP application.

    $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 1.1.1.1, 2.2.2.2, 3.3.3.3

### <a name="step-12"></a>Étape 12

Téléchargez le certificat à utiliser sur les ressources de pool principal ssl est activé.

    $authcert = New-AzureRmApplicationGatewayAuthenticationCertificate -Name 'whitelistcert1' -CertificateFile <full path to .cer file>

### <a name="step-13"></a>Étape 13

Configurer les paramètres application passerelle principale http. Attribuer le certificat téléchargé à l’étape précédente pour les paramètres http.

    $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name 'setting01' -Port 443 -Protocol Https -CookieBasedAffinity Enabled -AuthenticationCertificates $authcert

### <a name="step-14"></a>Étape 14

Configurer le port IP frontal du point de terminaison IP public. Ce port est le port auquel se connecter aux utilisateurs finaux.

    $fp = New-AzureRmApplicationGatewayFrontendPort -Name 'port01'  -Port 443

### <a name="step-15"></a>Étape 15

Créer une configuration IP frontale, ce paramètre mappe une adresse ip publique ou privée au serveur frontal de la passerelle d’application. L’étape suivante associe l’adresse IP à l’étape précédente à la configuration IP frontale.

    $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip

### <a name="step-16"></a>Étape 16

Configurer le certificat pour la passerelle d’application. Ce certificat est utilisé pour déchiffrer et chiffrer à nouveau le trafic sur la passerelle d’application.

    $cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password <password for certificate file>

### <a name="step-17"></a>Étape 17

Créer le récepteur HTTP pour la passerelle d’application. Attribuer le certificat de configuration, port et ssl ip frontale à utiliser.

    $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert

### <a name="step-18"></a>Étape 18

Créer une règle de routage de l’équilibrage de charge qui configure le comportement d’équilibrage de charge. Dans cet exemple, une règle de base alternées est créée.

    $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
   
### <a name="step-19"></a>Étape 19

Configurer la taille de l’instance de la passerelle d’application.

    $sku = New-AzureRmApplicationGatewaySku -Name WAF_Medium -Tier WAF -Capacity 2

>[AZURE.NOTE]  Vous pouvez choisir entre **WAF\_moyen** et **WAF\_grande**, la couche lorsque l’utilisation WAF est toujours **WAF**. Capacité est un nombre compris entre 1 et 10.

### <a name="step-20"></a>Étape 20

Configurer le mode pour WAF, les valeurs possibles sont **prévention** et **recherche**.

    $config = New-AzureRmApplicationGatewayWafConfig -Enabled $true -WafMode "Prevention"

### <a name="step-21"></a>Étape 21

Créer une passerelle d’application avec tous les éléments de configuration des étapes précédentes. Dans cet exemple, la passerelle d’application est appelée « appgwtest ».

    $appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $cert -AuthenticationCertificates $authcert

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

Découvrez comment configurer la journalisation des diagnostics, afin de vous connecter les événements qui sont détectées ou empêchés avec pare-feu pour applications Web, en visitant une [Application passerelle Diagnostics](application-gateway-diagnostics.md)


[scenario]: ./media/application-gateway-web-application-firewall-powershell/scenario.png