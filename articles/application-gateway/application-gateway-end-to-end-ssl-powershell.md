<properties
    pageTitle="Configurez la stratégie de SSL et SSL de bout en bout avec Application passerelle | Microsoft Azure"
    description="Cet article explique comment configurer SSL de bout en bout avec passerelle d’Application à l’aide de Azure Gestionnaire de ressources PowerShell"
    services="application-gateway"
    documentationCenter="na"
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

# <a name="configure-ssl-policy-and-end-to-end-ssl-with-application-gateway-using-powershell"></a>Configurez la stratégie de SSL et SSL de bout en bout avec passerelle d’Application à l’aide de PowerShell

## <a name="overview"></a>Vue d’ensemble

Application passerelle prend en charge le chiffrement de bout en bout du trafic. Application passerelle pour cela, en mettant fin à la connexion SSL au niveau de la passerelle d’application. La passerelle puis applique les règles de routage pour le trafic, nouveau chiffre des paquets et transfère le paquet vers le serveur principal approprié basé sur les règles de routage définis. Toutes les réponses à partir du serveur web accède à la même procédure revenir à l’utilisateur final.

Une autre fonctionnalité passerelle application prend en charge désactivation de certaines versions de protocole SSL. Application passerelle prend en charge la désactivation de la version du protocole suivant ; TLSv1.0, TLSv1.1 et TLSv1.2.

> [AZURE.NOTE] SSL 2.0 et SSL 3.0 sont désactivées par défaut et ne peut pas être activé. Ils sont considérés comme non sécurisés et ne parviennent pas à utiliser avec la passerelle d’Application

![image de scénario][scenario]

## <a name="scenario"></a>Scénario

Dans ce scénario, vous allez apprendre à créer une passerelle d’application à l’aide de SSL de bout en bout à l’aide de PowerShell.

Ce scénario sera :

- Créer un groupe de ressources nommé « appgw-routage »
- Créer un réseau virtuel nommé « appgwvnet » avec un bloc CIDR réservé de 10.0.0.0/16.
- Créez deux sous-réseaux appelés « appgwsubnet » et « appsubnet ».
- Créer une passerelle petite application prenant en charge le chiffrement SSL bout en bout qui désactive certains protocoles SSL.

## <a name="before-you-begin"></a>Avant de commencer

Pour configurer SSL de bout en bout avec une passerelle d’application, un certificat est requis pour la passerelle et des certificats sont requis pour les serveurs principaux. Le certificat de passerelle est utilisé pour chiffrer et déchiffrer le trafic envoyé à l’aide de SSL. Le certificat de passerelle doit être au format échange d’informations personnelles (pfx). Ce format de fichier permet de la clé privée à exporter est requis par la passerelle d’application pour effectuer le chiffrement et déchiffrement du trafic.

Pour le chiffrement ssl de bout en bout le système principal doit être autorisés à une passerelle de l’application. Pour cela, vous devez télécharger le certificat public des serveurs principaux de la passerelle d’application. Cela garantit que la passerelle d’application communique uniquement avec les instances de serveur principal connus. Cela permet davantage de sécuriser la communication de bout en bout.

Ce processus est décrit dans les étapes suivantes :

## <a name="create-the-resource-group"></a>Créer le groupe de ressources

Cette section vous guide dans la création d’un groupe de ressources, qui contient la passerelle d’application.

### <a name="step-1"></a>Étape 1

Connectez-vous à votre compte Azure.

    Login-AzureRmAccount

### <a name="step-2"></a>Étape 2

Sélectionnez l’abonnement à utiliser pour ce scénario.

    Select-AzureRmsubscription -SubscriptionName "<Subscription name>"

### <a name="step-3"></a>Étape 3

Créer un groupe de ressources (ignorer cette étape si vous utilisez un groupe de ressources existant).

    New-AzureRmResourceGroup -Name appgw-rg -Location "West US"

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Créer un réseau virtuel et un sous-réseau pour la passerelle d’application

L’exemple suivant crée un réseau virtuel et deux sous-réseaux. Un sous-réseau est utilisé pour inclure la passerelle d’application. Le second sous-réseau est utilisé pour les serveurs principaux qui héberge l’application web.

### <a name="step-1"></a>Étape 1

Affecter une plage d’adresses pour le sous-réseau être utilisés pour la passerelle d’Application elle-même.

    $gwSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24

> [AZURE.NOTE] Sous-réseaux configurés pour la passerelle d’application doivent être correctement dimensionnés. Une passerelle d’application peut être configurée pour jusqu'à 10 instances. Chaque instance prend 1 adresse IP du sous-réseau. Trop petit d’un sous-réseau peut affecter l’évolution horizontale une passerelle d’application.

