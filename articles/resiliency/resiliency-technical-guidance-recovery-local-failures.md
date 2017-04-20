<properties
   pageTitle="Conseils techniques : la récupération des défaillances locales dans Azure | Microsoft Azure"
   description="Voir l’article sur comprendre et conception résistant, hautement disponible, tolérance applications, ainsi que de planification de reprise axée sur pannes locales dans Azure."
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

#<a name="azure-resiliency-technical-guidance-recovery-from-local-failures-in-azure"></a>Conseils techniques résilience Azure : la récupération des défaillances locales dans Azure

Il existe deux principales menaces qui disponibilité des applications :

* L’échec d’appareils, tels que les lecteurs et les serveurs
* L’épuisement des ressources critiques, tels que cluster dans les conditions de charge de pointe

Azure fournit une combinaison de gestion des ressources, élasticité, l’équilibrage de charge et partition pour permettre à disponibilité dans ces conditions. Certaines de ces fonctionnalités sont exécutées automatiquement pour tous les services Azure. Cependant, dans certains cas, développeur de l’application doit effectuer des tâches supplémentaires pour bénéficier d’eux.

##<a name="cloud-services"></a>Services en nuage

Les Services en nuage Azure se compose de collections d’un ou plusieurs rôles web ou collaborateur. Une ou plusieurs instances d’un rôle peuvent s’exécuter simultanément. La configuration détermine le nombre d’instances. Instances de rôle sont contrôlés et gérés via un composant appelé contrôleur de tissu. Contrôleur de tissu détecte et répond aux échecs logicielle et matérielle automatiquement.

Chaque instance de rôle est exécuté dans sa propre machine virtuelle () et communique avec son contrôleur de tissu via un agent invité. L’agent invité collecte métriques de ressource et de nœud, y compris l’utilisation machine virtuelle, état, journaux, utilisation des ressources, exceptions et les conditions d’échec. Contrôleur de tissu requêtes l’agent invité à des intervalles et il redémarre la machine virtuelle si l’agent invité ne répond pas. En cas de panne, contrôleur de tissu associé déplace toutes les instances de rôle concerné vers un nouveau nœud de matériel et reconfigure le réseau pour acheminer le trafic il.

Pour tirer parti de ces fonctionnalités, les développeurs doivent s’assurer que tous les rôles de service évitent de stocker un état sur les instances de rôle. En revanche, toutes les données permanentes doivent être accessibles à partir du stockage résistant, tels que le stockage Azure ou base de données SQL Azure. Ainsi, tous les rôles gérer les requêtes. Cela signifie également qu’instances de rôle peuvent aller vers le bas à tout moment sans créer des incohérences dans l’état temporaire ou permanente du service.

La configuration minimale requise pour stocker l’état avec l’extérieur aux rôles a plusieurs implications. Par exemple, cela signifie que toutes les modifications associées à une table de stockage Azure doivent être modifiées dans une seule transaction groupe entité, si possible. Bien entendu, il n’est pas toujours possible de laisser toutes les modifications en une seule opération. Vous devez prendre une attention particulière pour vous assurer que les échecs d’instance de rôle n’entraînent pas de problèmes lorsqu’ils interrompent les opérations longue qui s’étalent sur deux ou plusieurs mises à jour de l’état du service permanente. Si un autre rôle tentative une telle opération, elle doit anticiper et gérer le cas où le travail a été partiellement effectué.

Par exemple, considérez un service de données des partitions sur plusieurs magasins. Si un rôle de travail s’arrête alors que celui-ci est déplacement un partagé, le déplacement de la partagé peut ne pas terminer. Ou le déplacement peut être répété à l’origine par un rôle de collaborateur différent, ce qui peut provoquer des données orphelines ou altération des données. Pour éviter les problèmes, les opérations de longue doivent être moins l’une des opérations suivantes :

 * *Idempotent*: répétitifs sans effets secondaires. Idempotent, une opération longue doit avoir le même effet quel que soit le nombre de fois elle est exécutée, même si elle est interrompue pendant l’exécution.
 * *Redémarrage par incréments*: en mesure de continuer à partir du point de panne plus récent. Pour être réutilisable par incréments, une opération longue doit comporter une séquence de plus petite atomiques opérations. Il doit également enregistrer sa progression dans le stockage résistant, afin que chaque appel ultérieur capte où son prédécesseur s’est arrêté.

