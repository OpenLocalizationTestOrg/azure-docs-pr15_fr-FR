<properties 
    pageTitle="Présentation de l’Architecture réseau des environnements de Service d’application" 
    description="Présentation de l’architecture de topologie de réseau ofApp environnements de Service." 
    services="app-service" 
    documentationCenter="" 
    authors="stefsch" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/04/2016" 
    ms.author="stefsch"/>   

# <a name="network-architecture-overview-of-app-service-environments"></a>Présentation de l’Architecture réseau des environnements de Service d’application

## <a name="introduction"></a>Introduction ##
Environnements de Service d’application sont toujours créés dans un sous réseau d’un [réseau virtuel] [ virtualnetwork] -applications en cours d’exécution dans un environnement de Service d’application puissent communiquer avec des points de terminaison privés situés au sein de la même topologie de réseau virtuel.  Dans la mesure où les clients peuvent verrouiller les parties de leur infrastructure réseau virtuel, il est important de comprendre les types de flux de communication réseau qui se produisent avec un environnement de Service d’application.

## <a name="general-network-flow"></a>Flux de réseau général ##
 
Lorsqu’un environnement de Service d’application (ASE) utilise une adresse IP virtuelle (VIP) publique pour les applications, tout le trafic entrant arrive sur ce VIP publique.  Cela inclut le trafic HTTP et HTTPS pour les applications, ainsi que d’autres le trafic pour FTP, la fonctionnalité de débogage distante et opérations de gestion Azure.  Pour obtenir la liste complète des ports spécifiques (obligatoires et facultatifs) qui sont disponibles sur le VIP publique, voir l’article sur le [contrôle de trafic entrant] [ controllinginboundtraffic] dans un environnement de Service d’application. 

Environnements de Service d’application prennent également en charge les applications en cours d’exécution qui sont liées uniquement à une adresse interne réseau virtuel, également appelée adresse ILB (équilibrage de charge interne).  Sur un ILB activé trafic ASE, HTTP et HTTPS pour applications, ainsi que les appels débogage à distance, arrivent sur l’adresse ILB.  Pour les configurations ILB ASE plus courantes, le trafic FTP/FTPS arriveront également sur l’adresse ILB.  Toutefois, opérations de gestion Azure passera toujours aux ports 454/455 sur l’adresse VIP publique d’un ILB activée ASE.

L’illustration ci-dessous présente un aperçu des différents flux réseau entrantes et sortantes pour un environnement de Service d’application où les applications sont liées à une adresse IP virtuelle publique :

![Flux de réseau général][GeneralNetworkFlows]

Un environnement de Service d’application puissent communiquer avec une variété de points de terminaison de client privé.  Par exemple, les applications en cours d’exécution dans l’environnement de Service d’application peuvent se connecter aux serveurs de base de données s’exécutant sur machines virtuelles de IaaS dans la même topologie de réseau virtuel.

>[AZURE.IMPORTANT] Prenons le réseau de tâches, « Autres ressources calculer » sont déployés dans un sous-réseau différent de celui de l’environnement de Service d’application. Déploiement des ressources dans le même sous-réseau avec la ASE bloquera connectivité entre ASE et ces ressources (à l’exception de routage interne-ASE spécifique). Déployer à en choisir un à la place (dans la même VNET). L’environnement de Service d’application pourront puis se connecter. Aucune configuration supplémentaire n’est nécessaire.

Environnements de Service d’application également communiquent avec DB Sql et le stockage Azure ressources nécessaires pour gérer et exploitation d’un environnement de Service d’application.  Parmi les ressources de stockage et Sql un environnement de Service d’application communique avec se trouvent dans la même région en tant que l’environnement de Service d’application, tandis que d’autres personnes sont situés dans des régions Azure à distance.  Par conséquent, connectivité sortante à Internet est toujours nécessaire pour un environnement de Service d’application fonctionner correctement. 

Dans la mesure où un environnement de Service d’application est déployé dans un sous-réseau, les groupes de sécurité réseau peuvent être utilisés pour contrôler le trafic entrant au sous-réseau.  Pour connaître la procédure contrôler le trafic entrant vers un environnement de Service d’application, consultez l' [article]suivant[controllinginboundtraffic].

Pour connaître la procédure pour autoriser la connectivité Internet sortante à partir d’un environnement de Service d’application, voir l’article suivant sur l’utilisation des [Itinéraire Express][ExpressRoute].  La même approche décrite dans l’article s’applique lorsque vous travaillez avec une connectivité à un Site et à l’aide de forcé tunnel.

## <a name="outbound-network-addresses"></a>Adresses réseau sortant ##
Lorsqu’un environnement de Service d’application effectue les appels sortants, une adresse IP est toujours associée les appels sortants.  L’adresse IP spécifique utilisé varie selon que le point de terminaison appelée est situé dans la topologie de réseau virtuel, ou en dehors de la topologie de réseau virtuel.

Si le point de terminaison appelée est **en dehors** de la topologie de réseau virtuel, l’adresse sortant (ou l’adresse NAT sortant) utilisé est l’adresse VIP publique de l’environnement de Service d’application.  Cette adresse peut être trouvée dans l’interface utilisateur du portail pour l’environnement de Service d’application dans la carte de propriétés.
 
![Adresse IP sortantes][OutboundIPAddress]

