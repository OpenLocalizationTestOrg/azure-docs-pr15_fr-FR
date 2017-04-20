<properties 
    pageTitle="Environnement de Service d’application | Microsoft Azure" 
    description="Qu’est un environnement de Service d’application Azure ? Introduction à l’environnement de Service d’application." 
    keywords="environnement de service d’application Azure, réseau virtuel, la mise en réseau de la banque d’informations sécurisé"
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

# <a name="app-service-environment-documentation"></a>Documentation environnement du Service d’application

Un environnement de Service d’application est un [Premium] [ PremiumTier] option offre de Service d’application Azure qui fournit un environnement entièrement isolé et dédié pour l’exécution en toute sécurité des applications de Service d’application Azure à l’échelle haute, y compris les [Applications Web]du service[WebApps], [Applications Mobile][MobileApps]et les [Applications de l’API][APIApps].  

Environnements de Service d’application sont idéales pour les charges de travail application nécessitant :

- Échelle de très haute
- Isolement et accès réseau sécurisé

Les clients peuvent créer plusieurs environnements de Service d’application dans une région Azure unique, ainsi que dans plusieurs régions Azure.  Environnements de Service d’application sont donc parfaits pour l’évolution horizontale niveaux application état sans une charges RPS élevées.

Environnements de Service d’application sont isolées dans des applications d’un seul client uniquement en cours d’exécution et sont toujours déployés dans un réseau virtuel.  Clients ont un contrôle précis sur les deux à l’aide de [groupes de sécurité réseau]le trafic réseau entrant et sortant application[NetworkSecurityGroups].  Applications peuvent également établir des connexions sécurisées haut débit sur les réseaux virtuels aux ressources d’entreprise en local.

Applications doivent fréquemment accéder à des ressources d’entreprise tels que des bases de données internes et des services web.  Applications en cours d’exécution sur les environnements de Service d’application peuvent accéder aux ressources accessibles via le [Site à] [ SiteToSite] VPN et [Azure ExpressRoute] [ ExpressRoute] connexions.

* [Qu’est un environnement de Service d’application ?](../app-service-web/app-service-app-service-environment-intro.md)
* [Création d’un environnement de Service d’application](../app-service-web/app-service-web-how-to-create-an-app-service-environment.md)
* [Création d’applications dans un environnement de Service d’application](../app-service-web/app-service-web-how-to-create-a-web-app-in-an-ase.md)
* [Création et l’utilisation d’un programme d’équilibrage de charge interne aux environnements de Service d’application](../app-service-web/app-service-environment-with-internal-load-balancer.md)
* [Configuration d’un environnement de Service d’application](../app-service-web/app-service-web-configure-an-app-service-environment.md) 
* [Mise à l’échelle des applications dans un environnement de Service d’application](../app-service-web/app-service-web-scale-a-web-app-in-an-app-service-environment.md)
* [Sécurité de réseau et l’Architecture](../app-service-web/app-service-app-service-environment-network-architecture-overview.md)

## <a name="how-tos"></a>Comment de

[AZURE.INCLUDE [app-service-blueprint-app-service-environment](../../includes/app-service-blueprint-app-service-environment.md)]


## <a name="videos"></a>Vidéos
[AZURE.VIDEO azurecon-2015-deploying-highly-scalable-and-secure-web-and-mobile-apps]

[AZURE.VIDEO microsoft-ignite-2015-running-enterprise-web-and-mobile-apps-on-azure-app-service]


<!-- LINKS -->
[PremiumTier]: http://azure.microsoft.com/pricing/details/app-service/
[WebApps]: http://azure.microsoft.com/documentation/articles/app-service-web-overview/
[MobileApps]: http://azure.microsoft.com/documentation/articles/app-service-mobile-value-prop-preview/
[APIApps]: http://azure.microsoft.com/documentation/articles/app-service-api-apps-why-best-platform/
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[SiteToSite]: https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
