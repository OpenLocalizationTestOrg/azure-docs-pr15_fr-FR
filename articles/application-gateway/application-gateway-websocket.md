<properties
   pageTitle="Prise en charge de la passerelle WebSocket applications | Microsoft Azure"
   description="Cette page fournit une vue d’ensemble de la prise en charge de l’Application passerelle WebSocket."
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
   ms.date="09/16/2016"
   ms.author="amsriva"/>

# <a name="application-gateway-websocket-support"></a>Prise en charge de la passerelle WebSocket applications

Application passerelle prise en charge native pour WebSocket au sein de toutes tailles de passerelle. Il n’existe aucun paramètre configurable par l’utilisateur permettent d’activer ou désactiver la prise en charge WebSocket. Vous pouvez continuer à utiliser un HTTPListener standard port sur 80/443 pour recevoir le trafic WebSocket. Le trafic WebSocket est alors dirigé vers le serveur principal activé WebSocket à l’aide de la liste principaux appropriés comme indiqué dans les règles de passerelle d’application. Protocole WebSocket normalisé dans [RFC6455](https://tools.ietf.org/html/rfc6455) permet une communication bidirectionnelle entre client et serveur sur une connexion TCP longue. Cette fonctionnalité permet une communication plus interactive entre serveur web et le client, qui peut être bidirectionnelle sans qu’il soit nécessaire pour l’interrogation comme requis dans choisiront HTTP.  WebSocket avoir faible surcharge Contrairement à HTTP et pouvez réutiliser la même connexion TCP pour plusieurs demande/réponses, ce qui entraîne une utilisation plus efficace des ressources. Protocoles WebSocket sont conçus pour fonctionner sur traditionnels ports HTTP 80 et 443.

Le serveur principal doit répondre aux sondes passerelle d’application, qui sont décrites dans la section [vue d’ensemble de la sonde d’intégrité](application-gateway-probe-overview.md) . Application passerelle santé sondes sont HTTP/HTTPS uniquement, cela signifie que chaque serveur principal doit répondre aux détecte HTTP passerelle d’application acheminer le trafic WebSocket sur le serveur.

## <a name="listener-configuration-element"></a>Élément de configuration de récepteur

HTTPListener existante peut servir à prendre en charge WebSocket. Voici un extrait de HttpListeners élément à partir d’un exemple de fichier de modèle. Vous devriez écoute HTTP et HTTPS pour prendre en charge WebSocket et sécuriser le trafic WebSocket. De la même façon que vous pouvez utiliser le [portail](application-gateway-create-gateway-portal.md) ou [PowerShell](application-gateway-create-gateway-arm.md) pour créer une passerelle d’application avec les récepteurs sur le port 80/443 pour prendre en charge le trafic WebSocket.


    "httpListeners": [
                {
                    "name": "appGatewayHttpsListener",
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
                    }
                },
                {
                    "name": "appGatewayHttpListener",
                    "properties": {
                        "FrontendIPConfiguration": {
                            "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendIPConfigurations/appGatewayFrontendIP'"
                        },
                        "FrontendPort": {
                            "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendPorts/appGatewayFrontendPort80'"
                        },
                        "Protocol": "Http",
                    }
                }
            ],

## <a name="backendaddresspool-backendhttpsetting-and-routing-rule-configuration"></a>Configuration de la règle BackendAddressPool, BackendHttpSetting et positionnement

BackendAddressPool doit être utilisé pour définir un pool principal avec les serveurs WebSocket activé. BackendHttpSetting doit être définie avec le serveur principal le port 80/443. Propriétés des affinité basée sur les cookies et requestTimeouts ne sont pas pertinentes pour le trafic WebSocket. Aucune modification n’est requise dans une règle de routage. Règle de routage 'Base' doit continuer à être utilisée pour lier le pool d’adresses principal correspondant que l’auditeur approprié. 

    "requestRoutingRules": [{
        "name": "<ruleName1>",
        "properties": {
            "RuleType": "Basic",
            "httpListener": {
                "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/httpListeners/appGatewayHttpsListener')]"
            },
            "backendAddressPool": {
                "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/ContosoServerPool')]"
            },
            "backendHttpSettings": {
                "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
            }
        }

    }, {
        "name": "<ruleName2>",
        "properties": {
            "RuleType": "Basic",
            "httpListener": {
                "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/httpListeners/appGatewayHttpListener')]"
            },
            "backendAddressPool": {
                "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/ContosoServerPool')]"
            },
            "backendHttpSettings": {
                "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
            }

        }
    }]

## <a name="websocket-enabled-backend"></a>Version serveur WebSocket activé

Votre serveur principal peut comporter qu’un serveur web HTTP/HTTPS sous configuré le port (généralement 80/443) pour WebSocket pour l’utiliser. Cette condition est car protocole WebSocket nécessite la liaison initiale à être HTTP mise à niveau du protocole WebSocket comme un champ d’en-tête.

    GET /chat HTTP/1.1
    Host: server.example.com
    Upgrade: websocket
    Connection: Upgrade
    Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
    Origin: http://example.com
    Sec-WebSocket-Protocol: chat, superchat
    Sec-WebSocket-Version: 13

Une autre raison est que sonde application passerelle principal d’intégrité prend en charge les protocoles HTTP/HTTPS uniquement. Si le serveur principal ne répond pas à HTTP/HTTPS sondes, il se déconnecter de pool principal et aucune demande y compris les requêtes WebSocket seraient à prendre, susceptibles d’atteindre cette version de serveur.

## <a name="next-steps"></a>Étapes suivantes

Après avoir appris WebSocket prise en charge, accédez à [créer une passerelle d’application](application-gateway-create-gateway.md) pour commencer à utiliser une application web WebSocket activé.
