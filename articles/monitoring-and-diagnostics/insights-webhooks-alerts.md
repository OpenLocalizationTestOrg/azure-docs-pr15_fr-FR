<properties
    pageTitle="Configurer webhooks sur alertes métriques Azure | Microsoft Azure"
    description="Rediriger des alertes Azure à d’autres systèmes non Azure."
    authors="kamathashwin"
    manager="carolz"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/15/2016"
    ms.author="ashwink"/>

# <a name="configure-a-webhook-on-an-azure-metric-alert"></a>Configurer un webhook sur une alerte métrique Azure

Webhooks vous autorise à acheminer une notification d’alerte Azure à d’autres systèmes pour les actions postérieures à la transformation ou personnalisées. Vous pouvez utiliser un webhook sur une alerte pour acheminer aux services d’envoyer des SMS, enregistrer les bogues, avertir une équipe via les services de messagerie/conversation ou effectuer diverses autres actions. Cet article décrit comment définir une webhook sur une alerte métrique Azure et ce à quoi ressemble la charge utile pour la publication HTTP à un webhook. Pour plus d’informations sur la configuration et le schéma pour un journal d’activité Azure d’alerte (une alerte sur événements), [consultez cette page à la place](./insights-auditlog-to-webhook-email.md).

Alertes Azure HTTP POST le contenu de l’alerte dans JSON mettre en forme, schéma défini ci-dessous, pour une webhook URI que vous fournissez lors de la création de l’alerte. Cet URI doit être un point de terminaison HTTP ou HTTPS valide. Azure publie une entrée par demande lorsqu’une alerte est activée.

## <a name="configuring-webhooks-via-the-portal"></a>Configuration webhooks via le portail

