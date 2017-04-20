<properties
    pageTitle="Comparaison de Service d’application, Machines virtuelles, tissu de Service et les Services en nuage Azure | Microsoft Azure"
    description="Découvrez comment choisir entre Azure Application Service, Machines virtuelles, tissu de Service et les Services en nuage pour héberger les applications web."
    services="app-service\web, virtual-machines, cloud-services"
    documentationCenter=""
    authors="tdykstra"
    manager="wpickett"
    editor="jimbe"/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/07/2016"
    ms.author="tdykstra"/>

# <a name="azure-app-service-virtual-machines-service-fabric-and-cloud-services-comparison"></a>Comparaison de Service d’application, Machines virtuelles, tissu de Service et les Services en nuage Azure

## <a name="overview"></a>Vue d’ensemble

Azure offre plusieurs façons pour héberger les sites web : [Azure Application Service][], [Machines virtuelles][], [Tissu de Service][]et [Les Services en nuage][]. Cet article vous aide à comprendre les options et faire le bon choix pour votre application web.

Azure Application Service est le meilleur choix pour la plupart des applications web. Déploiement et la gestion sont intégrées à la plate-forme, sites peuvent évoluer rapidement pour gérer les charges de trafic élevé et augmenter la disponibilité du Gestionnaire d’équilibrage de charge et le trafic de charge intégrés. Vous pouvez déplacer facilement des sites existants à Azure Application Service avec un [outil de migration en ligne](https://www.migratetoazure.net/), utiliser une application open source à partir de la galerie des applications Web ou créer un site à l’aide de framework et outils de votre choix. La fonctionnalité [WebJobs][] facilite l’ajouter travail en arrière-plan de traitement à votre application Service web app.

TISSU de service est un bon choix si vous créez une nouvelle application ou réécriture une application existante pour utiliser une architecture microservice. Applications qui s’exécutent sur un pool de machines partagé, peuvent démarrer petites et agrandir à grande échelle avec des centaines ou des milliers de machines selon vos besoins. Services avec état permettent de manière cohérente et fiable stocker l’état de l’application et Service TISSU gère automatiquement partition du service, mise à l’échelle et la disponibilité pour vous.  Service tissu prend également en charge WebAPI avec Interface Web ouverte pour .NET (OWIN) et Core ASP.NET.  Par rapport au Service d’application, Service TISSU fournit également plus contrôler, ou un accès direct à l’infrastructure sous-jacente. Vous pouvez à distance sur vos serveurs ou configurez des tâches de démarrage de serveur. Les Services en nuage est similaire pour tissu de Service dans le degré de contrôle facilité d’utilisation, mais il est désormais un service hérité et tissu de Service est recommandée pour le développement de nouveaux.

Si vous avez une application existante qui requièrent des modifications substantielles à s’exécuter dans le Service d’application ou Service tissu, vous pouvez choisir Machines virtuelles afin de simplifier la migration vers le cloud. Toutefois, correctement configuration, la sécurisation et la mise à jour machines virtuelles nécessite beaucoup plus de temps et de ressources informatiques par rapport aux services d’application Azure et tissu de Service. Si vous envisagez de Machines virtuelles Azure, veillez à que prendre en considération l’effort de maintenance continue requis pour corriger, mettre à jour et gérer votre environnement machine virtuelle. Machines virtuelles Azure est Infrastructure-as-a-Service (IaaS), alors que l’application Service et tissu de Service sont plateforme-as-a-Service (Paas). 

## <a name="features"></a>Comparaison des fonctionnalités

Le tableau suivant compare les fonctionnalités du Service d’application, les Services en nuage, Machines virtuelles et tissu de Service pour vous aider à effectuer le meilleur choix. Pour obtenir des informations récentes sur le contrat SLA pour chaque option, voir [Contrats de niveau de Service Azure](/support/legal/sla/).

Fonctionnalité|Service d’application (applications web)|Services de cloud (rôles web)|Machines virtuelles|TISSU de service|Notes
---|---|---|---|---|---
Déploiement quasi instantanée|X|||X|Déployer une application ou une mise à jour de l’application sur un Service Cloud ou en créant une machine virtuelle, prend quelques minutes au moins ; déploiement d’une application pour une application web prend secondes.
Évoluer vers machines plus grandes sans redéploiement|X|||X|
Instances de serveur Web partagent du contenu et configuration, ce qui signifie que vous n’êtes pas obligé redéployez ou reconfigurer mise à l’échelle.|X|||X|
Plusieurs environnements de déploiement (production et mise en attente)|X|X||X|TISSU de service vous permet d’avoir plusieurs environnements pour vos applications ou à déployer différentes versions de votre application côte à côte.
Gestion automatique des mises à jour du système d’exploitation|X|X|||Mises à jour du système d’exploitation automatiques sont planifiées pour libérer un tissu de Service future.
Changement de plateforme transparente (facilement passer 32 bits et 64 bits)|X|X|||
Déployer le code avec GIT, FTP|X||X||
Déployer le code avec déploiement Web|X||X||Les Services en nuage prend en charge l’utilisation de déploiement Web pour déployer des mises à jour sur des instances de rôle individuel. Toutefois, vous ne pouvez pas l’utiliser pour le déploiement initial d’un rôle, et si vous utilisez Web déployer une mise à jour que vous avez à déployer séparément chaque instance d’un rôle. Plusieurs instances sont nécessaires pour prétendre pour le Service Cloud SLA pour les environnements de production.
Prise en charge WebMatrix|X||X||
Accès aux services, tels que Bus des services, le stockage de base de données SQL|X|X|X|X|
Hôte web ou sur couche de services web d’une architecture à plusieurs niveaux|X|X|X|X|
Niveau de l’hôte au milieu d’une architecture à plusieurs niveaux|X|X|X|X|Application Service web applications peuvent héberger facilement un niveau intermédiaire API REST, et la fonctionnalité [WebJobs](http://go.microsoft.com/fwlink/?linkid=390226) peut héberger traitement des tâches en arrière-plan. Vous pouvez exécuter WebJobs dans un site Web dédié à atteindre indépendantes extensibilité élevées pour le niveau. La fonctionnalité [d’applications API](../app-service-api/app-service-api-apps-why-best-platform.md) aperçu fournit davantage de fonctionnalités pour les autres services d’hébergement.
Prise en charge MySQL en tant que service intégrée|X|X|X||Les Services en nuage peuvent intégrer MySQL en tant que service offres de ClearDB, mais pas dans le cadre du flux de travail portail Azure.
Prise en charge de ASP.NET, classique ASP, Node.js, PHP, Python|X|X|X|X|Service tissu prend en charge la création d’un site web frontaux à l’aide de [ASP.NET 5](../service-fabric/service-fabric-add-a-web-frontend.md) ou vous pouvez déployer n’importe quel type d’application (Node.js, Java, etc.) en tant qu' [invité exécutable](../service-fabric/service-fabric-deploy-existing-app.md).
Évolution horizontale à plusieurs instances sans redéploiement|X|X|X|X|Machines virtuelles peut évoluer à plusieurs instances, mais les services s’exécutant sur ceux-ci doivent être écrits pour gérer cette horizontale. Vous devez configurer un équilibrage de charge pour acheminer les requêtes sur les ordinateurs et créer un groupe affinité pour empêcher le redémarrage de sonnerie de toutes les instances en raison des échecs de maintenance ou du matériel.
Prise en charge de SSL|X|X|X|X|Pour application Service web apps, SSL pour les noms de domaine personnalisé est uniquement prise en charge pour le mode Basic et Standard. Pour plus d’informations sur l’utilisation de SSL avec les applications web, voir [configurer un certificat SSL pour un site Web Azure](../app-service-web/web-sites-configure-ssl-certificate.md).
Intégration de Visual Studio|X|X|X|X|
Le débogage distant|X|X|X||
Déployer le code avec TFS|X|X|X|X|
Isolement réseau avec le [Réseau virtuel Azure](/services/virtual-network/)|X|X|X|X|Voir aussi [l’intégration des réseaux virtuel Azure sites Web](/blog/2014/09/15/azure-websites-virtual-network-integration/)
Prise en charge pour le [Gestionnaire de trafic Azure](/services/traffic-manager/)|X|X|X|X|
Point de terminaison intégrée de surveillance|X|X|X||
Accès Bureau à distance aux serveurs||X|X|X|
Installer les MSI personnalisé||X|X|X|TISSU de service vous permet d’héberger n’importe quel fichier exécutable en tant qu' [invité exécutable](../service-fabric/service-fabric-deploy-existing-app.md) ou vous pouvez installer une application sur les ordinateurs virtuels.
Possibilité de définir/exécuter des tâches de démarrage||X|X|X|
Événements ETW peuvent écouter||X|X|X|

## <a name="scenarios"></a>Scénarios et recommandations

Voici quelques scénarios courants application avec recommandations en ce qui concerne le Azure hébergement option web peut-être être plus appropriée pour chacun.

- [J’ai besoin d’un site web frontal avec la version serveur de base de données et du traitement d’arrière-plan pour exécuter des applications d’entreprise intégrées à sur éléments de site.](#onprem)
- [J’ai besoin d’un moyen fiable héberger mon site Web d’entreprise qui s’adapte bien et offre globale atteint.](#corp)
- [J’ai une application IIS 6 s’exécutant sur Windows Server 2003.](#iis6)
- [Je suis propriétaire de petite entreprise, et j’ai besoin d’un bon moyen pour héberger mon site, mais avec une croissance future à l’esprit.](#smallbusiness)
- [Je suis un site Web ou concepteur graphique, et je veux concevoir et créer des sites web de mes clients.](#designer)
- [Je suis migration mon application à plusieurs niveaux avec un site web frontaux dans le Cloud.](#multitier)
- [Mon application dépend de Windows hautement personnalisés ou environnements Linux et je souhaite déplacer vers le cloud.](#custom)
- [Mon site utilise logiciels open source et héberger dans Azure.](#oss)
- [J’ai une application métier qui doit se connecter au réseau d’entreprise.](#lob)
- [Je veux héberger un API REST ou un service web pour les clients mobiles.](#mobile)


### <a id="onprem"></a>J’ai besoin d’un site web frontal avec la version serveur de base de données et du traitement d’arrière-plan pour exécuter des applications d’entreprise intégrées à sur éléments de site.

Azure Application Service est une solution idéale pour les applications métier complexe. Il vous permet de développer des applications qui adapte automatiquement sur une plateforme équilibrée charge, sont sécurisées avec Active Directory et se connectent à vos ressources locales. Il facilite la gestion de ces applications faciles via un portail mondial et API et vous permet de pour bien comprendre comment les clients sont à l’aise avec les outils d’un aperçu de l’application. La fonctionnalité [Webjobs][] vous permet d’exécuter le processus d’arrière-plan et des tâches dans le cadre de votre couche web, lors de la connectivité hybride et fonctionnalités VNET faites-en facile de se connecter aux ressources locales. Azure Application Service fournit SLA de trois 9 pour les applications web et vous permet de :

* Exécutez vos applications fiable sur une plateforme en nuage réparation automatique, correction automatique.
* Ajuster automatiquement sur un réseau global des centres de données.
* Sauvegarder et restaurer récupération d’urgence.
* Soit conforme ISO, SOC2 et PCI.
* Intégrer avec Active Directory

### <a id="corp"></a>J’ai besoin d’un moyen fiable héberger mon site Web d’entreprise qui s’adapte bien et offre globale atteint.

Azure Application Service est une solution idéale pour l’hébergement de sites Web d’entreprise. Il permet aux applications web à l’échelle rapidement et facilement pour répondre à la demande sur un réseau global de centres de données. Il propose accessibles local, tolérance de panne et la gestion du trafic intelligent. Sur une plateforme qui fournit des outils de gestion de niveau international, ce qui vous permet de mieux au niveau de sites et le trafic du site rapidement et facilement. Azure Application Service fournit SLA de trois 9 pour les applications web et vous permet de :

* Exécuter votre site Web fiable sur une plateforme de cloud réparation automatique, correction automatique.
* Mettre à l’échelle automatiquement sur un réseau global de centres de données.
* Sauvegarder et restaurer récupération d’urgence.
* Gérer les journaux et le trafic avec des outils intégrés.
* Soit conforme ISO, SOC2 et PCI.
* Intégrer avec Active Directory

### <a id="iis6"></a>J’ai une application IIS 6 s’exécutant sur Windows Server 2003.

Azure Application Service facilite l’afin d’éviter les coûts d’infrastructure associés à la migration anciennes applications IIS 6. Microsoft a créé des [Outils de migration facile à utiliser et les instructions de migration détaillées](https://www.movemetowebsites.net/) qui vous permettent de vérifier la compatibilité et d’identifier les modifications qui doivent être apportées. Intégration à Visual Studio, TFS et outils CMS courants facilite la déployer des applications IIS 6 directement dans le cloud. Une fois déployé, le portail Azur fournit des outils de gestion robuste qui vous permettent de mettre à l’échelle vers le bas pour gérer les coûts et jusqu'à se réunir demander si nécessaire. Avec l’outil de migration, vous pouvez :

* Rapidement et facilement migrer votre application web Windows Server 2003 héritée dans le cloud.
* Opter pour quitter votre joints SQL de base de données locales pour créer une application hybride.
* Déplacement automatique de votre base de données SQL ainsi que votre application héritée.

### <a id="smallbusiness"></a>Je suis propriétaire de petite entreprise, et j’ai besoin d’un bon moyen pour héberger mon site, mais avec une croissance future à l’esprit.

Azure Application Service est la solution idéale pour ce scénario, car vous pouvez l’utiliser gratuitement, puis ajouter davantage de fonctionnalités lorsque vous en avez besoin. Chaque application web gratuit est fourni avec un domaine fourni par Azure (*your_company*. azurewebsites.net), et la plateforme inclut des outils intégrés de déploiement et la gestion ainsi une galerie d’applications qui facilitent la prise en main. Il existe de nombreuses autres services et les options d’échelle qui permettent au site d’évoluer avec la demande accrue de l’utilisateur. Avec le Service d’application Azure, vous pouvez :

- Commencez par la couche gratuite et puis évoluer selon vos besoins.
- Utiliser la galerie des applications pour créer rapidement des applications web les plus courants, par exemple WordPress.
- Ajouter des fonctionnalités et services Azure supplémentaires à votre application selon vos besoins.
- Sécurisez votre application web avec HTTPS.

### <a id="designer"></a>Je suis un site Web ou concepteur graphique, et je veux concevoir et créer des sites Web pour mes clients

Pour les développeurs web et les concepteurs, Azure Application Service s’intègre facilement avec une variété de structures et outils prend en charge déploiement Git et FTP et offre une intégration étroite avec les outils et des services tels que Visual Studio et base de données SQL. Avec le Service d’application, vous pouvez :

- Utiliser les outils de ligne de commande pour les [tâches automatisées][scripting].
- Utiliser des langages courants tels que [.net][dotnet], [PHP][], [Node.js][nodejs]et [Python][].
- Sélectionnez trois différents niveaux de mise à l’échelle pour suivre l’évolution capacités très haute.
- Intégrer avec d’autres services tels que de [Base de données SQL]Azure[sqldatabase], [Service Bus] [ servicebus] et [stockage][]ou soulevés à partir du [Magasin Azure][azurestore], tels que MySQL et MongoDB.
- Intégration aux outils tels que Visual Studio Git, WebMatrix, WebDeploy, TFS et FTP.

### <a id="multitier"></a>Je suis migration mon application à plusieurs niveaux avec un site web frontaux dans le Cloud

Si vous exécutez une application à plusieurs niveaux, par exemple un serveur web qui se connecte à une base de données Azure Application Service est une bonne option qui offre une intégration étroite avec la base de données SQL Azure. Et vous pouvez utiliser la fonctionnalité WebJobs en exécutant les principaux processus.

Choisissez tissu de Service pour un ou plusieurs de vos niveaux si vous avez besoin plus de contrôle sur l’environnement de serveur, telles que la possibilité à distance dans votre serveur ou configurer des tâches de démarrage de serveur.

Sélectionnez Machines virtuelles pour un ou plusieurs de vos niveaux si vous souhaitez utiliser votre propre image de l’ordinateur ou exécuter le logiciel serveur ou les services que vous ne pouvez pas configurer sur tissu de Service.

### <a id="custom"></a>Mon application dépend de Windows hautement personnalisés ou environnements Linux et je souhaite déplacer vers le cloud.

Si votre application nécessite l’installation complexe ou la configuration des logiciels et le système d’exploitation, Machines virtuelles est probablement la meilleure solution. Machines virtuelles, vous pouvez :

- Utiliser la galerie de Machine virtuelle pour commencer avec un système d’exploitation, tels que Windows ou Linux et puis le personnaliser en fonction de vos besoins d’application.
- Créer et télécharger une image personnalisée d’un serveur local existant pour s’exécuter sur une machine virtuelle dans Azure.

### <a id="oss"></a>Mon site utilise le logiciel open source et héberger dans Azure

Si votre infrastructure open source est pris en charge sur le Service d’application, les langues et les structures nécessaires à votre application sont configurés automatiquement. Service d’application vous permet de :

- Utilisation de nombreux populaires Ouvrir source langues, tels que [.NET][dotnet], [PHP][], [Node.js][nodejs]et [Python][].
- Configurer la WordPress, Drupal, Umbraco, DNN et bien d’autres applications web tiers.
- Migrer une application existante ou créer un nouveau à partir de la galerie d’applications.

Si votre infrastructure open source n’est pas pris en charge sur le Service d’application, vous pouvez l’exécuter sur une des options d’hébergement l’autres web Azure. Machines virtuelles, vous installez et configurez le logiciel sur l’image de l’ordinateur, qui peut être Windows ou Linux.

### <a id="lob"></a>J’ai une application métier qui doit se connecter au réseau d’entreprise

Si vous voulez créer une application métier, votre site Web peut-être nécessitent un accès direct aux services ou de données sur le réseau d’entreprise. Cela est possible sur le Service d’application, tissu de Service et Machines virtuelles au moyen du [service de réseau virtuel Azure](/services/virtual-network/). Service d’application, vous pouvez utiliser la [fonctionnalité d’intégration VNET](https://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/), qui permet à vos applications Azure comme s’il s’agissait sur votre réseau d’entreprise.

### <a id="mobile"></a>Héberger une API REST ou un service web pour les clients mobiles

Services web basé sur HTTP permettent de prendre en charge un large éventail de clients, y compris les clients mobiles. Intégrant des cadres, telles que l’API Web ASP.NET avec Visual Studio pour créer et utiliser des services reste plus facilement.  Ces services sont exposées à partir d’un point de terminaison web, aussi est-il possible d’utiliser un site web d’hébergement technique sur Azure pour prendre en charge ce scénario. Toutefois, application Service s’avère très intéressante pour l’hébergement des API REST. Avec le Service d’application, vous pouvez :

- Créer rapidement une [application mobile](../app-service-mobile/app-service-mobile-value-prop.md) ou une [application API](../app-service-api/app-service-api-apps-why-best-platform.md) pour héberger le service web HTTP dans un des centres de données globalement distribué d’Azure.
- Migrer des services existants ou créer de nouveaux.
- Atteindre SLA pour la disponibilité avec une seule occurrence ou évoluer sur plusieurs ordinateurs dédiés.
- Utiliser le site publié pour fournir des API REST à tous les clients HTTP, y compris les clients mobiles.

> [AZURE.NOTE]
> Si vous voulez commencer à utiliser le Service d’application Azure avant de vous inscrire pour un compte, accédez à <a href="https://trywebsites.azurewebsites.net/">https://trywebsites.azurewebsites.net</a>, où vous pouvez immédiatement créer une application starter courte dans le Service d’application Azure gratuitement. Aucune carte de crédit n’obligatoire, aucune engagements.

## <a id="nextsteps"></a>Étapes suivantes

Pour plus d’informations sur les trois options d’hébergement web, voir [Présentation Azure](../fundamentals-introduction-to-azure.md).

Pour vous familiariser avec les options appropriées que vous choisissez pour votre application, consultez les ressources suivantes :

* [Service application Azure](/documentation/services/app-service/)
* [Services Cloud Azure](/documentation/services/cloud-services/)
* [Machines virtuelles Azure](/documentation/services/virtual-machines/)
* [TISSU de service](/documentation/services/service-fabric)

<!-- URL List -->

[Service application Azure]: /services/app-service/
[Services en nuage]: http://go.microsoft.com/fwlink/?LinkId=306052
[Machines virtuelles]: http://go.microsoft.com/fwlink/?LinkID=306053
[TISSU de service]: /services/service-fabric
[ClearDB]: http://www.cleardb.com/
[WebJobs]: http://go.microsoft.com/fwlink/?linkid=390226&clcid=0x409
[Configuring an SSL certificate for an Azure Website]: http://www.windowsazure.com/develop/net/common-tasks/enable-ssl-web-site/
[azurestore]: http://www.windowsazure.com/gallery/store/
[scripting]: http://www.windowsazure.com/documentation/scripts/?services=web-sites
[dotnet]: http://www.windowsazure.com/develop/net/
[nodejs]: http://www.windowsazure.com/develop/nodejs/
[PHP]: http://www.windowsazure.com/develop/php/
[Python]: http://www.windowsazure.com/develop/python/
[servicebus]: http://www.windowsazure.com/documentation/services/service-bus/
[sqldatabase]: http://www.windowsazure.com/documentation/services/sql-database/
[Espace de stockage]: http://www.windowsazure.com/documentation/services/storage/

<!-- IMG List -->

[ChoicesDiagram]: ./media/choose-web-site-cloud-service-vm/Websites_CloudServices_VMs_3.png
