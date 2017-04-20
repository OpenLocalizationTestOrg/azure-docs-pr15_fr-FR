<properties 
   pageTitle="Alertes dans le journal Analytique | Microsoft Azure"
   description="Alertes dans le journal Analytique identifier des informations importantes dans votre référentiel OMS et peuvent le fait de vous signaler des problèmes ou appellent des actions pour essayer de corriger les.  Cet article décrit comment créer une règle d’alerte et détails les différentes actions qu’ils peuvent prendre."
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
   ms.date="08/22/2016"
   ms.author="bwren" />

# <a name="alerts-in-log-analytics"></a>Alertes dans le journal Analytique

Alertes dans le journal Analytique identifient des informations importantes dans votre référentiel OMS.  Règles d’alerte automatiquement effectuent des recherches dans le journal selon un planning et créer un enregistrement d’alerte si les résultats correspondent aux critères particuliers.  La règle peut exécuter puis automatiquement une ou plusieurs des actions pour vous avertir de l’alerte fait ou appeler un autre processus.   

![Ouvrez une session alertes Analytique](media/log-analytics-alerts/overview.png)

## <a name="creating-an-alert-rule"></a>Création d’une règle d’alerte
Pour créer une règle d’alerte, vous commencez par créer une recherche de journal pour les enregistrements qui doit appeler l’alerte.  Le bouton **alerte** sera puis disponible afin de pouvoir créer et configurer la règle d’alerte.

1.  Dans la page Vue d’ensemble OMS, cliquez sur **Recherche de journal**.
2.  Créez une nouvelle requête de recherche de journal ou sélectionner un journal enregistré de recherche. 
3.  Cliquez sur l' **alerte** dans la partie supérieure de la page pour ouvrir l’écran **Ajouter une règle d’alerte** .
4. Consultez les tableaux ci-dessous pour plus d’informations sur les options pour configurer l’alerte.
5. Lorsque vous fournissez la fenêtre de temps pour la règle d’alerte, le nombre d’enregistrements existants qui remplissent les critères de recherche pour cette fenêtre s’affichera.  Cela peut vous aider à déterminer la fréquence à laquelle vous donne le nombre de résultats que vous attendez.
4.  Cliquez sur **Enregistrer** pour terminer la règle d’alerte.  Elle commence à s’exécuter immédiatement.


![Ajouter une règle d’alerte](media/log-analytics-alerts/add-alert-rule.png)

| Propriété | Description |
|:--|:--|
| **Informations sur les alertes** | |
| Nom |  Nom unique pour identifier la règle d’alerte. |
| Gravité | Gravité de l’alerte créée par cette règle. |
| Requête de recherche | Sélectionnez **utiliser la recherche en cours** pour utiliser la requête actuelle ou sélectionnez une recherche enregistrée existante dans la liste.  La syntaxe de la requête est fourni dans la zone de texte dans laquelle vous pouvez modifier si nécessaire.  |
| Fenêtre délai | Spécifie l’intervalle de temps pour la requête.  La requête renvoie uniquement les enregistrements qui ont été créés dans cette plage de l’heure actuelle.  Cela peut être une valeur comprise entre 5 minutes et 24 heures.  Il doit être supérieure ou égale à la fréquence de l’alerte.  <br><br> Par exemple, si la fenêtre de temps est définie sur 60 minutes, et la requête est exécutée en 1:15 PM, uniquement les enregistrements créés entre 12:15 PM et 1:15 PM apparaîtront. |
| **Planification** |     
| Seuil | Critères créer une alerte.  Si le nombre d’enregistrements renvoyés par la requête correspond à ce critère, une alerte est créée. |
| Fréquence de l’alerte | Spécifie la fréquence d’exécution de la requête.  Peut être une valeur comprise entre 5 minutes et 24 heures.  Doit être inférieure ou égale à la fenêtre de temps à. |
| Supprimer les alertes | Lorsque vous activez la suppression de la règle d’alerte, actions de la règle sont désactivées pour une durée définie après la création d’une nouvelle alerte.  La règle est toujours en cours d’exécution et crée des enregistrements d’alerte si le critère est satisfait.  Il s’agit pour autoriser que du temps pour résoudre le problème sans exécuter des actions en double. |
| **Actions** | |
| Notification par courrier électronique | Spécifiez **Oui** si vous souhaitez un message électronique à envoyer au déclenchement de l’alerte. |
| Objet    | Objet dans le message électronique.  Vous ne pouvez pas modifier le corps du message. |
| Destinataires | Adresses de tous les destinataires de messages électroniques.  Si vous spécifiez plusieurs adresses, puis séparez les adresses par un point-virgule ( ;). |
| Webhook | Spécifiez **Oui** si vous souhaitez appeler une webhook au déclenchement de l’alerte. |
| URL Webhook | L’URL de la webhook. |
| Inclure la charge utile JSON personnalisé | Sélectionnez cette option si vous souhaitez remplacer la charge utile par défaut par une charge personnalisée. |
| Entrez votre charge utile JSON personnalisé | La charge utile personnalisée pour le webhook.  Voir la section précédente pour plus d’informations. |
| Runbook | Spécifiez **Oui** si vous voulez commencer une procédure opérationnelle Azure Automation au déclenchement de l’alerte. |
| Sélectionnez un runbook | Sélectionnez le runbook pour démarrer à partir de la procédures opérationnelles dans le compte d’automatisation configuré dans votre solution d’automatisation. |
| Exécuter sur | Sélectionnez **Azure** pour exécuter la procédure opérationnelle dans le cloud Azure.  Sélectionnez **Collaborateur hybride** pour lancer la runbook dans un [Environnement hybride Runbook collaborateur](..\automation\automation-hybrid-runbook-worker.md) dans votre environnement local. |


