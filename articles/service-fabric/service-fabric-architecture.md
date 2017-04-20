<properties
   pageTitle="Architecture de matrice service | Microsoft Azure"
   description="TISSU de service est une plateforme de systèmes distribués utilisée pour créer des applications scalable fiables et facile à gérer pour le cloud. Cet article vous explique l’architecture de tissu de Service."
   services="service-fabric"
   documentationCenter=".net"
   authors="rishirsinha"
   manager="timlt"
   editor="rishirsinha"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="06/09/2016"
   ms.author="rsinha"/>

# <a name="service-fabric-architecture"></a>Architecture de matrice de service

TISSU de service est constitué de sous-systèmes en couches. Ces sous-systèmes permettent d’écrire des applications qui sont :

* Disponibilité
* Format SVG
* Facile à gérer
* Faire l’objet

Le diagramme suivant montre les principaux sous-systèmes de tissu de Service.

![Diagramme d’architecture de matrice de Service](media/service-fabric-architecture/service-fabric-architecture.png)

Dans un système distribué, la possibilité de communiquer en toute sécurité entre les nœuds dans un cluster est essentielle. Au bas de la pile est le produit de transport, qui fournit une communication sécurisée entre les nœuds. Au-dessus du transport sous-système trouve le produit de fédération, qui clusters différents nœuds en une seule entité (appelée clusters) afin que TISSU Service peut détecter les échecs, effectuer des élections points de suite et assurer l’acheminement du cohérentes. Le sous-système fiabilité, superposée sous-système fédération, est responsable de la fiabilité des services de structure de Service par le biais mécanismes tels que la réplication, gestion des ressources et basculement. Le sous-système fédération sous-jacente également le produit d’hébergement et l’activation, qui gère le cycle de vie d’une application sur un seul nœud. Le sous-système gestion gère le cycle de vie des applications et services. Le sous-système test permet aux développeurs application tester leurs services par défauts simulés avant et après le déploiement des applications et services aux environnements de production. Service TISSU permet de résoudre les emplacements de service via son sous-système de communication. Les modèles de programmation application exposés aux développeurs sont basés sur ces sous-systèmes ainsi que le modèle d’application pour activer des outils.

## <a name="transport-subsystem"></a>Sous-système de transport
Le sous-système de transport met en œuvre un canal de communication datagramme point à point. Ce canal est utilisé pour les communications dans clusters tissu de service et entre le cluster tissu de service et les clients. Il prend en charge à sens unique et modèles de communication demande-réponse, qui fournit la base de mise en œuvre des types de la couche de fédération. Le sous-système de transport protège les communications à l’aide de X509 certificats ou la sécurité de Windows. Ce sous-système est utilisé en interne par tissu de Service et n’est pas directement accessible aux développeurs de programmation d’application.

## <a name="federation-subsystem"></a>Sous-système de fédération
Pour réfléchir à un ensemble de nœuds dans un système distribué, vous devez avoir une vue cohérente du système. Le sous-système de fédération utilise les primitives communications fournies par le sous-système de transport et épousaient différents nœuds dans un seul cluster unifié il peut réfléchir à. Il fournit les primitives systèmes distribués requis par les autres sous-systèmes - détection de défaillance, élections points de suite et l’acheminement cohérentes. Le sous-système fédération est basé sur les tables de hachage distribué par un espace jeton 128 bits. Le sous-système crée une topologie en anneau sur les nœuds, chaque nœud dans l’anneau allocation un sous-ensemble de l’espace jeton pour la propriété. Pour détecter une défaillance, la couche utilise un mécanisme location basé sur cœur TREFILAGE et d’arbitrage. Le sous-système fédération garantit également via jointure complexe et les protocoles au départ uniquement propriétaire d’un jeton existant à tout moment. Cela fournissent des élections points de suite et les garanties routage cohérentes.

## <a name="reliability-subsystem"></a>Sous-système de fiabilité
Le sous-système fiabilité fournit le mécanisme pour rendre l’état d’un service Service TISSU hautement disponible à l’aide du _réplicateur_, _Le Gestionnaire de basculement_et _Équilibrage de la ressource_.

