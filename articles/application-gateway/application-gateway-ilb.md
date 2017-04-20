<properties 
   pageTitle="Créer et configurer une passerelle d’Application avec équilibrage de charge interne (ILB) dans un réseau virtuel | Microsoft Azure"
   description="Cette page fournit des instructions pour configurer une passerelle d’Application Azure avec un point de terminaison équilibrage de charge interne"
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
   ms.date="08/19/2016"
   ms.author="gwallace"/>

# <a name="create-an-application-gateway-with-an-internal-load-balancer-ilb"></a>Créer une passerelle d’Application avec un programme d’équilibrage de charge interne (ILB)

> [AZURE.SELECTOR]
- [Étapes classiques Azure](application-gateway-ilb.md)
- [Étapes Powershell Gestionnaire de ressources](application-gateway-ilb-arm.md)

Application passerelle peut être configurée via une connexion internet les en regard d’adresse IP virtuelle ou avec point de terminaison interne ne pas visible sur internet, également connu sous le point de terminaison équilibrage de charge interne (ILB). Configuration de la passerelle avec un ILB est utile pour les applications professionnelles internes ne pas visible non plues sur internet. Il est également utile pour les services/niveaux au sein d’une application à plusieurs niveaux, qui se trouve dans une limite de sécurité ne pas visible non plue sur internet, mais nécessitent toujours la distribution de charge alternées, caractère collant session ou arrêt de SSL. Cet article décrit les étapes de configuration d’une passerelle d’application avec un ILB.

## <a name="before-you-begin"></a>Avant de commencer

