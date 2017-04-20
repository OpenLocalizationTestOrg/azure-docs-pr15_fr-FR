<properties
   pageTitle="Conseils de travaux d’arrière-plan | Microsoft Azure"
   description="Conseils sur l’arrière-plan des tâches qui s’exécutent indépendamment de l’interface utilisateur."
   services=""
   documentationCenter="na"
   authors="dragon119"
   manager="christb"
   editor=""
   tags=""/>

<tags
   ms.service="best-practice"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/21/2016"
   ms.author="masashin"/>

# <a name="background-jobs-guidance"></a>Conseils de travaux d’arrière-plan

[AZURE.INCLUDE [pnp-header](../includes/guidance-pnp-header-include.md)]

## <a name="overview"></a>Vue d’ensemble

Différents types d’applications requièrent des tâches en arrière-plan qui s’exécutent indépendamment de l’interface utilisateur (IU). Exemples par lots, tâches de traitement intensive et processus longue, comme le flux de travail. Tâches en arrière-plan peuvent être exécutés sans interaction de l’utilisateur, l’application peut démarrer le travail, puis passez à traiter les demandes interactifs auprès des utilisateurs. Cela peut vous aider à réduire la charge sur l’application de l’interface utilisateur, ce qui peut améliorer la disponibilité et réduire les délais de réponse interactif.

Par exemple, si une application est requis pour générer les miniatures des images qui sont téléchargés par les utilisateurs, il peut effectuer cette opération dans une tâche en arrière-plan et enregistrer la miniature au stockage lorsqu’elle est terminée--sans que l’utilisateur ayant besoin d’attendre le processus se terminer. Dans la même manière, un utilisateur passer une commande pouvez démarrer un flux de travail d’arrière-plan traite la commande, alors que l’interface utilisateur permet à l’utilisateur continuer à Explorer l’application web. Lorsque le travail en arrière-plan est terminé, il vous pouvez mettre à jour les données stockées orders et envoyer un message électronique à l’utilisateur qui confirme l’ordre.

Lorsque vous envisagez si pour mettre en œuvre une tâche en tâche d’arrière-plan, les principaux critères est si la tâche peut s’exécuter sans interaction de l’utilisateur et sans l’interface utilisateur de devoir attendre la tâche doit se terminer. Tâches nécessitant l’utilisateur ou l’interface utilisateur attendre qu’ils sont remplis peut ne pas convient en tant que travaux en arrière-plan.

## <a name="types-of-background-jobs"></a>Types de tâches en arrière-plan

Tâches en arrière-plan incluent généralement une ou plusieurs des types de travaux suivants :