## <a name="manage-alert-rules"></a>Gérer les règles d’alerte
Vous pouvez obtenir une liste de toutes les règles d’alerte dans le menu **alertes** dans le journal Analytique **paramètres**.  

![Gérer les alertes](./media/log-analytics-alerts/configure.png)

1. Dans la console OMS sélectionnez la vignette **paramètres** .
2. Sélectionnez **alertes**.

Vous pouvez effectuer plusieurs actions à partir de cet affichage.

- Désactiver une règle en sélectionnant **désactiver** en regard de celle-ci.
- Modifier une règle d’alerte en cliquant sur l’icône de crayon en regard de celle-ci.
- Supprimer une règle d’alerte en cliquant sur l’icône **X** en regard de celle-ci. 


## <a name="setting-time-windows"></a>Définition de périodes 

### <a name="event-alerts"></a>Alertes d’événements

Événements incluent des sources de données telles que les journaux d’événements Windows, journal système, et journaux personnalisés.  Vous souhaiterez peut-être créer une alerte lorsqu’un événement d’erreur particulier est créé, ou lorsque plusieurs erreurs événements sont créés dans une fenêtre de temps particulier.

Pour alerter sur un seul événement, définissez le nombre de résultats sur supérieur à 0 et la fréquence et la fenêtre de temps à 5 minutes.  Qui exécute la requête toutes les 5 minutes et vérifier l’occurrence d’un événement unique qui a été créé depuis la dernière exécution de la requête.  Une fréquence plus longue peut retarder le délai entre l’événement collectées et l’alerte en cours de création.

Certaines applications peuvent enregistrer une erreur occasionnelle qui ne doit pas nécessairement déclencher une alerte.  Par exemple, l’application peut réessayer le processus qui a créé l’événement d’erreur et puis réussir la prochaine fois.  Dans ce cas, vous voudrez pas à créer l’alerte à moins d’avoir plusieurs événements sont créés dans une fenêtre de temps particulier.  

Dans certains cas, vous souhaiterez créer une alerte en l’absence d’un événement.  Par exemple, un processus peut enregistrer des événements standard pour indiquer qu’il fonctionne correctement.  Si elle n’enregistre pas un de ces événements dans une fenêtre de temps particulier, une alerte doit être créée.  Dans ce cas, vous devez définir le seuil au *moins 1*.

### <a name="performance-alerts"></a>Alertes de performances

[Les données de performance](log-analytics-data-sources-performance-counters.md) sont stockées sous forme d’enregistrements dans le référentiel OMS semblable aux événements.  La valeur de chaque enregistrement est la moyenne mesurée au cours des 30 dernières minutes.  Si vous voulez d’alerte lorsqu’un compteur de performance dépasse un seuil particulier, ce seuil doit être inclus dans la requête.

Par exemple, si vous souhaitez avertir lorsque le processeur s’exécute plus de 90 % pendant 30 minutes, vous utiliserez une requête comme *Type = performance ObjectName = processeur CounterName = « % temps processeur » contre-valeurs > 90* et le seuil de la règle d’alerte à *une valeur supérieure à 0*.  

 Dans la mesure où [fiches de performance](log-analytics-data-sources-performance-counters.md) sont regroupées toutes les 30 minutes quelle que soit la fréquence que vous ayez chaque compteur, un laps de temps inférieur à 30 minutes ne peut retourner aucun enregistrement.  Définition de la fenêtre de temps à 30 minutes assuré que vous obteniez un enregistrement unique pour chaque source connecté qui représente la moyenne sur cette période.

