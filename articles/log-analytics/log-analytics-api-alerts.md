<properties
   pageTitle="API REST alerte Analytique du journal"
   description="L’API REST alerte de journal Analytique vous permet de créer et gérer les alertes dans Suite de gestion des opérations (OMS).  Cet article fournit des détails de l’API et fournit plusieurs exemples permettant d’effectuer différentes opérations."
   services="log-analytics"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="log-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/18/2016"
   ms.author="bwren" />

# <a name="log-analytics-alert-rest-api"></a>Journal Analytique avertit API REST

L’API REST alerte de journal Analytique vous permet de créer et gérer les alertes dans Suite de gestion des opérations (OMS).  Cet article fournit des détails de l’API et fournit plusieurs exemples permettant d’effectuer différentes opérations.

L’API REST de journal Analytique recherche est RESTful et sont accessibles via le REST API de gestionnaire de ressources Azure. Dans ce document se trouvent les exemples dans lequel l’API est accessible à partir d’une ligne de commande PowerShell à l’aide [ARMClient](https://github.com/projectkudu/ARMClient), un outil de ligne de commande open source qui simplifie l’appel de l’API du Gestionnaire de ressources Azure. L’utilisation de ARMClient et PowerShell est une des nombreuses options pour accéder à l’API de recherche de journal Analytique. Grâce à ces outils, vous pouvez utiliser l’API du Gestionnaire de ressources Azure RESTful pour passer des appels vers les espaces de travail OMS et exécuter des commandes de recherche qu’ils contiennent. L’API génère des résultats de recherche pour vous au format JSON, ce qui vous permet d’utiliser les résultats de recherche de différentes façons par programme.

## <a name="prerequisites"></a>Conditions préalables
Pour l’instant, les alertes peuvent être créés uniquement avec une recherche enregistrée dans le journal Analytique.  Vous pouvez faire référence à l' [API REST de recherche journal](log-analytics-log-search-api.md) pour plus d’informations.

## <a name="schedules"></a>Calendriers
Une recherche enregistrée peut avoir une ou plusieurs planifications. La planification définit la fréquence à laquelle la recherche est exécutée et que l’intervalle de temps pendant laquelle les critères est identifié.
Les planifications avoir les propriétés dans le tableau suivant.

| Propriété  | Description |
|:--|:--|
| Intervalle | La fréquence à laquelle la recherche est exécutée. Mesurée en minutes. |
| QueryTimeSpan | L’intervalle de temps pendant laquelle les critères est évaluée. Doit être égale ou supérieure à intervalle. Mesurée en minutes. |
| Version | La version de l’API utilisée.  Pour l’instant, cela doit toujours être défini à 1. |

Par exemple, considérez une requête d’événement avec un intervalle de 15 minutes et Timespan de 30 minutes. Dans ce cas, la requête doit être exécutée toutes les 15 minutes, et une alerte serait déclenchée si les critères suite résoudre vers true sur un intervalle de 30 minutes.

### <a name="retrieving-schedules"></a>Récupération des planifications
Utilisez la méthode Get pour récupérer toutes les planifications pour une recherche enregistrée.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules?api-version=2015-03-20

Utilisez la méthode Get avec un code de grille pour récupérer un planning spécifique pour une recherche enregistrée.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20

Voici un exemple de réponse pour une planification.

    {
        "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/MyWorkspace/savedSearches/0f0f4853-17f8-4ed1-9a03-8e888b0d16ec/schedules/a17b53ef-bd70-4ca4-9ead-83b00f2024a8",
        "etag": "W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\"",
        "properties": {
        "Interval": 15,
        "QueryTimeSpan": 15
    }

### <a name="creating-a-schedule"></a>Création d’une planification
Utilisez la méthode place avec un ID de planification unique pour créer un échéancier.  Notez que deux calendriers ne peuvent pas porter le même ID même si elles sont associées à des différentes recherches enregistrées.  Lorsque vous créez une planification dans la console OMS, un GUID est créé pour la grille.

    $scheduleJson = "{'properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Active':'true' }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson

### <a name="editing-a-schedule"></a>Modification d’une planification
Utilisez la méthode place avec un ID de planification existant pour la recherche enregistrée même pour modifier ce calendrier.  Le corps de la requête doit inclure l’etag de l’échéancier.

    $scheduleJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\""','properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Active':'true' }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson


### <a name="deleting-schedules"></a>Suppression de planifications
Pour supprimer une planification, utilisez la méthode Delete avec un code de grille.

    armclient delete /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20


## <a name="actions"></a>Actions
Une planification peut avoir plusieurs actions. Une action ne peut définir un ou plusieurs processus pour effectuer telles que l’envoi d’un message ou démarrer une procédure opérationnelle, ou elle peut définir un seuil qui détermine quand les résultats d’une recherche correspondent à certains critères.  Certaines actions vous définirez les deux afin que les processus sont exécutés lorsque le seuil est atteint.

Toutes les actions ont les propriétés dans le tableau suivant.  Différents types d’alertes ont différentes propriétés supplémentaires qui sont décrites ci-dessous.

| Propriété | Description |
|:--|:--|
| Type | Type de l’action.  Actuellement les valeurs possibles sont alerte et Webhook. |
| Nom | Nom complet de l’alerte. |
| Version | La version de l’API utilisée.  Pour l’instant, cela doit toujours être défini à 1. |

### <a name="retrieving-actions"></a>Récupération d’actions
Utilisez la méthode Get pour récupérer toutes les actions d’une planification.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules/{Schedule ID}/actions?api-version=2015-03-20

Utilisez la méthode Get avec l’ID d’action pour récupérer une action particulière pour une planification.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/actions/{Action ID}?api-version=2015-03-20

### <a name="creating-or-editing-actions"></a>Création ou modification d’actions
Utilisez la méthode place avec un code d’action est unique à la planification pour créer une nouvelle action.  Lorsque vous créez une action dans la console OMS, un GUID est du code action.

Utilisez la méthode place avec un ID action existant pour la recherche enregistrée même pour modifier ce calendrier.  Le corps de la requête doit inclure l’etag de l’échéancier.

Le format des demandes de création d’une nouvelle action varie en fonction du type d’action pour que ces exemples sont fournies dans les sections ci-dessous.

### <a name="deleting-actions"></a>Suppression d’actions
Pour supprimer une action, utilisez la méthode de suppression de l’ID de l’action.

    armclient delete /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/Actions/{Action ID}?api-version=2015-03-20

### <a name="alert-actions"></a>Actions d’alerte
Une planification doit avoir qu’une seule action d’alerte.  Actions d’alerte ont une ou plusieurs des sections dans le tableau suivant.  Chacun est décrit plus en détail ci-dessous.

| Section | Description |
|:--|:--|
| Seuil | Critères pour lorsque l’action est exécutée. |  
| EmailNotification | Envoyer des messages à plusieurs destinataires. |
| Mise à jour | Démarrer une procédure opérationnelle dans Azure Automation pour tenter de résoudre le problème identifié. |

#### <a name="thresholds"></a>Seuils
Une action d’alerte doit avoir qu’un seul seuil.  Lorsque les résultats d’une recherche enregistrée correspondent au seuil dans une action associée que la recherche, puis tout autre processus dans cette action sont exécutées.  Une action peut contenir uniquement un seuil afin qu’il peut être utilisé avec des actions d’autres types qui ne contiennent pas de seuils.

Seuils ont les propriétés dans le tableau suivant.

| Propriété | Description |
|:--|:--|
| Opérateur | Opérateur de la comparaison de seuil. <br> gt = supérieur à <br> lt = inférieur à |
| Valeur | Valeur de seuil. |

Par exemple, considérez une requête d’événement avec un intervalle de 15 minutes, Timespan de 30 minutes et un seuil de plus de 10. Dans ce cas, la requête doit être exécutée toutes les 15 minutes, et une alerte serait déclenchée si elle a retourné 10 événements qui ont été créés sur une durée de 30 minutes.

Voici un exemple de réponse à une action avec uniquement un seuil.  

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My threshold action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "Version": 1
    }

Utilisez la méthode place avec un ID d’action unique pour créer une nouvelle action seuil pour une planification.  

    $thresholdJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson

Utilisez la méthode place avec un ID action existant pour modifier une action seuil pour une planification.  Le corps de la requête doit inclure l’etag de l’action.

    $thresholdJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson

#### <a name="email-notification"></a>Notification par courrier électronique
Notifications par courrier électronique envoient du courrier à un ou plusieurs destinataires.  Ils incluent les propriétés dans le tableau suivant.

| Propriété | Description |
|:--|:--|
| Destinataires | Liste d’adresses de messagerie. |
| Objet | L’objet du message. |
| Pièce jointe | Pièces jointes ne étant pas actuellement pris en charge, cela aura toujours une valeur de « Aucun ». |

Voici un exemple de réponse à une action de notification de messagerie avec un seuil.  

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My email action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "EmailNotification": {
            "Recipients": [
                "recipient1@contoso.com",
                "recipient2@contoso.com"
            ],
            "Subject": "This is the subject",
            "Attachment": "None"
        },
        "Version": 1
    }

