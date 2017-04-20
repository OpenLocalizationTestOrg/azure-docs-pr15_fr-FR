<properties
   pageTitle="Introduction à la passerelle d’Application | Microsoft Azure"
   description="Cette page fournit une vue d’ensemble du service passerelle d’Application pour l’équilibrage de charge couche 7, y compris les tailles de passerelle, HTTP charger affinité session équilibrage de charge, en fonction des cookies et décharger SSL."
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

# <a name="application-gateway-overview"></a>Vue d’ensemble de la passerelle application

## <a name="what-is-application-gateway"></a>Quelle est la passerelle d’Application

Microsoft Azure Application passerelle fournit Application remise contrôleur (ADC) en tant que service, offrant diverses fonctionnalités pour votre application d’équilibrage de charge de couche 7. Il permet aux clients d’optimiser la productivité de batterie de serveurs web en décharger arrêt de SSL intensive du processeur à la passerelle d’Application. Il fournit également d’autres fonctionnalités de routage couche 7, y compris la distribution alternées du trafic entrant, des cookies en fonction affinité de session, le routage de chemin d’accès basé URL et la possibilité d’héberger plusieurs sites Web derrière une passerelle d’Application unique. Application passerelle est également un pare-feu d’application web (WAF) qui protège votre application par rapport à la plupart d'entre l’OWASP avoir supérieure 10 courantes web. Passerelle d’application peut être configuré comme passerelle qui fait face à internet, seule passerelle interne ou une combinaison des deux. Application passerelle est entièrement Azure géré, scalable et hautement disponible. Il propose un ensemble complet de diagnostics et de fonctions de journalisation pour faciliter la gestion. Application passerelle fonctionne avec machines virtuelles, cloud services et applications web internes ou externes en vis-à-vis.

