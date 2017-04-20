<properties
   pageTitle="Hébergement de plusieurs sites sur Application passerelle | Microsoft Azure"
   description="Cette page fournit une vue d’ensemble de la prise en charge de plusieurs sites passerelle d’Application."
   documentationCenter="na"
   services="application-gateway"
   authors="amsriva"
   manager="rossort"
   editor="amsriva"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/25/2016"
   ms.author="amsriva"/>

# <a name="application-gateway-multiple-site-hosting"></a>Application passerelle plusieurs hébergement de sites

Hébergement de plusieurs sites vous permet de configurer plusieurs application web sur la même instance de passerelle d’application. Cette fonctionnalité permet de configurer une topologie plus efficace pour vos déploiements en ajoutant des sites Web jusqu'à 20 passerelle une application. Chacun des sites Web peut être dirigée vers son propre pool principal. Dans l’exemple suivant, passerelle d’application sert le trafic de contoso.com et fabrikam.com à partir de deux pools serveur principal appelés ContosoServerPool et FabrikamServerPool.

![imageURLroute](./media/application-gateway-multi-site-overview/multisite.png)

Demandes de http://contoso.com sont routés vers ContosoServerPool et http://fabrikam.com sont routés vers FabrikamServerPool.

De même deux sous-domaines du même domaine parent peuvent être hébergés sur le même déploiement de passerelle d’application. Exemples d’utilisation de sous-domaines peuvent inclure http://blog.contoso.com et http://app.contoso.com hébergée sur un déploiement de passerelle d’application unique.

## <a name="host-headers-and-server-name-indication-sni"></a>Indication de nom de serveur (SNI) et les en-têtes d’hôte

Il existe trois mécanismes pour l’activation de plusieurs hébergement de sites sur la même infrastructure.

1. Héberger plusieurs applications web chaque sur une adresse IP unique.
2. Utilisez le nom d’hôte pour héberger plusieurs applications web sur la même adresse IP.
3. Utiliser des ports différents pour héberger plusieurs applications web sur la même adresse IP.

Actuellement une passerelle d’application obtient une seule adresse IP publique sur lequel il écoute pour le trafic. Par conséquent prenant en charge plusieurs applications, chacune avec sa propre adresse IP, est actuellement pas pris en charge. Passerelle d’application prend en charge l’hébergement de plusieurs applications chaque écoute sur différents ports mais ce scénario de verser les applications pour accepter le trafic sur les ports non standard et n’est pas souvent une configuration souhaitée. Application passerelle repose sur les en-têtes d’hôte HTTP 1.1 pour héberger plusieurs sites Web dans la même adresse IP public et le port. Les sites hébergés sur passerelle d’application peuvent également de prise en charge SSL déchargement avec l’extension TLS Indication de nom de serveur (SNI). Ce scénario signifie que le client navigateur et serveur principal batterie de serveurs web doit prendre en charge HTTP/1.1 et TLS extension, telle que définie dans RFC 6066.

## <a name="listener-configuration-element"></a>Élément de configuration de récepteur

Élément de configuration HTTPListener existant est amélioré pour prendre en charge hôte nom nom indication les éléments et serveur, qui est utilisé par application passerelle à acheminer le trafic vers le pool principaux appropriés. L’exemple suivant est l’extrait de HttpListeners élément à partir du fichier de modèle.

    "httpListeners": [
                {
                    "name": "appGatewayHttpsListener1",
                    "properties": {
                        "FrontendIPConfiguration": {
                            "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendIPConfigurations/DefaultFrontendPublicIP"
                        },
                        "FrontendPort": {
                            "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendPorts/appGatewayFrontendPort443'"
                        },
                        "Protocol": "Https",
                        "SslCertificate": {
                            "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/sslCertificates/appGatewaySslCert1'"
                        },
                        "HostName": "contoso.com",
                        "RequireServerNameIndication": "true"
                    }
                },
                {
                    "name": "appGatewayHttpListener2",
                    "properties": {
                        "FrontendIPConfiguration": {
                            "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendIPConfigurations/appGatewayFrontendIP'"
                        },
                        "FrontendPort": {
                            "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendPorts/appGatewayFrontendPort80'"
                        },
                        "Protocol": "Http",
                        "HostName": "fabrikam.com",
                        "RequireServerNameIndication": "false"
                    }
                }
            ],




Vous pouvez visiter [modèle Gestionnaire de ressources à l’aide de plusieurs hébergement de sites](https://github.com/Azure/azure-quickstart-templates/blob/master/201-application-gateway-multihosting) pour un déploiement basé sur un modèle de bout en bout.

## <a name="routing-rule"></a>Règle de routage

Aucune modification n’est requise dans la règle de routage. La règle de routage 'Basic' continuent à être choisi pour lier le récepteur site approprié pour le pool d’adresses principal correspondante.

    "requestRoutingRules": [
    {
        "name": "<ruleName1>",
        "properties": {
            "RuleType": "Basic",
            "httpListener": {
                "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/httpListeners/appGatewayHttpsListener1')]"
            },
            "backendAddressPool": {
                "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/ContosoServerPool')]"
            },
            "backendHttpSettings": {
                "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
            }
        }

    },
    {
        "name": "<ruleName2>",
        "properties": {
            "RuleType": "Basic",
            "httpListener": {
                "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/httpListeners/appGatewayHttpListener2')]"
            },
            "backendAddressPool": {
                "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/FabrikamServerPool')]"
            },
            "backendHttpSettings": {
                "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
            }
        }

    }
    ]

## <a name="next-steps"></a>Étapes suivantes

Après avoir appris plusieurs hébergement de sites, accédez à [créer une passerelle d’application à l’aide de plusieurs hébergement de sites](application-gateway-create-multisite-azureresourcemanager-powershell.md) pour créer une passerelle d’application avec prise en charge plus d’une application web.
