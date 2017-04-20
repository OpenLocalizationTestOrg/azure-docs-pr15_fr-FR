<properties
   pageTitle="Récupération d’urgence pour les applications Azure | Microsoft Azure"
   description="Présentation technique et des informations détaillées sur la conception d’applications pour la reprise sur Microsoft Azure."
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

#<a name="disaster-recovery-for-applications-built-on-microsoft-azure"></a>Reprise des applications basées sur Microsoft Azure

Alors que la disponibilité est sur la gestion de panne temporaire, reprise (DR) est concernant la perte grave de fonctionnalités de l’application. Par exemple, considérez le scénario où une région va vers le bas. Dans ce cas, vous devez disposer d’un plan d’exécuter votre application ou accéder à vos données en dehors de la région Azure. L’exécution de cette offre implique personnes, processus et applications pris en charge qui autorisent le système de la fonction. Les propriétaires d’entreprise et technologie qui définissent le mode de fonctionnement du système à une situation d’urgence également déterminent le niveau de fonctionnalité pour le service durant un incident. Le niveau de fonctionnalité peut prendre plusieurs formes : indisponible entièrement, partiellement disponibles (détérioré fonctionnalité ou retardée de traitement), ou complètement disponible.

##<a name="azure-disaster-recovery-features"></a>Fonctions de récupération d’urgence Azure

Comme avec les considérations relatives à la disponibilité, Azure comporte des [conseils techniques résilience](./resiliency-technical-guidance.md) qui peut prendre en charge sinistre. Il y a également une relation entre les fonctionnalités de disponibilité d’Azure et récupération d’urgence. Par exemple, la gestion des rôles sur plusieurs domaines défaillance augmente la disponibilité d’une application. Sans cette gestion, une défaillance non prise en charge matérielle deviendrait un scénario « urgence ». Pour que l’application correcte de disponibilité des fonctionnalités et stratégies est essentiel de vérification d’urgence votre application. Toutefois, cet article au-delà de problèmes de disponibilité général aux événements de sinistre grave (et plus rares).

##<a name="multiple-datacenter-regions"></a>Plusieurs zones de centre de données

Azure conserve les centres de données dans plusieurs régions du monde entier. Cette infrastructure prend en charge plusieurs récupération d’urgence scénarios, tels que la fourni par le système geo-réplication de stockage Azure régions secondaires. Cela signifie également que vous pouvez facilement et sous-traitants déployer un service cloud à plusieurs emplacements dans le monde entier. Faites la comparaison avec le coût et réduire la lisibilité de l’exécution de vos propres centres de données dans plusieurs zones. Déploiement de services et données dans plusieurs régions, vous aide à protéger votre application des interruptions majeures d’une région spécifique.

##<a name="azure-traffic-manager"></a>Gestionnaire de trafic Azure

Lorsqu’une défaillance régionale se produit, vous devez rediriger le trafic vers déploiements dans une autre région ou les services. Vous pouvez effectuer cette gamme manuellement, mais il est plus efficace d’utiliser un processus automatisé. Azure le trafic Manager est conçu pour cette tâche. Vous pouvez l’utiliser pour gérer automatiquement le basculement du trafic utilisateur dans une autre région en cas d’échec de la région principale. Étant donné que la gestion du trafic est essentiel de la stratégie globale, il est important de comprendre les concepts de base du Gestionnaire de trafic.

