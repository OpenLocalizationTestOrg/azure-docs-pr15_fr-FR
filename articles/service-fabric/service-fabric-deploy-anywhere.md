<properties
   pageTitle="Créer des clusters Azure Service tissu sur Windows Server et Linux | Microsoft Azure"
   description="Clusters TISSU service s’exécutés sur Windows Server et Linux, ce qui signifie que vous pourrez déployer et les applications de Service TISSU hôtes n’importe où vous pouvez exécuter Windows Server ou Linux."
   services="service-fabric"
   documentationCenter=".net"
   authors="Chackdan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/22/2016"
   ms.author="chackdan"/>

# <a name="create-service-fabric-clusters-on-windows-server-or-linux"></a>Créer des clusters tissu de Service sur Windows Server ou Linux

TISSU Service Azure permet la création de clusters tissu de Service sur des ordinateurs virtuels ou des ordinateurs exécutant Windows Server ou Linux. Cela signifie que vous pouvez déployer et exécuter des applications de Service tissu dans n’importe quel environnement dans lequel vous avez un ensemble d’ordinateurs Windows Server ou Linux qui sont interconnectés, qu’il s’agisse en local, Microsoft Azure ou avec n’importe quel fournisseur cloud.

##<a name="create-service-fabric-clusters-on-azure"></a>Créer des clusters Service tissu sur Azure

Création d’un cluster sur Azure s’effectue via un modèle de ressource ou le portail Azure. Pour plus d’informations, lisez [créer un cluster de Service tissu en utilisant un modèle de gestionnaire de ressources](service-fabric-cluster-creation-via-arm.md) ou [créer un cluster de tissu de Service à partir du portail Azure](service-fabric-cluster-creation-via-portal.md) .

## <a name="supported-operating-systems-for-clusters-on-azure"></a>Prise en charge des systèmes d’exploitation pour clusters sur Azure

Vous ne pouvez créer des clusters sur machines virtuelles fonctionnant sous les systèmes d’exploitation :

* Windows Server 2012 R2
* Windows Server 2016 (une fois que celle-ci est annoncé comme sera-t-il disponible)
* Linux Ubuntu 16.04 (dans la version d’évaluation) 


##<a name="create-service-fabric-standalone-clusters-on-premise-or-with-any-cloud-provider"></a>Créer des Service TISSU autonome clusters locales ou avec n’importe quel fournisseur cloud

Service TISSU fournit un package d’installation pour vous permet de créer autonome Service TISSU clusters en local ou sur n’importe quel fournisseur cloud

Pour plus d’informations sur la configuration de clusters du tissu service autonome sur Windows Server, lisez [Création d’un cluster tissu de Service pour Windows Server](service-fabric-cluster-creation-for-windows-server.md)

### <a name="any-cloud-deployments-vs-on-premises-deployments"></a>Les déploiements dans le cloud et les déploiements sur site
Le processus de création d’un cluster de Service TISSU local est semblable au processus de création d’un cluster sur n’importe quel nuage de votre choix d’un jeu supplémentaire de machines virtuelles. Les premières étapes de mise en service les ordinateurs virtuels sont régis par le fournisseur de cloud ou un environnement local que vous utilisez. Lorsque vous avez un ensemble de machines virtuelles avec la connectivité réseau activée entre eux, puis les étapes pour configurer le package tissu de Service, modifier les paramètres de cluster et exécuter la création d’un cluster et gestion des scripts sont identiques. Cela garantit que vos connaissances et une expérience d’exploitation et la gestion des clusters tissu de Service est transférable lorsque vous choisissez de cibler les nouveaux environnements d’hébergement.

### <a name="benefits-of-creating-standalone-service-fabric-clusters"></a>Avantages de la création de clusters de Service TISSU autonome
* Vous pouvez choisir n’importe quel fournisseur cloud pour héberger votre cluster.
* Service TISSU applications, écrites une seule fois, peuvent être exécutées dans plusieurs environnements d’hébergement avec minimales à aucune modification.
* Connaissance de création d’applications de Service TISSU transfère à partir d’un environnement d’hébergement à l’autre.
* Expérience opérationnelle en cours d’exécution et la gestion des TISSU Service cluster ballon sur à partir d’un environnement vers un autre.
* La clientèle large est illimitée en contraintes de l’environnement d’hébergement.
* Un niveau supplémentaire de la fiabilité et la protection contre les défaillances largement existe parce que vous pouvez déplacer les services sur un autre environnement de déploiement si un fournisseur de données centre ou sur le cloud comporte une coupure de courant.

## <a name="supported-operating-systems-for-standalone-clusters"></a>Prise en charge des systèmes d’exploitation pour clusters autonome
Vous ne pouvez créer des clusters sur machines virtuelles ou les ordinateurs exécutant ces systèmes d’exploitation :

* Windows Server 2012 R2
* Windows Server 2016 (une fois que celle-ci est annoncé comme sera-t-il disponible)
* Linux (bientôt disponible)

## <a name="advantages-of-service-fabric-clusters-on-azure-over-standalone-service-fabric-clusters-created-on-premises"></a>Avantages du Service tissu sur Azure par rapport aux clusters autonome que service TISSU clusters créée en local

Clusters tissu de Service en cours d’exécution sur Azure fournit option avantages par rapport à l’en local, donc si vous n’avez pas besoins spécifiques pour lequel vous exécutez vos clusters, puis nous vous recommandons de les exécuter sur Azure. Sous Azure, nous fournissons l’intégration avec d’autres fonctionnalités Azure et services, ce qui facilite les opérations et la gestion du cluster et plus fiable.

* **Portail azure :** Portail Azure facilite la créer et gérer des clusters.

* **Le Gestionnaire de Ressources azure :** Utilisation du Gestionnaire de ressources Azure permet de faciliter la gestion de toutes les ressources utilisées par le cluster comme une unité et simplifie le suivi des coûts et la facturation.
* **Service TISSU Cluster comme une ressource d’Azure** Un cluster de Service tissu est une ressource processeur, afin que vous pouvez le modèle comme vous le faites d’autres ressources processeur dans Azure.
* **Intégration à l’Infrastructure Azure** Service TISSU coordonnées avec l’infrastructure sous-jacente Azure de système d’exploitation, du réseau et autres mises à niveau afin d’améliorer la disponibilité et la fiabilité de vos applications.  
* **Diagnostics :** Sous Azure, nous fournissons l’intégration avec les diagnostics de Windows Azure et journal Analytique.
* **Mise à l’échelle :** Pour les clusters sur Azure, nous fournissons des fonctionnalités intégrées de mise à l’échelle en raison de l’échelle des ensembles de Machine virtuelle. Dans les autres environnements cloud et en local, vous devez créer votre propre fonctionnalité ou échelle manuellement à l’aide de l’API expose tissu de Service pour suivre l’évolution de clusters mise à l’échelle.

## <a name="next-steps"></a>Étapes suivantes
Créer un cluster sur les ordinateurs exécutant Windows Server ou machines virtuelles : [Création d’un cluster tissu de Service pour Windows Server](service-fabric-cluster-creation-for-windows-server.md)

Créer un cluster sur les ordinateurs exécutant Linux ou machines virtuelles : [TISSU Service sous Linux](service-fabric-linux-overview.md)