Enfin, toutes les opérations de longue doivent être appelées à plusieurs reprises jusqu'à ce qu’ils réussissent. Par exemple, une opération de mise en service peut être placée dans une file d’attente Azure et puis supprimée de la file d’attente par un rôle de collaborateur uniquement lorsqu’il a réussi. Nettoyage peut être nécessaire pour créer des données qui interrompu opérations de nettoyage.

###<a name="elasticity"></a>Élasticité

Le nombre d’instances en cours d’exécution pour chaque rôle initial est déterminé dans chaque configuration des rôles. Les administrateurs doivent configurer initialement chaque rôle pour exécuter avec deux ou plusieurs instances basées sur charge attendue. Mais vous pouvez facilement évoluer instances de rôles ou vers le bas en tant que l’utilisation des motifs modifier. Vous pouvez le faire manuellement dans le portail Azure, ou vous pouvez automatiser le processus à l’aide de Windows PowerShell, l’API de gestion de Service ou d’outils tiers. Pour plus d’informations, voir [comment échelle une application](../cloud-services/cloud-services-how-to-scale.md).

###<a name="partitioning"></a>Partition

Contrôleur de tissu Azure utilise deux types de partitions :

* Une *mise à jour de domaine* est utilisé pour mettre à niveau des instances de rôle d’un service en groupes. Azure déploie instances de service dans plusieurs domaines de mise à jour. Pour une mise à jour sur place, contrôleur de tissu dysfonctionnement toutes les instances dans mettre à un jour domaine, les met à jour et les redémarre avant de déplacer vers le domaine de mise à jour suivant. Cette approche empêche l’ensemble du service indisponible pendant le processus de mise à jour.
* Un *domaine d’erreur* définit points potentiels de panne matérielle ou de réseau. Pour n’importe quel rôle comportant plusieurs instances, contrôleur de tissu garantit que les instances sont réparties sur plusieurs domaines d’erreur, pour empêcher les défaillances matérielles isolé d’interrompre le service. Domaines d’erreur régissent toute exposition au serveur et défaillances cluster.

L' [Azure au niveau de service (accord)](https://azure.microsoft.com/support/legal/sla/) garantit que lorsque deux ou plusieurs instances de rôle web sont déployés sur différent défaillance et mettre à niveau des domaines, leur aurez connectivité externe au moins 99,95 % du temps. Contrairement aux domaines de mise à jour, il n’existe aucun moyen pour contrôler le nombre de domaines d’erreur. Azure attribue des domaines d’erreur et répartit les instances de rôle sur les automatiquement. Au moins deux premières instances de chaque rôle sont placées dans défaillance différent et mettre à niveau des domaines pour vous assurer que n’importe quel rôle avec au moins deux instances répondra au contrat SLA. Ceci est représenté dans le diagramme suivant.

![Vue simplifiée de pannes de domaine](./media/resiliency-technical-guidance-recovery-local-failures/partitioning-1.png)

###<a name="load-balancing"></a>Équilibrage de charge

Tout le trafic entrant à un rôle web transmission à un équilibrage de charge sans état, qui distribue les demandes des clients entre les instances de rôle. Instances de rôle individuel n’ont pas d’adresses IP publiques, et elles ne sont pas directement dédiés à partir d’Internet. Rôles Web sont sans état afin que toute demande de client peut être routé vers n’importe quelle instance de rôle. Un événement [StatusCheck](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.statuscheck.aspx) est déclenché toutes les 15 secondes. Vous pouvez l’utiliser pour indiquer si le rôle est prêt à recevoir le trafic, ou si elle est occupé (e) et devez être retiré la rotation d’équilibrage de charge.

##<a name="virtual-machines"></a>Machines virtuelles

Machines virtuelles Azure diffère de plateforme comme un service (PaaS) calculer les rôles dans plusieurs aspects en fonction de la disponibilité. Dans certains cas, vous devez effectuer les tâches supplémentaires pour assurer la disponibilité élevée.

###<a name="disk-durability"></a>Durabilité disque

Contrairement aux instances de rôle PaaS, les données stockées sur les lecteurs machine virtuelle sont permanentes même lorsque la machine virtuelle est déplacée. Machines virtuelles Azure utiliser disques machine virtuelle qui existent comme des objets BLOB dans le stockage Azure. En raison de caractéristiques de disponibilité du stockage Azure, les données stockées sur les lecteurs d’un ordinateur virtuel sont également hautement disponibles.

