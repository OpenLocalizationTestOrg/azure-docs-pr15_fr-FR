<properties
   pageTitle="Vue d’ensemble du Service TISSU | Microsoft Azure"
   description="Vue d’ensemble de tissu de Service, où les applications sont composées de nombreux microservices pour fournir échelle et la résilience. Service est considérée comme une plateforme de systèmes distribués utilisé pour générer le format SVG, fiable et facile à gérer les applications pour le nuage"
   services="service-fabric"
   documentationCenter=".net"
   authors="msfussell"
   manager="timlt"
   editor="masnider"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/22/2016"
   ms.author="mfussell"/>

# <a name="overview-of-service-fabric"></a>Vue d’ensemble de tissu de Service
TISSU de service est une plateforme de systèmes distribués qui facilite le package, déployer et gérer des microservices scalable et fiables. Service TISSU résout également les problèmes importants de développement et de gestion des applications cloud. Les développeurs et les administrateurs peuvent éviter résoudre le focus et des problèmes d’infrastructure complexes à la place sur l’implémentation de charges de travail critiques, exigeants savoir qu’elles sont scalable, fiables et gérables. Service TISSU représente la plateforme logiciels intermédiaires nouvelle génération pour la création et gestion de ces entreprise, les applications cloud à l’échelle de niveau 1.

