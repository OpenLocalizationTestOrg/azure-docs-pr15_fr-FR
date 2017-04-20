<properties
   pageTitle="Résoudre les erreurs d’Application passerelle passerelle incorrecte (502) | Microsoft Azure"
   description="Découvrez comment résoudre les erreurs d’Application passerelle 502"
   services="application-gateway"
   documentationCenter="na"
   authors="amitsriva"
   manager="rossort"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/02/2016"
   ms.author="amitsriva" />

# <a name="troubleshooting-bad-gateway-errors-in-application-gateway"></a>Résolution des erreurs de passerelle incorrecte dans passerelle d’Application

## <a name="overview"></a>Vue d’ensemble

Après avoir configuré une passerelle d’Application Azure, un des erreurs que les utilisateurs peuvent rencontrer est « Erreur du serveur : 502 - serveur Web a reçu une réponse non valide en tant qu’un passerelle ou un serveur proxy ». Cela peut se produire en raison des raisons suivantes :

- Pool de principale de passerelle d’Application Azure n’est pas configurée ou est vide.
- Aucune des instances du jeu d’échelle machine virtuelle machines virtuelles est correct.
- Machines virtuelles ou des instances de machine virtuelle échelle définie principale ne répond à la sonde de santé par défaut.
- Configuration non valide ou incorrecte d’état personnalisé teste.
- Demander le délai d’expiration ou problèmes de connectivité avec les demandes d’utilisateur.

## <a name="empty-backendaddresspool"></a>BackendAddressPool vide

### <a name="cause"></a>Cause

Si la passerelle d’Application a machines virtuelles ni machine virtuelle échelle définie configuré dans le pool d’adresses principale, il ne peut pas router toute demande de client et génère une erreur de passerelle incorrecte.

### <a name="solution"></a>Solution

Vérifiez que le pool d’adresses principal n’est pas vide. Vous pouvez faire via PowerShell, infrastructure du langage commun ou portail.

    Get-AzureRmApplicationGateway -Name "SampleGateway" -ResourceGroupName "ExampleResourceGroup"

Le résultat de l’applet de commande précédent doit contenir pool d’adresses de principale non vide. Voici un exemple où deux pools sont retournés qui sont configurés avec des adresses IP ou le nom de domaine complet pour le serveur principal machines virtuelles. L’état de mise en service de la BackendAddressPool doit être « réussi. »
    
    BackendAddressPoolsText: 
            [{
                "BackendAddresses": [{
                    "ipAddress": "10.0.0.10",
                    "ipAddress": "10.0.0.11"
                }],
                "BackendIpConfigurations": [],
                "ProvisioningState": "Succeeded",
                "Name": "Pool01",
                "Etag": "W/\"00000000-0000-0000-0000-000000000000\"",
                "Id": "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name>/backendAddressPools/pool01"
            }, {
                "BackendAddresses": [{
                    "Fqdn": "xyx.cloudapp.net",
                    "Fqdn": "abc.cloudapp.net"
                }],
                "BackendIpConfigurations": [],
                "ProvisioningState": "Succeeded",
                "Name": "Pool02",
                "Etag": "W/\"00000000-0000-0000-0000-000000000000\"",
                "Id": "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name>/backendAddressPools/pool02"
            }]


## <a name="unhealthy-instances-in-backendaddresspool"></a>Instances incorrectes dans BackendAddressPool

### <a name="cause"></a>Cause

Si toutes les instances de BackendAddressPool sont incorrects, Application passerelle n’a pas de n’importe quel principale à la demande d’utilisateur itinéraire. Cela pourrait également être le cas lorsque principale instances fonctionnent correctement, mais n’ont pas l’application requise déployée.

### <a name="solution"></a>Solution

Assurez-vous que les instances fonctionnent correctement et l’application n’est pas configurée correctement. Vérifier si les instances principale sont en mesure de répondre à une commande ping à partir d’un autre ordinateur virtuel dans la même VNet. Si configuré avec un point de terminaison public, vérifiez qu’une demande de navigateur à l’application web est accessible.

## <a name="problems-with-default-health-probe"></a>Problèmes avec une sonde d’intégrité par défaut

### <a name="cause"></a>Cause

502 erreurs peuvent également être indicateurs fréquents que la sonde de santé par défaut n’est pas en mesure d’atteindre machines virtuelles principale. Lors de sa mise en service une instance de passerelle d’Application, il configure automatiquement une sonde d’intégrité par défaut pour chaque BackendAddressPool à l’aide des propriétés de la BackendHttpSetting. Aucune entrée utilisateur n’est requise pour définir cette sonde. Plus précisément, lorsqu’une règle d’équilibrage de charge est configurée, une association est rendue entre un BackendHttpSetting et BackendAddressPool. Une sonde par défaut est configurée pour chacun de ces associations et passerelle d’Application établit une connexion de vérifier régulièrement le fonctionnement à chaque occurrence dans le BackendAddressPool sur le port spécifié dans l’élément BackendHttpSetting. Tableau suivant répertorie les valeurs associées à la sonde de santé par défaut.


