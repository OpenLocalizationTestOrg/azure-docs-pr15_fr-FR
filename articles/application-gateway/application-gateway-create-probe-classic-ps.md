<properties
   pageTitle="Créer une sonde personnalisé pour passerelle d’Application à l’aide de PowerShell dans le modèle de déploiement classique | Microsoft Azure"
   description="Apprenez à créer une sonde personnalisée pour la passerelle d’Application à l’aide de PowerShell dans le modèle de déploiement classique"
   services="application-gateway"
   documentationCenter="na"
   authors="georgewallace"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/25/2016"
   ms.author="gwallace" />

# <a name="create-a-custom-probe-for-azure-application-gateway-classic-by-using-powershell"></a>Créer une sonde personnalisé pour Azure Application passerelle (classique) à l’aide de PowerShell

> [AZURE.SELECTOR]
- [Portail Azure](application-gateway-create-probe-portal.md)
- [Azure PowerShell Gestionnaire de ressources](application-gateway-create-probe-ps.md)
- [Azure PowerShell classique](application-gateway-create-probe-classic-ps.md)

[AZURE.INCLUDE [azure-probe-intro-include](../../includes/application-gateway-create-probe-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)]Découvrez comment [effectuer ces étapes en utilisant le modèle de gestionnaire de ressources](application-gateway-create-probe-ps.md).

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-an-application-gateway"></a>Créer une passerelle d’application

Pour créer une passerelle d’application :

1. Créer une ressource de passerelle d’application.
2. Créer un fichier XML de configuration ou un objet de configuration.
3. Valider la configuration de la ressource de passerelle application nouvellement créé.

### <a name="create-an-application-gateway-resource"></a>Créer une ressource de passerelle d’application

Pour créer la passerelle, utilisez l’applet de commande **New-AzureApplicationGateway** , remplacer les valeurs par la vôtre. Facturation pour la passerelle ne démarre pas à ce stade. Facturation commence plus tard, lorsque la passerelle est démarrée.

L’exemple suivant crée une passerelle d’application à l’aide d’un réseau virtuel appelé « testvnet1 » et un sous-réseau appelée « sous-réseau-1 ».

    New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")

Pour valider que la passerelle a été créée, vous pouvez utiliser l’applet de commande **Get-AzureApplicationGateway** .

    Get-AzureApplicationGateway AppGwTest

>[AZURE.NOTE]  La valeur par défaut pour *InstanceCount* est 2, avec une valeur maximale de 10. La valeur par défaut pour *GatewaySize* est adaptée au support. Vous pouvez choisir entre petites, moyennes et grandes.

 *VirtualIPs* et *NomDNS* apparaissent comme vide, car la passerelle n’a pas encore commencé. Ceux-ci sont créés une fois que la passerelle est en cours d’exécution.

## <a name="configure-an-application-gateway"></a>Configurer une passerelle d’application

Vous pouvez configurer la passerelle d’application à l’aide de XML ou un objet de configuration.

## <a name="configure-an-application-gateway-by-using-xml"></a>Configurer une passerelle d’application à l’aide de XML

Dans l’exemple suivant, vous utilisez un fichier XML pour configurer les paramètres de passerelle toutes les applications et les valider à la ressource de passerelle application.  

### <a name="step-1"></a>Étape 1

Copier le texte suivant dans le bloc-notes.

    <ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
    <FrontendIPConfigurations>
        <FrontendIPConfiguration>
            <Name>fip1</Name>
            <Type>Private</Type>
        </FrontendIPConfiguration>
    </FrontendIPConfigurations>    
    <FrontendPorts>
        <FrontendPort>
            <Name>port1</Name>
            <Port>80</Port>
        </FrontendPort>
    </FrontendPorts>
    <Probes>
        <Probe>
            <Name>Probe01</Name>
            <Protocol>Http</Protocol>
            <Host>contoso.com</Host>
            <Path>/path/custompath.htm</Path>
            <Interval>15</Interval>
            <Timeout>15</Timeout>
            <UnhealthyThreshold>5</UnhealthyThreshold>
        </Probe>
      </Probes>
     <BackendAddressPools>
        <BackendAddressPool>
            <Name>pool1</Name>
            <IPAddresses>
                <IPAddress>1.1.1.1</IPAddress>
                <IPAddress>2.2.2.2</IPAddress>
            </IPAddresses>
        </BackendAddressPool>
    </BackendAddressPools>
    <BackendHttpSettingsList>
        <BackendHttpSettings>
            <Name>setting1</Name>
            <Port>80</Port>
            <Protocol>Http</Protocol>
            <CookieBasedAffinity>Enabled</CookieBasedAffinity>
            <RequestTimeout>120</RequestTimeout>
            <Probe>Probe01</Probe>
        </BackendHttpSettings>
    </BackendHttpSettingsList>
    <HttpListeners>
        <HttpListener>
            <Name>listener1</Name>
            <FrontendIP>fip1</FrontendIP>
        <FrontendPort>port1</FrontendPort>
            <Protocol>Http</Protocol>
        </HttpListener>
    </HttpListeners>
    <HttpLoadBalancingRules>
        <HttpLoadBalancingRule>
            <Name>lbrule1</Name>
            <Type>basic</Type>
            <BackendHttpSettings>setting1</BackendHttpSettings>
            <Listener>listener1</Listener>
            <BackendAddressPool>pool1</BackendAddressPool>
        </HttpLoadBalancingRule>
    </HttpLoadBalancingRules>
    </ApplicationGatewayConfiguration>


