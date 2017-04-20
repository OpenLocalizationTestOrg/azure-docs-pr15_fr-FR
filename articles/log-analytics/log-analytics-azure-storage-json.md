<properties
    pageTitle="Analyser les journaux de diagnostic Azure à l’aide de journal Analytique | Microsoft Azure"
    description="Journal Analytique pouvez lire les journaux de services d’Azure écriture les journaux de diagnostic Azure BLOB storage au format JSON."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="banders"/>


# <a name="analyze-azure-diagnostic-logs-using-log-analytics"></a>Analyser les journaux de diagnostic Azure à l’aide de journal Analytique

Journal Analytique pouvez collecter les journaux pour les services suivants Azure écrivent [les journaux de diagnostic Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) au stockage blob au format JSON :

+ Automatisation (Preview)
+ Archivage sécurisé clé (Preview)
+ Application passerelle (Preview)
+ Groupe de sécurité réseau (Preview)

Les sections suivantes vous expliquant en détail à l’aide de PowerShell pour :

+ Configurer le journal Analytique pour collecter les journaux d’espace de stockage pour chaque ressource  
+ Activer la solution Analytique journal pour le service Azure

Avant de journal Analytique recueille des données de ces ressources, les diagnostics de Windows Azure doit être activé. Utiliser la `Set-AzureRmDiagnosticSetting` applet de commande pour activer la journalisation.

Consultez les articles suivants pour plus d’informations sur la façon d’activer la journalisation des diagnostics :

+ [Archivage sécurisé clé](../key-vault/key-vault-logging.md)
+ [Passerelle d’application](../application-gateway/application-gateway-diagnostics.md)
+ [Groupe de sécurité réseau](../virtual-network/virtual-network-nsg-manage-log.md)

Cette documentation inclut également des informations sur :

+ Résolution des problèmes de collecte de données
+ Arrêter la collecte de données

## <a name="configure-log-analytics-to-collect-azure-diagnostic-logs"></a>Configurer le journal Analytique pour collecter les journaux de diagnostic Azure

Collecter les journaux de ces services et l’activation de la solution à visualiser les journaux sont effectuée à l’aide des scripts PowerShell.

L’exemple ci-dessous active la journalisation de toutes les ressources pris en charge

```
# update to be the storage account that logs will be written to. Storage account must be in the same region as the resource to monitor
# format is similar to "/subscriptions/ec11ca60-ab12-345e-678d-0ea07bbae25c/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount"
$storageAccountId = ""

$supportedResourceTypes = ("Microsoft.Automation/AutomationAccounts", "Microsoft.KeyVault/Vaults", "Microsoft.Network/NetworkSecurityGroups", "Microsoft.Network/ApplicationGateways")

# update location to match your storage account location
$resources = Get-AzureRmResource | where { $_.ResourceType -in $supportedResourceTypes -and $_.Location -eq "westus" }

foreach ($resource in $resources) {
    Set-AzureRmDiagnosticSetting -ResourceId $resource.ResourceId -StorageAccountId $storageAccountId -Enabled $true -RetentionEnabled $true -RetentionInDays 1
}
```


