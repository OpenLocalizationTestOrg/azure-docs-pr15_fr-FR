<properties
 pageTitle="Entités, termes et concepts Scheduler | Microsoft Azure"
 description="Hiérarchie d’entités, y compris les tâches et des collections de travail, la terminologie et concepts Scheduler Azure.  Montre un exemple d’une tâche planifiée."
 services="scheduler"
 documentationCenter=".NET"
 authors="derek1ee"
 manager="kevinlam1"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="get-started-article"
 ms.date="08/18/2016"
 ms.author="deli"/>

# <a name="scheduler-concepts-terminology--entity-hierarchy"></a>Concepts de planificateur, terminologie + hiérarchie d’entités

## <a name="scheduler-entity-hierarchy"></a>Hiérarchie d’entités Scheduler

Le tableau suivant décrit les ressources principales exposé ou de l’API du planificateur :

|Ressource | Description |
|---|---|
|**Collection de sites de projet**|Une collection de travail contient un groupe de tâches et conserve les paramètres, les quotas et les limitations qui sont partagées par des tâches dans la collection. Une collection de travail est créée par un propriétaire de l’abonnement et regroupe les tâches ensemble sur la base des limites de l’utilisation ou d’une application. Il est limité à une zone géographique. Il permet également la mise en œuvre des quotas pour limiter l’utilisation de toutes les tâches dans cette collection. Les quotas incluent MaxJobs et MaxRecurrence.|
|**Tâche**|Un travail définit une action récurrente unique, des stratégies simples ou complexes pour l’exécution. Actions peuvent inclure HTTP, file d’attente stockage, file d’attente de service bus ou demandes de service bus rubrique.|
|**Historique du travail**|Un historique des travaux représentent les détails de l’exécution d’une tâche. Il contient réussite et échec, ainsi que les détails de la réponse.|

## <a name="scheduler-entity-management"></a>Gestion des entités Scheduler

À un niveau élevé, le planificateur et la gestion des services API exposent les opérations suivantes sur les ressources :

|Fonction|Description et URI l’adresse|
|---|---|
|**Gestion des collections de travail**|PLACER les obtenir et supprimer prise en charge pour créer et modifier des collections de travail et les tâches qui s’y trouvent. Une collection de travail est un conteneur pour les tâches et cartes quotas et des paramètres partagés. Nombre maximal de travaux et plus petit intervalle de périodicité sont des exemples de quotas, décrites plus loin. <p>PLACER et supprimer :`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}`</p><p>Télécharger :`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}`</p>
|**Gestion des tâches**|OBTENIR, placer, publier, correctif et supprimer prise en charge pour la création et modification des travaux. Toutes les tâches doivent appartenir à une collection de travail qui existe déjà, donc il n’existe aucune création implicite. <p>`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}/jobs/{jobName}`</p>|
|**Gestion de l’historique de travail**|OBTENIR une assistance technique pour l’extraction de 60 jours d’historique de l’exécution de tâche, telles que le temps de travail et les résultats de l’exécution de tâche. Ajoute le support de paramètre de chaîne de requête pour le filtrage en fonction de l’état et le statut. <P>`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}/jobs/{jobName}/history`</p>|

## <a name="job-types"></a>Types de tâches

Il existe plusieurs types de travaux : travaux HTTP (y compris les travaux HTTPS qui prennent en charge SSL) travaux en file d’espace de stockage, travaux en file de service bus et des travaux de rubrique bus de service. Tâches HTTP sont idéales si vous disposez d’un point de terminaison d’un service ou la charge de travail existant. Vous pouvez utiliser des travaux en file d’espace de stockage pour publier des messages dans les files d’attente de stockage, afin que ces tâches sont idéales pour les charges de travail qui utilisent des files d’attente de stockage. De même, les travaux de bus de service est idéal pour les charges de travail qui utilisent des rubriques et files d’attente de bus de service.

## <a name="the-job-entity-in-detail"></a>L’entité de « projet » dans le détail

Un niveau de base, une tâche planifiée compose plusieurs composants :

- L’action à effectuer lorsque le minuteur de la tâche se déclenche  

- (Facultatif) Le temps d’exécution de la tâche  

- (Facultatif) Quand et comment souvent à répéter la tâche  

- (Facultatif) Une action pour déclencher cas d’échec de l’action principale  

En interne, une tâche planifiée contienne également des données fournis par le système telles que la prochaine heure d’exécution.