* Le réplicateur garantit que les changements d’état du réplica de service principal seront automatiquement répliquées vers réplicas secondaires, la cohérence entre les réplicas principaux et secondaires d’un jeu de réplica de service. Le réplicateur est responsable de gestion de quorum entre les réplicas dans le jeu de réplica. Il interagit avec l’unité de basculement pour obtenir la liste des opérations à répliquer, et lui l’agent reconfiguration fournit la configuration de l’ensemble de copie. Cette configuration indique les réplicas les opérations devront être répliquées. Service TISSU fournit un fabricant par défaut appelé tissu Replicator, qui peuvent être utilisées par l’API de modèle de programmation pour vérifier l’état du service hautement disponible et fiable.
* Le Gestionnaire de basculement garantit que lorsque des nœuds sont ajoutés à ou supprimés du cluster, la charge est redistribuée automatiquement sur les nœuds disponibles. Si un nœud du cluster échoue, le cluster reconfigurer automatiquement les répliques pour maintenir la disponibilité du service.
* Le Gestionnaire de ressources place réplicas de service au sein de domaine Échec du cluster et garantit que toutes les unités de basculement sont opérationnelles. Le Gestionnaire de ressources équilibre également ressources du service sur le pool de nœuds pour atteindre la distribution de charge uniforme optimale partagé sous-jacente.

## <a name="management-subsystem"></a>Sous-système de gestion
Le sous-système gestion fournit service de bout en bout et gestion du cycle de vie des applications. API d’administration et des applets de commande PowerShell permettre de mise en service, le déploiement, correctif, mise à niveau et mettre hors service les applications sans perte de disponibilité. Le sous-système gestion effectue cela via les services suivants.

* **Gestionnaire de cluster**: il s’agit du service principal qui interagit avec le Gestionnaire de basculement à partir de la fiabilité pour placer les applications sur les nœuds selon les contraintes de placement de service. Le Gestionnaire de ressources dans sous-système basculement garantit que les contraintes ne sont jamais rompus. Le Gestionnaire de cluster gère le cycle de vie des applications à partir de la mise en service pour mettre hors service. Il s’intègre avec le Gestionnaire d’état pour vous assurer que la disponibilité des applications n’est pas perdue du point de vue sémantique santé au cours des mises à niveau.
* **Gestionnaire d’état**: ce service permet d’analyse du fonctionnement des applications, services et entités cluster. Entités cluster (par exemple, les nœuds, service des partitions et duplications) peuvent signaler les informations d’intégrité, qui sont regroupées puis dans le magasin d’intégrité centralisé. Ces informations d’intégrité fournissent un instantané global point-à-temps d’intégrité des services et des nœuds répartis sur plusieurs nœuds dans le cluster, ce qui vous permet d’effectuer les actions de correction nécessaires. Requête santé Qu'api permettre des événements d’état de la requête a signalé au sous-système d’intégrité. La requête d’intégrité API retournent les données brutes santé stockées dans le magasin d’intégrité ou agrégé, interprété données d’intégrité d’une entité cluster spécifique.
* **Magasin d’image**: ce service offre de stockage et distribution des fichiers binaires application. Ce service offre un espace de stockage de fichiers DFS simple où les applications sont téléchargées vers et téléchargées à partir de.


## <a name="hosting-subsystem"></a>Sous-système d’hébergement
Le Gestionnaire de cluster vous informe le sous-système d’hébergement (s’exécutant sur chaque nœud) quels services il doit gérer pour un nœud particulier. Le sous-système d’hébergement gère ensuite le cycle de vie de l’application sur ce nœud. Il interagit avec les composants de la fiabilité et l’état pour vous assurer que les réplicas sont placés correctement et fonctionnent correctement.

## <a name="communication-subsystem"></a>Sous-système de communication
Ce sous-système fournit messagerie fiable au sein de la découverte cluster et le service via le service d’attribution de noms. Le service d’appellation résout les noms de service vers un emplacement dans le cluster et permet aux utilisateurs de gérer les propriétés et les noms de service. Au moyen du service d’appellation, clients peuvent communiquer en toute sécurité avec n’importe quel nœud dans le cluster pour résoudre un nom de service et de récupérer les métadonnées de service. À l’aide d’un client d’appellation simple API, les utilisateurs du Service TISSU peuvent développer des services et clients capables de résoudre l’emplacement réseau actuel malgré dynamisme nœud ou nouveau de redimensionnement du cluster.

## <a name="testability-subsystem"></a>Sous-système de test
Elle est un ensemble d’outils conçues spécialement conçus pour tissu de Service de test. Les outils permettent un développeur facilement inciter les erreurs significatifs et exécuter des scénarios de test pour exercice et valider les nombreux États et transitions conséquences d’un service tout au long de sa durée de vie, tous de manière contrôlée et approuvée. Capacité de test fournit également un mécanisme pour exécuter les tests plus pouvant parcourir via différentes défaillances possibles sans perdre sa disponibilité. Cela vous offre un environnement de test dans production.
