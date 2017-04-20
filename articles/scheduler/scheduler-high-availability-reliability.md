<properties
 pageTitle="Planificateur disponibilité et fiabilité"
 description="Planificateur disponibilité et fiabilité"
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
 ms.topic="article"
 ms.date="08/16/2016"
 ms.author="deli"/>


# <a name="scheduler-high-availability-and-reliability"></a>Planificateur disponibilité et fiabilité

## <a name="azure-scheduler-high-availability"></a>Disponibilité du planificateur Azure

Comme un service de plateforme Azure central, Azure Scheduler est hautement disponible et aux fonctionnalités déploiement du service geo redondants et réplication de travail geo régionales.

### <a name="geo-redundant-service-deployment"></a>Déploiement de service geo redondants

Planificateur Azure est disponible via l’interface utilisateur dans presque toutes les régions geo qui se trouve dans Azure aujourd'hui. La liste des régions Azure Scheduler est disponible dans est [répertorié ici](https://azure.microsoft.com/regions/#services). Si un centre de données dans une zone hébergé est rendu indisponible, les fonctionnalités de basculement du planificateur Azure sont telles que le service est disponible à partir d’un autre centre de données.

### <a name="geo-regional-job-replication"></a>Réplication de travail geo régionales

Non seulement le planificateur Azure est disponible pour les demandes de gestion, mais votre travail est également répliquée geo frontale. Lorsqu’il y a une panne dans une zone géographique, planificateur Azure bascule et garantit que la tâche est exécutée à partir d’un autre centre de données dans la région géographique apparié.

Par exemple, si vous avez créé une tâche dans Sud, planificateur Azure réplique automatiquement ce travail en Amérique du Nord centrale nous. Lorsqu’il y a une défaillance dans Sud, planificateur Azure garantit que la tâche est exécutée à partir de l’Amérique du Nord centrale contactez-nous. 

![][1]

Par conséquent, planificateur Azure garantit que vos données ne dépasse pas la région géographique plus large même en cas d’un échec Azure. Par conséquent, vous devez dupliquer pas votre travail simplement pour ajouter disponibilité – planificateur Azure fournit automatiquement les fonctionnalités de disponibilité pour vos tâches.

## <a name="azure-scheduler-reliability"></a>Fiabilité Scheduler Azure

Planificateur de Azure garantit sa propre haute disponibilité et une approche différente vous permet d’accéder aux projets créés par l’utilisateur. Par exemple, votre travail peut-être appeler un point de terminaison HTTP qui n’est pas disponible. Planificateur Azure tente néanmoins s’exécute correctement, votre travail en vous donnant des autres options de traiter défaillance. Planificateur Azure pour cela de deux façons :

### <a name="configurable-retry-policy-via-retrypolicy"></a>Stratégie de réessayer configurable via « retryPolicy »

Planificateur Azure permet de configurer une stratégie de réessayer. Par défaut, si une tâche échoue, planificateur essaie de la tâche à nouveau quatre fois de plus, toutes les 30 secondes. Vous pouvez configurer nouveau cette stratégie Réessayer pour être plus agressifs (par exemple, dix fois, à des intervalles de 30 secondes) ou plus souple (par exemple, deux fois, à des intervalles quotidiennes.)

Ainsi, lorsque cela peut aider, vous pouvez créer une tâche qui s’exécute une fois par semaine et appelle un point de terminaison HTTP. Si le point de terminaison HTTP est arrêté pendant quelques heures lorsque votre travail s’exécute, vous voudrez pas attendre une semaine plus pour la tâche à exécuter à nouveau dans la mesure où même la stratégie de nouvelles tentatives par défaut échouera. Dans ce cas, vous pouvez reconfigurer la stratégie de nouvelles tentatives standard pour recommencer toutes les trois heures (par exemple) au lieu de toutes les 30 secondes.

Pour savoir comment configurer une stratégie de nouvelles tentatives, reportez-vous à [retryPolicy](scheduler-concepts-terms.md#retrypolicy).

### <a name="alternate-endpoint-configurability-via-erroraction"></a>Autre point de terminaison simplifiées via « errorAction »

Si le point de terminaison cible pour votre projet Azure Scheduler reste inaccessible, planificateur Azure revient au point de terminaison de gestion des erreurs autre après avoir suivi sa stratégie de réessayer. Si un autre point de terminaison de gestion des erreurs est configuré, Azure Scheduler appelle. Avec un point de terminaison autre, vos propres tâches sont hautement disponibles cas de panne.

Par exemple, dans l’illustration ci-dessous, planificateur Azure suit sa stratégie Réessayer pour atteindre un service web Saint-Étienne. Une fois les tentatives échouent, elle vérifie s’il existe une autre. Elle puis continue et démarre effectuant des demandes à l’autre avec la même stratégie de réessayer.

![][2]

Notez que la stratégie de nouvelles tentatives même s’applique à l’action d’origine et l’action de substitution d’erreur. Il est également possible d’avoir type d’action de l’action autre erreur différer de type d’action de l’action principale. Par exemple, tandis que l’action principale peut-être appeler un point de terminaison HTTP, l’action d’erreur à la place peut-être une file d’attente stockage, file d’attente de service bus ou service bus rubrique action qui effectue la journalisation des erreurs.

Pour savoir comment configurer un autre point de terminaison, reportez-vous à [errorAction](scheduler-concepts-terms.md#action-and-erroraction).

## <a name="see-also"></a>Voir aussi

 [Quel est le planificateur ?](scheduler-intro.md)

 [Azure Scheduler concepts, terminologie et hiérarchie d’entités](scheduler-concepts-terms.md)

 [Commencer à utiliser le planificateur dans le portail Azure](scheduler-get-started-portal.md)

 [Les offres et facturation dans le planificateur d’Azure](scheduler-plans-billing.md)

 [Découvrez comment créer des planifications complexes et leur périodicité avancée avec le planificateur d’Azure](scheduler-advanced-complexity.md)

 [Référence de l’API REST Scheduler Azure](https://msdn.microsoft.com/library/mt629143)

 [Référence à des applets de commande PowerShell Scheduler Azure](scheduler-powershell-reference.md)

 [Limites de planificateur Azure, les valeurs par défaut et codes d’erreur](scheduler-limits-defaults-errors.md)

 [Authentification sortante Scheduler Azure](scheduler-outbound-authentication.md)


[1]: ./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image1.png

[2]: ./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image2.png
