<properties
   pageTitle="Créer, démarrer ou supprimer une passerelle d’application | Microsoft Azure"
   description="Cette page fournit des instructions pour créer, configurer, démarrer et supprimer une passerelle d’application Azure"
   documentationCenter="na"
   services="application-gateway"
   authors="georgewallace"
   manager="carmonm"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/25/2016"
   ms.author="gwallace"/>

# <a name="create-start-or-delete-an-application-gateway"></a>Créer, démarrer ou supprimer une passerelle d’application

> [AZURE.SELECTOR]
- [Portail Azure](application-gateway-create-gateway-portal.md)
- [Azure PowerShell Gestionnaire de ressources](application-gateway-create-gateway-arm.md)
- [Azure PowerShell classique](application-gateway-create-gateway.md)
- [Modèle de gestionnaire de ressources Azure](application-gateway-create-gateway-arm-template.md)
- [Azure infrastructure du langage commun](application-gateway-create-gateway-cli.md)

Azure Application passerelle est un équilibrage de charge de calque à 7. Il propose basculement, routage des performances des requêtes HTTP entre différents serveurs, qu’ils soient sur le nuage ou sur site. Application passerelle fournit de nombreuses fonctionnalités contrôleur de remise Application (ADC), y compris l’équilibrage de charge HTTP, affinité session basée sur les cookies, calque SSL (Secure Sockets) décharger, état personnalisé sondes, prise en charge de plusieurs sites et bien d’autres. Pour rechercher une liste complète des fonctionnalités prises en charge, consultez [Vue d’ensemble de la passerelle Application](application-gateway-introduction.md)

Cet article vous explique comment procéder pour créer, configurer, démarrer et supprimer une passerelle d’application.

## <a name="before-you-begin"></a>Avant de commencer