Cette [courte vidéo](https://aka.ms/servicefabricvideo) fournit une introduction pour tissu de Service et microservices.


## <a name="applications-composed-of-microservices"></a>Les applications composées de microservices
TISSU de service vous permet de créer et gérer des applications fiables et scalable composées de microservices en cours d’exécution en très haute densité sur un pool de machines (appelées un cluster) partagé. Il fournit un runtime sophistiqué pour générer distribué, scalable microservices avec et sans état. Il fournit également des fonctionnalités de gestion des application complète pour la mise en service, le déploiement, de surveillance, la mise à niveau/correction et suppression d’applications déployées.

Pourquoi une approche microservices est-il important ? Les deux raisons principales sont :

1. Ils permettent à l’échelle des différentes parties de votre application en fonction de ses besoins.

2. Équipes de développement peuvent être plus agile dans déploiement de modifications et par conséquent fournissent des fonctionnalités à vos clients plus rapidement et plus fréquemment.

Service TISSU se met plusieurs services Microsoft aujourd'hui, y compris la base de données SQL Azure, Azure DocumentDB, Cortana, Power BI, Microsoft Intune, Azure événement Hubs, IoT Azure, Skype entreprise et la plupart des services Azure principaux.

TISSU de service est conçue pour la création de cloud services natifs qui peuvent démarrer petite, selon vos besoins et agrandir à grande échelle avec des centaines ou des milliers d’ordinateurs.

Les services Internet à l’échelle d’aujourd'hui sont composés de microservices. Exemples de microservices passerelles protocole, profils utilisateur, vos achats paniers, inventaire traitement, files d’attente et met en cache. TISSU de service est une plateforme microservices qui donne chaque microservice un nom unique qui peut être sans état ou avec état.

Service TISSU propose des fonctionnalités complètes gestion de runtime et du cycle de vie aux applications composées de ces microservices. Il héberge microservices à l’intérieur de conteneurs déployé et activé sur le cluster tissu de Service. Passer de machines virtuelles à possible de rend conteneurs augmente ordre d’ampleur dans densité. De même, un autre ordre d’ampleur dans densité devient possible en déplaçant des conteneurs vers microservices. Par exemple, un seul cluster de base de données SQL Azure comprend des centaines d’ordinateurs qui exécutent des milliers de conteneurs d’hébergement au total des milliers de bases de données. Chaque base de données est un microservice dynamique tissu de Service. Vaut des autres services mentionnés précédemment, c’est pourquoi le terme « hyperscale » est utilisé pour décrire les fonctionnalités de tissu de Service. Si les conteneurs donnent haute densité, puis microservices vous offre hyperscale.

Pour plus d’informations sur l’approche microservices, lisez [Pourquoi une approche microservices à la création d’applications ?](service-fabric-overview-microservices.md)

## <a name="container-deployment-and-orchestration"></a>Orchestration et déploiement de conteneur
TISSU de service est un [orchestrator](service-fabric-cluster-resource-manager-introduction.md) de microservices sur un cluster de machines. Microservices peuvent être développés de nombreuses façons d’utiliser les [modèles de programmation tissu de Service](service-fabric-choose-framework.md) pour le déploiement [exécutables invité](service-fabric-deploy-existing-app.md). Service TISSU pouvez déployer des services dans les images de conteneur et par ailleurs, vous pouvez combiner les services dans les processus et services dans des conteneurs de collaborer dans la même application. Si vous voulez simplement [déployer et gérer des images du conteneur](service-fabric-containers-overview.md) sur un cluster de machines, tissu de Service est le choix idéal pour cela.


## <a name="create-service-fabric-clusters-anywhere"></a>Créer des clusters tissu de Service n’importe où
Vous pouvez créer Service TISSU clusters dans de nombreux environnements, y compris Azure ou en local, sur Windows Server ou sur Linux. En outre, l’environnement de développement dans le Kit de développement est identique à l’environnement de production avec aucune émulateurs impliquées. En d’autres termes, si elle s’exécute sur votre cluster de développement local il déploie sur le même cluster dans d’autres environnements.

Pour plus d’informations sur la création de clusters locales, consultez [Création d’un cluster sur Windows Server ou Linux](service-fabric-deploy-anywhere.md) ou pour Azure création d’un cluster [via le portail Azure](service-fabric-cluster-creation-via-portal.md).

![Plateforme tissu de service][Image1]

## <a name="stateless-and-stateful-service-fabric-microservices"></a>Avec et sans état microservices tissu de Service

TISSU de service vous permet de créer des applications composée de microservices. Microservices sans état (passerelles protocole, proxys web, etc.) ne conservent pas un état modifiable en dehors de toute demande donnée et sa réponse à partir du service. Rôles de travail Azure Cloud Services sont un exemple d’un service sans état. État microservices (comptes d’utilisateur, les bases de données, périphériques, les paniers, files d’attente, etc.) maintenir un état modifiable, faisant autorité au-delà de la demande et sa réponse. Les applications Internet à l’échelle d’aujourd'hui se composent d’une combinaison de microservices avec et sans état.

Pourquoi utiliser microservices dynamique ainsi que celles sans état ? Les deux raisons principales sont :

1. La possibilité de créer une transaction en ligne haut débit à tolérance de panne, faible latence traitement des services (OLTP) en conservant le code et les données fermer sur le même ordinateur. Voici quelques exemples sont interactifs présentations, recherche, systèmes Internet des objets (IoT), systèmes commerciaux, systèmes de détection de traitement et la fraude carte de crédit et gestion des enregistrements personnels.

2. Simplification de conception d’application. Microservices avec état supprimer la nécessité de files d’attente supplémentaires et met en cache, généralement requises pour répondre aux besoins disponibilité et latence d’une application purement sans état. Les services avec état sont naturellement disponibilité et faible latence, afin de réduire le nombre d’éléments mobiles à gérer dans votre application dans son ensemble.

Pour plus d’informations sur les modèles d’application avec tissu de Service, lisez [scénarios d’Application](service-fabric-application-scenarios.md) , puis [en choisissant une infrastructure de modèle de programmation](service-fabric-choose-framework.md) pour votre service

## <a name="application-lifecycle-management"></a>Gestion du cycle de vie des applications
Service TISSU fournit excellente prise en charge pour l’application complète du cycle de vie gestion des applications cloud--de développement jusqu’au déploiement, la gestion quotidienne et maintenance au déclassement éventuelle.

Les fonctionnalités de Service TISSU ALM permettent aux administrateurs d’applications / opérateurs informatiques d’utiliser des flux de travail simple, min tactile mise en service, déployer, correctif et surveiller des applications. Ces flux de travail intégrés réduire considérablement la charge sur les opérateurs informatiques vos applications disponibles en permanence.

La plupart des applications comportent une combinaison de microservices avec et sans état et autres exécutables/exécutions déployées ensemble. En installant des types forts sur les applications et microservices empaqueté, Service TISSU permet le déploiement de plusieurs instances de l’application. Chaque instance est géré et mis à niveau séparément. Par ailleurs, tissu de Service est en mesure de déployer *des* exécutables ou runtime et les rendre fiables. Par exemple, Service TISSU déploie ASP.NET Core 1, Node.js, machines virtuelles Java, scripts ou tout autre élément qui constitue votre application.

Pour plus d’informations sur la gestion du cycle de vie des applications, [leur cycle de vie](service-fabric-application-lifecycle.md) de lire et sur le déploiement de code, voir [déployer un invité exécutable](service-fabric-deploy-existing-app.md)

## <a name="key-capabilities"></a>Fonctionnalités clés
À l’aide de tissu de Service, vous pouvez :

- Développer des applications hautement scalable compense.

- Développer des applications composé de microservices à l’aide du modèle de programmation tissu de Service de recherche. Ou, il vous suffit hôte invité exécutables et autres structures d’application de votre choix, tels que ASP.NET Core 1 ou Node.js.

- Développer très fiable microservices avec et sans état.

- Déployer et gérer les conteneurs incluent Windows conteneurs et Docker sur un cluster. Ces conteneurs peuvent conteneur invité exécutables ou fiable microservices avec et sans état.  Dans les deux cas, vous obtenez port de conteneur pour le mappage de port hôte, détectabilité conteneur et basculement automatique.

- Simplifier la conception de votre application à l’aide de microservices dynamique à la place de cache et files d’attente.

- Déployer sur Azure ou sur nuages local exécutant Windows Server ou Linux avec zéro modifications du code. Écrire une seule fois, puis déployez n’importe où à n’importe quel cluster tissu de Service.

- Développer une approche « centre de données sur votre ordinateur ». L’environnement de développement local est le même code qui s’exécute dans les centres de données Azure.

- Déployer des applications en secondes.

- Déployer des applications à densité supérieure à celle de machines virtuelles, déployer des centaines ou des milliers d’applications par ordinateur.

- Déployer les différentes versions de la même application côte à côte, chaque indépendamment être mis à niveau.

- Gérer le cycle de vie de vos applications avec état sans interruption de service, y compris les mises à jour rupture et insécables.

- Gérer les applications à l’aide des API .NET, Java (Linux), PowerShell, Azure infrastructure du langage commun (Linux) ou reste interfaces.

- Mettre à niveau et correctifs microservices au sein d’applications séparément.

- Surveiller et diagnostiquer l’état de vos applications et définir des règles permettant d’effectuer des réparations automatiques.

- Possibilité de horizontale ou échelle dans le nombre de nœuds dans un cluster, ainsi qu’échelle à distance ou échelle en bas de la taille de chaque nœud, sachant que vos applications ajuster automatiquement et sont distribuées conformément les ressources disponibles.

- Regarder la réparation automatique équilibrage de ressources organiser la redistribuer des applications au sein du cluster. Service TISSU récupère échecs et optimise la distribution de charge en fonction des ressources disponibles.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations :
    * [Pourquoi un microservices approche à la création d’applications ?](service-fabric-overview-microservices.md)
    * [Vue d’ensemble de termes](service-fabric-technical-overview.md)
* Configurer votre [environnement de développement](service-fabric-get-started.md) tissu de Service  
* [Choisir une structure de modèle de programmation](service-fabric-choose-framework.md) pour votre service

[Image1]: media/service-fabric-overview/Service-Fabric-Overview.png