Le code suivant fournit un exemple d’une tâche planifiée. Détails sont fournis dans les sections suivantes.

    {
        "startTime": "2012-08-04T00:00Z",               // optional
        "action":
        {
            "type": "http",
            "retryPolicy": { "retryType":"none" },
            "request":
            {
                "uri": "http://contoso.com/foo",        // required
                "method": "PUT",                        // required
                "body": "Posting from a timer",         // optional
                "headers":                              // optional

                {
                    "Content-Type": "application/json"
                },
            },
           "errorAction":
           {
               "type": "http",
               "request":
               {
                   "uri": "http://contoso.com/notifyError",
                   "method": "POST",
               },
           },
        },
        "recurrence":                                   // optional
        {
            "frequency": "week",                        // can be "year" "month" "day" "week" "minute"
            "interval": 1,                              // optional, how often to fire (default to 1)
            "schedule":                                 // optional (advanced scheduling specifics)
            {
                "weekDays": ["monday", "wednesday", "friday"],
                "hours": [10, 22]
            },
            "count": 10,                                 // optional (default to recur infinitely)
            "endTime": "2012-11-04",                     // optional (default to recur infinitely)
        },
        "state": "disabled",                           // enabled or disabled
        "status":                                       // controlled by Scheduler service
        {
            "lastExecutionTime": "2007-03-01T13:00:00Z",
            "nextExecutionTime": "2007-03-01T14:00:00Z ",
            "executionCount": 3,
                                                "failureCount": 0,
                                                "faultedCount": 0
        },
    }

Comme indiqué dans le travail planifié exemple ci-dessus, une définition de travail compose plusieurs composants :

- Heure de début (« heure de début »)  

- Action (« action »), qui inclut l’action d’erreur (« errorAction »)

- Périodicité (« périodicité »)  

- (« État »)  

- État (« status »)  

- Réessayer de stratégie (« retryPolicy »)  

Examinons chacune d’elles en détail :

## <a name="starttime"></a>heure de début

« Heure de début » est l’heure de début et permet à l’appelant spécifier un décalage de fuseau horaire sur le câble au [format ISO 8601](http://en.wikipedia.org/wiki/ISO_8601).

## <a name="action-and-erroraction"></a>action et errorAction

L’action « » est l’action appelée sur chaque occurrence et décrit un type d’appel de service. L’action est que sera exécuté sur l’échéancier fourni. Planificateur prend en charge HTTP, file d’attente stockage, rubrique bus de service et les actions de file d’attente de bus de service.

L’action dans l’exemple ci-dessus est une action HTTP. Voici un exemple d’une action de file d’attente de stockage :

    {
            "type": "storageQueue",
            "queueMessage":
            {
                "storageAccount": "myStorageAccount",  // required
                "queueName": "myqueue",                // required
                "sasToken": "TOKEN",                   // required
                "message":                             // required
                    "My message body",
            },
    }

Voici un exemple d’une action service bus rubrique.

  « action » : {« type » : « serviceBusTopic », « serviceBusTopicMessage » : {« topicPath » : « t1 »,  
      « espace de noms » : « mySBNamespace », « type de transport » : « netMessaging », / / peut être netMessaging ou AMQP « authentification » : {« sasKeyName » : « QPolicy », « type » : « sharedAccessKey »}, « message » : « Certains message », « brokeredMessageProperties » : {}, « customMessageProperties » : {« appname » : « FromScheduler »}},}

Voici un exemple d’une action de file d’attente de bus de service :


  « action » : {« serviceBusQueueMessage » : {« nom » : « T1 »,  
      « espace de noms » : « mySBNamespace », « type de transport » : « netMessaging », / / peut être netMessaging ou AMQP « authentification » : {}  
        « sasKeyName » : « QPolicy », « type » : « sharedAccessKey »}, « message » : « Certains message »  
      « brokeredMessageProperties » : {}, « customMessageProperties » : {« appname » : « FromScheduler »}}, « type » : « serviceBusQueue »}