### <a name="step-2"></a>Étape 2

Affecter une plage d’adresses à utiliser pour le pool d’adresses principal.

    $nicSubnet = New-AzureRmVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24

### <a name="step-3"></a>Étape 3

Créer un réseau virtuel avec les sous-réseaux définis dans les étapes précédentes.

    $vnet = New-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet

### <a name="step-4"></a>Étape 4

Récupérer les ressources de réseau virtuel et les ressources de sous-réseau à utiliser dans les étapes suivantes :

    $vnet = Get-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg
    $gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -VirtualNetwork $vnet
    $nicSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appsubnet' -VirtualNetwork $vnet

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Créer une adresse IP publique pour la configuration frontale

Créez une ressource IP publique à utiliser pour la passerelle d’application. Cette adresse IP publique est utilisée une étape suivante.

    $publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -Name 'publicIP01' -Location "West US" -AllocationMethod Dynamic

> [AZURE.IMPORTANT] Application passerelle ne prend pas en charge l’utilisation d’une adresse IP publique créée avec une étiquette de domaine définie. Uniquement une adresse IP publique avec un nom de domaine créés de façon dynamique est prise en charge. Si vous avez besoin d’un nom convivial dns pour la passerelle d’application, il est recommandé d’utiliser un enregistrement cname en tant qu’alias.

## <a name="create-an-application-gateway-configuration-object"></a>Créer un objet de configuration de passerelle application

Vous devez configurer tous les éléments de configuration avant de créer la passerelle d’application. Les étapes suivantes créent les éléments de configuration sont nécessaires pour une ressource de passerelle d’application.

### <a name="step-1"></a>Étape 1

Créer une configuration d’application passerelle IP, ce paramètre configure quel sous-réseau utilise la passerelle d’application. Au démarrage de passerelle d’application, il sélectionne une adresse IP à partir du sous-réseau configuré et achemine le trafic réseau aux adresses IP du pool d’IP principale. N’oubliez pas que chaque instance prend une seule adresse IP.

    $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet

### <a name="step-2"></a>Étape 2

Créer une configuration IP frontale, ce paramètre mappe une adresse ip publique ou privée au serveur frontal de la passerelle d’application. L’étape suivante associe l’adresse IP à l’étape précédente à la configuration IP frontale.

    $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip

### <a name="step-3"></a>Étape 3

Configurer le pool d’adresses IP principale et les adresses IP des serveurs web principaux. Ces adresses IP sont les adresses IP recevoir le trafic réseau provenant du point de terminaison IP frontale. Vous remplacez les adresses IP suivantes pour ajouter vos propres points de terminaison d’adresse IP application.

    $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 1.1.1.1, 2.2.2.2, 3.3.3.3

> [AZURE.NOTE] Un nom de domaine complet (FQDN) est également une valeur valide à la place d’une adresse ip pour les serveurs principaux en utilisant le commutateur - BackendFqdns.

### <a name="step-4"></a>Étape 4

Configurer le port IP frontal du point de terminaison IP public. Ce port est le port auquel se connecter aux utilisateurs finaux.

    $fp = New-AzureRmApplicationGatewayFrontendPort -Name 'port01'  -Port 443

### <a name="step-5"></a>Étape 5

Configurer le certificat pour la passerelle d’application. Ce certificat est utilisé pour déchiffrer et chiffrer à nouveau le trafic sur la passerelle d’application.

    $cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password <password for certificate file>

> [AZURE.NOTE] Cet exemple configure le certificat utilisé pour la connexion SSL. Le certificat doit être au format .pfx et le mot de passe doit être comprise entre 4 à 12 caractères.

### <a name="step-6"></a>Étape 6

Créer le récepteur HTTP pour la passerelle d’application. Attribuer le certificat de configuration, port et ssl ip frontale à utiliser.

    $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert

### <a name="step-7"></a>Étape 7

Téléchargez le certificat à utiliser sur les ressources de pool principal ssl est activé.

