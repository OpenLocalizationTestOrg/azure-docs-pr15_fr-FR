<properties
    pageTitle="Utiliser PowerShell pour créer des alertes pour les services Azure | Microsoft Azure"
    description="Utiliser PowerShell pour créer des alertes Azure, ce qui peuvent engendrer des notifications ou automation lorsque les conditions spécifiées sont remplies."
    authors="rboucher"
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
    ms.date="10/20/2016"
    ms.author="robb"/>

# <a name="use-powershell-to-create-alerts-for-azure-services"></a>Utiliser PowerShell pour créer des alertes pour les services d’Azure

> [AZURE.SELECTOR]
- [Portail](insights-alerts-portal.md)
- [PowerShell](insights-alerts-powershell.md)
- [INFRASTRUCTURE DU LANGAGE COMMUN](insights-alerts-command-line-interface.md)

## <a name="overview"></a>Vue d’ensemble

Cet article vous explique comment configurer des alertes Azure à l’aide de PowerShell.  

Vous pouvez recevoir une alerte basée sur les audits de surveillance pour ou des événements sur vos services Azure.

- **Valeurs métriques** - l’alert se déclenche lorsque la valeur d’une métrique spécifiée dépasse un seuil que vous attribuez dans les deux sens. Autrement dit, les deux déclenche quand la condition est remplie tout d’abord et puis par la suite que lorsque la condition est n’est plus remplie.    
- **Événements du journal activité** - une alerte peut déclencher sur *chaque* événement, ou uniquement lorsqu’un certain nombre d’événements se produire.

Vous pouvez configurer une alerte lorsqu’il déclenche, procédez comme suit :

- envoyer des notifications par courrier électronique à l’administrateur de service et coadministrateurs
- envoyer des messages électroniques aux messages électroniques supplémentaires que vous spécifiez.
- appeler un webhook
- lancer l’exécution d’une procédure opérationnelle Azure (uniquement à partir du portail Azure)

Vous pouvez configurer et obtenir des informations sur les règles d’alerte à l’aide

