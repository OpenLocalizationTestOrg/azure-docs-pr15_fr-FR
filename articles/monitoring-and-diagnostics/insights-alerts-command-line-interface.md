<properties
    pageTitle="Utiliser l’Interface de ligne de commande de disponibilité sur plusieurs plateformes (CLI) pour créer des alertes pour les services Azure | Microsoft Azure"
    description="L’interface de ligne de commande permet de créer des alertes Azure, qui peuvent engendrer des notifications ou automation lorsque les conditions spécifiées sont remplies."
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
    ms.date="10/24/2016"
    ms.author="robb"/>

# <a name="use-the-cross-platform-command-line-interface-cli-to-create-alerts-for-azure-services"></a>Utiliser l’Interface de ligne de commande de disponibilité sur plusieurs plateformes (CLI) pour créer des alertes pour les services d’Azure

> [AZURE.SELECTOR]
- [Portail](insights-alerts-portal.md)
- [PowerShell](insights-alerts-powershell.md)
- [INFRASTRUCTURE DU LANGAGE COMMUN](insights-alerts-command-line-interface.md)

## <a name="overview"></a>Vue d’ensemble

Cet article vous explique comment configurer des alertes Azure à l’aide de l’Interface de ligne de commande (CLI).

>[AZURE.NOTE] Moniteur Azure est le nouveau nom de ce qui était appelé « Perspectives Azure » jusqu'à 25 septembre 2016. Toutefois, les espaces de noms et donc les commandes ci-dessous contient toujours les « perspectives ».

Vous pouvez recevoir une alerte basée sur les audits de surveillance pour ou des événements sur vos services Azure.

- **Valeurs métriques** - l’alert se déclenche lorsque la valeur d’une métrique spécifiée dépasse un seuil que vous attribuez dans les deux sens. Autrement dit, les deux déclenche quand la condition est remplie tout d’abord et puis par la suite que lorsque la condition est n’est plus remplie.    
- **Événements du journal activité** - une alerte peut déclencher sur *chaque* événement, ou uniquement lorsqu’un certain nombre d’événements se produire.

Vous pouvez configurer une alerte lorsqu’il déclenche, procédez comme suit :

- envoyer des notifications par courrier électronique à l’administrateur de service et coadministrateurs
- envoyer des messages électroniques aux messages électroniques supplémentaires que vous spécifiez.
- appeler un webhook
- lancer l’exécution d’une procédure opérationnelle Azure (uniquement à partir du portail Azure pour le moment)

Vous pouvez configurer et obtenir des informations sur les règles d’alerte à l’aide

