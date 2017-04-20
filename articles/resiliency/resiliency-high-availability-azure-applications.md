<properties
   pageTitle="Disponibilité des applications Azure | Microsoft Azure"
   description="Présentation technique et des informations détaillées sur la conception et création d’applications de disponibilité sur Microsoft Azure."
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

#<a name="high-availability-for-applications-built-on-microsoft-azure"></a>Disponibilité des applications basées sur Microsoft Azure

Une application hautement disponible absorbe variations de disponibilité, la charge et échecs temporaires dans les services qui en dépendent et le matériel. L’application continue de fonctionner à un utilisateur acceptable et un niveau de réponse systémique, telle que définie par leurs besoins ou application au niveau du service accords ().

##<a name="azure-high-availability-features"></a>Fonctionnalités de disponibilité Azure

Azure comporte de nombreuses fonctionnalités de plateforme intégrée qui prennent en charge des applications hautement disponibles. Cette section décrit les principales fonctionnalités. Pour une analyse plus complète de la plateforme, voir [conseils techniques résilience Azure](./resiliency-technical-guidance.md).

###<a name="fabric-controller"></a>Contrôleur de tissu

Contrôleur de tissu Azure les dispositions et surveille l’état des instances cluster Azure. Contrôleur de tissu vérifie l’état du matériel et les logiciels des instances ordinateur hôte et invité. Quand il détecte une défaillance, il applique SLA en déplaçant automatiquement les instances de machine virtuelle. Le concept de défaillance et mise à niveau des domaines supplémentaires prend en charge le SLA cluster.

Lorsque plusieurs instances de rôle du Service Cloud sont déployés, Azure déploie ces instances sur les domaines d’erreur différents. Limite de domaine d’une défaillance est en fait un rack matériel différent dans la même région. Domaines d’erreur réduisent la probabilité qu’une défaillance matérielle localisés va interrompre le service d’une application. Vous ne pouvez pas gérer le nombre de domaines d’erreur qui sont attribuées à certains rôles web ou votre travail. Contrôleur de tissu utilise des ressources dédiées qui sont distinguent des applications hébergées Azure. Il comporte 100 pour cent, car il sert le noyau du système Azure. Elle surveille et gère les instances de rôles au sein de domaines d’erreur.

Le diagramme suivant montre les ressources partagées Azure dont le contrôleur de tissu déploie et gère les domaines défaillance différents.

![Vue simplifiée de pannes de domaine](./media/resiliency-high-availability-azure-applications/fault-domain-isolation.png)

Mise à niveau domaines sont semblables aux domaines défaillance de la fonction, mais ils prennent en charge les mises à jour au lieu d’échecs. Un domaine de mise à niveau est une unité logique de séparation instance qui détermine les instances dans un service particulier passeront à un point dans le temps. Par défaut, pour votre déploiement de service hébergé, cinq domaines mise à niveau sont définies. Toutefois, vous pouvez modifier cette valeur dans le fichier de définition de service. Par exemple, supposons que vous avez huit instances de votre rôle web. Il y a deux instances dans trois domaines mise à niveau et deux instances d’un domaine de mise à niveau. Azure définit la séquence de mise à jour, mais il est basé sur le nombre de domaines mise à niveau. Pour plus d’informations sur les domaines de mise à niveau, voir [mettre à jour un service cloud](../cloud-services/cloud-services-update-azure-service.md).

###<a name="features-in-other-services"></a>Fonctionnalités dans d’autres services

Outre les fonctionnalités de plateforme qui prennent en charge cluster haute disponibilité, Azure intègre des fonctionnalités de disponibilité dans ses autres services. Par exemple, le stockage Azure gère trois répliques de tous les objets blob, table et données file d’attente. Il permet également de l’option de réplication geo stocker les sauvegardes des objets BLOB et des tableaux dans une zone secondaire. Le réseau de distribution de contenu Azure permet d’objets BLOB à mettre en cache dans le monde entier pour redondance et extensibilité. Base de données SQL Azure conserve plusieurs réplicas également.