La « errorAction » est le Gestionnaire d’erreur, l’action appelée en cas d’échec de l’action principale. Vous pouvez utiliser cette variable pour appeler un point de terminaison de gestion des erreurs ou envoyer une notification de l’utilisateur. Cela peut être utilisé pour atteindre un point de terminaison secondaire dans le cas que le serveur principal n’est pas disponible (par exemple, dans le cas d’urgence sur le site du point de terminaison) ou peut être utilisée pour informer une erreur de point de terminaison de la gestion des. Comme action principale, l’action d’erreur peut être simple ou composite logique en fonction des autres actions. Pour apprendre à créer un jeton associations de sécurité, reportez-vous à [créer et utiliser une Signature accès partagé](https://msdn.microsoft.com/library/azure/jj721951.aspx).

## <a name="recurrence"></a>Périodicité

Périodicité compose plusieurs composants :

- Fréquence : Une des minutes, heure, jour, semaine, mois, année  

- Intervalle : Intervalle à la fréquence donnée de la périodicité  

- Planification prescrite : spécifier des minutes, heures, jours de la semaine, mois et monthdays de la périodicité  

- Nombre : Nombre d’occurrences  

- Heure de fin : aucune tâche ne s’exécutera après l’heure de fin spécifiée  

Un projet est récurrent si elle comporte un objet périodique spécifié dans sa définition JSON. Si nombre et l’heure de fin sont spécifiés, la règle de saisie semi-automatique qui se produit en premier est appliquée.

## <a name="state"></a>état

L’état de la tâche est une des quatre valeurs : activé, désactivé, terminé ou défectueux. Vous pouvez placer ou travaux correctif afin de les mettre à jour à l’état activé ou désactivé. Si une tâche a été terminée ou défectueuse, c'est-à-dire un état final qui ne peut pas être mis à jour (même si la tâche peut toujours être supprimée). Exemple de la propriété state est la suivante :


        "state": "disabled", // enabled, disabled, completed, or faulted
Tâches terminées et défectueux sont supprimées après 60 jours.

## <a name="status"></a>état

Une fois qu’une tâche de planificateur a déjà commencé, informations sur l’état actuel de la tâche d’être renvoyées. Cet objet n’est pas être définie par l’utilisateur, il est défini par le système. Toutefois, il est inclus dans l’objet de traitement (plutôt qu’une ressource liée distincte) afin que le langage permet d’obtenir le statut d’un travail facilement.

État du travail inclut l’heure de l’exécution précédente (le cas échéant), l’heure de la prochaine exécution (pour les travaux en cours) et le nombre de l’exécution de la tâche.

## <a name="retrypolicy"></a>retryPolicy

Si une tâche de planificateur échoue, il est possible de spécifier une stratégie de nouvelles tentatives pour déterminer si et comment l’action est retentée. Cela est déterminé par l’objet **retryType** , il est défini sur **Aucun** si aucune stratégie réessayer, comme illustré ci-dessus. Configurer **corrigé** s’il existe une stratégie de réessayer.

Pour définir une stratégie de nouvelles tentatives, vous pouvez spécifier les deux paramètres supplémentaires : un intervalle (**retryInterval**) et le nombre de tentatives (**retryCount**).

L’intervalle de nouvelle tentative, avec l’objet **retryInterval** , est l’intervalle entre les tentatives. Sa valeur par défaut est de 30 secondes, sa valeur configurable minimale est de 15 secondes et sa valeur maximale est de 18 mois. Travaux dans les collections de travail gratuit ont une valeur minimale configurable de 1 heure.  Il est défini dans le format ISO8601. De même, la valeur du nombre de tentatives est spécifiée avec l’objet **retryCount** ; C’est le nombre de fois qu’une nouvelle tentative. Sa valeur par défaut est 4, et sa valeur maximale est 20\. les deux **retryInterval** et **retryCount** sont facultatifs. Ils sont fournis par défaut valeurs si **retryType** est défini sur **fixe** et aucune valeur spécifiée explicitement.

## <a name="see-also"></a>Voir aussi

 [Quel est le planificateur ?](scheduler-intro.md)

 [Commencer à utiliser le planificateur dans le portail Azure](scheduler-get-started-portal.md)

 [Les offres et facturation dans le planificateur d’Azure](scheduler-plans-billing.md)

 [Découvrez comment créer des planifications complexes et leur périodicité avancée avec le planificateur d’Azure](scheduler-advanced-complexity.md)

 [Référence de l’API REST Scheduler Azure](https://msdn.microsoft.com/library/mt629143)

 [Référence à des applets de commande PowerShell Scheduler Azure](scheduler-powershell-reference.md)

 [Azure Scheduler disponibilité et fiabilité](scheduler-high-availability-reliability.md)

 [Limites de planificateur Azure, les valeurs par défaut et codes d’erreur](scheduler-limits-defaults-errors.md)

 [Authentification sortante Scheduler Azure](scheduler-outbound-authentication.md)
