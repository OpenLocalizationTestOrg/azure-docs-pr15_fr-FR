<properties
    pageTitle="Disponibilité et reprise pour SQL Server | Microsoft Azure"
    description="Explication des différents types de stratégies HADR pour SQL Server dans le Machines virtuelles Azure en cours d’exécution."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="MikeRayMSFT"
    manager="jhubbard"
    editor=""
    tags="azure-service-management"/>
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="10/20/2016"
    ms.author="MikeRayMSFT" />

# <a name="high-availability-and-disaster-recovery-for-sql-server-in-azure-virtual-machines"></a>Haute disponibilité et récupération après incident pour SQL Server dans le Machines virtuelles Azure

## <a name="overview"></a>Vue d’ensemble

Microsoft Azure, machines virtuelles (machines virtuelles) avec SQL Server peut contribuer à réduire le coût d’une solution de base de données haute disponibilité et urgence récupération (HADR). La plupart des solutions de SQL Server HADR prises en charge dans Azure machines virtuelles, à la fois en Azure seule et solutions hybride. Dans une solution Azure uniquement, tout le système HADR s’exécute dans Azure. Dans une configuration hybride, partie de la solution s’exécute dans Azure et l’autre partie s’exécute en local dans votre organisation. La possibilité de l’environnement Azure permet d’atteindre partiellement ou complètement Azure aux exigences le budget et HADR de vos systèmes de base de données SQL Server.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


## <a name="understanding-the-need-for-an-hadr-solution"></a>Comprendre la nécessité d’une solution HADR

C’est à vous pour vous assurer que votre système de base de données possède les capacités HADR nécessitant des niveaux de service (accord). Le fait que Azure fournit des mécanismes de disponibilité, tel que le service correcteur pour les services de cloud et la détection de récupération défaillance pour les ordinateurs virtuels, pas lui-même garantit que vous pouvez satisfaire le l’accord souhaité. Ces mécanismes protègent la disponibilité des ordinateurs virtuels, mais pas la disponibilité de SQL Server exécutée sur les ordinateurs virtuels. Il est possible de l’instance SQL Server à échouer alors que la machine virtuelle est en ligne et correct. En outre, y compris la disponibilité mécanismes fournis par Azure autoriser d’interruption de service des ordinateurs virtuels en raison d’événements telles que la récupération des logiciels ou défaillances matérielles et mises à niveau du système d’exploitation.

