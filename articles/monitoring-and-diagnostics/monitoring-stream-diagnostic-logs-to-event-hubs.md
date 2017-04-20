<properties
    pageTitle="Diffuser en continu les journaux de Diagnostic Azure aux événements Hubs | Microsoft Azure"
    description="Découvrez comment diffuser des journaux de Diagnostic Azure aux événements Hubs."
    authors="johnkemnetz"
    manager="rboucher"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/08/2016"
    ms.author="johnkem"/>

# <a name="stream-azure-diagnostic-logs-to-event-hubs"></a>Les journaux de Diagnostic Azure aux événements Hubs de flux

**[Les journaux de Diagnostic Azure](monitoring-overview-of-diagnostic-logs.md)** peuvent être diffusées en temps quasi réel à n’importe quelle application à l’aide de l’option « Exporter à événement Hubs » intégrée dans le portail, ou en activant l’Id de règle Service Bus dans un facteur de Diagnostic via les applets de commande PowerShell Azure Azure infrastructure du langage commun.

## <a name="what-you-can-do-with-diagnostics-logs-and-event-hubs"></a>Ce que vous pouvez faire avec les journaux de Diagnostics et Hubs d’événement
Voici quelques méthodes que vous utilisez la fonctionnalité de diffusion en continu pour les journaux de Diagnostic :

- **Les fichiers journaux de flux à 3e partie journalisation et systèmes de télémétrie** – au fil du temps, Hubs événement de diffusion en continu devient le mécanisme de canal votre les journaux de Diagnostic dans SIEMs tiers et vous y connecter des solutions analytique.

- **Fonctionnement du service vue par diffusion en continu de données « chemin réactif » à PowerBI** – à l’aide d’événement Hubs, flux Analytique et PowerBI, vous pouvez transformer facilement vos données diagnostics près perspectives en temps réel sur vos services Azure. [Cet article documentation fournit une vue d’ensemble de configurer un événement Hubs, traiter les données avec Analytique de flux de données et utiliser PowerBI comme résultat](../stream-analytics/stream-analytics-power-bi-dashboard.md). Voici quelques conseils pour la configuration avec les journaux de Diagnostic :
    - Les Hubs événement pour une catégorie de journaux de Diagnostic est créé automatiquement lorsque vous activez l’option dans le portail ou activez via PowerShell, afin que vous souhaitez sélectionner les Hubs événement dans l’espace de noms Bus des services dont le nom commence par « perspectives- »
    - Voici un exemple de requête Analytique de flux de données que vous pouvez utiliser pour analyser simplement toutes les données de journal d’à une table PowerBI :

```
SELECT
records.ArrayValue.[Properties you want to track]
INTO
[OutputSourceName – the PowerBI source]
FROM
[InputSourceName] AS e
CROSS APPLY GetArrayElements(e.records) AS records
```