|Propriété sonde | Valeur | Description|
|---|---|---|
| URL sonde| http://127.0.0.1/ | Chemin d’URL |
| Intervalle | 30 | Intervalle sonde en secondes |
| Délai d’attente  | 30 | Délai de sonde en secondes |
| Seuil incorrecte | 3 | Sonde nombre de tentatives. Le serveur principal est marquée comme vers le bas après que le nombre d’échecs consécutifs sonde atteint le seuil mauvais état. |

### <a name="solution"></a>Solution

- Vous assurer qu’un site par défaut est configuré et qu’il est à l’écoute sur 127.0.0.1.
- Si BackendHttpSetting indique un port différent de 80, le site par défaut doit être configuré pour écouter à ce port.
- L’appel vers http://127.0.0.1:port doit renvoyer un code de résultat HTTP de 200. Cette valeur doit être retournée dans le délai de 30 secondes.
- Assurez-vous que le port configuré est ouvert et qu’il n’y a pas de règles de pare-feu ou des groupes de sécurité de réseau Azure qui bloquent le trafic entrant ou sortant sur le port configuré.
- Si Azure machines virtuelles classiques ou un Service Cloud est utilisée avec nom de domaine complet ou l’adresse IP publique, vérifiez que [point de terminaison](../virtual-machines/virtual-machines-windows-classic-setup-endpoints.md) correspondant est ouvert.
- Si la machine virtuelle est configurée via le Gestionnaire de ressources Azure et se trouve en dehors de la VNet où passerelle d’Application est déployé, [Groupe de sécurité réseau](../virtual-network/virtual-networks-nsg.md) doit être configuré pour autoriser l’accès sur le port souhaité.


## <a name="problems-with-custom-health-probe"></a>Problèmes avec sonde état personnalisé

### <a name="cause"></a>Cause

État personnalisé sondes permettent une flexibilité supplémentaire pour le comportement de recherche par défaut. Lorsque vous utilisez sondes personnalisé, les utilisateurs peuvent configurer l’intervalle sonde, l’URL, chemin d’accès pour tester et des réponses sans succès combien à accepter avant de marquer l’instance principale pool comme étant non fonctionnelle. Les propriétés supplémentaires suivantes sont ajoutées.

|Propriété sonde| Description|
|---|---|
| Nom | Nom de la sonde. Ce nom est utilisé pour faire référence à la sonde dans les paramètres principale HTTP. |
| Protocole | Protocole utilisé pour envoyer la sonde. La sonde utilise le protocole défini dans les paramètres HTTP principale |
| Hôte |  Nom d’hôte pour envoyer la sonde. S’applique uniquement lorsque plusieurs sites est configuré sur Application passerelle. Ceci est différent de nom d’hôte machine virtuelle.  |
| Chemin d’accès | Chemin d’accès relatif de la sonde. Le chemin d’accès valide commence à partir de « / ». La sonde est envoyée à \<protocole\>://\<hôte\>:\<port\>\<chemin d’accès\> |
| Intervalle | Sonde intervalle en secondes. Il s’agit de l’intervalle de temps entre deux tests consécutifs.|
| Délai d’attente | Sonde du délai d’attente en secondes. La sonde est marquée comme ayant échoué si une réponse valide n’est pas reçue pendant cette période d’expiration. |
| Seuil incorrecte | Sonde nombre de tentatives. Le serveur principal est marquée comme vers le bas après que le nombre d’échecs consécutifs sonde atteint le seuil mauvais état. |


### <a name="solution"></a>Solution

Valider que la sonde de santé personnalisé est correctement configuré comme étant la table précédente. Outre les étapes de dépannage précédents, vérifiez également les points suivants :

- Vérifiez que la sonde est correctement spécifiée par le [guide](application-gateway-create-probe-ps.md).
- Si l’Application passerelle est configuré pour un site unique, par défaut l’hôte de nom doit être spécifié comme '127.0.0.1', à moins que dans le cas contraire configuré dans sonde personnalisé.
- Assurez-vous qu’un appel à http://\<hôte\>:\<port\>\<chemin d’accès\> retourne un code de résultat HTTP de 200.
- Assurez-vous que l’intervalle, délai d’expiration et UnhealtyThreshold sont inclus dans la plage acceptable.

## <a name="request-time-out"></a>Demande de délai d’expiration

### <a name="cause"></a>Cause

Lors de la réception d’une demande de l’utilisateur, passerelle d’Application applique les règles configurées à la demande et dirige vers une instance du pool principale. Il attend un intervalle de temps d’une réponse de l’instance principale configurable. Par défaut, cet intervalle est de **30 secondes**. Si la passerelle d’Application ne reçoit pas de réponse à partir d’application principale dans cet intervalle, demande utilisateur voyez une erreur 502.

### <a name="solution"></a>Solution

Passerelle d’application permet aux utilisateurs de configurer ce paramètre via BackendHttpSetting, qui peut être appliqué puis à différents pools. Différents pools principale peuvent avoir différente BackendHttpSetting et demande donc différente délai d’expiration configuré.

    New-AzureRmApplicationGatewayBackendHttpSettings -Name 'Setting01' -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 60

## <a name="next-steps"></a>Étapes suivantes

Si les étapes précédentes ne résolvent pas le problème, ouvrez une [prise en charge des tickets](https://azure.microsoft.com/support/options/).