Application passerelle est une application dédiée virtuelle pour votre application et se compose de plusieurs instances de travail pour élevées. Lorsque vous créez une passerelle d’application, un point de terminaison (interne ILB IP ou VIP publique) est associé et utilisé pour le trafic réseau pénétration. Cette VIP ou ILB IP est fourni par équilibrage de charge Azure fonctionne au niveau du transport (TCP/UDP) et avoir tout le trafic réseau entrant en cours équilibrer vers les instances de travail passerelle d’Application. La passerelle d’Application puis itinéraires le trafic HTTP/HTTPS en fonction de sa configuration, qu’il s’agisse d’une machine virtuelle, cloud service, interne ou une adresse IP externe. Pour le contrat SLA et tarifs, reportez-vous aux pages de [SLA](https://azure.microsoft.com/support/legal/sla/) et [tarification](https://azure.microsoft.com/pricing/details/application-gateway/) .

## <a name="features"></a>Fonctionnalités

Application passerelle prend actuellement en charge la livraison des applications 7 calque avec les fonctionnalités suivantes :

- **[Pare-feu pour applications web (Preview)](application-gateway-webapplicationfirewall-overview.md)** - le pare-feu d’application web (WAF) dans Azure Application Gateway protège les applications web contre les attaques courantes sur le web comme injection SQL, les attaques de script intersites et détournements de session.
- **Équilibrage de charge HTTP** - passerelle d’Application fournit l’équilibrage de charge alternées. L’équilibrage de charge s’effectue au calque 7 et est utilisé pour le trafic HTTP (S) uniquement.
- **Affinité session basée sur les cookies** - cette fonctionnalité est utile lorsque vous voulez conserver une session utilisateur sur le même principale. En utilisant des cookies de passerelle gérée, la passerelle d’Application peut diriger le trafic suivant à partir d’une session utilisateur vers le même serveur principal pour traitement. Cette fonctionnalité est importante dans les cas où l’état de session est enregistré localement sur le serveur principal pour une session utilisateur.
- **[Décharger le calque SSL (secure Sockets)](application-gateway-ssl-arm.md)** - cette fonctionnalité permet d’accéder la tâche coûteuse de déchiffrer le trafic HTTPS désactiver vos serveurs web. En fin de la connexion SSL au niveau de la passerelle d’Application et transférer la demande au serveur non chiffrée, le serveur web est unburdened par le déchiffrement.  Application passerelle ré-chiffre la réponse avant l’envoi vers le client. Cette fonctionnalité est utile dans les scénarios où se trouve le serveur principal dans le même réseau virtuel sécurisé que la passerelle d’Application dans Azure.
- **[SSL de bout en bout](application-gateway-backend-ssl.md)** - passerelle d’Application prend en charge le chiffrement de bout en bout du trafic. Application passerelle pour cela, en mettant fin à la connexion SSL au niveau de la passerelle d’application. La passerelle puis applique les règles de routage pour le trafic, nouveau chiffre des paquets et transfère le paquet vers le serveur principal approprié basé sur les règles de routage définis. Toutes les réponses à partir du serveur web accède à la même procédure revenir à l’utilisateur final.
- **[Basée sur URL l’acheminement du contenu](application-gateway-url-route-overview.md)** - cette fonctionnalité offre la possibilité d’utiliser différents serveurs principale pour le trafic différent. Le trafic pour un dossier sur le serveur web ou un CDN peut être acheminé vers un autre principale, réduction de charge inutile sur les serveurs principaux qui ne remplissent un contenu spécifique.
- **[Routage de plusieurs sites](application-gateway-multi-site-overview.md)** - Application passerelle permet de vous permet de consolider jusqu'à 20 sites Web sur une passerelle d’application unique.
- **[Prise en charge Websocket](application-gateway-websocket.md)** - une autre fonctionnalité intéressante de la passerelle de l’Application est la prise en charge native Websocket.
- **[Contrôle d’état](application-gateway-probe-overview.md)** - passerelle d’Application fournit santé par défaut d’analyse des ressources de serveur principal et personnalisée teste pour contrôler le scénarios plus spécifiques.

## <a name="benefits"></a>Avantages

Application passerelle est utile pour :

- Applications qui requièrent des demandes de la même session utilisateur/client vers atteigne la même machine virtuelle principale. Exemples de ces applications d’achat panier applications et les serveurs de messagerie web.
- Applications que vous souhaitez libérer de batteries de serveurs web à partir de frais de résiliation SSL.
- Applications, comme un réseau de distribution de contenu, qui requiert plusieurs requêtes HTTP sur la même connexion TCP longue à être routés ou charger équilibrée vers d’autres serveurs de données principale.
- Applications prenant en charge le trafic websocket
- Applications web protection contre les attaques courantes sur le web comme injection SQL, les attaques de script intersites et détournements de session.

Application passerelle équilibrage de charge en tant qu’un service géré Azure permet la mise en service d’équilibrage de charge un calque 7 derrière l’équilibrage de charge logiciel Azure. Le trafic manager peut être utilisé pour terminer le scénario, comme illustré dans l’image suivante. L’emplacement dans lequel le trafic Manager fournit la redirection et disponibilité, équilibrage de charge fournit disponibilité et extensibilité élevées région et passerelle d’application fournit région croisée couche 7 l’équilibrage de charge.

![asdasd](./media/application-gateway-introduction/tm-lb-ag-scenario.png)

[AZURE.INCLUDE [load-balancer-compare-tm-ag-lb-include.md](../../includes/load-balancer-compare-tm-ag-lb-include.md)]

## <a name="gateway-sizes-and-instances"></a>Instances et les tailles de passerelle

Application passerelle n’est actuellement proposé trois tailles : petites, moyennes et grandes. Tailles petite instance sont conçus pour le développement et les scénarios de test.

Il n’y a deux références SKU pour passerelle d’Application : WAF et Standard.

Vous pouvez créer jusqu'à 50 passerelles d’application par abonnement, et chaque application passerelle peut contenir jusqu'à 10 instances chaque. Chaque application passerelle peut comporter 20 écoute http. Pour une liste complète des limites de passerelle application visitez la page [Service limites](../azure-subscription-service-limits.md#application-gateway) .

Le tableau suivant montre un débit performance moyenne pour chaque instance de passerelle d’application :

| Réponse de la page principale | MINUSCULE | Moyenne | Grande taille|
|---|---|---|---|
| 6K | 7.5 Mbps | 13 Mbits/s | 50 Mbps |
|100 KO | 35 Mbps | 100 Mbps| 200 Mbps |

>[AZURE.NOTE] Ces valeurs sont approximative pour un débit de passerelle d’application. Le débit réel dépend de divers détails de l’environnement, telles que la taille de page moyenne, emplacement d’instances principale et de temps de traitement pour répondre à une page. Pour les nombres de performances exacte, vous devez exécuter les tests de votre propre, ces valeurs ne sont fournies pour des conseils de planification de la capacité.

## <a name="health-monitoring"></a>Contrôle d’état

Azure Application passerelle surveille automatiquement l’intégrité des instances principale Basic ou teste état personnalisé. À l’aide d’intégrité sondes, ainsi que des hôtes uniquement exact répondent au trafic. Pour plus d’informations, voir [Présentation de l’analyse d’intégrité passerelle d’Application](application-gateway-probe-overview.md).

## <a name="configuring-and-managing"></a>Configurer et gérer des

Pour son point de terminaison, passerelle d’application peut avoir une adresse IP publique, IP privé ou les deux lorsqu’il est configuré. Application passerelle est configurée à l’intérieur d’un réseau virtuel dans sa propre sous-réseau. Le sous-réseau créé ou utilisé pour la passerelle d’application ne peut pas contenir d’autres types de ressources, seules les ressources qui sont autorisés dans le sous-réseau sont autres passerelles d’application. Pour protéger vos ressources de serveur principal le système principal de serveurs pouvant être contenus dans un autre sous-réseau dans le même réseau virtuel que la passerelle d’application. Cette supplémentaires que ne requises pas pour les applications serveur principal, dans la mesure où la passerelle d’application communiquer avec l’adresse ip, passerelle d’application constitue en mesure de fournir des fonctionnalités de connecteur Active Directory pour les serveurs principaux.

Vous pouvez créer et gérer une passerelle d’application à l’aide des API REST, applets de commande PowerShell, Azure infrastructure du langage commun ou [portail Azure](https://portal.azure.com/).

## <a name="next-steps"></a>Étapes suivantes

Après avoir appris passerelle d’Application, vous pouvez [créer une passerelle d’application](application-gateway-create-gateway-portal.md) ou vous pouvez [créer une passerelle d’application SSL décharger](application-gateway-ssl-arm.md) aux connexions HTTPS l’équilibrage de charge.

Pour apprendre à créer une passerelle d’application à l’aide de basée sur URL l’acheminement du contenu, accédez à [créer une passerelle d’application à l’aide de routage basé sur une URL](application-gateway-create-url-route-arm-ps.md) pour plus d’informations.