1. Installer la dernière version des applets de commande PowerShell Azure à l’aide de Web Platform Installer. Vous pouvez télécharger et installer la dernière version de la section **Windows PowerShell** de la [page de téléchargement](https://azure.microsoft.com/downloads/).
2. Si vous avez déjà un réseau virtuel, sélectionner un sous-réseau vide existant ou en créer un nouveau sous-réseau dans votre réseau existant virtuel uniquement pour une utilisation par la passerelle d’application. Vous ne pouvez pas déployer la passerelle d’application à un autre réseau virtuel que les ressources que vous voulez déployer derrière la passerelle d’application, sauf si vnet peering est utilisé. Pour en savoir plus, visitez [Vnet Peering](../virtual-network/virtual-network-peering-overview.md)
3. Vérifiez que vous disposez d’un réseau virtuel de travail avec un sous-réseau valide. Vérifiez qu’aucune machines virtuelles ou des déploiements dans le cloud n’utilisent le sous-réseau. La passerelle d’application elle-même doit être dans un sous-réseau virtuel.
3. Les serveurs que vous configurez pour utiliser la passerelle d’application doivent exister ou ont leurs points de terminaison créés dans le réseau virtuel ou avec un public IP/VIP affectées.

## <a name="what-is-required-to-create-an-application-gateway"></a>Ce qui est requis pour créer une passerelle d’application ?

Lorsque vous utilisez la commande **New-AzureApplicationGateway** pour créer la passerelle d’application, aucune configuration n’est définie à ce stade et la ressource nouvellement créée sont configurés en utilisant XML ou un objet de configuration.

Les valeurs sont :

- **Pool du serveur principal :** La liste d’adresses IP des serveurs principale. Présente les adresses IP doivent appartenir soit au sous-réseau virtuel ou doivent être un IP/VIP publique.
- **Paramètres du pool serveur principal :** Chaque pool inclut des paramètres, tels qu’affinité basée sur les cookies, port et protocole. Ces paramètres sont liés à un pool et sont appliquées à tous les serveurs au sein du pool.
- **Port frontal :** Ce port est le port public est ouvert sur la passerelle d’application. Le trafic appuie sur ce port et puis redirigé à un des serveurs principale.
- **Récepteur :** Le récepteur inclut un port frontal, un protocole (Http ou Https, ces valeurs respectent la casse) et le nom du certificat SSL (si la configuration de SSL décharge).
- **Règle :** La règle lie le récepteur et au pool de serveur principal et définit le pool de serveur principal le trafic doit être dirigé vers lorsqu’il rencontre un récepteur particulier.

## <a name="create-an-application-gateway"></a>Créer une passerelle d’application

Pour créer une passerelle d’application :

1. Créer une ressource de passerelle d’application.
2. Créer un fichier XML de configuration ou un objet de configuration.
3. Valider la configuration de la ressource de passerelle application nouvellement créé.

>[AZURE.NOTE] Si vous avez besoin configurer une sonde personnalisé pour votre passerelle d’application, voir [créer une passerelle d’application avec sondes personnalisé à l’aide de PowerShell](application-gateway-create-probe-classic-ps.md). Consultez [sondes personnalisé et le contrôle d’état](application-gateway-probe-overview.md) pour plus d’informations.

![Exemple de scénario][scenario]

### <a name="create-an-application-gateway-resource"></a>Créer une ressource de passerelle d’application

Pour créer la passerelle, utilisez l’applet de commande **New-AzureApplicationGateway** , remplacer les valeurs par la vôtre. Facturation pour la passerelle ne démarre pas à ce stade. Facturation commence plus tard, lorsque la passerelle est démarrée.

L’exemple suivant crée une passerelle d’application à l’aide d’un réseau virtuel appelé « testvnet1 » et un sous-réseau appelée « sous-réseau-1 ».


    New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")

    VERBOSE: 4:31:35 PM - Begin Operation: New-AzureApplicationGateway
    VERBOSE: 4:32:37 PM - Completed Operation: New-AzureApplicationGateway
    Name       HTTP Status Code     Operation ID                             Error
    ----       ----------------     ------------                             ----
    Successful OK                   55ef0460-825d-2981-ad20-b9a8af41b399


 *Description*, *InstanceCount*et *GatewaySize* sont des paramètres facultatifs.

Pour valider que la passerelle a été créée, vous pouvez utiliser l’applet de commande **Get-AzureApplicationGateway** .

    Get-AzureApplicationGateway AppGwTest
    Name          : AppGwTest
    Description   :
    VnetName      : testvnet1
    Subnets       : {Subnet-1}
    InstanceCount : 2
    GatewaySize   : Medium
    State         : Stopped
    VirtualIPs    : {}
    DnsName       :

>[AZURE.NOTE]  La valeur par défaut pour *InstanceCount* est 2, avec une valeur maximale de 10. La valeur par défaut pour *GatewaySize* est adaptée au support. Vous pouvez choisir entre petite, moyenne et grande.

*VirtualIPs* et *NomDNS* apparaissent comme vide, car la passerelle n’a pas encore commencé. Ceux-ci sont créés une fois que la passerelle est en cours d’exécution.

## <a name="configure-the-application-gateway"></a>Configurer la passerelle d’application

Vous pouvez configurer la passerelle d’application à l’aide de XML ou un objet de configuration.

## <a name="configure-the-application-gateway-by-using-xml"></a>Configurer la passerelle d’application à l’aide de XML

Dans l’exemple suivant, vous utilisez un fichier XML pour configurer les paramètres de passerelle toutes les applications et les valider à la ressource de passerelle application.  

### <a name="step-1"></a>Étape 1  

Copier le texte suivant dans le bloc-notes.

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
        <FrontendPorts>
            <FrontendPort>
                <Name>(name-of-your-frontend-port)</Name>
                <Port>(port number)</Port>
            </FrontendPort>
        </FrontendPorts>
        <BackendAddressPools>
            <BackendAddressPool>
                <Name>(name-of-your-backend-pool)</Name>
                <IPAddresses>
                    <IPAddress>(your-IP-address-for-backend-pool)</IPAddress>
                    <IPAddress>(your-second-IP-address-for-backend-pool)</IPAddress>
                </IPAddresses>
            </BackendAddressPool>
        </BackendAddressPools>
        <BackendHttpSettingsList>
            <BackendHttpSettings>
                <Name>(backend-setting-name-to-configure-rule)</Name>
                <Port>80</Port>
                <Protocol>[Http|Https]</Protocol>
                <CookieBasedAffinity>Enabled</CookieBasedAffinity>
            </BackendHttpSettings>
        </BackendHttpSettingsList>
        <HttpListeners>
            <HttpListener>
                <Name>(name-of-the-listener)</Name>
                <FrontendPort>(name-of-your-frontend-port)</FrontendPort>
                <Protocol>[Http|Https]</Protocol>
            </HttpListener>
        </HttpListeners>
        <HttpLoadBalancingRules>
            <HttpLoadBalancingRule>
                <Name>(name-of-load-balancing-rule)</Name>
                <Type>basic</Type>
                <BackendHttpSettings>(backend-setting-name-to-configure-rule)</BackendHttpSettings>
                <Listener>(name-of-the-listener)</Listener>
                <BackendAddressPool>(name-of-your-backend-pool)</BackendAddressPool>
            </HttpLoadBalancingRule>
        </HttpLoadBalancingRules>
    </ApplicationGatewayConfiguration>

Modifiez les valeurs entre les parenthèses pour les éléments de configuration. Enregistrez le fichier avec une extension .xml.

>[AZURE.IMPORTANT] L’élément de protocole Http ou Https respecte la casse.

L’exemple suivant montre comment utiliser un fichier de configuration pour configurer la passerelle d’application. La charge exemple équilibre le trafic HTTP sur le port public 80 et envoie le trafic réseau principale port 80 entre deux adresses IP.

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
        <FrontendPorts>
            <FrontendPort>
                <Name>FrontendPort1</Name>
                <Port>80</Port>
            </FrontendPort>
        </FrontendPorts>
        <BackendAddressPools>
            <BackendAddressPool>
                <Name>BackendPool1</Name>
                <IPAddresses>
                    <IPAddress>10.0.0.1</IPAddress>
                    <IPAddress>10.0.0.2</IPAddress>
                </IPAddresses>
            </BackendAddressPool>
        </BackendAddressPools>
        <BackendHttpSettingsList>
            <BackendHttpSettings>
                <Name>BackendSetting1</Name>
                <Port>80</Port>
                <Protocol>Http</Protocol>
                <CookieBasedAffinity>Enabled</CookieBasedAffinity>
            </BackendHttpSettings>
        </BackendHttpSettingsList>
        <HttpListeners>
            <HttpListener>
                <Name>HTTPListener1</Name>
                <FrontendPort>FrontendPort1</FrontendPort>
                <Protocol>Http</Protocol>
            </HttpListener>
        </HttpListeners>
        <HttpLoadBalancingRules>
            <HttpLoadBalancingRule>
                <Name>HttpLBRule1</Name>
                <Type>basic</Type>
                <BackendHttpSettings>BackendSetting1</BackendHttpSettings>
                <Listener>HTTPListener1</Listener>
                <BackendAddressPool>BackendPool1</BackendAddressPool>
            </HttpLoadBalancingRule>
        </HttpLoadBalancingRules>
    </ApplicationGatewayConfiguration>


### <a name="step-2"></a>Étape 2

Ensuite, définissez la passerelle d’application. Utiliser l’applet de commande **Set-AzureApplicationGatewayConfig** avec un fichier XML de configuration.


    Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile "D:\config.xml"

    VERBOSE: 7:54:59 PM - Begin Operation: Set-AzureApplicationGatewayConfig
    VERBOSE: 7:55:32 PM - Completed Operation: Set-AzureApplicationGatewayConfig
    Name       HTTP Status Code     Operation ID                             Error
    ----       ----------------     ------------                             ----
    Successful OK                   9b995a09-66fe-2944-8b67-9bb04fcccb9d

## <a name="configure-the-application-gateway-by-using-a-configuration-object"></a>Configurer la passerelle d’application à l’aide d’un objet de configuration

L’exemple suivant montre comment configurer la passerelle d’application à l’aide des objets de configuration. Tous les éléments de configuration doivent être configurés individuellement et ensuite ajoutés à un objet de configuration de passerelle application. Après avoir créé l’objet de configuration, vous utilisez la commande **Set-AzureApplicationGateway** pour valider la configuration de la ressource de passerelle application créé précédemment.

>[AZURE.NOTE] Avant d’assigner une valeur à chaque objet de configuration, vous devez déclarer quel type d’objet PowerShell utilise pour le stockage. La première ligne pour créer les éléments individuels définit quels Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model (nom de l’objet) sont utilisés.

### <a name="step-1"></a>Étape 1

Créer tous les éléments de configuration individuels.

Créer la période d’enquête frontale comme le montre l’exemple suivant.

    $fip = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendIPConfiguration
    $fip.Name = "fip1"
    $fip.Type = "Private"
    $fip.StaticIPAddress = "10.0.0.5"

Créer le port frontal comme le montre l’exemple suivant.

    $fep = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendPort
    $fep.Name = "fep1"
    $fep.Port = 80

Créer le pool de serveur principal.

 Définir les adresses IP qui sont ajoutés au pool de serveur principal comme illustré dans l’exemple suivant.


    $servers = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendServerCollection
    $servers.Add("10.0.0.1")
    $servers.Add("10.0.0.2")

 Utilisez l’objet $server pour ajouter les valeurs à l’objet de regroupement de données principale ($pool).

    $pool = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendAddressPool
    $pool.BackendServers = $servers
    $pool.Name = "pool1"

Créer le paramètre de pool serveur principal.

    $setting = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendHttpSettings
    $setting.Name = "setting1"
    $setting.CookieBasedAffinity = "enabled"
    $setting.Port = 80
    $setting.Protocol = "http"

Créer le récepteur.

    $listener = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpListener
    $listener.Name = "listener1"
    $listener.FrontendPort = "fep1"
    $listener.FrontendIP = "fip1"
    $listener.Protocol = "http"
    $listener.SslCert = ""

Créez la règle.

    $rule = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpLoadBalancingRule
    $rule.Name = "rule1"
    $rule.Type = "basic"
    $rule.BackendHttpSettings = "setting1"
    $rule.Listener = "listener1"
    $rule.BackendAddressPool = "pool1"

### <a name="step-2"></a>Étape 2

Attribuer tous les éléments de configuration individuels à un objet de configuration de passerelle application ($appgwconfig).

Ajoutez l’adresse IP frontal à la configuration.

    $appgwconfig = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.ApplicationGatewayConfiguration
    $appgwconfig.FrontendIPConfigurations = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendIPConfiguration]"
    $appgwconfig.FrontendIPConfigurations.Add($fip)