Vous pouvez ajouter ou mettre à jour la webhook URI dans l’écran alertes créer/mettre à jour dans le [portail](https://portal.azure.com/).

![Ajouter une règle d’alerte](./media/insights-webhooks-alerts/Alertwebhook.png)

Vous pouvez également configurer une alerte pour publier des billets dans un webhook URI en utilisant les [Applets de commande PowerShell Azure](./insights-powershell-samples.md#create-alert-rules), [Infrastructure du langage commun disponibilité sur plusieurs plateformes](./insights-cli-samples.md#work-with-alerts)ou [L’API REST Azure moniteur](https://msdn.microsoft.com/library/azure/dn933805.aspx).

## <a name="authenticating-the-webhook"></a>La webhook d’authentification

La webhook peut s’authentifier à l’aide d’une des méthodes suivantes :

1. **Autorisation basée sur un jeton** - la webhook URI est enregistré avec un ID de jeton, par exemple. `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`
2.  **D’autorisation de base** - la webhook URI est enregistré avec un nom d’utilisateur et mot de passe, par exemple. `https://userid:password@mysamplealert/webcallback?someparamater=somevalue&foo=bar`

## <a name="payload-schema"></a>Schéma de charge

L’opération POST contient la charge utile JSON suivant et le schéma pour toutes les alertes basées sur métrique.

```JSON
{
"status": "Activated",
"context": {
            "timestamp": "2015-08-14T22:26:41.9975398Z",
            "id": "/subscriptions/s1/resourceGroups/useast/providers/microsoft.insights/alertrules/ruleName1",
            "name": "ruleName1",
            "description": "some description",
            "conditionType": "Metric",
            "condition": {
                        "metricName": "Requests",
                        "metricUnit": "Count",
                        "metricValue": "10",
                        "threshold": "10",
                        "windowSize": "15",
                        "timeAggregation": "Average",
                        "operator": "GreaterThanOrEqual"
                },
            "subscriptionId": "s1",
            "resourceGroupName": "useast",                                
            "resourceName": "mysite1",
            "resourceType": "microsoft.foo/sites",
            "resourceId": "/subscriptions/s1/resourceGroups/useast/providers/microsoft.foo/sites/mysite1",
            "resourceRegion": "centralus",
            "portalLink": "https://portal.azure.com/#resource/subscriptions/s1/resourceGroups/useast/providers/microsoft.foo/sites/mysite1"
},
"properties": {
              "key1": "value1",
              "key2": "value2"
              }
}
```


| Champ | Obligatoire | Ensemble de valeurs fixe | Notes |
| :-------------| :-------------   | :-------------   | :-------------   |
|état|Y|« Activé », « Résolu »|État de l’alerte en fonction des conditions que vous avez défini.|
|contexte| Y | | Le contexte de l’alerte.|
|horodatage| Y | | L’heure à laquelle elle a été déclenchée.|
|ID | Y | | Chaque règle d’alerte possède un id unique.|
|nom               |Y                  |                   | Le nom de l’alerte.|
|Description        |Y                  |                           |Description de l’alerte.|
|conditionType      |Y                  |« Métrique », « Événement »          |Deux types d’alertes sont prises en charge. Un selon une condition métrique, l’autre basée sur un événement dans le journal d’activité. Utilisez cette valeur pour vérifier si l’alerte est basé sur métrique ou un événement.|
|condition          |Y                  |                           | Les champs spécifiques pour vérifier les basé sur le conditionType.|
|metricName         |pour les alertes métriques  |                           |Le nom de la métrique qui définit ce que la règle surveille.|
|metricUnit         |pour les alertes métriques  |« Octets », « BytesPerSecond », « Compter », « CountPerSecond », « Pourcentage », « Secondes »|     L’unité autorisée dans la mesure. [Valeurs autorisées sont répertoriés ici](https://msdn.microsoft.com/library/microsoft.azure.insights.models.unit.aspx).|
|metricValue        |pour les alertes métriques  |                           |Valeur réelle de la métrique qui a provoqué l’alerte.|
|seuil          |pour les alertes métriques  |                           |La valeur de seuil auquel l’alerte est activé.|
|windowSize         |pour les alertes métriques  |                           |La période de temps qui est utilisée pour contrôler l’activité alerte basée sur le seuil. Doit être comprise entre 5 minutes et 1 jour. Format de durée ISO8601.|
|timeAggregation    |pour les alertes métriques  |« Moyenne », « Nom », « Maximum », « Minimum », « Aucun », « Total » | Comment les données qui sont collectées doivent être combinées au fil du temps. La valeur par défaut est moyenne. [Valeurs autorisées sont répertoriés ici](https://msdn.microsoft.com/library/microsoft.azure.insights.models.aggregationtype.aspx).|
|opérateur           |pour les alertes métriques  |                           |L’opérateur utilisé pour comparer les données actuelles métriques pour le seuil défini.|
|subscriptionId     |Y                  |                           |ID d’abonnement Azure.|
|resourceGroupName  |Y                  |                           |Nom du groupe de ressources pour la ressource concernée.|
|resourceName       |Y                  |                           |Nom de la ressource de la ressource concernée.|
|type de ressource       |Y                  |                           |Type de la ressource concernée.|
|resourceId         |Y                  |                           |Nº ressource de la ressource concernée.|
|resourceRegion     |Y                  |                           |Région ou l’emplacement de la ressource concernée.|
|portalLink         |Y                  |                           |Lien direct vers la page de résumé ressource portail.|
|propriétés         |N                  |Facultatif                   |Définir des `<Key, Value>` paires (c'est-à-dire `Dictionary<String, String>`) qui inclut des informations concernant l’événement. Le champ propriétés est facultatif. Dans un IU ou logique basée sur l’application workflow personnalisé, les utilisateurs peuvent entrer clé/valeurs qui peuvent être passées via la charge utile. L’autre façon pour passer les propriétés personnalisées à la webhook est via l’uri webhook lui-même (en tant que paramètres de requête)|


>[AZURE.NOTE] Le champ de propriétés peut uniquement être défini à l’aide de l' [API REST de moniteur Azure](https://msdn.microsoft.com/library/azure/dn933805.aspx).

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les alertes Azure et webhooks dans vidéo [Intégrer Azure alertes avec PagerDuty](http://go.microsoft.com/fwlink/?LinkId=627080)
- [Exécuter des scripts Automation Azure (procédures opérationnelles) sur les alertes Azure](http://go.microsoft.com/fwlink/?LinkId=627081)
- [Utiliser une logique de l’application pour envoyer un SMS via Twilio à partir d’une alerte Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)
- [Application logique permet d’envoyer un message marge à partir d’une alerte Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)
- [Utiliser une logique de l’application pour envoyer un message à une file d’attente Azure à partir d’une alerte Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)