Utilisez la méthode place avec un ID d’action unique pour créer une nouvelle action de messagerie pour une planification.  L’exemple suivant crée une notification par courrier électronique avec un seuil afin que le message est envoyé lorsque les résultats de la recherche enregistrée dépassent le seuil.

    $emailJson = "{'properties': { 'Name': 'MyEmailAction', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'EmailNotification': {'Recipients': ['recipient1@contoso.com', 'recipient2@contoso.com'], 'Subject':'This is the subject', 'Attachment':'None'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myemailaction?api-version=2015-03-20 $ emailJson

Utilisez la méthode place avec un ID action existant pour modifier une action de messagerie pour une planification.  Le corps de la requête doit inclure l’etag de l’action.

    $emailJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'MyEmailAction', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'EmailNotification': {'Recipients': ['recipient1@contoso.com', 'recipient2@contoso.com'], 'Subject':'This is the subject', 'Attachment':'None'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myemailaction?api-version=2015-03-20 $ emailJson

#### <a name="remediation-actions"></a>Actions de conversion
Correctives démarrer un runbook dans Automation Azure qui pour tenter de résoudre le problème identifié par l’alerte.  Vous devez créer un webhook pour la runbook utilisée dans une action de conversion, puis spécifiez l’URI dans la propriété WebhookUri.  Lorsque vous créez cette action à l’aide de la console OMS, un nouveau webhook est automatiquement créée pour le runbook.

Correctives contiennent les propriétés dans le tableau suivant.

| Propriété | Description |
|:--|:--|
| RunbookName | Nom de la procédure opérationnelle. Il doit correspondre à une runbook publié dans le compte d’automatisation configuré dans la Solution Automation dans votre espace de travail OMS. |
| WebhookUri | URI de la webhook.
| Expiration | La date d’expiration et l’heure de la webhook.  Si le webhook n’a pas une date d’expiration, cela peut être une date future valide. |

Voici un exemple de réponse pour une action de conversion avec un seuil.

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My remediation action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "Remediation": {
            "RunbookName": "My-Runbook",
            "WebhookUri": "https://s1events.azure-automation.net/webhooks?token=4jCibOjO3w4W2Cfg%2b2NkjLYdafnusaG6i8tnP8h%2fNNg%3d",
            "Expiry": "2018-02-25T18:27:20"
            },
        "Version": 1
    }