Ajouter le port frontal à la configuration.

    $appgwconfig.FrontendPorts = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendPort]"
    $appgwconfig.FrontendPorts.Add($fep)

Ajouter le pool de serveur principal à la configuration.

    $appgwconfig.BackendAddressPools = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendAddressPool]"
    $appgwconfig.BackendAddressPools.Add($pool)

Ajouter le paramètre de pool principale à la configuration.

    $appgwconfig.BackendHttpSettingsList = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendHttpSettings]"
    $appgwconfig.BackendHttpSettingsList.Add($setting)

Ajouter le récepteur à la configuration.

    $appgwconfig.HttpListeners = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpListener]"
    $appgwconfig.HttpListeners.Add($listener)

Ajouter à la configuration de la règle.

    $appgwconfig.HttpLoadBalancingRules = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpLoadBalancingRule]"
    $appgwconfig.HttpLoadBalancingRules.Add($rule)

### <a name="step-3"></a>Étape 3

Valider l’objet de configuration dans la ressource de passerelle d’application à l’aide de **Jeu AzureApplicationGatewayConfig**.

    Set-AzureApplicationGatewayConfig -Name AppGwTest -Config $appgwconfig

## <a name="start-the-gateway"></a>Démarrer la passerelle

Une fois que la passerelle a été configurée, utilisez l’applet de commande **Démarrer AzureApplicationGateway** pour démarrer la passerelle. Facturation pour une passerelle d’application commence après que la passerelle a été démarrée.