Cette adresse peut également être déterminée pour ASEs qui ont seulement un VIP publique en créant une application dans l’environnement de Service d’application, puis en effectuant un *nslookup* sur l’adresse de l’application. L’adresse IP qui en résulte est l’adresse VIP publique, ainsi qu’adresse NAT sortant de l’environnement de Service de l’application.

Si le point de terminaison appelée se trouve **à l’intérieur** de la topologie de réseau virtuel, l’adresse de l’application appel sortant sera l’adresse IP interne de la ressource cluster individuels l’application en cours d’exécution.  Cependant, il n’existe pas un mappage permanent des adresses IP internes de réseau virtuel aux applications.  Applications pouvant naviguer dans plusieurs ressources cluster différent et le pool de cluster disponible ressources dans un environnement de Service d’application peuvent être modifiées en raison de l’évolution des opérations.

Toutefois, dans la mesure où un environnement de Service d’application est toujours situé dans un sous-réseau, vous êtes certain que l’adresse IP interne d’une ressource cluster une application en cours d’exécution sera toujours se trouvent dans la plage CIDR du sous-réseau.  Par conséquent, lorsque permissions utilisateurs ou groupes de sécurité réseau sont utilisés pour sécuriser l’accès aux autres points de terminaison au sein du réseau virtuel, la plage de sous-réseau contenant l’environnement de Service d’application doit être autorisés à accéder.

Le diagramme suivant montre ces concepts plus en détail :

![Adresses réseau sortant][OutboundNetworkAddresses]

Dans le diagramme ci-dessus :

- Étant donné que l’adresse VIP publique de l’environnement de Service d’application est 192.23.1.2, qui est l’adresse IP sortant utilisé lorsque vous faites appel aux points de terminaison « Internet ».
- La plage CIDR du conteneur sous-réseau pour l’environnement de Service d’application est 10.0.1.0/26.  Autres points de terminaison dans la même infrastructure réseau virtuel verront les appels à partir des applications comme provenant de n’importe où dans cette plage d’adresses.

## <a name="calls-between-app-service-environments"></a>Appels entre environnements de Service d’application ##
Un scénario plus complexe peut se produire si vous déployez plusieurs environnements de Service d’application dans le même réseau virtuel et effectuer des appels sortants à partir d’un environnement de Service d’application à une autre application Service environnement.  Ces types de croisées environnement de Service d’application appels sont également considérés comme appels « Internet ».

Le diagramme suivant montre un exemple d’une architecture en couches avec les applications dans un environnement de Service d’application (par exemple, les applications web « Porte ») applications l’appel sur un deuxième environnement de Service d’application (par exemple, interne principale API applications ne pas destinées à être accessibles à partir d’Internet). 

![Appels entre environnements de Service d’application][CallsBetweenAppServiceEnvironments] 

Dans l’exemple ci-dessus l’environnement de Service d’application « ASE un » possède une adresse IP sortante de 192.23.1.2.  Si une application en cours d’exécution sur cet environnement de Service d’application effectue un appel sortant pour une application en cours d’exécution sur un deuxième environnement de Service d’application (« ASE deux ») situé dans le même réseau virtuel, l’appel sortant sera considéré comme un appel « Internet ».  Par conséquent le trafic réseau arrivant sur la seconde application Service environnement s’affichent comme provenant de 192.23.1.2 (c'est-à-dire pas la sous-réseau plage d’adresses du premier environnement de Service d’application).

Bien que les appels entre différents environnements de Service d’application sont considérées comme appels « Internet », lorsque les deux environnements de Service d’application se trouvent dans la même région Azure le trafic réseau restera sur le réseau Azure régionaux et ne passera pas physiquement via Internet public.  Par conséquent, vous pouvez utiliser un groupe de sécurité réseau sur le sous-réseau du deuxième environnement de Service d’application pour autoriser uniquement les appels entrants de la première application Service environnement (dont l’adresse IP sortante est 192.23.1.2), afin de garantir une communication sécurisée entre les environnements de Service d’application.

## <a name="additional-links-and-information"></a>Informations et liens supplémentaires ##
Tous les articles et comment-du pour les environnements de Service d’application sont disponibles dans le [fichier Lisez-moi pour les environnements de Service d’Application](../app-service/app-service-app-service-environments-readme.md).

Plus d’informations sur les ports utilisés par les environnements de Service d’application d’entrée et à l’aide de groupes de sécurité réseau pour contrôler le trafic entrant est disponible [ici][controllinginboundtraffic].

Informations sur l’utilisation d’utilisateur définis itinéraires octroyer l’accès Internet sortant aux environnements de Service d’application n’est disponible dans cet [article][ExpressRoute]. 


<!-- LINKS -->
[virtualnetwork]: http://azure.microsoft.com/services/virtual-network/
[controllinginboundtraffic]:  http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[ExpressRoute]:  http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-configuration-expressroute/

<!-- IMAGES -->
[GeneralNetworkFlows]: ./media/app-service-app-service-environment-network-architecture-overview/NetworkOverview-1.png
[OutboundIPAddress]: ./media/app-service-app-service-environment-network-architecture-overview/OutboundIPAddress-1.png
[OutboundNetworkAddresses]: ./media/app-service-app-service-environment-network-architecture-overview/OutboundNetworkAddresses-1.png
[CallsBetweenAppServiceEnvironments]: ./media/app-service-app-service-environment-network-architecture-overview/CallsBetweenEnvironments-1.png