## <a name="alert-actions"></a>Actions d’alerte

En plus de la création d’un enregistrement alert, vous pouvez configurer la règle d’alerte pour exécuter automatiquement une ou plusieurs actions.  Actions pouvant fait vous avertir de l’alerte ou certains le sous-processus qui pour tenter de résoudre le problème qui a été détecté.  Les sections suivantes décrivent les actions qui sont actuellement disponibles.

### <a name="email-actions"></a>Actions de messagerie
Actions de messagerie envoyer un message électronique avec les détails de l’alerte à un ou plusieurs destinataires.  Vous pouvez spécifier l’objet du message, mais son contenu est un format standard construit par journal Analytique.  Il inclut des informations de synthèse telles que le nom de l’alerte en plus de détails de dix enregistrements renvoyés par la recherche du journal.  Il inclut également un lien vers une recherche des journaux dans journal Analytique qui renvoie l’ensemble des enregistrements à partir de cette requête.   L’expéditeur du message est *équipe Suite de gestion des opérations Microsoft &lt; noreply@oms.microsoft.com *. 


### <a name="webhook-actions"></a>Actions Webhook

Actions de Webhook permettent de vous permet d’appeler un processus externe via une demande HTTP POST unique.  Le service actuellement appelé doit prendre en charge webhooks et déterminer la manière dont il utilise une charge utile qu’elle reçoit.  Vous pouvez également appeler une API REST qui ne prennent en charge spécifiquement webhooks tant que la demande est dans un format que comprend l’API.  Exemples d’utilisation d’un webhook en réponse à une alerte utilisent un service tel que [marge](http://slack.com) pour envoyer un message avec les détails de l’alerte ou en créant un incident dans un service tel que [PagerDuty](http://pagerduty.com/).  

Différentes étapes de création d’une règle d’alerte avec un webhook pour appeler un exemple de service n’est disponible en [Webhooks dans les alertes de journal Analytique](log-analytics-alerts-webhooks.md).

Webhooks inclure une URL et une charge utile mis en forme en JSON correspond aux données envoyées au service externe.  Par défaut, la charge utile inclut les valeurs du tableau ci-dessous.  Vous pouvez choisir de remplacer cette charge active avec un personnalisé de votre choix.  Dans ce cas vous pouvez utiliser les variables dans le tableau pour chacun des paramètres pour inclure leur valeur de votre charge utile personnalisé.


| Paramètre | Variable | Description |
|:--|:--|:--|
| AlertRuleName | #alertrulename | Nom de la règle d’alerte. |
| AlertThresholdOperator | #thresholdoperator | Opérateur de seuil pour la règle d’alerte.  *Supérieur* ou *inférieur à*. |
| AlertThresholdValue | #thresholdvalue | Valeur de seuil pour la règle d’alerte. |
| LinkToSearchResults | #linktosearchresults | Créer un lien vers la recherche de journal de journal Analytique qui renvoie les enregistrements de la requête qui a créé l’alerte. |
| ResultCount  | #searchresultcount | Nombre d’enregistrements dans les résultats de recherche. |
| SearchIntervalEndtimeUtc  | #searchintervalendtimeutc | Heure de fin de la requête au format UTC. |
| SearchIntervalInSeconds | #searchinterval | Fenêtre de temps pour la règle d’alerte. |
| SearchIntervalStartTimeUtc  | #searchintervalstarttimeutc | Heure de début de la requête au format UTC. |
| SearchQuery | #SearchQuery | Requête de recherche de journal utilisée par la règle d’alerte. |
| SearchResults | Consultez la section ci-dessous | Enregistrements renvoyés par la requête au format JSON.  Limité à la première 5 000 enregistrements. |
| WorkspaceID | #workspaceid | ID de votre espace de travail OMS. |


Par exemple, vous pouvez spécifier la charge personnalisée suivante incluant un seul paramètre appelé *texte*.  Le service appelle cette webhook serait digne ce paramètre.

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }

Charge utile de cet exemple serait résoudre vers les éléments suivants lors de l’envoi pour la webhook.

    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }

Pour inclure les résultats de recherche dans une charge personnalisée, ajoutez la ligne suivante comme une propriété de niveau supérieur dans la charge utile json.  

    "IncludeSearchResults":true

Par exemple, pour créer une charge personnalisée qui inclut uniquement le nom de l’alerte et les résultats de recherche, vous pouvez utiliser les éléments suivants. 

    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }


Vous pouvez passez en revue un exemple complet de création d’une règle d’alerte avec un webhook pour démarrer un service externe au [journal Analytique webhook exemple d’alerte](log-analytics-alerts-webhooks.md).

### <a name="runbook-actions"></a>Actions Runbook

