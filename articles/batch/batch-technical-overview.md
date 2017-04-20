<properties
    pageTitle="Concepts de base de service lot Azure | Microsoft Azure"
    description="Découvrez comment utiliser le service Azure lot pour parallèle à grande échelle et charges de travail HPC"
    services="batch"
    documentationCenter=""
    authors="mmacy"
    manager="timlt"
    editor=""/>

<tags
    ms.service="batch"
    ms.workload="big-compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/22/2016"
    ms.author="marsma"/>

# <a name="basics-of-azure-batch"></a>Concepts de base du lot Azure

Lot Azure active vous permet d’exécuter des applications à grande échelle parallèle et High performance computing (HPC) efficacement dans le cloud. Il s’agit d’un service de plateforme qui planifie cluster de manière intensive à s’exécuter sur une collection managée des machines virtuelles et pouvez échelle calculer automatiquement ressources pour répondre aux besoins de vos projets.

Avec le service lot, vous définissez des ressources cluster Azure pour exécuter vos applications en parallèle et à l’échelle. Vous pouvez exécuter à la demande ou planifiée travaux et que vous n’avez pas besoin manuellement créer, configurer et gérer un cluster HPC, machines virtuelles, réseaux virtuels ou un travail complexe et infrastructure de planification de la tâche.

## <a name="use-cases-for-batch"></a>Exemples d’utilisation pour traitement par lots

Traitement par lots est un service Azure géré qui est utilisé pour le *traitement par lots* ou *lot computing*--un grand nombre de tâches similaires pour obtenir un résultat escompté en cours d’exécution. Lot computing est plus couramment utilisé par les organisations qui régulièrement traitent, transforment et analyser les grands volumes de données.

Lot fonctionne bien avec les charges de travail et applications (également appelé « ces parallèles ») intrinsèque parallèles. Les charges de travail intrinsèque parallèles sont facilement divisées en plusieurs tâches qui effectuent le travail simultanément sur plusieurs ordinateurs.

![Tâches parallèles][1]<br/>

Quelques exemples de charges de travail qui sont traités fréquemment à l’aide de cette technique sont :

* Modélisation de risque financier
* Hydrologie et climat de l’analyse des données
* Traitement, l’analyse et rendu d’image
* Le codage de média et transcodage
* Analyse de séquence génétique
* Analyse de contrainte d’ingénierie
* Test de logiciels

Lot peut également effectuer des calculs en parallèle avec une étape de réduire à la fin et exécuter les charges de travail HPC plus complexes tels que Message transmission des applications [MPI (Interface)](batch-mpi.md) .

Pour une comparaison entre les commandes et d’autres options de solution HPC dans Azure, consultez [solutions lot et HPC](batch-hpc-solutions.md).

## <a name="developing-with-batch"></a>Développement au moyen de commandes

