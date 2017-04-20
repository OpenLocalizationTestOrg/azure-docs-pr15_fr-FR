<properties 
    pageTitle="Introduction à l’environnement de Service d’application" 
    description="En savoir plus sur la fonctionnalité d’environnement de Service d’application qui fournit des unités de l’échelle dédié, sécurisée rejoint VNet pour toutes les applications en cours d’exécution." 
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

# <a name="introduction-to-app-service-environment"></a>Introduction à l’environnement de Service d’application

## <a name="overview"></a>Vue d’ensemble ##
Un environnement de Service d’application est un [Premium] [ PremiumTier] option offre de Service d’application Azure qui fournit un environnement entièrement isolé et dédié pour l’exécution en toute sécurité des applications de Service d’application Azure à l’échelle haute, y compris les [Applications Web]du service[WebApps], [Applications Mobile][MobileApps]et les [Applications de l’API][APIApps].  

Environnements de Service d’application sont idéales pour les charges de travail application nécessitant :

- Échelle très haute
- Isolement et accès réseau sécurisé

Les clients peuvent créer plusieurs environnements de Service d’application dans une région Azure unique, ainsi que dans plusieurs régions Azure.  Environnements de Service d’application sont donc parfaits pour l’évolution horizontale niveaux application état sans prise en charge les charges de travail RPS haute.

Environnements de Service d’application sont isolées dans des applications d’un seul client uniquement en cours d’exécution et sont toujours déployés dans un réseau virtuel.  Clients contrôlez permissions à la fois le trafic réseau entrant et sortant application, et applications pouvant établir une connexion sécurisée haut débit sur réseaux virtuels aux ressources d’entreprise en local.

Tous les articles et comment-aux informations d’environnements de Service d’application sont disponibles dans le [fichier Lisez-moi pour les environnements de Service d’Application](../app-service/app-service-app-service-environments-readme.md).

Découvrez comment les environnements de Service d’application activer haute échelle et sécuriser l’accès réseau, voir la [AzureCon approfondie] [ AzureConDeepDive] sur les environnements de Service d’application !

Pour un approfondi sur l’évolution horizontale à l’aide de plusieurs environnements de Service d’application voir l’article comment configurer un [encombrement application distribué geo][GeodistributedAppFootprint].

Pour voir comment l’architecture de sécurité affiché dans l’approfondie AzureCon a été configurée, voir l’article sur l’implémentation d’un [couches d’architecture de sécurité](app-service-app-service-environment-layered-security.md) aux environnements de Service d’application.

Applications en cours d’exécution sur les environnements de Service d’application peuvent avoir leur accès contrôlé en amont appareils tels que les pare-feux application web (WAF).  L’article sur la [configuration d’un WAF pour les environnements de Service d’application](app-service-app-service-environment-web-application-firewall.md) couvre ce scénario. 

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

## <a name="dedicated-compute-resources"></a>Ressources cluster dédié ##
Toutes les ressources de cluster dans un environnement de Service d’application sont réservés en mode exclusif à un seul abonnement, et un environnement de Service d’application peut être configuré avec les ressources cluster jusqu'à cinquante (50) pour une utilisation exclusive par une seule application.

Un environnement de Service d’application est composé d’une liste de ressources cluster frontal, ainsi que les listes de ressources partagées un à trois collaborateur cluster. 

Le pool frontal contient des ressources de cluster responsable de l’arrêt de SSL en tant que l’équilibrage de charge bien automatique des demandes d’applications dans un environnement de Service d’application. 

Chaque pool de travail contient des ressources cluster allouées à [l’Application Service Plans][AppServicePlan], qui à son tour contiennent une ou plusieurs applications de Service d’application Azure.  Dans la mesure où il peut être jusqu'à trois pools travail différents dans un environnement de Service d’application, vous avez la possibilité de choisir les ressources de cluster différents pour chaque pool de travail.  

Par exemple, cela vous permet de créer un pool de travail avec les ressources cluster moins puissants pour application Service Plans destiné aux applications de développement ou de test.  Un pool de travail deuxième (ou même troisième) peut utiliser plus puissante cluster ressources destinées à une application Service Plans de production applications en cours d’exécution.

Pour plus d’informations sur la quantité de ressources de cluster disponibles pour les pools frontale et de travail, voir [comment configurer un environnement de Service d’application][HowToConfigureanAppServiceEnvironment].  