> [AZURE.NOTE] La sonde par défaut Obtient la clé publique à partir de la liaison SSL **par défaut** sur l’adresse IP de principale et compare la valeur de clé publique qu’il reçoit vers la valeur de clé publique vous fournissez ici. La clé publique récupérée peut-être pas nécessairement au site concerné auquel le trafic passera **Si** vous utilisez en-têtes d’hôte et SNI sur le serveur principal. En cas de doute, visitez https://127.0.0.1/ sur les extrémités de précédent pour confirmer le certificat utilisé pour la liaison SSL **par défaut** . Utiliser la clé publique à partir de cette demande dans cette section. Si vous utilisez en-têtes d’hôte et SNI sur les liaisons HTTPS et vous ne recevez pas un certificat et réponse à partir d’une demande de navigateur manuel à https://127.0.0.1/ sur les extrémités précédent, vous devez configurer une liaison SSL par défaut sur les extrémités précédent. Si vous ne procédez pas ainsi, sondes échoue et le principaux être ne seront pas autorisés.

    $authcert = New-AzureRmApplicationGatewayAuthenticationCertificate -Name 'whitelistcert1' -CertificateFile C:\users\gwallace\Desktop\cert.cer

> [AZURE.NOTE] Le certificat fourni dans cette étape doit être la clé publique du certificat pfx présent sur le serveur principal. Exportez le certificat (pas le certificat racine) installé sur le serveur principal dans. Limitée mettre en forme et son utilisation dans cette étape. Cette étape blocage du serveur principal avec la passerelle d’application.

### <a name="step-8"></a>Étape 8

Configurer les paramètres application passerelle principale http. Attribuer le certificat téléchargé à l’étape précédente pour les paramètres http.

    $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name 'setting01' -Port 443 -Protocol Https -CookieBasedAffinity Enabled -AuthenticationCertificates $authcert

### <a name="step-9"></a>Étape 9

Créer une règle de routage de l’équilibrage de charge qui configure le comportement d’équilibrage de charge. Dans cet exemple, une règle de base alternées est créée.

    $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

### <a name="step-10"></a>Étape 10

Configurer la taille de l’instance de la passerelle d’application.  Les tailles disponibles sont **Standard\_petite**, **Standard\_support**, et **Standard\_grande**.  Pour la capacité, les valeurs disponibles sont 1 et 10.

    $sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

>[AZURE.NOTE] Vous pouvez choisir un nombre d’instances de 1 à des fins de test. Il est important de savoir que n’importe quel nombre d’instances sous deux instances n’est pas traité par le contrat SLA et sont donc pas recommandée. Petites passerelles doivent être utilisés pour le test de développement et non à des fins de production.

### <a name="step-11"></a>Étape 11

Configurer la stratégie SSL à utiliser sur la passerelle d’Application. Application passerelle prend en charge la possibilité de désactiver certaines versions de protocole SSL.

Les valeurs suivantes sont une liste des versions de protocole peut être désactivée.

- **TLSv1_0**
- **TLSv1_1**
- **TLSv1_2**

L’exemple suivant désactive TLSv1\_0.

    $sslPolicy = New-AzureRmApplicationGatewaySslPolicy -DisabledSslProtocols TLSv1_0

## <a name="create-the-application-gateway"></a>Créer la passerelle d’Application

Utilisez toutes les étapes précédentes pour créer la passerelle d’Application. La création de la passerelle est un processus long.

    $appgw = New-AzureRmApplicationGateway -Name appgateway -SslCertificates $cert -ResourceGroupName "appgw-rg" -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SslPolicy $sslPolicy -AuthenticationCertificates $authcert -Verbose

## <a name="disable-ssl-protocol-versions-on-an-existing-application-gateway"></a>Désactiver les versions de protocole SSL sur une passerelle d’Application existant

Les étapes précédentes alors via la création d’une application avec ssl de bout en bout et la désactivation de certaines versions de protocole SSL. L’exemple suivant désactive certaines stratégies SSL sur une passerelle d’application existant.

### <a name="step-1"></a>Étape 1

Récupérer la passerelle d’application pour mettre à jour.

    $gw = Get-AzureRmApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG

### <a name="step-2"></a>Étape 2

Définir une stratégie SSL. Dans l’exemple suivant, TLSv1.0 et TLSv1.1 sont désactivés.

    Set-AzureRmApplicationGatewaySslPolicy -DisabledSslProtocols TLSv1_0, TLSv1_1 -ApplicationGateway $gw

### <a name="step-3"></a>Étape 3

Enfin, mettez à jour la passerelle. Il est important de noter que cette dernière étape est une tâche longue. Lorsqu’il est terminé, ssl de bout en bout est configuré sur la passerelle d’application.

    $gw | Set-AzureRmApplicationGateway

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

En savoir plus sur renforcer la sécurité de vos applications web avec Web Application pare-feu via la passerelle d’Application, visitez le [Pare-feu présentation de l’Application Web](application-gateway-webapplicationfirewall-overview.md)

[scenario]: ./media/application-gateway-end-to-end-ssl-powershell/scenario.png