Utilisez la méthode place avec un ID d’action unique pour créer une nouvelle action de mise à jour pour une planification.  L’exemple suivant crée une mise à jour avec un seuil afin que le runbook est démarré lorsque les résultats de la recherche enregistrée dépassent le seuil.

    $remediateJson = "{'properties': { 'Type':'Alert', 'Name': 'My Remediation Action', 'Version':'1', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'Remediation': {'RunbookName': 'My-Runbook', 'WebhookUri':'https://s1events.azure-automation.net/webhooks?token=4jCibOjO3w4W2Cfg%2b2NkjLYdafnusaG6i8tnP8h%2fNNg%3d', 'Expiry':'2018-02-25T18:27:20Z'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myremediationaction?api-version=2015-03-20 $remediateJson

Utilisez la méthode place avec un ID action existant pour la modification d’une action de conversion d’une planification.  Le corps de la requête doit inclure l’etag de l’action.

    $remediateJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Type':'Alert', 'Name': 'My Remediation Action', 'Version':'1', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'Remediation': {'RunbookName': 'My-Runbook', 'WebhookUri':'https://s1events.azure-automation.net/webhooks?token=4jCibOjO3w4W2Cfg%2b2NkjLYdafnusaG6i8tnP8h%2fNNg%3d', 'Expiry':'2018-02-25T18:27:20Z'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myremediationaction?api-version=2015-03-20 $remediateJson

#### <a name="example"></a>Exemple
Voici un exemple complet pour créer un nouveau message d’alerte.  Cela crée un planning ainsi qu’une action contenant un seuil et la messagerie électronique.

    $subscriptionId = "3d56705e-5b26-5bcc-9368-dbc8d2fafbfc"
    $workspaceId    = "MyWorkspace"
    $searchId       = "51cf0bd9-5c74-6bcb-927e-d1e9080b934e"

    $scheduleJson = "{'properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Active':'true' }"
    armclient put /subscriptions/$subscriptionId/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/$workspaceId/savedSearches/$searchId/schedules/myschedule?api-version=2015-03-20 $scheduleJson

    $thresholdJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/$subscriptionId/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/$workspaceId/savedSearches/$searchId/schedules/myschedule/actions/mythreshold?api-version=2015-03-20 $thresholdJson

    $emailJson = "{'properties': { 'Name': 'MyEmailAction', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'EmailNotification': {'Recipients': ['recipient1@contoso.com', 'recipient2@contoso.com'], 'Subject':'This is the subject', 'Attachment':'None'} }"
    armclient put /subscriptions/$subscriptionId/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/$workspaceId/savedSearches/$searchId/schedules/myschedule/actions/myemailaction?api-version=2015-03-20 $emailJson

