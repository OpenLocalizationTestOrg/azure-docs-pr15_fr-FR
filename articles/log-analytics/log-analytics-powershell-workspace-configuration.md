<properties
    pageTitle="Utiliser PowerShell pour créer et configurer un espace de travail Analytique journal | Microsoft Azure"
    description="Ouvrez une session Analytique utilise données à partir de serveurs dans vos locaux ou sur le cloud infrastructure. Vous pouvez collecter des données de l’ordinateur à partir du stockage Azure lorsque générés par les diagnostics de Windows Azure."
    services="log-analytics"
    documentationCenter=""
    authors="richrundmsft"
    manager="jochan"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="powershell"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="richrund"/>

# <a name="manage-log-analytics-using-powershell"></a>Gérer les journaux Analytique à l’aide de PowerShell

Vous pouvez utiliser les [applets de commande PowerShell Analytique de journal] (https://msdn.microsoft.com/library/mt188224(v=azure.300\).aspx) d’effectuer diverses tâches dans le journal Analytique à partir d’une ligne de commande ou dans le cadre d’un script.  Voici quelques exemples des tâches que vous pouvez effectuer avec PowerShell :

+ Créer un espace de travail
+ Ajouter ou supprimer une solution
+ Importer et exporter des recherches enregistrées
+ Créer un groupe d’ordinateurs
+ Activer la collecte des journaux IIS à partir d’ordinateurs avec l’agent Windows est installé
+ Collecter les compteurs d’ordinateurs Linux et Windows
+ Collecter les événements de journal système sur les ordinateurs Linux 
+ Collecter les événements de journaux des événements Windows
+ Collecter les journaux d’événements personnalisés
+ Ajouter l’agent analytique journal à une machine virtuelle Azure
+ Configurer le journal analytique pour indexer les données collectées à l’aide des diagnostics de Windows Azure


Cet article fournit deux exemples de code qui illustrent quelques-unes des fonctions que vous pouvez effectuer à partir de PowerShell.  Vous pouvez faire référence à la [référence applet de commande PowerShell Analytique de journal] (https://msdn.microsoft.com/library/mt188224(v=azure.300\).aspx) à d’autres fonctions.

> [AZURE.NOTE] Journal Analytique était appelé précédemment Insights opérationnelles, c’est pourquoi il est le nom utilisé dans les applets de commande.

## <a name="prerequisites"></a>Conditions préalables

Pour utiliser PowerShell à votre espace de travail journal Analytique, vous devez :

+ Un abonnement Azure, et 
+ Votre espace de travail Azure journal Analytique liée à votre abonnement Azure.

Si vous avez créé un espace de travail OMS, mais elle n’est encore liée il à un abonnement Azure, vous pouvez créer le lien :

+ Dans le portail Azure
+ Dans le portail OMS ou 
+ Utiliser les applets de commande Get-AzureRmOperationalInsightsLinkTargets et AzureRmOperationalInsightsWorkspace de nouveau.


## <a name="create-and-configure-a-log-analytics-workspace"></a>Créer et configurer un espace de travail Analytique journal

L’exemple de script suivant illustre comment :

1.  Créer un espace de travail
2.  Énumérer les solutions disponibles
3.  Ajouter des solutions à l’espace de travail
4.  Recherches importation enregistrée
5.  Recherches de l’exportation enregistrée
6.  Créer un groupe d’ordinateurs
7.  Activer la collecte des journaux IIS à partir d’ordinateurs avec l’agent Windows est installé
8.  Collecter les compteurs de performance disque logique d’ordinateurs Linux (% Inodes utilisés ; Mégaoctets libres ; % Utilisé espace ; Transferts disque/s ; Lectures disque/s ; Écritures disque/s)
9.  Collecter les événements de journal système d’ordinateurs Linux
10. Collecter les événements d’erreur et d’avertissement dans le journal des événements à partir d’ordinateurs Windows
11. Collecter les compteur de performance de mémoire disponible Mo d’ordinateurs Windows
12. Collecte un journal personnalisé 


```

$ResourceGroup = "oms-example"
$WorkspaceName = "log-analytics-" + (Get-Random -Maximum 99999) # workspace names need to be unique - Get-Random helps with this for the example code
$Location = "westeurope"

# List of solutions to enable
$Solutions = "Security", "Updates", "SQLAssessment"

# Saved Searches to import
$ExportedSearches = @"
[
    {
        "Category":  "My Saved Searches",
        "DisplayName":  "WAD Events (All)",
        "Query":  "Type=Event SourceSystem:AzureStorage ",
        "Version":  1
    },
    {        
        "Category":  "My Saved Searches",
        "DisplayName":  "Current Disk Queue Length",
        "Query":  "Type=Perf ObjectName=LogicalDisk InstanceName=\"C:\" CounterName=\"Current Disk Queue Length\"",
        "Version":  1
    }
]
"@ | ConvertFrom-Json

# Custom Log to collect
$CustomLog = @"
{
    "customLogName": "sampleCustomLog1", 
    "description": "Example custom log datasource", 
    "inputs": [
        { 
            "location": { 
            "fileSystemLocations": { 
                "windowsFileTypeLogPaths": [ "e:\\iis5\\*.log" ], 
                "linuxFileTypeLogPaths": [ "/var/logs" ] 
                } 
            }, 
        "recordDelimiter": { 
            "regexDelimiter": { 
                "pattern": "\\n", 
                "matchIndex": 0, 
                "matchIndexSpecified": true, 
                "numberedGroup": null 
                } 
            } 
        }
    ], 
    "extractions": [
        { 
            "extractionName": "TimeGenerated", 
            "extractionType": "DateTime", 
            "extractionProperties": { 
                "dateTimeExtraction": { 
                    "regex": null, 
                    "joinStringRegex": null 
                    } 
                } 
            }
        ] 
    }
"@

# Create the resource group if needed
try {
    Get-AzureRmResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzureRmResourceGroup -Name $ResourceGroup -Location $Location
}

# Create the workspace
New-AzureRmOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup

# List all solutions and their installation status
Get-AzureRmOperationalInsightsIntelligencePacks -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Add solutions
foreach ($solution in $Solutions) {
    Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -IntelligencePackName $solution -Enabled $true
}

#List enabled solutions
(Get-AzureRmOperationalInsightsIntelligencePacks -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName).Where({($_.enabled -eq $true)})

# Import Saved Searches
foreach ($search in $ExportedSearches) {
    $id = $search.Category + "|" + $search.DisplayName
    New-AzureRmOperationalInsightsSavedSearch -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId $id -DisplayName $search.DisplayName -Category $search.Category -Query $search.Query -Version $search.Version
}

# Export Saved Searches
(Get-AzureRmOperationalInsightsSavedSearch -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName).Value.Properties | ConvertTo-Json 

# Create Computer Group
New-AzureRmOperationalInsightsComputerGroup -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId "My Web Servers" -DisplayName "Web Servers" -Category "My Saved Searches" -Query "Computer=""web*"" | distinct Computer" -Version 1

# Enable IIS Log Collection using agent
Enable-AzureRmOperationalInsightsIISLogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Linux Perf
New-AzureRmOperationalInsightsLinuxPerformanceObjectDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -ObjectName "Logical Disk" -InstanceName "*"  -CounterNames @("% Used Inodes", "Free Megabytes", "% Used Space", "Disk Transfers/sec", "Disk Reads/sec", "Disk Reads/sec", "Disk Writes/sec") -IntervalSeconds 20  -Name "Example Linux Disk Performance Counters"
Enable-AzureRmOperationalInsightsLinuxCustomLogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Linux Syslog
New-AzureRmOperationalInsightsLinuxSyslogDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -Facility "kern" -CollectEmergency -CollectAlert -CollectCritical -CollectError -CollectWarning -Name "Example kernal syslog collection"
Enable-AzureRmOperationalInsightsLinuxSyslogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Windows Event
New-AzureRmOperationalInsightsWindowsEventDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -EventLogName "Application" -CollectErrors -CollectWarnings -Name "Example Application Event Log"

# Windows Perf
New-AzureRmOperationalInsightsWindowsPerformanceCounterDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -ObjectName "Memory" -InstanceName "*" -CounterName "Available MBytes" -IntervalSeconds 20 -Name "Example Windows Performance Counter"

# Custom Logs
New-AzureRmOperationalInsightsCustomLogDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -CustomLogRawJson "$CustomLog" -Name "Example Custom Log Collection"

```

## <a name="configuring-log-analytics-to-index-azure-diagnostics"></a>Configuration de journal Analytique à indexer les diagnostics de Windows Azure 

Pour une analyse sans agent de ressources Azure, les ressources doivent être diagnostics de Windows Azure activé et configuré pour écrire dans un compte de stockage. Journal Analytique puis peut être configuré pour collecter les journaux à partir du compte de stockage. Ressources dont vous avez besoin pour effectuer la configuration précédente inclut :

+ Services de cloud classique (rôles web et de travail)
+ Clusters TISSU service
+ Groupes de sécurité réseau
+ Touche chambres fortes et 
+ Passerelles d’application

Vous pouvez également utiliser PowerShell pour configurer un espace de travail journal Analytique avec un même abonnement Azure pour collecter les journaux des différents abonnements Azure.

L’exemple suivant montre comment :

1.  Liste des comptes de stockage existant et emplacements journal Analytique indexer les données à partir de
2.  Créer une configuration pour lire à partir d’un compte de stockage
3.  Mise à jour de la configuration d’indexer des données nouvellement créée à partir d’autres emplacements
4.  Supprimer la configuration nouvellement créée

```
# validTables = "WADWindowsEventLogsTable", "LinuxsyslogVer2v0", "WADServiceFabric*EventTable", "WADETWEventTable" 
$workspace = (Get-AzureRmOperationalInsightsWorkspace).Where({$_.Name -eq "your workspace name"})

# Update these two lines with the storage account resource ID and the storage account key for the storage account you want to Log Analytics to  
$storageId = "/subscriptions/ec11ca60-1234-491e-5678-0ea07feae25c/resourceGroups/demo/providers/Microsoft.Storage/storageAccounts/wadv2storage"
$key = "abcd=="

# List existing insights
Get-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name

# Create a new insight
New-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" -StorageAccountResourceId $storageId -StorageAccountKey $key -Tables @("WADWindowsEventLogsTable") -Containers @("wad-iis-logfiles")

# Update existing insight
Set-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" -Tables @("WADWindowsEventLogsTable", "WADETWEventTable") -Containers @("wad-iis-logfiles", "insights-logs-networksecuritygroupevent/resourceId=/SUBSCRIPTIONS/ec11ca60-1234-491e-5678-0ea07feae25c/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/DEMO")

# Remove the insight
Remove-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" 

```

## <a name="next-steps"></a>Étapes suivantes

- [Applets de commande PowerShell de révision journal Analytique](http://msdn.microsoft.com/library/mt188224.aspx) pour plus d’informations sur l’utilisation de PowerShell pour la configuration de journal Analytique.