> [AZURE.NOTE] L’applet de commande **Démarrer AzureApplicationGateway** peut prendre jusqu'à 15-20 minutes pour terminer.

    Start-AzureApplicationGateway AppGwTest

    VERBOSE: 7:59:16 PM - Begin Operation: Start-AzureApplicationGateway
    VERBOSE: 8:05:52 PM - Completed Operation: Start-AzureApplicationGateway
    Name       HTTP Status Code     Operation ID                             Error
    ----       ----------------     ------------                             ----
    Successful OK                   fc592db8-4c58-2c8e-9a1d-1c97880f0b9b

## <a name="verify-the-gateway-status"></a>Vérifier l’état de la passerelle

Utiliser l’applet de commande **Get-AzureApplicationGateway** pour vérifier l’état de la passerelle. Si **Début AzureApplicationGateway** a réussi à l’étape précédente, *état* doit s’exécuter, et *Vip* et *NomDNS* doivent avoir des entrées valides.

L’exemple suivant montre une passerelle d’application qui est en cours d’exécution, vers le haut et prête à prendre le trafic destiné à `http://<generated-dns-name>.cloudapp.net`.

    Get-AzureApplicationGateway AppGwTest

    VERBOSE: 8:09:28 PM - Begin Operation: Get-AzureApplicationGateway
    VERBOSE: 8:09:30 PM - Completed Operation: Get-AzureApplicationGateway
    Name          : AppGwTest
    Description   :
    VnetName      : testvnet1
    Subnets       : {Subnet-1}
    InstanceCount : 2
    GatewaySize   : Medium
    State         : Running
    Vip           : 138.91.170.26
    DnsName       : appgw-1b8402e8-3e0d-428d-b661-289c16c82101.cloudapp.net


