<properties
    pageTitle="Configuration de connexions VPN gestion des API Azure"
    description="Découvrez comment configurer une connexion VPN dans Azure API de gestion des services et d’accès web traversé."
    services="api-management"
    documentationCenter=""
    authors="antonba"
    manager="erikre"
    editor=""/>

<tags
    ms.service="api-management"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="antonba"/>

# <a name="how-to-setup-vpn-connections-in-azure-api-management"></a>Configuration de connexions VPN gestion des API Azure

Prise en charge des VPN de la direction de l’API vous permet de vous connecter votre passerelle de gestion des API à un réseau virtuel Azure (standard). Cela permet aux clients de gestion de l’API pour vous connecter en toute sécurité à leurs principale des services web qui sont locaux ou inaccessibles à l’internet public.

>[AZURE.NOTE] Gestion de l’API Azure fonctionne avec VNETs classiques. Pour plus d’informations sur la création d’un VNET classique, voir [créer un réseau virtuel (classique) à l’aide du portail Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). Pour plus d’informations sur la connexion classiques VNETs à VNETS processeur, voir [connexion classique VNets à nouveau VNets](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

## <a name="enable-vpn"> </a>Connexions VPN activer

>Connectivité VPN est disponible uniquement dans les couches **Premium** et **développeur** . Pour basculer vers celui-ci, ouvrez votre service de gestion de l’API dans le [Portail classique Azure][] , puis ouvrez l’onglet **échelle** . Sous la section **Général** , sélectionnez le niveau Premium et cliquez sur Enregistrer.

Pour activer la connectivité VPN, ouvrez votre service de gestion de l’API dans le [Portail classique Azure][] et accédez à l’onglet **configurer** . 

Sous la section VPN, **connexion VPN** du commutateur sur **activé**.

![Configurer l’onglet d’instance de gestion de l’API][api-management-setup-vpn-configure]

Vous verrez maintenant une liste de toutes les régions où votre service de gestion de l’API est mis en service.

Sélectionnez un VPN et sous-réseau pour chaque région. La liste des VPN est remplie basée sur les réseaux virtuels disponibles dans votre abonnement Azure qui sont configurés dans la zone de que configuration.

![Sélectionnez VPN][api-management-setup-vpn-select]

Cliquez sur **Enregistrer** dans la partie inférieure de l’écran. Vous ne serez pas en mesure d’effectuer d’autres opérations sur le service de gestion de l’API à partir du portail classique Azure alors que celui-ci est à jour. La passerelle de services restent disponible et runtime appelle ne doit pas être affectées.

Notez que l’adresse de la passerelle modifiera chaque fois VPN est activé ou désactivé.

## <a name="connect-vpn"> </a>Se connecter à un service web derrière VPN

Une fois que votre service de gestion de l’API est connecté au réseau VPN, accès aux services web au sein du réseau virtuel est pas différente de l’accès aux services public. Tapez simplement l’adresse locale ou le nom d’hôte (si un serveur DNS a été configuré pour le réseau virtuel Azure) de votre service web dans le champ **URL du service Web** lorsque vous créez une nouvelle API ou de modifier une existante.

![Ajouter des API de VPN][api-management-setup-vpn-add-api]

## <a name="required-ports-for-api-management-vpn-support"></a>Ports requis pour la prise en charge API gestion VPN

Lorsqu’une instance de service de gestion de l’API est hébergée dans un VNET, les ports dans le tableau suivant sont utilisés. Si ces ports sont bloqués, le service peut ne pas fonctionner correctement. Possédant un ou plusieurs de ces ports bloqués est le problème de configuration incorrecte plus courantes lors de l’utilisation de la gestion des API avec un VNET.

| Ports                      | Direction        | Protocole de transport | Objectif                                                          | Source / Destination              |
|------------------------------|------------------|--------------------|------------------------------------------------------------------|-----------------------------------|
| 80, 443                      | Entrant          | TCP                | Communication avec les clients à la gestion des API                           | INTERNET / VIRTUAL_NETWORK        |
| 80,443                       | Sortant         | TCP                | Dépendance de gestion des API sur le stockage Azure et Bus des services Azure | VIRTUAL_NETWORK / INTERNET        |
| 1433                         | Sortant         | TCP                | Dépendances de gestion de l’API sur SQL                               | VIRTUAL_NETWORK / INTERNET        |
| 9350, 9351, 9352, 9353, 9354 | Sortant         | TCP                | Dépendances de gestion de l’API sur Bus des services                       | VIRTUAL_NETWORK / INTERNET        |
| 5671                         | Sortant         | AMQP               | Dépendance de gestion de l’API pour journal événement stratégie concentrateur            | VIRTUAL_NETWORK / INTERNET        |
| 6381, 6382, 6383             | Entrant/sortant | UDP                | Dépendances de gestion de l’API Redis cache                       | VIRTUAL_NETWORK / VIRTUAL_NETWORK |
| 445                          | Sortant         | TCP                | Dépendance de gestion des API de partage de fichiers Azure pour GIT            | VIRTUAL_NETWORK / INTERNET        |

## <a name="custom-dns"> </a>Le programme d’installation personnalisée DNS server

Gestion des API dépend de plusieurs services Azure. Lorsqu’une instance de service de gestion de l’API est hébergée dans un VNET où un serveur DNS personnalisé est utilisé, elle doit être en mesure de résoudre les noms d’hôtes de ces services Azure. Suivez [ces](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server) instructions sur la configuration de DNS personnalisée.  

## <a name="related-content"> </a>Contenu associé


* [Créer un réseau virtuel avec une connexion VPN site à l’aide du portail classique Azure][]
* [Comment utiliser l’inspecteur de l’API de suivi des appels gestion des API Azure][]

[api-management-setup-vpn-configure]: ./media/api-management-howto-setup-vpn/api-management-setup-vpn-configure.png
[api-management-setup-vpn-select]: ./media/api-management-howto-setup-vpn/api-management-setup-vpn-select.png
[api-management-setup-vpn-add-api]: ./media/api-management-howto-setup-vpn/api-management-setup-vpn-add-api.png

[Enable VPN connections]: #enable-vpn
[Connect to a web service behind VPN]: #connect-vpn
[Related content]: #related-content

[Portail classique Azure]: https://manage.windowsazure.com/

[Créer un réseau virtuel avec une connexion VPN de site à l’aide du portail classique Azure]: ../vpn-gateway/vpn-gateway-site-to-site-create.md
[Comment utiliser l’inspecteur de l’API de suivi des appels gestion des API Azure]: api-management-howto-api-inspector.md
