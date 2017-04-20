<properties
    pageTitle="Modèles d’Application SQL Server sur machines virtuelles | Microsoft Azure"
    description="Cet article traite des modèles d’application pour SQL Server sur machines virtuelles Azure. Il propose solution architectes et les développeurs de base pour la conception et architecture de l’application appropriée."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="luisherring"
    manager="jhubbard"
    editor=""
    tags="azure-service-management,azure-resource-manager" />
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="08/19/2016"
    ms.author="lvargas" />

# <a name="application-patterns-and-development-strategies-for-sql-server-in-azure-virtual-machines"></a>Modèles d’application et les stratégies de développement pour SQL Server dans des Machines virtuelles Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


## <a name="summary"></a>Résumé :
Déterminer quel modèle d’application ou des motifs à utiliser pour vos applications basés sur SQL Server dans Azure environnement est une décision de conception important et nécessite une bonne compréhension du fonctionnement des SQL Server et chaque composant infrastructure d’Azure. Avec SQL Server dans Azure Infrastructure Services, vous pouvez facilement migrer, mettre à jour et contrôler vos applications SQL Server existantes Windows Server intégré sur des machines virtuelles dans Azure.

L’objectif de cet article consiste à fournir solution architectes et développeurs d’une base de bonne application architecture et la conception, qu’il peut utiliser lors de la migration d’applications existantes d’Azure ainsi que de développer de nouvelles applications dans Azure.

Pour chaque modèle d’application, vous trouverez d’un scénario en local, sa solution respectif activé pour le cloud et les recommandations techniques connexes. En outre, l’article aborde les stratégies de développement Azure spécifiques afin que vous pouvez créer vos applications correctement. En raison de nombreux modèles d’application possible, il est recommandé d’architectes et les développeurs doivent choisir le modèle qui conviennent pour leurs utilisateurs et d’applications.

**Collaborateurs techniques :** Luis Carlos Vargas individus, Madhan Arumugam Ramakrishnan

**Réviseurs techniques :** Corey Sanders, Drew McDaniel, Narayan Annamalai, Nir Mashkowski, Sanjay Mishra, Silvano Coriani, Stefan Schackow, Tim Hickey, Tim Wieman, Jin x

## <a name="introduction"></a>Introduction

Vous pouvez développer différents types d’applications multicouches en séparant les composants des couches d’application sur des ordinateurs différents ainsi que dans des composants séparés. Par exemple, vous pouvez placer l’application cliente et composants dans un seul ordinateur, couche web frontaux et composants du niveau d’accès aux données dans un autre ordinateur et un niveau de base de données principale dans un autre ordinateur des règles d’entreprise. Ce type de structurer permet d’isoler chaque niveau de l’autre. Si vous changez d'où proviennent les données, vous n’avez pas besoin de modifier la client ou une application web, mais uniquement les composants de niveau de l’accès aux données.

Une application classique *multicouches* inclut la couche de présentation, la couche d’entreprise et la couche de données :


| Niveau              | Description                                                                                                                                                                     |
|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Présentation** | La *couche de présentation* (couche web, couche frontale) est la couche dans laquelle les utilisateurs interagissent avec une application.                                                                      |
| **Entreprise**     | Le *niveau de l’entreprise* (intermédiaire) est la couche que la présentation et la couche de données de communiquer avec eux et inclut les fonctionnalités principales du système. |
| **Données**         | La *couche données* correspond essentiellement au serveur qui stocke les données d’une application (par exemple, un serveur qui exécute SQL Server).                                                             |

