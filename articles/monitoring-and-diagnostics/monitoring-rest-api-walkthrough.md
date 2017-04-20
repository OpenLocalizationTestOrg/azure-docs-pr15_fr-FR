<properties
    pageTitle="Azure procédure pas à pas l’API REST de surveillance | Microsoft Azure"
    description="Comment authentifier les requêtes à et utiliser l’API REST de surveillance Azure."
    authors="mcollier, rboucher"
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
    ms.date="09/27/2016"
    ms.author="mcollier"/>

# <a name="azure-monitoring-rest-api-walkthrough"></a>Azure surveillance procédure pas à pas l’API REST
Cet article vous explique comment effectuer une authentification pour que votre code puisse utiliser la [Référence de l’API de Microsoft Azure moniteur reste](https://msdn.microsoft.com/library/azure/dn931943.aspx).         

L’API moniteur Azure rend possible récupérer par programme les définitions métrique par défaut disponibles (le type de métrique tels que le temps processeur, requêtes, etc.), précision et valeurs métriques. Une fois récupéré, les données peuvent être enregistrées dans un magasin de données distinct comme base de données SQL Azure, DocumentDB ou Lake de données Azure. À partir de là, une analyse plus approfondie peut être effectuée selon vos besoins.

Outre l’utilisation des différents points de données métriques, comme le montre cet article, l’API moniteur permet de répertorier les règles d’alerte, afficher les journaux d’activité et bien plus encore. Pour une liste complète des opérations disponibles, voir la [Référence de l’API de Microsoft Azure moniteur reste](https://msdn.microsoft.com/library/azure/dn931943.aspx).

## <a name="authenticating-azure-monitor-requests"></a>Authentification Azure analysez les requêtes

La première étape consiste à authentifier la demande.

Toutes les tâches exécutées l’API moniteur Azure utilisent le modèle d’authentification Azure le Gestionnaire de ressources. Par conséquent, toutes les demandes doivent être authentifiés avec Azure Active Directory (AD Azure). L’une des approches s’authentifier à l’application cliente consiste à créer une annonce Azure principal du service et de récupérer le jeton d’authentification (JWT). Exemple de script suivant illustre la création d’un service d’Azure AD principal via PowerShell. Pour une vue d’ensemble plus détaillée, consultez la documentation relative [à l’aide de PowerShell Azure pour créer un service principal pour accéder aux ressources](../resource-group-authenticate-service-principal.md#authenticate-service-principal-with-password—powershell). Il est également possible de [créer un principe service via le portail Azure](../resource-group-create-service-principal-portal.md).

```PowerShell
$subscriptionId = "{azure-subscription-id}"
$resourceGroupName = "{resource-group-name}"
$location = "centralus"

# Authenticate to a specific Azure subscription.
Login-AzureRmAccount -SubscriptionId $subscriptionId

# Password for the service principal
$pwd = "{service-principal-password}"

# Create a new Azure AD application
$azureAdApplication = New-AzureRmADApplication `
                        -DisplayName "My Azure Monitor" `
                        -HomePage "https://localhost/azure-monitor" `
                        -IdentifierUris "https://localhost/azure-monitor" `
                        -Password $pwd

# Create a new service principal associated with the designated application
New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

# Assign Reader role to the newly created service principal
New-AzureRmRoleAssignment -RoleDefinitionName Reader `
                          -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

```

Pour interroger l’API moniteur Azure, l’application cliente doit utiliser l’entité de service créé précédemment s’authentifier. L’exemple de script PowerShell suivant présente une approche, à l’aide de la [Bibliothèque de l’authentification Active Directory](../active-directory/active-directory-authentication-libraries.md) (terme ADAL) pour commencer à l’authentification JWT jeton. Le jeton JWT est passé dans le cadre d’un paramètre d’autorisation HTTP dans les requêtes à l’API REST de moniteur Azure.

```PowerShell
$azureAdApplication = Get-AzureRmADApplication -IdentifierUri "https://localhost/azure-monitor"

$subscription = Get-AzureRmSubscription -SubscriptionId $subscriptionId

$clientId = $azureAdApplication.ApplicationId.Guid
$tenantId = $subscription.TenantId
$authUrl = "https://login.windows.net/${tenantId}"

$AuthContext = [Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext]$authUrl
$cred = New-Object -TypeName Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential -ArgumentList ($clientId, $pwd)
 
$result = $AuthContext.AcquireToken("https://management.core.windows.net/", $cred)

# Build an array of HTTP header values 
$authHeader = @{
'Content-Type'='application/json'
'Accept'='application/json'
'Authorization'=$result.CreateAuthorizationHeader()
}
```

Une fois que l’étape de configuration de l’authentification est terminée, requêtes peuvent ensuite être exécutées l’API REST de moniteur Azure. Il existe deux requêtes utiles :

1. Répertorier les définitions métriques pour une ressource

2. Récupérer les valeurs métriques


## <a name="retrieve-metric-definitions"></a>Extraire des définitions de mesures
>[AZURE.NOTE] Pour récupérer les définitions métriques à l’aide de l’API REST de moniteur Azure, utilisez « 2016-03-01 » en tant que la version de l’API.

```PowerShell
$apiVersion = "2016-03-01"
$request = "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/${resourceProviderNamespace}/${resourceType}/${resourceName}/providers/microsoft.insights/metricDefinitions?api-version=${apiVersion}"

Invoke-RestMethod -Uri $request `
                  -Headers $authHeader `
                  -Method Get `
                  -Verbose
```
Pour une application de logique Azure, les définitions métriques devrait ressembler à la capture d’écran suivante :

![ALT « View JSON de réponse définition métrique. »](./media/monitoring-rest-api-walkthrough/available_metric_definitions_logic_app_json_response_clean.png)

Pour plus d’informations, consultez la documentation de [répertorier les définitions métriques pour une ressource dans l’API REST Azure moniteur](https://msdn.microsoft.com/library/azure/mt743621.aspx) .

## <a name="retrieve-metric-values"></a>Récupérer des valeurs métriques
Une fois que les définitions métriques disponibles sont connues, il est alors possible récupérer les valeurs métriques liées. Utiliser nom « valeur » de la métrique (pas « localizedValue ») pour toutes les demandes de filtrage (par exemple, extraire les points de données métriques « CpuTime » et « Demande »). Si aucun filtre n’est spécifiés, la mesure par défaut est renvoyée.

>[AZURE.NOTE] Pour récupérer des valeurs métriques à l’aide de l’API REST de moniteur Azure, utilisez « 2016-06-01 » en tant que la version de l’API.

**Méthode**: GET

**URI demandé**:_{nom de groupe ressource}_/providers/_{ressource-fournisseur-espace de noms}_https://management.azure.com/subscriptions/_} - id de l’abonnement {_/resourceGroups//_{type de ressource}_//providers/microsoft.insights/metrics?$filter=_{nom de ressource}__{filter}_& version de l’api =_{apiVersion}_

Par exemple, pour récupérer les points de données métriques RunsSucceeded pour la plage horaire donnée et grains temps d’une heure, la demande se présentent comme suit :

```PowerShell
$apiVersion = "2016-06-01"
$filter = "(name.value eq 'RunsSucceeded') and aggregationType eq 'Total' and startTime eq 2016-09-23 and endTime eq 2016-09-24 and timeGrain eq duration'PT1H'"
$request = "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/${resourceProviderNamespace}/${resourceType}/${resourceName}/providers/microsoft.insights/metrics?`$filter=${filter}&api-version=${apiVersion}"
(Invoke-RestMethod -Uri $request `
                   -Headers $authHeader `
                   -Method Get `
                   -Verbose).Value | ConvertTo-Json
```

Le résultat apparaît comme dans l’exemple suivant capture d’écran :

![ALT « Réponse JSON affichant la valeur métrique de temps de réponse moyen »](./media/monitoring-rest-api-walkthrough/available_metrics_logic_app_json_response.png)

Pour extraire plusieurs points de données ou d’agrégation, ajoutez les noms de définition métriques et les types d’agrégation pour le filtre, comme illustré dans l’exemple suivant :

```PowerShell
$apiVersion = "2016-06-01"
$filter = "(name.value eq 'ActionsCompleted' or name.value eq 'RunsSucceeded') and (aggregationType eq 'Total' or aggregationType eq 'Average') and startTime eq 2016-09-23T13:30:00Z and endTime eq 2016-09-23T14:30:00Z and timeGrain eq duration'PT1M'"
$request = "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/${resourceProviderNamespace}/${resourceType}/${resourceName}/providers/microsoft.insights/metrics?`$filter=${filter}&api-version=${apiVersion}"
(Invoke-RestMethod -Uri $request `
                   -Headers $authHeader `
                   -Method Get `
                   -Verbose).Value | ConvertTo-Json
```

### <a name="use-armclient"></a>Utiliser ARMClient
Alternative à l’aide de PowerShell (comme illustré ci-dessus), consiste à utiliser [ARMClient](https://github.com/projectkudu/ARMClient) sur votre ordinateur Windows. ARMClient gère automatiquement l’authentification Azure Active Directory (et qui en résulte jeton JWT). Les étapes suivantes décrivent l’utilisation de ARMClient pour extraire des données métriques :

1. Installez [Chocolatey](https://chocolatey.org/) et [ARMClient](https://github.com/projectkudu/ARMClient).

2. Dans une fenêtre de terminal, tapez _armclient.exe login_. Cela vous invite à se connecter à Azure.

3. Type de _armclient GET [your_resource_id]/providers/microsoft.insights/metricdefinitions?api-version=2016-03-01_

4. Type de _armclient GET [your_resource_id]/providers/microsoft.insights/metrics?api-version=2016-06-01_


![ALT « ARMClient à l’aide pour l’utiliser avec l’API REST de surveillance Azure »](./media/monitoring-rest-api-walkthrough/armclient_metricdefinitions.png)


## <a name="retrieve-the-resource-id"></a>Récupérer l’ID de ressource
À l’aide de l’API REST vraiment de vous aider à comprendre les définitions métriques disponibles, précision et les valeurs liées. Ces informations sont utiles lorsque vous utilisez la [Bibliothèque de gestion des Azure](https://msdn.microsoft.com/library/azure/mt417623.aspx).

Pour le code précédent, l’ID de ressource à utiliser est le chemin d’accès complet à la ressource Azure souhaitée. Par exemple, pour interroger par rapport à une application Web Azure, l’ID de ressource serait le suivant :

*/Subscriptions/{Subscription-ID}/resourceGroups/{Resource-Group-Name}/Providers/Microsoft.Web/sites/{Site-Name}/*

La liste suivante contient quelques exemples de formats d’ID de ressources pour diverses ressources Azure :

* **Concentrateur IoT** - /subscriptions/_} - id de l’abonnement {_/resourceGroups/_{nom de groupe ressource}_/providers/Microsoft.Devices/IotHubs/_{nom de concentrateur iot}_

* **Élastique SQL Pool** : /subscriptions/_} - id de l’abonnement {_/resourceGroups/_{nom de groupe ressource}_/providers/Microsoft.Sql/servers/_{pool-db}_/elasticpools/_{nom de pool sql}_

* **Base de données SQL (v12)** : /subscriptions/_} - id de l’abonnement {_/resourceGroups/_{nom de groupe ressource}_/providers/Microsoft.Sql/servers/_{nom serveur}_/databases/_{nom de la base de données}_

* **Service Bus** - /subscriptions/_} - id de l’abonnement {_/resourceGroups/_{nom de groupe ressource}_/providers/Microsoft.ServiceBus/_{noms}_/_{servicebus-name}_

* **Machine virtuelle échelle jeux** - /subscriptions/_} - id de l’abonnement {_/resourceGroups/_{nom de groupe ressource}_/providers/Microsoft.Compute/virtualMachineScaleSets/_{nom de la machine virtuelle}_

* **Machines virtuelles** - /subscriptions/_} - id de l’abonnement {_/resourceGroups/_{nom de groupe ressource}_/providers/Microsoft.Compute/virtualMachines/_{nom de la machine virtuelle}_

* **Événement Hubs** - /subscriptions/_} - id de l’abonnement {_/resourceGroups/_{nom de groupe ressource}_/providers/Microsoft.EventHub/namespaces/_{eventhub-noms}_


Il existe autres approches sur l’extraction de l’ID de ressource, notamment à l’aide de l’Explorateur de ressources Azure, l’affichage de la ressource souhaitée dans le portail Azure et via PowerShell ou de l’infrastructure du langage commun Azure.

### <a name="azure-resource-explorer"></a>Explorateur de ressources Azure
Pour rechercher l’ID de ressource pour une ressource de votre choix, une approche utile consiste à utiliser l’outil [Azure ressource Explorer](https://resources.azure.com) . Accédez à la ressource souhaitée, puis regardez l’ID présenté, comme dans l’écran suivant :

![ALT « Ressource Azure l’Explorateur »](./media/monitoring-rest-api-walkthrough/azure_resource_explorer.png)

### <a name="azure-portal"></a>Portail Azure
L’ID de ressource peut également être obtenu à partir du portail Azure. Pour ce faire, accédez à la ressource souhaitée, puis sélectionnez Propriétés. L’ID de ressource est affiché dans la carte de propriétés, comme illustré dans la capture d’écran suivante :

![ALT « Nº ressource affiché dans la carte de propriétés dans le portail Azure »](./media/monitoring-rest-api-walkthrough/resourceid_azure_portal.png)

### <a name="azure-powershell"></a>PowerShell Azure
L’ID de ressource peut être récupéré à l’aide des applets de commande PowerShell Azure également. Par exemple, pour obtenir l’ID de ressource pour une application Web Azure, exécutez l’applet de commande Get-AzureRmWebApp, comme dans l’écran suivant :

![ALT « Nº ressource obtenu via PowerShell »](./media\monitoring-rest-api-walkthrough\resourceid_powershell.png)

### <a name="azure-cli"></a>Azure infrastructure du langage commun
Pour récupérer l’ID de ressource à l’aide de l’infrastructure du langage commun Azure, exécutez la commande 'webapp azure afficher', spécifiant la '--json' option, comme le montre la capture d’écran suivante :

![ALT « Nº ressource obtenu via PowerShell »](./media\monitoring-rest-api-walkthrough\resourceid_azurecli.png)

## <a name="retrieve-activity-log-data"></a>Récupérer des données de journal d’activité
En plus de travailler avec les valeurs liées et les définitions métriques, il est également possible de récupérer intéressantes supplémentaires liées aux ressources Azure. Par exemple, il est possible d’interroger les données de [journal d’activité](https://msdn.microsoft.com/library/azure/dn931934.aspx) . L’exemple suivant illustre l’utilisation de l’API Azure moniteur reste aux données du journal d’activité requête dans une plage de dates spécifique pour un abonnement Azure :

```PowerShell
$apiVersion = "2014-04-01"
$filter = "eventTimestamp ge '2016-09-23' and eventTimestamp le '2016-09-24'and eventChannels eq 'Admin, Operation'"
$request = "https://management.azure.com/subscriptions/${subscriptionId}/providers/microsoft.insights/eventtypes/management/values?api-version=${apiVersion}&`$filter=${filter}"
(Invoke-RestMethod -Uri $request `
                   -Headers $authHeader `
                   -Method Get `
                   -Verbose).Value | ConvertTo-Json
```

## <a name="next-steps"></a>Étapes suivantes
* Passez en revue [Présentation du contrôle](monitoring-overview.md).
* Afficher les [mesures pris en charge avec Azure moniteur](monitoring-supported-metrics.md).
* Passez en revue la [Référence de l’API REST Microsoft Azure moniteur](https://msdn.microsoft.com/library/azure/dn931943.aspx).
* Passez en revue la [bibliothèque de gestion des Azure](https://msdn.microsoft.com/library/azure/mt417623.aspx).
