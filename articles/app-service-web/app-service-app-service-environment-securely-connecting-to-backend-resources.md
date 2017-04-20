<properties 
    pageTitle="Connexion sécurisée au serveur principal ressources à partir d’un environnement de Service d’application" 
    description="Découvrez comment connecter de façon sécurisée aux ressources serveur principal à partir d’un environnement de Service d’application." 
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

# <a name="securely-connecting-to-backend-resources-from-an-app-service-environment"></a>Connexion sécurisée au serveur principal ressources à partir d’un environnement de Service d’application #

## <a name="overview"></a>Vue d’ensemble ##
Dans la mesure où un environnement de Service d’application est toujours créé dans **soit** un réseau virtuel Azure le Gestionnaire de ressources, **ou** un déploiement classique de modèle de [réseau virtuel][virtualnetwork], les connexions sortantes à partir d’un environnement de Service d’application à d’autres ressources de serveur principal susceptible de déborder exclusivement le réseau virtuel.  Avec une modification récente apportée dans juin 2016, ASEs peuvent également être déployées dans les réseaux virtuels qui utilisent les plages d’adresses public, ou les espaces d’adressage RFC1918 (c'est-à-dire adresses privées).  

Par exemple, il peut être un serveur SQL Server s’exécutant sur un cluster de machines virtuelles avec le port 1433 verrouillé.  Le point de terminaison peut être ACLd pour autoriser uniquement l’accès à partir d’autres ressources sur le même réseau virtuel.  

Voici un autre exemple, points de terminaison sensibles peuvent s’exécuter en local et être connectés à Azure via un [Site à] [ SiteToSite] ou [Azure ExpressRoute] [ ExpressRoute] connexions.  Par conséquent, seules les ressources dans des réseaux virtuels connectés au Site à ou tunnel ExpressRoute pourront accéder aux points de terminaison locaux.

Pour tous les scénarios suivants, applications en cours d’exécution dans un environnement de Service d’application seront en mesure de connecter de façon sécurisée aux différents serveurs et ressources.  Le trafic sortant à partir des applications en cours d’exécution dans un environnement de Service d’application aux points de terminaison privés sur le même réseau virtuel (ou se connecter au réseau virtuel même), sera flux uniquement sur le réseau virtuel.  N’achemine le trafic sortant aux points de terminaison privés via Internet public.

Un avertissement s’applique au trafic sortant à partir d’un environnement de Service d’application aux points de terminaison au sein d’un réseau virtuel.  Environnements de Service d’application ne peut pas atteindre les points de terminaison de machines virtuelles situés dans le **même** sous-réseau que l’environnement de Service d’application.  Cela normalement à ne pas un problème dans la mesure où environnements de Service d’application sont déployés dans un sous-réseau réservé pour une utilisation exclusive par uniquement l’environnement de Service d’application.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

## <a name="outbound-connectivity-and-dns-requirements"></a>Connectivité sortante et configuration requise de DNS ##
Pour un environnement de Service d’application fonctionne correctement, elle nécessite un accès sortant à différents points de terminaison. Une liste complète des points de terminaison externes utilisées par ASE se trouve dans la section « Obligatoire la connectivité réseau » de l’article [Configuration du réseau pour ExpressRoute](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity) .

Application Service environnements nécessitent une infrastructure DNS valide configurée pour le réseau virtuel.  Si pour une raison quelconque, que la configuration de DNS est modifiée après la création d’un environnement de Service d’application, les développeurs peuvent forcer un environnement de Service d’application pour reprendre la nouvelle configuration de DNS.  Déclencher un redémarrage environnement propagée à l’aide de l’icône « Redémarrer » situé en haut de la carte de gestion d’environnement de Service d’application dans le portail va entraîner l’environnement reprendre la nouvelle configuration de DNS.

Il est également recommandé que tous les serveurs DNS personnalisés sur le vnet le programme d’installation avance avant de créer un environnement de Service d’application.  Si la configuration de DNS d’un réseau virtuel est modifiée pendant la création d’un environnement de Service d’application, qui entraîne l’échec de processus de création d’une application Service environnement.  De la même manière, si un serveur DNS personnalisé existe à l’autre extrémité d’une passerelle VPN, et que le serveur DNS est inaccessible ou indisponible, le processus de création d’une application Service environnement échouera également.

## <a name="connecting-to-a-sql-server"></a>Connexion à un serveur SQL Server
Une configuration SQL Server commune comporte un point de terminaison écoute sur le port 1433 :

![Point de terminaison SQL Server][SqlServerEndpoint]

Il existe deux approches pour limiter le trafic sur ce point de terminaison :


- [Listes de contrôle d’accès du réseau] [ NetworkAccessControlLists] (réseau utilisateurs)

- [Groupes de sécurité réseau][NetworkSecurityGroups]


## <a name="restricting-access-with-a-network-acl"></a>Limiter l’accès à un réseau et

Port 1433 peut être sécurisé à l’aide d’une liste de contrôle d’accès réseau.  L’exemple ci-dessous d’autorisation client adresses à partir d’origine à l’intérieur d’un réseau virtuel et bloque l’accès à tous les autres clients.

![Exemple de liste de contrôle de l’accès réseau][NetworkAccessControlListExample]

Toutes les applications en cours d’exécution dans un environnement de Service d’application dans le même réseau virtuel que le serveur SQL Server seront en mesure de vous connecter à l’instance SQL Server à l’aide de l’adresse IP **VNet interne** pour la machine virtuelle SQL Server.  

Chaîne de connexion de l’exemple ci-dessous fait référence à SQL Server à l’aide de son adresse IP privée.

    Server=tcp:10.0.1.6;Database=MyDatabase;User ID=MyUser;Password=PasswordHere;provider=System.Data.SqlClient

Bien que la machine virtuelle dispose d’un public point de terminaison, tentatives de connexion à l’aide de l’adresse IP seront rejetés en raison du réseau et. 

## <a name="restricting-access-with-a-network-security-group"></a>Restriction d’accès avec un groupe de sécurité réseau
Une autre méthode pour sécuriser l’accès est avec un groupe de sécurité réseau.  Groupes de sécurité réseau peuvent être appliquées aux différentes machines virtuelles, ou à un sous-réseau contenant les machines virtuelles.

Tout d’abord un groupe de sécurité réseau doit être créé :

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"

Limiter l’accès aux uniquement le trafic interne de VNet est très simple avec un groupe de sécurité réseau.  Les règles par défaut dans un groupe de sécurité réseau autorisent uniquement l’accès à partir d’autres clients du réseau dans le même réseau virtuel.

Verrouillage par conséquent accès à SQL Server est aussi simple que l’application d’un groupe de sécurité réseau avec ses règles par défaut aux deux machines virtuelles SQL Server ou le sous-réseau contenant les machines virtuelles en cours d’exécution.

L’exemple ci-dessous s’applique à un groupe de sécurité réseau au sous-réseau contenant :

    Get-AzureNetworkSecurityGroup -Name "testNSGExample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-1'
    
Le résultat final est un ensemble de règles de sécurité qui bloquent l’accès externe, tout en autorisant un accès VNet interne :

![Règles de sécurité par défaut réseau][DefaultNetworkSecurityRules]


## <a name="getting-started"></a>Prise en main
Tous les articles et comment-du pour les environnements de Service d’application sont disponibles dans le [fichier Lisez-moi pour les environnements de Service d’Application](../app-service/app-service-app-service-environments-readme.md).

Pour commencer avec les environnements de Service d’application, consultez [Introduction à l’environnement de Service d’application][IntroToAppServiceEnvironment]

Pour plus d’informations sur le contrôle de trafic entrant pour votre environnement de Service d’application, voir [contrôler le trafic entrant vers un environnement de Service d’application][ControlInboundASE]

Pour plus d’informations sur la plateforme Azure Application Service, voir [Service d’application Azure][AzureAppService].

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]
 

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[ControlInboundTraffic]:  http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[SiteToSite]: https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[NetworkAccessControlLists]: https://azure.microsoft.com/documentation/articles/virtual-networks-acl/
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[IntroToAppServiceEnvironment]:  http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/ 
[ControlInboundASE]:  http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/ 

<!-- IMAGES -->
[SqlServerEndpoint]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/SqlServerEndpoint01.png
[NetworkAccessControlListExample]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/NetworkAcl01.png
[DefaultNetworkSecurityRules]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/DefaultNetworkSecurityRules01.png 