### <a name="webhook-actions"></a>Actions Webhook
Actions Webhook démarrer un processus en appelant une URL et vous pouvez également fournir une charge utile soient envoyées.  Ils sont similaires aux actions de conversion sauf qu’ils sont destinées aux webhooks qui peuvent appeler des processus différent de procédures opérationnelles Automation Azure.  Ils offrent également l’option supplémentaire de fournir une charge utile pour être remis au processus distant.

Actions Webhook n’ont pas un seuil mais à la place doivent être ajoutées à un calendrier qui comporte une action d’alerte avec un seuil.  Vous pouvez ajouter plusieurs actions Webhook qui seront toutes exécutées lorsque le seuil est atteint.

Actions Webhook incluent les propriétés dans le tableau suivant.

| Propriété | Description |
|:--|:--|
| WebhookUri | L’objet du message. |
| CustomPayload | Charge personnalisé à envoyer à le webhook.  Le format dépend de ce que le webhook attend. |

Voici un exemple de réponse pour webhook action et une action d’alerte associée avec un seuil.

    {
        "__metadata": {},
        "value": [
            {
                "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/bwren/savedSearches/2d1b30fb-7f48-4de5-9614-79ee244b52de/schedules/b80f5621-7217-4007-b32d-165d14377093/Actions/72884702-acf9-4653-bb67-f42436b342b4",
                "etag": "W/\"datetime'2016-02-26T20%3A25%3A00.6862124Z'\"",
                "properties": {
                    "Type": "Webhook",
                    "Name": "My Webhook Action",
                    "WebhookUri": "https://oaaswebhookdf.cloudapp.net/webhooks?token=VfkYTIlpk%2fc%2bJBP",
                    "CustomPayload": "{\"fielld1\":\"value1\",\"field2\":\"value2\"}",
                    "Version": 1
                }
            },
            {
                "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/bwren/savedSearches/2d1b30fb-7f48-4de5-9614-79ee244b52de/schedules/b80f5621-7217-4007-b32d-165d14377093/Actions/90a27cf8-71b7-4df2-b04f-54ed01f1e4b6",
                "etag": "W/\"datetime'2016-02-26T20%3A25%3A00.565204Z'\"",
                "properties": {
                    "Type": "Alert",
                    "Name": "Threshold for my webhook action",
                    "Threshold": {
                        "Operator": "gt",
                        "Value": 10
                    },
                    "Version": 1
                }
            }
        ]
    }

#### <a name="create-or-edit-a-webhook-action"></a>Créer ou modifier une action webhook
Utilisez la méthode place avec un ID d’action unique pour créer une nouvelle action webhook pour une planification.  L’exemple suivant crée une action Webhook et une action d’alerte avec un seuil afin que le webhook sera déclenchée lorsque les résultats de la recherche enregistrée dépassent le seuil.

    $thresholdAction = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdAction

    $webhookAction = "{'properties': {'Type': 'Webhook', 'Name': 'My Webhook", 'WebhookUri': 'https://oaaswebhookdf.cloudapp.net/webhooks?token=VrkYTKlhk%2fc%2bKBP', 'CustomPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}', 'Version': 1 }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mywebhookaction?api-version=2015-03-20 $webhookAction

Utilisez la méthode place avec un ID action existant pour modifier une action webhook pour une planification.  Le corps de la requête doit inclure l’etag de l’action.

    $webhookAction = "{'etag': 'W/\"datetime'2016-02-26T20%3A25%3A00.6862124Z'\"','properties': {'Type': 'Webhook', 'Name': 'My Webhook", 'WebhookUri': 'https://oaaswebhookdf.cloudapp.net/webhooks?token=VrkYTKlhk%2fc%2bKBP', 'CustomPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}', 'Version': 1 }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mywebhookaction?api-version=2015-03-20 $webhookAction

## <a name="next-steps"></a>Étapes suivantes

- Suivez l' [API REST pour effectuer des recherches de journal](log-analytics-log-search-api.md) journal Analytique.