En outre, Geo redondants stockage (GRS) dans Azure, qui est implémentée avec une fonctionnalité appelée geo réplication, peut ne pas être une solution de récupération d’urgence adéquate pour vos bases de données. Étant donné que la réplication geo envoie des données en mode asynchrone, mises à jour récentes peuvent être perdues en cas d’urgence. Plus d’informations sur les limitations geo réplication sont décrites dans la section [Geo réplication pas pris en charge pour les fichiers journaux et de données sur des disques distincts](#geo-replication-support) .

## <a name="hadr-deployment-architectures"></a>Architectures de déploiement HADR

Technologies SQL Server HADR prises en charge dans Azure incluent :

- [Toujours sur les groupes de disponibilité](https://technet.microsoft.com/library/hh510230.aspx)
- [La mise en miroir de base de données](https://technet.microsoft.com/library/ms189852.aspx)
- [Envoi des journaux](https://technet.microsoft.com/library/ms187103.aspx)
- [Sauvegarder et restaurer avec le Service de stockage Blob Azure](https://msdn.microsoft.com/library/jj919148.aspx)
- [Toujours sur des Instances de Cluster de basculement](https://technet.microsoft.com/library/ms189134.aspx)

Il est possible de combiner les technologies ensemble afin de mettre en œuvre une solution SQL Server contenant la disponibilité et reprise. En fonction de la technologie que vous utilisez, un déploiement hybride peut-être nécessiter un tunnel VPN avec le réseau virtuel Azure. Les sections ci-dessous illustrent quelques-unes des architectures de déploiement d’exemple.

## <a name="azure-only-high-availability-solutions"></a>Azure seule : solutions de disponibilité

Vous pouvez avoir une solution haute disponibilité pour vos bases de données SQL Server dans Azure à l’aide de toujours sur les groupes de disponibilité ou la mise en miroir de base de données.

| Technologie                               | Exemples d’architecture                    |
| ---------------------------------------- | ---------------------------------------- |
| **Toujours sur les groupes de disponibilité**        | Tous les réplicas de disponibilité en cours d’exécution dans Azure machines virtuelles de disponibilité au sein de la même région. Vous devez configurer un contrôleur de domaine machine virtuelle, car Windows Server basculement cluster (WSFC) nécessite un domaine Active Directory.<br/> ![Toujours sur les groupes de disponibilité](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_ha_always_on.gif)<br/>Pour plus d’informations, consultez [Configurer toujours sur la disponibilité des groupes dans Azure (graphique)](virtual-machines-windows-portal-sql-alwayson-availability-groups.md). |
| **Toujours sur des Instances de Cluster de basculement** | Basculement Cluster Instances (FCI), qui nécessitent un stockage partagé, pouvant être créées de différentes 2 manières.<br/><br/>1. une FCI sur un WSFC deux nœuds en cours d’exécution dans Azure machines virtuelles avec stockage pris en charge par une solution de cluster tiers. Pour obtenir un exemple spécifique qui utilise DataKeeper SIÔS, voir [disponibilité pour un partage de fichiers à l’aide de WSFC et 3e partie logiciels SIÔS Datakeeper](https://azure.microsoft.com/blog/high-availability-for-a-file-share-using-wsfc-ilb-and-3rd-party-software-sios-datakeeper/).<br/><br/>2. un FCI sur un WSFC deux nœuds exécute dans Azure machines virtuelles avec un stockage en blocs cible partagé à distance iSCSI via ExpressRoute. Par exemple, NetApp privé stockage (NPS) expose une cible iSCSI via ExpressRoute avec Equinix aux machines virtuelles Azure.<br/><br/>Pour le stockage partagé tiers et les solutions de réplication de données, vous devez contacter le fournisseur pour tous les problèmes relatifs à l’accès aux données de basculement.<br/><br/>Notez que FCI en haut de [stockage de fichiers Azure](https://azure.microsoft.com/services/storage/files/) n'est pas prise en charge encore, étant donné que cette solution n’utilise pas de stockage Premium. Nous travaillons pour prendre en charge ce plus rapidement. |

## <a name="azure-only-disaster-recovery-solutions"></a>Azure seule : solutions de reprise

Vous pouvez avoir une solution de récupération d’urgence pour vos bases de données SQL Server dans Azure à l’aide de toujours sur les groupes de disponibilité, la mise en miroir de base de données ou sauvegarde et restaurer avec des objets BLOB de stockage.

| Technologie                               | Exemples d’architecture                    |
| ---------------------------------------- | ---------------------------------------- |
| **Toujours sur les groupes de disponibilité**        | Réplicas de disponibilité en cours d’exécution sur plusieurs centres de données dans Azure machines virtuelles de reprise. Cette solution entre région protège arrêt du site terminée. <br/> ![Toujours sur les groupes de disponibilité](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_dr_alwayson.png)<br/>Au sein d’une région, tous les réplicas doivent être au sein du même service cloud et la même VNet. Étant donné que chaque région aura un VNet distincte, ces solutions nécessitent VNet VNet la connectivité. Pour plus d’informations, voir [configurer un réseau privé virtuel de Site à Site dans le portail classique Azure](../vpn-gateway/vpn-gateway-site-to-site-create.md). |
| **La mise en miroir de base de données**                   | Principal et les pages en vis-à-vis et les serveurs exécutant dans différents centres de données pour la reprise. Vous devez déployer à l’aide de certificats de serveur, car un domaine active directory ne peut pas s’étalent sur plusieurs centres de données.<br/>![La mise en miroir de base de données](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_dr_dbmirroring.gif) |
| **Sauvegarder et restaurer avec le Service de stockage Blob Azure** | Bases de données de production sauvegardées directement au stockage d’objets blob dans un autre centre de données pour la reprise.<br/>![Sauvegarde et restauration](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_dr_backup_restore.gif)<br/>Pour plus d’informations, voir [sauvegarde et restauration pour SQL Server dans le Machines virtuelles Azure](virtual-machines-windows-sql-backup-recovery.md). |

## <a name="hybrid-it-disaster-recovery-solutions"></a>INFORMATIQUES hybrides : Solutions de reprise

Vous pouvez avoir une solution de récupération d’urgence pour vos bases de données SQL Server dans un environnement hybride informatiques à l’aide de toujours sur les groupes de disponibilité, la mise en miroir de base de données, d’envoi des journaux et sauvegarde et restaurer avec le stockage de blog Azure.

| Technologie                               | Exemples d’architecture                    |
| ---------------------------------------- | ---------------------------------------- |
| **Toujours sur les groupes de disponibilité**        | Certains réplicas disponibilité en cours d’exécution dans Azure machines virtuelles et autres réplicas en cours d’exécution en local pour intersites sinistre. Le site de production peut être localement ou dans un centre de données Azure.<br/>![Toujours sur les groupes de disponibilité](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_alwayson.gif)<br/>Étant donné que tous les réplicas de disponibilité doivent être placé dans le même cluster WSFC, le cluster WSFC doit couvrir les deux réseaux (un cluster WSFC plusieurs sous-réseaux). Cette configuration nécessite une connexion VPN entre Azure et le réseau local.<br/><br/>Pour sinistre réussie de vos bases de données, vous devez également installer un contrôleur de domaine réplica sur le site de reprise.<br/><br/>Il est possible d’utiliser l’Assistant Ajout de réplica dans SSMS pour ajouter un réplica Azure à un toujours sur Disponibilité groupe existant. Pour plus d’informations, consultez le didacticiel : développer votre toujours dans le groupe de disponibilité dans Azure. |
| **La mise en miroir de base de données**                   | Un partenaire en cours d’exécution dans une machine virtuelle Azure et les autres en cours d’exécution en local pour intersites sinistre à l’aide de certificats de serveur. Partenaires n’avez pas besoin d’être présent à la même domaine Active Directory et aucune connexion VPN n’est requise.<br/>![La mise en miroir de base de données](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_dbmirroring.gif)<br/>Une autre base de données en miroir scénario implique un partenaire en cours d’exécution dans une machine virtuelle Azure et les autres en cours d’exécution en local dans le même domaine Active Directory de sinistre intersites. Une [connexion VPN entre le réseau virtuel Azure et le réseau local](../vpn-gateway/vpn-gateway-site-to-site-create.md) est requise.<br/><br/>Pour sinistre réussie de vos bases de données, vous devez également installer un contrôleur de domaine réplica sur le site de reprise. |
| **Envoi des journaux**                         | Un serveur exécutant dans une machine virtuelle Azure et les autres en cours d’exécution en local pour intersites sinistre. Envoi des journaux dépend de partage de fichiers Windows, une connexion VPN entre le réseau virtuel Azure et le réseau local est requise.<br/>![Envoi des journaux](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_log_shipping.gif)<br/>Pour sinistre réussie de vos bases de données, vous devez également installer un contrôleur de domaine réplica sur le site de reprise. |
| **Sauvegarder et restaurer avec le Service de stockage Blob Azure** | Bases de données de production sauvegardées directement sur le stockage d’objets blob Azure de sinistre sur site.<br/>![Sauvegarde et restauration](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_backup_restore.gif)<br/>Pour plus d’informations, voir [sauvegarde et restauration pour SQL Server dans le Machines virtuelles Azure](virtual-machines-windows-sql-backup-recovery.md). |

## <a name="important-considerations-for-sql-server-hadr-in-azure"></a>Considérations importantes pour SQL Server HADR dans Azure

Machines virtuelles Azure, le stockage et mise en réseau sont distinguent opérationnelles à un local, non virtualisé l’infrastructure. Une mise en œuvre réussie d’une solution HADR SQL Server dans Azure nécessite que vous comprenez ces différences et concevez votre solution pour prendre en compte les.

### <a name="high-availability-nodes-in-an-availability-set"></a>Nœuds de disponibilité d’une série de disponibilité

Jeux de disponibilité dans Azure vous permettre d’y placer les nœuds haute disponibilité distincte défaillance domaines (FDs) et mise à jour (UDs). Pour les Azure machines virtuelles est placé dans le même jeu de disponibilité, vous devez les déployer dans le même service cloud. Seuls les nœuds dans le service cloud même peuvent intervenir dans le même jeu de disponibilité. Pour plus d’informations, voir [Gérer la disponibilité des ordinateurs virtuels](virtual-machines-windows-manage-availability.md).

### <a name="wsfc-cluster-behavior-in-azure-networking"></a>Comportement de cluster WSFC de mise en réseau Azure

Le service DHCP non conforme à RFC dans Azure peut entraîner la création de certaines configurations cluster WSFC échec, en raison du nom de réseau cluster attribué une adresse IP en double, la même adresse IP dans l’un des nœuds du cluster. Il s’agit d’un problème lors de l’implémentation toujours sur les groupes de disponibilité, qui dépend de la fonctionnalité WSFC.

Considérez le scénario lorsqu’un cluster à deux nœuds est créé et mis en ligne :

1. Le cluster est en ligne, puis nœud 1 demande une adresse IP affectée dynamiquement pour le nom du réseau cluster.

2. Aucune adresse IP différent d’adresse IP de NODE1 n’est donnée par le service DHCP, étant donné que le service DHCP reconnaît que la demande provient de nœud 1 lui-même.

3. Windows détecte qu’une adresse en double est affectée à la fois pour le nœud 1 et le nom du réseau cluster et le groupe de cluster par défaut ne parvient pas à mettre en ligne.

4. Le groupe de cluster par défaut se déplace vers le nœud 2, qui traite adresse IP du nœud 1 comme adresse IP du cluster et fait apparaître le groupe de cluster par défaut en ligne.

5. Lorsqu’un nœud 2 tente d’établir une connectivité avec nœud 1, paquets dirigées nœud 1 quittent jamais nœud 2, car il est résolu en adresse IP du nœud 1 à elle-même. Nœud 2 ne peut pas établir la connectivité avec nœud 1, puis perd le quorum et arrête le cluster.

6. En attendant, nœud 1 peut envoyer des paquets à nœud 2, mais le nœud 2 ne peut pas répondre. Nœud 1 perd le quorum et arrête le cluster.

Ce scénario peut être évité en assignant une adresse IP statique inutilisée, par exemple, une adresse IP liaison locale comme 169.254.1.1, sur le nom du réseau cluster afin de mettre le nom de réseau cluster en ligne. Pour simplifier ce processus, consultez la section [Configuration de Windows basculement Cluster dans Azure pour toujours sur les groupes de disponibilité](http://social.technet.microsoft.com/wiki/contents/articles/14776.configuring-windows-failover-cluster-in-windows-azure-for-alwayson-availability-groups.aspx).

Pour plus d’informations, consultez [Configurer toujours sur la disponibilité des groupes dans Azure (graphique)](virtual-machines-windows-portal-sql-alwayson-availability-groups.md).

### <a name="availability-group-listener-support"></a>Prise en charge de disponibilité groupe récepteur

Écoute de groupe de disponibilité est prises en charge sur machines virtuelles Azure exécutant Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 et Windows Server 2016. Cette prise en charge est rendu possible par l’utilisation de points de terminaison équilibrage de charge activé sur les ordinateurs virtuels Azure qui sont des nœuds de groupe de disponibilité. Vous devez suivre les étapes de configuration spéciale pour les récepteurs pour l’utiliser pour les deux applications clientes qui sont exécutent dans Azure, ainsi que celles en cours d’exécution en local.

Il existe deux principales options permettant de configurer votre récepteur : externe (public) ou interne. Le récepteur (public) externes utilise internet facing équilibrage de charge et est associé à un public IP virtuelle qui n’est accessible via internet. Un récepteur interne utilise un programme d’équilibrage de charge interne et ne prennent en charge les clients au sein du même réseau virtuel. Pour des options de charger le type d’équilibrage, vous devez activer Direct serveur renvoyer. 

Si le groupe de disponibilité s’étend sur plusieurs sous réseaux Azure (par exemple, un déploiement croisant régions Azure), la chaîne de connexion du client doit inclure «**MultisubnetFailover = True**». Cela entraîne tentatives de connexion en parallèle avec les réplicas dans différents sous-réseaux. Pour obtenir des instructions sur la configuration d’un récepteur, voir

- [Configurer un récepteur ILB pour toujours sur les groupes de disponibilité dans Azure](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md).
- [Configurer un récepteur externe pour toujours sur les groupes de disponibilité dans Azure](virtual-machines-windows-classic-ps-sql-ext-listener.md).

Vous pouvez toujours vous connecter à chaque réplica de disponibilité séparément en vous connectant directement à l’instance du service. En outre, toujours sur les groupes de disponibilité étant compatibilité descendante avec les clients de mise en miroir, vous pouvez vous connecter aux réplicas disponibilité comme base de données reflétant exactement les partenaires dans la mesure où les réplicas sont configurés similaire à la mise en miroir de base de données :

- Un seul réplica principal et un réplica secondaire

- Le réplica secondaire est configuré comme non lisible (**Lisible secondaire** option définie sur **non**)

Une chaîne de connexion de client exemple qui correspond à cette configuration j’aime la mise en miroir de base de données à l’aide d’ADO.NET ou SQL Server Native Client est ci-dessous :

    Data Source=ReplicaServer1;Failover Partner=ReplicaServer2;Initial Catalog=AvailabilityDatabase;

Pour plus d’informations sur la connectivité des clients, voir :

- [Utilisation de mots clés de chaîne de connexion avec SQL Server Native Client](https://msdn.microsoft.com/library/ms130822.aspx)
- [Connecter des Clients à une base de données en miroir Session (SQL Server)](https://technet.microsoft.com/library/ms175484.aspx)
- [Connexion à l’écoute de groupe de disponibilité dans informatiques hybrides](http://blogs.msdn.com/b/sqlalwayson/archive/2013/02/14/connecting-to-availability-group-listener-in-hybrid-it.aspx)
- [Groupe de disponibilité récepteurs, la connectivité des clients et le basculement d’Application (SQL Server)](https://technet.microsoft.com/library/hh213417.aspx)
- [À l’aide de la mise en miroir de base de données des chaînes de connexion des groupes de disponibilité](https://technet.microsoft.com/library/hh213417.aspx)

### <a name="network-latency-in-hybrid-it"></a>Latence réseau informatiques hybrides

Vous devez déployer votre solution HADR en supposant qu’il peut y avoir des périodes de temps à latence élevée réseau entre votre réseau local et Azure. Lorsque vous déployez réplicas vers Azure, vous devez utiliser validation asynchrone au lieu de validation synchrone pour le mode de synchronisation. Lorsque déploiement reflétant exactement les serveurs de base de données à la fois en local et dans Azure, utilisez le mode de haute performance plutôt que le mode de sécurité élevé.

### <a name="geo-replication-support"></a>Prise en charge geo réplication

Geo-réplication dans Azure disques ne reconnaît pas le fichier de données et le fichier journal de la même base de données à stocker sur des disques distincts. GRS réplique les modifications sur chaque disque indépendamment et asynchrone. Ce mécanisme garantit l’ordre d’écriture au sein d’un seul disque sur la copie répliquée geo, mais pas au sein des copies répliquées geo de plusieurs disques. Si vous configurez une base de données pour stocker son fichier de données et son fichier journal sur des disques distincts, les disques récupérés après un incident peuvent contenir une copie du fichier de données plus à jour que le fichier journal, qui s’arrête le journal écriture anticipée dans SQL Server et les propriétés ACID des transactions. Si vous n’avez pas l’option de désactivation de la réplication geo sur le compte de stockage, vous devez garder toutes les données et les fichiers journaux d’une base de donnée sur le même disque. Si vous devez utiliser plusieurs disques en raison de la taille de la base de données, vous devez déployer parmi les solutions de reprise répertoriées ci-dessus pour garantir la redondance de données.

## <a name="next-steps"></a>Étapes suivantes

Si vous avez besoin créer une machine virtuelle Azure avec SQL Server, voir [mise en service d’un serveur SQL Machine virtuelle sur Azure](virtual-machines-windows-portal-sql-server-provision.md).

Pour obtenir les meilleures performances à partir de SQL Server s’exécutant sur un ordinateur virtuel Azure, reportez-vous à l’aide dans [Les procédures recommandées pour SQL Server dans le Machines virtuelles Azure](virtual-machines-windows-sql-performance.md).

D’autres rubriques liés à l’exécution de SQL Server dans Azure machines virtuelles, consultez [SQL Server Azure machines virtuelles en fonctionnement](virtual-machines-windows-sql-server-iaas-overview.md).

### <a name="other-resources"></a>Autres ressources

- [Installer une nouvelle forêt Active Directory dans Azure](../active-directory/active-directory-new-forest-virtual-machine.md)
- [Créer WSFC Cluster pour toujours sur les groupes de disponibilité dans Azure machine virtuelle](http://gallery.technet.microsoft.com/scriptcenter/Create-WSFC-Cluster-for-7c207d3a)