Dans le diagramme suivant, les utilisateurs se connectent à une URL qui a spécifié pour le trafic Manager (__http://myATMURL.trafficmanager.net__) et qui extrait les URL de site réel (__http://app1URL.cloudapp.net__ et __http://app2URL.cloudapp.net__). Comment configurer des critères pour lorsque pour acheminer les utilisateurs en fonction, l’utilisateur reçoit vers le site réel approprié lorsque détermine la stratégie. Les options de stratégie sont cyclique, les performances ou basculement. Pour des raisons de cet article, nous sera concernées avec l’option de basculement seulement.

![Routage via le Gestionnaire de trafic Azure](./media/resiliency-disaster-recovery-azure-applications/routing-using-azure-traffic-manager.png)

Lorsque vous configurez le trafic Manager, vous fournir un nouveau préfixe le trafic Gestionnaire DNS. Il s’agit le préfixe d’URL que vous devez fournir à vos utilisateurs pour accéder à votre service. Gestionnaire de trafic résume désormais un niveau au-dessus et pas au niveau de la région d’équilibrage de charge. Le DNS Manager le trafic correspond à un enregistrement CNAME pour tous les déploiements qu’il gère.

Dans le Gestionnaire de trafic, vous spécifiez la priorité des déploiements que les utilisateurs doivent être routés vers en cas d’échec. Le trafic Manager surveille les points de terminaison des déploiements et des notes en cas d’échec du déploiement principal. En échec, le trafic Gestionnaire analyse la liste par ordre de priorité des déploiements et achemine les utilisateurs à la suivante dans la liste.

Bien que le trafic responsable décide où aller dans un basculement, vous pouvez décider si votre domaine basculement est en sommeil ou active pendant que vous n’êtes pas en mode de basculement. Cette fonctionnalité ne comporte rien faire avec le Gestionnaire de trafic Azure. Gestionnaire de trafic détecte une erreur dans le site principal et bascule vers le site de basculement. Gestionnaire de trafic bascule indépendamment si ce site sert actuellement utilisateurs ou non.

Pour plus d’informations sur le fonctionnement du Gestionnaire de trafic Azure, reportez-vous à :

 * [Présentation du Gestionnaire de trafic](../traffic-manager/traffic-manager-overview.md)
 * [Configurer la méthode de routage basculement](../traffic-manager/traffic-manager-configure-failover-routing-method.md)

##<a name="azure-disaster-scenarios"></a>Scénarios d’urgence Azure

Les sections suivantes décrivent les différents types de scénarios d’urgence. Interruptions de service à l’échelle de la région ne sont pas la cause uniquement des échecs de l’application. Une mauvaise conception ou administration erreurs peuvent également entraîner défaillances. Il est important de prendre en compte les causes possibles d’une défaillance lors de la création et le tests phases de votre plan de récupération. Un bon plan tire parti des fonctionnalités Azure et les augmente avec les stratégies spécifiques à l’application. La réponse choisie dépend de l’importance de l’application, la récupération point l’objectif et la récupération heure objectifs.

###<a name="application-failure"></a>Échec de l’application

Gestionnaire de trafic Azure gère automatiquement échecs qui génèrent à partir du logiciel de matériel ou de système d’exploitation sous-jacent dans la machine virtuelle. Azure crée une nouvelle instance du rôle sur un serveur qui fonctionne et ajoute la rotation d’équilibrage de charge. Si le nombre d’instances de rôle est supérieur à 1, Azure entraîne un décalage traitement aux autres instances rôle en cours d’exécution lors du remplacement le nœud a échoué.

Il existe erreurs d’application graves qui s’exécutent indépendamment de celles les échecs de matériel ou de système d’exploitation. L’application peut échouer en raison des exceptions grave dus logique défectueux ou problèmes d’intégrité des données. Vous devez incorporer suffisamment télémétrie dans le code afin qu’un système d’analyse puisse détecter les conditions d’erreur et avertir un administrateur de l’application. Un administrateur qui connaît parfaitement les processus de récupération d’urgence peut prendre une décision d’appeler un processus de basculement. Par ailleurs, un administrateur peut accepter simplement une panne de disponibilité pour résoudre les erreurs critiques.

###<a name="data-corruption"></a>Altération des données

Azure stocke automatiquement vos données de base de données SQL Azure et stockage Azure trois fois redondants au sein des domaines de défaillance différents dans la même région. Si vous utilisez geo réplication, les données sont stockées trois fois supplémentaires dans une zone différente. Toutefois, si vos utilisateurs ou votre application endommage les données dans la copie principale, les données réplique rapidement aux autres copies. Malheureusement, le résultat trois copies de données endommagées.

Pour gérer le risque d’altération des données, vous avez deux possibilités. Tout d’abord, vous pouvez gérer une stratégie de sauvegarde personnalisée. Vous pouvez stocker vos sauvegardes dans Azure ou local, selon vos besoins ou réglementaires de gouvernance. Une autre option consiste à utiliser la nouvelle option Restaurer point-à-temps pour restaurer une base de données SQL. Pour plus d’informations, consultez la section des [stratégies de données pour la reprise](#data-strategies-for-disaster-recovery).

###<a name="network-outage"></a>Panne du réseau

Lorsque des parties du réseau Azure sont inaccessibles, vous ne serez peut-être pas en mesure d’accéder à votre application ou vos données. Si une ou plusieurs instances de rôle ne sont pas disponibles en raison de problèmes réseau, Azure utilise les instances disponibles restants de votre application. Si votre application ne peut pas accéder à ses données en raison d’une panne du réseau Azure, vous pouvez éventuellement exécuter en mode dégradé localement à l’aide des données mises en cache. Vous devez concevoir la stratégie de rétablissement d’exécution en mode dégradé dans votre application. Pour certaines applications, cela peut ne pas être pratique.

Une autre option consiste à stocker des données dans un autre emplacement jusqu'à ce que la connectivité est restaurée. Si mode dégradé n’est pas une option, les autres options sont temps d’arrêt de l’application ou basculer vers une autre zone. La conception d’une application en cours d’exécution en mode dégradé est autant une décision qu’une technique. Cela est décrit plus en détail dans la section [est détérioré fonctionnalités de l’application](#degraded-application-functionality).

###<a name="failure-of-a-dependent-service"></a>Échec d’un service dépendant

Azure fournit plusieurs services peuvent rencontrer des interruptions liées aux tâches périodiques. Prenons l’exemple [Azure Redis Cache](https://azure.microsoft.com/services/cache/) . Ce service multi-client fournit des fonctions de mise en cache à votre application. Il est important de prendre en considération que se passe-t-il dans votre application si le service dépendant n’est pas disponible. Il existe plusieurs façons ce scénario est similaire au scénario de panne du réseau. Toutefois, compte tenu de chaque service entraîne indépendamment des améliorations potentielles à votre plan global.

Cache Redis Azure fournit la mise en cache pour votre application à partir de votre déploiement de service cloud, qui fournit des avantages de la récupération d’urgence. Tout d’abord, le service s’exécute maintenant sur les rôles local dans votre déploiement. Par conséquent, vous pourrez mieux contrôler et de gérer le statut du cache dans le cadre de vos processus de gestion globale pour le service cloud. Ce type de mise en cache expose également les nouvelles fonctionnalités. Une des nouvelles fonctionnalités est disponibilité des données mises en cache. Cela vous permet de conserver les données mises en cache si un seul nœud échoue en conservant des copies sur d’autres nœuds.

Notez que haute disponibilité diminue débit et augmentation de la latence en raison de la mise à jour de la copie secondaire sur écrit. Il double également la quantité de mémoire qui est utilisée pour chaque élément, planifier ainsi que. Cet exemple spécifique montre que chaque service dépendant peut-être avoir des fonctions améliorer votre disponibilité globale et la résistance à défaillance.

Avec chaque service dépendant, vous devez comprendre les implications d’une interruption de service. Dans l’exemple de mise en cache, il est possible d’accéder aux données directement à partir d’une base de données jusqu'à ce que vous restaurez le cache. Ce serait un mode dégradé en termes de performances mais fournit des fonctionnalités complètes en ce qui concerne les données.

###<a name="region-wide-service-disruption"></a>Interruption de service à l’échelle de la région

Les échecs précédents ont été principalement échecs pouvant être gérées au sein de la même région Azure. Toutefois, vous devez également préparer la possibilité qu’il existe une interruption de service de l’ensemble de la région. En cas d’une interruption de service à l’échelle de la région, les copies localement redondants de vos données ne sont pas disponibles. Si vous avez activé geo réplication, il existe trois copies supplémentaires de vos objets BLOB et les tables dans une zone différente. Si Microsoft déclare la région s’est interrompue, Azure reconfigure toutes les entrées DNS à la région répliquées geo.

>[AZURE.NOTE] N’oubliez pas que vous n’avez aucun contrôle sur ce processus, et il se produira uniquement pour interruption de service à l’échelle de la région. Pour cette raison, vous devez compter sur d’autres stratégies de sauvegarde spécifiques à l’application pour atteindre le niveau le plus élevé de disponibilité. Pour plus d’informations, consultez la section des [stratégies de données pour la reprise](#data-strategies-for-disaster-recovery).

###<a name="azure-wide-service-disruption"></a>Interruption de service à l’échelle de Azure

Dans la planification d’urgence, vous devez tenir compte l’ensemble des incidents possibles. Parmi les interruptions de service plus graves implique toutes les régions Azure simultanément. Comme avec d’autres interruptions de service, vous pouvez décider que nous allons les risques d’interruption temporaire dans ce cas. Interruption de service étendu qui s’étalent régions doit être beaucoup plus rares que les interruptions de service isolé qui impliquent régions unique ou les services qui en dépendent.

Toutefois, pour certaines applications critiques, vous pouvez décider qu’il doit être un plan de sauvegarde dans ce scénario également. Le plan de cet événement peut-être inclure basculement aux services dans un [autre cloud](#alternative-cloud) ou un [hybride local et en nuage solution](#hybrid-on-premises-and-cloud-solution).

###<a name="degraded-application-functionality"></a>Fonctionnalités de l’application de dégradé

En général, une application bien conçue utilise une collection de modules de communiquer avec eux via l’implémentation de modèles d’échange d’information souple. Une application compatible avec DR requiert séparation des tâches au niveau du module. Il s’agit pour empêcher l’interruption d’un service dépendant d’interrompre l’ensemble de l’application. Par exemple, considérez une application de commerce web pour Y société. Les modules suivants peuvent constituer l’application :

 * __Catalogue de produits__ permet aux utilisateurs de parcourir les produits.
 * __Panier__ permet aux utilisateurs d’ajouter/supprimer des produits dans son panier.
 * __Commande état__ indique l’état de livraison de commandes de l’utilisateur.
 * __Envoi de la commande__ permet de finaliser la session d’achat par l’envoi de l’ordre de paiement.
 * __Traitement des__ valide l’ordre de l’intégrité des données et effectue une vérification de la disponibilité de la quantité.

Lorsqu’un dépendant d’un module de cette application est indisponible, comment le module fonctionne jusqu'à ce que cette partie récupère ? Un système bien structurée met en œuvre des limites d’isolement via séparation des tâches au moment de la conception et en cours d’exécution. Vous pouvez classer chaque panne comme récupérables et non récupérable. Erreurs non récupérables prendra vers le bas du module, mais vous pouvez limiter une erreur récupérable par le biais alternatives. Comme indiqué dans la section disponibilité, vous pouvez masquer certains problèmes auprès des utilisateurs en gestion des erreurs et autres actions. Au cours d’une interruption de service plus grave, l’application peut être pas complètement disponible. Toutefois, une troisième option consiste à continuer à traiter les utilisateurs en mode dégradé.

Par exemple, si la base de données pour l’hébergement de commandes s’arrête, le module Traitement des perd sa capacité à traiter les transactions de vente. Selon l’architecture, il peut être difficile, voire impossible pour l’envoi de la commande et traitement des parties de l’application pour continuer. Si l’application n’est pas conçue pour gérer ce scénario, l’ensemble de l’application peut se déconnecter.

Toutefois, dans ce scénario, il est possible que les données de produit sont stockées dans un autre emplacement. Dans ce cas, le module de catalogue produit peut toujours être utilisé pour l’affichage des produits. En mode dégradé, l’application continue soit disponible pour les utilisateurs pour les fonctionnalités disponibles à l’affichage du catalogue de produits. Toutefois, d’autres parties de l’application, sont indisponibles, telles que le classement ou stock requêtes.

Autre variante du mode dégradé est axé sur les performances plutôt que des fonctionnalités. Par exemple, considérez un scénario où le catalogue de produits est mis en cache via le Cache Redis Azure. Si la mise en cache n’est plus disponible, l’application peut atteindre directement le stockage server pour récupérer les informations du catalogue produit. Mais cet accès peut être plus lent que la version en cache. Pour cette raison, les performances de l’application sont détérioré jusqu'à ce que le service de mise en cache est entièrement restauré.

Décider combien d’une application continuent à fonction en mode dégradé est une décision commerciale et une décision technique. L’application doit également décider informer les utilisateurs des problèmes temporaires. Dans cet exemple, l’application peut permettre l’affichage de produits et même en les ajoutant à votre panier. Toutefois, lorsque l’utilisateur essaie d’effectuer un achat, l’application vous avertit l’utilisateur que le module de ventes est temporairement inaccessible. Il n’est pas idéale pour le client, mais elle n’empêche pas une interruption de service à l’échelle de l’application.

##<a name="data-strategies-for-disaster-recovery"></a>Stratégies de données pour la reprise

Gestion des données correctement correspond à la zone plus difficile à réaliser correctement dans n’importe quel plan de récupération. Restauration de données est également la partie de la procédure de récupération qui prend en général le plus de temps. Dans les modes dégradation de différentes manières entraînent difficile défis associés à une récupération des données à partir de panne et la cohérence après défaillance.

Un des facteurs est qu’il soit nécessaire de restaurer ou conserver une copie des données de l’application. Vous allez utiliser ces données à des fins transactions sur un site secondaire et de référence. Un local définition nécessite un processus long et coûteux planification pour mettre en œuvre une stratégie de rétablissement multiple région. Pratique, la plupart des fournisseurs de nuage, y compris Azure, permettent de facilement le déploiement des applications à plusieurs zones. Ces zones sont répartis de manière à ce que l’interruption de service multiple région doit être extrêmement rare. La stratégie de gestion des données au sein de régions figure parmi les éléments déterminants de la réussite d’un plan de récupération.

Les sections suivantes décrivent les techniques de récupération d’urgence liées à des sauvegardes de données, les données de référence et les données.

###<a name="backup-and-restore"></a>Sauvegarde et restauration

Des sauvegardes régulières des données d’application peuvent prendre en charge certains scénarios de récupération d’urgence. Les ressources de stockage différents nécessitent différentes techniques.

Pour les niveaux basique, Standard et base de données SQL Premium, vous pouvez tirer parti de restauration point-à-temps pour récupérer votre base de données. Pour plus d’informations, voir [vue d’ensemble : Cloud continuité et base de données reprise avec la base de données SQL](../sql-database/sql-database-business-continuity.md). Une autre option consiste à utiliser Active Geo-réplication de base de données SQL. Cela réplique automatiquement les modifications de base de données à des bases de données secondaires dans la même région Azure ou même dans une zone différente Azure. Cela fournit une alternative potentielle à certaines des techniques de la synchronisation de données plus manuels présentés dans cet article. Pour plus d’informations, voir [vue d’ensemble : SQL de base de données Active Geo réplication](../sql-database/sql-database-geo-replication-overview.md).

Vous pouvez également utiliser une approche plus manuelle pour sauvegarder et restaurer. Utilisez la commande Copier de base de données pour créer une copie de la base de données. Vous devez utiliser cette commande pour obtenir une sauvegarde avec cohérence. Vous pouvez également utiliser le service d’importation/exportation de base de données SQL Azure. Cela prend en charge l’exportation bases de données à des fichiers à DOS qui sont stockés dans le stockage Blob Azure.

La redondance intégrée de stockage Azure crée deux copies du fichier de sauvegarde dans la même région. Toutefois, la fréquence de l’exécution du processus de sauvegarde détermine votre RPO, qui est la quantité de données, que vous risquez de perdre dans les scénarios d’urgence. Par exemple, supposons que vous effectuez une sauvegarde dans la partie supérieure de l’heure et un sinistre deux minutes avant de la partie supérieure de l’heure. Vous perdez 58 minutes de données qui se sont produites après que la dernière sauvegarde a été effectuée. En outre, pour vous protéger contre une interruption de service à l’échelle de la région, vous devez copier les fichiers à DOS à une autre zone. Ensuite, vous avez la possibilité de restaurer les sauvegardes dans la zone secondaire. Pour plus d’informations, voir [vue d’ensemble : Cloud continuité et base de données reprise avec la base de données SQL](../sql-database/sql-database-business-continuity.md).

Espace de stockage Azure, vous pouvez développer votre propre processus de sauvegarde personnalisé ou utiliser un des nombreux outils de sauvegarde tiers. Notez que la plupart des conceptions application complexité supplémentaires où les ressources de stockage référencent mutuellement. Par exemple, considérez une base de données SQL qui comporte une colonne liée à un objet blob dans le stockage Azure. Si les sauvegardes ne sont pas effectuées simultanément, la base de données peut avoir le pointeur vers un blob qui n’était pas sauvegardé avant l’échec. L’application ou du plan de récupération doit implémenter des processus pour gérer cette incohérence après une récupération.

###<a name="reference-data-pattern-for-disaster-recovery"></a>Modèle de données de référence sur la récupération d’urgence

Données de référence sont des données en lecture seule qui prend en charge des fonctionnalités de l’application. Il ne change généralement pas fréquemment. Bien que la sauvegarde et restauration est une méthode pour gérer des interruptions de service à l’échelle de la région, le RTO est relativement long. Lorsque vous déployez l’application à une zone secondaire, certaines stratégies peuvent améliorer le RTO pour les données de référence.

Étant donné que faire référence à des modifications aux données rarement, vous pouvez améliorer le RTO en conservant une copie permanente des données de référence dans la zone secondaire. Cela supprime le temps nécessaire pour restaurer des sauvegardes en cas de sinistre. Pour répondre à la configuration requise récupération urgence multiple région, vous devez déployer l’application et les données de référence gagnante dans plusieurs régions. Comme indiqué dans le [modèle de données de référence de disponibilité](./resiliency-high-availability-azure-applications.md#reference-data-pattern-for-high-availability), vous pouvez déployer des données de référence au rôle lui-même vers le stockage externe ou à une combinaison des deux.

Le modèle de déploiement de données de référence dans les nœuds de cluster satisfait implicitement aux exigences de récupération après sinistre. Déploiement des données de référence sur la base de données SQL nécessite que vous déployez une copie des données de référence pour chaque région. La stratégie même s’applique au stockage Azure. Vous devez déployer une copie de toutes les données de référence qui sont stockées dans le stockage Azure les parties principales et secondaires.

![Publication des données de référence à des zones principales et secondaires](./media/resiliency-disaster-recovery-azure-applications/reference-data-publication-to-both-primary-and-secondary-regions.png)

Vous devez implémenter vos propres routines de sauvegarde spécifiques à l’application pour toutes les données, y compris les données de référence. Copies répliquées geo sur des régions servent uniquement dans une interruption de service à l’échelle de la région. Pour éviter toute interruption étendue, déployer les parties critiques de données de l’application à la zone secondaire. Pour obtenir un exemple de cette topologie, voir le [modèle actif-passif](#active-passive).

###<a name="transactional-data-pattern-for-disaster-recovery"></a>Modèle de données pour la reprise

Implémentation d’une stratégie de mode d’urgence entièrement fonctionnel nécessite réplication asynchrone des données transactions à la zone secondaire. Les fenêtres Délai pratique dans lequel la réplication peut se produire déterminera les caractéristiques RPO de l’application. Vous pouvez toujours récupérer les données qui a été interrompues à partir de la région principale pendant la période de réplication. Vous pourrez également fusionner avec la zone secondaire ultérieurement.

Les exemples d’architecture suivants fournissent des idées sur différentes façons de gestion des données de transactions dans un scénario de basculement. Il est important de noter que ces exemples ne sont pas exhaustives. Par exemple, emplacements de stockage intermédiaire tels que des files d’attente peuvent être remplacés par base de données SQL Azure. Les files d’attente eux-mêmes peuvent être des files d’attente stockage Azure ou Bus des services Azure (voir [Azure files d’attente et Bus des services--comparées et différence entre et](../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md)). Destinations de stockage de serveur peuvent également varier, tels que des tables Azure au lieu de la base de données SQL. En outre, rôles de travail peuvent être insérées en tant qu’intermédiaires dans différentes étapes. L’important est ne pas émuler ces architectures exactement, mais à prendre en considération les diverses solutions de la récupération des données et des modules connexes.

####<a name="replication-of-transactional-data-in-preparation-for-disaster-recovery"></a>Réplication de données transactions en vue de sinistre

Prenez une application qui utilise des files d’attente de stockage Azure pour conserver les données. Cela permet de rôles de travail traiter les données à la base de données dans une architecture découplée transactions. Cette fonctionnalité nécessite les transactions à utiliser une forme de mise en cache temporaire si les rôles frontale exigent la requête immédiate de ces données. Selon le niveau de tolérance de perte de données, vous pouvez choisir de répliquer les files d’attente, la base de données ou toutes les ressources de stockage. Avec la réplication de base de données uniquement, si la région principale s’arrête, vous pouvez toujours récupérer les données dans les files d’attente lors de la région primaire revient.

L’illustration ci-dessous présente une architecture où la base de données est synchronisé entre zones.

![Réplication de données transactions en vue de sinistre](./media/resiliency-disaster-recovery-azure-applications/replicate-transactional-data-in-preparation-for-disaster-recovery.png)

Le plus grand défi à l’implémentation de cette architecture est la stratégie de réplication entre les régions. Le service de synchronisation de données SQL Azure permet ce type de réplication. Toutefois, le service est toujours en mode Aperçu avant et n’est pas recommandé pour les environnements de production. Pour plus d’informations, voir [vue d’ensemble : Cloud continuité et base de données reprise avec la base de données SQL](../sql-database/sql-database-business-continuity.md). Pour les applications de production, vous devez investir dans une solution tierce ou créer votre propre logique de réplication dans le code. Selon l’architecture, la réplication peut-être être bidirectionnelle, qui est également plus complexe.

Une mise en œuvre potentiel peut-être faire l’utilisation de la file d’attente intermédiaire dans l’exemple précédent. Le rôle de collaborateur qui traite les données à la destination du stockage final peut effectuer les modifications dans la région principale et la zone secondaire. Il s’agit pas de tâches simples et Guide pas à pas pour le code de réplication est dépasse le cadre de cet article. Le point important qui est un grand nombre de votre temps et de test doit se concentrer sur la façon dont vous répliquez vos données à la zone secondaire. Traitement supplémentaires et le test permettent de garantir que les processus de basculement et de restauration de gérer correctement les données possibles incohérences ou les transactions en double.

>[AZURE.NOTE] La plupart de ce livre blanc se concentre sur plate-forme en tant que service (PaaS). Toutefois, des options de réplication et disponibilité supplémentaires pour des applications hybrides utilisent Machines virtuelles Azure. Ces applications hybrides utilisent infrastructure en tant que service (IaaS) pour héberger SQL Server sur des machines virtuelles dans Azure. Cela permet de disponibilité traditionnel approches dans SQL Server, tels que les groupes de disponibilité AlwaysOn ou envoi des journaux. Certaines techniques, tels que AlwaysOn, fonctionnent uniquement entre des instances de SQL Server en local et Azure machines virtuelles. Pour plus d’informations, voir [disponibilité et reprise pour SQL Server dans le Machines virtuelles Azure](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md).

####<a name="degraded-application-mode-for-transaction-capture"></a>Mode d’application qui fonctionnement différemment pour l’acquisition de transaction

Envisagez une architecture de deuxième qui fonctionne en mode dégradé. L’application sur la zone secondaire désactive toutes les fonctionnalités, telles que création de rapports, à la décision (BI), ou écoulement files d’attente. Elle accepte uniquement les principaux types de flux de travail transactions, telle que définie par les besoins de l’entreprise. Le système capture toutes les transactions et les écrit aux files d’attente. Le système peut différer le traitement des données pendant la phase initiale de l’interruption de service. Si le système de la région principal est réactivé pendant la période prévue, les rôles de travail dans la région primaire peuvent se vider les files d’attente. Ce processus élimine la nécessité de fusion de base de données. Si l’interruption de service région primaire au-delà de la fenêtre d’interruption, l’application peut démarrer le traitement des files d’attente.

Dans ce scénario, la base de données secondaire contient des données de transactions incrémentielles qui doivent être fusionnées une fois le serveur principal est réactivé. Le diagramme suivant illustre cette stratégie pour stocker temporairement des données jusqu'à ce que la zone primaire est restaurée.

![Mode d’application qui fonctionnement différemment pour l’acquisition de transaction](./media/resiliency-disaster-recovery-azure-applications/degraded-application-mode-for-transaction-capture.png)

Pour plus d’informations techniques de gestion des données pour les applications Azure résistants, voir [secours : conseils pour des Architectures de Cloud résistant](https://channel9.msdn.com/Series/FailSafe).

##<a name="deployment-topologies-for-disaster-recovery"></a>Topologies de déploiement de sinistre

Vous devez préparer les applications critiques la possibilité d’une interruption de service à l’échelle de la région. Pour cela, vous incorporation d’une stratégie de déploiement multiple région dans la barre de planning opérationnel.

Déploiements multiple région peuvent impliquer processus professionnels de l’informatique pour publier les données d’application et de référence sur la zone secondaire après un incident. Si l’application requiert basculement instantanée, le processus de déploiement peut impliquer une installation actif/passif ou un actif/actif. Ce type de déploiement comporte des instances existantes de l’application en cours d’exécution dans la zone de remplacement. Outil du routage tels que le Gestionnaire de trafic Azure fournit des services d’équilibrage de charge au niveau du DNS. Il peut détecter des interruptions de service et acheminer les utilisateurs vers différentes régions lorsque cela est nécessaire.

Partie d’un sinistre Azure réussie est architecture que la récupération dans la solution à partir du début. Le cloud fournit des options supplémentaires pour la récupération de défaillances durant un incident qui ne sont pas disponibles dans un fournisseur d’hébergement traditionnel. En particulier, vous pouvez rapidement et dynamiquement affecter les ressources à une zone différente. Par conséquent, vous ne payez souvent des ressources inactives pendant que vous patientez pour un échec de se produire.

Les sections suivantes décrivent les différentes topologies de déploiement de reprise. En règle générale, il existe un compromis dans une augmentation des coûts ou la complexité pour davantage de disponibilité.

###<a name="single-region-deployment"></a>Déploiement seule région

Un déploiement seule région n’est pas réellement une topologie de récupération après sinistre, mais il est destiné à contraste avec les autres architectures. Déploiements seule région sont courants pour les applications dans Azure. Toutefois, ce type de déploiement n’est pas un candidat grave pour un plan de récupération.

Le diagramme suivant illustre une application en cours d’exécution dans une seule région Azure. Azure le trafic responsable et l’utilisation des domaines de défaillance et mise à niveau augmentent la disponibilité de l’application dans la zone.

![Déploiement seule région](./media/resiliency-disaster-recovery-azure-applications/single-region-deployment.png)

Dans ce cas, il est évident que la base de données est un point de défaillance unique. Même si Azure réplique les données dans les domaines de défaillance différents réplicas internes, tout cela se produit dans la même région. L’application ne peut pas face à une panne grave. Si la région s’arrête, tous les domaines de défaillance accédez vers le bas, y compris toutes les instances de service et les ressources de stockage.

Pour tous les applications moins critiques, vous devez devise un plan de déploiement de vos applications dans plusieurs régions. Vous devez également prendre en considération RTO et coût contraintes en ce qui concerne la topologie de déploiement à utiliser.

Jetons un coup désormais à des modèles spécifiques pour prendre en charge le basculement entre différentes régions. Ces exemples tous les utilisent deux régions pour décrire le processus.

###<a name="redeployment-to-a-secondary-azure-region"></a>Redéploiement à une zone Azure secondaire

Dans le modèle de redéploiement à une zone secondaire, uniquement la région principale comporte des applications et bases de données en cours d’exécution. La zone secondaire n’a pas étée configurée pour un basculement automatique. Donc en cas de sinistre, tournent tous les composants du service dans la nouvelle zone. Cela inclut le téléchargement d’un service cloud dans Azure, déploiement du service cloud, restaurer les données et la modification DNS afin de rediriger le trafic.

Bien que ce soit la plus économique des options multiple région, il présente les caractéristiques RTO pires. Dans ce modèle, les sauvegardes service package et base de données sont stockées localement ou dans l’instance de stockage Blob Azure de la zone secondaire. Toutefois, vous devez déployer un nouveau service et restaurer les données avant de le réutiliser opération. Même si vous automatisez entièrement le transfert de données à partir du stockage de sauvegarde, faisant pivoter l’environnement de base de données nouvelle consomme beaucoup de temps. Déplacement de données à partir du stockage disque de sauvegarde à la base de données vide dans la zone secondaire est la partie plus cher du processus de restauration. Vous devez faire ceci, cependant, pour mettre la nouvelle base de données à un état opérationnel, car il n’est pas dupliqué.

La meilleure approche consiste à stocker les packages de service dans le stockage Blob dans la zone secondaire. Cela supprime le besoin de télécharger le package vers Azure, ce qui correspond à ce qui se passe lorsque vous déployez à partir d’un ordinateur de développement local. Vous pouvez rapidement déployer les packages de service à un service en nuage depuis le stockage Blob à l’aide des scripts PowerShell.

Cette option est utile que pour les applications non critiques qui peuvent tolérer une augmentation du RTO. Par exemple, cela peut fonctionner pour une application qui peut être arrêtée pour plusieurs heures mais doit s’exécuter à nouveau dans les 24 heures.

![Redéploiement à une zone Azure secondaire](./media/resiliency-disaster-recovery-azure-applications/redeploy-to-a-secondary-azure-region.png)

###<a name="active-passive"></a>Actif-passif

Le modèle actif-passif est le choix de préférence de nombreuses sociétés. Ce modèle fournit des améliorations pour le RTO avec une petite augmentation du coût de la motif redéploiement.
Dans ce scénario, il est à nouveau un serveur principal et une région Azure secondaire. Tout le trafic accède au déploiement actif sur la zone principale. La zone secondaire est préparée mieux sinistre parce que la base de données est en cours d’exécution sur les deux régions. En outre, un mécanisme de synchronisation est en place entre elles. Cette approche secours implique deux variantes : une approche de base de données uniquement ou un déploiement complet dans la zone secondaire.

####<a name="database-only"></a>Base de données uniquement

Dans la première version du modèle actif-passif, uniquement la région principale comporte une application de service cloud déployés. Toutefois, contrairement au modèle redéploiement, les deux régions sont synchronisées avec le contenu de la base de données. (Pour plus d’informations, consultez la section modèle de [données pour la reprise](#transactional-data-pattern-for-disaster-recovery).) Lorsqu’un incident se produit, il vous faut impérativement moins d’activation. Vous démarrez l’application dans la zone secondaire, modifiez les chaînes de connexion à la nouvelle base de données et modifiez les entrées DNS afin de rediriger le trafic.

Comme le modèle redéploiement, doit déjà stockées les packages de service de stockage d’objets Blob Azure dans la région secondaire pour un déploiement plus rapide. À la différence du modèle redéploiement, vous ne subir la majorité de la charge nécessitant une opération de restauration de base de données. La base de données est prêt et en cours d’exécution. Cette opération enregistre une quantité importante de temps, ce qui en fait un motif DR économique. Il est également le modèle DR les plus populaires.

![Base de données actif-passif, uniquement](./media/resiliency-disaster-recovery-azure-applications/active-passive-database-only.png)

####<a name="full-replica"></a>Réplica complet

Dans la seconde version du modèle actif-passif, la région principale et la région secondaire ont un déploiement complet. Ce déploiement comprend les services de cloud et une base de données synchronisée. Toutefois, uniquement la région primaire traite activement les demandes de réseau aux utilisateurs. La zone secondaire devient active uniquement lorsque la région principale des expériences d’une interruption de service. Dans ce cas, toutes les nouvelles demandes de réseau acheminer vers la zone secondaire. Gestionnaire de trafic Azure peut gérer cette basculement automatiquement.

Basculement plus rapidement que la variation de base de données, car les services sont déjà déployés. Ce modèle fournit un RTO très faible. Le basculement secondaire région doit être prêt à être envoyé immédiatement après échec de la région principale.

Ainsi qu’un délai de réponse plus rapide, ce modèle présente l’avantage de la réservation et déploiement de services de sauvegarde. Vous n’avez pas à vous soucier une région n’ayant ne pas l’espace à allouer des instances en cas d’urgence. Ceci est important si votre région Azure secondaire est pratiquement la capacité. Il n’existe aucune garantie (contrat de niveau de service) que vous pourrez instantanément déployer un certain nombre de nouveaux services cloud dans n’importe quelle région.

Pour le temps de réponse plus rapide avec ce modèle, vous devez disposer échelle similaire (nombre d’instances de rôle) dans les zones principales et secondaires. Malgré les avantages, inutilisée cluster instances d’un abonnement payant est coûteux et cela peut ne pas être le plus prudent choix financière. Pour cette raison, il est plus courant d’utiliser une version allégée légèrement des services en nuage sur la zone secondaire. Vous pouvez rapidement basculer, puis évoluer le déploiement secondaire si nécessaire. Vous devez automatiser le processus de basculement afin qu’après que la région principale n’est pas accessible, vous activer des instances supplémentaires, en fonction de la charge. Cela peut impliquer l’utilisation d’un mécanisme autoscaling comme [machine virtuelle échelle définit](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md).

Le diagramme suivant montre le modèle dans lequel les zones principales et secondaires contiennent un service cloud entièrement déployée dans un modèle actif-passif.

![Réplica actif-passif, complet](./media/resiliency-disaster-recovery-azure-applications/active-passive-full-replica.png)

###<a name="active-active"></a>Actif

À présent, vous êtes sans doute à découvrir l’évolution des modèles : en réduisant le RTO augmente les coûts et la complexité. La solution actif effectivement coupé cette tendance en ce qui concerne le coût.

Dans un modèle actif, les services cloud et base de données sont entièrement déployées dans les deux régions. Contrairement au modèle actif-passif, les deux régions recevoir le trafic des utilisateurs. Cette option permet d’obtenir la durée de récupération le plus rapide. Les services sont déjà ajustées pour gérer une partie de la charge au niveau de chaque région. DNS est déjà activé pour utiliser la zone secondaire. Il est plus complexe pour déterminer comment routage des utilisateurs vers la zone appropriée. Planification cyclique est possible. Il est probable qu’à certains utilisateurs utilisent une région spécifique où réside la copie principale de leurs données.

En cas de basculement simplement désactiver DNS à la zone principale. Ceci achemine tout le trafic à la zone secondaire.

Même dans ce modèle, il existe quelques différences. Par exemple, le diagramme suivant montre un modèle dans lequel la région primaire possède la copie de la base de données principale. Les services de cloud dans les deux régions écrivent dans cette base de données principale. Le déploiement secondaire peut lire à partir de la base de données principale ou répliquée. La réplication dans cet exemple se produit unidirectionnelle.

![Actif](./media/resiliency-disaster-recovery-azure-applications/active-active.png)

Il existe un inconvénient à l’architecture actif dans le diagramme précédent. La deuxième zone devez accéder à la base de données dans la première zone, car la copie principale réside à cet endroit. Performances considérablement disparaît lorsque vous accédez à des données à partir d’en dehors d’une région. Dans les appels entre région de base de données, vous devez prendre en compte un type de traitement par lots de stratégie pour améliorer les performances de ces appels. Pour plus d’informations, voir [comment utiliser le traitement par lots pour améliorer les performances de l’application de base de données SQL](../sql-database/sql-database-use-batching-to-improve-performance.md).

Une architecture de remplacement peut impliquer chaque région accéder directement à sa propre base de données. Dans ce modèle, un type de réplication bidirectionnelle est requis pour synchroniser les bases de données dans chaque région.

Dans le modèle actif, vous devrez peut-être pas autant d’instances de la région primaire comme vous le feriez dans le modèle actif-passif. Si vous avez 10 instances sur la zone primaire dans une architecture actif-passif, vous devrez peut-être uniquement 5 dans chaque région dans une architecture actif. Les deux régions désormais partagent la charge. Il peut s’agir d’un économies sur le modèle actif-passif si vous conservez une chaud en attente de la région passive avec 10 instances et en attente de basculement.

Réalisez jusqu'à ce que vous restaurez la région principale, la zone secondaire peut recevoir une brusque lancer une augmentation des nouveaux utilisateurs. S’il existe 10 000 utilisateurs sur chaque serveur lorsque la région primaire rencontre une interruption de service, la zone secondaire comporte soudainement à gérer 20 000 utilisateurs. Règles de la zone secondaire de surveillance doivent détecter cette augmentation et appuyez deux fois les instances dans la zone secondaire. Pour plus d’informations, voir la section sur [la détection de défaillance](#failure-detection).

##<a name="hybrid-on-premises-and-cloud-solution"></a>Hybrides en local et solution en nuage

Une stratégie supplémentaire pour la reprise consiste à concevoir une application hybride qui s’exécute en local et dans le cloud. Selon l’application, la région primaire peut-être être deux emplacements. Prendre en compte les architectures précédente et imaginer la région principale ou secondaire comme un emplacement local.

Il existe certains problèmes dans ces architectures hybride. Tout d’abord, la plupart de cet article a résolu les modèles d’architecture PaaS. Classiques applications PaaS dans Azure s’appuient sur constructions Azure spécifiques tels que le trafic Gestionnaire rôles et les services en nuage. Pour créer une solution en local pour ce type d’application PaaS verser une architecture significatives. Il peut s’agir pas possible d’une gestion ou une perspective de coût.

Toutefois, une solution hybride de reprise est moins défis pour architectures traditionnels qui ont été déplacées simplement dans le cloud. C’est vrai d’architecture qui utilise IaaS. Les applications IaaS utiliser machines virtuelles dans le nuage qui peut avoir équivalents direct en local. Vous pouvez également utiliser les réseaux virtuels pour connecter des ordinateurs dans le nuage avec les ressources de réseau local. Plusieurs possibilités qui ne sont pas possibles avec les applications PaaS seule s’ouvre. Par exemple, SQL Server peut tirer parti des solutions de reprise telles que les groupes de disponibilité AlwaysOn et la mise en miroir de base de données. Pour plus d’informations, voir [disponibilité et reprise pour SQL Server dans des machines virtuelles Azure](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md).

IaaS proposent également un chemin d’accès plus facile pour les applications en local à utiliser Azure en tant que l’option de basculement. Vous pouvez avoir une application entièrement fonctionnelle dans une zone locale existante. Toutefois, que se passe-t-il si vous n’avez pas les ressources à mettre à jour une région géographiquement pour le basculement ? Vous pouvez décider d’utiliser des machines virtuelles et des réseaux virtuels pour obtenir votre application en cours d’exécution dans Azure. Dans ce cas, définir des processus synchroniser des données dans le cloud. Le déploiement d’Azure puis devient la région secondaire à utiliser pour le basculement. La zone principale reste l’application en local. Pour plus d’informations sur IaaS architectures et des fonctions, voir la [documentation de Machines virtuelles](https://azure.microsoft.com/documentation/services/virtual-machines/).

##<a name="alternative-cloud"></a>Nuage de remplacement

Il existe des situations où même la fiabilité du Cloud Microsoft pas forcément conformes aux règles de conformité interne ou les stratégies que votre organisation a besoin. Même les meilleures préparation et création d’implémenter des systèmes de sauvegarde durant un incident se situer courtes s’il existe une interruption de service global d’un fournisseur de services cloud.

Vous souhaiterez comparer les exigences de disponibilité avec le coût et la complexité de disponibilité accrue. Effectuer une analyse de risque et définir les RTO et RPO de votre solution. Si votre application ne peut pas tolérer aucune interruption de service, il peut être utile à prendre en compte à l’aide d’une autre solution cloud. À moins que la totalité de l’Internet s’arrête, une autre solution cloud peut-être toujours disponible si Azure devient globalement inaccessible.

Comme avec le scénario hybride, les déploiements basculement dans les architectures de récupération urgence précédent peuvent également exister au sein d’une autre solution cloud. Sites de remplacement cloud DR doivent être utilisées que pour les solutions dont RTO permet très peu, le cas échéant, temps d’arrêt. Notez qu’une solution qui utilise un site DR en dehors d’Azure nécessite plus de travail pour configurer, développer, déployer et mettre à jour. Il est également plus difficile à mettre en œuvre des meilleures pratiques dans une architecture de manière croisée cloud. Bien que cloud plateformes ont des concepts de haut niveau similaires, les API et les architectures sont différents.

Si vous décidez de fractionner votre DR entre différentes plates-formes, il serait logique d’architecture de couches dans la conception de la solution. Si vous procédez ainsi, vous n’aurez pas besoin développer et maintenir des deux versions différentes d’une même application pour les plateformes nuage différent en cas de panne. Comme avec le scénario hybride, l’utilisation de Machines virtuelles Azure ou Azure conteneur Service peut être plus facile dans ce cas que l’utilisation des modèles de PaaS spécifiques au cloud.

##<a name="automation"></a>Automatisation

Certains des modèles que nous l’avons vu simplement nécessitent l’activation rapide des déploiements en mode hors connexion et restauration des composants spécifiques d’un système. Automatisation, ou le script, prend en charge la fonctionnalité permettant d’activer les ressources à la demande et déployer des solutions rapidement. Dans cet article, liés à la DR automation est mis en équation avec [Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx), mais l' [API REST de gestion des services](https://msdn.microsoft.com/library/azure/ee460799.aspx) est également une option.

Développement de scripts permet de gérer les parties de DR Azure ne traite pas en toute transparence. Cela a l’avantage de produire des résultats cohérents chaque fois, ce qui permet de réduire les risques d’erreur humaine. Ayant des scripts de DR prédéfinis réduisent également le temps pour reconstruire un système et ses composantes au milieu d’un incident. Vous ne voulez pas essayer d’identifier manuellement comment restaurer votre site, bien qu’il soit argent vers le bas et perdre toutes les minutes.

Après avoir créé les scripts, les tester à plusieurs reprises d’a à z. Après avoir vérifié leurs fonctionnalités de base, vérifiez que les tester dans [simulation d’urgence](#disaster-simulation). Cela permet de découvrir les erreurs dans les scripts ou les processus.

Avec l’automation, une meilleure solution consiste à créer un référentiel de scripts PowerShell ou interface de ligne (commande) pour la reprise Azure. Clairement marquer et les classer pour la recherche simple. Désigner une personne pour gérer le référentiel et le contrôle de version des scripts. Les documenter correctement avec explications des paramètres et des exemples d’utilisation de script. Assurez-vous également que vous synchronisez cette documentation avec vos déploiements Azure. Cela souligne l’intérêt d’avoir une personne responsable de toutes les parties du référentiel.

##<a name="failure-detection"></a>Détection de défaillance

Pour gérer correctement les problèmes de disponibilité et de récupération d’urgence, vous devez être en mesure de détecter et diagnostiquer les échecs. Vous devez faire avancée des serveurs et suivi du déploiement afin de déterminer rapidement si un système ou ses parties sont soudainement vers le bas. Outils qui s’affichent à l’état du service cloud et ses dépendances général de surveillance peut effectuer partie de ce travail. [Système Centre 2016](https://www.microsoft.com/en-us/server-cloud/products/system-center-2016/)est un outil de Microsoft. Outils tiers peuvent également fournir des fonctionnalités d’analyse. La plupart des solutions de contrôle suivi des compteurs de performance clés et disponibilité du service.

Bien que ces outils sont essentiels, ils ne remplacent pas la nécessité de planifier la détection des erreurs et création de rapports au sein d’un service cloud. Vous devez planifier l’utilisation correctement Azure Diagnostics. Compteurs de performance personnalisés ou des entrées de journal des événements peuvent également faire partie de la stratégie globale. Cela fournit davantage de données au cours des échecs de diagnostiquer le problème et restaurer toutes les fonctionnalités rapidement. Il fournit également les mesures supplémentaires que les outils d’analyse peuvent utiliser pour déterminer la santé des applications. Pour plus d’informations, voir [Activation des Diagnostics Azure dans Azure Cloud Services](../cloud-services/cloud-services-dotnet-diagnostics.md). Pour une description de la planification globale « modèle de fonctionnement », consultez [secours : conseils pour des Architectures de Cloud résistant](https://channel9.msdn.com/Series/FailSafe).

##<a name="disaster-simulation"></a>Simulation de sinistre

Test de simulation consiste à créer des petites situations réelles dans la surface de travail pour observer la façon dont les membres d’équipe réagissent. Simulations également indiquent l’efficacité les solutions dans le plan de récupération. Effectuer des simulations de telle sorte que les scénarios créés n’interrompent métier réelle lors toujours vous vous sentez comme situations réelles.

Vous pouvez créer l’architecture de « menu général » dans l’application pour simuler manuellement des problèmes de disponibilité d’un type. Par exemple, via un commutateur contours, déclencher des exceptions de base de données d’accès d’un module d’ordre de tri en à l’origine pour fonctionner correctement. Vous pouvez prendre approches légères similaires pour les autres modules au niveau de l’interface réseau.

La simulation met en évidence des problèmes qui ont été adressés mal. Les scénarios simulés doivent être complètement contrôlables. Cela signifie que, même si le plan de récupération a échoué, vous pouvez restaurer la situation dans normal sans endommager significative. Il est également important que vous informez de gestion de haut niveau sur quand et comment les exercices simulation seront exécutées. Ce planning doit inclure des informations sur l’heure ou les ressources qui pourraient être accomplissez pendant l’exécution du test de simulation. Lorsque vous êtes soumettre votre plan de récupération à un test, il est également important de définir comment success est mesuré.

Il existe plusieurs autres techniques que vous pouvez utiliser pour tester les plans de reprise. Toutefois, la plupart d'entre elles est des versions modifiées simplement de ces techniques de base. La principale motivation de ce test consiste à évaluer comment réalisable et comment réalisable le plan de récupération. Tests de reprise se concentre sur les détails pour découvrir holes dans le plan de récupération de base.

##<a name="next-steps"></a>Étapes suivantes

Cet article fait partie d’une série d’articles axée sur [sinistre et la disponibilité des applications basées sur Microsoft Azure](./resiliency-disaster-recovery-high-availability-azure-applications.md). L’article précédent dans cette série est [haute disponibilité des applications basées sur Microsoft Azure](./resiliency-high-availability-azure-applications.md).
