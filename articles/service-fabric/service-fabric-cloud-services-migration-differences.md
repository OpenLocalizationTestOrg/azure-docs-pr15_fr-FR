<properties
   pageTitle="Différences entre les Services en nuage et tissu Service | Microsoft Azure"
   description="Une vue d’ensemble conceptuelle de la migration applications à partir de Services Cloud pour tissu de Service."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/19/2016"
   ms.author="vturecek"/>

# <a name="learn-about-the-differences-between-cloud-services-and-service-fabric-before-migrating-applications"></a>En savoir plus sur les différences entre les Services en nuage et tissu de Service avant de migrer les applications.
Microsoft Azure Service tissu est la plate-forme d’applications cloud de nouvelle génération pour les applications distribuées hautement scalable et très fiables. Il présente plusieurs nouvelles fonctionnalités d’emballage, le déploiement, la mise à niveau et gérer les applications cloud distribué. 

Il s’agit d’un guide d’introduction à la migration applications à partir de Services Cloud pour tissu de Service. Il se concentre principalement sur architecturale et différences de conception entre les Services en nuage et tissu de Service.
 
## <a name="applications-and-infrastructure"></a>Applications et l’infrastructure

Une différence fondamentale entre les Services de Cloud et tissu de Service est la relation entre machines virtuelles, les charges de travail et les applications. Une charge de travail est définie comme le code que vous écrivez pour effectuer une tâche spécifique ou fournir un service.
 
 - **Les Services en nuage est sur le déploiement des applications en tant que machines virtuelles.** Le code que vous écrivez est étroitement à une instance de machine virtuelle, tel qu’un Web ou le rôle de travail. Pour déployer une charge de travail dans les Services en nuage consiste à déployer une ou plusieurs instances de machine virtuelle qui s’exécutent de la charge de travail. Il n’existe aucune séparation des applications et des machines virtuelles et, si bien qu’il n’existe aucune définition formelle d’une application. Une application peut être considérée sous la forme d’un ensemble d’instances Web ou le rôle de collaborateur dans un déploiement de Services de Cloud ou un déploiement de Services de Cloud entière. Dans cet exemple, une application apparaît sous la forme d’un ensemble d’instances de rôle.
 
![Applications de Services de cloud et la topologie][1]

 - **TISSU de service est sur le déploiement des applications de machines virtuelles existantes ou machines tissu de Service en cours d’exécution sur Windows ou Linux.** Les services que vous rédigez sont complètement dissociées à partir de l’infrastructure sous-jacente, disparaît par la plateforme d’application tissu de Service, pour une application peut être déployée sur plusieurs environnements. Une charge de travail dans tissu de Service est appelé « service » et un ou plusieurs services sont regroupées dans une application formelle défini qui s’exécute sur la plate-forme d’application tissu de Service. Plusieurs applications peuvent être déployées vers un seul cluster tissu de Service.
 
![Topologie et les applications de Service TISSU][2]
 
TISSU de service elle-même est une couche de plateforme d’application qui s’exécute sur Windows ou Linux, tandis que les Services en nuage est un système pour le déploiement des machines virtuelles gérées Azure avec joint les charges de travail.
Le modèle d’application Service TISSU compte de nombreux avantages :

 - Temps de déploiement rapide. Création d’instances de machine virtuelle peut prendre beaucoup de temps. Dans Service tissu, machines virtuelles sont déployés uniquement une fois pour former un cluster qui héberge la plate-forme tissu de Service d’application. À partir de là, packages d’application peuvent être déployées vers le cluster très rapidement.
 - Densité d’hébergement. Dans les Services en nuage, un rôle de collaborateur machine virtuelle héberge une charge de travail. Dans Service tissu, applications sont distingue des ordinateurs virtuels qui s’exécutent, ce qui signifie que vous pouvez déployer un grand nombre d’applications pour un petit nombre d’ordinateurs virtuels, qui peuvent réduire les coûts globaux des déploiements plus importants.
 - La structure de Service plateforme peut exécuter n’importe où qui a machines Windows Server ou Linux, qu’il s’agisse Azure ou en local. La plateforme fournit une couche d’abstraction au-dessus de l’infrastructure sous-jacente afin que votre application peut s’exécuter sur différents environnements. 
 - Gestion des applications distribuées. TISSU de service est une plateforme que non seulement hôtes d’applications distribuées, mais également vous aide à gérer leur cycle de vie indépendamment de la machine virtuelle d’hébergement ou cycle de vie machine.

## <a name="application-architecture"></a>Architecture de l’application

L’architecture d’une application de Services de Cloud inclut généralement nombreuses dépendances des services externes, tels que Service Bus Table Azure et Blob Storage, SQL, Redis et d’autres personnes pour gérer l’état et les données d’une application et les communications entre Web et les rôles de travail dans un déploiement de Services de Cloud. Exemple d’une application complète de Services de Cloud peut ressembler à ceci :  

![Architecture des Services cloud][9]