Outre la série [conseils techniques résilience](https://aka.ms/bctechguide) des articles, consultez le livre [Meilleures pratiques pour la création de Services à grande échelle sur Azure Cloud Services](https://azure.microsoft.com/blog/best-practices-for-designing-large-scale-services-on-windows-azure/) . Ces articles proposent plus d’informations sur les fonctionnalités de disponibilité plateforme Azure.

Bien que Azure propose plusieurs fonctionnalités qui prennent en charge de disponibilité, il est important de comprendre leurs limitations :

- Pour cluster, Azure garantit que vos rôles sont disponibles et en cours d’exécution, mais il ne sait pas si votre application est en cours d’exécution ou surchargé.
- Pour la base de données SQL Azure, les données sont répliquées synchrone dans la zone. Vous pouvez choisir geo-réplication active, qui permet de jusqu'à quatre copies de base de données supplémentaires dans le même (ou les différentes régions). Celles-ci de base de données n’est pas des sauvegardes point dans le temps. Bases de données SQL fournissez des fonctions de sauvegarde de point dans le temps. Pour en savoir plus sur les fonctionnalités de base de données SQL-instantanées, lisez [Point de base de données SQL Azure dans la zone heure restaurer](https://azure.microsoft.com/blog/azure-sql-database-point-in-time-restore/).
- Espace de stockage Azure, données de tables et des objets blob sont répliquées par défaut pour une autre zone. Toutefois, vous ne pouvez pas accéder les réplicas jusqu'à ce que Microsoft décide de basculer vers l’autre site. Basculement de région uniquement dans le cas d’une interruption de service à l’échelle de la région prolongée, et il n’existe pas de SLA pour le temps de geo basculement. Il est également important de noter que toute corruption de données double rapidement aux réplicas.

Pour ces raisons, vous devez compléter les fonctionnalités de disponibilité plateforme avec des fonctionnalités de disponibilité spécifiques à l’application. Fonctionnalités spécifiques à l’application disponibilité incluent la fonctionnalité de capture instantanée blob pour créer des sauvegardes point-à-temps des données blob.

###<a name="availability-sets-for-azure-virtual-machines"></a>Disponibilité définit pour le Machines virtuelles Azure

La majorité de cet article se concentre sur les services en nuage, qui utilisent une plateforme comme un modèle de service (PaaS). Cependant, il existe également des fonctionnalités de disponibilité spécifiques pour Azure Machines virtuelles, qui utilise une infrastructure comme un modèle de service (IaaS). Pour obtenir une haute disponibilité avec des Machines virtuelles, vous devez utiliser des ensembles de disponibilité. Un ensemble de disponibilité sert une fonction similaire aux domaines défaillance et mise à niveau. Au sein d’un ensemble de disponibilité, Azure positionne les machines virtuelles d’une manière qui empêche défaillances matérielles localisés et activités de maintenance d’à récupérer tous les ordinateurs de ce groupe. Jeux de disponibilité est nécessaires pour atteindre le contrat SLA Azure pour la disponibilité des Machines virtuelles.

Le diagramme suivant fournit une représentation de deux jeux de disponibilité qui web de groupe et machines virtuelles SQL Server, respectivement.

![Disponibilité définit pour le Machines virtuelles Azure](./media/resiliency-high-availability-azure-applications/availability-set-for-azure-virtual-machines.png)

>[AZURE.NOTE] Dans le diagramme précédent, SQL Server est installé et exécuté sur des machines virtuelles. Ceci est différent de la discussion précédente de base de données SQL Azure qui fournit une base de données sous forme d’un service géré.

##<a name="application-strategies-for-high-availability"></a>Stratégies d’application de disponibilité

La plupart des stratégies d’application de disponibilité impliquent redondance ou la suppression du disque durs dépendances entre les composants d’application. Conception de l’application doit prendre en charge la tolérance de panne au cours des interruptions interruption du Azure ou les services tiers. Les sections suivantes décrivent les modèles d’application pour augmenter la disponibilité de vos services cloud.

###<a name="asynchronous-communication-and-durable-queues"></a>Communication asynchrone et résistants files d’attente

Envisagez une communication asynchrone entre services souple pour accroître la disponibilité dans les applications Azure. Dans ce modèle, écrire des messages dans les files d’attente de stockage ou files d’attente de Bus des services Azure pour un traitement ultérieur. Lorsque vous écrivez le message dans la file d’attente, le contrôle retourne immédiatement à l’expéditeur du message. Un autre niveau de l’application gère le message traitement, généralement déployé comme un rôle de collaborateur. Si le rôle de travail s’arrête, les messages s’accumuler dans la file d’attente jusqu'à ce que le service de traitement est restauré. Dans la mesure où la file d’attente est disponible, il n’existe pas de dépendance directe entre l’expéditeur frontale et le processeur de messages. Cela supprime l’exigence d’appels de service synchrone qui peut être une critique débit dans les applications distribuées.

Une variation de cette utilise le stockage Azure (objets BLOB, les tableaux, files d’attente) ou files d’attente de Bus des services comme emplacement de basculement pour les appels de base de données endommagée. Par exemple, un appel synchrone au sein d’une application à un autre service (par exemple, base de données SQL Azure) échoue à plusieurs reprises. Vous serez peut-être en mesure de sérialiser ces données en stockage résistant. Ultérieurement lorsque le service ou une base de données est en ligne, l’application peut la renvoyer la demande à partir du stockage. La différence dans ce modèle est que l’emplacement intermédiaire n’est pas une partie du flux de travail application constante. Il est utilisé uniquement dans les scénarios d’échec.

Dans les deux scénarios, communication asynchrone et le stockage intermédiaire empêchent un service de données principale en panne interrompre l’ensemble de l’application. Files d’attente servent un intermédiaire logique. Pour obtenir des instructions sur le choix du service files d’attente correct, voir [Azure files d’attente et Bus des services Azure--comparées et différence entre et](../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md).

###<a name="fault-detection-and-retry-logic"></a>Logique de détection et réessayer de défaillance

Un point clé dans la conception d’application hautement disponible consiste à utiliser une logique de nouvelle tentative dans le code traiter normalement un service est temporairement vers le bas. Le [Bloc d’Application de gestion des pannes transitoires](https://msdn.microsoft.com/library/hh680934.aspx), développés par l’équipe Microsoft Patterns et pratiques, assiste les développeurs d’applications dans ce processus. Le mot « transitoires » signifie un problème temporaire qui dure uniquement relativement peu de temps. Dans le cadre de cet article, gérer les erreurs transitoires fait partie du développement d’une application hautement disponible. Exemples de conditions temporaires incluent les erreurs de réseau intermittents et connexions de base de données perdues.

Le bloc d’Application de gestion des pannes transitoires est un moyen simple vous permettant de gérer les échecs au sein de votre code de façon appropriée. Vous pouvez l’utiliser pour améliorer la disponibilité de vos applications en ajoutant une logique de gestion des erreurs transitoire robuste. Dans la plupart des cas, logique des nouvelles tentatives gère l’interruption brève et reconnecte l’expéditeur et le destinataire après une ou plusieurs tentatives. Une tentative réussie accède généralement inaperçue aux utilisateurs de l’application.

Les développeurs ont trois options permettant de gérer la logique de nouvelle tentative : intervalle incrémentiel, fixe et exponentielle. Attend incrémentielles plus avant chaque nouvelle tentative de manière croissante linéaire (par exemple, 1, 2, 3 et 4 secondes). Intervalle fixe attend le même laps de temps entre chaque tentative (par exemple, 2 secondes). Pour une option plus aléatoire, la fonction exponentielle interruption attend plus entre les essais. Toutefois, elle utilise le comportement exponentiel (par exemple, 2, 4, 8 et 16 secondes).

La stratégie de haut niveau au sein de votre code est la suivante :

1. Définir votre stratégie de nouvelles tentatives et la stratégie.
1. Recommencez l’opération qui peut entraîner une défaillance transitoire.
1. En cas de panne transitoire, appeler la stratégie de réessayer.
1. Si toutes les tentatives échouent, intercepter une exception finale.

Tester votre logique de nouvelle tentative dans échecs simulés pour vous assurer que les nouvelles tentatives sur opérations successives n’aient pas dans un délai long inattendue. Procédez comme suit avant de décider d’échec de la tâche globale.

###<a name="reference-data-pattern-for-high-availability"></a>Modèle de données de référence de disponibilité

Données de référence sont les données en lecture seule d’une application. Ces données fournissent le contexte d’entreprise dans lequel l’application génère des données de transactions au cours d’une opération d’entreprise. Données sont une fonction de point dans le temps des données de référence. Par conséquent, son intégrité dépend de la capture instantanée des données de référence au moment de la transaction. Il s’agit d’une définition vague quelque peu, mais il devrait être suffisant pour notre objectif ici.

Données de référence dans le contexte d’une application sont nécessaires pour le fonctionnement de l’application. Les applications respectifs créer et gérer des données de référence ; systèmes de gestion (MDM) des données de base effectuent souvent cette fonction. Ces systèmes sont responsables de cycle de vie des données de référence. Exemples de données de référence incluent catalogue produit, principal employé, masque des composants et masque des équipements. Données de référence peuvent également issus en dehors de l’organisation, par exemple, les codes postaux ou taux des taxes. Stratégies d’augmentation de la disponibilité des données de référence sont généralement moins difficiles que celles des données. Données de référence ont l’avantage d’être principalement immuable.

Vous pouvez apporter Azure rôles web et de travail qui utilisent les données de référence autonome lors de l’exécution en déployant les données de référence ainsi que l’application. Si la taille du stockage local permet d’un tel déploiement, il s’agit d’un état idéal. Bases de données incorporés (SQL, NoSQL) ou des fichiers XML déployés sur un système de fichiers local aidera avec l’autonomie des unités de l’échelle cluster Azure. Toutefois, vous devez avoir un mécanisme pour mettre à jour les données dans chaque rôle sans redéploiement. Pour ce faire, placez les mises à jour les données de référence à un point de terminaison du stockage de cloud (par exemple, stockage d’objets Blob Azure ou base de données SQL). Ajouter le code à chaque rôle qui télécharge les mises à jour des données dans les nœuds de calcul au démarrage de rôle. Vous pouvez également ajouter du code qui permet à un administrateur effectuer un téléchargement forcé dans les instances de rôle.

Pour augmenter la disponibilité, les rôles doivent également contenir un ensemble de données de référence en cas d’espace de stockage vers le bas. Cela permet des rôles commencer avec un ensemble de base de données de référence jusqu'à ce que la ressource de stockage devient disponible pour les mises à jour.

![Haute disponibilité des applications via les nœuds de calcul autonome](./media/resiliency-high-availability-azure-applications/application-high-availability-through-autonomous-compute-nodes.png)

Un compte de ce modèle est le déploiement et la vitesse de démarrage pour vos rôles. Si vous déployez ou le téléchargement de grandes quantités de données de référence au démarrage, cela peut augmenter la durée de tourner nouveaux déploiements ou instances de rôle. Il peut s’agir d’un compromis acceptable pour l’autonomie de présentant les données de référence immédiatement disponibles sur chaque rôle plutôt que d’en fonction des services de stockage externe.

###<a name="transactional-data-pattern-for-high-availability"></a>Modèle de données de disponibilité

Données sont les données générés par l’application dans un contexte d’entreprise. Données sont une combinaison de l’ensemble des processus d’entreprise qui mettent en œuvre, l’application et les données de référence qui prend en charge ces processus. Exemples de données peuvent inclure des commandes, les bordereaux d’expédition avancées, les factures et les opportunités de gestion des relations client. Les données de transactions générées par conséquent seront chargées vers des systèmes externes pour l’enregistrement ou pour un traitement approfondi.

N’oubliez pas cette référence données peuvent changer dans les systèmes qui sont responsables de ces données. Pour cette raison, données doivent enregistrer le contexte de données point-à-temps référence afin qu’il comporte des dépendances externes minimales pour sa cohérence sémantique. Par exemple, considérez la suppression d’un produit à partir du catalogue quelques mois après qu’une commande est exécutée. La meilleure solution consiste à incorporer la transaction autant contexte de données de référence que possible. Cela préserve la sémantique associée à la transaction, même si les données de référence est modifié après que la transaction est capturée.

Comme indiqué précédemment, architectures qui utilisent couplage faible et communication asynchrone eux-mêmes prêtent permettant une plus grande disponibilité. Cela est vrai pour les données également, mais la mise en œuvre est plus complexe. Notions transactions traditionnelles s’appuient généralement sur la base de données pour garantir la transaction. Lorsque vous introduisez couches intermédiaires, le code de l’application doit gérer correctement les données dans les différentes couches pour garantir la cohérence et durabilité suffisantes.

La séquence suivante décrit un flux de travail qui sépare la capture des données à partir de son traitement :

1. Nœud de calcul Web : présenter font référence aux données.
1. Stockage externe : enregistrer les données de transactions intermédiaires.
1. Nœud de calcul Web : finaliser la transaction de l’utilisateur final.
1. Nœud de calcul Web : envoyer les données de transactions terminées, ainsi que le contexte de données de référence, vers le stockage résistant temporaire qui est assuré de fournir une réponse prévisible.
1. Nœud de calcul Web : signaler l’achèvement de l’utilisateur final de la transaction.
1. Nœud de calcul en arrière-plan : extraire les données de transactions, permis de procéder, le cas échéant et l’envoyer à son emplacement de stockage final dans le système actuel.

Le diagramme suivant montre une implémentation possible de cette conception dans un service cloud hébergé Azure.

![Disponibilité élevée via couplage faible](./media/resiliency-disaster-recovery-high-availability-azure-applications/application-high-availability-through-loose-coupling.png)

Les flèches en pointillés dans le diagramme précédent indiquent le traitement asynchrone. Le rôle web frontaux n’est pas prenant en charge du traitement asynchrone. Cela permet d’accéder au stockage de la transaction à sa destination finale en ce qui concerne le système actuel. En raison de la latence présente ce modèle asynchrone, les données de transactions ne sont pas immédiatement disponibles pour la requête. Par conséquent, chaque unité de données transactions doit être enregistré dans un cache ou une session de l’utilisateur pour répondre à l’interface utilisateur immédiate besoins.

Le rôle web est autonome du reste de l’infrastructure. Son profil de disponibilité est une combinaison du rôle web et la file d’attente Azure et pas l’ensemble de l’infrastructure. En plus de disponibilité, cette approche permet le rôle web à l’échelle horizontalement, indépendamment de celles du stockage principale. Ce modèle de disponibilité peut avoir un impact sur le coût des opérations. Autres composants comme Azure files d’attente et les rôles de travail peuvent affecter les coûts d’utilisation mensuel.

Notez que le diagramme précédent affiche une implémentation de cette approche découplée de données. Il existe de nombreuses autres mises en œuvre possibles. La liste suivante fournit des alternatives :

 * Un rôle de collaborateur peut être placé entre le rôle web et la file d’attente de stockage.
 * Une file d’attente Bus de Service peut être utilisé à la place d’une file d’attente de stockage Azure.
 * La destination finale peut-être stockage Azure ou un fournisseur autre base de données.
 * Cache Azure peut être utilisé à la couche web pour assurer la mise en cache immédiatement après la transaction.

###<a name="scalability-patterns"></a>Modèles d’extensibilité élevées

Il est important de noter que l’extensibilité élevées du service cloud affectent directement la disponibilité. Si l’augmentation de la charge provoque votre service ne répond pas, l’impression de l’utilisateur est que l’application est vers le bas. Observez les meilleures pratiques suivantes pour une extensibilité en fonction de votre charge de l’application attendus et ses attentes futures. L’échelle plus élevée implique de nombreuses considérations, telles que l’utilisation d’un ou plusieurs comptes de stockage, partage entre plusieurs bases de données et mise en cache des stratégies. Pour une analyse détaillée de ces modèles, voir [Meilleures pratiques pour la création de Services à grande échelle sur Azure Cloud Services](https://azure.microsoft.com/blog/best-practices-for-designing-large-scale-services-on-windows-azure/).

##<a name="next-steps"></a>Étapes suivantes

Cet article fait partie d’une série d’articles axée sur [sinistre et la disponibilité des applications basées sur Microsoft Azure](./resiliency-disaster-recovery-high-availability-azure-applications.md). L’article suivant de cette série est [reprise des applications basées sur Microsoft Azure](./resiliency-disaster-recovery-azure-applications.md).