Notez que le lecteur D (en machines virtuelles Windows) est l’exception à cette règle. Le lecteur D est en réalité physique stockage sur le serveur rack qui héberge la machine virtuelle et ses données seront perdues si la machine virtuelle est suppression initiale. Le lecteur D est destiné au stockage temporaire uniquement. Sous Linux, Azure « généralement » (mais pas toujours) expose le disque local temporaire en tant que périphérique en mode bloc /dev/sdb. Il est souvent chargé par l’Agent de Linux Azure en tant que points de montage /mnt/resource ou /mnt (configurables via /etc/waagent.conf).

###<a name="partitioning"></a>Partition

Azure en mode natif comprend les niveaux dans une application PaaS (rôle web et le rôle de collaborateur) et par conséquent correctement distribuer les domaines défaillance et mise à jour. En revanche, les niveaux dans une infrastructure comme une application de service (IaaS) doivent être définis manuellement les ensembles de disponibilité. Jeux de disponibilité est requises pour accord sous IaaS.

![Disponibilité définit pour les machines virtuelles Azure](./media/resiliency-technical-guidance-recovery-local-failures/partitioning-2.png)

Dans le diagramme précédent, la couche Internet Information Services (IIS) (qui fonctionne comme un niveau de l’application web) et la couche SQL (qui fonctionne comme un niveau de données) affectés à des ensembles de disponibilité différentes. Ainsi que toutes les instances de chaque niveau ont redondance matérielle en répartir machines virtuelles domaines d’erreur, et que les niveaux entière n’est pas pris vers le bas au cours d’une mise à jour.

###<a name="load-balancing"></a>Équilibrage de charge

Si les ordinateurs virtuels doivent avoir le trafic répercuté sur les, vous devez regrouper les ordinateurs virtuels dans une application et charge le solde sur un point de terminaison TCP ou UDP spécifique. Pour plus d’informations, voir [l’équilibrage de charge machines virtuelles](../virtual-machines/virtual-machines-linux-load-balance.md). Si les ordinateurs virtuels recevoir une entrée d’une autre source (par exemple, un mécanisme files d’attente), un équilibrage de charge n’est pas obligatoire. L’équilibrage de charge utilise une vérification au niveau de base pour déterminer si le trafic doit être envoyé au nœud. Il est également possible de créer vos propres sondes pour mettre en œuvre des indicateurs d’état spécifiques à l’application qui déterminent si la machine virtuelle devant recevoir le trafic.

##<a name="storage"></a>Espace de stockage

Stockage Azure est le service de données permanentes de référence pour Azure. Il propose blob, table, file d’attente et de stockage sur disque machine virtuelle. Il utilise une combinaison de réplication et de gestion des ressources pour augmenter la disponibilité au sein d’un centre de données unique. La disponibilité de stockage Azure SLA garantit qu’au moins 99,9 % du temps :

* Demandes correctement mis en forme à ajouter, mettre à jour, veuillez lire et supprimer des données seront correctement et correctement traitées.
* Comptes de stockage disposera la connectivité à la passerelle Internet.

###<a name="replication"></a>Réplication

Stockage Azure facilite durabilité de données en stockant plusieurs copies de toutes les données sur des lecteurs différents aux sous-systèmes de stockage physique entièrement indépendante dans la zone. Les données sont répliquées synchrone, et toutes les copies sont validées avant l’écriture est reconnue. Stockage Azure est fortement cohérente, ce qui signifie que lectures sont garanties pour refléter les opérations d’écriture plus récentes. En outre, les copies des données sont en permanence analysés pour détecter et réparer bit Component, une menace souvent ignorée pour l’intégrité des données stockées.

Services des avantages de la réplication uniquement à l’aide de stockage Azure. Le développeur du service ne doit pas effectuent des opérations supplémentaires pour récupérer une panne locale.

###<a name="resource-management"></a>Gestion des ressources

Comptes de stockage créées après mai 2014, peut aller jusqu'à 500 To maximum (la valeur maximale précédente était de 200 To). Si l’espace supplémentaire est requise, applications doivent être conçues pour utiliser plusieurs comptes de stockage.