Pour plus d’informations sur la disposition calculer les tailles de ressources prises en charge dans un environnement de Service d’application, consultez les [Tarifs de Service application] [ AppServicePricing] page et passez en revue les options disponibles pour les environnements de Service d’application dans la prime tarifs niveau.

## <a name="virtual-network-support"></a>Prise en charge réseau virtuel ##
Un environnement de Service d’application peut être créé dans **soit** un réseau virtuel Azure le Gestionnaire de ressources, **ou** un déploiement classique de modèle de réseau virtuel ([plus d’informations sur les réseaux virtuels][MoreInfoOnVirtualNetworks]).  Dans la mesure où un environnement de Service d’application existe toujours dans un réseau virtuel, et plus précisément au sein d’un sous réseau de réseau virtuel, vous pouvez tirer parti des fonctionnalités de sécurité des réseaux virtuels pour contrôler les communications réseau entrantes et sortantes.  

Un environnement de Service d’application peut être soit ouvert avec une adresse IP publique, ou interne facing avec uniquement une adresse Azure interne charge Équilibrage (ILB) sur Internet.

Vous pouvez utiliser des [groupes de sécurité réseau] [ NetworkSecurityGroups] de limiter les communications réseau entrant au sous-réseau où se trouve un environnement de Service d’application.  Ainsi, vous permet d’exécuter des applications derrière les périphériques situés en amont et services tels que web application pare-feu et les fournisseurs de SaaS réseau.

Applications doivent également fréquemment accéder aux ressources d’entreprise tels que des bases de données internes et des services web.  Une approche commune consiste à rendre ces points de terminaison disponible uniquement pour le trafic réseau interne s’étalant dans un réseau virtuel Azure.  Une fois qu’un environnement de Service d’application est joint au même réseau virtuel en tant que les services internes, applications en cours d’exécution dans l’environnement puissent y accéder, y compris les points de terminaison accessibles via le [Site à] [ SiteToSite] et [Azure ExpressRoute] [ ExpressRoute] connexions.

Pour plus d’informations sur le fonctionnement des environnements de Service d’application avec réseaux virtuels et en local, consultez les articles suivants sur [l’Architecture réseau][NetworkArchitectureOverview], [Contrôler le trafic entrant][ControllingInboundTraffic]et [Sécuriser une connexion pour les serveurs principaux][SecurelyConnectingToBackends]. 

## <a name="getting-started"></a>Prise en main

Pour commencer avec les environnements de Service d’application, voir [Comment en créer une application Service environnement][HowToCreateAnAppServiceEnvironment]

Tous les articles et comment-du pour les environnements de Service d’application sont disponibles dans le [fichier Lisez-moi pour les environnements de Service d’Application](../app-service/app-service-app-service-environments-readme.md).

Pour plus d’informations sur la plateforme Azure Application Service, voir [Service d’application Azure][AzureAppService].

Pour une vue d’ensemble de l’architecture réseau environnement de Service d’application, consultez la [Présentation de l’Architecture réseau] [ NetworkArchitectureOverview] article.

Pour plus d’informations sur l’utilisation d’un environnement de Service d’application avec ExpressRoute, consultez l’article suivant sur [Express acheminer et environnements de Service d’application][NetworkConfigDetailsForExpressRoute].

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[PremiumTier]: http://azure.microsoft.com/pricing/details/app-service/
[MoreInfoOnVirtualNetworks]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePlan]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[HowToCreateAnAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[WebApps]: http://azure.microsoft.com/documentation/articles/app-service-web-overview/
[MobileApps]: http://azure.microsoft.com/documentation/articles/app-service-mobile-value-prop-preview/
[APIApps]: http://azure.microsoft.com/documentation/articles/app-service-api-apps-why-best-platform/
[LogicApps]: http://azure.microsoft.com/documentation/articles/app-service-logic-what-are-logic-apps/
[AzureConDeepDive]:  https://azure.microsoft.com/documentation/videos/azurecon-2015-deploying-highly-scalable-and-secure-web-and-mobile-apps/
[GeodistributedAppFootprint]:  https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-geo-distributed-scale/
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[SiteToSite]: https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[HowToConfigureanAppServiceEnvironment]:  http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment/
[ControllingInboundTraffic]:  https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[SecurelyConnectingToBackends]:  https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-securely-connecting-to-backend-resources/
[NetworkArchitectureOverview]:  https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-architecture-overview/
[NetworkConfigDetailsForExpressRoute]:  https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-configuration-expressroute/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/ 

<!-- IMAGES -->

 