Actions Runbook démarrer un runbook dans Azure Automation.  Pour pouvoir utiliser ce type d’action, vous devez disposer de la [solution d’automatisation](log-analytics-add-solutions.md) installé et configuré dans votre espace de travail OMS.  Si vous n’avez pas installé lorsque vous créez une nouvelle règle d’alerte, un lien vers son installation s’affiche.  Vous pouvez sélectionner dans les procédures opérationnelles dans le compte d’automatisation que vous avez configuré dans la solution automatisation.

Actions Runbook démarrer la runbook à l’aide d’un [webhook](../automation/automation-webhooks.md).  Lorsque vous créez la règle d’alerte, il crée automatiquement une nouvelle webhook pour la runbook avec le nom **OMS alerte Remediation** suivi d’un GUID.  

Vous ne pouvez pas remplir directement tous les paramètres de la procédure opérationnelle, mais le [paramètre $WebhookData](../automation/automation-webhooks.md) doit inclure les détails de l’alerte, y compris les résultats de la recherche du journal qui l’a créé.  La procédure opérationnelle devrez définir **$WebhookData** comme un paramètre pour qu’il puisse accéder aux propriétés de l’alerte.  Les données d’alerte sont disponibles au format json dans une propriété unique appelée **SearchResults** dans la propriété **RequestBody** de **$WebhookData**.  Cela aura avec les propriétés du tableau ci-dessous.


| Nœud | Description |
|:--|:--|
| ID         | Chemin d’accès et GUID de la recherche. |
| __metadata | Informations sur l’alerte, y compris le nombre d’enregistrements et l’état des résultats de recherche. |
|  valeur     |  Entrée distincte pour chaque enregistrement dans les résultats de recherche.  Les détails de l’entrée donneront les propriétés et les valeurs de l’enregistrement.   |

Par exemple, la procédure suivante opérationnelle serait extraire les enregistrements renvoyés par la recherche du journal et affecter des propriétés différentes en fonction du type de chaque enregistrement.  Notez que la runbook démarre en convertissant **RequestBody** à partir de json afin qu’il peut être utilisé en tant qu’objet dans PowerShell.

    param ( 
        [object]$WebhookData
    )

    $RequestBody = ConvertFrom-JSON -InputObject $WebhookData.RequestBody
    $Records     = $RequestBody.SearchResults.value
    
    foreach ($Record in $Records)
    {
        $Computer = $Record.Computer
        
        if ($Record.Type -eq 'Event')
        {
            $EventNo    = $Record.EventID
            $EventLevel = $Record.EventLevelName
            $EventData  = $Record.EventData
        }
        
        if ($Record.Type -eq 'Perf')
        {
            $Object    = $Record.ObjectName
            $Counter   = $Record.CounterName
            $Instance  = $Record.InstanceName
            $Value     = $Record.CounterValue
        }
    }


## <a name="alert-records"></a>Enregistrements d’alerte

Alertes enregistrements créés par les règles d’alerte dans un journal Analytique ont pour un **Type** **d’alerte** et un **SourceSystem** de **OMS**.  Ils ont les propriétés dans le tableau suivant.

| Propriété | Description |
|:--|:--|
| Type          | *Alerte* |
| SourceSystem  | *OMS* |
| AlertSeverity | Niveau de gravité de l’alerte. |
| Nom_alerte     | Nom de l’alerte. |
| Requête         | Texte de la requête qui a été exécutée.  |
| QueryExecutionEndTime   | Fin de la plage horaire pour la requête. |
| QueryExecutionStartTime | Début de la plage horaire pour la requête.  |
| TimeGenerated | Date et heure de que création de l’alerte. |

Il existe d’autres types d’enregistrements d’alerte créés par la [solution de gestion de l’alerte](log-analytics-solution-alert-management.md) et [exporte Power BI](log-analytics-powerbi.md).  Ces tous ont un **Type** **d’alerte** , mais sont distinguent par leur **SourceSystem**.




## <a name="next-steps"></a>Étapes suivantes

- Installez la [solution de gestion des alertes](log-analytics-solution-alert-management.md) pour analyser les alertes créées dans journal Analytique ainsi que des alertes collectées à partir de System Center Operations Manager (SCOM).
- En savoir plus sur les [recherches de journaux](log-analytics-log-searches.md) qui peut générer des alertes.
- Effectuez une procédure pas à pas pour la [configuration d’un webook](log-analytics-alerts-webhooks.md) avec une règle d’alerte.  
- Apprenez à écrire des [procédures opérationnelles dans Azure Automation](https://azure.microsoft.com/documentation/services/automation) pour corriger les problèmes identifiés par les alertes.