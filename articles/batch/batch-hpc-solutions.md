<properties
   pageTitle="Solutions lot et HPC dans le cloud | Microsoft Azure"
   description="En savoir plus sur lot et les scénarios d’High performance computing (HPC et calculer volumineux) et les solutions proposées dans Azure"
   services="batch, virtual-machines, cloud-services"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""/>

<tags
   ms.service="batch"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="big-compute"
   ms.date="07/27/2016"
   ms.author="danlep"/>

# <a name="batch-and-hpc-solutions-in-the-azure-cloud"></a>Solutions lot et HPC dans le cloud Azure

Azure propose des solutions cloud efficace, fichiers par lots et High performance computing (HPC) - également appelées *Calculer volumineux*. Découvrez ici les charges de travail calculer volumineux et services d’Azure prenant en charge les, ou accédez directement aux [scénarios de solution](#scenarios) plus loin dans cet article. Cet article concerne principalement aux décideurs techniques, les responsables informatiques et les éditeurs de logiciels, mais d’autres professionnels de l’informatique et les développeurs peuvent l’utiliser pour se familiariser avec ces solutions.

Organisations ont des problèmes informatiques à grande échelle : ingénierie création et analyse, rendu d’image, modélisation complexes, simulations Monte Carlo, calculs financiers risque et d’autres personnes. Azure permet aux organisations de résoudre ces problèmes avec les ressources, échelle et planification que dont elles ont besoin. Avec Azure, les organisations peuvent :

* Créer des solutions hybride, extension un cluster HPC en local pour décharger les charges de pointe vers le cloud

* Exécuter les charges de travail et des outils de cluster HPC entièrement en Azure

* Services Azure scalable et gérées comme [lot](https://azure.microsoft.com/documentation/services/batch/) permet d’exécuter cluster accrue les charges de travail sans avoir à déployer et gérez l’infrastructure cluster

Bien que dépasse le cadre de cet article, Azure permet aux développeurs et partenaires un ensemble complet de fonctionnalités, les options d’architecture et outils de développement pour générer des flux de travail à grande échelle, personnalisé calculer volumineux. Et un réseau de partenaires croissant est prêt à vous aider à prendre vos charges de travail calculer volumineux productif dans le cloud Azure.


## <a name="batch-and-hpc-applications"></a>Applications lot et HPC

Contrairement aux web applications et applications métier de nombreux, lot et applications HPC ont un début défini et une fin, et ils peuvent s’exécuter sur une planification ou à la demande, parfois pendant les heures ou plus. La plupart il existe deux catégories principale : *intrinsèque parallèle* (parfois appelée « ces parallèle », car les problèmes ils résoudre eux-mêmes prêtent à l’exécution en parallèle sur plusieurs ordinateurs ou processeurs) et *étroitement*. Pour plus d’informations sur ces types d’applications, voir le tableau suivant. Une solution Azure approches fonctionnent mieux pour un seul type ou l’autre.

>[AZURE.NOTE] Dans les solutions lot et HPC, exécution d’une instance d’une application est généralement appelée un *projet*, et chaque projet peut-être obtenir divisé en *tâches*. Et les ressources cluster groupé d’application sont souvent appelées *nœuds de calcul*.

Type | Caractéristiques | Exemples
------------- | ----------- | ---------------
**Intrinsèque parallèle**<br/><br/>![Intrinsèque parallèle][parallel] |• Exécutés logique de l’application indépendamment des ordinateurs individuels<br/><br/> • Ajout ordinateurs permet à l’application à l’échelle et réduire le délai de calcul<br/><br/>• Application se compose d’exécutables séparés, ou est divisé en un groupe de services appelé par un client (SOA, application ou une architecture orientée services) |• La modélisation risque financier<br/><br/>• Rendu d’image et traitement d’image<br/><br/>• Le codage de média et transcodage<br/><br/>Simulations Monte Carlo •<br/><br/>Test de logiciels •
**Étroitement**<br/><br/>![Étroitement][coupled] |• Application nécessite des nœuds de calcul pour interagir ou échanger des résultats intermédiaires<br/><br/>• Cluster nœuds peuvent communiquer au moyen de la transmission des Interface MPI (Message), un protocole de communication courantes pour l’informatique parallèle<br/><br/>• L’application est sensible à la bande passante et la latence du réseau<br/><br/>• Performances de l’application peuvent être améliorées à l’aide des technologies réseau haut débit tels que InfiniBand et accès direct à la mémoire à distance (RDMA) |• Oil mises et gaz modélisation<br/><br/>• Ingénierie conception et l’analyse, tels que des fluides calcul<br/><br/>• Physique simulations tels que voiture se bloque et effets nucléaire<br/><br/>Prévisions météorologiques •

### <a name="considerations-for-running-batch-and-hpc-applications-in-the-cloud"></a>Considérations relatives à des commandes et applications HPC dans le cloud

Vous pouvez facilement migrer de nombreuses applications sont conçues pour s’exécuter en clusterisés en local pour Azure, ou pour un environnement hybride (cross locaux). Toutefois, il peut y avoir certaines limitations ou considérations, y compris :


* **Disponibilité des ressources de cloud** - selon le type de ressources de cluster cloud que vous utilisez, vous ne peut-être en mesure de s’appuient sur la disponibilité de la machine continue pendant l’exécution d’une tâche. L’avancement et gestion d’état vérifient pointant courantes techniques de gestion des défaillances transitoires possibles et plus nécessaire lors de l’utilisation des ressources de cloud.


* **Accès aux données** - techniques d’accès aux données généralement disponibles dans les clusters d’entreprise, tels que NFS, peuvent nécessiter une configuration spéciale dans le cloud. Ou bien, vous devrez peut-être adopter différentes données access pratiques et les motifs par le cloud.

* **Déplacement de données** - pour les applications qui traitent de grandes quantités de données, stratégies sont nécessaires pour déplacer les données dans le stockage cloud et ressources de calcul. Vous devrez peut-être haut débit locaux croisée mise en réseau tels que [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/). Envisagez également juridiques, réglementaires ou les limites de la politique de stockage ou accéder à ces données.


* **Gestion des licences** - contactez le fournisseur de n’importe quelle application commerciale pour les restrictions de licences ou toute autre pour en cours d’exécution dans le cloud. Pas tous les fournisseurs offrent des licences de paiement. Vous devrez peut-être planifier un serveur de licences dans le cloud pour votre solution ou se connecter à un serveur de licences en local.


### <a name="big-compute-or-big-data"></a>Grand cluster ou données volumineuses ?

La ligne de séparation entre les applications calculer volumineux et Big Data n’est pas toujours claire et certaines applications peuvent avoir des caractéristiques des deux. Les deux impliquent l’exécution de calculs à grande échelle, généralement sur groupes d’ordinateurs. Mais la solution approches et outils d’assistance peuvent différer.

• **Calculer volumineux** tendent à impliquent des applications qui dépendent de la puissance de l’UC et la mémoire, tels que simulations ingénieries risque financier modélisation et rendu numérique. L’infrastructure pour une solution calculer Big peut-être inclure des ordinateurs avec processeurs voie spécialisées pour effectuer le calcul brute et spécialisé, haut débit matériel réseau pour connecter les ordinateurs.

• **Big Data** résoudre les problèmes d’analyse de données qui impliquent de grandes quantités de données qui ne peuvent pas être gérées par un système de gestion ordinateur ou une autre base de données unique. Les exemples sont des volumes importants de journaux web ou d’autres données business intelligence. Données volumineuses a tendance à se baser davantage sur disque et des performances d’e/s à puissance de l’UC. Il existe également des outils Big Data spécialisés tels que Hadoop Apache pour gérer le cluster et partition les données. (Pour plus d’informations sur Azure HDInsight et d’autres solutions Azure Hadoop, voir [Hadoop](https://azure.microsoft.com/solutions/hadoop/).)

## <a name="compute-management-and-job-scheduling"></a>Calculer la gestion et planification des tâches

Exécution des applications lot ou HPC souvent comprend un *Gestionnaire de cluster* et un *Planificateur de tâches* pour aider à gérer les ressources de cluster groupé et les affecter aux applications qui exécutent les tâches. Ces fonctions peuvent être exécutées par outils distincts, ou un outil intégré ou un service.

* **Gestionnaire de cluster** - dispositions, libère et gère ressources de calcul (ou les nœuds de calcul). Un gestionnaire de cluster peut-être automatiser le processus d’installation d’images de système d’exploitation et applications sur les nœuds de calcul, mettre à l’échelle des ressources de cluster en fonction des besoins et surveiller les performances des nœuds.

* **Planificateur de tâches** - spécifie les ressources (par exemple, processeurs ou de mémoire) une application besoins, ainsi que les conditions lorsqu’il s’exécute. Un planificateur conserve une file d’attente de travaux et attribue des ressources à ces derniers basés sur une priorité attribué ou d’autres caractéristiques.

Cluster et outils pour Windows et Linux clusters la planification des tâches peut migrer bien à Azure. Par exemple, [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029), solution de cluster de calcul gratuites de Microsoft pour Windows et Linux HPC charges de travail, propose plusieurs options pour exécuter dans Azure. Vous pouvez également créer les clusters Linux pour exécuter les outils d’open source tels que couple et SLURM. Vous pouvez également apporter des solutions de grille professionnelle à Azure, tels que [TIBCO DataSynapse GridServer](http://www.tibco.com/company/news/releases/2016/tibco-to-accelerate-cloud-adoption-of-banking-and-capital-markets-customers-via-microsoft-collaboration), [Symphony IBM](http://www-01.ibm.com/support/docview.wss?uid=isg3T1023592)et [Moteur de grille Univa](http://www.univa.com/products/grid-engine).

Comme indiqué dans les sections suivantes, vous pouvez également tirer parti des services Azure pour gérer les ressources de calcul et planifier les outils de gestion des travaux sans (ou en plus de) cluster traditionnel.


## <a name="scenarios"></a>Scénarios

Voici trois scénarios courants pour exécuter volumineux calculer les charges de travail dans Azure à l’aide des solutions de cluster HPC existantes, services Azure ou une combinaison des deux. Considérations relatives à la clé pour le choix de chaque scénario sont répertoriés, mais ne sont pas exhaustive. Plus sur les services disponibles Azure, que vous pouvez utiliser dans votre solution sont plus loin dans l’article.

  | Scénario | Pourquoi choisir il ?
------------- | ----------- | ---------------
**Burst un cluster HPC vers Azure**<br/><br/>[! [Cluster rupture] [burst_cluster]](./media/batch-hpc-solutions/burst_cluster.png) <br/><br/> Pour en savoir plus :<br/>• [Burst sur des instances de travail Azure avec HPC Pack](https://technet.microsoft.com/library/gg481749.aspx)<br/><br/>• [Configurer un déploiement calculer cluster avec HPC Pack](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)<br/><br/>• [Burst au lot Azure avec HPC Pack](https://technet.microsoft.com/library/mt612877.aspx)<br/><br/>|• Combiner votre [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) ou un autre cluster en local avec d’autres ressources Azure dans une solution hybride.<br/><br/>• Étendre vos charges de travail calculer volumineux pour s’exécuter sur plate-forme en tant que Service (PaaS) machine virtuelle instances (actuellement Windows Server uniquement).<br/><br/>• Accéder à un magasin local licence server ou des données à l’aide d’un réseau virtuel Azure facultatif|• Vous avez un cluster HPC existant et que vous avez besoin de davantage de ressources <br/><br/>• Vous ne voulez pas acheter et gérer une infrastructure cluster HPC supplémentaire<br/><br/>• Vous avez transitoires périodes de pointe à la demande ou projets spéciaux
**Créer un cluster HPC entièrement en Azure**<br/><br/>[! [Cluster dans IaaS] [iaas_cluster]](./media/batch-hpc-solutions/iaas_cluster.png)<br/><br/>Pour en savoir plus :<br/>• [Solutions de cluster HPC dans Azure](./big-compute-resources.md)<br/><br/>|• Rapidement et de manière cohérente déployer vos applications et outils cluster sur infrastructure Windows ou Linux standard ou personnalisé en tant qu’un machines virtuelles service (IaaS).<br/><br/>• Exécuter diverses charges de travail calculer volumineux à l’aide de la solution de votre choix de la planification des tâches.<br/><br/>• Utilisez des services Azure supplémentaires, y compris de réseau et stockage pour créer des solutions complètes sur le nuage. |• Vous ne voulez pas acheter et gérer une infrastructure cluster Linux ou Windows HPC supplémentaire<br/><br/>• Vous avez transitoires périodes de pointe à la demande ou projets spéciaux<br/><br/>• Vous avez besoin d’un autre cluster pour une fois, mais ne voulez pas investir dans l’espace à déployer et des ordinateurs<br/><br/>• Vous souhaitez alléger la charge votre application cluster accrue pour qu’il fonctionne comme un service entièrement dans le cloud
**Déployer une application parallèle vers Azure**<br/><br/>[! [Lot azure] [batch_proc]](./media/batch-hpc-solutions/batch_proc.png)<br/><br/>Pour en savoir plus :<br/>• [Notions de base du lot Azure](./batch-technical-overview.md)<br/><br/>• [Prise en main la bibliothèque lot Azure pour .NET](./batch-dotnet-get-started.md)|• Développer avec [Lot Azure](https://azure.microsoft.com/documentation/services/batch/) à l’échelle des tâches variées calculer volumineux pour s’exécuter sur des pools de machines virtuelles Windows ou Linux.<br/><br/>• Utilisez un service plateforme Azure pour gérer le déploiement et autoscaling de machines virtuelles, planification des tâches, sinistre, déplacement de données, la gestion des dépendances et déploiement de l’application.|• Vous ne voulez pas gérer calculer des ressources ou un planificateur de tâches ; à la place, vous souhaitez vous concentrer sur vos applications<br/><br/>• Vous souhaitez alléger la charge votre application cluster accrue pour qu’il fonctionne comme un service dans le cloud<br/><br/>• Vous souhaitez ajuster automatiquement les ressources de votre cluster pour correspondre à la charge de travail cluster


## <a name="azure-services-for-big-compute"></a>Services Azure pour calculer volumineux

Voici des informations supplémentaires sur le calcul, les données, mise en réseau et vous pouvez combiner les services connexes pour calculer Big solutions et flux de travail. Pour obtenir des instructions détaillées sur les services Azure, consultez la [documentation](https://azure.microsoft.com/documentation/)de services Azure. Les [scénarios](#scenarios) plus haut dans cet article Afficher seulement quelques exemples d’utilisation de ces services.

>[AZURE.NOTE] Azure présente régulièrement les nouveaux services qui peuvent s’avérer utiles pour votre scénario. Si vous avez des questions, contactez un [partenaire Azure](https://pinpoint.microsoft.com/en-US/search?keyword=azure) ou messagerie *bigcompute@microsoft.com*.

### <a name="compute-services"></a>Calculer des services

Azure cluster services constituent un élément essentiel d’une solution calculer volumineux et les avantages cluster différents services offre pour différents scénarios. Un niveau de base, ces services offrent différents modes pour les applications de s’exécuter sur des instances de cluster basées sur machine virtuelle Azure fournit à l’aide de la technologie Windows Server Hyper-V. Ces instances peuvent s’exécuter standards et personnalisées outils et les systèmes d’exploitation Linux et Windows. Azure vous donne un choix de [tailles instance](../virtual-machines/virtual-machines-windows-sizes.md) avec des configurations différentes de cœurs du processeur, la mémoire, capacité de disque et autres caractéristiques. Selon vos besoins, vous pouvez mettre à l’échelle les instances à des milliers de cœurs et puis échelle lorsque vous avez besoin de moins de ressources.

>[AZURE.NOTE] Tirer parti des instances cluster accrue Azure pour améliorer les performances et extensibilité élevées de charges de travail HPC, y compris des applications MPI parallèles nécessitant un haut débit application réseau et latence faible. Voir [à propos des machines virtuelles A série H-série et cluster accrue](../virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md).  

Service | Description
------------- | -----------
**[Machines virtuelles](https://azure.microsoft.com/documentation/services/virtual-machines/)**<br/><br/> |• Fournir infrastructure de calcul en tant que service (IaaS) à l’aide de la technologie Microsoft Hyper-V<br/><br/>• Vous permettre de flexible mise en service et la gestion des ordinateurs cloud permanente à partir d’images de Windows Server ou Linux standards à partir d' [Azure Marketplace](https://azure.microsoft.com/marketplace/), ou des images et des disques de données fournis<br/><br/>• Peuvent être déployés et gérés en tant que [Machine virtuelle échelle jeux](https://azure.microsoft.com/documentation/services/virtual-machine-scale-sets/) pour générer des services à grande échelle à partir des machines virtuelles identiques, avec autoscaling pour augmenter ou diminuer la capacité automatiquement<br/><br/>• Exécuté en local calculer cluster outils et applications entièrement dans le cloud<br/><br/>
**[Services en nuage](https://azure.microsoft.com/documentation/services/cloud-services/)**<br/><br/> |• Pouvant exécuter des applications calculer volumineux dans collaborateur instances de rôle, qui sont machines virtuelles exécute une version de Windows Server et qui sont entièrement gérés par Azure<br/><br/>• Permettent aux applications de format SVG, fiables avec faible surcharge administrative, en cours d’exécution dans une plateforme, tel qu’un modèle de service (PaaS)<br/><br/>• Peuvent nécessiter des outils supplémentaires ou le développement d’intégrer les solutions de cluster HPC en local
**[Traitement par lots](https://azure.microsoft.com/documentation/services/batch/)**<br/><br/> |• S’exécute les charges de travail parallèle et de la feuille à grande échelle dans un service entièrement géré<br/><br/>• Fournit la planification des tâches et autoscaling d’un pool de machines virtuelles géré<br/><br/>• Permet aux développeurs de créer et exécuter des applications en tant que service ou activer le cloud des applications existantes<br/>

### <a name="storage-services"></a>Services de stockage

Une solution calculer volumineux généralement fonctionne sur un jeu de données d’entrée et génère des données de ses résultats. Parmi les services de stockage Azure utilisés dans les solutions volumineux calculer :

* [Blob, table et le stockage de file d’attente](https://azure.microsoft.com/documentation/services/storage/) - gérer de grandes quantités de données non structurées, les données de NoSQL et les messages de flux de travail et de communication, respectivement. Par exemple, vous pouvez utiliser stockage d’objets blob pour les grands ensembles de données techniques, ou pour les images d’entrée ou fichiers multimédias votre processus d’application. Vous pouvez utiliser des files d’attente pour les communications asynchrones dans une solution. Voir [Introduction au stockage de Microsoft Azure](../storage/storage-introduction.md).

* [Stockage de fichiers azure](https://azure.microsoft.com/services/storage/files/) - partage de fichiers communs et des données dans Azure à l’aide du protocole PME standard, qui est requis pour certaines solutions de cluster HPC.

* [Données Lake Store](https://azure.microsoft.com/services/data-lake-store/) - fournit un hyperscale Apache Hadoop Distributed File System pour le cloud, utile pour le lot, en temps réel et analytique interactifs.

### <a name="data-and-analysis-services"></a>Services et analyse des données

Certains scénarios calculer volumineux impliquant des flux de données de grande taille ou génèrent des données nécessitant un traitement approfondi ou analyse. Azure propose plusieurs services et analyse des données, y compris :

* [Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) - générations pilotées par les données des flux de travail (canalisations) cette jointure, agrégat et transformer les données à partir d’en local, sur le nuage et banques de données Internet.

* [Base de données SQL](https://azure.microsoft.com/documentation/services/sql-database/) - fournit les principales fonctionnalités d’un système de gestion de base de données relationnelle Microsoft SQL Server dans un service géré.

* [HDInsight](https://azure.microsoft.com/documentation/services/hdinsight/) - est déployée et dispositions groupes Windows Server ou basé sur Linux Apache Hadoop dans un nuage à gérer, analyser et créer des rapports sur les données volumineuses.

* [Apprentissage automatique](https://azure.microsoft.com/documentation/services/machine-learning/) - vous permet de créer, tester, fonctionnent et gérer des solutions analytiques prédictives dans un service entièrement géré.

### <a name="additional-services"></a>Services supplémentaires

Votre solution calculer Big devrez d’autres services Azure se connecter aux ressources locales ou dans d’autres environnements. Voici quelques exemples :

* [Réseau virtuel](https://azure.microsoft.com/documentation/services/virtual-network/) - crée une section isolée logiquement dans Azure se connecter ressources Azure entre eux ou à votre centre de données locale. Avec un réseau virtuel entre local, calculer volumineux applications peuvent accéder aux données locales, les services Active Directory et les serveurs de licences

* [ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/) - crée une connexion privée entre centres de données Microsoft et de l’infrastructure locale ou dans un environnement de co-création emplacement. ExpressRoute fournit une sécurité accrue, plus la fiabilité, vitesses et inférieur latence que les connexions classiques via Internet.

* [Service Bus](https://azure.microsoft.com/documentation/services/service-bus/) - propose plusieurs mécanismes pour les applications communiquer ou échanger des données, si elles sont situés dans Azure, sur une autre plate-forme cloud, ou dans un centre de données.

## <a name="next-steps"></a>Étapes suivantes

* Consultez [Les ressources techniques par lots et HPC](big-compute-resources.md) pour rechercher des conseils pour créer votre solution.

* Présentez vos options Azure avec les partenaires, y compris le Cycle informatique et UberCloud.

* En savoir plus sur Calculer volumineux Azure solutions fournies par [Tours Watson](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18222), [Altair](https://azure.microsoft.com/blog/availability-of-altair-radioss-rdma-on-microsoft-azure/), [ANSYS](https://azure.microsoft.com/blog/ansys-cfd-and-microsoft-azure-perform-the-best-hpc-scalability-in-the-cloud/)et [d3VIEW](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=22088).

* Pour les dernières informations, voir [Microsoft HPC et le blog de l’équipe lot](http://blogs.technet.com/b/windowshpc/) et le [blog Azure](https://azure.microsoft.com/blog/tag/hpc/).

<!--Image references-->
[parallel]: ./media/batch-hpc-solutions/parallel.png
[coupled]: ./media/batch-hpc-solutions/coupled.png
[iaas_cluster]: ./media/batch-hpc-solutions/iaas_cluster.png
[burst_cluster]: ./media/batch-hpc-solutions/burst_cluster.png
[batch_proc]: ./media/batch-hpc-solutions/batch_proc.png