## <a name="delete-an-application-gateway"></a>Supprimer une passerelle d’application

Pour supprimer une passerelle d’application :

1. Utiliser l’applet de commande **Stop AzureApplicationGateway** pour arrêter la passerelle.
2. Utiliser l’applet de commande **Supprimer AzureApplicationGateway** pour supprimer la passerelle.
3. Vérifiez que la passerelle a été supprimée à l’aide de l’applet de commande **Get-AzureApplicationGateway** .

L’exemple suivant montre l’applet de commande **Arrêter AzureApplicationGateway** sur la première ligne, suivie de la sortie.

    Stop-AzureApplicationGateway AppGwTest

    VERBOSE: 9:49:34 PM - Begin Operation: Stop-AzureApplicationGateway
    VERBOSE: 10:10:06 PM - Completed Operation: Stop-AzureApplicationGateway
    Name       HTTP Status Code     Operation ID                             Error
    ----       ----------------     ------------                             ----
    Successful OK                   ce6c6c95-77b4-2118-9d65-e29defadffb8

Une fois que la passerelle d’application est dans un état arrêté, utilisez l’applet de commande **AzureApplicationGateway de supprimer** pour supprimer le service.


    Remove-AzureApplicationGateway AppGwTest

    VERBOSE: 10:49:34 PM - Begin Operation: Remove-AzureApplicationGateway
    VERBOSE: 10:50:36 PM - Completed Operation: Remove-AzureApplicationGateway
    Name       HTTP Status Code     Operation ID                             Error
    ----       ----------------     ------------                             ----
    Successful OK                   055f3a96-8681-2094-a304-8d9a11ad8301

Pour vérifier que le service a été supprimé, vous pouvez utiliser l’applet de commande **Get-AzureApplicationGateway** . Cette étape n’est pas obligatoire.


    Get-AzureApplicationGateway AppGwTest

    VERBOSE: 10:52:46 PM - Begin Operation: Get-AzureApplicationGateway

    Get-AzureApplicationGateway : ResourceNotFound: The gateway does not exist.
    .....

## <a name="next-steps"></a>Étapes suivantes

Si vous voulez configurer déchargement SSL, voir [configurer une passerelle d’application pour SSL décharger](application-gateway-ssl.md).

Si vous souhaitez configurer une passerelle d’application à utiliser avec un programme d’équilibrage de charge interne, voir [créer une passerelle d’application avec un programme d’équilibrage de charge interne (ILB)](application-gateway-ilb.md).

Si vous souhaitez plus d’informations sur Charger les options d’équilibrage de charge en général, voir :

- [Équilibrage de charge Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Gestionnaire de trafic Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)

[scenario]: ./media/application-gateway-create-gateway/scenario.png