- [Portail Azure](insights-alerts-portal.md)
- [PowerShell](insights-alerts-powershell.md)
- [interface de ligne (commande)](insights-alerts-command-line-interface.md)
- [API REST moniteur Azure](https://msdn.microsoft.com/library/azure/dn931945.aspx)


Pour plus d’informations, vous pouvez toujours taper ```get-help``` et puis la commande PowerShell que vous voulez de l’aide.

## <a name="create-alert-rules-in-powershell"></a>Créer des règles d’alerte dans PowerShell

1. Se connecter à Azure.   

    ```PowerShell
    Login-AzureRmAccount

    ```

2. Obtenir une liste des abonnements dont vous disposez. Vérifiez que vous travaillez avec l’abonnement. Dans le cas contraire, attribuez-lui le bon en utilisant la sortie de `Get-AzureRmSubscription`.

    ```PowerShell
    Get-AzureRmSubscription
    Get-AzureRmContext
    Set-AzureRmContext -SubscriptionId <subscriptionid>
    ```

3.  Pour répertorier les règles existantes dans un groupe de ressources, utilisez la commande suivante :

    ```PowerShell
    Get-AzureRmAlertRule -ResourceGroup <myresourcegroup> -DetailedOutput
    ```

4. Pour créer une règle, vous devez tout d’abord à plusieurs éléments importants d’information. 
    - **Nº ressource** pour la ressource que vous souhaitez définir une alerte pour
    - Les **définitions métriques** disponibles pour cette ressource

    Une des façons d’obtenir l’ID de ressource consiste à utiliser le portail Azure. En supposant que la ressource est déjà créée, sélectionnez-le dans le portail. Dans la carte suivante, sélectionnez *Propriétés* sous la section *paramètres* . L’ID de ressource est un champ dans la carte suivante. Une autre façon consiste à utiliser l' [Explorateur de ressources Azure](https://resources.azure.com/).

    Est un id de ressource exemple pour une application web

    ```
    /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename
    ```

    Vous pouvez utiliser `Get-AzureRmMetricDefinition` pour afficher la liste de toutes les définitions métriques pour une ressource spécifique.

    ```PowerShell
    Get-AzureRmMetricDefinition -ResourceId <resource_id>
    ```

    L’exemple suivant génère une table avec la métrique nom et l’unité de cette métrique.

    ```PowerShell
    Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit

    ```
    Une liste complète des options disponibles pour Get-AzureRmMetricDefinition est disponible en exécutant Get-MetricDefinitions.


5. L’exemple suivant configure une alerte sur une ressource de site web. Déclencheurs alertes chaque fois qu’il reçoit manière cohérente à travers tout le trafic de 5 minutes, et à nouveau lorsqu’il ne reçoit aucun trafic pendant 5 minutes.

    ```PowerShell
    Add-AzureRmMetricAlertRule -Name myMetricRuleWithWebhookAndEmail -Location "East US" -ResourceGroup myresourcegroup -TargetResourceId /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename -MetricName "BytesReceived" -Operator GreaterThan -Threshold 2 -WindowSize 00:05:00 -TimeAggregationOperator Total -Description "alert on any website activity"

    ```

6. Pour créer webhook ou envoyer des messages électroniques lorsqu’une alerte se déclenche, commencez par créer la messagerie et/ou webhooks. Puis créer immédiatement la règle par la suite avec la - balise Actions et comme l’illustre l’exemple suivant. Vous ne pouvez pas associer webhook ou des messages électroniques avec déjà créé des règles via PowerShell.


    ```PowerShell
    $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
    $actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://www.contoso.com?token=mytoken

    Add-AzureRmMetricAlertRule -Name myMetricRuleWithWebhookAndEmail -Location "East US" -ResourceGroup myresourcegroup -TargetResourceId /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename -MetricName "BytesReceived" -Operator GreaterThan -Threshold 2 -WindowSize 00:05:00 -TimeAggregationOperator Total -Actions $actionEmail, $actionWebhook -Description "alert on any website activity"
    ```


7. Pour créer une alerte qui se déclenche une condition spécifique dans le journal d’activité, utilisez les commandes sous la forme suivante

    ```PowerShell
    $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
    $actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://www.contoso.com?token=mytoken

    Add-AzureRmLogAlertRule -Name myLogAlertRule -Location "East US" -ResourceGroup myresourcegroup -OperationName microsoft.web/sites/start/action -Status Succeeded -TargetResourceGroup resourcegroupbeingmonitored -Actions $actionEmail, $actionWebhook
    ```

    -OperationName correspond à un type d’événement dans le journal d’activité. Les exemples sont *Microsoft.Compute/virtualMachines/delete* et *microsoft.insights/diagnosticSettings/write*.

    Vous pouvez utiliser la commande PowerShell [Get-AzureRmProviderOperation](https://msdn.microsoft.com/library/mt603720.aspx) pour obtenir une liste des operationNames possibles. Vous pouvez également cliquer, vous pouvez utiliser le portail Azure pour interroger le journal d’activité et rechercher spécifique les opérations que vous voulez créer une alerte pour. Les opérations présentées dans l’affichage du graphique journal des noms conviviales. Examinez le JSON pour l’entrée et extraire la valeur NomOpération.   

8. Vérifiez que vos alertes ont été créés correctement en consultant les règles individuelles.

    ```PowerShell
    Get-AzureRmAlertRule -Name myMetricRuleWithWebhookAndEmail -ResourceGroup myresourcegroup -DetailedOutput

    Get-AzureRmAlertRule -Name myLogAlertRule -ResourceGroup myresourcegroup -DetailedOutput
    ```

9. Supprimer vos alertes. Ces commandes suppriment les règles créées précédemment dans cet article.

    ```PowerShell
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myrule
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myMetricRuleWithWebhookAndEmail
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myLogAlertRule
    ```

## <a name="next-steps"></a>Étapes suivantes

* [Obtenir un aperçu de surveillance Azure](monitoring-overview.md) , y compris les types d’informations que vous pouvez collecter et surveiller.
* Apprenez-en davantage sur [webhooks configuration dans les alertes](insights-webhooks-alerts.md).
* Découvrez les [Procédures opérationnelles Automation Azure](..\automation\automation-starting-a-runbook.md).
* Obtenez une [vue d’ensemble de collecte des journaux de diagnostic](monitoring-overview-of-diagnostic-logs.md) pour recueillir des métriques haute fréquence détaillées sur votre service.
* Obtenez une [vue d’ensemble de la collecte de métriques](insights-how-to-customize-monitoring.md) pour vous assurer que votre service n’est disponible et injoignable.