###<a name="virtual-machine-disks"></a>Machine virtuelle disques

Disque d’un ordinateur virtuel est stocké comme un blob de page dans le stockage Azure, lui donner les mêmes propriétés résistance et extensibilité élevées en tant que stockage d’objets Blob. Cette conception rend les données sur le disque d’un ordinateur virtuel permanente, même si le serveur qui exécute la machine virtuelle échoue et la machine virtuelle doit être redémarrée sur un autre serveur.

##<a name="database"></a>Base de données

###<a name="sql-database"></a>Base de données SQL

Base de données SQL Azure fournit une base de données en tant que service. Il permet aux applications de mise en service rapidement, insérer des données dans et bases de données relationnelles de requête. Il fournit la plupart des fonctionnalités de SQL Server et qu’une fonctionnalité familière tout en analysant la charge de la résilience matérielle, de configuration et de correction.

>[AZURE.NOTE] Base de données SQL Azure ne fournit pas les disparités qui existent une fonctionnalité avec SQL Server. Il est conçu pour répondre à une série d’exigences--qui a été adapté à applications cloud (échelle élastique, base de données en tant que service pour réduire les coûts de maintenance et ainsi de suite). Pour plus d’informations, voir [Choisissez un option de SQL Server cloud : base de données SQL Azure (PaaS) ou SQL Server sur Azure machines virtuelles (IaaS)](../sql-database/sql-database-paas-vs-sql-server-iaas.md).

####<a name="replication"></a>Réplication

Base de données SQL Azure fournit la résilience intégrée pour l’échec du niveau de nœud. Toutes les écritures dans une base de données sont automatiquement répliquées sur deux ou plusieurs nœuds arrière-plan via une technique de valider quorum. (Principal et secondaire au moins un doivent vérifier que l’activité est écrites dans le journal des transactions avant la transaction est considérée comme réussie et renvoie.) Dans le cas d’échec de nœud, la base de données automatiquement bascule vers un réplica secondaire. Cela entraîne une interruption de connexion transitoires pour les applications clientes. Pour cette raison, tous les clients de base de données SQL Azure doivent implémenter une forme de traitement des connexions transitoires. Pour plus d’informations, voir [Réessayer aide spécifique à des services](../best-practices-retry-service-specific.md).

####<a name="resource-management"></a>Gestion des ressources