Couches d’application décrivent les regroupements logiques des fonctionnalités et des composants d’une application ; alors que les niveaux décrire la distribution physique des fonctionnalités et des composants sur des serveurs physiques distincts, ordinateurs, réseaux ou emplacements à distance. Les couches d’une application peuvent se trouver sur le même ordinateur physique (le même niveau) ou peuvent être distribués sur des ordinateurs distincts (multicouches) et les composants de chaque calque communiquent avec des composants dans d’autres calques au moyen d’interfaces bien définies. Vous pouvez considérer le terme niveau comme faisant référence à des modèles de distribution physiques tels que multicouches à deux niveaux et à trois niveaux. Un **modèle d’application de niveau 2** contient deux niveaux de l’application : serveur d’applications et serveur de base de données. La communication directe a lieu entre le serveur d’applications et le serveur de base de données. Le serveur d’applications contient des composants de niveau web et couche d’entreprise. Dans le **modèle d’application de niveau 3**, il existe trois niveaux de l’application : serveur web, serveur d’applications, qui contient le niveau de la logique métier et/ou les composants d’accès aux données métier couche et le serveur de base de données. La communication entre le serveur web et le serveur de base de données qui se passe sur le serveur d’applications. Pour plus d’informations sur les niveaux et les couches d’application, voir [Microsoft Application Architecture Guide](https://msdn.microsoft.com/library/ff650706.aspx).

Avant de commencer la lecture de cet article, vous devez connaître sur les concepts fondamentaux de SQL Server et Azure. Pour plus d’informations, voir la [Documentation en ligne de SQL Server](https://msdn.microsoft.com/library/bb545450.aspx), [SQL Server dans le Machines virtuelles Azure](virtual-machines-windows-sql-server-iaas-overview.md) et [Azure.com](https://azure.microsoft.com/).

Cet article décrit plusieurs modèles d’application qui peuvent être adaptés à vos applications simples, ainsi que les applications d’entreprise très complexes. Avant de détaillant chaque modèle, nous vous recommandons de que vous devez vous familiariser avec les services de stockage de données disponibles dans Azure, tels que [Le stockage Azure](../storage/storage-introduction.md)et [Base de données SQL Azure](../sql-database/sql-database-technical-overview.md) [SQL Server dans une Machine virtuelle Azure](virtual-machines-windows-sql-server-iaas-overview.md). Pour prendre les meilleures décisions de conception pour vos applications, apprendre à utiliser le service de stockage de données clairement.

### <a name="choose-sql-server-in-an-azure-virtual-machine-when"></a>Sélectionnez SQL Server dans une Machine virtuelle Azure, lorsque :

- Vous avez besoin de contrôle sur SQL Server et Windows. Par exemple, cela peut inclure la version de SQL Server, correctifs spécial, configuration de performances, etc..

- Vous avez besoin d’une compatibilité complète avec SQL Server en local et à déplacer les applications existantes vers Azure comme-est.

- Vous voulez exploiter les fonctionnalités de l’environnement Azure mais base de données SQL Azure ne prend pas en charge toutes les fonctionnalités que votre application requiert. Cela peut inclure les domaines suivants :

    - **Taille de la base de données**: au moment de cet article a été mis à jour, base de données SQL prend en charge une base de données de 1 To maximum de données. Si votre application requiert plus de 1 To de données et vous ne voulez pas implémenter des solutions ont personnalisé, il est recommandé d’utiliser dans une Machine virtuelle Azure SQL Server. Pour plus d’informations, voir [Mise à l’échelle horizontale Azure SQL bases de données](https://msdn.microsoft.com/library/azure/dn495641.aspx) et [les niveaux de performances et les niveaux de Service de base de données SQL Azure](../sql-database/sql-database-service-tiers.md).
    - **Conformité HIPAA**: Santés clients et les éditeurs de logiciels (éditeurs de logiciels) peuvent choisir [SQL Server dans le Machines virtuelles Azure](virtual-machines-windows-sql-server-iaas-overview.md) au lieu de la [Base de données SQL Azure](../sql-database/sql-database-technical-overview.md) , car SQL Server dans une Machine virtuelle Azure est couvert par HIPAA entreprise associer accord (BA). Pour plus d’informations sur la conformité, consultez [Microsoft Azure Trust Center : conformité](https://azure.microsoft.com/support/trust-center/compliance/).
    - **Fonctionnalités au niveau de l’instance**: pour le moment, base de données SQL non prises en charge des fonctionnalités qui résident en dehors de la base de données (tels que des serveurs liés, Agent des travaux, FileStream, Service intermédiaire, etc..). Pour plus d’informations, voir [instructions de bases de données SQL Azure et Limitations](https://msdn.microsoft.com/library/azure/ff394102.aspx).

## <a name="1-tier-simple-single-virtual-machine"></a>niveau 1 (simple) : machine virtuelle unique

Dans ce modèle d’application, vous déployez votre application SQL Server et la base de données pour une machine virtuelle autonome dans Azure. La même machine virtuelle contient votre application client/du web, les composants métier, couche d’accès aux données et le serveur de base de données. La présentation, entreprise et code d’accès aux données sont séparées logiquement mais se trouvent physiquement dans une machine serveur unique. La plupart des clients démarrer avec ce modèle d’application et ensuite, ils évoluer en ajoutant des rôles web ou machines virtuelles à leur système.

Ce modèle d’application est utile dans les cas suivants :

- Vous souhaitez effectuer une migration simple vers la plateforme Azure afin de déterminer si la plateforme répond à configuration requise de votre application ou non.

- Vous souhaitez afficher tous les niveaux d’application hébergés dans la même machine virtuelle dans le centre de données Azure même de réduire la latence entre les niveaux.

- Vous souhaitez rapidement mise en service de développement et tester les environnements de courtes périodes.

- Vous voulez effectuer le test pour différents niveaux de charge de travail de contrainte, mais en même temps que vous ne souhaitez pas posséder et gérer plusieurs machines physiques tout le temps.

Le diagramme suivant montre une simple en local scénario et comment vous pouvez déployer sa solution cloud activé dans une seule machine virtuelle dans Azure.

![modèle d’application de niveau 1](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728008.png)

Déploiement de la couche métier (logique métier et les composants d’accès aux données) sur le même niveau physique que la couche de présentation peut optimiser les performances de l’application, sauf si vous devez utiliser un niveau séparé en raison de problèmes de sécurité ou extensibilité du.

Dans la mesure où il s’agit pour commencer avec un modèle très commun, peuvent être utiles l’article suivant sur la migration pour déplacer vos données vers votre machine virtuelle de SQL Server : [migration de la base de données SQL Server sur un ordinateur virtuel Azure](virtual-machines-windows-migrate-sql.md).

## <a name="3-tier-simple-multiple-virtual-machines"></a>niveau de 3 (simple) : plusieurs machines virtuelles

Dans ce modèle d’application, vous déployez une application de niveau 3 dans Azure en plaçant chaque niveau de l’application dans une machine virtuelle différente. Cela fournit un environnement souple pour un simple scénarios extensions verticale et horizontale. Lorsqu’une machine virtuelle contient votre application client/du web, autre héberge vos composants métier et autre héberge le serveur de base de données.

Ce modèle d’application est utile dans les cas suivants :

- Vous souhaitez effectuer une migration des applications de base de données complexe pour le Machines virtuelles Azure.

- Vous voulez différents niveaux d’application devant figurer dans différentes régions. Par exemple, vous avez peut-être partagé bases de données qui sont déployés sur plusieurs zones pour créer un rapport.

- Vous voulez déplacer les applications d’entreprise à partir de plateformes locaux virtualisé pour le Machines virtuelles Azure. Pour plus d’informations sur les applications d’entreprise, voir [qu’est une Application d’entreprise](https://msdn.microsoft.com/library/aa267045.aspx).

- Vous souhaitez rapidement mise en service de développement et tester les environnements de courtes périodes.

- Vous voulez effectuer le test pour différents niveaux de charge de travail de contrainte, mais en même temps que vous ne souhaitez pas posséder et gérer plusieurs machines physiques tout le temps.

Le diagramme suivant illustre comment vous pouvez placer une application de niveau 3 simple dans Azure en plaçant chaque niveau de l’application dans une machine virtuelle différente.

![modèle d’application de niveau 3](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728009.png)

Dans ce modèle d’application, il existe une machine virtuelle () dans chaque niveau. Si vous avez plusieurs ordinateurs virtuels dans Azure, nous vous recommandons de configurer un réseau virtuel. [Réseau virtuel Azure](../virtual-network/virtual-networks-overview.md) crée une limite de sécurité approuvé et permet également de machines virtuelles de communiquer entre eux via l’adresse IP privée. En outre, toujours s’assurer que toutes les connexions Internet uniquement à la couche de présentation. Lorsque vous suivez ce modèle d’application, gérer les règles de groupe de sécurité de réseau pour contrôler l’accès. Pour plus d’informations, voir [Autoriser l’accès externe à votre ordinateur virtuel à l’aide du portail Azure](virtual-machines-windows-nsg-quickstart-portal.md).

Dans le diagramme, protocoles Internet peut être TCP, UDP, HTTP ou HTTPS.

>[AZURE.NOTE] La configuration d’un réseau virtuel dans Azure est gratuite. Toutefois, vous êtes chargé de la passerelle VPN qui se connecte à local. Ces frais est basé sur la durée de connexion est généré et disponible.

## <a name="2-tier-and-3-tier-with-presentation-tier-scale-out"></a>2-3 couche et présentation hiérarchiser horizontale

Dans ce modèle d’application, vous déployez application 2 ou 3 niveaux de base de données pour le Machines virtuelles Azure en plaçant chaque niveau de l’application dans une machine virtuelle différente. En outre, vous évoluer le niveau de la présentation en raison de l’augmentation du volume des demandes entrantes du client.

Ce modèle d’application est utile dans les cas suivants :

- Vous voulez déplacer les applications d’entreprise à partir de plateformes locaux virtualisé pour le Machines virtuelles Azure.

- Vous voulez augmenter le niveau de la présentation en raison de l’augmentation du volume des demandes entrantes du client.

- Vous souhaitez rapidement mise en service de développement et tester les environnements de courtes périodes.

- Vous voulez effectuer le test pour différents niveaux de charge de travail de contrainte, mais en même temps que vous ne souhaitez pas posséder et gérer plusieurs machines physiques tout le temps.

- Vous voulez qu’il possède un environnement d’infrastructure pouvant évoluer monter et descendre à la demande.

Le diagramme suivant illustre comment vous pouvez placer les niveaux de l’application sur plusieurs machines virtuelles dans Azure en faisant évoluer le niveau de la présentation en raison de l’augmentation du volume des demandes entrantes du client. Comme indiqué dans le diagramme, équilibrage de charge Azure est responsable de la distribution du trafic sur plusieurs machines virtuelles et également déterminer quel serveur web pour vous connecter à. Plusieurs instances des serveurs web derrière un équilibrage de charge permet de garantir la disponibilité de la couche de présentation.

![Modèle d’application - présentation couche mise à l’échelle](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728010.png)

### <a name="best-practices-for-2-tier-3-tier-or-n-tier-patterns-that-have-multiple-vms-in-one-tier"></a>Meilleures pratiques pour les modèles de niveau 2, niveau 3 ou multicouches comportant plusieurs ordinateurs virtuels un niveau

Il est recommandé de placer les machines virtuelles qui appartiennent au même niveau dans le même service cloud, dans la même la disponibilité définir. Par exemple, placez un ensemble de serveurs web dans **CloudService1** et **AvailabilitySet1** et un ensemble de serveurs de base de données dans **CloudService2** et **AvailabilitySet2**. Une disponibilité définie dans Azure vous permet de placer les nœuds haute disponibilité dans domaines d’erreur distincte et mise à niveau de domaines.

Pour tirer parti de plusieurs instances de machine virtuelle d’un niveau, vous devez configurer équilibrage de charge Azure entre les niveaux de l’application. Pour configurer l’équilibrage de charge de chaque niveau, créez un point de terminaison équilibrage de charge sur machines virtuelles de chaque niveau séparément. Pour un niveau spécifique, tout d’abord créer des ordinateurs virtuels dans le même service cloud. Ainsi qu’ils ont la même adresse IP virtuelle publique. Ensuite, créez un point de terminaison sur l’un des machines virtuelles à ce niveau. Ensuite, affectez au même point de terminaison aux autres machines virtuelles à ce niveau pour l’équilibrage de charge. En créant un jeu d’équilibrage de charge, vous répartissez le trafic entre plusieurs machines virtuelles et permettent également l’équilibrage de charge déterminer le nœud pour vous connecter en cas d’échec de nœud machine virtuelle principal. Par exemple, la présence de plusieurs instances des serveurs web derrière un équilibrage de charge garantit la disponibilité de la couche de présentation.

Pour obtenir les meilleurs résultats, toujours s’assurer que toutes les connexions internet tout d’abord à la couche de présentation. La couche de présentation accède à la couche d’entreprise et le niveau d’entreprise accède ensuite à la couche de données. Pour plus d’informations sur la façon d’autoriser l’accès à la couche de présentation, voir [Autoriser l’accès externe à votre ordinateur virtuel à l’aide du portail Azure](virtual-machines-windows-nsg-quickstart-portal.md).

Notez que l’équilibrage de charge dans Azure fonctionne similaire pour charger des programmes d’équilibrage de dans un environnement local. Pour plus d’informations, voir [équilibrage de charge pour les services d’infrastructure Azure](virtual-machines-windows-load-balance.md).

En outre, nous vous recommandons de configurer un réseau privé pour vos machines virtuelles à l’aide de réseau virtuel Azure. Cela leur permet de communiquer entre eux via l’adresse IP privée. Pour plus d’informations, voir [Réseau virtuel Azure](../virtual-network/virtual-networks-overview.md).

## <a name="2-tier-and-3-tier-with-business-tier-scale-out"></a>2 et 3-niveaux avec business couche horizontale

Dans ce modèle d’application, vous déployez application 2 ou 3 niveaux de base de données pour le Machines virtuelles Azure en plaçant chaque niveau de l’application dans une machine virtuelle différente. En outre, vous souhaiterez peut-être distribuer les composants de serveur d’application à plusieurs machines virtuelles en raison de la complexité de votre application.

Ce modèle d’application est utile dans les cas suivants :

- Vous voulez déplacer les applications d’entreprise à partir de plateformes locaux virtualisé pour le Machines virtuelles Azure.

- Vous souhaitez distribuer les composants d’application serveur à plusieurs machines virtuelles en raison de la complexité de votre application.

- Vous voulez déplacer lourds de logique métier locale métier (de-) applications professionnelles pour le Machines virtuelles Azure. Les applications métiers sont un ensemble d’applications critiques ordinateur qui sont essentiels à l’exécution d’une entreprise, tels que comptabilité des ressources humaines (RH), paie, gestion de la chaîne d’approvisionnement et les applications de planification des ressources.

- Vous souhaitez rapidement mise en service de développement et tester les environnements de courtes périodes.

- Vous voulez effectuer le test pour différents niveaux de charge de travail de contrainte, mais en même temps que vous ne souhaitez pas posséder et gérer plusieurs machines physiques tout le temps.

- Vous voulez qu’il possède un environnement d’infrastructure pouvant évoluer monter et descendre à la demande.

Le diagramme suivant illustre d’un scénario local et sa solution cloud activé. Dans ce scénario, vous placez les niveaux de l’application sur plusieurs machines virtuelles dans Azure en faisant évoluer le niveau d’entreprise, qui contient le niveau de la logique métier et les composants d’accès aux données. Comme indiqué dans le diagramme, équilibrage de charge Azure est responsable de la distribution du trafic sur plusieurs machines virtuelles et également déterminer quel serveur web pour vous connecter à. Plusieurs instances des serveurs d’applications derrière un équilibrage de charge permet de garantir la disponibilité de la couche d’entreprise. Pour plus d’informations, voir [meilleures pratiques pour la couche 2, 3-niveaux ou modèles d’application multicouches qui ont plusieurs machines virtuelles dans un niveau](#best-practices-for-2-tier-3-tier-or-n-tier-patterns-that-have-multiple-vms-in-one-tier).

![Modèle d’application avec une échelle de niveau entreprise arrière](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728011.png)

## <a name="2-tier-and-3-tier-with-presentation-and-business-tiers-scale-out-and-hadr"></a>2-3 couche et présentation et entreprise niveaux horizontale et HADR

Dans ce modèle d’application, vous déployez application 2 ou 3 niveaux de base de données pour le Machines virtuelles Azure à distribuer la couche de présentation (serveur web) et les composants métier couche (serveur d’application) à plusieurs machines virtuelles. En outre, vous implémentez des solutions et la disponibilité et de reprise pour vos bases de données dans des machines virtuelles Azure.

Ce modèle d’application est utile dans les cas suivants :

- Vous souhaitez déplacer des applications d’entreprise plateformes virtualisé locales vers Azure en mettant en œuvre SQL Server haute disponibilité et reprise.

- Vous voulez évoluer la couche de présentation et la couche d’entreprise en raison d’augmentation du volume des demandes des clients et la complexité de votre application.

- Vous souhaitez rapidement mise en service de développement et tester les environnements de courtes périodes.

- Vous voulez effectuer le test pour différents niveaux de charge de travail de contrainte, mais en même temps que vous ne souhaitez pas posséder et gérer plusieurs machines physiques tout le temps.

- Vous voulez qu’il possède un environnement d’infrastructure pouvant évoluer monter et descendre à la demande.

Le diagramme suivant illustre d’un scénario local et sa solution cloud activé. Dans ce scénario, vous évoluer la couche de présentation et les composants des couches business sur plusieurs machines virtuelles dans Azure. En outre, vous implémentez haute disponibilité et urgence les techniques de récupération (HADR) pour les bases de données SQL Server dans Azure.

Plusieurs copies d’une application en cours d’exécution dans différentes machines virtuelles Assurez-vous que vous êtes demandes équilibrage entre eux. Lorsque vous avez plusieurs machines virtuelles, vous devez vous assurer que vos ordinateurs virtuels sont accessibles et en cours d’exécution à un moment donné dans le temps. Si vous configurez l’équilibrage de charge, équilibrage de charge Azure effectue le suivi de l’état de machines virtuelles et dirige les appels entrants vers les nœuds de machine virtuelle exact qu’il fonctionne correctement. Pour plus d’informations sur la configuration de l’équilibrage de charge des machines virtuelles, consultez [services d’Azure infrastructure équilibrage de la charge](virtual-machines-windows-load-balance.md). Plusieurs instances de serveurs web et d’application derrière un équilibrage de charge permet de garantir la disponibilité des présentation et les niveaux métier.

![Horizontale et haute disponibilité](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728012.png)

### <a name="best-practices-for-application-patterns-requiring-sql-hadr"></a>Meilleures pratiques pour les modèles d’application pour demander une SQL HADR

Lorsque vous définissez la disponibilité de SQL Server et les solutions de reprise dans le Machines virtuelles Azure, la configuration d’un réseau virtuel pour vos machines virtuelles à l’aide de [Réseau virtuel Azure](../virtual-network/virtual-networks-overview.md) est obligatoire.  Machines virtuelles au sein d’un réseau virtuel ont une adresse IP privée stable même après une interruption de service, par conséquent, vous pouvez éviter le temps de mise à jour nécessaire pour la résolution de noms DNS. En outre, le réseau virtuel vous permet d’étendre votre réseau local vers Azure et crée une limite de sécurité approuvé. Par exemple, si votre application a des restrictions de domaine d’entreprise (par exemple, l’authentification Windows, Active Directory), la configuration [Réseau virtuel Azure](../virtual-network/virtual-networks-overview.md) est nécessaire.

La plupart des clients, qui exécutent du code production sur Azure, conservez réplicas principales et secondaires dans Azure.

Pour obtenir des informations complètes et didacticiels sur la disponibilité et les techniques de récupération d’urgence, voir [disponibilité et reprise pour SQL Server dans le Machines virtuelles Azure](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="2-tier-and-3-tier-using-azure-vms-and-cloud-services"></a>2-3 couche et à l’aide de machines virtuelles Azure et Services de Cloud

Dans ce modèle d’application, vous déployez application 2 ou 3 niveaux sur Azure à l’aide de ces deux [Services Cloud Azure](../cloud-services/cloud-services-choose-me.md#tellmecs) (rôles web et collaborateur - plateforme en tant que Service (PaaS)) et [Machines virtuelles Azure](virtual-machines-windows-about.md) (Infrastructure en tant que Service (IaaS)). Utilisation [d’Azure Cloud Services](https://azure.microsoft.com/documentation/services/cloud-services/) pour la couche métier/de couche de présentation et SQL Server dans des [Machines virtuelles Azure](virtual-machines-windows-about.md) pour la couche de données est utile pour la plupart des applications qui s’exécutent sur Azure. La raison est que disposer d’une instance cluster s’exécutant sur les Services en nuage fournit une gestion facile, déploiement, la surveillance et horizontale.

Avec les Services Cloud, Azure gère l’infrastructure pour vous, effectue la maintenance de routine, correctifs les systèmes d’exploitation et essaie de restaurer à partir des échecs de service et le matériel. Lorsque votre application doit horizontale, options horizontale manuelles et automatiques sont disponibles pour votre projet de service cloud en augmenter ou diminuer le nombre d’instances ou machines virtuelles qui sont utilisés par votre application. En outre, vous pouvez utiliser Visual Studio en local pour déployer votre application dans un projet de service cloud dans Azure.

En résumé, si vous ne voulez pas approfondies hiérarchiser les tâches administratives présentation/entreprise et votre application ne pas exiger une configuration complexe du logiciel ou le système d’exploitation, utiliser les Services en nuage Azure. Si la base de données SQL Azure ne prend pas en charge toutes les fonctionnalités que vous recherchez, utilisez SQL Server dans une Machine virtuelle Azure pour la couche données. Exécution d’une application sur Azure Cloud Services et le stockage de données dans le Machines virtuelles Azure combinant les avantages des services. Pour une comparaison détaillée, consultez la section dans cette rubrique sous [stratégies de développement de comparaison dans Azure](#comparing-web-development-strategies-in-azure).

Dans ce modèle d’application, la couche de présentation inclut un rôle web, qui est un composant de Services Cloud en cours d’exécution dans l’environnement d’exécution Azure et il est personnalisé pour la programmation d’applications web pris en charge par IIS et ASP.NET. Le niveau d’entreprise ou serveur principal comprend un rôle de collaborateur, qui est un composant de Services Cloud en cours d’exécution dans l’environnement d’exécution Azure et il est utile pour le développement GRG et peut effectuer le traitement en arrière-plan pour un rôle web. Le niveau de base de données se trouve dans une machine virtuelle SQL Server dans Azure. La communication entre la couche de présentation et la couche de base de données s’effectue directement ou le contrôle de la couche d’entreprise – composants de rôle de collaborateur.

Ce modèle d’application est utile dans les cas suivants :

- Vous souhaitez déplacer des applications d’entreprise plateformes virtualisé locales vers Azure en mettant en œuvre SQL Server haute disponibilité et reprise.

- Vous voulez qu’il possède un environnement d’infrastructure pouvant évoluer monter et descendre à la demande.

- Base de données SQL Azure ne prend pas en charge toutes les fonctionnalités qui a besoin de votre application ou une base de données.

- Vous voulez effectuer le test pour différents niveaux de charge de travail de contrainte, mais en même temps que vous ne souhaitez pas posséder et gérer plusieurs machines physiques tout le temps.

Le diagramme suivant illustre d’un scénario local et sa solution cloud activé. Dans ce scénario, vous placez le niveau de la présentation dans rôles web, le niveau d’entreprise dans les rôles de travail, mais la couche de données dans des machines virtuelles dans Azure. Plusieurs copies de la couche de présentation en cours d’exécution dans des rôles différents web garantit pour charger des demandes de solde entre eux. Lorsque vous combinez Azure Cloud Services avec le Machines virtuelles Azure, nous vous recommandons de configurer [Réseau virtuel Azure](../virtual-network/virtual-networks-overview.md) ainsi. Avec [Réseau virtuel Azure](../virtual-network/virtual-networks-overview.md), vous pouvez avoir stables et permanentes adresses IP privées au sein du même service cloud dans le cloud. Une fois que vous définissez un réseau virtuel pour vos machines virtuelles et services en nuage, qu’ils puissent commencer à communiquer entre eux sur l’adresse IP privée. En outre, ayant des machines virtuelles et rôles Azure web/travail dans le même [Réseau virtuel Azure](../virtual-network/virtual-networks-overview.md) fournit une latence faible et la connectivité plus sécurisée. Pour plus d’informations, voir [qu’est un service cloud](../cloud-services/cloud-services-choose-me.md).

Comme indiqué dans le diagramme, équilibrage de charge Azure distribue le trafic sur plusieurs machines virtuelles et également détermine quelle serveur web ou un serveur d’applications pour vous connecter à. Plusieurs instances des serveurs web et d’application derrière un équilibrage de charge permet de garantir la disponibilité de la couche de présentation et le niveau d’entreprise. Pour plus d’informations, voir [meilleures pratiques pour les modèles d’application pour demander une HADR SQL](#best-practices-for-application-patterns-requiring-sql-hadr).

![Modèles d’application avec les Services Cloud](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728013.png)

Une autre approche implémenter ce modèle d’application consiste à utiliser un rôle web consolidée qui contient le niveau de la présentation et les composants métier couche comme le montre l’illustration suivante. Ce modèle d’application est utile pour les applications qui nécessitent de conception avec état. Dans la mesure où Azure fournit des nœuds de calcul sans état sur les rôles web et de travail, nous vous recommandons de mise en œuvre d’une logique pour stocker l’état de session en utilisant l’une des technologies suivantes : [Mise en cache Azure](https://azure.microsoft.com/documentation/services/redis-cache/), de [Stockage de Table Azure](../storage/storage-dotnet-how-to-use-tables.md) ou de [Base de données SQL Azure](../sql-database/sql-database-technical-overview.md).

![Modèles d’application avec les Services Cloud](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728014.png)

## <a name="pattern-with-azure-vms-azure-sql-database-and-azure-app-service-web-apps"></a>Modèle avec machines virtuelles Azure et base de données SQL Azure Azure Application Service (applications Web)

L’objectif principal de ce modèle d’application consiste à vous montrer comment combiner Azure infrastructure comme un composants de service (IaaS) avec des composants de plateforme en tant que service (PaaS) Azure dans votre solution. Ce modèle se concentre sur la base de données SQL Azure pour le stockage de données relationnelles. Il n’inclut pas SQL Server sur une machine virtuelle Azure, qui fait partie de l’infrastructure Azure comme une offre de service.

Dans ce modèle d’application, vous déployez une application de base de données vers Azure en plaçant les couches de présentation et d’entreprise dans la même machine virtuelle et accéder à une base de données dans les serveurs de base de données SQL Azure (base de données SQL). Vous pouvez implémenter le niveau de la présentation à l’aide de solutions de site web IIS traditionnel. Ou bien, vous pouvez implémenter un présentation combinée et un niveau d’entreprise en utilisant [Azure Web Apps](https://azure.microsoft.com/documentation/services/app-service/web/).

Ce modèle d’application est utile dans les cas suivants :

- Vous disposez déjà d’un serveur de base de données SQL existant configuré dans Azure et que vous voulez tester rapidement votre application.

- Vous souhaitez tester les fonctionnalités d’un environnement Azure.

- Vous souhaitez rapidement mise en service de développement et tester les environnements de courtes périodes.

- Vos composants métier logique et les données access peuvent être autonomes dans une application web.

Le diagramme suivant illustre d’un scénario local et sa solution cloud activé. Dans ce scénario, vous placez les niveaux de l’application dans une seule machine virtuelle dans Azure et accès aux données dans la base de données SQL Azure.

![Modèle d’application mixte](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728015.png)

Si vous choisissez d’implémenter un web et par la couche application à l’aide de Azure Web Apps, nous vous recommandons de conserver la couche intermédiaire ou de l’application en tant que bibliothèques de liens dynamiques (DLL) dans le contexte d’une application web.

En outre, passez en revue les recommandations fournies dans la section de [comparaison des stratégies de développement web dans Azure](#comparing-web-development-strategies-in-azure) à la fin de cet article pour en savoir plus sur les techniques de programmation.

## <a name="n-tier-hybrid-application-pattern"></a>Modèle d’application multicouches hybride

Dans le modèle d’application multicouches hybride, vous implémentez votre application dans plusieurs niveaux distribué entre en local et Azure. Par conséquent, vous créez un système hybride flexible et réutilisable, que vous pouvez modifier ou ajouter un niveau spécifique sans modifier les autres niveaux. Pour étendre votre réseau d’entreprise dans le cloud, vous utilisez service [Réseau virtuel Azure](../virtual-network/virtual-networks-overview.md) .

Ce modèle d’application hybride est utile dans les cas suivants :

- Vous souhaitez créer des applications qui s’exécutent partiellement dans le cloud et partiellement en local.

- Vous voulez migrer certains ou tous les éléments d’une application locale existante dans le cloud.

- Vous voulez basculer vers Azure applications d’entreprise à partir de plateformes locaux virtualisé.

- Vous voulez qu’il possède un environnement d’infrastructure pouvant évoluer monter et descendre à la demande.

- Vous souhaitez rapidement mise en service de développement et tester les environnements de courtes périodes.

- Vous souhaitez un moyen rentable d’effectuer des sauvegardes pour les applications de base de données d’entreprise.

Le diagramme suivant illustre un modèle d’application multicouches hybride s’étend sur local et Azure. Comme indiqué dans le diagramme, infrastructure locale inclut contrôleur de domaine [Active Directory Domain Services](https://technet.microsoft.com/library/hh831484.aspx) pour prendre en charge l’autorisation et l’authentification des utilisateurs. Notez que le diagramme illustre un scénario, où certaines parties de la couche données live dans un centre de données locale alors que certaines parties de la couche données live dans Azure. En fonction des besoins de votre application, vous pouvez implémenter plusieurs autres scénarios hybride. Par exemple, vous pouvez conserver le niveau de présentation et la couche d’entreprise dans un environnement local, mais la couche données d’Azure.

![Modèle d’application multicouches](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728016.png)

Dans Azure, vous pouvez utiliser Active Directory comme un répertoire de cloud autonome pour votre organisation, ou vous pouvez également intégrer Active Directory existant en local avec [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/). Comme indiqué dans le diagramme, les composants de niveau entreprise peuvent y accéder à plusieurs sources de données, telles que [SQL Server dans Azure](virtual-machines-windows-sql-server-iaas-overview.md) via une adresse IP interne privée, à locale SQL Server via un [Réseau virtuel Azure](../virtual-network/virtual-networks-overview.md), ou à la [Base de données SQL](../sql-database/sql-database-technical-overview.md) à l’aide des technologies de fournisseur de données .NET Framework. Dans ce diagramme, base de données SQL Azure est un service de stockage de données facultatif.

Dans le modèle d’application multicouches hybride, vous pouvez implémenter le flux de travail suivant dans l’ordre indiqué :

1. Identifier les applications de base de données d’entreprise qui doivent être déplacé vers le haut, vers le cloud à l’aide de l' [évaluation de Microsoft et Kit de ressources de Planning (MAP)](http://microsoft.com/map). Le Kit de ressources de carte collecte des données de performance et des stocks à partir d’ordinateurs que vous envisagez de virtualisation et fournit des recommandations sur la capacité et planification de l’évaluation.

1. Planifiez les ressources et la configuration requise dans la plateforme Azure, tels que les comptes de stockage et des machines virtuelles.

1. Configurer la connectivité réseau entre le réseau d’entreprise en local et le [Réseau virtuel Azure](../virtual-network/virtual-networks-overview.md). Pour configurer la connexion entre le réseau d’entreprise en local et une machine virtuelle dans Azure, utilisez une des deux méthodes suivantes :

    1. Établir une connexion entre en local et Azure via des points de terminaison publics sur une machine virtuelle dans Azure. Cette méthode fournit un programme d’installation facile et vous permet d’utiliser l’authentification SQL Server sur votre ordinateur virtuel. En outre, configurez vos règles de groupe de sécurité de réseau pour contrôler le trafic public de la machine virtuelle. Pour plus d’informations, voir [Autoriser l’accès externe à votre ordinateur virtuel à l’aide du portail Azure](virtual-machines-windows-nsg-quickstart-portal.md).

    1. Établir une connexion entre en local et Azure via tunnel (VPN) de réseau privé virtuel Azure. Cette méthode vous permet d’étendre les stratégies de domaine pour une machine virtuelle dans Azure. En outre, vous pouvez configurer des règles de pare-feu et utiliser l’authentification Windows sur votre ordinateur virtuel. Pour l’instant, Azure prend en charge sécurisé VPN de site à connexions VPN point-à-site :

        - Avec une connexion sécurisée à un site, vous pouvez établir la connectivité réseau entre votre réseau local et votre réseau virtuel dans Azure. Il est recommandé de connexion de votre environnement de centre de données locale à Azure.

        - Avec une connexion sécurisée point-à-site, vous pouvez établir la connectivité réseau entre votre réseau virtuel dans Azure et vos ordinateurs individuels en cours d’exécution. Il est recommandé principalement à des fins de développement et test.

    Pour plus d’informations sur la façon de se connecter au serveur SQL Azure, voir [se connecter à un ordinateur de virtuelle SQL Server dans Azure](virtual-machines-windows-classic-sql-connect.md).

1. Configurer les tâches planifiées et les alertes de sauvegarder vos données en local sur un disque de machine virtuelle dans Azure. Pour plus d’informations, voir [SQL Server sauvegarde et restauration avec le Service de stockage Blob Azure](https://msdn.microsoft.com/library/jj919148.aspx) et [sauvegarde et restauration pour SQL Server dans le Machines virtuelles Azure](virtual-machines-windows-sql-backup-recovery.md).

1. En fonction des besoins de votre application, vous pouvez effectuer une des trois scénarios courants suivants :

    1. Vous pouvez conserver votre serveur web, serveur d’applications et données non sensibles dans un serveur de base de données dans Azure alors que vous conservez les données sensibles en local.

    1. Vous pouvez conserver votre serveur web et application serveur local alors que le serveur de base de données dans une machine virtuelle dans Azure.

    1. Vous pouvez conserver votre serveur de base de données, le serveur web, puis sur application serveur local alors que vous conservez les réplicas de base de données dans des machines virtuelles dans Azure. Ce paramètre permet les serveurs web locaux ou des applications de création de rapports à accéder aux répliques de base de données dans Azure. Par conséquent, vous pouvez obtenir pour réduire la charge de travail dans une base de données locale. Nous vous recommandons d’implémenter ce scénario pour lourds lire les charges de travail et à des fins de développement. Pour plus d’informations sur la création de duplications de base de données dans Azure, voir groupes de disponibilité AlwaysOn en [haute disponibilité et reprise pour SQL Server dans le Machines virtuelles Azure](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="comparing-web-development-strategies-in-azure"></a>Comparaison des stratégies de développement web dans Azure

Pour mettre en œuvre et déployer une application basée sur SQL Server dans Azure à plusieurs niveaux, vous pouvez utiliser une des deux méthodes de programmation suivantes :

- Configurer un serveur web traditionnel (IIS - Internet Information Services) dans les bases de données Azure et access dans SQL Server dans le Machines virtuelles Azure.

- Mettre en œuvre et déployer un service cloud sur Azure. Ensuite, assurez-vous que ce service cloud puisse accéder aux bases de données SQL Server dans machines virtuelles Azure. Un service cloud peut inclure plusieurs rôles web et de travail.

Le tableau suivant fournit une comparaison du développement web traditionnel avec Azure Cloud Services et Azure Web Apps en ce qui concerne SQL Server dans le Machines virtuelles Azure. La table comprend Azure Web Apps qu’il est possible d’utiliser les SQL Server dans la machine virtuelle Azure comme source de données pour les applications Web Azure via son adresse IP publique virtuelle ou son nom DNS.

||Développement web traditionnel dans le Machines virtuelles Azure|Services de cloud dans Azure|Hébergement Web Azure applications Web|
|---|---|---|---|
|**Migration des applications locales**|Applications existantes en tant que-est.|Les applications doivent rôles web et collaborateur.|Applications existantes en tant que-est mais adapté aux applications web autonome et les services web nécessitant extensibilité élevées rapide.|
|**Développement et déploiement**|Visual Studio, WebMatrix, Visual Web Developer, WebDeploy, FTP, TFS, Gestionnaire des services Internet, PowerShell.|Visual Studio, Azure SDK, TFS, PowerShell. Chaque service cloud comporte deux environnements sur lesquels vous pouvez déployer votre package de service et de configuration : intermédiaires et production. Vous pouvez déployer un service cloud à l’environnement intermédiaire à des tests avant de vous promouvoir en production.|Visual Studio, WebMatrix, Visual Web Developer, FTP, GIT, BitBucket, CodePlex, DropBox, GitHub, mercurienne, TFS, Web déployez, PowerShell.|
|**Installation et administration**|Vous êtes responsable pour les tâches administratives dans l’application, les données, les règles de pare-feu, réseau virtuel et système d’exploitation.|Vous êtes responsable pour les tâches administratives dans l’application, les données, les règles de pare-feu et réseau virtuel.|Vous êtes responsable pour les tâches administratives de l’application et les données uniquement.|
|**Disponibilité et récupération d’urgence (HADR)**|Nous vous recommandons de placer machines virtuelles dans le même jeu de disponibilité et dans le même service cloud. Tout en conservant vos ordinateurs virtuels dans le même ensemble de disponibilité permet d’Azure placer les nœuds haute disponibilité dans les domaines d’erreur distincte et mettre à niveau des domaines. De même, tout en conservant vos ordinateurs virtuels dans le service cloud même permet l’équilibrage de charge et machines virtuelles peuvent communiquer directement entre eux via le réseau local dans un centre de données Azure.<br/><br/>Vous êtes responsable de la mise en œuvre une grande disponibilité et reprise après sinistre pour SQL Server dans le Machines virtuelles Azure afin d’éviter les temps d’arrêt. Pour les technologies HADR prises en charge, voir [disponibilité et reprise pour SQL Server dans le Machines virtuelles Azure](virtual-machines-windows-sql-high-availability-dr.md).<br/><br/>Vous êtes responsable de la sauvegarde de vos propres données et des applications.<br/><br/>Azure pouvez déplacer vos machines virtuelles si l’ordinateur hôte dans le centre de données échoue en raison de problèmes de matériel. En outre, vous pouvez rencontrer des interruptions planifiées de votre ordinateur virtuel lorsque l’ordinateur hôte est mis à jour de sécurité ou logiciel mises à jour. Par conséquent, nous vous recommandons de maintenir au moins deux machines virtuelles dans chaque niveau d’application pour assurer la disponibilité continue. Azure ne fournit pas SLA pour une seule machine virtuelle. Pour plus d’informations, voir [conseils techniques résilience Azure](../resiliency/resiliency-technical-guidance.md).|Azure gère les échecs résultant à partir du logiciel de matériel ou de système d’exploitation sous-jacent. Nous vous recommandons d’implémenter plusieurs instances d’un rôle web ou de travail pour vous assurer la disponibilité de votre application. Pour plus d’informations, voir [les Services en nuage, Machines virtuelles et virtuel contrat de niveau de Service de réseau](http://www.microsoft.com/download/details.aspx?id=38427) et [sinistre et disponibilité des applications Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)<br/><br/>Vous êtes responsable de la sauvegarde de vos propres données et des applications.<br/><br/>Pour les bases de données qui résident dans une base de données SQL Server dans une machine virtuelle Azure, vous êtes responsable de l’implémentation d’une solution récupération disponibilité et urgence élevée pour éviter les temps d’arrêt. Pour les technologies HDAR prises en charge, voir disponibilité et reprise pour SQL Server dans le Machines virtuelles Azure.<br/><br/>**Miroir de base de données SQL Server**: utilisation avec les Services de Cloud Azure (rôles web/travail). Machines virtuelles SQL Server et un projet de service cloud peuvent se trouver dans le même réseau virtuel Azure. Si machine virtuelle SQL Server n’est pas dans le même réseau virtuel, vous devez créer un Alias de SQL Server pour acheminer les communications vers l’instance de SQL Server. En outre, le nom d’alias doit correspondre au nom de SQL Server.|Disponibilité héritée de travail Azure rôles, stockage d’objets blob Azure et base de données SQL Azure. Par exemple, le stockage Azure gère trois répliques de tous les objets blob, table et données file d’attente. À un moment donné, base de données SQL Azure conserve trois répliques des données en cours d’exécution : un réplica principal et deux réplicas secondaires. Pour plus d’informations, voir [Le stockage Azure](https://azure.microsoft.com/documentation/services/storage/) et [Base de données SQL Azure](../sql-database/sql-database-technical-overview.md).<br/><br/>Lorsque vous utilisez SQL Server dans la machine virtuelle Azure comme source de données pour les applications Web Azure, n’oubliez pas que Azure Web Apps ne prend pas en charge réseau virtuel Azure. En d’autres termes, toutes les connexions à partir d’Azure Web Apps aux ordinateurs virtuels SQL Server Azure doivent traitée publics points de terminaison de machines virtuelles. Cela peut provoquer des limitations de disponibilité et récupération d’urgence. Par exemple, l’application cliente dans des applications Web Azure se connecter à machine virtuelle SQL Server avec la mise en miroir de base de données ne serait pas vous connecter à nouveau serveur primaire comme la mise en miroir de base de données nécessite que vous configuriez réseau virtuel Azure entre machines virtuelles hôte de SQL Server dans Azure. Par conséquent, à l’aide de **Miroir de base de données SQL Server** avec Azure Web Apps n'est pas pris en charge actuellement.<br/><br/>**Groupes de disponibilité AlwaysOn de SQL Server**: vous pouvez définir des groupes de disponibilité AlwaysOn lorsque vous utilisez Azure Web Apps avec machines de virtuelles SQL Server dans Azure. Mais vous avez besoin configurer un récepteur de groupe de disponibilité AlwaysOn pour acheminer les communications vers réplica principal via les points de terminaison équilibrage publics.|
|**Connectivité entre les différents locaux**|Vous pouvez utiliser le réseau virtuel Azure pour vous connecter à local.|Vous pouvez utiliser le réseau virtuel Azure pour vous connecter à local.|Réseau virtuel Azure est pris en charge. Pour plus d’informations, voir [Applications virtuel Web l’intégration des réseaux](https://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/).|
|**Extensibilité élevées**|Haut de l’échelle n’est disponible en augmentant les tailles machine virtuelle ou en ajoutant des disques. Pour plus d’informations sur les tailles de machine virtuelle, voir [Tailles Machine virtuelle pour Azure](virtual-machines-windows-sizes.md).<br/><br/>**Pour le serveur de base de données**: horizontale est disponible via techniques partition de base de données et des groupes de disponibilité AlwaysOn de SQL Server.<br/><br/>Pour les charges de lecture, vous pouvez utiliser des [Groupes de disponibilité AlwaysOn](https://msdn.microsoft.com/library/hh510230.aspx) sur plusieurs nœuds secondaires, ainsi que la réplication SQL Server.<br/><br/>Pour les charges de travail en écriture, vous pouvez implémenter données partition horizontales entre plusieurs serveurs physiques pour fournir application horizontale.<br/><br/>En outre, vous pouvez implémenter une horizontale à l’aide de [SQL Server avec routage dépendant des données](https://technet.microsoft.com/library/cc966448.aspx). Avec données dépendantes routage (TAD), vous devez mettre en œuvre le mécanisme partition dans l’application cliente, en général dans la couche d’entreprise, pour acheminer les requêtes de base de données à plusieurs nœuds de SQL Server. Le niveau métier contient les mappages sur la façon dont les données sont réparties et nœud qui contient les données.<br/><br/>Vous pouvez mettre à l’échelle applications qui s’exécutent machines virtuelles. Pour plus d’informations, voir [comment mettre à l’échelle d’une Application](../cloud-services/cloud-services-how-to-scale.md).<br/><br/>**Note importante**: la fonctionnalité **échelle** dans Azure permet d’augmenter ou diminuer les ordinateurs virtuels qui sont utilisés par votre application automatiquement. Cette fonctionnalité garantit que l’utilisateur final n’est pas affecté négatif pendant les périodes et machines virtuelles sont s’arrête lorsque la demande est faible. Il est recommandé que vous ne définissez pas l’option d’échelle pour votre service cloud s’il comprend machines virtuelles SQL Server. La raison pour laquelle est que la fonctionnalité échelle automatique vous permet de Azure pour désactiver une machine virtuelle lors de l’utilisation de l’UC dans cette dernière est supérieure à un seuil et pour désactiver une machine virtuelle lorsque l’utilisation du processeur est inférieure à celle-ci. La fonctionnalité échelle automatique est utile pour les applications sans état, tels que les serveurs web, où n’importe quel ordinateur virtuel peut gérer la charge de travail sans toutes les références à n’importe quel état précédent. Toutefois, la fonctionnalité d’échelle n’est pas utile pour les applications avec état, telles que SQL Server, où qu’une seule instance permet d’écriture dans la base de données.|En haut de l’échelle n’est disponible à l’aide de plusieurs rôles web et de travail. Pour plus d’informations sur les tailles de machine virtuelle pour les rôles web et les rôles de travail, voir [Configurer les tailles des Services en nuage](../cloud-services/cloud-services-sizes-specs.md).<br/><br/>Lorsque vous utilisez **Les Services en nuage**, vous pouvez définir plusieurs rôles pour distribuer le traitement et également assurer la mise à l’échelle flexibles de votre application. Chaque service cloud inclut un ou plusieurs rôles web et/ou rôles collaborateur, chacune avec ses propres fichiers d’application et de configuration. Vous pouvez échelle vers un service cloud en augmentant le nombre d’instances de rôle (machines virtuelles) déployé pour un rôle et échelle inférieurs un service cloud en réduisant le nombre d’instances de rôle. Pour plus d’informations, voir [Modèles d’exécution Azure](../cloud-services/cloud-services-choose-me.md).<br/><br/>Horizontale est disponible via Azure haute disponibilité intégrée prise en charge via [les Services en nuage, Machines virtuelles et virtuel contrat de niveau de Service de réseau](http://www.microsoft.com/download/details.aspx?id=38427) et équilibrage de charge.<br/><br/>Pour une application à plusieurs niveaux, nous vous recommandons de vous connecter application de rôles web/travail server machines virtuelles via réseau virtuel Azure la base de données. En outre, Azure fournit équilibrage de charge pour les machines virtuelles dans le même service cloud, répartition des demandes de l’utilisateur sur les. Machines virtuelles reliés de cette manière peuvent communiquer directement entre eux sur le réseau local dans un centre de données Azure.<br/><br/>Vous pouvez configurer **échelle** sur le portail classique Azure, ainsi que les heures de planification. Pour plus d’informations, voir [comment mettre à l’échelle d’une Application](../cloud-services/cloud-services-how-to-scale.md).|**Mettre à l’échelle monter et Descendre**: vous pouvez augmenter/diminuer la taille de l’instance (machine virtuelle) réservé pour votre site web.<br/><br/>Mise à l’échelle : vous pouvez ajouter des instances plus réservées (machines virtuelles) pour votre site web.<br/><br/>Vous pouvez configurer **échelle** sur le portail ainsi que les heures de planification. Pour plus d’informations, voir [comment échelle Web Apps](../app-service-web/web-sites-scale.md).|

Pour plus d’informations sur le choix entre ces deux méthodes de programmation, voir [Azure Web Apps, des Services de Cloud et machines virtuelles : quand utiliser qui](../app-service-web/choose-web-site-cloud-service-vm.md).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’exécution de SQL Server dans machines virtuelles Azure, voir [SQL Server dans la vue d’ensemble des Machines virtuelles Azure](virtual-machines-windows-sql-server-iaas-overview.md).