Applications de service TISSU peuvent également choisir d’utiliser les mêmes services externes dans une application complète. Cet exemple montre l’architecture des Services Cloud le chemin migration la plus simple de Services Cloud pour tissu de Service est remplacer uniquement du déploiement des Services Cloud avec une application de Service tissu, tout en conservant l’architecture globale de la même. Le Web et les rôles de travail peuvent être portés aux services sans état Service tissu avec modifications un minimum de code.

![Architecture de service TISSU après la migration simple][10]

À ce stade, le système doit continuer à travailler les mêmes qu’auparavant. Profiter des fonctionnalités avec état du Service tissu, état externe stores peut être admise comme avec état des services le cas échéant. Il est plus complexe qu’une migration simple de rôles Web et collaborateur aux services sans état tissu de Service, car il nécessite l’écriture de services personnalisés qui fournissent une fonctionnalité équivalente à votre application comme des services externes avant. Les avantages de ces actions sont les suivantes : 

 - Suppression de dépendances externes 
 - Unifier le déploiement, la gestion et la mise à niveau des modèles. 
 
Une architecture qui en résulte exemple d’internalizing de ces services peut ressembler à ceci :

![Architecture de service TISSU après la migration complète][11]

## <a name="communication-and-workflow"></a>Communications et des flux de travail

La plupart des applications de Service Cloud comportent à plusieurs niveaux. De même, une application de Service TISSU se compose de plusieurs services (généralement plusieurs services). Deux modèles de communication courants sont une communication directe et via un stockage résistant externe.

### <a name="direct-communication"></a>Communication directe

Avec une communication directe, niveaux peut communiquer directement par le biais de point de terminaison exposé par chaque niveau. Dans les environnements sans état tels que les Services en nuage, cela signifie sélection d’une instance d’un rôle machine virtuelle, soit aléatoirement ou la cyclique pour équilibrer la charge et la connexion à son point de terminaison directement.

![Les Services en nuage rediriger des communications][5]

 Communication directe est un modèle de communication courantes dans tissu de Service. La différence clée entre tissu de Service et les Services en nuage est que Services Cloud en vous vous connectez à une machine virtuelle, tandis que dans tissu de Service que vous vous connectez à un service. Il s’agit d’une fonction importante pour plusieurs raisons :

 - Services dans Service tissu ne sont pas liés aux ordinateurs virtuels hébergeant les ; services peuvent se déplacer dans le cluster et sont en fait, prévus pour vous déplacer pour différentes raisons : ressources équilibrage de charge, le basculement, mises à niveau de l’application et de l’infrastructure et contraintes position ou les charger. Cela signifie qu'adresse d’une instance de service peut modifier à tout moment. 
 - Une machine virtuelle dans TISSU Service peut héberger plusieurs services, chacun avec des points de terminaison uniques.

Service TISSU fournit un mécanisme de découverte de service, appelé le Service de dénomination, qui peut être utilisé pour résoudre les adresses de point de terminaison de services. 

![Communication directe tissu de service][6]

### <a name="queues"></a>Files d’attente

Un mécanisme de communication commun entre les différents niveaux dans des environnements sans état tels que les Services en nuage consiste à utiliser une file d’attente de stockage externe pour stocker durablement les tâches de travail à partir d’un niveau à l’autre. Un scénario courant est un niveau web qui envoie des travaux à une file d’attente Azure ou Service Bus où les instances de rôle de collaborateur peuvent supprimer file d’attente et traiter les tâches.

![Communication de file d’attente de Services de cloud][7]

Le modèle de communication même peut être utilisé dans tissu de Service. Cela peut être utile lors de la migration d’une application Services Cloud pour tissu de Service. 

![Communication directe tissu de service][8]
 
## <a name="next-steps"></a>Étapes suivantes

Le chemin de migration la plus simple de Services de Cloud pour tissu de Service consiste à remplacer uniquement du déploiement des Services Cloud avec une application de Service tissu, tout en conservant l’architecture globale de votre application à peu près les mêmes. L’article suivant fournit un guide pour vous aider à convertir un site Web ou rôle de travail à un service sans état tissu de Service.

 - [Migration simple : convertir un site Web ou rôle de travail à un service sans état tissu de Service](./service-fabric-cloud-services-migration-worker-role-stateless-service.md)

<!--Image references-->
[1]: ./media/service-fabric-cloud-services-migration-differences/topology-cloud-services.png
[2]: ./media/service-fabric-cloud-services-migration-differences/topology-service-fabric.png
[5]: ./media/service-fabric-cloud-services-migration-differences/cloud-service-communication-direct.png
[6]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-communication-direct.png
[7]: ./media/service-fabric-cloud-services-migration-differences/cloud-service-communication-queues.png
[8]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-communication-queues.png
[9]: ./media/service-fabric-cloud-services-migration-differences/cloud-services-architecture.png
[10]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-architecture-simple.png
[11]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-architecture-full.png