Traitement de charges de travail en parallèle avec lot est généralement effectuée par programme à l’aide d’une des [Commandes API](#batch-development-apis). Avec l’API lot, créer et gérer des pools de nœuds de calcul (machines virtuelles) et planifier des tâches et tâches à exécuter sur ces nœuds. Une application cliente ou le service que vous créez utilise les API lot pour communiquer avec le service lot.

Vous pouvez efficacement traiter les charges de travail à grande échelle pour votre organisation, ou fournir un serveur frontal de service à vos clients afin qu’ils puissent exécuter projets et des tâches, à la demande, ou selon un planning--sur un, centaines ou même des milliers de nœuds. Vous pouvez également utiliser les commandes dans le cadre d’un flux de travail plus grande, géré par les outils tels que [Azure Data Factory](../data-factory/data-factory-data-processing-using-batch.md).

> [AZURE.TIP] Lorsque vous êtes prêt à approfondir à l’API lot pour comprendre le fonctionnement des fonctionnalités qu'il propose approfondi, consultez la [vue d’ensemble de la fonctionnalité lot pour les développeurs](batch-api-basics.md).

### <a name="azure-accounts-youll-need"></a>Vous aurez besoin de comptes Azure

Lorsque vous développez des solutions de lot, vous allez utiliser les comptes suivants dans Microsoft Azure.

- **Abonnement et compte azure** - si vous n’avez pas déjà un abonnement Azure, vous pouvez activer votre [abonné MSDN bénéficier][msdn_benefits], ou s’inscrire à un [compte Azure gratuit][free_account]. Lorsque vous créez un compte, un abonnement par défaut est créé pour vous.

- **Compte lot** - lorsque vos applications interagissent avec le service lot, le nom du compte, l’URL du compte, ainsi qu’une touche d’accès sont utilisés comme informations d’identification. Toutes vos ressources lot tels que des pools, calcul des nœuds, tâches, et les tâches sont associées à un compte lot. Vous pouvez [Créer lot compte](batch-account-create-portal.md) dans le portail Azure.

- **Compte de stockage** - lot prend en charge l’utilisation des fichiers dans le [Stockage Azure][azure_storage]. Presque toutes scénario lot utilise le stockage Azure--pour organiser des programmes qui s’exécutent vos tâches et les données qu’ils traitent et pour le stockage des données de sortie qu’ils génèrent. Pour créer un compte de stockage, voir [comptes de stockage sur Azure](./../storage/storage-create-storage-account.md).

### <a name="batch-development-apis"></a>Développement lot API

Vos applications et services peuvent émettre des appels API REST directs, utiliser un ou plusieurs des bibliothèques client suivantes, ou une combinaison des deux pour gérer calculer les ressources et exécuter les charges de travail parallèles à l’échelle au moyen du service lot.

| API    | Référence de l’API | Télécharger | Exemples de code |
| ----------------- | ------------- | -------- | ------------ |
| **Lot reste** | [MSDN][batch_rest] | N/A | [MSDN][batch_rest] |
| **Lot .NET**    | [MSDN][api_net] | [NuGet][api_net_nuget] | [GitHub][api_sample_net] |
| **Python lot**  | [readthedocs.IO][api_python] | [PyPI][api_python_pypi] |[GitHub][api_sample_python] |
| **Node.js lot** | [github.IO][api_nodejs] | [npm][api_nodejs_npm] | - |
| **Lot Java** (preview) | [github.IO][api_java] | [Maven][api_java_jar] | [GitHub][api_sample_java] |

### <a name="batch-resource-management"></a>Gestion des ressources de traitement par lots

Outre le client API, vous pouvez également utiliser les éléments suivants pour gérer les ressources au sein de votre compte lot.

- [Applets de commande PowerShell de commandes][batch_ps]: le lot Azure les applets de commande dans le module [Azure PowerShell](../powershell-install-configure.md) permettent de gérer les ressources du lot avec PowerShell.

- [Azure infrastructure du langage commun](../xplat-cli-install.md): Interface de ligne The Azure (commande Azure) est un ensemble d’outils de disponibilité sur plusieurs plateformes qui fournit des commandes shell pour interagir avec de nombreux services Azure, notamment par lots.

- Bibliothèque cliente [Lot gestion .NET](batch-management-dotnet.md) : également disponible via [NuGet][api_net_mgmt_nuget], vous pouvez utiliser la bibliothèque de client par lot Management .NET pour gérer par programme les comptes lot, les quotas et les packages d’application. Référence de la bibliothèque de gestion est sur [MSDN][api_net_mgmt].

### <a name="batch-tools"></a>Outils de traitement par lots

Tandis que ne pas obligé de créer des solutions à l’aide des commandes, voici quelques outils précieux à utiliser lors de la création et le débogage vos lot applications et services.

 - [Portail Azure][portal]: vous pouvez créer, surveiller et supprimer des pools lot, des projets et des tâches dans les cartes de lot du portail Azure. Vous pouvez afficher les informations d’état pour ces et autres ressources pendant que vous exécutez vos tâches et même téléchargement des fichiers à partir des nœuds cluster dans vos pools (télécharger une tâche a échoué `stderr.txt` lors de la résolution des problèmes, par exemple). Vous pouvez également télécharger des fichiers RDP (Remote Desktop) que vous pouvez utiliser pour vous connecter à nœuds de calcul.

 - [Explorer lot Azure][batch_explorer]: Explorer lot fournit lot ressource gestion une fonctionnalité similaire en tant que le portail Azure, mais dans une application cliente Windows Presentation Foundation (WPF) autonome. Une des applications exemple .NET lot disponibles sur [GitHub][github_samples], vous pouvez créer avec Visual Studio 2015 ou version ultérieure et utilisez-le pour parcourir et gérer les ressources dans votre compte lot lorsque vous développez et déboguez vos solutions lot. Afficher la tâche, pool et détails de la tâche, téléchargement des fichiers à partir des nœuds de calcul et se connecteront à des nœuds à distance en utilisant des fichiers RDP (Remote Desktop) vous pouvez télécharger avec l’Explorateur de lot.

 - [Explorateur de stockage de Microsoft Azure][storage_explorer]: pendant que ne soient pas un outil Azure lot, l’Explorateur d’espace de stockage est un autre outil précieux qui ont tandis que vous développez et débogage vos solutions lot.

## <a name="scenario-scale-out-a-parallel-workload"></a>Scénario : Échelle une charge de travail en parallèle

Une solution courantes qui utilise les API lot pour interagir avec le service lot entraîne la mise à l’échelle des travaux intrinsèque en parallèle, telles que le rendu des images dans les scènes 3D--sur un pool de nœuds de calcul. Ce pool des nœuds de calcul peut être votre « batterie de serveurs rendu » qui fournit des dizaines, des centaines ou même des milliers de cœurs à votre travail rendu, par exemple.

L’illustration ci-dessous présente un flux de travail de commandes courantes, avec une application cliente ou service hébergé à l’aide de commandes pour exécuter une charge de travail en parallèle.

![Flux de travail lot solution][2]

Dans ce scénario courant, votre application ou service traite la charge de travail calcul Azure lot en effectuant les étapes suivantes :

1. Téléchargez les **fichiers d’entrée** et l' **application** qui traitera ces fichiers à votre compte de stockage Azure. Les fichiers d’entrée peuvent être des données que votre application traitera, telles que les données financiers ou des fichiers vidéo à être transcodé. Les fichiers d’application peuvent être n’importe quelle application qui est utilisée pour traiter les données, tel qu’une application de rendu 3D ou TRANSCODEUR multimédia.

2. Créer un lot **pool** des nœuds de calcul dans votre compte lot--ces nœuds sont les machines virtuelles qui s’exécutera vos tâches. Vous spécifiez les propriétés telles que la [taille du nœud](./../cloud-services/cloud-services-sizes-specs.md), leur système d’exploitation et l’emplacement dans le stockage Azure de l’application à installer lorsque les nœuds rejoindre le pool (l’application que vous avez téléchargée à l’étape 1 #). Vous pouvez également configurer le pool d' [Ajuster automatiquement](batch-automatic-scaling.md)--dynamiquement ajuster le nombre de nœuds de calcul dans le pool--en réponse à la charge de travail qui génèrent de vos tâches.

3. Créer une **tâche** pour exécuter la charge de travail sur le pool de nœuds de calcul. Lorsque vous créez une tâche, vous l’associer à une liste de commandes.

4. Ajouter des **tâches** à la tâche. Lorsque vous ajoutez des tâches à une tâche, le service lot planifie automatiquement les tâches pour une exécution sur les nœuds de calcul dans le pool. Chaque tâche utilise l’application que vous avez téléchargée pour traiter les fichiers d’entrée.

    - 4 a. Avant l’exécution d’une tâche, il peut télécharger les données (les fichiers d’entrée) à la procédure à suivre pour le nœud de calcul à qu'il est affecté. Si l’application n’a pas déjà été installée sur le nœud (voir l’étape #2), il peut être téléchargé ici à la place. Lorsque les téléchargements sont terminées, les tâches s’exécutent sur leurs nœuds affectées.

5. Exécuter des tâches, vous pouvez interroger lot pour surveiller l’avancement de la tâche et ses tâches. Votre application cliente ou service communique avec le service lot via HTTPS et parce que vous avez peut-être d’analyser des milliers de tâches qui s’exécutent sur des milliers de nœuds de calcul, veillez à [interroger le service lot efficacement](batch-efficient-list-queries.md).

6. En tant que les tâches, ils peuvent télécharger les données de résultat au stockage Azure. Vous pouvez également récupérer des fichiers directement à partir des nœuds de calcul.

7. Quand votre analyse détecte que les tâches dans votre travail terminées, votre application cliente ou le service peut télécharger les données de sortie pour un traitement approfondi ou d’évaluation.

Gardez à l’esprit c’est simplement une façon d’utiliser le lot, et ce scénario décrit seulement quelques-unes de ses fonctionnalités disponibles. Par exemple, vous pouvez exécuter [plusieurs tâches en parallèle](batch-parallel-node-tasks.md) sur chaque nœud de calcul, et vous pouvez utiliser les [tâches de préparation et de saisie semi-automatique](batch-job-prep-release.md) pour préparer les nœuds pour vos tâches, puis nettoyer par la suite.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez une vue d’ensemble du service lot, il est temps Approfondissez pour découvrir comment vous pouvez l’utiliser pour traiter les charges de travail en parallèle votre cluster accrue.

- Lisez la [Présentation des fonctionnalités de lot pour les développeurs](batch-api-basics.md), les informations essentielles pour tout le monde préparation à l’utilisation des commandes. Cet article contient des informations plus détaillées sur les ressources de service lot, tels que des pools et nœuds, tâches, tâches et les nombreuses fonctionnalités de l’API que vous pouvez utiliser lors de la création de votre application lot.

- [Prise en main la bibliothèque lot Azure pour .NET](batch-dotnet-get-started.md) pour apprendre à utiliser c# et la bibliothèque .NET lot pour exécuter une charge de travail simple à l’aide d’un flux de travail lot courantes. Cet article doit être un de vos taquets premières tout en apprendre à utiliser le service lot de. Il existe également une [version Python](batch-python-tutorial.md) du didacticiel.

- Télécharger les [exemples de code sur GitHub] [ github_samples] pour voir comment c# et Python peut interagir avec lot aux charges de travail exemple planning et processus.

- Consultez la [Rubrique d’apprentissage lot] [ learning_path] pour avoir une idée des ressources disponibles pour vous comme vous apprendrez à utiliser des commandes.

[azure_storage]: https://azure.microsoft.com/services/storage/
[api_java]: http://azure.github.io/azure-sdk-for-java/
[api_java_jar]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-batch%22
[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_nuget]: https://www.nuget.org/packages/Azure.Batch/
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_net_mgmt_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/
[api_nodejs]: http://azure.github.io/azure-sdk-for-node/azure-batch/latest/
[api_nodejs_npm]: https://www.npmjs.com/package/azure-batch
[api_python]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.html
[api_python_pypi]: https://pypi.python.org/pypi/azure-batch
[api_sample_net]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp
[api_sample_python]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch
[api_sample_java]: https://github.com/Azure/azure-batch-samples/tree/master/Java/
[batch_ps]: https://msdn.microsoft.com/library/azure/mt125957.aspx
[batch_rest]: https://msdn.microsoft.com/library/azure/Dn820158.aspx
[free_account]: https://azure.microsoft.com/free/
[github_samples]: https://github.com/Azure/azure-batch-samples
[learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/
[msdn_benefits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[batch_explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[storage_explorer]: http://storageexplorer.com/
[portal]: https://portal.azure.com

[1]: ./media/batch-technical-overview/tech_overview_01.png
[2]: ./media/batch-technical-overview/tech_overview_02.png
