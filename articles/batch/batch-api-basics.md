<properties
    pageTitle="Vue d’ensemble de la fonctionnalité lot Azure pour les développeurs | Microsoft Azure"
    description="Découvrez les fonctionnalités du service lot et ses API à partir d’un point de vue développement."
    services="batch"
    documentationCenter=".net"
    authors="mmacy"
    manager="timlt"
    editor=""/>

<tags
    ms.service="batch"
    ms.devlang="multiple"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="big-compute"
    ms.date="09/29/2016"
    ms.author="marsma"/>

# <a name="batch-feature-overview-for-developers"></a>Présentation des fonctionnalités de lot pour les développeurs

Dans cette vue d’ensemble des composants principaux du service Azure lot, abordent les fonctionnalités du service principal et ressources qui permet aux développeurs de lot pour générer parallèle à grande échelle de calcul solutions.

Si vous êtes développement d’une application de calcul distribuée ou un service problèmes direct [API REST] [ batch_rest_api] appels ou vous utilisez une du [Lot SDK](batch-technical-overview.md#batch-development-apis), vous allez utiliser la plupart des ressources et aux fonctionnalités décrites dans cet article.

> [AZURE.TIP] Pour une présentation plus haut niveau du service lot, voir [Concepts de base du lot Azure](batch-technical-overview.md).

## <a name="batch-service-workflow"></a>Flux de travail lot service

Le flux de travail de haut niveau suivant est classique de presque toutes les applications et services qui utilisent le service lot pour le traitement des charges de travail en parallèle :

1. Téléchargez les **fichiers de données** que vous souhaitez traiter à un [Espace de stockage Azure] [ azure_storage] compte. Lot prend en charge l’accès au stockage Blob Azure et vos tâches peuvent télécharger ces fichiers sur [les nœuds de calcul](#compute-node) lorsque les tâches sont exécutées.

2. Téléchargez les **fichiers d’application** qui exécutent les vos tâches. Ces fichiers peuvent être binaires ou scripts et leurs dépendances et sont exécutées par les tâches de vos tâches. Vos tâches peuvent télécharger ces fichiers à partir de votre compte de stockage, ou vous pouvez utiliser la fonctionnalité [packages d’application](#application-packages) du lot pour le déploiement et la gestion des applications.

3. Créer un [pool](#pool) de nœuds de calcul. Lorsque vous créez un pool, vous spécifiez le nombre de nœuds de calcul pour le pool, leur taille et le système d’exploitation. Lors de l’exécution de chaque tâche de votre projet, il a attribué à l’exécution sur l’un des nœuds dans votre liste.

4. Créer une [tâche](#job). Une tâche gère une collection de tâches. Vous associez chaque tâche vers un pool spécifique où exécutent les tâches de ce projet.

5. Ajouter des [tâches](#task) à la tâche. Chaque tâche s’exécute à l’application ou le script que vous avez téléchargée pour traiter les fichiers de données téléchargés à partir de votre compte de stockage. Que chaque tâche est terminée, il peut télécharger sa sortie au stockage Azure.

6. Surveiller l’avancement de tâche et de récupérer le résultat de la tâche à partir du stockage Azure.

Les sections suivantes décrivent ces et les autres ressources du lot qui permettent à votre scénario de calcul distribué.

> [AZURE.NOTE] Vous besoin d’un [compte lot](batch-account-create-portal.md) à utiliser le service lot. En outre, presque toutes les solutions utilisent un [Stockage Azure] [ azure_storage] du compte de stockage de fichiers et d’extraction. Traitement par lots actuellement prend en charge uniquement **l’objectif général** stockage type de compte, comme décrit à l’étape 5 de [créer un compte de stockage](../storage/storage-create-storage-account.md#create-a-storage-account) dans [les comptes de stockage sur Azure](../storage/storage-create-storage-account.md).

## <a name="batch-service-resources"></a>Ressources du service de traitement par lots

Parmi les ressources suivantes : comptes, calculez nœuds, pools, tâches et les tâches--sont requises par toutes les solutions qui utilisent le service lot. D’autres personnes, tels que les planifications de travaux et des packages d’application, sont des fonctionnalités utiles, mais facultatives.

- [Compte](#account)
- [Nœud de calcul](#compute-node)
- [Pool](#pool)
- [Tâche](#job)

  - [Plannings de travail](#scheduled-jobs)

- [Tâche](#task)

  - [Démarrer la tâche](#start-task)
  - [Gestionnaire des tâches](#job-manager-task)
  - [Tâches de préparation et de publication](#job-preparation-and-release-tasks)
  - [Tâche plusieurs instances (MPI)](#multi-instance-tasks)
  - [Interdépendances de tâches](#task-dependencies)

- [Packages d’application](#application-packages)

## <a name="account"></a>Compte

Un compte lot est une entité identifiée de façon unique au sein du service lot. Tout le traitement est associé à un compte lot. Lorsque vous effectuez les opérations avec le service lot, vous devez le nom du compte et une de ses clés de compte. Vous pouvez [créer un compte Azure lot à l’aide du portail Azure](batch-account-create-portal.md).

## <a name="compute-node"></a>Nœud de calcul

Nœud de calcul est une Azure machine virtuelle () qui s’efforce de traiter une partie de la charge de travail de votre application. La taille d’un nœud détermine le nombre de cœurs processeur, la capacité de mémoire et taille de système de fichiers local allouée au nœud. Vous pouvez créer des pools des nœuds Windows ou Linux à l’aide des Services Cloud Azure Marketplace Machines virtuelles images ou. Consultez la section [Pool](#pool) suivante pour plus d’informations sur ces options.

Nœuds peuvent exécuter un fichier exécutable ou un script qui est pris en charge par l’environnement du système d’exploitation du nœud. Cela inclut \*.exe, \*.cmd, \*.bat et les scripts PowerShell pour Windows--et binaires, gros et les scripts Python par Linux.

Tous les nœuds de calcul dans lot également incluent :

- Standard [arborescence](#files-and-directories) et associées [variables d’environnement](#environment-settings-for-tasks) qui sont disponibles pour référence par les tâches.
- Paramètres de **pare-feu** qui sont configurés pour contrôler l’accès.
- [Accès à distance](#connecting-to-compute-nodes) pour Windows (RDP Remote Desktop Protocol ()) et nœuds Linux (SSH (Secure Shell)).

## <a name="pool"></a>Pool

Un pool est un ensemble de nœuds que votre application s’exécute sur. Le pool peut être créé manuellement par vous-même, ou automatiquement par le service lot lorsque vous spécifiez le travail à effectuer. Vous pouvez créer et gérer un pool qui remplit les besoins en ressources de votre application. Un pool peut être utilisé uniquement par le compte du lot dans lequel il a été créé. Un compte lot peut avoir plusieurs pools.

Créer des pools lot Azure en haut de la plateforme Azure cluster core. Ils fournissent allocation à grande échelle, installation de l’application, la distribution des données, le contrôle d’état et des réglages du nombre de nœuds de calcul au sein d’un pool ([mise à l’échelle](#scaling-compute-resources)).

Chaque nœud est ajouté à un pool est affecté un nom unique et l’adresse IP. Lorsqu’un nœud est supprimé à partir d’un pool, les modifications apportées au système d’exploitation ou les fichiers sont perdues et son nom et l’adresse IP sont publiées pour une utilisation ultérieure. Lorsqu’un nœud quitte un pool, sa durée de vie a dépassé.

Lorsque vous créez un pool, vous pouvez spécifier les attributs suivants :

- Calculer le nœud **système d’exploitation** et **version**

    Vous avez deux possibilités lorsque vous sélectionnez un système d’exploitation pour les nœuds dans votre pool : **Configuration de Machine virtuelle** et **Configuration des Services Cloud**.

    **Configuration de Machine virtuelle** fournit des images Linux et Windows pour calculent les nœuds à partir de [Machines virtuelles Azure Marketplace][vm_marketplace].
    Lorsque vous créez un pool qui contient des nœuds de Configuration de Machine virtuelle, vous devez spécifier non seulement la taille des nœuds, mais également la **référence d’image machine virtuelle** et lot **agent nœud référence (SKU)** soit installé sur les nœuds. Pour plus d’informations sur la spécification de ces propriétés du pool, voir [Linux de mise en service des nœuds dans des pools d’Azure lot de calcul](batch-linux-nodes.md).

    **Configuration des Services cloud** fournit que Windows calculent nœuds *uniquement*. Systèmes d’exploitation disponibles pour des pools de Configuration des Services Cloud figurent dans [les versions du système d’exploitation invité Azure et matrice de compatibilité SDK](../cloud-services/cloud-services-guestos-update-matrix.md). Lorsque vous créez un pool qui contient des nœuds de Services Cloud, vous devez spécifier uniquement la taille du nœud et sa *Famille du système d’exploitation*. Lorsque vous créez des pools de Windows les nœuds de calcul, vous plus fréquemment utiliser les Services en nuage.

    - La *Famille du système d’exploitation* détermine également les versions de .NET sont installées avec le système d’exploitation.
    - Comme avec les rôles de travail au sein de Services Cloud, vous pouvez spécifier une *Version du système d’exploitation* (pour plus d’informations sur les rôles de travail, voir la section [M’avertir à propos des services cloud](../cloud-services/cloud-services-choose-me.md#tell-me-about-cloud-services) dans la [vue d’ensemble des Services Cloud](../cloud-services/cloud-services-choose-me.md)).
    - Comme avec les rôles de travail, nous vous recommandons de que vous spécifiez `*` pour la *Version du système d’exploitation* afin que les nœuds sont mis à niveau automatiquement, et il n’existe aucune solution nécessaire pour prendre en compte que vous venez publié des versions. Le cas d’utilisation principal pour la sélection d’une version du système d’exploitation spécifique consiste à vérifier la compatibilité des applications, qui permet de test pour être exécutés avant d’autoriser la version mise à jour de compatibilité descendante. Après validation, la *Version du système d’exploitation* pour le pool pouvant être mis à jour et la nouvelle image du système d’exploitation peut être installé – toutes les tâches en cours d’exécution sont interrompus et remise en attente.

- **Taille des nœuds**

    **Configuration des Services cloud** cluster nœud tailles sont répertoriées dans les [tailles des Services en nuage](../cloud-services/cloud-services-sizes-specs.md). Lot prend en charge tous les tailles de Services Cloud à l’exception de `ExtraSmall`.

    **Configuration de Machine virtuelle** calculer nœud tailles sont répertoriées dans les [tailles des machines virtuelles dans Azure](../virtual-machines/virtual-machines-linux-sizes.md) (Linux) et les [tailles des machines virtuelles dans Azure](../virtual-machines/virtual-machines-windows-sizes.md) (Windows). Prend en charge lot de toutes tailles machine virtuelle Azure à l’exception de `STANDARD_A0` et celles avec stockage premium (`STANDARD_GS`, `STANDARD_DS`, et `STANDARD_DSV2` série).

    Lorsque vous sélectionnez une taille de nœud cluster, tenez compte des caractéristiques et configuration requise pour les applications que vous exécutez sur les nœuds. Aspects tels que si l’application est multithread et la quantité de mémoire qu’il consomme peut aider à déterminent la taille de nœud plus approprié et plus économique. Il est courant pour sélectionner une taille nœud en supposant une tâche s’exécuter sur un nœud à la fois. Cependant, il est possible d’avoir plusieurs tâches (et par conséquent, plusieurs instances d’application) [s’exécuter en parallèle](batch-parallel-node-tasks.md) sur des nœuds de calcul pendant l’exécution du travail. Dans ce cas, il est courant pour choisir une taille de nœud plus grande pour s’adapter à l’augmentation de la demande d’exécution des tâches en parallèle. Pour plus d’informations, voir [planification des tâches stratégie](#task-scheduling-policy) .

    Tous les nœuds dans un pool ont la même taille. Si envisagez d’exécuter des applications avec différentes requise et/ou charger niveaux, nous vous conseillons d’utiliser des pools distincts.

- **Nombre de postes cibles**

    Ceci est le nombre de nœuds de calcul que vous voulez déployer dans le pool. Ceci est appelé une *cible* , car, dans certains cas, votre pool ne peut pas atteint le nombre de nœuds souhaité. Un pool risquent de ne pas atteindre le nombre de nœuds souhaité si elle atteigne le [quota de base](batch-quota-limit.md#batch-account-quotas) pour votre compte lot--ou s’il existe une formule de mise à l’échelle que vous avez appliqués au pool qui limite le nombre maximal de nœuds (voir la section suivante « Stratégie de mise à l’échelle »).

- **Stratégie de mise à l’échelle**

    En plus de spécifier un nombre de nœuds statique, vous pouvez à la place écrire et appliquer une [mise à l’échelle de formule](#scaling-compute-resources) à un pool. Le service lot évalue votre formule régulièrement et ajuste le nombre de nœuds au sein du pool en fonction de différents pool, tâche et paramètres de la tâche que vous pouvez spécifier.

- **Stratégie de planification des tâches**

    L’option de configuration [max tâches par nœud](batch-parallel-node-tasks.md) détermine le nombre maximal de tâches pouvant être exécutées en parallèle sur chaque nœud de calcul au sein du pool.

    La configuration par défaut est qu’une tâche à la fois exécuté sur un nœud, mais il existe des scénarios dans lesquels il est utile d’avoir plusieurs tâches exécuté sur un nœud simultanément. Consultez le [scénario d’exemple](batch-parallel-node-tasks.md#example-scenario) dans l’article [tâches simultanées nœud](batch-parallel-node-tasks.md) pour voir comment vous pouvez bénéficier à partir de plusieurs tâches par nœud.

    Vous pouvez également spécifier un *type de remplissage* qui détermine si lot répartit uniformément les tâches sur tous les nœuds dans un pool ou modules chaque nœud avec le nombre maximal de tâches avant d’affecter des tâches vers un autre nœud.

- **État de communication** des nœuds de calcul

    Dans la plupart des scénarios, tâches fonctionnent indépendamment et n’avez pas besoin de communiquer avec eux. Il existe cependant certaines applications dans lequel les tâches doivent communiquer, tels que des [scénarios MPI](batch-mpi.md).

    Vous pouvez configurer un groupe pour autoriser la communication entre les nœuds au sein de l’informatique--**communication entre les nœuds**. Lorsque la communication entre les nœuds est activée, nœuds dans des pools Configuration des Services Cloud puissent communiquer avec eux sur les ports supérieures à 1100 et pools de Configuration de Machine virtuelle ne pas restreint le trafic sur n’importe quel port.

    Notez que l’activation de communication entre les nœuds également a un impact sur la position des nœuds dans clusters et peut limiter le nombre maximal de nœuds dans un pool en raison des restrictions de déploiement. Si votre application ne nécessite pas de communication entre les nœuds, le service de lot peut allouer un potentiellement grand nombre de nœuds au pool de nombreux cluster différents et centres de données pour activer augmentée power traitement en parallèle.

- **Démarrer la tâche** pour les nœuds de calcul

    Facultatif *Démarrer tâche* s’exécute sur chaque nœud comme ce nœud rejoint le pool, chaque fois qu’un nœud est redémarré ou que. Cette tâche est particulièrement utile de préparation des nœuds de calcul pour l’exécution des tâches, comme l’installation des applications qui s’exécutent de vos tâches sur les nœuds de calcul.

- **Packages d’application**

    Vous pouvez spécifier [des packages d’application](#application-packages) à déployer les nœuds de calcul dans le pool. Packages d’application fournissent un déploiement simplifié et contrôle de version des applications qui s’exécutent de vos tâches. Packages d’application que vous spécifiez pour un pool sont installés sur tous les nœuds qui joint ce pool, et chaque fois qu’un nœud est redémarré ou que. Packages d’application ne sont actuellement pas pris en charge nœuds de calcul Linux.

- **Configuration du réseau**

    Vous pouvez spécifier l’ID d’un Azure [réseau virtuel (VNet)](../virtual-network/virtual-networks-overview.md) dans lequel les nœuds de calcul du pool doit être créé. Configuration requise pour spécifier un VNet pour votre pool sont accessibles dans [Ajouter un pool à un compte] [ vnet] dans la référence de l’API REST lot.

> [AZURE.IMPORTANT] Tous les comptes lot ont un par défaut **quota** limitant le nombre de **cœurs** (et par conséquent, les nœuds de calcul) dans un compte lot. Vous pouvez trouver les quotas par défaut et les instructions sur la façon d' [augmenter un quota](batch-quota-limit.md#increase-a-quota) (par exemple, le nombre maximal de cœurs dans votre compte lot) dans [les Quotas et les limites pour le service lot Azure](batch-quota-limit.md). Si vous vous trouvez demandant « Pourquoi ne mon pool atteigne plus de X nœuds ? » Ce quota core peut-être être la cause.

## <a name="job"></a>Tâche

Une tâche est un ensemble de tâches. Il gère comment calcul est effectué par ses tâches sur les nœuds de calcul dans un pool.

- La tâche spécifie le **pool** dans lequel le travail doit être exécuté. Vous pouvez créer un nouveau pool correspondant à chaque tâche, ou utiliser un pool de nombreuses tâches. Vous pouvez créer un pool pour chaque tâche qui est associée à un calendrier des tâches, ou pour toutes les tâches qui sont associées à un calendrier des tâches.

- Vous pouvez spécifier une option **priorité de la tâche**. Lorsqu’une tâche est envoyée avec une priorité plus élevée que les tâches qui sont actuellement en cours, les tâches du projet plus urgent sont insérés dans la file d’attente en avance sur les tâches pour les tâches de haute priorité. Tâches en tâches haute priorité déjà en cours d’exécution ne sont pas interrompus.

- Travail **contraintes** vous permet de spécifier certaines limites pour vos tâches :

    Vous pouvez définir une **heure wallclock maximale**, afin que si une tâche s’exécute pendant plus de la durée maximale wallclock est spécifiée, le travail et toutes les tâches sont terminées.

    Lot peut détecter et recommencez tâches a échoué. Vous pouvez spécifier le **nombre maximal de tentatives de tâche** à une contrainte, notamment si une tâche est *toujours* ou *jamais* retentée. Reprise d’une tâche signifie que la tâche est remise en attente pour être exécuté à nouveau.

- Votre application cliente peut ajouter des tâches à une tâche, ou vous pouvez spécifier une [tâche de responsable de projet](#job-manager-task). Une tâche de gestionnaire de projet contient les informations nécessaires créer les tâches requises pour une tâche, avec la Gestionnaire de tâche en cours d’exécution sur l’un des nœuds de calcul dans le pool. La tâche Gestionnaire de projet est gérée en particulier par lot--il est en attente dès que le travail est créé et redémarré en cas d’échec. Une tâche de responsable de projet est *requis* pour les tâches qui sont créés par un [calendrier des tâches](#scheduled-jobs) , car il est le seul moyen pour définir les tâches avant l’instanciation de la tâche.

- Par défaut, les tâches restent à l’état actif lorsque toutes les tâches du projet sont terminées. Vous pouvez modifier ce comportement afin que la tâche est terminée automatiquement lorsque toutes les tâches du travail sont terminées. Définir les propriétés de **onAllTasksComplete** du travail ([OnAllTasksComplete] [ net_onalltaskscomplete] dans .NET lot) à *terminatejob* automatiquement mettre fin à la tâche lorsque toutes les tâches sont dans l’état terminé.

    Notez que le service lot considère qu’une tâche *sans* tâches pour que toutes ses tâches terminées. Par conséquent, cette option est généralement utilisée avec une [tâche de responsable de projet](#job-manager-task). Si vous voulez utiliser les frais de résiliation automatique travail sans gestionnaire de projet, vous devez initialement la valeur de propriété de **onAllTasksComplete** d’une nouvelle tâche *noaction*, puis attribuez-lui *terminatejob* qu’après que vous avez terminé d’ajouter des tâches à la tâche.

### <a name="job-priority"></a>Priorité de la tâche

Vous pouvez affecter une priorité aux tâches que vous créez dans le lot. Le service de traitement par lots utilise la valeur de priorité de la tâche pour déterminer l’ordre de planification des tâches au sein d’un compte (ne doit ne pas être confondus avec une [tâche planifiée](#scheduled-jobs)). La priorité de valeurs comprises entre-1 000 et 1000,-1 000 étant la priorité la plus basse et 1000 le plus élevé. Vous pouvez mettre à jour la priorité d’une tâche à l’aide de la [mise à jour les propriétés d’une tâche] [ rest_update_job] opération (lot reste) ou en modifiant la [CloudJob.Priority] [ net_cloudjob_priority] propriété (lot .NET).

Dans le même compte, plus haute priorité travaux ont planification de la priorité des travaux de haute priorité. Un projet avec une valeur plus haute priorité dans un seul compte n’a pas de planification priorité sur une autre tâche avec une valeur de haute priorité dans un autre compte.

Dans l’ensemble des pools la planification des tâches sont indépendant. Entre différents pools, ne sont pas forcément qu’une priorité plus élevée est planifiée tout d’abord si son pool associé est insuffisant nœuds inactifs. Dans le même pool, travaux avec le même niveau de priorité ont autant de chance de planification.

### <a name="scheduled-jobs"></a>Tâches planifiées

[Calendriers des tâches] [ rest_job_schedules] vous permettent de créer des tâches périodiques au sein du service lot. Planification d’une tâche indique à quel moment exécuter des tâches et inclut les spécifications pour les tâches à exécuter. Vous pouvez spécifier la durée de la planification--combien de temps et lorsque la planification est en vigueur--et à quelle fréquence pendant la période que les tâches doivent être créées.

## <a name="task"></a>Tâche

Une tâche est une unité de calcul qui est associé à une tâche. Il s’exécute sur un nœud. Tâches sont affectées à un nœud pour l’exécution ou en file d’attente jusqu'à ce qu’un nœud de forme libre. En réalité, une tâche s’exécute à l’un ou plusieurs programmes ou des scripts sur un nœud de calcul pour effectuer le travail que vous devez terminé.

Lorsque vous créez une tâche, vous pouvez spécifier :

- La **ligne de commande** de la tâche. Il s’agit de la ligne de commande qui s’exécute votre application ou un script sur le nœud de calcul.

    Il est important de noter que la ligne de commande ne fonctionne pas réellement sous un shell. Par conséquent, il ne peut pas en mode natif tirer parti des fonctionnalités de shell comme [variable d’environnement](#environment-settings-for-tasks) d’extension (Cela inclut les `PATH`). Pour tirer parti de ces fonctionnalités, vous devez appeler au shell dans la ligne de commande, par exemple, en lançant `cmd.exe` sur des nœuds de Windows ou `/bin/sh` sous Linux :

    `cmd /c MyTaskApplication.exe %MY_ENV_VAR%`

    `/bin/sh -c MyTaskApplication $MY_ENV_VAR`

    Si vos tâches requis pour exécuter une application ou un script qui n’est pas dans le nœud `PATH` ou référence à des variables d’environnement, appelez le shell explicitement dans la ligne de commande de tâche.

- **Fichiers de ressources** qui contiennent les données soient traités. Ces fichiers sont automatiquement copiées dans le nœud de stockage d’objets Blob dans un compte de stockage Azure **objectif général** avant l’exécution de ligne de commande de la tâche. Pour plus d’informations, reportez-vous aux sections [Démarrer la tâche](#start-task) et des [fichiers et des répertoires](#files-and-directories).

- Les **variables d’environnement** qui sont requis par votre application. Pour plus d’informations, consultez la section [paramètres d’environnement pour les tâches](#environment-settings-for-tasks) .

- Les **contraintes** sous lequel la tâche doit s’exécuter. Pour exemple, la durée maximale que la tâche est autorisée à exécuter, le nombre maximal de fois qu’une tâche qui a échoué doit être retentée, et la valeur maximale temps que les fichiers dans le répertoire de travail de la tâche sont conservés.

- **Packages d’application** à déployer le nœud de calcul dans laquelle la tâche est planifiée pour exécuter. [Packages d’application](#application-packages) fournissent un déploiement simplifié et contrôle de version des applications qui s’exécutent de vos tâches. Packages d’application au niveau des tâches sont particulièrement utiles dans les environnements pool partagés, où différentes tâches sont exécutées sur un pool et le pool n’est pas supprimé lorsqu’un travail est terminé. Si votre tâche a des tâches moins de nœuds dans la liste, des packages d’application tâche peuvent réduire le transfert de données depuis votre application est déployée uniquement sur les nœuds qui s’exécutent les tâches.

Outre les tâches que vous définissez pour effectuer un calcul sur un nœud, les tâches spéciaux suivants sont également disponibles par le service lot :

- [Démarrer la tâche](#start-task)
- [Gestionnaire des tâches](#job-manager-task)
- [Tâches de préparation et de publication](#job-preparation-and-release-tasks)
- [Tâches de plusieurs instances (MPI)](#multi-instance-tasks)
- [Interdépendances de tâches](#task-dependencies)

### <a name="start-task"></a>Démarrer la tâche

En associant un **Démarrer la tâche** à un pool, vous pouvez préparer l’environnement d’exploitation de ses nœuds. Par exemple, vous pouvez effectuer des actions telles que l’installation des applications qui s’exécutent de vos tâches et démarrer le processus d’arrière-plan. Tâche de démarrage s’exécute chaque fois qu’un nœud démarre, pour tant qu’il reste dans le pool--y compris quand le nœud est d’abord ajoutés au pool et lorsqu’il est redémarré ou que.

Un principal avantage de cette tâche est qu’il peut contenir toutes les informations nécessaires configurer un nœud de calcul et installer les applications qui sont requises pour l’exécution des tâches. Par conséquent, il est aussi simple que d’augmenter le nombre de nœuds dans un pool spécifiant le nombre de nœuds cible nouveau--lot déjà comporte les informations nécessaires pour configurer les nouveaux nœuds et préparer pour l’acceptation des tâches.

Comme avec n’importe quelle tâche Azure lot, vous pouvez spécifier une liste de **fichiers** de ressources dans le [Stockage Azure][azure_storage], en plus d’une **ligne de commande** à exécuter. Traitement par lots copie tout d’abord les fichiers de ressources vers le nœud de stockage Azure, puis exécute la ligne de commande. Pour une tâche de démarrage pool, la liste de fichiers contient généralement l’application de la tâche et ses dépendances.

Toutefois, il peut également inclure des données de référence devant être utilisé par toutes les tâches qui sont exécutent sur le nœud de calcul. Par exemple, la ligne de commande d’une tâche de démarrage peut effectuer un `robocopy` opération pour copier les fichiers d’application (qui ont été spécifiés en tant que fichiers de ressources et téléchargées vers le nœud) de [répertoire de travail de cette tâche](#files-and-directories) vers le [dossier partagé](#files-and-directories), puis exécuter un MSI ou `setup.exe`.

> [AZURE.IMPORTANT] Traitement par lots actuellement prend en charge *uniquement* le type de compte de stockage **usage général** , comme décrit à l’étape 5 de [créer un compte de stockage](../storage/storage-create-storage-account.md#create-a-storage-account) dans [les comptes de stockage sur Azure](../storage/storage-create-storage-account.md). Vos tâches par lots (y compris les tâches standard, tâches de début, tâches de préparation et release tâches) doivent spécifier des fichiers de ressources qui se trouvent *uniquement* dans les comptes de stockage **objectif général** .

Il convient généralement pour le service lot attendre la tâche démarrage se termine avant d’aborder le nœud prêt à être des tâches affectées, mais vous pouvez configurer cette option.

Si une tâche de démarrage échoue sur un nœud de calcul, puis l’état du nœud est mis à jour pour refléter l’échec et le nœud n’est pas affecté toutes les tâches. Une tâche de démarrage peut échouer s’il existe un problème copie ses fichiers de ressources à partir du stockage, ou si le processus exécuté par sa ligne de commande renvoie un code de sortie différente de zéro.

Si vous ajoutez ou mettez à jour de la tâche de démarrer un pool *existant* , vous devez redémarrer ses nœuds de calcul de la tâche de début à appliquer aux nœuds.

### <a name="job-manager-task"></a>Gestionnaire des tâches

Vous généralement utilisez une **tâche de gestionnaire de projet** au contrôle et/ou surveiller l’exécution de la tâche, par exemple, de créer et soumettre les tâches d’un projet, déterminez les tâches supplémentaires d’exécution et déterminer quand le travail est terminée. Toutefois, une tâche de responsable de projet n’est pas limitée à ces activités. Il est une tâche part entière qui peut effectuer les actions nécessaires pour la tâche. Par exemple, une tâche de gestionnaire de projet peut télécharger un fichier spécifié en tant que paramètre, analyser le contenu de ce fichier et soumettre des tâches supplémentaires en fonction de ces contenu.

Une tâche de responsable de projet est démarrée avant que toutes les autres tâches. Il fournit les fonctionnalités suivantes :

- Il est automatiquement soumise en tant que tâche par le service lot lorsque le travail est créé.

- Il doit s’exécuter avant les autres tâches dans un projet.

- Son nœud associé est le dernier à être supprimé à partir d’un pool lorsque le pool est en cours réduite.

- La résiliation peut être liée à la fin de toutes les tâches de la tâche.

- Une tâche de responsable de projet est la priorité la plus élevée lorsqu’il doit être redémarré. Si un nœud inactif n’est pas disponible, le service du lot peut mettre fin à l’une des autres tâches en cours d’exécution du pool pour libérer de l’espace pour la tâche du Gestionnaire de projet exécuter.

- Une tâche du Gestionnaire de projet dans une tâche n’a pas de priorité sur les tâches d’autres tâches. Dans tâches, uniquement les priorités au niveau du projet sont respectées.

### <a name="job-preparation-and-release-tasks"></a>Tâches de préparation et de publication

Lot fournit des tâches de préparation d’un projet pour le programme d’installation de l’exécution du travail avant. Tâches de publication sont appliquent aux maintenance postérieures à la tâche ou de nettoyage.

- **Tâche de préparation du projet**: une tâche de préparation du projet s’exécute sur tous les nœuds de calcul sont planifiées pour exécuter des tâches, avant les autres tâches de projet sont exécutées. Vous pouvez utiliser une tâche de préparation du projet pour copier les données qui sont partagées par toutes les tâches, mais qui sont propre à la tâche, par exemple.
- **Libérer la tâche travail**: lorsqu’une tâche est terminée, une tâche de version s’exécute sur chaque nœud dans le pool exécutées au moins une tâche. Vous pouvez utiliser une tâche de publication pour supprimer des données qui sont copiées à la tâche de préparation du projet, ou pour compresser et télécharger des données de journal de diagnostic, par exemple.

Les deux tâches préparation et version options vous permettent de spécifier une ligne de commande pour exécuter l’appel de la tâche. Ils offrent des fonctionnalités telles que le téléchargement du fichier, l’exécution avec élévation de privilèges, variables d’environnement personnalisées, durée maximale d’exécution, nombre de tentatives et la période de rétention.

Pour plus d’informations sur les tâches de préparation et de version, voir [tâches de préparation et l’achèvement de projet sur Azure lot exécuter les nœuds de calcul](batch-job-prep-release.md).

### <a name="multi-instance-task"></a>Tâches de plusieurs instances

Une [tâche de plusieurs instances](batch-mpi.md) est une tâche qui est configurée pour s’exécuter sur plusieurs nœud de calcul simultanément. Les tâches de plusieurs instances, vous pouvez activer High performance informatiques scénarios nécessitant un groupe de nœuds de calcul qui sont attribuées ensemble pour traiter une seule charge de travail (comme passant Interface MPI (Message)).

Pour plus d’informations sur l’exécution des travaux MPI en lot à l’aide de la bibliothèque .NET lot, consultez [utiliser les tâches de plusieurs instances d’exécuter des applications en passant Interface MPI (Message) dans Azure lot](batch-mpi.md).

### <a name="task-dependencies"></a>Interdépendances de tâches

[Interdépendances de tâches](batch-task-dependencies.md), comme le nom l’indique, permet de spécifier qu’une tâche dépend de l’achèvement d’autres tâches avant son exécution. Cette fonctionnalité prend en charge pour les situations dans lesquelles une tâche « en aval » utilise la sortie d’une tâche « en amont »--ou une tâche en amont effectue une initialisation qui est requis par une tâche en aval. Pour utiliser cette fonctionnalité, vous devez d’abord activer interdépendances de tâches sur votre tâche. Ensuite, pour chaque tâche qui dépend d’un autre (ou autres), vous spécifiez les tâches qui dépend de cette tâche.

Avec les interdépendances de tâches, vous pouvez configurer des scénarios comme suit :

* *taskB* dépend de *taskA* (*taskB* ne commence pas l’exécution jusqu'à ce que *taskA* terminée).
* *taskC* dépend de *taskA* et *taskB*.
* *taskD* varie selon une plage de tâches, telles que les tâches *1* à *10*, avant d’exécuter.

Consultez les [interdépendances de tâches dans Azure lot](batch-task-dependencies.md) et la [TaskDependencies] [ github_sample_taskdeps] exemples de code dans les [exemples de lot azure] [ github_samples] GitHub référentiel pour plus d’informations détaillées sur cette fonctionnalité.

## <a name="environment-settings-for-tasks"></a>Paramètres d’environnement pour les tâches

Chaque tâche exécutée par le service lot a accès aux variables d’environnement qu’il définit sur les nœuds de calcul. Cela inclut les variables d’environnement définies par le service de traitement par lots ([service défini][msdn_env_vars]) et variables d’environnement personnalisés que vous pouvez définir pour vos tâches. Les applications et les scripts qu'exécution de vos tâches ont accès à ces variables d’environnement pendant l’exécution.

Vous pouvez définir des variables d’environnement personnalisées au niveau de la tâche en remplissant la propriété de *paramètres d’environnement* pour ces entités. Par exemple, voir [Ajouter une tâche à une tâche] [ rest_add_task] opération (API REST de lot) ou la [CloudTask.EnvironmentSettings] [ net_cloudtask_env] et [CloudJob.CommonEnvironmentSettings] [ net_job_env] propriétés dans .NET lot.

Votre application cliente ou service peuvent-ils variables d’environnement d’une tâche, définies par le service et personnalisés, à l’aide à [obtenir des informations sur une tâche] [ rest_get_task_info] opération (lot reste) ou en accédant à la [CloudTask.EnvironmentSettings] [ net_cloudtask_env] propriété (lot .NET). Processus qui s’exécutent sur un nœud de calcul puissent accéder ces et autres variables d’environnement sur le nœud, par exemple, en utilisant la familiers `%VARIABLE_NAME%` (Windows) ou `$VARIABLE_NAME` syntaxe (Linux).

Vous trouverez une liste complète de toutes les variables d’environnement service défini dans [les variables d’environnement nœud de calcul][msdn_env_vars].

## <a name="files-and-directories"></a>Fichiers et des répertoires

Chaque tâche a un *répertoire de travail* sous lequel il crée zéro ou plusieurs fichiers et des répertoires. Ce répertoire de travail peut être utilisé pour stocker le programme qui est exécuté par la tâche, les données qu’il traite et le résultat de la transformation effectuée. Tous les fichiers et des répertoires d’une tâche appartiennent à l’utilisateur de la tâche.

Le service lot expose qu’une partie du système de fichiers sur un nœud que le *répertoire racine*. Tâches peuvent accéder au répertoire racine en faisant référence à la `AZ_BATCH_NODE_ROOT_DIR` variable d’environnement. Pour plus d’informations sur l’utilisation de variables d’environnement, voir [paramètres d’environnement pour les tâches](#environment-settings-for-tasks).

Le répertoire racine contient l’arborescence suivante :

![Calculer la structure du répertoire nœud][1]

- **partagé**: ce répertoire offre un accès en lecture/écriture à *toutes les* tâches qui s’exécutent sur un nœud. N’importe quelle tâche qui s’exécute sur le nœud peut créer, lire, mettre à jour et supprimer des fichiers dans ce répertoire. Tâches peuvent accéder au répertoire en faisant référence à la `AZ_BATCH_NODE_SHARED_DIR` variable d’environnement.

- **démarrage**: ce répertoire est utilisé par une tâche de démarrage comme répertoire de travail. Tous les fichiers qui sont téléchargés vers le nœud par cette tâche sont stockés ici. Cette tâche peut créer, lire, mettre à jour et supprimer des fichiers dans ce répertoire. Tâches peuvent accéder au répertoire en faisant référence à la `AZ_BATCH_NODE_STARTUP_DIR` variable d’environnement.

- **Tâches**: un répertoire est créé pour chaque tâche qui s’exécute sur le nœud. Il est accessible en faisant référence à la `AZ_BATCH_TASK_DIR` variable d’environnement.

    Répertoire chaque tâche, le service lot crée un répertoire de travail (`wd`) dont chemin d’accès unique a été spécifié par le `AZ_BATCH_TASK_WORKING_DIR` variable d’environnement. Ce répertoire offre un accès en lecture/écriture à la tâche. La tâche peut créer, lire, mettre à jour et supprimer des fichiers dans ce répertoire. Ce répertoire est conservé en fonction de la contrainte *RetentionTime* spécifiée pour la tâche.

    `stdout.txt`et `stderr.txt`: ces fichiers sont écrits sur le dossier de tâches pendant l’exécution de la tâche.

>[AZURE.IMPORTANT] Lorsqu’un nœud est supprimé à partir du pool, *tous les* fichiers qui sont stockées sur le nœud sont supprimés.

## <a name="application-packages"></a>Packages d’application

La fonctionnalité [packages d’application](batch-application-packages.md) fournit faciliter la gestion et déploiement des applications sur les nœuds de calcul dans vos pools. Vous pouvez télécharger et gérer plusieurs versions d’applications exécutez par vos tâches, y compris leurs fichiers binaires et les fichiers de prise en charge. Ensuite, vous pouvez déployer automatiquement une ou plusieurs de ces applications aux nœuds de calcul dans votre liste.

Vous pouvez spécifier des packages d’application au niveau du pool et tâche. Lorsque vous spécifiez des packages d’application pool, l’application est déployée sur tous les nœuds dans le pool. Lorsque vous spécifiez des packages d’application Office, l’application est déployée uniquement aux nœuds planifiées pour s’exécuter au moins une des tâches du projet, juste avant l’exécution de ligne de commande de la tâche.

Lot gère les détails de l’utilisation de stockage Azure pour stocker vos packages d’application et les déployer pour le calcul des nœuds, afin que votre code et la gestion de la charge peut être simplifiée.

Pour en savoir plus sur la fonctionnalité de package d’application, consultez [déploiement d’applications avec Azure lot des packages d’application](batch-application-packages.md).

>[AZURE.NOTE] Si vous ajoutez des packages d’application pool à un pool *existant* , vous devez redémarrer ses nœuds de calcul pour les packages d’application à déployer sur les nœuds.

## <a name="pool-and-compute-node-lifetime"></a>Durée de vie nœud pool et de calcul

Lorsque vous créez votre solution lot Azure, vous devez prendre une décision de conception sur la façon et lorsque des pools sont créées et combien calculent nœuds au sein de ces pools restent disponibles.

Sur une extrémité du spectre, vous pouvez créer un pool pour chaque tâche que vous envoyez et supprimer le pool dès que ses tâches de fin de l’exécution. Cela permet d’optimiser l’utilisation du car les nœuds sont attribuées uniquement lorsque vos besoins et arrêter dès qu’ils sont inactifs. Bien que cela signifie que la tâche doit attendre les nœuds à allouer, il est important de noter que les tâches sont planifiées pour l’exécution dès nœuds sont disponibles individuellement, alloués, et cette tâche est terminée. Lot signifie *pas* attendre jusqu'à ce que tous les nœuds au sein d’un pool sont disponibles avant d’attribuer des tâches vers les nœuds. Cela permet une utilisation maximale de tous les nœuds disponibles.

À l’autre extrémité du spectre, si vous rencontrez des travaux immédiatement sont la priorité la plus élevée, vous pouvez créer une liste à l’avance et rendre ses nœuds avant l’envoi des tâches. Dans ce scénario, les tâches peuvent démarrer immédiatement, mais nœuds peuvent restez inactif en attendant les doivent être affectées.

Une approche combinée est généralement utilisée pour gérer une charge variable, mais en cours. Vous pouvez avoir un pool de plusieurs tâches sont envoyés au, mais peuvent s’adapter le nombre de nœuds vers le haut ou vers le bas en fonction de la tâche charge (voir [mise à l’échelle des ressources de calcul](#scaling-compute-resources) dans la section suivante). Vous pouvez faire ceci réactive, en fonction de chargement en cours, ou le fait, si chargement peut être prédites.

## <a name="scaling-compute-resources"></a>Mise à l’échelle de ressources de calcul

[Mise à l’échelle automatique](batch-automatic-scaling.md), vous pouvez avoir le service lot ajuster dynamiquement le nombre de nœuds de calcul dans une liste en fonction de l’utilisation de la charge de travail et des ressources actuelle de votre scénario cluster. Cela vous permet de réduire le coût total de l’exécution de votre application en utilisant uniquement les ressources que nécessaires, relâché ceux que vous n’avez pas besoin.

Activer la mise à l’échelle automatique en entrant une [formule de mise à l’échelle automatique](batch-automatic-scaling.md#automatic-scaling-formulas) et en associant cette formule avec un pool. Le service de traitement par lots utilise la formule pour déterminer le nombre cible de nœuds dans le pool pour l’intervalle de mise à l’échelle suivant (un intervalle que vous pouvez configurer). Vous pouvez spécifier les paramètres de mise à l’échelle automatique pour un pool lorsque vous créez ou activez à l’échelle sur un pool ultérieurement. Vous pouvez également mettre à jour les paramètres de mise à l’échelle sur un pool compatibles avec mise à l’échelle.

Par exemple, par exemple un travail nécessite que vous envoyez un grand nombre de tâches à exécuter. Vous pouvez affecter une formule mise à l’échelle au pool qu’ajuste le nombre de nœuds dans le pool selon le nombre de tâches en file d’attente et le taux d’achèvement des tâches dans la tâche. Le service lot régulièrement évalue la formule et redimensionne le pool, en fonction de la charge de travail (ajouter des nœuds pour de nombreuses tâches en file d’attente et supprimer des nœuds pour aucune tâche en file d’attente ou en cours d’exécution) et d’autres paramètres de votre formules.

Une formule de mise à l’échelle peut être basée sur les mesures suivantes :

- **Indicateurs de temps** sont basées sur les statistiques collectées toutes les cinq minutes dans le nombre d’heures spécifié.

- **Indicateurs de ressource** basés sur l’utilisation du processeur, l’utilisation de la bande passante, utilisation de la mémoire et nombre de nœuds.

- **Tâche** est basée sur l’état de la tâche, tel *Active* (en file d’attente), *en cours d’exécution*ou *terminé*.

Lors de la mise à l’échelle automatique réduit le nombre de nœuds de calcul dans un pool, vous devez tenir compte comment gérer les tâches qui s’exécutent au moment de l’opération de baisse. Pour ce faire, lot propose une *option de libération nœud* que vous pouvez inclure dans vos formules. Par exemple, vous pouvez spécifier que les tâches en cours d’exécution sont arrêtés immédiatement, arrêtés immédiatement et puis remise en attente pour l’exécution sur un autre nœud ou autorisés se terminer avant que le nœud est supprimé à partir du pool.

Pour plus d’informations sur l’adaptation automatique d’une application, voir [automatiquement échelle nœuds dans un pool d’Azure lot de calcul](batch-automatic-scaling.md).

> [AZURE.TIP] Pour optimiser l’utilisation des ressources cluster, définissez le nombre cible de nœuds à zéro à la fin d’une tâche, mais autorise l’exécution de tâches à se terminer.

## <a name="security-with-certificates"></a>Sécurité et certificats

Vous devez généralement utiliser certificats lorsque vous chiffrez ou déchiffrer des informations sensibles pour des tâches, comme la clé pour un [compte de stockage Azure][azure_storage]. Pour ce faire, vous pouvez installer des certificats sur les nœuds. Secrets chiffrés sont passés aux tâches par le biais des paramètres de ligne de commande ou incorporées dans une des ressources de la tâche, et les certificats installés peuvent servir à déchiffrer les.

Vous utilisez le [certificat ajouter] [ rest_add_cert] opération (lot reste) ou [CertificateOperations.CreateCertificate] [ net_create_cert] méthode (lot .NET) pour ajouter un certificat à un compte lot. Vous pouvez ensuite associer le certificat à un pool nouveau ou existant. Lorsqu’un certificat est associé à un pool, le service lot installe le certificat sur chaque nœud dans le pool. Le service lot installe les certificats appropriés lorsque le nœud démarre, avant de lancer des tâches (y compris la tâche de début et la tâche de gestionnaire de projet).

Si vous ajoutez des certificats à un pool *existant* , vous devez redémarrer ses nœuds de calcul pour les certificats à appliquer aux nœuds.

## <a name="error-handling"></a>Gestion des erreurs

Il peut arriver nécessaires pour gérer des échecs de tâche et application au sein de votre solution lot.

### <a name="task-failure-handling"></a>Gestion de défaillance de tâche
Échecs de tâche peuvent être classées dans les catégories suivantes :

- **Échecs de planification**

    Si le transfert de fichiers qui sont spécifiés pour une tâche échoue pour une raison quelconque, une « erreur planification » est définie pour la tâche.

    Planification des erreurs peut se produire si les fichiers de ressources de la tâche ont été déplacées, le compte de stockage n’est plus disponible, ou un autre problème s’est produite qui empêche la copie réussie des fichiers vers le nœud.

- **Échecs d’application**

    Le processus qui a été spécifié par ligne de commande de la tâche peut échouer également. Le processus est considérée comme un échec lorsqu’un code de sortie différente de zéro est retourné par le processus est exécuté par la tâche (voir *codes de sortie de tâche* dans la section suivante).

    Pour les échecs d’application, vous pouvez configurer lot pour recommencer automatiquement la tâche jusqu'à un nombre spécifié de fois.

- **Échecs de contrainte**

    Vous pouvez définir une contrainte qui spécifie la durée maximale de l’exécution d’une tâche ou une tâche, le *maxWallClockTime*. Cela peut être utile pour terminer les tâches « suspendu ».

    Lorsque le nombre maximal d’heures a été dépassé, la tâche est marquée comme *terminée*, mais le code de sortie est défini sur `0xC000013A` et le champ *schedulingError* est marqué comme `{ category:"ServerError", code="TaskEnded"}`.

### <a name="debugging-application-failures"></a>Débogage des échecs d’application

- `stderr`et`stdout`

    Pendant l’exécution d’une application risque de sortie de diagnostic que vous pouvez utiliser pour résoudre les problèmes. Comme mentionné précédemment dans la section [fichiers et des répertoires](#files-and-directories), le service lot écrit sortie standard et sortie d’erreur standard à `stdout.txt` et `stderr.txt` fichiers dans le répertoire de tâche sur le nœud de calcul. Vous pouvez utiliser le portail Azure ou l’un des kits de développement logiciel lot pour télécharger ces fichiers. Par exemple, vous pouvez récupérer ces et autres fichiers aux fins de dépannage à l’aide de [ComputeNode.GetNodeFile] [ net_getfile_node] et [CloudTask.GetNodeFile] [ net_getfile_task] dans la bibliothèque .NET lot.

- **Codes de sortie de tâche**

    Comme mentionné précédemment, une tâche est marquée comme ayant échoué par le service lot si le processus est exécuté par la tâche renvoie un code de sortie différente de zéro. Lorsqu’une tâche exécute un processus, lot remplit propriété de code de fermeture de la tâche avec le *code du processus de retour*. Il est important de noter que code de sortie d’une tâche n’est **pas** déterminé par le service lot--il est déterminé par le processus lui-même ou le système d’exploitation sur lequel exécuter le processus.

### <a name="accounting-for-task-failures-or-interruptions"></a>Gestion des comptes pour les échecs de tâche ou interruptions

Tâches peuvent parfois échouer ou être interrompus. L’application Office elle-même peut échouer, le nœud sur lequel s’exécute la tâche peut être redémarré, ou le nœud être supprimé à partir du pool pendant une opération de redimensionnement si libération stratégie du pool est définie pour supprimer les nœuds immédiatement sans attendre des tâches terminer. Dans tous les cas, la tâche peut être automatiquement remise en attente par lot pour l’exécution sur un autre nœud.

Il est également possible pour un problème intermittent à l’origine du blocage ou prennent trop de temps d’exécution d’une tâche. Vous pouvez définir la durée d’exécution maximale pour une tâche. Si elle est dépassé, lot interrompt l’application de la tâche.

### <a name="connecting-to-compute-nodes"></a>Connexion pour les nœuds de calcul

Vous pouvez effectuer supplémentaires débogage et résolution des problèmes en vous connectant à distance à un nœud de calcul. Vous pouvez utiliser le portail Azure pour télécharger un fichier de protocole RDP (Remote Desktop) pour les nœuds de Windows et obtenir des informations de connexion SSH (Secure Shell) pour les nœuds Linux. Vous pouvez également le faire à l’aide de l’API lot--par exemple, avec [Lot .NET] [ net_rdpfile] ou [Lot Python](batch-linux-nodes.md#connect-to-linux-nodes).

>[AZURE.IMPORTANT] Pour vous connecter à un nœud via RDP ou SSH, vous devez tout d’abord créer un utilisateur sur le nœud. Pour ce faire, vous pouvez utiliser la Azure portail, [Ajouter un compte d’utilisateur à un nœud] [ rest_create_user] à l’aide de l’API REST lot, appelez le [ComputeNode.CreateComputeNodeUser] [ net_create_user] méthode dans .NET lot ou d’un appel l' [Ajouter un utilisateur] [ py_add_user] méthode dans le module lot Python.

### <a name="troubleshooting-bad-compute-nodes"></a>Résolution des problèmes « bad » les nœuds de calcul

Dans les cas où certaines de vos tâches ne parviennent pas, votre application cliente lot ou service peut examiner les métadonnées des tâches pour identifier un nœud dysfonctionnement a échoué. Chaque nœud dans un pool est remplacé par un ID unique, et le nœud sur lequel s’exécute une tâche est inclus dans les métadonnées de la tâche. Une fois que vous avez identifié un nœud de problème, vous pouvez effectuer plusieurs actions avec lui :

- **Redémarrez le nœud** ([REST][rest_reboot] | [.NET][net_reboot])

    Redémarrer le nœud peut parfois effacer des problèmes une comme processus en panne ou figés. Remarque : Si votre pool utilise une tâche de début ou votre travail d’une tâche de préparation du projet, elles sont exécutées lorsque le nœud redémarre.

- **Recréez le nœud** ([REST][rest_reimage] | [.NET][net_reimage])

    Ceci permet de réinstaller le système d’exploitation sur le nœud. Comme avec un nœud du redémarrage, démarrez tâches et tâches de préparation d’un projet sont réexécuter une fois que le nœud a été que.

- **Supprimer le nœud à partir du pool** ([REST][rest_remove] | [.NET][net_remove])

    Il est parfois nécessaire supprimer complètement le nœud à partir du pool.

- **Désactiver le nœud de planification des tâches** ([REST][rest_offline] | [.NET][net_offline])

    Cela efficacement prend le nœud « hors connexion » afin qu’aucune autre tâche lui n’est affectés, mais autorise le nœud doit rester en cours d’exécution et dans le pool. Cela vous permet d’effectuer plus approfondi la cause des échecs de sans perte de données de la tâche a échoué--et sans le nœud à l’origine d’échecs de tâche supplémentaires. Par exemple, vous pouvez désactiver la planification sur le nœud, puis [Connectez-vous à distance](#connecting-to-compute-nodes) pour examiner les journaux d’événements du nœud ou effectuer d’autres opérations de dépannage des tâches. Une fois que vous avez terminé votre enquête, vous pouvez puis remettre le nœud en ligne en activant la planification des tâches ([reste][rest_online] | [.NET][net_online]), ou effectuez l’une des opérations décrites précédemment.

> [AZURE.IMPORTANT] Avec chaque action qui est décrit dans cette section--redémarrer, créer une nouvelle image, supprimer et désactiver la planification des tâches, vous pouvez spécifier le traitement des tâches en cours d’exécution sur le nœud lorsque vous effectuez l’action. Par exemple, lorsque vous désactivez la planification des tâches sur un nœud à l’aide de la bibliothèque cliente .NET lot, vous pouvez spécifier une [DisableComputeNodeSchedulingOption] [ net_offline_option] valeur énumération à indiquer à la **fin** des tâches, **Requeue** d’exécute leur pour la planification des autres nœuds, ou autorise l’exécution de tâches à effectuer avant d’exécuter l’action (**TaskCompletion**).

## <a name="next-steps"></a>Étapes suivantes

- Passez en revue un exemple d’application lot étape par étape, [prise en main la bibliothèque par lots Azure pour .NET](batch-dotnet-get-started.md). Il existe également une [version Python](batch-python-tutorial.md) du didacticiel qui s’exécute une charge de travail sur les nœuds de calcul Linux.

- Télécharger et créer [Lot Explorer] [ github_batchexplorer] exemple de projet pour une utilisation pendant que vous développez des solutions votre lot. À l’aide de l’Explorateur de lot, vous pouvez effectuer les éléments suivants et bien plus encore :
  - Surveiller et manipuler des pools, les tâches et les tâches au sein de votre compte par lots
  - Télécharger `stdout.txt`, `stderr.txt`et autres fichiers à partir des nœuds
  - Créer des utilisateurs sur des nœuds et télécharger des fichiers RDP d’accès distant

- Découvrez comment [créer des pools Linux des nœuds de calcul](batch-linux-nodes.md).

- Visitez le [forum Azure lot] [ batch_forum] sur MSDN. Le forum est idéal pour poser des questions, que vous venez de découvrir ou sont un expert au moyen de commandes.

[1]: ./media/batch-api-basics/node-folder-structure.png

[azure_storage]: https://azure.microsoft.com/services/storage/
[batch_forum]: https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurebatch
[cloud_service_sizes]: ../cloud-services/cloud-services-sizes-specs.md
[msmpi]: https://msdn.microsoft.com/library/bb524831.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_sample_taskdeps]:  https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/TaskDependencies
[github_batchexplorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[batch_net_api]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[msdn_env_vars]: https://msdn.microsoft.com/library/azure/mt743623.aspx
[net_cloudjob_jobmanagertask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobmanagertask.aspx
[net_cloudjob_priority]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.priority.aspx
[net_cloudpool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx
[net_cloudtask_env]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.environmentsettings.aspx
[net_create_cert]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificateoperations.createcertificate.aspx
[net_create_user]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.createcomputenodeuser.aspx
[net_getfile_node]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.getnodefile.aspx
[net_getfile_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.getnodefile.aspx
[net_job_env]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.commonenvironmentsettings.aspx
[net_multiinstancesettings]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.aspx
[net_onalltaskscomplete]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.onalltaskscomplete.aspx
[net_rdp]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.getrdpfile.aspx
[net_reboot]: https://msdn.microsoft.com/library/azure/mt631495.aspx
[net_reimage]: https://msdn.microsoft.com/library/azure/mt631496.aspx
[net_remove]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.removefrompoolasync.aspx
[net_offline]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.disableschedulingasync.aspx
[net_online]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.enableschedulingasync.aspx
[net_offline_option]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.common.disablecomputenodeschedulingoption.aspx
[net_rdpfile]: https://msdn.microsoft.com/library/azure/Mt272127.aspx
[vnet]: https://msdn.microsoft.com/library/azure/dn820174.aspx#bk_netconf

[py_add_user]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.operations.html#azure.batch.operations.ComputeNodeOperations.add_user

[batch_rest_api]: https://msdn.microsoft.com/library/azure/Dn820158.aspx
[rest_add_job]: https://msdn.microsoft.com/library/azure/mt282178.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[rest_add_cert]: https://msdn.microsoft.com/library/azure/dn820169.aspx
[rest_add_task]: https://msdn.microsoft.com/library/azure/dn820105.aspx
[rest_create_user]: https://msdn.microsoft.com/library/azure/dn820137.aspx
[rest_get_task_info]: https://msdn.microsoft.com/library/azure/dn820133.aspx
[rest_job_schedules]: https://msdn.microsoft.com/library/azure/mt282179.aspx
[rest_multiinstance]: https://msdn.microsoft.com/library/azure/mt637905.aspx
[rest_multiinstancesettings]: https://msdn.microsoft.com/library/azure/dn820105.aspx#multiInstanceSettings
[rest_update_job]: https://msdn.microsoft.com/library/azure/dn820162.aspx
[rest_rdp]: https://msdn.microsoft.com/library/azure/dn820120.aspx
[rest_reboot]: https://msdn.microsoft.com/library/azure/dn820171.aspx
[rest_reimage]: https://msdn.microsoft.com/library/azure/dn820157.aspx
[rest_remove]: https://msdn.microsoft.com/library/azure/dn820194.aspx
[rest_offline]: https://msdn.microsoft.com/library/azure/mt637904.aspx
[rest_online]: https://msdn.microsoft.com/library/azure/mt637907.aspx

[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/