Lorsque vous avez créé, chaque base de données est configuré avec une limite de taille maximale. La taille maximale disponible est de 1 To (taille limites peuvent varier en fonction de votre niveau de service, consultez la rubrique [niveaux de service et de performances des bases de données SQL Azure](../sql-database/sql-database-resource-limits.md#service-tiers-and-performance-levels). Lorsqu’une base de données atteint sa limite de taille maximale, il refuse des commandes supplémentaires d’insertion ou mise à jour. (L’interrogation et supprimer des données sont toujours possible.)

Dans une base de données, base de données SQL Azure utilise un tissu pour gérer les ressources. Toutefois, au lieu d’un contrôleur de tissu, elle utilise une topologie en anneau pour détecter les erreurs. Chaque réplica dans un cluster comporte deux voisin et est chargé de détecter lorsqu’ils accèdent vers le bas. Lorsqu’un réplica est indisponible, son voisin déclenche un agent reconfiguration pour recréer sur un autre ordinateur. Moteur la limitation est fournie pour s’assurer qu’un serveur logique n’utiliser trop de ressources sur un ordinateur ou dépasse les limites physiques de l’ordinateur.

###<a name="elasticity"></a>Élasticité

Si l’application requiert plus de la limite de base de données de 1 To, il doit implémenter une approche horizontale. Vous évoluer avec la base de données SQL Azure par manuellement partition, également appelés ont des données entre plusieurs bases de données SQL. Cette approche horizontale offre la possibilité d’atteindre croissance coût quasi linéaires avec échelle. Croissance élastique ou la capacité à la demande peut devenir aux coûts incrémentielles selon vos besoins, car les bases de données sont facturés en fonction de la moyenne taille réelle utilisée par jour, non basée sur la taille maximale possible.

##<a name="sql-server-on-virtual-machines"></a>SQL Server sur Machines virtuelles

En installant SQL Server (version 2014 ou version ultérieure) sur le Machines virtuelles Azure, vous pouvez tirer parti des fonctionnalités de disponibilité traditionnel de SQL Server. Ces fonctionnalités incluent les groupes de disponibilité AlwaysOn et la mise en miroir de base de données. Notez que machines virtuelles Azure, le stockage et mise en réseau ont des caractéristiques opérationnelles différentes à un local, non virtualisé l’infrastructure. Une mise en œuvre réussie d’une grande disponibilité/sinistre solution (HA/DR) SQL Server dans Azure nécessite que vous comprenez ces différences et concevez votre solution pour prendre en compte les.

###<a name="high-availability-nodes-in-an-availability-set"></a>Disponibilité nœuds dans un jeu de disponibilité

Lorsque vous implémentez une solution haute disponibilité dans Azure, vous pouvez utiliser la disponibilité définie dans Azure pour placer les nœuds haute disponibilité dans les domaines d’erreur distincte et mettre à niveau des domaines. Pour être clair, le jeu de disponibilité est un concept Azure. Il est recommandé de représenter que vous devez suivre pour vous assurer que vos bases de données sont bien hautement disponibles, si vous utilisez des groupes de disponibilité AlwaysOn, la mise en miroir de base de données ou autre chose. Si vous n’utilisez cette pratique recommandée, vous êtes peut-être en false supposant que votre système est hautement disponible. Mais en réalité, les nœuds peuvent tous échouer simultanément car elles se produisent à placer dans le même domaine d’erreur dans la région Azure.

Cette recommandation n’est pas selon vos besoins avec envoi des journaux. Une fonctionnalité de récupération après sinistre, vous devez vous assurer que les serveurs s’exécutent dans les régions Azure distinctes. Par définition, ces zones sont des domaines d’erreur distincte.

Pour les Azure Cloud Services machines virtuelles déployé via le portail classique se trouver dans le même jeu de disponibilité, vous devez les déployer dans le même Service Cloud. Machines virtuelles déployés via le Gestionnaire de ressources Azure (le portail actuel) n’ont pas cette limitation. Pour portail classique déployé machines virtuelles dans Azure Cloud Service, seuls les nœuds dans le Service Cloud même peuvent participer le même jeu de disponibilité. En outre, les Services de Cloud machines virtuelles doivent être sur le même réseau virtuel pour vous assurer qu’ils conservent leur adresses IP même après avoir service correcteur. Cela permet d’éviter toute interruption de mise à jour DNS.

###<a name="azure-only-high-availability-solutions"></a>Azure seule : solutions de disponibilité

Vous pouvez avoir une solution haute disponibilité pour vos bases de données SQL Server dans Azure à l’aide de groupes de disponibilité AlwaysOn ou la mise en miroir de base de données.

Le diagramme suivant illustre l’architecture de groupes de disponibilité AlwaysOn Azure machines virtuelles en fonctionnement en cours d’exécution. Ce diagramme tiré de l’article détaillé sur ce sujet, [haute disponibilité et reprise pour SQL Server Azure machines virtuelles en fonctionnement](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md).

![Groupes de disponibilité AlwaysOn dans Microsoft Azure](./media/resiliency-technical-guidance-recovery-local-failures/high_availability_solutions-1.png)

Vous pouvez également automatiquement mise en service un groupes de disponibilité AlwaysOn déploiement de bout en bout sur Azure machines virtuelles à l’aide du modèle AlwaysOn dans le portail Azure. Pour plus d’informations, voir [SQL Server AlwaysOn offrant dans Microsoft Azure Portal galerie](https://blogs.technet.microsoft.com/dataplatforminsider/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery/).

Le diagramme suivant illustre l’utilisation de la base de données mise en miroir Azure machines virtuelles en fonctionnement. Elle a été également récupérée à partir de la rubrique profondeur [haute disponibilité et reprise pour SQL Server Azure machines virtuelles en fonctionnement](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md).

![Base de données mise en miroir dans Microsoft Azure](./media/resiliency-technical-guidance-recovery-local-failures/high_availability_solutions-2.png)

>[AZURE.NOTE] Les deux architectures nécessitent un contrôleur de domaine. Toutefois, avec la mise en miroir de base de données, il est possible d’utiliser des certificats de serveur pour éliminer le besoin d’un contrôleur de domaine.

##<a name="other-azure-platform-services"></a>D’autres services de plateforme Azure

Les applications qui sont appuient sur Azure bénéficient des fonctions de plateforme pour récupérer des défaillances locales. Dans certains cas, vous pouvez prendre des actions spécifiques pour accroître la disponibilité pour votre scénario spécifique.

###<a name="service-bus"></a>Bus des services

Pour atténuer une panne temporaire de Bus des services Azure, envisagez de créer une file d’attente côté client résistant. Cela temporairement utilise un mécanisme de stockage de remplacement, local pour stocker les messages qui ne peuvent pas être ajoutés à la file d’attente Bus des services. L’application peut décider comment traiter les messages stockées provisoirement une fois que le service est restauré. Pour plus d’informations, voir [meilleures pratiques pour améliorer les performances à l’aide du Service Bus demandé de messagerie](../service-bus-messaging/service-bus-performance-improvements.md) et [Bus des services (reprise)](./resiliency-technical-guidance-recovery-loss-azure-region.md#other-azure-platform-services).

###<a name="hdinsight"></a>HDInsight

Les données associée Azure HDInsight sont stockées par défaut dans le stockage Blob Azure. Stockage Azure spécifie les propriétés de disponibilité et durabilité pour le stockage Blob. Le traitement de plusieurs nœuds associée Hadoop MapReduce travaux s’exécute sur un transitoires Hadoop Distributed fichier système (HDFS) qui est mis en service lorsque HDInsight a besoin. Résultats d’une tâche MapReduce sont également stockés par défaut dans le stockage Blob Azure, afin que les données traitées sont résistants et restent hautement disponibles une fois le cluster Hadoop est annulé. Pour plus d’informations, voir [HDInsight (reprise)](./resiliency-technical-guidance-recovery-loss-azure-region.md#other-azure-platform-services).

##<a name="checklists-for-local-failures"></a>Aide-mémoire pour les pannes locales

###<a name="cloud-services"></a>Services en nuage

  1. Passez en revue la section Services en nuage de ce document.
  2. Configurez au moins deux instances pour chaque rôle.
  3. Maintenir l’état dans le stockage résistant, pas sur des instances de rôle.
  4. Gérer correctement l’événement StatusCheck.
  5. Ajuster les modifications connexes dans les transactions dès que possible.
  6. Vérifiez que les tâches de rôle de collaborateur sont idempotents et réutilisable.
  7. Continuez à jusqu'à ce qu’ils réussissent les opérations d’appel.
  8. Prendre en compte les stratégies autoscaling.

###<a name="virtual-machines"></a>Machines virtuelles

  1. Passez en revue la section Machines virtuelles de ce document.
  2. N’utilisez pas de lecteur D pour le stockage permanent.
  3. Regroupement d’ordinateurs dans un niveau de service à un ensemble de disponibilité.
  4. Configurer l’équilibrage de charge et sondes facultatifs.

###<a name="storage"></a>Espace de stockage

  1. Passez en revue la section de stockage de ce document.
  2. Utiliser plusieurs comptes de stockage lorsque les données ou la bande passante dépasse les quotas.

###<a name="sql-database"></a>Base de données SQL

  1. Passez en revue la section de base de données SQL de ce document.
  2. Mise en œuvre d’une stratégie de nouvelles tentatives pour gérer les erreurs transitoires.
  3. Utiliser partition/ont comme une stratégie horizontale.

###<a name="sql-server-on-virtual-machines"></a>SQL Server sur Machines virtuelles

  1. Passez en revue SQL Server sur Machines virtuelles section de ce document.
  2. Suivez les recommandations précédentes pour les Machines virtuelles.
  3. Utiliser les fonctionnalités de disponibilité de SQL Server, telles que AlwaysOn.

###<a name="service-bus"></a>Bus des services

  1. Passez en revue la section Bus des services de ce document.
  2. Envisager de créer une file d’attente côté client résistant comme copie de sauvegarde.

###<a name="hdinsight"></a>HDInsight

  1. Passez en revue la section HDInsight de ce document.
  2. Aucune étape davantage de disponibilité est requise pour les échecs locales.

##<a name="next-steps"></a>Étapes suivantes

Cet article fait partie d’une série de [conseils techniques résilience Azure](./resiliency-technical-guidance.md). L’article suivant de cette série est la [reprise d’une interruption de service à l’échelle de la région](./resiliency-technical-guidance-recovery-loss-azure-region.md).
