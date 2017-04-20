<properties
   pageTitle="La résilience pour récupération de perte d’une assistance technique région Azure | Microsoft Azure"
   description="Article sur la présentation et conception d’applications à tolérance de panne résistant, hautement disponible, ainsi que de planification de sinistre"
   services=""
   documentationCenter="na"
   authors="adamglick"
   manager="saladki"
   editor=""/>

<tags
   ms.service="resiliency"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/18/2016"
   ms.author="aglick"/>

#<a name="azure-resiliency-technical-guidance-recovery-from-a-region-wide-service-disruption"></a>Conseils techniques résilience Azure : récupération à partir d’une interruption de service à l’échelle de la région

Azure est divisé physiquement et logiquement en unités appelées régions. Une région se compose d’un ou plusieurs centres de données à proximité. Au moment de la rédaction, Azure a 24 régions du monde entier.

Dans des circonstances exceptionnelles, il est possible que les installations dans une région entière peuvent devenir inaccessibles, par exemple en raison de défaillances du réseau. Ou installations peuvent être perdues entièrement, par exemple en raison d’un incident Natural Keyboard. Cette section décrit les fonctionnalités de Azure pour créer des applications qui sont réparties sur régions. Ce distribution contribue à réduire la possibilité qu’une défaillance dans une zone géographique peut affecter les autres régions.

##<a name="cloud-services"></a>Services en nuage

###<a name="resource-management"></a>Gestion des ressources

Vous pouvez répartir des instances cluster entre régions en créant un service cloud distinct dans chaque région cible, puis publier le package de déploiement sur chaque service cloud. Toutefois, notez que répartir le trafic services cloud dans différentes régions doit être implémentée par le développeur de l’application ou avec un service de gestion du trafic.

Déterminer le nombre d’instances de rôle rechange pour déployer à l’avance pour la reprise est un aspect important de planification de la capacité. Un déploiement à grande échelle secondaire permet de garantir que capacité est déjà disponible lorsque cela est nécessaire ; Toutefois, cela double efficacement le coût. Un modèle commun est d’avoir un petit déploiement secondaire, juste assez grand pour exécuter les services critiques. Ce déploiement secondaire petit est une excellente idée, les deux pour réserver capacité et pour tester la configuration de l’environnement secondaire.

>[AZURE.NOTE]Le quota de l’abonnement n’est pas une garantie capacité. Le quota est simplement une limite de crédit. Pour garantir la capacité, le nombre requis de rôles doit être défini dans le modèle de service et les rôles doivent être déployés.

###<a name="load-balancing"></a>Équilibrage de charge