Modifiez les valeurs entre les parenthèses pour les éléments de configuration. Enregistrez le fichier avec une extension .xml.

L’exemple suivant montre comment utiliser un fichier de configuration pour configurer la passerelle d’application, pour équilibrer le trafic HTTP sur le port public 80 et envoyer le trafic réseau principale port 80 entre deux adresses IP à l’aide d’une sonde personnalisé.

>[AZURE.IMPORTANT] L’élément de protocole Http ou Https respecte la casse.

Un nouvel élément de configuration \<sonde\> est ajouté à configurer sondes personnalisé.

Les paramètres de configuration sont :

- **Nom** : nom de référence pour sonde personnalisé.
- **Protocole** - protocole utilisé (valeurs possibles sont HTTP ou HTTPS).
- **Hôte** et le **chemin d’accès** - chemin d’URL complète qui est appelé par la passerelle d’application pour déterminer l’état de l’instance. Par exemple, si vous avez un site Web http://contoso.com/, puis la sonde personnalisée peut être configurée pour « http://contoso.com/path/custompath.htm » pour les chèques sonde d’avoir une réponse HTTP réussie.
- **Intervalle** - configure les tests d’intervalle sonde en secondes.
- **Délai d’expiration** : définit le délai d’expiration de sonde pour une vérification de la réponse HTTP.
- **UnhealthyThreshold** - le nombre de réponses HTTP échecs nécessaire pour signaler l’instance principale comme *incorrect*.

Le nom de la sonde référencé dans les <BackendHttpSettings> configuration à affecter le pool principale utilise les paramètres de sonde personnalisé.

## <a name="add-a-custom-probe-configuration-to-an-existing-application-gateway"></a>Ajouter une configuration sonde personnalisé pour une passerelle d’application existant

Modification de la configuration en cours d’une passerelle application requiert trois étapes : obtenir le fichier de configuration XML actuel, modifier pour avoir une sonde personnalisé et configurer la passerelle d’application avec les nouveaux paramètres XML.

### <a name="step-1"></a>Étape 1

Obtenir le fichier XML en utilisant get-AzureApplicationGatewayConfig. Cette action exporte le fichier XML à modifier pour ajouter un paramètre de sonde de configuration.

    Get-AzureApplicationGatewayConfig -Name "<application gateway name>" -Exporttofile "<path to file>"


### <a name="step-2"></a>Étape 2

Ouvrez le fichier XML dans un éditeur de texte. Ajouter un `<probe>` section après `<frontendport>`.

    <Probes>
        <Probe>
            <Name>Probe01</Name>
            <Protocol>Http</Protocol>
            <Host>contoso.com</Host>
            <Path>/path/custompath.htm</Path>
            <Interval>15</Interval>
            <Timeout>15</Timeout>
            <UnhealthyThreshold>5</UnhealthyThreshold>
        </Probe>
    </Probes>

Dans la section backendHttpSettings du code XML, ajoutez le nom de la sonde comme le montre l’exemple suivant :

        <BackendHttpSettings>
            <Name>setting1</Name>
            <Port>80</Port>
            <Protocol>Http</Protocol>
            <CookieBasedAffinity>Enabled</CookieBasedAffinity>
            <RequestTimeout>120</RequestTimeout>
            <Probe>Probe01</Probe>
        </BackendHttpSettings>

Enregistrez le fichier XML.

### <a name="step-3"></a>Étape 3

Mettre à jour la configuration de passerelle d’application avec le nouveau fichier XML à l’aide de **Jeu AzureApplicationGatewayConfig**. Cela met à jour votre passerelle d’application avec la nouvelle configuration.

    Set-AzureApplicationGatewayConfig -Name "<application gateway name>" -Configfile "<path to file>"


## <a name="next-steps"></a>Étapes suivantes

Si vous voulez configurer déchargement couche SSL (Secure Sockets), consultez [configurer une passerelle d’application pour SSL décharger](application-gateway-ssl.md).

Si vous souhaitez configurer une passerelle d’application à utiliser avec un programme d’équilibrage de charge interne, voir [créer une passerelle d’application avec un programme d’équilibrage de charge interne (ILB)](application-gateway-ilb.md).