1. Installer la dernière version des applets de commande PowerShell Azure à l’aide de Web Platform Installer. Vous pouvez télécharger et installer la dernière version de la section **Windows PowerShell** de la [page de téléchargement](https://azure.microsoft.com/downloads/).
2. Vérifiez que vous disposez d’un réseau virtuel travail avec sous-réseau valide.
3. Vérifiez que vous disposez des serveurs principaux dans le réseau virtuel, ou avec un public IP/VIP affectées.


Pour créer une passerelle d’application, effectuez les opérations suivantes dans l’ordre indiqué. 

1. [Créer une passerelle d’application](#create-a-new-application-gateway)
2. [Configuration de la passerelle](#configure-the-gateway)
3. [Définir la configuration de passerelle](#set-the-gateway-configuration)
4. [Démarrer la passerelle](#start-the-gateway)
4. [Vérifiez que la passerelle](#verify-the-gateway-status)



## <a name="create-an-application-gateway"></a>Créer une passerelle d’application :

**Pour créer la passerelle**, utilisez la `New-AzureApplicationGateway` applet de commande, remplacer les valeurs par la vôtre. Notez que facturation pour la passerelle ne démarre pas à ce stade. Facturation commence plus tard, lorsque la passerelle est démarrée.

    PS C:\> New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")

    VERBOSE: 4:31:35 PM - Begin Operation: New-AzureApplicationGateway 
    VERBOSE: 4:32:37 PM - Completed Operation: New-AzureApplicationGateway
    Name       HTTP Status Code     Operation ID                             Error 
    ----       ----------------     ------------                             ----
    Successful OK                   55ef0460-825d-2981-ad20-b9a8af41b399

**Pour valider** que la passerelle a été créée, vous pouvez utiliser la `Get-AzureApplicationGateway` applet de commande. 

Dans l’exemple, la *Description*, *InstanceCount*et *GatewaySize* sont des paramètres facultatifs. La valeur par défaut pour *InstanceCount* est 2, avec une valeur maximale de 10. La valeur par défaut pour *GatewaySize* est adaptée au support. Petites et grandes existe d’autres valeurs disponibles. *VIP* et *NomDNS* apparaissent comme vide, car la passerelle n’a pas encore commencé. Ceux-ci sont créés une fois que la passerelle est en cours d’exécution. 

    PS C:\> Get-AzureApplicationGateway AppGwTest

    VERBOSE: 4:39:39 PM - Begin Operation:
    Get-AzureApplicationGateway VERBOSE: 4:39:40 PM - Completed 
    Operation: Get-AzureApplicationGateway
    Name: AppGwTest 
    Description: 
    VnetName: testvnet1 
    Subnets: {Subnet-1} 
    InstanceCount: 2 
    GatewaySize: Medium 
    State: Stopped 
    VirtualIPs: 
    DnsName:


## <a name="configure-the-gateway"></a>Configuration de la passerelle

Configuration de passerelle une application se compose de plusieurs valeurs. Les valeurs peuvent être liés entre eux pour créer la configuration.
 
Les valeurs sont :

- **Pool du serveur principal :** La liste d’adresses IP des serveurs principaux. Présente les adresses IP doivent appartenir soit au sous-réseau VNet ou doivent être un IP/VIP publique. 
- **Paramètres du pool serveur principal :** Chaque pool inclut des paramètres, tels qu’affinité basée sur les cookies, port et protocole. Ces paramètres sont liés à un pool et sont appliquées à tous les serveurs au sein du pool.
- **Port Frontend :** Ce port est le port public ouvert sur la passerelle d’application. Le trafic appuie sur ce port et puis redirigé à un des serveurs principaux.
- **Récepteur :** Le récepteur inclut un port frontend, un protocole (Http ou Https, il s’agit des respectant la casse) et le nom du certificat SSL (si la configuration de SSL décharge). 
- **Règle :** La règle lie le récepteur et le pool de serveur principal et définit le pool de serveur principal le trafic doit être dirigé vers lorsqu’il rencontre un récepteur particulier. Pour l’instant, uniquement la règle de *base* est pris en charge. La règle de *base* est la distribution de charge cyclique.

Vous pouvez créer votre configuration en créant un objet de configuration, ou à l’aide d’un fichier XML de configuration. Pour créer votre configuration à l’aide d’un fichier XML de configuration, utilisez l’exemple ci-dessous.



Notez les points suivants :


- L’élément *FrontendIPConfigurations* décrit les détails ILB pertinents pour la configuration de passerelle d’Application avec un ILB. 

- Le *Type* de Frontend IP doit être définie sur « Privé »

- La *StaticIPAddress* doit être défini à l’adresse IP souhaitée interne sur lequel la passerelle reçoit le trafic. Notez que l’élément *StaticIPAddress* est facultatif. Si ce n’est pas ensemble, une adresse IP interne disponible à partir du sous-réseau déployée est sélectionnée. 

- La valeur de *nom* de l’élément spécifié dans *FrontendIPConfiguration* doit être utilisée dans *FrontendIP* élément de HTTPListener pour faire référence à la FrontendIPConfiguration.

 **Exemple de configuration XML**

 

        <?xml version="1.0" encoding="utf-8"?>
        <ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
            <FrontendIPConfigurations>
                <FrontendIPConfiguration>
                    <Name>fip1</Name> 
                    <Type>Private</Type> 
                    <StaticIPAddress>10.0.0.10</StaticIPAddress> 
                </FrontendIPConfiguration>
            </FrontendIPConfigurations>
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
                    <FrontendIP>fip1</FrontendIP>
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
    


## <a name="set-the-gateway-configuration"></a>Définir la configuration de passerelle

Ensuite, vous devez configurer la passerelle d’application. Vous pouvez utiliser la `Set-AzureApplicationGatewayConfig` applet de commande avec un objet de configuration, ou un fichier XML de configuration. 

    PS C:\> Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile D:\config.xml

    VERBOSE: 7:54:59 PM - Begin Operation: Set-AzureApplicationGatewayConfig 
    VERBOSE: 7:55:32 PM - Completed Operation: Set-AzureApplicationGatewayConfig
    Name       HTTP Status Code     Operation ID                             Error 
    ----       ----------------     ------------                             ----
    Successful OK                   9b995a09-66fe-2944-8b67-9bb04fcccb9d

## <a name="start-the-gateway"></a>Démarrer la passerelle

Une fois que la passerelle a été configurée, utilisez la `Start-AzureApplicationGateway` applet de commande pour démarrer la passerelle. Facturation pour une passerelle d’application commence après que la passerelle a été démarrée. 


> [AZURE.NOTE] La `Start-AzureApplicationGateway` applet de commande peut prendre jusqu'à 15-20 minutes. 
   
    PS C:\> Start-AzureApplicationGateway AppGwTest 

    VERBOSE: 7:59:16 PM - Begin Operation: Start-AzureApplicationGateway 
    VERBOSE: 8:05:52 PM - Completed Operation: Start-AzureApplicationGateway
    Name       HTTP Status Code     Operation ID                             Error 
    ----       ----------------     ------------                             ----
    Successful OK                   fc592db8-4c58-2c8e-9a1d-1c97880f0b9b

## <a name="verify-the-gateway-status"></a>Vérifier l’état de la passerelle

Utiliser la `Get-AzureApplicationGateway` applet de commande pour vérifier l’état de la passerelle. Si *Début AzureApplicationGateway* a réussi à l’étape précédente, l’état doit être *en cours d’exécution*, et la Vip et NomDNS doivent avoir entrées valides. Ceci montre l’applet de commande sur la première ligne, suivi par la sortie. Dans cet exemple, la passerelle est en cours d’exécution et est prête à prendre le trafic. 

> [AZURE.NOTE] La passerelle d’application est configurée pour accepter le trafic au point de terminaison ILB configuré de 10.0.0.10 dans cet exemple.

    PS C:\> Get-AzureApplicationGateway AppGwTest 

    VERBOSE: 8:09:28 PM - Begin Operation: Get-AzureApplicationGateway 
    VERBOSE: 8:09:30 PM - Completed Operation: Get-AzureApplicationGateway
    Name          : AppGwTest
    Description   : 
    VnetName      : testvnet1
    Subnets       : {Subnet-1}
    InstanceCount : 2
    GatewaySize   : Medium
    State         : Running
    VirtualIPs    : {10.0.0.10}
    DnsName       : appgw-b2a11563-2b3a-4172-a4aa-226ee4c23eed.cloudapp.net

## <a name="next-steps"></a>Étapes suivantes


Si vous souhaitez plus d’informations sur Charger les options d’équilibrage de charge en général, voir :

- [Équilibrage de charge Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Gestionnaire de trafic Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)