Pour certaines ressources, il est possible d’effectuer la configuration précédente à partir du portail Azure en suivant les étapes décrites dans [les journaux de diagnostic vue d’ensemble d’Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#how-to-enable-collection-of-diagnostic-logs).

## <a name="configure-log-analytics-to-collect-azure-diagnostic-logs"></a>Configurer le journal Analytique pour collecter les journaux de diagnostic Azure

Nous vous fournissons un module de script PowerShell qui permet d’exporter deux applets de commande pour vous aider à configurer journal Analytique :

1. `Add-AzureDiagnosticsToLogAnalyticsUI`vous demande d’entrée et est en mesure de définir des configurations simples
2. `Add-AzureDiagnosticsToLogAnalytics`vous permet d’accéder les ressources à surveiller comme entrée, puis configure Analytique de journal  

### <a name="pre-requisites"></a>Conditions préalables

1. Azure PowerShell avec la version 1.0.8 ou version ultérieure des applets de commande Insights opérationnel.
  - [Comment installer et configurer PowerShell Azure](../powershell-install-configure.md)
  - Vérifiez votre version d’applets de commande :`Import-Module AzureRM.OperationalInsights -MinimumVersion 1.0.8 `
2. Journalisation des diagnostics est configurée pour la ressource Azure que vous voulez analyser. Utiliser `Set-AzureRmDiagnosticSetting` ou reportez-vous à [Utiliser journal Analytique pour recueillir des données à partir des comptes de stockage Azure](log-analytics-azure-storage.md) pour savoir comment activer les diagnostics.
3. Un espace de travail [Analytique de journal](https://portal.azure.com/#create/Microsoft.LogAnalyticsOMS)  
4. Le module AzureDiagnosticsAndLogAnalytics PowerShell
  - Téléchargez le module [AzureDiagnosticsAndLogAnalytics](https://www.powershellgallery.com/packages/AzureDiagnosticsAndLogAnalytics/) à partir de la galerie de PowerShell

### <a name="option-1-run-the-interactive-configuration-scripts"></a>Option 1 : Exécuter les Scripts de Configuration interactifs

Ouvrez PowerShell et exécutez :

```
# Connect to Azure
Login-AzureRmAccount
# If you have diagnostics logs being written to classic storage you will also need to run
Add-AzureAccount

# Import the module
Install-Module -Name AzureDiagnosticsAndLogAnalytics

# Run the UI configuration script
Add-AzureDiagnosticsToLogAnalyticsUI

```

Vous êtes indiqué une liste de sélections disponibles et donné une invite pour effectuer votre sélection.
Vous êtes invité à effectuer des sélections pour chacune des opérations suivantes :

+ Types de ressources (Azure Services) pour collecter les journaux à partir de
+ Instances de ressources pour collecter les journaux à partir de
+ Espace de travail journal Analytique pour collecter les données

Après avoir exécuté ce script, vous devriez voir enregistrements dans le journal Analytique environ 30 minutes une fois les nouvelles données Diagnostics écrites au stockage. Si les enregistrements ne sont pas disponibles une fois que cette fois reportez-vous à la section dépannage ci-dessous.

### <a name="option-2-build-a-list-of-resources-and-pass-them-to-the-configuration-cmdlet"></a>Option 2 : Créer une liste de ressources et les passer à l’applet de commande de configuration

Vous pouvez créer une liste de ressources qui ont des diagnostics de Windows Azure activé, puis passer les ressources à l’applet de commande de configuration.

Vous pouvez afficher des informations supplémentaires sur l’applet de commande en exécutant `Get-Help Add-AzureDiagnosticsToLogAnalytics`.


Pour en savoir plus sur OMS [Applets de commande PowerShell journal Analytique](https://msdn.microsoft.com/library/mt188224.aspx)

>[AZURE.NOTE] Si la ressource et espace de travail se trouvent dans différents abonnements Azure, basculer entre elles selon vos besoins à l’aide`Select-AzureRmSubscription -SubscriptionId <Subscription the resource is in>`

```
# Connect to Azure
Login-AzureRmAccount
# If you have diagnostics logs being written to classic storage you will also need to run
Add-AzureAccount

# Import the module
Install-Module -Name AzureDiagnosticsAndLogAnalytics

# Update the values below with the name of the resource that has Azure diagnostics enabled and the name of your Log Analytics workspace
$resourceName = "***example-resource***"
$workspaceName = "***log-analytics-workspace***"

# Find the resource to monitor:
$resource = Find-AzureRmResource -ResourceType "Microsoft.KeyVault/Vaults" -ResourceNameContains $resourceName

# Find the Log Analytics workspace to configure, for example:
$workspace = Find-AzureRmResource -ResourceType "Microsoft.OperationalInsights/workspaces" -ResourceNameContains $workspaceName

# Perform configuration
Add-AzureDiagnosticsToLogAnalytics $resource $workspace

# Enable the Log Analytics solution
Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -intelligencepackname KeyVault -Enabled $true

```
Après avoir exécuté ce script, vous devriez voir enregistrements dans le journal Analytique environ 30 minutes une fois les nouvelles données Diagnostics écrites au stockage. Si les enregistrements ne sont pas disponibles une fois que cette fois reportez-vous à la section dépannage ci-dessous.  

>[AZURE.NOTE] La configuration n’est pas visible dans le portail Azure. Vous pouvez vérifier la configuration à l’aide du `Get-AzureRmOperationalInsightsStorageInsight` applet de commande.  


## <a name="stopping-log-analytics-from-collecting-azure-diagnostic-logs"></a>Arrêt du journal Analytique à partir de la collecte des journaux de diagnostic Azure

Pour supprimer la configuration de journal Analytique pour une ressource, utilisez le `Remove-AzureRmOperationalInsightsStorageInsight` applet de commande.

## <a name="troubleshooting-configuration-for-azure-diagnostic-logs"></a>Résolution des problèmes de configuration pour les journaux de diagnostic Azure

*Problème*

L’erreur suivante s’affiche lorsque vous configurez une ressource qui se connecte au stockage classique :

```
Select-AzureSubscription : The subscription id 7691b0d1-e786-4757-857c-7360e61896c3 doesn't exist.

Parameter name: id
```

*Résolution*

Se connecter à l’API pour le modèle de déploiement classique avec`Add-AzureAccount`

## <a name="troubleshooting-data-collection-for-azure-diagnostic-logs"></a>Résolution des problèmes de collecte de données pour les journaux de diagnostic Azure

Si vous ne voyez pas de données pour la ressource Azure dans journal Analytique, vous pouvez utiliser les étapes de résolution des problèmes suivantes :

+ Vérifier le compte de stockage de données
+ Vérifiez que la solution Analytique journal pour le service est activée
+ Vérifiez que le journal Analytique est configuré pour lire à partir du stockage
+ Mettre à jour la clé de compte de stockage

### <a name="verify-data-is-flowing-to-the-storage-account"></a>Vérifier les données sont dirige vers le compte de stockage

Vous pouvez vérifier si les données sont dirige vers le compte de stockage avec un outil qui permet la consultation de stockage Azure (par exemple Visual Studio) ou à l’aide de PowerShell.

Pour trouver le compte de stockage de la ressource est configurée pour vous connecter pour utiliser la session PowerShell suivante :

`Find-AzureRmResource -ResourceType "Microsoft.KeyVault/Vaults" | select ResourceId | Get-AzureRmDiagnosticSetting `

Le conteneur de stockage utilisé par Azure Diagnostics comporte un nom avec une mise en forme de :  

`insights-logs-<Category>`

Reportez-vous à [l’utilisation des applets de commande de stockage pour vérifier un conteneur pour les données récentes](../storage/storage-powershell-guide-full.md) pour en savoir plus sur l’affichage du contenu du compte de stockage.

### <a name="verify-the-log-analytics-solution-for-the-service-is-enabled"></a>Vérifiez que la solution Analytique journal pour le service est activée

Si vous utilisez `Add-AzureDiagnosticsToLogAnalyticsUI`, la solution journal Analytique correcte est activée automatiquement pour vous.

Pour vérifier si une solution est activée, exécutez la session PowerShell suivante :

`Get-AzureRmOperationalInsightsIntelligencePacks -ResourceGroupName $logAnalyticsResourceGroup -WorkspaceName $logAnalyticsWorkspaceName`

Si la solution n’est pas activée, vous pouvez l’activer à l’aide de la session PowerShell suivante :

`Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName $logAnalyticsResourceGroup -WorkspaceName $logAnalyticsWorkspaceName -IntelligencePackName $solution -Enabled $true`

Pour trouver le nom de la solution à activer pour chaque type de ressource, utilisez la session PowerShell suivante (cette variable est disponible une fois que vous avez importé le module) :

`$MonitorableResourcesToOMSSolutions`

### <a name="verify-that-log-analytics-is-configured-to-read-from-storage"></a>Vérifiez que le journal Analytique est configuré pour lire à partir du stockage

Si vous ajoutez des ressources supplémentaires Azure, vous devez activer l’enregistrement des Diagnostics pour les, configurer Analytique journal pour eux.
Pour vérifier les ressources et les comptes de stockage Analytique journal est configuré pour collecter les journaux de, utilisez la session PowerShell suivante :

```
# Find the Workspace ResourceGroup and Name
$logAnalyticsWorkspace = Get-AzureRmOperationalInsightsWorkspace

#Get the configuration for all resources:
Get-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $logAnalyticsWorkspace.ResourceGroupName -WorkspaceName $logAnalyticsWorkspace.Name

#Get the just the resources configured:
Get-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $logAnalyticsWorkspace.ResourceGroupName -WorkspaceName $logAnalyticsWorkspace.Name | select Containers
```

### <a name="update-the-storage-key"></a>Mise à jour de la clé de stockage

Si vous modifiez la clé pour le compte de stockage, la configuration du journal Analytique doit également être mis à jour avec la nouvelle clé.
Vous pouvez mettre à jour la configuration du journal Analytique avec une nouvelle clé à l’aide de la session PowerShell suivante :

`Set-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $logAnalyticsWorkspace.ResourceGroupName -WorkspaceName $logAnalyticsWorkspace.Name –Name <Storage Insight Name> -StorageAccountKey $newKey `

Pour trouver le nom d’un aperçu de stockage, utilisez la `Get-AzureRmOperationalInsightsStorageInsight` applet de commande, comme indiqué dans les exemples précédents.

## <a name="next-steps"></a>Étapes suivantes

- [Stockage d’objets blob utiliser pour IIS et le stockage de table pour les événements](log-analytics-azure-storage-iis-table.md) pour lire les journaux pour Azure services ce diagnostics écriture au stockage de tables ou les journaux IIS écrites dans stockage blob.
- [Activer des Solutions](log-analytics-add-solutions.md) afin de fournir des données.
- [Utiliser des requêtes de recherche](log-analytics-log-searches.md) pour analyser les données.