- **Créer une plate-forme journalisation et télémétrie personnalisé** – si ou est déjà une plateforme de télémétrie personnalisés sont simplement penser à un projet, hautement scalable publication abonnement nature des événements Hubs vous permet d’acquisition flexible journaux de diagnostic. [Guide de voir Dan Rosanova de l’utilisation de Hubs événement dans une plateforme de télémétrie l’échelle internationale ici](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

## <a name="enable-streaming-of-diagnostic-logs"></a>Activer la diffusion en continu de journaux de Diagnostic
Vous pouvez activer la diffusion de journaux de Diagnostic par programme, via le portail, ou à l’aide de l' [API REST de moniteur Azure](https://msdn.microsoft.com/library/azure/dn931943.aspx). Dans les deux cas, que vous avez choisi un Namespace Bus de Service et un événement Hubs est créé dans l’espace de noms pour chaque catégorie de journal que vous activez. Une **Catégorie de journal** de Diagnostic est un type de journal une ressource peut recueillir. Vous pouvez sélectionner les catégories de journal que vous voulez collecter les données pour une ressource particulière dans le portail Azure sous la carte Diagnostics.

![Journal des catégories dans le portail](./media/monitoring-stream-diagnostic-logs-to-event-hubs/log-categories.png)

> [AZURE.WARNING] L’activation et la diffusion en continu les journaux de diagnostic à partir de ressources (par exemple, machines virtuelles ou Service TISSU) cluster [nécessite une nouvelle série d’étapes](../event-hubs/event-hubs-streaming-azure-diags-data.md).

### <a name="via-powershell-cmdlets"></a>Via les applets de commande PowerShell
Pour activer la diffusion en continu via les [Applets de commande PowerShell Azure](insights-powershell-samples.md), vous pouvez utiliser la `Set-AzureRmDiagnosticSetting` applet de commande avec ces paramètres :

```
Set-AzureRmDiagnosticSetting -ResourceId [your resource Id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
```

ID de la règle de Service Bus est une chaîne avec ce format : `{service bus resource ID}/authorizationrules/{key name}`, par exemple, `/subscriptions/{subscription ID}/resourceGroups/Default-ServiceBus-WestUS/providers/Microsoft.ServiceBus/namespaces/{service bus namespace}/authorizationrules/RootManageSharedAccessKey`.


### <a name="via-azure-cli"></a>Via Azure infrastructure du langage commun
Pour activer la diffusion en continu via l' [Infrastructure du langage commun Azure](insights-cli-samples.md), vous pouvez utiliser la `insights diagnostic set` commande comme suit :

```
azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
```

Utilisez la même mise en forme de l’ID de règle Service Bus comme expliqué pour la PowerShell Cmdlet.

###<a name="via-azure-portal"></a>Via le portail Azure
Pour activer la diffusion en continu via le portail Azure, accédez aux paramètres de diagnostics d’une ressource, puis sélectionnez « Exportation de l’événement concentrateur ».

![Exporter vers Hubs événement dans le portail](./media/monitoring-stream-diagnostic-logs-to-event-hubs/portal-export.png)

Pour configurer, sélectionnez un Namespace Bus de Service existante. L’espace de noms sélectionné sera où les Hubs événement est créé (s’il s’agit de votre première heure diffusion en continu les journaux de diagnostic) ou diffusé sur (si il existe déjà ressources diffusés en continu cette catégorie journal pour cet espace de noms), et la stratégie définit les autorisations qui comporte le mécanisme de diffusion en continu. Aujourd'hui, la diffusion à un événement Hubs nécessite des autorisations de gérer, veuillez lire et envoyer. Vous pouvez créer ou modifier les stratégies d’accès Service Bus Namespace partagé dans le portail classique sous l’onglet « Configurer » pour votre Namespace Bus de Service. Pour mettre à jour un de ces paramètres des diagnostics, le client doit avoir l’autorisation ListKey sur la règle Service Bus d’autorisation.

##<a name="how-do-i-consume-the-log-data-from-event-hubs"></a>Comment utiliser les données du journal d’événement Hubs ?
Voici des exemples de données de sortie à partir de l’événement Hubs :

```
{
    "records": [
        {
            "time": "2016-07-15T18:00:22.6235064Z",
            "workflowId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330013509921957/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Error",
            "operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T17:58:55.048482Z",
                "endTime": "2016-07-15T18:00:22.4109204Z",
                "status": "Failed",
                "code": "BadGateway",
                "resource": {
                    "subscriptionId": "df602c9c-7aa0-407d-a6fb-eb20c8bd1192",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330013509921957",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "29a9862f-969b-4c70-90c4-dfbdc814e413",
                    "clientTrackingId": "08587330013509921958"
                }
            }
        },
        {
            "time": "2016-07-15T18:01:15.7532989Z",
            "workflowId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330012106702630/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Information",
            "operationName": "Microsoft.Logic/workflows/workflowActionStarted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T18:01:15.5828115Z",
                "status": "Running",
                "resource": {
                    "subscriptionId": "df602c9c-7aa0-407d-a6fb-eb20c8bd1192",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330012106702630",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "042fb72c-7bd4-439e-89eb-3cf4409d429e",
                    "clientTrackingId": "08587330012106702632"
                }
            }
        }
    ]
}
```

| Nom de l’élément | Description                                            |
|--------------|--------------------------------------------------------|
|enregistrements       | Tableau de tous les événements de journal dans cette charge utile.            |
|heure          | Heure à laquelle l’événement s’est produite.                      |
|catégorie      | Catégorie de journal pour cet événement.                           |
|resourceId    | Nº ressource de la ressource à l’origine de cet événement. |
|NomOpération | Nom de l’opération.                                 |
|niveau         | Facultatif. Indique le niveau d’événement journal.               |
|propriétés    | Propriétés de l’événement.                               |


Vous pouvez afficher une liste de tous les fournisseurs de ressources qui prennent en charge la diffusion en continu à concentrateur événement [ici](monitoring-overview-of-diagnostic-logs.md).

##<a name="next-steps"></a>Étapes suivantes
- [En savoir plus sur les journaux de Diagnostic Azure](monitoring-overview-of-diagnostic-logs.md)
- [Prise en main avec Hubs d’événement](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