- [Portail Azure](insights-alerts-portal.md)
- [PowerShell](insights-alerts-powershell.md)
- [interface de ligne (commande)](insights-alerts-command-line-interface.md)
- [API REST moniteur Azure](https://msdn.microsoft.com/library/azure/dn931945.aspx)


Vous pouvez toujours obtenir de l’aide pour les commandes en tapant une commande et en insérant - aide à la fin. Par exemple :

    ```console
    azure insights alerts -help
    azure insights alerts actions email create -help
    ```

## <a name="create-alert-rules-using-the-cli"></a>Créer des règles d’alerte à l’aide de l’infrastructure du langage commun

1. Effectuer la configuration requise et connectez-vous à Azure. Consultez des [exemples de Azure infrastructure du langage commun moniteur](insights-cli-samples.md). En bref, installez l’infrastructure du langage commun et exécuter ces commandes. Ils vous aider à vous connecté, afficher abonnement vous utilisez et vous préparez pour exécuter des commandes Azure moniteur.


    ```console
    azure login
    azure account show
    azure config mode arm

    ```

2.  Pour répertorier les règles existantes dans un groupe de ressources, utilisez formulaire suivant **azure analyse des alertes de règle de liste** *[options] &lt;resourceGroup&gt; *

    ```console
    azure insights alerts rule list myresourcegroupname

    ```
3. Pour créer une règle, vous devez tout d’abord à plusieurs éléments importants d’information.
    - **Nº ressource** pour la ressource que vous souhaitez définir une alerte pour
    - Les **définitions métriques** disponibles pour cette ressource

    Une des façons d’obtenir l’ID de ressource consiste à utiliser le portail Azure. En supposant que la ressource est déjà créée, sélectionnez-le dans le portail. Dans la carte suivante, sélectionnez *Propriétés* sous la section *paramètres* . L' *ID de la ressource* est un champ dans la carte suivante. Une autre façon consiste à utiliser l' [Explorateur de ressources Azure](https://resources.azure.com/).

    Est un id de ressource exemple pour une application web

    ```console
    /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename
    ```

    Pour obtenir une liste des statistiques disponibles et des unités pour ces indicateurs de l’exemple précédent de la ressource, utilisez la commande CLI suivante :  

    ```console
    azure insights metrics list /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename PT1M
    ```

    _PT1M_ est la précision de la mesure disponible (1-minutes). L’utilisation de différentes granularités vous donne différentes options métriques.


4. Pour créer une règle d’alerte métrique, utilisez une commande sous la forme suivante :

    **métrique de règle d’alertes insights Azure définir** *[options] &lt;ruleName&gt; &lt;emplacement&gt; &lt;resourceGroup&gt; &lt;windowSize&gt; &lt;opérateur&gt; &lt;seuil&gt; &lt;targetResourceId&gt; &lt;metricName&gt; &lt;timeAggregationOperator&gt; *

    L’exemple suivant configure une alerte sur une ressource de site web. Déclencheurs alertes chaque fois qu’il reçoit manière cohérente à travers tout le trafic de 5 minutes, et à nouveau lorsqu’il ne reçoit aucun trafic pendant 5 minutes.

    ```console
    azure insights alerts rule metric set myrule eastus myreasourcegroup PT5M GreaterThan 2 /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename BytesReceived Total

    ```

5. Pour créer webhook ou envoyer des messages électroniques lorsqu’une alerte se déclenche, commencez par créer la messagerie et/ou webhooks. Puis créez la règle immédiatement par la suite. Vous ne pouvez pas associer webhook ou des messages électroniques avec déjà créé des règles à l’aide de l’infrastructure du langage commun.

    ```console
    azure insights alerts actions email create --customEmails myemail@contoso.com

    azure insights alerts actions webhook create https://www.contoso.com

    azure insights alerts rule metric set myrulewithwebhookandemail eastus myreasourcegroup PT5M GreaterThan 2 /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename BytesReceived Total
    ```


6. Pour créer une alerte qui se déclenche une condition spécifique dans le journal d’activité, utilisez le formulaire :

    **règle d’alertes Insights connecter ensemble** *[options] &lt;ruleName&gt; &lt;emplacement&gt; &lt;resourceGroup&gt; &lt;NomOpération&gt; *

    Par exemple

    ```console
    azure insights alerts rule log set myActivityLogRule eastus myresourceGroupName Microsoft.Storage/storageAccounts/listKeys/action
    ```

    La NomOpération correspond à un type d’événement pour une entrée dans le journal d’activité. Les exemples sont *Microsoft.Compute/virtualMachines/delete* et *microsoft.insights/diagnosticSettings/write*.

    Vous pouvez utiliser la commande PowerShell [Get-AzureRmProviderOperation](https://msdn.microsoft.com/library/mt603720.aspx) pour obtenir une liste des operationNames possibles. Vous pouvez également cliquer, vous pouvez utiliser le portail Azure pour interroger le journal d’activité et rechercher spécifique les opérations que vous voulez créer une alerte pour. Les opérations présentées dans l’affichage du graphique journal des noms conviviales. Examinez le JSON pour l’entrée et extraire la valeur NomOpération.   

7. Vous pouvez vérifier que vos alertes ont été créés correctement en consultant une règle individuelle.

    ```console
    azure insights alerts rule list myresourcegroup --ruleName myrule
    ```

8. Pour supprimer les règles, utilisez une commande sous la forme :

    **analyse des alertes règle supprimer** [options] &lt;resourceGroup&gt; &lt;ruleName&gt;

    Ces commandes suppriment les règles créées précédemment dans cet article.

    ```console
    azure insights alerts rule delete myresourcegroup myrule
    azure insights alerts rule delete myresourcegroup myrulewithwebhookandemail
    azure insights alerts rule delete myresourcegroup myActivityLogRule
    ```



## <a name="next-steps"></a>Étapes suivantes

* [Obtenir un aperçu de surveillance Azure](monitoring-overview.md) , y compris les types d’informations que vous pouvez collecter et surveiller.
* Apprenez-en davantage sur [webhooks configuration dans les alertes](insights-webhooks-alerts.md).
* Découvrez les [Procédures opérationnelles Automation Azure](..\automation\automation-starting-a-runbook.md).
* Obtenez une [vue d’ensemble de collecte des journaux de diagnostic](monitoring-overview-of-diagnostic-logs.md) pour recueillir des métriques haute fréquence détaillées sur votre service.
* Obtenez une [vue d’ensemble de la collecte de métriques](insights-how-to-customize-monitoring.md) pour vous assurer que votre service n’est disponible et injoignable.