Pour équilibrer le trafic via régions nécessite une solution de gestion du trafic. Azure fournit [Azure le trafic Gestionnaire](https://azure.microsoft.com/services/traffic-manager/). Vous pouvez également tirer parti de services tiers qui fournissent des fonctionnalités de gestion de trafic similaire.

###<a name="strategies"></a>Stratégies

Plusieurs autres stratégies sont disponibles pour mettre en œuvre cluster distribué dans régions. Ils doivent être adaptés pour les besoins spécifiques et les circonstances de l’application. À un niveau élevé, les approches peuvent être divisées en les catégories suivantes :

  * __Redéployez sur sinistre__: dans cette approche l’application est si votre zéro au moment de l’incident. Ceci est approprié pour les applications non critiques qui ne nécessitent pas un temps de récupération garantie.

  * __Rechange chaudes (actif/passif)__: un service hébergé secondaire est créé dans une autre zone et rôles sont déployés afin de garantir la capacité minimale ; Toutefois, les rôles ne reçoivent le trafic de production. Cette approche est utile pour les applications qui n’ont pas été conçues pour répartir le trafic entre régions.

  * __Secours (actif/actif)__: l’application est conçue pour recevoir la charge de production dans plusieurs régions. Les services de cloud dans chaque région peuvent être configurés pour capacité plus élevée que nécessaire pour reprise après sinistre. Par ailleurs, les services cloud peuvent dimensionner out comme bon vous semble au moment de l’un sinistre et basculement. Cette approche requiert beaucoup investi dans la conception d’application, mais il a des avantages significatifs. Ces incluent le temps de récupération basse et garanties, continue test de tous les emplacements de récupération et l’utilisation efficace de la capacité.

Une description complète de conception distribuée est en dehors de l’étendue de ce document. Pour plus d’informations, voir [sinistre et disponibilité des Applications Azure](https://aka.ms/drtechguide).

##<a name="virtual-machines"></a>Machines virtuelles

Récupération de l’infrastructure comme un machines virtuelles de service (IaaS) (machines virtuelles) est similaire à la plateforme comme un service (PaaS) calculer récupération de nombreux aspects. Il existe des différences importantes, cependant, dû au fait qu’un IaaS VM se compose de la machine virtuelle et le disque machine virtuelle.

  * __Utiliser Azure sauvegarde pour créer des sauvegardes région croisée qui sont application cohérente__.
  [Sauvegarde Azure](https://azure.microsoft.com/services/backup/) permet aux clients de créer des sauvegardes cohérentes application sur plusieurs disques machine virtuelle et prise en charge de la réplication des sauvegardes parmi les régions. Vous pouvez le faire en choisissant à geo répliquer l’archivage sécurisé sauvegarde au moment de la création. Notez que la réplication de la sauvegarde l’archivage sécurisé doit être configuré au moment de la création. Elle ne peut pas être définie ultérieurement. Si une zone est perdue, Microsoft rend les sauvegardes disponibles pour les clients. Clients pourront restaurer à n’importe quel leurs configuré des points de restauration.

  * __Séparer le disque de données à partir du disque système d’exploitation__. Un facteur important pour les machines virtuelles IaaS est que vous ne pouvez pas modifier le disque système d’exploitation sans recréer la machine virtuelle. Il s’agit pas d’un problème si votre stratégie de récupération consiste à redéployez après sinistre. Toutefois, il peut être un problème si vous utilisez l’approche chaudes rechange pour réserver de la capacité. Pour implémenter correctement, vous devez disposer du disque système d’exploitation approprié déployé vers les emplacements principaux et secondaires, et les données d’application doivent être stockées sur un lecteur distinct. Si possible, utilisez une configuration de système d’exploitation standard qui peut être fournie dans les deux emplacements. Après un basculement, vous devez puis joignez le lecteur de données à vos machines virtuelles IaaS existantes dans le contrôleur de domaine secondaire. AzCopy permet de copier des instantanés des données disques vers un site distant.

  * __Tenir compte des problèmes potentiels de cohérence après un basculement de geo de plusieurs disques machine virtuelle__. Machine virtuelle disques sont implémentées comme des objets BLOB Azure stockage et présenter la même caractéristique geo réplication. À moins que la [Sauvegarde Azure](https://azure.microsoft.com/services/backup/) est utilisée, il n’existe aucune garantie de cohérence sur disques, geo réplication étant asynchrone et réplique séparément. Disques machine virtuelle individuels sont forcément dans un blocage cohérent état après un basculement geo, mais n’est pas cohérente sur disques. Cela peut entraîner des problèmes dans certains cas (par exemple, dans le cas d’agrégat).

##<a name="storage"></a>Espace de stockage

###<a name="recovery-by-using-geo-redundant-storage-of-blob-table-queue-and-vm-disk-storage"></a>Récupération à l’aide de stockage Geo redondants de blob, table, file d’attente et de stockage sur disque machine virtuelle

Dans Azure BLOB, tables, files d’attente et machine virtuelle disques sont tout geo répliquées par défaut. Il s’agit d’en tant que stockage Geo redondantes (GRS). GRS réplique les données de stockage dans un centre de données pour des centaines de miles cm au sein d’une zone géographique spécifique. GRS est conçu pour offrir la durabilité supplémentaire en cas de sinistre majeur. Contrôles Microsoft basculement et le basculement est limitée aux graves dans lequel l’emplacement principal d’origine est considéré comme irrécupérable dans un laps de temps acceptable. Dans certains cas, il peut s’agir plusieurs jours. Les données sont généralement répliquées quelques minutes, bien que l’intervalle de synchronisation n’est pas encore couvert par un contrat de niveau de service.

En cas de basculement geo, il n’y ait pas de modifications à la manière dont le compte est accessible (pas modifie la clé de compte et URL). Le compte de stockage, cependant, sera dans une zone différente après le basculement. Cela peut affecter les applications qui nécessitent régionale affinité avec leur compte de stockage. Même pour les services et applications qui ne nécessitent pas un compte de stockage dans le même centre de données, la latence entre-centre de données et les frais de bande passante peuvent être une qualité visuelle époustouflante raisons pour déplacer temporairement le trafic vers la zone basculement. Cela pourrait facteur dans une stratégie de récupération d’urgence générale.

Outre le basculement automatique fournie par GRS, Azure a mis en place un service qui vous donne accès en lecture à la copie de vos données dans l’emplacement de stockage secondaire. Cette option est appelée stockage Geo redondants accès en lecture (RA GRS).

Pour plus d’informations sur le stockage GRS et RA GRS, voir [réplication de stockage Azure](../storage/storage-redundancy.md).

###<a name="geo-replication-region-mappings"></a>Mappages de région géographique réplication :

Il est important de savoir où vos données sont répliquées geo, afin de savoir où déployer les autres instances de vos données nécessitant régionale affinité avec votre espace de stockage. Le tableau suivant montre les paires d’emplacement principal et secondaire :

[AZURE.INCLUDE [paired-region-list](../../includes/paired-region-list.md)]

###<a name="geo-replication-pricing"></a>Geo réplication tarifs :

Geo réplication est incluse dans les tarifs actuels pour le stockage Azure. Cette option est appelée stockage Geo redondantes (GRS). Si vous ne souhaitez pas vos données répliquée geo vous pouvez désactiver la réplication geo pour votre compte. Cette option est appelée stockage redondants localement, et il est facturée au prix d’une remise par rapport à GRS.

###<a name="determining-if-a-geo-failover-has-occurred"></a>Déterminer si un basculement geo s’est produite

Si un basculement geo se produit, cela est validée dans le [Tableau de bord au niveau de Service Azure](https://azure.microsoft.com/status/). Applications peuvent implémenter un moyen automatisé de détecter, cependant, en analysant la région de geo pour leur compte de stockage. Cela peut servir à déclencher la lecture d’autres opérations de récupération, telles que l’activation des ressources de cluster dans la zone de geo où leur stockage déplacé vers. Vous pouvez effectuer une requête pour cela à partir de la gestion des services API, à l’aide des [Propriétés du compte de stockage obtenir](https://msdn.microsoft.com/library/ee460802.aspx). Les propriétés pertinentes sont :

    <GeoPrimaryRegion>primary-region</GeoPrimaryRegion>
    <StatusOfPrimary>[Available|Unavailable]</StatusOfPrimary>
    <LastGeoFailoverTime>DateTime</LastGeoFailoverTime>
    <GeoSecondaryRegion>secondary-region</GeoSecondaryRegion>
    <StatusOfSecondary>[Available|Unavailable]</StatusOfSecondary>

###<a name="vm-disks-and-geo-failover"></a>Machine virtuelle disques et geo basculement

Comme indiqué dans la section sur disques machine virtuelle, il n’existe aucune garantie la cohérence des données, sur disques machine virtuelle après un basculement. Pour garantir l’exactitude des sauvegardes, un logiciel de sauvegarde tels que le Gestionnaire de Protection des données doit être utilisé pour sauvegarder et restaurer les données d’application.

##<a name="database"></a>Base de données

###<a name="sql-database"></a>Base de données SQL

Base de données SQL Azure propose deux types de récupération : Geo restaurer et la réplication de Geo Active.

####<a name="geo-restore"></a>Geo-restaurer

[Geo-restaurer](../sql-database/sql-database-recovery-using-backups.md#geo-restore) est également disponible avec les bases de données basique, Standard et Premium. Il propose l’option de récupération par défaut lors de la base de données n’est pas disponible en raison d’un incident dans la région dans lequel votre base de données est hébergé. Semblable au Point-à-temps restaurer, Geo restaurer s’appuie sur des sauvegardes de base de données dans le stockage Azure geo redondants. Il restaure à partir de la copie de sauvegarde répliquées geo et par conséquent est résistant pour les interruptions de stockage dans la région principale. Pour plus d’informations, voir [restaurer une base de données SQL Azure ou basculer vers un moniteur secondaire](../sql-database/sql-database-disaster-recovery.md).

####<a name="active-geo-replication"></a>Geo-réplication Active

[Réplication de Geo Active](../sql-database/sql-database-geo-replication-overview.md) est disponible pour tous les niveaux de base de données. Il est conçu pour les applications qui ont des exigences de récupération plus agressifs que Geo restaurer peut proposer. À l’aide de la réplication Geo Active, vous pouvez créer jusqu'à quatre secondaires lisibles sur des serveurs dans différentes régions. Vous pouvez le faire basculer vers un des secondaires. En outre, la réplication Geo Active peut servir à prendre en charge les scénarios de mise à niveau ou déplacement d’applications, ainsi que les charges de travail en lecture seule équilibrage de la charge. Pour plus d’informations, voir [configurer la réplication de Geo](../sql-database/sql-database-geo-replication-portal.md) et basculer [vers la base de données secondaire](../sql-database/sql-database-geo-replication-failover-portal.md). Pour plus d’informations sur la création et mettre en œuvre les applications et applications mise à niveau sans interruption de service, consultez [Création d’une application de reprise cloud à l’aide de réplication Geo Active dans la base de données SQL](../sql-database/sql-database-designing-cloud-solutions-for-disaster-recovery.md) et [Gestion des mises à niveau des applications cloud à l’aide de base de données Active Geo-réplication SQL](../sql-database/sql-database-manage-application-rolling-upgrade.md) .

###<a name="sql-server-on-virtual-machines"></a>SQL Server sur Machines virtuelles

De nombreuses options sont disponibles pour la récupération et la disponibilité pour SQL Server 2012 (et versions ultérieures) en cours d’exécution dans le Machines virtuelles Azure. Pour plus d’informations, voir [disponibilité et reprise pour SQL Server dans le Machines virtuelles Azure](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md).

##<a name="other-azure-platform-services"></a>D’autres services de plateforme Azure

Lorsque vous essayez d’exécuter votre service cloud dans plusieurs régions Azure, vous devez prendre en compte les implications pour chacun de vos dépendances. Dans les sections suivantes, les instructions spécifiques au service part du principe que vous devez utiliser le même service Azure dans un autre centre de données Azure. Cela implique la configuration et les tâches de réplication de données.

>[AZURE.NOTE]Dans certains cas, ces étapes peuvent vous aider à limiter une interruption de service spécifiques au lieu d’un événement de centre de données entier. À partir du point de vue de l’application, une panne de service spécifiques peut être aussi limitation et verser migration temporairement le service vers une autre zone Azure.

###<a name="service-bus"></a>Bus des services

Bus des services Azure utilise un espace de noms unique qui ne couvre pas régions Azure. La première exigence consiste à configurer les espaces de noms bus service nécessaire dans la zone de remplacement. Il existe cependant considérations pour la durer des messages en file d’attente. Il existe plusieurs stratégies pour la réplication des messages dans les régions Azure. Pour plus d’informations sur ces stratégies de réplication et d’autres stratégies de récupération, voir [meilleures pratiques pour les applications isolantes contre défaillances Bus des services et des incidents](../service-bus-messaging/service-bus-outages-disasters.md). Pour d’autres considérations relatives à la disponibilité, voir [Bus de Service (disponibilité)](./resiliency-technical-guidance-recovery-local-failures.md#other-azure-platform-services).

###<a name="app-service"></a>Service d’application

Pour migrer une application de Service d’application Azure, tels que des applications Web ou applications Mobile, à une zone Azure secondaire, vous devez disposer d’une sauvegarde du site Web disponible pour la publication. Si la coupure n’entraîne pas le centre de données Azure entier, il peut être possible d’utiliser FTP pour télécharger une sauvegarde récente le contenu du site. Puis créer une nouvelle application dans la zone secondaire, à moins que cette option pour réserver de la capacité n’est déjà fait. Publier le site dans la nouvelle zone, puis apportez les modifications de configuration nécessaires. Ces modifications peuvent inclure les chaînes de connexion de base de données ou d’autres paramètres régionale. Si nécessaire, ajoutez certificat SSL du site et modifiez l’enregistrement CNAME DNS afin que le nom de domaine personnalisé pointe vers l’URL de l’application Web Azure redéployé.

###<a name="hdinsight"></a>HDInsight

Les données associées à HDInsight sont stockées par défaut dans le stockage Blob Azure. HDInsight nécessite qu’un cluster Hadoop traiter les tâches MapReduce doit se trouver dans la même région que le compte de stockage contenant les données en cours d’analyse co-création. Condition que vous utilisiez la fonctionnalité geo réplication disponible pour le stockage Azure, vous pouvez accéder à vos données dans la zone secondaire où les données a été répliquées si pour une raison quelconque la région principale n’est plus disponible. Vous pouvez créer un nouveau cluster Hadoop dans la région dans lequel les données ont été répliquées et poursuivre son traitement. Pour d’autres considérations relatives à la disponibilité, voir [HDInsight (disponibilité)](./resiliency-technical-guidance-recovery-local-failures.md#other-azure-platform-services).

###<a name="sql-reporting"></a>Création de rapports SQL

Pour l’instant, la récupération de la perte d’une région Azure nécessite plusieurs instances SQL Reporting dans différentes régions Azure. Ces instances SQL Reporting doivent accéder les mêmes données, et ces données doivent avoir son propre récupération planifier en cas de sinistre. Vous pouvez également conserver des copies de sauvegarde externe du fichier RDL pour chaque rapport.

###<a name="media-services"></a>Services de support

Azure Media Services a une approche de récupération différent pour la diffusion en continu et d’encodage. En règle générale, la diffusion est plus critique durant une panne régionale. Pour préparer, dont vous avez un compte Media Services dans les deux régions Azure différentes. Le contenu codé doit se trouver dans les deux régions. Lors d’une défaillance, vous pouvez rediriger le trafic de diffusion en continu à la zone de remplacement. Codage peut être effectué dans n’importe quelle région Azure. Si le codage dépend du temps, par exemple pendant le traitement de l’événement en direct, vous devez être préparé à soumettre des tâches à un autre centre de données lors de défaillances.

###<a name="virtual-network"></a>Réseau virtuel

Fichiers de configuration fournissent le plus rapide pour configurer un réseau virtuel dans une autre zone Azure. Après avoir configuré le réseau virtuel dans la région Azure principale, [Exporter les paramètres de réseau virtuel](../virtual-network/virtual-networks-create-vnet-classic-portal.md) pour le réseau actuel vers un fichier de configuration réseau. Dans le cas d’une interruption de service dans la région principale, [restaurer le réseau virtuel](../virtual-network/virtual-networks-create-vnet-classic-portal.md) à partir du fichier de configuration stockée. Ensuite configurer d’autres services en nuage, machines virtuelles ou paramètres locaux croisée pour travailler avec le réseau virtuel.

##<a name="checklists-for-disaster-recovery"></a>Liste de vérification de sinistre

##<a name="cloud-services-checklist"></a>Liste de vérification de Services cloud

  1. Passez en revue la section Services en nuage de ce document.
  2. Créer une stratégie de rétablissement entre région.
  3. Comprendre les compromis dans réserver des capacités dans les autres régions.
  4. Utilisez le trafic routage outils, tels que le Gestionnaire de trafic Azure.

##<a name="virtual-machines-checklist"></a>Machines virtuelles aide-mémoire

  1. Passez en revue la section Machines virtuelles de ce document.
  2. [Sauvegarde Azure](https://azure.microsoft.com/services/backup/) permet de créer des sauvegardes cohérentes application au sein de régions.

##<a name="storage-checklist"></a>Liste de vérification de stockage

  1. Passez en revue la section de stockage de ce document.
  2. Ne désactivez pas la réplication geo des ressources de stockage.
  3. Comprendre autre région géographique réplication en cas de basculement.
  4. Créer des stratégies de sauvegarde personnalisés de stratégies de basculement contrôlée par l’utilisateur.

##<a name="sql-database-checklist"></a>Liste de vérification de base de données SQL

  1. Passez en revue la section de base de données SQL de ce document.
  2. Utilisez [Geo restaurer](../sql-database/sql-database-recovery-using-backups.md#geo-restore) ou [Geo réplication](../sql-database/sql-database-geo-replication-overview.md) le cas échéant.

##<a name="sql-server-on-virtual-machines-checklist"></a>SQL Server dans la liste de vérification Machines virtuelles

  1. Passez en revue SQL Server sur Machines virtuelles section de ce document.
  2. Utiliser des groupes de disponibilité AlwaysOn entre région ou la mise en miroir de base de données.
  3. Vous pouvez également utiliser la sauvegarde et de restauration stockage BLOB.

##<a name="service-bus-checklist"></a>Liste de vérification Bus des services

  1. Passez en revue la section Bus des services de ce document.
  2. Configurer un espace de noms Bus des services dans une autre zone.
  3. Prendre en compte les stratégies de réplication personnalisés pour les messages au sein de régions.

##<a name="app-service-checklist"></a>Liste de vérification de Service d’application

  1. Passez en revue la section Service de l’application de ce document.
  2. Mettre à jour des sauvegardes site en dehors de la région principale.
  3. Si une panne est partielle, essayez de récupérer site actuel avec FTP.
  4. Plan pour déployer le site sur un site nouveau ou existant dans une autre zone.
  5. Planifier les modifications de configuration de l’application et les enregistrements DNS CNAME.

##<a name="hdinsight-checklist"></a>Liste de vérification HDInsight

  1. Passez en revue la section HDInsight de ce document.
  2. Créer un nouveau cluster Hadoop dans la région avec les données répliquées.

##<a name="sql-reporting-checklist"></a>Liste de vérification de création de rapports SQL

  1. Passez en revue la section Création de rapports SQL de ce document.
  2. Mettre à jour une autre instance SQL Reporting dans une zone différente.
  3. Gérer un plan distinct pour répliquer la cible dans cette zone.

##<a name="media-services-checklist"></a>Liste de contrôle Media Services

  1. Passez en revue la section Media Services de ce document.
  2. Créer un compte Media Services dans une autre zone.
  3. Coder le même contenu dans les deux régions pour prendre en charge le basculement de diffusion en continu.
  4. Soumettre des travaux codage à une autre zone en cas d’interruption de service.

##<a name="virtual-network-checklist"></a>Liste de vérification réseau virtuel

  1. Passez en revue la section réseau virtuel de ce document.
  2. Utiliser exportés paramètres réseau virtuel pour recréer dans une autre région.

##<a name="next-steps"></a>Étapes suivantes

Cet article fait partie d’une série de [conseils techniques résilience Azure](./resiliency-technical-guidance.md). L’article suivant de cette série met l’accent sur la [récupération à partir d’un centre de données locale à Azure](./resiliency-technical-guidance-recovery-on-premises-azure.md).
