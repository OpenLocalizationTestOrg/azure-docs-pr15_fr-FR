<properties
    pageTitle="SQL (PaaS) de base de données par rapport à SQL Server dans le cloud sur machines virtuelles (IaaS) | Microsoft Azure"
    description="En savoir quelle option de SQL Server cloud adaptée à votre application : base de données SQL Azure (PaaS) ou SQL Server dans le cloud Azure machines virtuelles en fonctionnement."
    services="sql-database, virtual-machines"
    keywords="Nuage de SQL Server, SQL Server dans le cloud, PaaS de base de données, SQL Server, DBaaS en nuage"
    documentationCenter=""
    authors="CarlRabeler"
    manager="jhubbard"
    editor="cjgronlund"/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/06/2016"
    ms.author="carlrab"/>

# <a name="choose-a-cloud-sql-server-option-azure-sql-paas-database-or-sql-server-on-azure-vms-iaas"></a>Choisissez un option de SQL Server cloud : base de données SQL Azure (PaaS) ou SQL Server sur Azure machines virtuelles (IaaS)

Azure propose deux options pour héberger les charges de travail de SQL Server dans Microsoft Azure :

* [Base de données SQL Azure](https://azure.microsoft.com/services/sql-database/): base de données SQL A natif dans le cloud, également connu sous une plateforme, tel qu’une base de données de service (PaaS) ou une base de données en tant que service (DBaaS) qui est optimisé pour le développement de logiciels en tant que service (SaaS) application. Il est compatible avec la plupart des fonctionnalités de SQL Server. Pour plus d’informations sur PaaS, voir [Nouveautés PaaS](https://azure.microsoft.com/overview/what-is-paas/).
* [SQL Server Azure machines virtuelles en fonctionnement](https://azure.microsoft.com/services/virtual-machines/sql-server/): SQL Server installé et hébergé dans le nuage sur Windows Server Machines virtuelles (machines virtuelles) s’exécutant sur Azure, également connu sous une infrastructure en tant que service (IaaS).
SQL Server sur des machines virtuelles Azure est optimisé pour migrer des applications SQL Server existantes. Toutes les versions et les éditions de SQL Server sont disponibles. Il propose compatible à 100 % avec SQL Server, ce qui vous permet d’héberger autant bases de données sous forme de transactions de bases de données croisées nécessaires et en cours d’exécution. Il propose un contrôle total sur SQL Server et Windows.

Découvrez comment chaque option s’adapte à la plateforme de données Microsoft et obtenir de l’aide correspondant à l’option appropriée à vos besoins. Si vous modifier la priorité des économies ou administration minimale préparent tout le reste, cet article peut vous aider à décider quelle approche offre par rapport aux besoins de l’entreprise que vous intéressent le plus.


## <a name="microsofts-data-platform"></a>Plate-forme de données de Microsoft

Un des premiers facteurs à comprendre dans n’importe quelle discussion d’Azure par rapport à des bases de données SQL Server en local est que vous pouvez utiliser tout. Données plate-forme Microsoft utilise la technologie SQL Server et rend disponible machines physique en local, les environnements de cloud privé, cloud privé hébergé tiers environnements, et nuage public. SQL Server sur Azure mchines virtuels vous permet de répondent à des besoins uniques et centralisée via une combinaison d’en local et les déploiements hébergé sur le nuage, tout en utilisant le même ensemble de produits serveur, outils de développement et de l’expertise ces environnements.

   ![Options de SQL Server le cloud : SQL server sous IaaS ou SaaS SQL de base de données dans le cloud.](./media/sql-database-paas-vs-sql-server-iaas/SQLIAAS_SQL_Server_Cloud_Continuum.png)

Comme indiqué dans le diagramme, chaque offre peut être caractérisée par le niveau d’administration que vous avez sans l’infrastructure (sur l’axe X) et par le degré de rentabilité obtenue en consolidation à l’échelle de base de données et d’automatisation (sur l’axe des ordonnées).

Lorsque vous créez une application, quatre options de base sont disponibles pour le composant de SQL Server de l’application d’hébergement :

- SQL Server sur machines physiques non virtualisé
- SQL Server en local virtualisé machines (cloud privé)
- SQL Server dans Machine virtuelle Azure (cloud public Microsoft)
- Base de données SQL Azure (cloud public Microsoft)

Dans les sections suivantes, vous en savoir plus sur SQL Server dans le nuage public Microsoft : base de données SQL Azure et SQL Server sur machines virtuelles Azure. En outre, vous explorez facteurs de motivation entreprise courants pour déterminer quelle option appropriée pour votre application.

## <a name="a-closer-look-at-azure-sql-database-and-sql-server-on-azure-vms"></a>Observer de base de données SQL Azure et SQL Server sur machines virtuelles Azure

**Base de données SQL Azure** est un relationnelles de base de données-comme-a-service (DBaaS) hébergé dans le cloud Azure qui se situe dans les catégories du secteur de *Logiciels en tant que-a-Service (SaaS)* et *Plateforme-comme-a-Service (PaaS)*. [Base de données SQL](sql-database-technical-overview.md) repose sur standardisé matérielle et logicielle qui est propriétaire, hébergé, géré par Microsoft. Base de données SQL, vous pouvez développer directement sur le service en utilisant les fonctionnalités et fonctionnalités intégrées. Lorsque vous utilisez la base de données SQL, vous paiement avec les options à l’échelle vers le haut ou arrière pour power supérieur sans interruption.

**SQL Server Azure machines virtuelles en fonctionnement (machines virtuelles)** correspond à la catégorie industrie *Infrastructure-comme-a-Service (IaaS)* et vous autorise à exécuter SQL Server à l’intérieur d’une machine virtuelle dans le cloud. Tout comme base de données SQL, il est basé sur matériel normalisé qui est propriétaire, hébergé, géré par Microsoft. Lorsque vous utilisez SQL Server sur un ordinateur virtuel, vous pouvez soit rémunération-que vous accédez pour une licence de SQL Server déjà inclus dans une image de SQL Server ou utiliser facilement une licence existante. Vous pouvez également facilement échelle-haut/bas et suspendre/reprendre la machine virtuelle selon vos besoins.

En règle générale, ces deux options SQL sont optimisées à des fins différentes :

- **Base de données SQL** est optimisé pour réduire les coûts globaux à la valeur minimale pour la mise en service et la gestion de nombreuses bases de données. Il permet de réduire les coûts d’administration en cours, car vous n’êtes pas obligé de gérer les machines virtuelles, le système d’exploitation ou le logiciel de base de données. Vous n’êtes pas obligé de gérer les mises à niveau, disponibilité ou [des sauvegardes](sql-database-automated-backups.md). En règle générale, base de données SQL Azure peut considérablement augmenter le nombre de bases de données gérées par un seul informatique ou d’une ressource de développement.
- **SQL Server exécuté sur machines virtuelles Azure** est optimisé pour la migration d’applications existantes vers Azure ou développement d’applications locales existantes dans le cloud dans les déploiements hybrides. En outre, vous pouvez utiliser SQL Server sur un ordinateur virtuel pour développer et tester les applications SQL Server traditionnelles. Avec SQL Server sur machines virtuelles Azure, vous disposez des droits d’administrateur complets sur une instance de SQL Server dédiée et un ordinateur virtuel sur le nuage. Il est le choix idéal lorsqu’une organisation possède déjà disponible pour gérer les machines virtuelles des ressources informatiques. Ces fonctionnalités permettent de créer un système hautement personnalisé pour les destinataires de votre application performance spécifiques en matière de disponibilité.

Le tableau suivant résume les caractéristiques principales de base de données SQL et SQL Server sur machines virtuelles Azure :

|       | Base de données SQL | SQL Server dans une Machine virtuelle Azure|
| -------------- | ------------ | ---------------------- |
| **Idéal pour :** | Applications nouveau conçues par le cloud ayant des contraintes de temps de développement et de marketing. |Applications existantes qui requièrent migration rapide dans le cloud avec peu de modifications. Rapide développement et test scénarios lorsque vous ne voulez pas d’acheter du matériel de SQL Server locale hors production. |
|| Équipes qui ont besoin de haute disponibilité intégrée, reprise et mise à niveau pour la base de données. |Permet aux équipes qui peut configurer et gérer la disponibilité, sinistre et appliquer un correctif pour SQL Server. Certains fournis fonctionnalités automatisées considérablement simplifient ceci. |
||Équipes qui ne souhaitez pas gérer le système d’exploitation sous-jacent et les paramètres de configuration.| Si vous avez besoin d’un environnement personnalisé avec des droits d’administrateur complets.|
||Bases de données plus grande pouvant être [horizontalement ou verticalement partition](sql-database-elastic-scale-introduction.md#horizontal-and-vertical-scaling) à l’aide d’un motif horizontale ou bases de données de 1 To maximum.|Instances de SQL Server avec jusqu'à 64 To de stockage. L’instance peut prendre en charge autant de bases de données selon vos besoins. |
||[Applications de construction logiciels en tant que-a-Service (SaaS)](sql-database-design-patterns-multi-tenancy-saas-applications.md).| Migration et la création d’applications d’entreprise et hybride.|
|||||
|**Ressources :**|Vous ne souhaitez pas utiliser les ressources informatiques pour la configuration et la gestion de l’infrastructure sous-jacente, mais voulez concentrer sur la couche d’application.|Vous avez des ressources informatiques pour configurer et gérer. Certains fournis fonctionnalités automatisées considérablement simplifient ceci.|
|**Coût total de propriété :**|Supprime les coûts du matériel et réduire les coûts d’administration.|Supprime les coûts du matériel.|
|**Continuité des activités :**|Outre les fonctionnalités d’infrastructure de tolérance de panne intégrés, base de données SQL Azure fournit des fonctionnalités, telles que [des sauvegardes automatisées](sql-database-automated-backups.md), [Point-à-temps restaurer](sql-database-recovery-using-backups.md#point-in-time-restore), [Geo restaurer](sql-database-recovery-using-backups.md#geo-restore)et la [Réplication de Geo Active](sql-database-geo-replication-overview.md) pour augmenter la continuité des activités. Pour plus d’informations, voir [vue d’ensemble de la continuité de gestion de la base de données SQL](sql-database-business-continuity.md).|SQL Server sur Azure machines virtuelles vous permet de définir une solution de récupération après incident et de disponibilité élevée pour des besoins spécifiques de votre base de données. Par conséquent, vous pouvez accéder à un système qui est optimisé pour votre application. Vous pouvez tester et exécuter basculement par vous-même lorsque cela est nécessaire. Pour plus d’informations, voir [disponibilité et reprise pour SQL Server Azure machines virtuelles en fonctionnement](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md).|
|**Cloud hybride :**|Votre application locale peut accéder aux données dans la base de données SQL Azure.|Avec SQL Server sur machines virtuelles Azure, vous pouvez avoir des applications qui s’exécutent partiellement dans le cloud et partiellement en local. Par exemple, vous pouvez étendre votre réseau local et le domaine Active Directory dans le cloud via un [Réseau virtuel Azure](../virtual-network/virtual-networks-overview.md). En outre, vous pouvez stocker des fichiers de données locales dans le stockage Azure à l’aide de [Fichiers de données SQL Server dans Azure](http://msdn.microsoft.com/library/dn385720.aspx). Pour plus d’informations, voir [Présentation de SQL Server 2014 hybride Cloud](http://msdn.microsoft.com/library/dn606154.aspx).|
||Prend en charge la [réplication de transactions SQL Server](https://msdn.microsoft.com/library/mt589530.aspx) en tant qu’abonné pour répliquer des données.|Entièrement prend en charge [la réplication transactions SQL Server](https://msdn.microsoft.com/library/mt589530.aspx), [Groupes de disponibilité AlwaysOn](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md), Integration Services et envoi des journaux pour répliquer les données. En outre, sauvegardes SQL Server traditionnels sont entièrement prises en charge|
|||||
|||||

## <a name="business-motivations-for-choosing-azure-sql-database-or-sql-server-on-azure-vms"></a>Motivations des choix de base de données SQL Azure ou SQL Server sur machines virtuelles Azure

### <a name="cost"></a>Coût

Si vous êtes un démarrage fixé de trésorerie, ou d’une équipe dans une société établie qui fonctionne sous contraintes de budget serré, budgets limités sont souvent le pilote principal lorsque vous décidez comment héberger vos bases de données. Dans cette section, vous en savoir plus sur la facturation et concepts de base dans Azure en ce qui concerne ces deux options de base de données relationnelle des licences : base de données SQL et SQL Server sur machines virtuelles Azure. Vous découvrez également calculer le coût total d’application.

#### <a name="billing-and-licensing-basics"></a>Facturation et concepts de base des licences

**Base de données SQL** est vendu aux clients en tant que service, pas avec une licence.  [SQL Server sur machines virtuelles Azure](../virtual-machines/virtual-machines-windows-sql-server-iaas-overview.md) est vendu avec une licence incluse que vous payez par minute. Si vous avez une licence existante, vous pouvez également l’utiliser.  

Pour l’instant, **Base de données SQL** est disponible dans plusieurs niveaux de service, ce qui sont facturé toutes les heures à taux fixe en fonction du niveau de service et niveau de performance que vous choisissez. En outre, vous êtes facturé pour le trafic Internet sortant en normal des [taux de transfert de données](https://azure.microsoft.com/pricing/details/data-transfers/). Les niveaux de service Basique, Standard et Premium sont conçus pour offrir des performances prévisibles avec plusieurs niveaux de performance pour correspondre à la configuration requise de votre application pointe. Vous pouvez basculer entre les niveaux de service et de performances pour répondre aux besoins de débit variées de votre application. Si votre base de données a important volume de transactions et doit prendre en charge de plusieurs utilisateurs simultanés, nous vous recommandons le niveau de service Premium. Pour obtenir les dernières informations sur les niveaux de service pris en charge en cours, voir [Niveaux de Service de base de données SQL Azure](sql-database-service-tiers.md). Vous pouvez également créer [des pools élastique de base de données](sql-database-elastic-pool.md) pour partager les ressources de performances entre les instances de base de données.

Avec la **Base de données SQL**, le logiciel de base de données est automatiquement configuré, corrigé et mis à niveau par Microsoft, qui permet de réduire les coûts d’administration. En outre, ses capacités de [sauvegarde intégrée](sql-database-automated-backups.md) vous aident à atteindre des économies, surtout si vous avez un grand nombre de bases de données.

Avec **SQL Server sur machines virtuelles Azure**, vous pouvez utiliser une image SQL Server fournis par la plate-forme (qui inclut une licence) ou mettre votre licence SQL Server. Toutes les versions de SQL Server pris en charge (2008 R2, 2012, 2014, 2016) et les éditions (pour les développeurs, Express, Web, Standard, entreprise) sont disponibles. En outre, mettre votre-appartient-licence versions (BYOL) des images sont disponibles. Lorsqu’elle est à l’aide de la Azure fournie images, le coût des opérations dépend de la taille de la mémoire virtuelle et l’édition de SQL Server que vous choisissez. Quelle que soit la taille de mémoire virtuelle ou édition de SQL Server, vous payez par minute coût de la licence de SQL Server et Windows Server, ainsi que le coût de stockage Azure pour les disques machine virtuelle. L’option de facturation par minute vous permet d’utiliser SQL Server pour tant que vous devez sans achat addition SQL Server des licences. Si vous avancez votre propre licence SQL Server Azure, vous êtes chargé pour Windows Server et les coûts de stockage uniquement. Pour plus d’informations sur les licences mettre vos propres, voir [Mobilité licence via Software Assurance sur Azure](https://azure.microsoft.com/pricing/license-mobility/).

#### <a name="calculating-the-total-application-cost"></a>Calculer le coût total d’application

Lorsque vous commencez à utiliser une plateforme en nuage, le coût de l’exécution de votre application comprend les coûts de développement et d’administration, ainsi que les coûts de service plateforme cloud public.

Voici le calcul des coûts détaillées pour votre application en cours d’exécution dans la base de données SQL et SQL Server sur machines virtuelles Azure :

**Lors de l’utilisation de base de données SQL Azure :**

*Coût total d’application = les coûts d’administration hautement réduit les coûts de développement logiciel + coûts de service de base de données SQL*

**Lorsque vous utilisez SQL Server sur machines virtuelles Azure :**

*Coût total d’application = coût de développement logiciel hautement réduit + les coûts d’administration + coûts des licences SQL Server et Windows Server + les coûts du stockage Azure*

Pour plus d’informations sur les tarifs, consultez les ressources suivantes :

- [Tarifs de base de données SQL](https://azure.microsoft.com/pricing/details/sql-database/)
- [Machine virtuelle tarification](https://azure.microsoft.com/pricing/details/virtual-machines/) pour [SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#sql) et [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#windows)
- [Azure calculateur de prix](https://azure.microsoft.com/pricing/calculator/)

> [AZURE.NOTE] Il existe un petit sous-ensemble des fonctionnalités qui ne sont pas applicable ou non disponible avec la base de données SQL sur SQL Server. Pour plus d’informations, voir [Limitations générales de base de données SQL et des instructions](sql-database-general-limitations.md) et [des informations de base de données SQL Transact-SQL](sql-database-transact-sql-information.md) . Si vous déplacez une solution SQL Server existante dans le cloud, voir [migrer une base de données SQL Server pour la base de données SQL Azure](sql-database-cloud-migrate.md). Lorsque vous migrez une application pour SQL de base de données SQL Server en local, vous pouvez mettre à jour l’application pour tirer parti des fonctionnalités qui offre des services en nuage. Par exemple, vous pouvez envisager à l’aide [d’Azure Web Application Service](https://azure.microsoft.com/services/app-service/web/) ou [Azure Cloud Services](https://azure.microsoft.com/services/cloud-services/) pour héberger la couche d’application pour augmenter économies.

### <a name="administration"></a>Administration

Pour de nombreuses entreprises, la décision de la transition vers un service cloud est comme beaucoup sur décharger la complexité d’administration tel qu’il est coût. Avec la **Base de données SQL**, Microsoft gère le matériel sous-jacent. Microsoft automatiquement réplique toutes les données pour augmenter la disponibilité, configure et met à niveau le logiciel de base de données, gère l’équilibrage de charge et ne bascule transparente s’il existe une défaillance du serveur. Vous pouvez continuer à gérer votre base de données, mais vous n’avez plus besoin gérer le moteur de base de données, le système d’exploitation serveur ou le matériel.  Bases de données et connexions, index et analyse de requêtes et audit et de sécurité des exemples d’éléments que vous pouvez continuer à gérer.

Avec **SQL Server sur machines virtuelles Azure**, vous contrôlez totalement le système d’exploitation et la configuration de l’instance SQL Server. Avec une machine virtuelle, c’est à vous de décider quand mettre à jour/mise à niveau le système d’exploitation et logiciels de base de données et à installer d’autres logiciels comme antivirus. Certaines fonctionnalités automatisées sont fournies afin de simplifier considérablement la correction, de sauvegarde et de haute disponibilité. En outre, vous pouvez contrôler la taille de la machine virtuelle, le nombre de disques et leurs configurations de stockage. Azure permet de modifier la taille d’un ordinateur virtuel selon vos besoins. Pour plus d’informations, voir [Machine virtuelle et les tailles de Service Cloud pour Azure](../virtual-machines/virtual-machines-linux-sizes.md). 

### <a name="service-level-agreement-sla"></a>Niveau accord de service)

De nombreux départements informatiques, le temps d’obligations d’un contrat de niveau de Service (SLA) de la réunion est une priorité. Dans cette section, nous examinons que SLA s’applique à chaque option d’hébergement de base de données.

Pour les niveaux de service Basic de **Base de données SQL** , Standard et Premium Microsoft fournit une disponibilité SLA de 99,99 %. Pour plus d’informations, voir le [Contrat de niveau de Service](https://azure.microsoft.com/support/legal/sla/sql-database/). Pour obtenir les dernières informations sur les niveaux de service de base de données SQL et les plans de continuité de l’activité entreprise pris en charge, voir [Niveaux de Service](sql-database-service-tiers.md).

Pour **SQL Server exécuté sur machines virtuelles Azure**, Microsoft fournit une disponibilité SLA de 99,95 % qui recouvre simplement la Machine virtuelle. Ce contrat SLA ne comprend pas les processus (par exemple, SQL Server) en cours d’exécution sur l’ordinateur virtuel et nécessite que vous hébergez au moins deux instances de machine virtuelle dans un jeu de disponibilité. Pour plus d’informations, voir la [Machine virtuelle SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Pour une base de données haute disponibilité (HA) dans des ordinateurs virtuels, vous devez configurer une des options de disponibilité pris en charge dans SQL Server, tels que des [Groupes de disponibilité AlwaysOn](http://blogs.technet.com/b/dataplatforminsider/archive/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery.aspx). À l’aide d’une option de disponibilité pris en charge ne fournit pas d’accord supplémentaire, mais vous permet d’atteindre > disponibilité de base de données 99,99 %.

### <a name="market"></a>Sur le marché

**Base de données SQL** est la solution idéale pour les applications conçus cloud lorsque la productivité du développeur et rapide de temps au marché sont critiques. Avec la fonctionnalité de programmation j’aime DBA, il est idéale pour les développeurs et architectes cloud comme il réduit qu’il soit nécessaire pour gérer le système d’exploitation et la base de données sous-jacente. Par exemple, vous pouvez utiliser [l’API REST](http://msdn.microsoft.com/library/azure/dn505719.aspx) et les [Applets de commande PowerShell](http://msdn.microsoft.com/library/azure/dn546726.aspx) pour automatiser et gérer des tâches d’administration pour des milliers de bases de données. Fonctionnalités telles que [Élastique Pools de base de données](sql-database-elastic-pool.md) vous permettent de vous concentrer sur la couche d’application et fournir votre solution sur le marché plus rapidement.

**SQL Server exécuté sur machines virtuelles Azure** est idéale si vos applications existantes ou nouveau requièrent des bases de données, bases de données liées entre elles ou l’accès à toutes les fonctionnalités dans SQL Server ou Windows. Il est également une bonne cible lorsque vous voulez migrer existant en local applications et bases de données Azure comme-est. Étant donné que vous n’avez pas besoin modifier la présentation, des applications et des couches de données, vous gagner du temps et budgétaire sur remaniement votre solution existante. À la place, vous pouvez vous concentrer sur la migration de toutes vos solutions Azure et effectuer certaines optimisations de performances qui peuvent être requises par la plateforme Azure. Pour plus d’informations, voir [Les procédures recommandées pour SQL Server Azure machines virtuelles en fonctionnement](../virtual-machines/virtual-machines-windows-sql-performance.md).

## <a name="summary"></a>Résumé

Cet article exploré SQL Server et base de données SQL Azure machines virtuelles en fonctionnement (machines virtuelles) et mentionnés facteurs de motivation entreprise courants qui peuvent affecter votre décision. Voici un résumé des suggestions à prendre en compte :

Choisissez la **base de données SQL Azure** si :

- Vous créez des nouvelles applications sur le cloud pour tirer parti de la réduction des coûts et fournissez des services en nuage optimisation des performances. Cette approche offre les avantages d’un service cloud entièrement géré, vous aide à inférieur initial heure sur le marché et peut fournir à long terme optimiser les coûts.

- Vous souhaitez que Microsoft effectuer des opérations de gestion courantes sur vos bases de données et nécessitent une disponibilité renforcée SLA des bases de données.



Sélectionnez **SQL Server sur machines virtuelles Azure** si :

- Vous disposez d’applications en local existant que vous voulez migrer ou étendre dans le cloud, ou si vous souhaitez créer des applications d’entreprise supérieures à 1 To. Cette approche offre l’avantage de compatible SQL à 100 %, capacité de base de données volumineuse, un contrôle total sur SQL Server et Windows et sécuriser tunnel vers local. Cette approche réduit les coûts de développement et les modifications des applications existantes.

- Vous avez des ressources informatiques et pouvez être propriétaires finalement correctifs, les sauvegardes et disponibilité de base de données. Notez que certaines fonctionnalités automatisées simplifient considérablement ces opérations. 


## <a name="next-steps"></a>Étapes suivantes
- Voir [didacticiel de base de données SQL : créer une base de données SQL en quelques minutes à l’aide du portail Azure](sql-database-get-started.md) mise en route de la base de données SQL.
- Consultez [base de données SQL tarification] (https://azure.microsoft.com/pricing/details/sql-database/).
- Voir [mise en service une machine virtuelle SQL Server dans Azure](../virtual-machines/virtual-machines-windows-portal-sql-server-provision.md) mise en route de SQL Server sur machines virtuelles Azure.
- Voir [SQL Server sur une Machine virtuelle Azure : rubriques d’apprentissage](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/).