- Tâches intensive du processeur, tels que des calculs mathématiques ou analyse du modèle structurels.
- J’ai/O-accrue tâches, telles que l’exécution d’une série de transactions de stockage ou l’indexation de fichiers.
- Tâches, telles que les mises à jour des données nuit ou traitement planifié.
- Longue flux de travail, tels que le traitement des commandes ou mise en service des services et systèmes.
- Traitement des données sensibles dans laquelle la tâche est remise à un emplacement plus sécurisé pour traitement. Par exemple, vous souhaiterez pas traitement des données sensibles dans une application web. À la place, vous pouvez utiliser un motif comme [opérateur](http://msdn.microsoft.com/library/dn589793.aspx) pour changer les données d’un processus en arrière-plan isolé qui a accès à l’emplacement protégé.

## <a name="triggers"></a>Déclencheurs

Tâches en arrière-plan peuvent être lancées de différentes manières. Ils appartiennent à l’une des catégories suivantes :

- [**Déclencheurs piloté par l’événement**](#event-driven-triggers). La tâche est démarrée en réponse à un événement, généralement une action effectuée par un utilisateur ou d’une étape dans un flux de travail.
- [**Déclencheurs pilotés par la planification**](#schedule-driven-triggers). La tâche est appelée sur une planification basée sur une horloge. Il peut s’agir d’un calendrier récurrent ou un appel One n’est spécifié pour une date ultérieure.

### <a name="event-driven-triggers"></a>Déclencheurs événementielle

Appel événementielle utilise un déclencheur pour démarrer la tâche en arrière-plan. Voici quelques exemples d’utilisation de déclencheurs événementielle :

- L’interface utilisateur ou une autre tâche place un message dans une file d’attente. Le message contient des données sur une action qui a eu lieu, telles que l’utilisateur passer une commande. La tâche d’arrière-plan écoute sur cette file d’attente et détecte l’arrivée d’un nouveau message. Il lit le message et utilise les données qu’il contient comme entrée pour le travail en arrière-plan.
- L’interface utilisateur ou une autre tâche enregistre ou met à jour une valeur dans le stockage. La tâche d’arrière-plan surveille le stockage et détecte les modifications. Il lit les données et l’utilise comme entrée pour le travail en arrière-plan.
- L’interface utilisateur ou une autre tâche effectue une requête à un point de terminaison, par exemple une URI HTTPS ou une API qui est exposée comme un service web. Elle passe les données qui sont nécessaire pour achever la tâche d’arrière-plan dans le cadre de la demande. Le point de terminaison ou un service web appelle la tâche d’arrière-plan, qui utilise les données comme son entrée.

Exemples de tâches qui sont adaptés à appel événementielle : flux de travail, envoyer des informations aux services à distance, envoi de messages électroniques et mise en service de nouveaux utilisateurs dans les applications partagées, de traitement d’images.

### <a name="schedule-driven-triggers"></a>Déclencheurs pilotés par la planification

Appel pilotés par la planification utilise une horloge pour démarrer la tâche en arrière-plan. Voici quelques exemples d’utilisation de déclencheurs pilotés par la planification :

- Une horloge qui s’exécute localement dans l’application ou dans le cadre du système d’exploitation de l’application appelle une tâche en arrière-plan à intervalles réguliers.
- Une horloge qui s’exécute dans une autre application ou un service de minuteur tel que planificateur Azure, envoie une demande d’un service web ou de l’API de façon régulière. Le service web ou de l’API appelle la tâche en arrière-plan.
- Une application ou un processus distinct démarre une horloge qui entraîne la tâche d’arrière-plan à appeler une fois après un délai de temps spécifié, ou à un moment donné.

Exemples de tâches qui sont adaptés à appel pilotés par la planification : routines de traitement par lots (par exemple, mise à jour les listes de produits liés pour les utilisateurs en fonction de leur comportement récente), des tâches de routine traitement des données (par exemple, mise à jour à partir d’index ou génération de résultats cumulés), analyse des données pour les rapports quotidiens, nettoyage de rétention des données et des contrôles de cohérence de données.

Si vous utilisez une tâche par l’effort planning doit exécuter en tant qu’une seule instance, gardez les éléments suivants :

- Si l’instance cluster exécutant le planificateur (par exemple, une machine virtuelle à l’aide de tâches planifiées Windows) est mise à l’échelle, vous devrez plusieurs instances du planificateur en cours d’exécution. Ces peuvent démarrer plusieurs instances de la tâche.
- Si tâches sont exécutées pendant plus de la période entre les événements du planificateur, le planificateur peut démarrer une autre instance de la tâche alors que l’option précédente est toujours en cours d’exécution.

## <a name="returning-results"></a>Renvoi de résultats

Tâches en arrière-plan exécutent en mode asynchrone dans un processus distinct, ou même dans un autre endroit, à partir de l’interface utilisateur ou le processus qui a appelé la tâche en arrière-plan. Dans l’idéal, tâches en arrière-plan sont opérations « envoyer et d’oublier », et leurs cours d’exécution n’a aucun impact sur l’interface utilisateur ou le processus d’appel. Cela signifie que le processus d’appel n’attend pas d’achèvement des tâches. Par conséquent, il ne peut pas détecter automatiquement lorsque la tâche est terminée.

Si vous avez besoin d’une tâche en arrière-plan pour communiquer avec la tâche d’appel pour indiquer la progression ou fin, vous devez implémenter un mécanisme pour cela. Quelques exemples sont :

- Écrire une valeur indicateur d’état dans le stockage accessible à la tâche de l’interface utilisateur ou de l’appelant, qui peut surveiller ou vérifier cette valeur lorsque cela est nécessaire. Autres données de la tâche en arrière-plan doit retourner à l’appelant peuvent être placées dans le même emplacement de stockage.
- Établir une file d’attente de réponse écoute l’interface utilisateur ou l’appelant. La tâche d’arrière-plan peut envoyer des messages dans la file d’attente qui indiquent l’état et l’achèvement. Données de la tâche en arrière-plan doit retourner à l’appelant peuvent être placées dans les messages. Si vous utilisez Bus des services Azure, vous pouvez utiliser les propriétés **ReplyTo** et **CorrelationId** pour implémenter cette fonctionnalité. Pour plus d’informations, voir [corrélation Service Bus demandé de messagerie](http://www.cloudcasts.net/devguide/Default.aspx?id=13029).
- Exposer un API ou un point de terminaison de la tâche d’arrière-plan que l’interface utilisateur ou un appelant accessibles pour obtenir des informations d’état. Données de la tâche en arrière-plan doit retourner à l’appelant peuvent être incluses dans la réponse.
- Être appelé par la tâche d’arrière-plan revenir à l’interface utilisateur ou un appelant via une API pour indiquer l’état à des points prédéfinis ou à la fin. Il peut s’agir via déclenchés localement ou via un mécanisme de publication et d’abonnement. Données de la tâche en arrière-plan doit retourner à l’appelant peuvent être incluses dans la charge utile de demande ou un événement.

## <a name="hosting-environment"></a>Environnement d’hébergement

Vous pouvez héberger des tâches en arrière-plan à l’aide d’une plage de services de plateforme Azure différente :

- [**WebJobs et azure Web Apps**](#azure-web-apps-and-webjobs). Vous pouvez utiliser WebJobs pour exécuter les travaux personnalisée basée sur une plage de différents types de scripts ou des programmes exécutables dans le contexte d’une application web.
- [**Azure Cloud Services web et collaborateur rôles**](#azure-cloud-services-web-and-worker-roles). Vous pouvez écrire du code dans un rôle qui s’exécute en arrière-plan.
- [**Machines virtuelles azure**](#azure-virtual-machines). Si vous disposez d’un service Windows ou que vous souhaitez utiliser le Planificateur de tâches de Windows, il est courant pour héberger vos tâches en arrière-plan sur une machine virtuelle dédiée.
- [**Lot azure**](./batch/batch-technical-overview.md). Il s’agit d’un service de plateforme qui planifie cluster de manière intensive à s’exécuter sur une collection managée des machines virtuelles et pouvez échelle calculer automatiquement ressources pour répondre aux besoins de vos projets.

Les sections suivantes décrivent chacune de ces options plus en détail et incluent les considérations pour vous aider à choisir l’option appropriée.

## <a name="azure-web-apps-and-webjobs"></a>WebJobs et azure Web Apps

Vous pouvez utiliser WebJobs Azure pour exécuter les travaux personnalisé en tant que tâches d’arrière-plan au sein d’une application Web Azure. WebJobs s’exécutent dans le contexte de votre application web comme un processus continu. WebJobs également exécuté en réponse à un événement déclencheur d’Azure planificateur ou des facteurs externes, tels que les modifications apportées à l’espace de stockage BLOB et les files d’attente. Tâches peuvent être démarrés et arrêtés à la demande et s’arrête. Si un WebJob s’exécute en permanence échoue, il est automatiquement redémarré. Actions d’erreur et des nouvelles tentatives peuvent être configurées.

Lorsque vous configurez un WebJob :

- Si vous souhaitez que la tâche pour répondre à un déclencheur d’événement par l’effort, vous devez le configurer comme **Exécuter en permanence**. Le script ou le programme est stocké dans le dossier nommé site/wwwroot/app_data/travaux/continue.
- Si vous souhaitez que la tâche pour répondre à un déclencheur pilotés par la planification, vous devez le configurer comme **exécuté sur un planning**. Le script ou le programme est stocké dans le dossier nommé site/wwwroot/app_data/travaux/déclenchée.
- Si vous choisissez l’option **Exécuter à la demande** lorsque vous configurez une tâche, elle s’exécute le même code que l’option **exécuter sur un planning** au démarrage.

WebJobs Azure s’exécutent dans le bac à sable de l’application web. Cela signifie qu’ils peuvent accéder aux variables d’environnement et partager des informations, telles que les chaînes de connexion, avec l’application web. Le travail a accès à l’identificateur unique de l’ordinateur qui exécute la tâche. La chaîne de connexion nommée **AzureWebJobsStorage** fournit l’accès à des files d’attente de stockage Azure BLOB et des tableaux pour les données d’application et l’accès aux Bus des services de messagerie et de communication. La chaîne de connexion nommée **AzureWebJobsDashboard** permet d’accéder aux fichiers journaux de l’action de la tâche.

Azure WebJobs présentent les caractéristiques suivantes :

- **Sécurité**: WebJobs sont protégés par les informations d’identification de déploiement de l’application web.
- **Types de fichiers pris en charge**: vous pouvez définir WebJobs à l’aide des scripts de commandes (.cmd), fichiers de commandes (.bat), des scripts PowerShell (.ps1), bash shell scripts (.sh), les scripts PHP (.php), Python scripts (.py), code JavaScript (.js) et programmes exécutables (.exe, .jar, etc.).
- **Déploiement**: vous pouvez déployer des scripts et exécutables à l’aide du portail Azure, à l’aide du complément [WebJobsVs](https://visualstudiogallery.msdn.microsoft.com/f4824551-2660-4afa-aba1-1fcc1673c3d0) pour Visual Studio ou [Visual Studio 2013 mise à jour 4](http://www.visualstudio.com/news/vs2013-update4-rc-vs) (vous pouvez créer et déployer avec cette option), en utilisant le [Kit de développement logiciel Azure WebJobs](./app-service-web/websites-dotnet-webjobs-sdk-get-started.md), ou en les copiant directement aux emplacements suivants :
  - Pour déclenché exécution : site/wwwroot/app_data/travaux/déclenchée / {travail nom}
  - Pour l’exécution en continu : site/wwwroot/app_data/travaux/continue / {travail nom}
- **Journalisation**: Console.Out est considéré (marqué) comme INFO. Console.Error est considéré comme erreur. Vous pouvez accéder aux informations de surveillance et les diagnostics à l’aide du portail Azure. Vous pouvez télécharger les fichiers journaux directement à partir du site. Ils sont enregistrés dans les emplacements suivants :
  - Pour déclenché exécution : Vfs/données/travaux/déclenchée/jobName
  - Pour l’exécution en continu : Vfs/données/travaux/continue/jobName
- **Configuration**: vous pouvez configurer WebJobs à l’aide du portail de l’API REST et PowerShell. Vous pouvez utiliser un fichier de configuration nommé settings.job dans le même répertoire racine que le script de travail pour fournir des informations de configuration pour effectuer une tâche. Par exemple :
  - {« stopping_wait_time » : 60}
  - {« is_singleton » : true}

### <a name="considerations"></a>Considérations relatives à la

- Par défaut, WebJobs échelle avec l’application web. Toutefois, vous pouvez configurer des tâches à exécuter sur instance unique en définissant la propriété de configuration **is_singleton** sur **true**. Instance unique WebJobs sont utiles pour les tâches que vous ne souhaitez pas mettre à l’échelle ou exécuter en tant que simultanément plusieurs instances, tels que réindexation analyse des données et des tâches similaires.
- Pour réduire l’impact des travaux sur les performances de l’application web, envisagez de créer une instance de l’application Web Azure vide dans une nouvelle offre de Service d’application à hôte WebJobs qui peuvent être longue ou une ressource intensive.

### <a name="more-information"></a>Plus d’informations

- [Azure WebJobs recommandé ressources](./app-service-web/websites-webjobs-resources.md) répertorie les nombreuses ressources utiles, téléchargements et exemples pour WebJobs.

## <a name="azure-cloud-services-web-and-worker-roles"></a>Azure Cloud Services web et collaborateur les rôles

Vous pouvez exécuter des tâches en arrière-plan au sein d’un rôle web ou dans un rôle de travail distinct. Lorsque vous décidez si vous voulez utiliser un rôle de collaborateur, envisagez d’extensibilité élevées et configuration requise élasticité, durée de vie de tâche, relâchez cadence, la sécurité, tolérance de panne, conflit, la complexité et l’architecture logique. Pour plus d’informations, voir [Calculer un motif de Consolidation de ressources](http://msdn.microsoft.com/library/dn589778.aspx).

Il existe plusieurs manières d’implémenter des tâches en arrière-plan au sein d’un rôle des Services Cloud :

- Créez une implémentation de la classe **RoleEntryPoint** dans le rôle et ses méthodes permet d’exécuter des tâches en arrière-plan. Les tâches s’exécutent dans le contexte de WaIISHost.exe. La méthode **GetSetting** de la classe **CloudConfigurationManager** ils permet de charger les paramètres de configuration. Pour plus d’informations, consultez [cycle de vie (Cloud Services)](#lifecycle-cloud-services).
- Tâches de démarrage permet d’exécuter des tâches en arrière-plan lorsque l’application démarre. Pour forcer les tâches à continuer à s’exécuter en arrière-plan, définissez la propriété **taskType** sur **arrière-plan** (si vous ne procédez pas cela, le processus de démarrage application arrêtera et attendez que la tâche se termine). Pour plus d’informations, voir [exécuter des tâches de démarrage dans Azure](./cloud-services/cloud-services-startup-tasks.md).
- Utiliser le SDK WebJobs pour mettre en œuvre des tâches en arrière-plan comme WebJobs démarrée comme une tâche de démarrage. Pour plus d’informations, voir [prise en main du Kit de développement WebJobs Azure](./app-service-web/websites-dotnet-webjobs-sdk-get-started.md).
- Utilisez une tâche de démarrage pour installer un service Windows qui s’exécute une ou plusieurs tâches en arrière-plan. Vous devez définir la propriété **taskType** à **l’arrière-plan** afin que le service s’exécute en arrière-plan. Pour plus d’informations, voir [exécuter des tâches de démarrage dans Azure](./cloud-services/cloud-services-startup-tasks.md).

### <a name="running-background-tasks-in-the-web-role"></a>Tâches en arrière-plan en cours d’exécution dans le rôle web

Le principal avantage de l’exécution de tâches en arrière-plan dans le rôle web est l’enregistrement dans les coûts d’hébergement, car il n’est pas obligatoire pour déployer des rôles supplémentaires.

### <a name="running-background-tasks-in-a-worker-role"></a>Tâches en arrière-plan en cours d’exécution dans un rôle de collaborateur

L’exécution de tâches en arrière-plan dans un rôle de collaborateur présente plusieurs avantages :

- Il vous permet de gérer la mise à l’échelle séparément pour chaque type de rôle. Par exemple, vous devrez peut-être plusieurs instances d’un rôle web pour prendre en charge la charge, mais moins d’instances de ce rôle de collaborateur qui exécute les tâches en arrière-plan. En mise à l’échelle d’arrière-plan tâche cluster instances séparément les rôles de l’interface utilisateur, vous pouvez réduire les coûts d’hébergement, tout en conservant les performances acceptables.
- Il libère la charge de traitement des tâches en arrière-plan du rôle web. Le rôle web qui fournit l’interface utilisateur peut rester réactif, et il peut indiquer que moins d’instances sont requises pour prendre en charge un volume de demandes provenant des utilisateurs.
- Il vous permet d’implémenter séparation des problèmes. Chaque type de rôle peut implémenter un ensemble spécifique de tâches clairement définis et connexes. Cela permet de concevoir et gérer le code plus facile, car il est moins interdépendance de code et les fonctionnalités entre chaque rôle.
- Il peut vous aider à isoler les données et processus sensibles. Par exemple, rôles web qui implémentent l’interface utilisateur est inutile d’accéder aux données qui sont gérées et contrôlées par un rôle de collaborateur. Cela peut être utile de renforcer la sécurité, notamment lorsque vous utilisez un modèle tel que le [Modèle de contrôle d’appels](http://msdn.microsoft.com/library/dn589793.aspx).  

### <a name="considerations"></a>Considérations relatives à la

Tenez compte des points suivants lorsque vous choisissez où et comment déployer des tâches en arrière-plan lorsque vous utilisez des rôles web et collaborateur Services Cloud :

- Hébergement des tâches en arrière-plan dans un rôle web existant peut enregistrer le coût d’un rôle travail distinct uniquement pour les tâches suivantes en cours d’exécution. Toutefois, il est susceptible d’affecter les performances et la disponibilité de l’application si il existe un conflit de transformation et d’autres ressources. En utilisant un rôle travail distinct protège le rôle web à partir de l’impact des tâches en arrière-plan longue ou beaucoup de ressources.
- Si vous hébergez des tâches en arrière-plan à l’aide de la classe **RoleEntryPoint** , vous pouvez facilement ce déplacer vers un autre rôle. Par exemple, si vous créez la classe dans un rôle web et que vous décidez ensuite que vous devez exécuter les tâches dans un rôle de collaborateur, vous pouvez déplacer l’implémentation de classe **RoleEntryPoint** dans le rôle de travail.
- Tâches de démarrage sont conçues pour exécuter un programme ou un script. Déploiement d’une tâche en arrière-plan en tant que programme exécutable peut être plus difficile, surtout si elle implique également déploiement d’assemblys dépendants. Il peut être plus facile à déployer et utiliser un script pour définir une tâche en arrière-plan lorsque vous utilisez des tâches de démarrage.
- Exceptions occasionnant une tâche en arrière-plan échec ont une incidence différente, selon la manière qu’ils sont hébergés :
  - Si vous utilisez l’approche de classe **RoleEntryPoint** , le rôle de redémarrer afin que la tâche redémarre automatiquement va entraîner l’échec d’une tâche. Cela peut affecter la disponibilité de l’application. Pour éviter ce problème, veillez à inclure des exceptions robuste au sein de la classe **RoleEntryPoint** et toutes les tâches en arrière-plan. Code permet de redémarrer des tâches qui échouent lorsque cela est approprié, puis lever l’exception pour reprendre le rôle uniquement si vous ne pouvez pas récupérer normalement à partir de l’échec au sein de votre code.
  - Si vous utilisez des tâches de démarrage, vous êtes responsable de la gestion de l’exécution de tâche et en cas d’échec de la vérification.
- Gestion des tâches et de surveillance démarrage sont plus difficile qu’à l’aide de l’approche de classe **RoleEntryPoint** . Toutefois, le Kit de développement WebJobs Azure inclut un tableau de bord pour faciliter la gestion WebJobs que vous lancer dans les tâches de démarrage.

### <a name="more-information"></a>Plus d’informations

- [Calculer un motif de Consolidation des ressources](http://msdn.microsoft.com/library/dn589778.aspx)
- [Prise en main du Kit de développement WebJobs Azure](./app-service-web/websites-dotnet-webjobs-sdk-get-started.md)

## <a name="azure-virtual-machines"></a>Machines virtuelles Azure

Tâches en arrière-plan peuvent être implémentées d’une manière qui empêche le déployé pour Azure Web Apps ou les Services en nuage ou ces options peuvent ne pas être pratiques. Exemples : services de Windows, utilitaires tiers et programmes exécutables. Un autre exemple peut-être être écrites pour un environnement d’exécution qui est différent de celui qui héberge l’application de programmes. Par exemple, il peut être un programme Unix ou Linux que vous voulez exécuter à partir d’une application Windows ou .NET. Vous pouvez choisir parmi une gamme de systèmes d’exploitation pour une machine virtuelle Azure et exécuter votre service ou l’exécutable sur cet ordinateur virtuel.

Pour vous aider à choisir d’utiliser des Machines virtuelles, voir [Azure application Services, de Services de Cloud et de comparaison des Machines virtuelles](./app-service-web/choose-web-site-cloud-service-vm.md). Pour plus d’informations sur les options pour les ordinateurs virtuels, consultez [tailles Machine virtuelle et Service Cloud pour Azure](http://msdn.microsoft.com/library/azure/dn197896.aspx). Pour plus d’informations sur les systèmes d’exploitation et les images prédéfinis qui sont disponibles pour les ordinateurs virtuels, consultez [Machines virtuelles Azure Marketplace](https://azure.microsoft.com/gallery/virtual-machines/).

Pour lancer la tâche d’arrière-plan dans un ordinateur virtuel distinct, vous avez des options :

- Vous pouvez exécuter la tâche à la demande directement à partir de votre application en envoyant une requête à un point de terminaison expose la tâche. Cela passe dans toutes les données qu’elle nécessite. Ce point de terminaison appelle la tâche.
- Vous pouvez configurer la tâche à exécuter sur un planning en utilisant un planificateur ou le minuteur est disponible dans votre système d’exploitation choisi. Par exemple, sur Windows, vous pouvez utiliser le Planificateur de tâches Windows pour exécuter des scripts et des tâches. Ou, si vous avez installé sur la machine virtuelle SQL Server, vous pouvez utiliser l’Agent SQL Server pour exécuter des scripts et des tâches.
- Vous pouvez utiliser le planificateur Azure pour lancer la tâche en ajoutant un message à une file d’attente écoute la tâche, ou en envoyant une requête à une API expose la tâche.

Voir la section précédente [déclencheurs](#triggers) pour plus d’informations sur la façon dont vous pouvez lancer des tâches en arrière-plan.  

### <a name="considerations"></a>Considérations relatives à la

Lorsque vous décidez s’il faut déployer des tâches en arrière-plan sur une machine virtuelle Azure, tenez compte des points suivants :

- Tâches en arrière-plan sur une machine virtuelle Azure distincte d’hébergement offre une flexibilité et permet de contrôler précisément d’initiation, l’exécution, la planification et la répartition des ressources. Toutefois, il augmente coûteuses à l’exécution si une machine virtuelle doit être déployée simplement pour exécuter des tâches en arrière-plan.
- Il n’existe aucune fonction pour surveiller les tâches dans le portail Azure et aucune fonction de redémarrage automatisé pour les tâches qui ont échoué--bien que vous pouvez surveiller l’état de base de la machine virtuelle et gérer à l’aide de la [Gestion des applets de commande Azure](http://msdn.microsoft.com/library/azure/dn495240.aspx). Toutefois, il n’existe pas de fonction permettant de contrôler les processus et threads dans les nœuds de calcul. En règle générale, à l’aide d’une machine virtuelle nécessite des efforts supplémentaires pour implémenter un mécanisme de collecte des données à partir d’instrumentation dans la tâche et à partir du système d’exploitation de la machine virtuelle. Une solution qui peut s’avérer utile consiste à utiliser le [Management Pack System Center pour Azure](http://technet.microsoft.com/library/gg276383.aspx).
- Vous pouvez envisager de créer des sondes surveillance exposés via des points de terminaison HTTP. Le code de ces peut vérifications au niveau, collecter les informations opérationnelles ainsi que les statistiques--ou collecter les informations d’erreur et revenir à une application de gestion. Pour plus d’informations, voir [État du point de terminaison de surveillance motif](http://msdn.microsoft.com/library/dn589789.aspx).

### <a name="more-information"></a>Plus d’informations

- [Machines virtuelles](https://azure.microsoft.com/services/virtual-machines/) sur Azure
- [Forum aux questions sur Machines virtuelles Azure](virtual-machines/virtual-machines-linux-classic-faq.md)

## <a name="design-considerations"></a>Considérations relatives à la conception

Il existe plusieurs facteurs fondamentaux à prendre en considération lors de la création de tâches en arrière-plan. Les sections suivantes traitent partition, les conflits et débute.

## <a name="partitioning"></a>Partition

Si vous décidez d’inclure des tâches en arrière-plan au sein d’une instance de calcul existante (par exemple, une application web, rôle web, existant rôle de travail ou machine virtuelle), vous devez tenir compte comment cela affecte les attributs de qualité de l’instance cluster et la tâche d’arrière-plan. Ces facteurs vous aidera à décider si vous souhaitez peut-être co-implanter les tâches à l’instance de calcul existante ou séparez-les dans une instance de calcul distinctes :

- **Disponibilité**: tâches en arrière-plan n’ayez pas à avoir le même niveau de disponibilité d’autres parties de l’application, notamment l’interface utilisateur et autres composants directement impliqués dans l’interaction de l’utilisateur. Tâches en arrière-plan peuvent être plus tolérance de latence, les échecs de nouvelle tentative de connexion et autres facteurs qu’affecte-t-elle la disponibilité, car les opérations peuvent être mis en attente. Toutefois, vous devez être capacité suffisante pour éviter la sauvegarde de requêtes qui pourrait bloquer files d’attente et affecter l’application dans son ensemble.
- **Extensibilité du**: tâches en arrière-plan sont susceptibles d’avoir une exigence extensibilité élevées autre que l’interface utilisateur et les composants interactifs de l’application. Mise à l’échelle de l’interface utilisateur peut s’avérer nécessaire pour répondre aux pointes de la demande, tandis que les tâches en arrière-plan en suspens peuvent être effectuées pendant les heures de moins occupé (e) par un moins nombre d’instances de calcul.
- **Résilience**: échec d’une instance de calcul qui héberge uniquement les tâches en arrière-plan peut affecter mortellement pas l’application dans son ensemble si les demandes de ces tâches peuvent être en attente ou différées jusqu'à ce que la tâche est disponible à nouveau. Si l’instance de calcul et/ou tâches peuvent être redémarrés au sein d’un intervalle approprié, les utilisateurs de l’application ne peuvent pas être affectées.
- **Sécurité**: tâches en arrière-plan peuvent avoir des exigences de sécurité différentes ou restrictions à l’interface utilisateur ou d’autres parties de l’application. En utilisant une instance de calcul distincte, vous pouvez spécifier un environnement de sécurité différentes pour les tâches. Vous pouvez également utiliser des motifs comme opérateur à isoler les instances cluster d’arrière-plan à partir de l’interface utilisateur afin d’optimiser la sécurité et la séparation.
- **Performances**: vous pouvez choisir le type d’instance de calcul pour les tâches en arrière-plan à spécifiquement respecter les exigences de performances des tâches. Cela peut signifie en utilisant une option de calcul cher si les tâches ne nécessitent pas les mêmes fonctionnalités de traitement que l’interface utilisateur ou une instance supérieure s’ils ont besoin de ressources et des capacités supplémentaires.
- **Facilité de gestion**: tâches en arrière-plan peuvent avoir un autre rythme développement et de déploiement de code de l’application principale ou de l’interface utilisateur. Les déployer dans une instance de calcul distincte peuvent simplifier mises à jour et le contrôle de version.
- **Coût**: ajout de calculer les instances d’exécuter en arrière-plan tâches augmente les coûts d’hébergement. Réfléchissez attentivement le compromis entre la capacité supplémentaire et ces coûts supplémentaires.

Pour plus d’informations, voir [Points de suite élections motif](http://msdn.microsoft.com/library/dn568104.aspx) et [En patinage consommateurs motif](http://msdn.microsoft.com/library/dn568101.aspx).

## <a name="conflicts"></a>Conflits

Si vous avez plusieurs instances d’une tâche en arrière-plan, il est possible qu’ils entrent en concurrence pour accéder aux ressources et des services, tels que des bases de données et de stockage. Cet accès simultané peut entraîner des conflits de ressources, qui peuvent provoquer des conflits dans la disponibilité des services et de l’intégrité des données dans le stockage. Vous pouvez résoudre les conflits de ressources à l’aide d’une approche de verrouillage pessimiste. Cela permet d’éviter en patinage instances d’une tâche simultanément l’accès à un service ou altérer les données.

Une autre méthode pour résoudre les conflits consiste à définir des tâches en arrière-plan en tant que singleton, afin qu’il n’y jamais qu’une seule instance en cours d’exécution. Toutefois, cela supprime les avantages de la fiabilité et les performances qui fournit une configuration plusieurs instances. Cela est particulièrement vrai si l’interface utilisateur peut fournir la quantité de travail suffisante pour conserver plusieurs tâches en arrière-plan occupé (e).

Il est essentiel de vous assurer que la tâche d’arrière-plan peut redémarrer automatiquement et qu’il a une capacité suffisante pour respecter les pointes de la demande. Vous pouvez y parvenir en attribuant une instance de calcul avec les ressources nécessaires, en mettant en œuvre un mécanisme file d’attente qui peut stocker des demandes d’exécution ultérieure lorsque la demande diminue, ou en utilisant une combinaison de ces techniques.

## <a name="coordination"></a>Débute

Les tâches en arrière-plan peuvent être complexes et peuvent nécessiter plusieurs tâches individuelles à exécuter pour produire un résultat ou afin de satisfaire la configuration requise. Il est courant dans les scénarios suivants pour diviser la tâche en plus petites discrètes étapes ou des tâches subordonnées pouvant être exécutées par plusieurs clients. Tâches en plusieurs étapes peuvent être plus efficaces et plus flexible car étapes individuels peuvent être réutilisables dans plusieurs tâches. Il est également facile à ajouter, supprimer ou modifier l’ordre des étapes.

Coordonner plusieurs tâches et des étapes peut s’avérer difficile, mais il existe trois modèles courants que vous pouvez utiliser pour guider votre implémentation d’une solution :

- **La décomposition d’une tâche en plusieurs étapes réutilisables**. Une application peut être nécessaire pour effectuer diverses tâches de complexité variable sur les informations qu’il traite. Une approche simple mais stricte en œuvre cette application peut être pour effectuer ce traitement comme un module monolithique. Toutefois, cette approche est susceptible de réduire les risques de refactorisation du code, optimisant ou réutiliser si des parties de la même transformation sont requises ailleurs dans l’application. Pour plus d’informations, voir [canaux et le motif de filtres](http://msdn.microsoft.com/library/dn568100.aspx).
- **Gestion de l’exécution des étapes d’une tâche**. Une application peut effectuer des tâches qui comprennent un nombre d’étapes (certains d'entre eux peuvent appeler des services à distance ou accéder aux ressources distantes). Les étapes individuels peuvent être indépendants entre eux, mais ils sont déclenchés par la logique d’application qui mettent en œuvre, la tâche. Pour plus d’informations, voir [Planificateur Agent responsable motif](http://msdn.microsoft.com/library/dn589780.aspx).
- **Gestion de la récupération des étapes de tâche qui ne satisfont pas**. Une application peut avoir besoin annuler le travail effectué par une série d’étapes (qui définissent une opération finalement cohérente) si une ou plusieurs des étapes échouent. Pour plus d’informations, voir [Compensation Transaction motif](http://msdn.microsoft.com/library/dn589804.aspx).

## <a name="lifecycle-cloud-services"></a>Cycle de vie (Cloud Services)

 Si vous décidez d’implémenter des travaux en arrière-plan pour les applications de Services de Cloud qui utilisent des rôles web et de travail à l’aide de la classe **RoleEntryPoint** , il est important de comprendre le cycle de vie de ce cours pour pouvoir utiliser correctement.

Rôles Web et de travail, passez par un ensemble de phases distinctes comme elles Démarrer, exécutent et arrêter. La classe **RoleEntryPoint** expose une série d’événements qui indiquent lorsque ces étapes sont en cours. Vous utilisez ces initialisation, exécuter et arrêtez vos tâches arrière-plan personnalisé. La durée du cycle est la suivante :

- Azure charge l’assembly de rôle et de recherche pour une classe qui est dérivée de **RoleEntryPoint**.
- S’il trouve ce cours, il appelle **RoleEntryPoint.OnStart()**. Vous ignorer cette méthode pour initialisation vos tâches en arrière-plan.
- À l’issue de la méthode **OnStart** , appels Azure **Application_Start()** fichier Global de l’application si cela est présentent (par exemple, Global.asax dans un rôle web ASP.NET en cours d’exécution).
- Azure appelle **RoleEntryPoint.Run()** sur un nouveau thread de premier plan qui s’exécute en parallèle avec **OnStart()**. Vous ignorer cette méthode pour démarrer vos tâches en arrière-plan.
- Lorsque la méthode Run se termine, Azure appelle tout d’abord **Application_End()** fichier Global de l’application si cela est présente, puis appelle **RoleEntryPoint.OnStop()**. Vous ignorer la méthode **OnStop** pour arrêter de vos tâches en arrière-plan, nettoyer les ressources, supprimer les objets et fermer les connexions les tâches peuvent avoir utilisé.
- Le processus hôte rôle de collaborateur Azure est arrêté. À ce stade, le rôle sera suppression initiale et va redémarrer.

Pour plus d’informations et un exemple de l’aide des méthodes de la classe **RoleEntryPoint** , voir [Calculer un motif de Consolidation de ressources](http://msdn.microsoft.com/library/dn589778.aspx).

## <a name="considerations"></a>Considérations relatives à la

Tenez compte des points suivants lorsque vous planifiez comment vous exécutent les tâches en arrière-plan dans un rôle web ou de travail :

- L’implémentation de méthode **exécuter** par défaut de la classe **RoleEntryPoint** contienne un appel à **thread.Sleep (Timeout.Infinite)** qui conserve le rôle actif indéfiniment. Si vous remplacez la méthode **Run** (qui est généralement nécessaire à l’exécution des tâches en arrière-plan), vous ne devez pas autoriser votre code quitter la méthode sauf si vous souhaitez Corbeille de l’instance de rôle.
- Une implémentation classique de la méthode **Run** inclut du code pour démarrer chacun des tâches en arrière-plan et un élément de boucle qui vérifie régulièrement l’état de toutes les tâches en arrière-plan. Il peut redémarrer celles qui échouent ou contrôler les jetons d’annulation qui indiquent que les tâches terminées.
- Si une tâche en arrière-plan lève une exception non gérée, cette tâche doit être suppression initiale tout en autorisant d’autres tâches d’arrière-plan dans le rôle continue de fonctionner. Toutefois, si l’exception est due à l’altération des objets en dehors de la tâche, par exemple de stockage partagé, l’exception doit être gérée par votre cours **RoleEntryPoint** , toutes les tâches doivent être annulées et la méthode **Run** doit être autorisée à la fin. Azure redémarrent le rôle.
- Utilisez la méthode **OnStop** pour suspendre ou arrêter les tâches en arrière-plan et nettoyer les ressources. Il faudra peut-être arrêter des tâches longues ou en plusieurs étapes. Il est essentiel à prendre en considération comment vous pouvez faire pour éviter les incohérences de données. Si une instance de rôle s’arrête pour une raison quelconque autre qu’un arrêt initialisé par l’utilisateur, le code en cours d’exécution dans la méthode **OnStop** doit être effectuée dans les cinq minutes avant qu’elle est fermée force. Vérifiez que votre code peut être effectuée pendant cette période ou tolère ne pas en cours d’exécution jusqu'à la fin.  
- L’équilibrage de charge Azure démarre diriger le trafic vers l’instance de rôle lorsque la méthode **RoleEntryPoint.OnStart** renvoie la valeur **true**. Par conséquent, envisagez de placer tout votre code d’initialisation dans la méthode **OnStart** afin que les instances de rôle ne pas correctement initialisé ne recevront pas le trafic.
- Vous pouvez utiliser les tâches de démarrage outre les méthodes de la classe **RoleEntryPoint** . Vous devez utiliser des tâches de démarrage initialisation tous les paramètres que vous devez modifier dans l’équilibrage de charge Azure, car ces tâches seront exécuté avant que le rôle reçoit toutes les requêtes. Pour plus d’informations, voir [exécuter des tâches de démarrage dans Azure](./cloud-services/cloud-services-startup-tasks.md).
- S’il existe une erreur dans une tâche de démarrage, il peut forcer le rôle de redémarrer continuellement. Cela peut vous empêcher d’effectuer un échange d’adresse IP (VIP) virtuel à une version précédemment intermédiaire, car le remplacement requiert un accès exclusif au rôle. Cela ne peut être obtenu pendant le redémarrage le rôle. Pour résoudre ce problème :
    -  Ajoutez le code suivant au début des méthodes **OnStart** et **exécuter** dans votre rôle :

    ```C#
    var freeze = CloudConfigurationManager.GetSetting("Freeze");
    if (freeze != null)
    {
        if (Boolean.Parse(freeze))
        {
            Thread.Sleep(System.Threading.Timeout.Infinite);
        }
    }
    ```

   - Ajoutez la définition du paramètre **Figer** comme une valeur booléenne au ServiceDefinition.csdef et ServiceConfiguration. *fichiers .cscfg pour le rôle et le configurer* *faux* *. Si le rôle se prolonge dans un mode de redémarrer répétées, vous pouvez modifier le paramètre pour * *Vrai** à figer l’exécution de rôle et lui permettre d’être remplacée par une version antérieure.

## <a name="resiliency-considerations"></a>Considérations relatives à la résilience

Tâches en arrière-plan doivent être résistants afin de fournir des services fiables à l’application. Lorsque vous planifiez et création de tâches en arrière-plan, tenez compte des points suivants :

- Tâches en arrière-plan doivent être en mesure de gérer correctement le redémarrage de rôle ou service sans altérer les données ni introduire incohérence dans l’application. Pour les tâches longues ou en plusieurs étapes, envisagez d’utiliser la _vérification du pointage_ en enregistrant l’état des tâches dans un stockage permanent, ou sous forme de messages dans une file d’attente si cela est approprié. Par exemple, vous pouvez conserver les informations d’état dans un message dans une file d’attente et mettre à jour de progressivement ces informations d’état avec l’avancement des tâches afin que la tâche peut être traitée à partir de la dernière connu bon point de contrôle--au lieu de redémarrer à partir du début. Lorsque vous utilisez des files d’attente Bus des services Azure, vous pouvez utiliser les sessions de messagerie pour activer le même scénario. Sessions permettent d’enregistrer et récupérer l’état de traitement de l’application en utilisant les méthodes [SetState](http://msdn.microsoft.com/library/microsoft.servicebus.messaging.messagesession.setstate.aspx) et [GetState](http://msdn.microsoft.com/library/microsoft.servicebus.messaging.messagesession.getstate.aspx) . Pour plus d’informations sur la création de flux de travail et processus en plusieurs étapes fiables, voir [Planificateur Agent responsable motif](http://msdn.microsoft.com/library/dn589780.aspx).
- Lorsque vous utilisez des rôles web ou de travail pour héberger plusieurs tâches en arrière-plan, créez votre substitution de la méthode **Run** pour surveiller des tâches a échoué ou bloquées et les redémarrer. Lorsque cela n’est pas pratique, et que vous utilisez un rôle de collaborateur, forcer le rôle de collaborateur redémarrer en partant de la méthode **Run** .
- Lorsque vous utilisez des files d’attente de communiquer avec des tâches en arrière-plan, les files d’attente peuvent agir en tant que tampon pour stocker les requêtes qui sont envoyées aux tâches lors de l’application est sous supérieures à celles de chargement habituelle. Ainsi, les tâches afin de prendre en compte l’interface utilisateur pendant les périodes occupé (e). Cela signifie également que le rôle de recyclage se bloque pas l’interface utilisateur. Pour plus d’informations, voir [file d’attente charge audit motif](http://msdn.microsoft.com/library/dn589783.aspx). Si certaines tâches sont plus importants que d’autres personnes, envisagez d’implémenter la [Priorité file d’attente motif](http://msdn.microsoft.com/library/dn589794.aspx) pour vous assurer que ces tâches exécutées avant celles moins importants.
- Tâches en arrière-plan qui sont initiées par messages ou traiter les messages doivent être conçus pour gérer les éventuelles incohérences détectées, tels que messages arrivant désordre, messages à plusieurs reprises génèrent une erreur (souvent appelée _messages poison_) et messages remis plusieurs fois. Prenez en compte les éléments suivants :
  - Messages qui doivent être traitées dans un ordre spécifique, tels que ceux qui modifient les données en fonction de la valeur de données existante (par exemple, l’ajout d’une valeur à une valeur existante), peut ne pas arriver dans l’ordre dans lequel ils ont été envoyés. Par ailleurs, ils peuvent être gérés par des instances différentes d’une tâche d’arrière-plan dans un ordre différent en raison de charges à variation sur chaque instance. Les messages qui doivent être traitées dans un ordre spécifique doivent inclure un numéro de séquence, touche ou certains indicateur de tâches en arrière-plan permet de vous assurer qu’elles sont traitées dans l’ordre correct. Si vous utilisez Bus des services Azure, vous pouvez utiliser les sessions de messagerie afin de garantir l’ordre de remise. Toutefois, il est généralement plus efficace, si possible, à concevoir le processus de sorte que l’ordre des messages n’est pas important.
  - En règle générale, une tâche en arrière-plan sera lire les messages dans la file d’attente, qui masque temporairement les auprès des autres consommateurs de message. Il supprime ensuite les messages une fois qu’ils ont été traités. Si une tâche en arrière-plan échoue lors du traitement d’un message, ce message s’affiche à nouveau dans la file d’attente après l’expiration d’aperçu. Il sera traité par une autre instance de la tâche ou lors du prochain cycle de traitement de cette instance. Si le message génère toujours une erreur dans le client, il se bloque la tâche, la file d’attente et finalement l’application elle-même lorsque la file d’attente est plein. Par conséquent, il est essentiel de détecter et de supprimer des messages incohérents à partir de la file d’attente. Si vous utilisez Bus des services Azure, les messages qui génèrent une erreur peuvent être déplacés automatiquement ou manuellement à une file d’attente lettre inactive associé.
  - Files d’attente sont garanties au _moins une fois_ les mécanismes de remise, mais elles peuvent effectuer plusieurs fois le même message. En outre, si une tâche en arrière-plan échoue après le traitement d’un message, mais avant de le supprimer à partir de la file d’attente, le message sera disponible pour le traitement à nouveau. Tâches en arrière-plan doivent être idempotents, ce qui signifie que le même message de traitement plusieurs fois n’entraîne pas une erreur ou incohérence dans les données d’application. Certaines opérations sont naturellement idempotent, telles que la définition d’une valeur de stockage pour une nouvelle valeur spécifique. Toutefois, opérations, telles que l’ajout d’une valeur à une valeur stockée sans vérifier que la valeur stockée est toujours différente de celle dans laquelle le message a été envoyé à l’origine va entraîner des incohérences. Azure files d’attente de Bus de Service peuvent être configurés pour supprimer automatiquement les messages en double.
  - Certains systèmes de messagerie, tels que le stockage Azure files d’attente et Bus des services Azure, prend en charge une propriété count de-queue qui indique le nombre de fois qu’un message a été lu à partir de la file d’attente. Cela peut être utile de gestion des messages poison et répétées. Pour plus d’informations, voir [Introduction à la messagerie asynchrone](http://msdn.microsoft.com/library/dn589781.aspx) et [Des motifs idempotence](http://blog.jonathanoliver.com/2010/04/idempotency-patterns/).

## <a name="scaling-and-performance-considerations"></a>Considérations relatives à la mise à l’échelle et les performances

Tâches en arrière-plan doivent proposer des performances suffisantes pour vous assurer qu’ils ne pas bloquer l’application ou de causer des incohérences en raison de l’opération différée lorsque le système est tendu. En règle générale, les performances sont améliorées en redimensionnant les instances cluster qui hébergent les tâches en arrière-plan. Lorsque vous planifiez et création de tâches en arrière-plan, tenez compte des points suivants autour de performances et extensibilité élevées :

- Prend en charge Azure autoscaling (mise à l’échelle et mise à l’échelle dans) en fonction de la demande et charge--ou selon un planning prédéfini, pour les applications Web, web Services Cloud et les rôles de travail et Machines virtuelles hébergé déploiements. Cette fonctionnalité permet de garantir que l’application entier est suffisamment performant et en limitant les coûts d’exécution.
- L’emplacement dans lequel les tâches en arrière-plan bénéficier d’une capacité de performances différentes à partir des autres parties d’une application de Services de Cloud (par exemple, l’interface utilisateur ou les composants tels que la couche d’accès aux données), qui héberge les tâches en arrière-plan collaborer dans un rôle de travail séparé permet de l’interface utilisateur et un arrière-plan rôles de tâche à l’échelle indépendamment pour gérer la charge. Si plusieurs tâches en arrière-plan des performances significatives à partir de l’autre, pensez à leur division en rôles de travail séparé et la même échelle indépendamment de chaque type de rôle. Toutefois, notez que cela peut accroître les coûts runtime par rapport à la combinaison de toutes les tâches en moins rôles.
- Tout simplement en redimensionnant les rôles n’est peut-être pas suffisant pour éviter les pertes de performances sous charge. Vous devrez également à l’échelle des files d’attente de stockage et autres ressources pour éviter un point générale de la chaîne de devient un élément critique de traitement. Envisagez également d’autres limitations, telles que le débit maximal de stockage et d’autres services qui en dépendent l’application et les tâches en arrière-plan.
- Tâches en arrière-plan doivent être conçus pour suivre l’évolution. Par exemple, ils doivent être en mesure de détecter dynamiquement le nombre de files d’attente de stockage en cours d’utilisation écoutent ou envoyer des messages à la file d’attente appropriée.
- Par défaut, WebJobs échelle avec leur instance Azure Web Apps associée. Toutefois, si vous souhaitez un WebJob exécuter en tant qu’une seule instance, vous pouvez créer un fichier Settings.job qui contient les données JSON **{« is_singleton » : true}**. Cela force Azure à n'exécuter qu’une instance de WebJob, même s’il existe plusieurs instances de l’application web associée. Cela peut être très utile pour les tâches planifiées qui doit s’exécuter en tant que qu’une seule instance.

## <a name="related-patterns"></a>Modèles connexes

- [Introduction à messagerie asynchrone](http://msdn.microsoft.com/library/dn589781.aspx)
- [Conseils AutoScaling](http://msdn.microsoft.com/library/dn589774.aspx)
- [Compensateurs motif Transaction](http://msdn.microsoft.com/library/dn589804.aspx)
- [Concurrents consommateurs motif](http://msdn.microsoft.com/library/dn568101.aspx)
- [Calculer partition recommandations](http://msdn.microsoft.com/library/dn589773.aspx)
- [Calculer un motif de Consolidation des ressources](http://msdn.microsoft.com/library/dn589778.aspx)
- [Modèle de contrôle d’appels](http://msdn.microsoft.com/library/dn589793.aspx)
- [Points de suite élections motif](http://msdn.microsoft.com/library/dn568104.aspx)
- [Canaux et filtres motif](http://msdn.microsoft.com/library/dn568100.aspx)
- [Priorité file d’attente motif](http://msdn.microsoft.com/library/dn589794.aspx)
- [Motif de nivellement de charge en fonction des file d’attente](http://msdn.microsoft.com/library/dn589783.aspx)
- [Modèle de planificateur Agent responsable](http://msdn.microsoft.com/library/dn589780.aspx)

## <a name="more-information"></a>Plus d’informations

- [Mise à l’échelle des Applications Azure avec les rôles de travail](http://msdn.microsoft.com/library/hh534484.aspx#sec8)
- [L’exécution de tâches en arrière-plan](http://msdn.microsoft.com/library/ff803365.aspx)
- [Cycle de vie de démarrage de rôle Azure](http://blog.syntaxc4.net/post/2011/04/13/windows-azure-role-startup-life-cycle.aspx) (billet de blog)
- [Cycle de vie rôle Azure Cloud Services](http://channel9.msdn.com/Series/Windows-Azure-Cloud-Services-Tutorials/Windows-Azure-Cloud-Services-Role-Lifecycle) (vidéo)
- [Prise en main du Kit de développement WebJobs Azure](./app-service-web/websites-dotnet-webjobs-sdk-get-started.md)
- [Azure files d’attente et Service Bus - comparés et la différence entre et](./service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md)
- [Comment activer les Diagnostics dans un Service Cloud](./cloud-services/cloud-services-dotnet-diagnostics.md)
