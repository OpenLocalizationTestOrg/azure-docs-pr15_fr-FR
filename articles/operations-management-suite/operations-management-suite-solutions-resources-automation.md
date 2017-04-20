<properties
   pageTitle="Ressources Automation solutions OMS | Microsoft Azure"
   description="Solutions dans OMS inclura généralement procédures opérationnelles dans Azure Automation pour automatiser des processus tels que pour collecter et traiter les données d’analyse.  Cet article décrit comment inclure des procédures opérationnelles et leurs ressources connexes dans une solution."
   services="operations-management-suite"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="operations-management-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/19/2016"
   ms.author="bwren" />

# <a name="automation-resources-in-oms-solutions-preview"></a>Ressources Automation solutions OMS (Preview)

>[AZURE.NOTE]Il s’agit d’une documentation préliminaire pour créer des solutions de gestion dans OMS qui sont actuellement en mode Aperçu avant. N’importe quel schéma décrit ci-dessous peut être modifiée.   

[Solutions de gestion OMS](operations-management-suite-solutions.md) inclura généralement procédures opérationnelles dans Azure Automation pour automatiser des processus tels que pour collecter et traiter les données d’analyse.  Outre les procédures opérationnelles, comptes Automation inclut tels que des variables et des planifications qui prennent en charge les procédures opérationnelles utilisées dans la solution.  Cet article décrit comment inclure des procédures opérationnelles et leurs ressources connexes dans une solution.

>[AZURE.NOTE]Les exemples de cet article utilisent les paramètres et les variables obligatoire ou communes aux solutions de gestion et décrites dans les [solutions de gestion de création dans Suite de gestion des opérations (OMS)](operations-management-suite-solutions-creating.md) 


## <a name="prerequisites"></a>Conditions préalables
Cet article suppose que vous connaissez déjà comment [créer une solution de gestion](operations-management-suite-solutions-creating.md) et la structure d’un fichier solution.

## <a name="samples"></a>Exemples
Vous pouvez obtenir des exemples de modèles Gestionnaire de ressources pour les ressources Automation à partir des [modèles de démarrage rapide dans GitHub](https://github.com/azureautomation/automation-packs/tree/master/101-sample-automation-resource-templates).

## <a name="automation-account"></a>Compte d’Automation
Toutes les ressources dans Azure Automation sont contenues dans un [compte Automation](../automation/automation-security-overview.md#automation-account-overview).  Comme décrit dans [l’espace de travail OMS et compte Automation](operations-management-suite-solutions-creating.md#oms-workspace-and-automation-account) le compte Automation n’est pas inclus dans la solution de gestion mais doit exister avant que la solution est installée.  Si elle n’est pas disponible, l’installation de solution échouera.

Le nom de leur propre compte Automation est le nom de chaque ressource Automation.  Pour ce faire de la solution avec le paramètre de **nom de compte** comme dans l’exemple suivant d’une ressource runbook.
    
    "name": "[concat(parameters('accountName'), '/MyRunbook'))]"


## <a name="runbooks"></a>Procédures opérationnelles
[Azure Automation runbook](../automation/automation-runbook-types.md) ressources ont un type de **Microsoft.Automation/automationAccounts/runbooks** et ont les propriétés dans le tableau suivant.

| Propriété | Description |
|:--|:--|
| runbookType | Spécifie les types de la procédure opérationnelle. <br><br> Script - script PowerShell <br>PowerShell - flux de travail PowerShell <br> GraphPowerShell - runbook de script PowerShell graphique <br> GraphPowerShellWorkflow - runbook de flux de travail PowerShell graphique   |
| logProgress | Indique si les [enregistrements de l’avancement](../automation/automation-runbook-output-and-messages.md) doivent être générés pour la procédure opérationnelle. |
| logVerbose  | Indique si les [enregistrements détaillées](../automation/automation-runbook-output-and-messages.md) doivent être générés pour la procédure opérationnelle. |
| Description | Description facultative de la procédure opérationnelle. |
| publishContentLink | Spécifie le contenu de la procédure opérationnelle. <br><br>URI - Uri au contenu de la procédure opérationnelle.  Il s’agit d’un fichier .ps1 pour PowerShell et Script procédures opérationnelles et un fichier exporté runbook graphique pour un runbook de graphique.  <br> version : Version de la procédure opérationnelle pour votre propre suivi. |

Exemple d’une ressource runbook est inférieur à.  Dans ce cas, il récupère le runbook de la [Galerie de PowerShell](https://www.powershellgallery.com).

    "name": "[concat(parameters('accountName'), '/Start-AzureV2VMs'))]",
    "type": "Microsoft.Automation/automationAccounts/runbooks",
    "apiVersion": "[variables('AutomationApiVersion')]",
    "location": "[parameters('regionId')]",
    "dependsOn": [
        "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('startVmsRunbookName'))]"
    ],
    "tags": { },
    "properties": {
        "runbookType": "PowerShell",
        "logProgress": "true",
        "logVerbose": "true",
        "description": "",
        "publishContentLink": {
            "uri": "https://www.powershellgallery.com/api/v2/items/psscript/package/Update-ModulesInAutomationToLatestVersion/1.0/Start-AzureV2VMs.ps1",
            "version": "1.0"
        }
    }

### <a name="starting-a-runbook"></a>Démarrage d’un runbook
Il existe deux méthodes pour démarrer un runbook dans une solution de gestion.

- Pour commencer la procédure opérationnelle après avoir installé la solution, créez une [ressource de travail](#automation-jobs) comme décrit ci-dessous.
- Pour commencer la procédure opérationnelle sur un planning, créez une [planification ressource](#schedules) comme décrit ci-dessous. 


## <a name="automation-jobs"></a>Tâches d’automatisation
Avant de lancer une procédure opérationnelle après avoir installé la solution de gestion, vous créez une ressource de **travail** .  Ressources de travail ont un type de **Microsoft.Automation/automationAccounts/jobs** et les propriétés dans le tableau suivant.

| Propriété | Description |
|:--|:--|
| runbook    | Seul **nom** entité portant le nom de la procédure opérationnelle.  |
| paramètres | Entité pour chaque paramètre requis par la procédure opérationnelle. |


La tâche contient le nom de runbook et des valeurs de paramètre à envoyer à la procédure opérationnelle.  La tâche doit [dépendent](operations-management-suite-solutions-creating.md#resources) du runbook lequel il démarre depuis la runbook doit être créé avant la tâche.  Vous également créer des dépendances sur les autres tâches de procédures opérationnelles qui doivent être exécutées avant celle en cours.

Le nom d’une ressource de travail doit contenir un GUID qui est généralement attribué par un paramètre.  Vous pouvez en savoir plus sur les paramètres GUID [Création de solutions dans Suite de gestion des opérations (OMS)](operations-management-suite-solutions-creating.md#parameters).  

Voici un exemple d’une ressource de travail qui démarre un runbook après avoir installé la solution de gestion.  Une autres procédures opérationnelles doit être terminé avant ce une commence, afin qu’il comporte des dépendances sur les tâches de cette procédure opérationnelle.  Les détails de la tâche sont fournies par les paramètres et variables plutôt que directement spécifiée.

    {
        "name": "[concat(parameters('accountName'), '/', parameters('startVmsRunbookGuid'))]",
        "type": "Microsoft.Automation/automationAccounts/jobs",
        "apiVersion": "[variables('AutomationApiVersion')]",
        "location": "[parameters('regionId')]",
        "dependsOn": [
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('startVmsRunbookName'))]",
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/jobs/', parameters('initialPrepRunbookGuid'))]"
        ],
        "tags": { },
        "properties": {
            "runbook": {
                "name": "[variables('startVmsRunbookName')]"
            },
            "parameters": {
                "AzureConnectionAssetName": "[variables('AzureConnectionAssetName')]",
                "ResourceGroupName": "[variables('ResourceGroupName')]"
            }
        }
    }


## <a name="certificates"></a>Certificats
[Certificats Azure Automation](../automation/automation-certificates.md) ont un type de **Microsoft.Automation/automationAccounts/certificates** et avoir les propriétés dans le tableau suivant.

| Propriété | Description |
|:--|:--|
| base64Value   | Valeur de base 64 pour le certificat. |
| empreinte numérique    | Empreinte numérique du certificat. |

Exemple d’une ressource de certificat est inférieur à.

    "name": "[concat(parameters('accountName'), '/MyCertificate')]",
    "type": "certificates",
    "apiVersion": "2015-01-01-preview",
    "location": "[parameters('regionId')]",
    "tags": {},
    "dependsOn": [
    ],
    "properties": {
        "base64Value": "MIIC1jCCAA8gAwIAVgIYJY4wXCXH/YAHMtALh7qEFzANAgkqhkiG5w0AAQUFGDAUMRIwEBYDVQQDEwlsA3NhAGevc3QqHhcNMTZwNjI5MHQxMjAsWhcNOjEwNjI5NKWwMDAwWjAURIwEAYDVQQBEwlsA2NhAGhvc3QwggEiMA0GCSqGSIA3DQEAAQUAA4IADwAwggEKAoIAAQDIyzv2A0RUg1/AAryI9W1DGAHAqqGdlFfTkUSDfv+hEZTAwKv0p8daqY6GroT8Du7ctQmrxJsy8JxIpDWxUaWwXtvv1kR9eG9Vs5dw8gqhjtOwgXvkOcFdKdQwA82PkcXoHlo+NlAiiPPgmHSELGvcL1uOgl3v+UFiiD1ro4qYqR0ITNhSlq5v2QJIPnka8FshFyPHhVtjtKfQkc9G/xDePW8dHwAhfi8VYRmVMmJAEOLCAJzRjnsgAfznP8CZ/QUczPF8LuTZ/WA/RaK1/Arj6VAo1VwHFY4AZXAolz7xs2sTuHplVO7FL8X58UvF7nlxq48W1Vu0l8oDi2HjvAgMAAAGjJDAiMAsGA1UdDwREAwIEsDATAgNVHSUEDDAKAggrAgEFNQcDATANAgkqhkiG9w0AAQUFAAOCAQEAk8ak2A5Ug4Iay2v0uXAk95qdAthJQN5qIVA13Qay8p4MG/S5+aXOVz4uMXGt18QjGds1A7Q8KDV4Slnwn95sVgA5EP7akvoGXhgAp8Dm90sac3+aSG4fo1V7Y/FYgAgpEy4C/5mKFD1ATeyyhy3PmF0+ZQRJ7aLDPAXioh98LrzMZr1ijzlAAKfJxzwZhpJamAwjZCYqiNZ54r4C4wA4QgX9sVfQKd5e/gQnUM8gTQIjQ8G2973jqxaVNw9lZnVKW3C8/QyLit20pNoqX2qQedwsqg3WCUcPRUUqZ4NpQeHL/AvKIrt158zAfU903yElAEm2Zr3oOUR4WfYQ==",
        "thumbprint": "F485CBE5569F7A5019CB68D7G6D987AC85124B4C"
    }

## <a name="credentials"></a>Informations d’identification
[Informations d’identification Azure Automation](../automation/automation-credentials.md) ont un type de **Microsoft.Automation/automationAccounts/credentials** et avoir les propriétés dans le tableau suivant.

| Propriété | Description |
|:--|:--|
| nom d’utilisateur   | Nom d’utilisateur pour les informations d’identification. |
| mot de passe   | Mot de passe pour les informations d’identification. |

Exemple d’une ressource d’informations d’identification est inférieur à.

    "name": "[concat(parameters('accountName'), '/', variables('credentialName'))]",
    "type": "Microsoft.Automation/automationAccounts/runbooks/credentials",
    "apiVersion": "[variables('AutomationApiVersion')]",
    "location": "[parameters('regionId')]",
    "tags": { },
    "dependsOn": [
    ],
    "properties": {
        "userName": "User01",
        "password": "password"
    }


## <a name="schedules"></a>Calendriers
[Automatisation Azure planifications](../automation/automation-schedules.md) ont un type de **Microsoft.Automation/automationAccounts/schedules** et avoir les propriétés dans le tableau suivant.

| Propriété | Description |
|:--|:--|
| Description | Description facultative de l’échéancier. |
| heure de début   | Spécifie l’heure de début d’une planification comme un objet DateTime. Une chaîne peut être fournie si elle peut être converti en une valeur DateTime valide. |
| isEnabled   | Indique si la planification est activée. |
| intervalle    | Le type d’intervalle au planning.<br><br>jour<br>heure |
| fréquence   | Fréquence à laquelle la planification doit se déclenche dans nombre de jours ou d’heures. |

Exemple d’une ressource planning est inférieur à.

    "name": "[concat(parameters('accountName'), '/', variables('variableName'))]",
    "type": "microsoft.automation/automationAccounts/schedules",
    "apiVersion": "[variables('AutomationApiVersion')]",
    "tags": { },
    "dependsOn": [
    ],
    "properties": {
        "description": "Schedule for StartByResourceGroup runbook",
        "startTime": "10/30/2016 12:00:00",
        "isEnabled": "true",
        "interval": "day",
        "frequency": "1"
    }


## <a name="variables"></a>Variables
[Les variables Azure Automation](../automation/automation-variables.md) ont un type de **Microsoft.Automation/automationAccounts/variables** et avoir les propriétés dans le tableau suivant.

| Propriété | Description |
|:--|:--|
| Description | Description facultative de la variable. |
| isEncrypted | Indique si la variable doit être chiffrée. |
| type        | Type de données pour la variable. |
| valeur       | Valeur de la variable. |

Exemple d’une ressource variable est inférieur à.

    "name": "[concat(parameters('accountName'), '/', variables('StartTargetResourceGroupsAssetName')) ]",
    "type": "microsoft.automation/automationAccounts/variables",
    "apiVersion": "[variables('AutomationApiVersion')]",
    "tags": { },
    "dependsOn": [
    ],
    "properties": {
        "description": "Description for the variable.",
        "isEncrypted": "true",
        "type": "string",
        "value": "'This is a string value.'"
    }


## <a name="modules"></a>Modules
Votre solution de gestion n’a pas besoin de définir des [modules globales](../automation/automation-integration-modules.md) utilisée par les procédures opérationnelles parce qu’elles soient toujours disponibles.  Vous n’avez pas besoin d’inclure une ressource pour tout autre module utilisé par les procédures opérationnelles, et la procédure opérationnelle doit dépendre de la ressource module pour vous assurer qu’elle est créée avant la runbook.

[Modules d’intégration](../automation/automation-integration-modules.md) ont un type de **Microsoft.Automation/automationAccounts/modules** et les propriétés dans le tableau suivant.

| Propriété | Description |
|:--|:--|
| contentLink | Spécifie le contenu du module. <br><br>URI - Uri au contenu de la procédure opérationnelle.  Il s’agit d’un fichier .ps1 pour PowerShell et Script procédures opérationnelles et un fichier exporté runbook graphique pour un runbook de graphique.  <br> version : Version de la procédure opérationnelle pour votre propre suivi. |

Exemple d’une ressource de module est inférieur à.

    {       
        "name": "[concat(parameters('accountName'), '/', variables('OMSIngestionModuleName'))]",
        "type": "Microsoft.Automation/automationAccounts/modules",
        "apiVersion": "[variables('AutomationApiVersion')]",
        "dependsOn": [
        ],
        "properties": {
            "contentLink": {
                "uri": "https://devopsgallerystorage.blob.core.windows.net/packages/omsingestionapi.1.3.0.nupkg"
            }
        }
    }

### <a name="updating-modules"></a>Mise à jour des modules
Si vous mettez à jour une solution de gestion qui inclut un runbook qui utilise une planification, et la nouvelle version de votre solution comporte un nouveau module utilisé par cette procédure opérationnelle, le runbook peut utiliser l’ancienne version du module.  Vous devez inclure les procédures opérationnelles suivants dans votre solution et créer une tâche pour exécuter les avant les autres procédures opérationnelles.  Cela garantit que tous les modules sont mis à jour en tant que nécessaire avant que les procédures opérationnelles sont chargés.

- [Mise à jour ModulesinAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/1.03/DisplayScript) garantit que tous les modules utilisés par les procédures opérationnelles dans votre solution sont la version la plus récente.  
- [ReRegisterAutomationSchedule MS-gestion](https://www.powershellgallery.com/packages/ReRegisterAutomationSchedule-MS-Mgmt/1.0/DisplayScript) sera réenregistrer toutes les ressources de planification pour vous assurer que les procédures opérationnelles sont liées avec l’option utiliser les modules dernières.


Voici un exemple des éléments requis d’une solution pour prendre en charge de la mise à jour du module.
    
    "parameters": {
        "ModuleImportGuid": {
            "type": "string",
            "metadata": {
                "control": "guid"
            }
        },
        "ReRegisterRunbookGuid": {
            "type": "string",
            "metadata": {
                "control": "guid"
            }
        }
    },

    "variables": {
        "ModuleImportRunbookName": "Update-ModulesInAutomationToLatestVersion",
        "ModuleImportRunbookUri": "https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/1.03/Content/Update-ModulesInAutomationToLatestVersion.ps1",
        "ModuleImportRunbookDescription": "Imports modules and also updates all Azure dependent modules that are in the same Automation Account.",
        "ReRegisterSchedulesRunbookName": "Update-ModulesInAutomationToLatestVersion",
        "ReRegisterSchedulesRunbookUri": "https://www.powershellgallery.com/packages/ReRegisterAutomationSchedule-MS-Mgmt/1.0/Content/ReRegisterAutomationSchedule-MS-Mgmt.ps1",
        "ReRegisterSchedulesRunbookDescription": "Reregisters schedules to ensure that they use latest modules."
    },

    "resources": [
        {
            "name": "[concat(parameters('accountName'), '/', variables('ModuleImportRunbookName'))]",
            "type": "Microsoft.Automation/automationAccounts/runbooks",
            "apiVersion": "[variables('AutomationApiVersion')]",
            "dependsOn": [
            ],
            "location": "[parameters('regionId')]",
            "tags": { },
            "properties": {
                "runbookType": "PowerShell",
                "logProgress": "true",
                "logVerbose": "true",
                "description": "[variables('ModuleImportRunbookDescription')]",
                "publishContentLink": {
                    "uri": "[variables('ModuleImportRunbookUri')]",
                    "version": "1.0.0.0"
                }
            }
        },
        {
            "name": "[concat(parameters('accountName'), '/', parameters('ModuleImportGuid'))]",
            "type": "Microsoft.Automation/automationAccounts/jobs",
            "apiVersion": "[variables('AutomationApiVersion')]",
            "location": "[parameters('regionId')]",
            "dependsOn": [
                "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('ModuleImportRunbookName'))]"
            ],
            "tags": { },
            "properties": {
                "runbook": {
                    "name": "[variables('ModuleImportRunbookName')]"
                },
                "parameters": {
                    "ResourceGroupName": "[resourceGroup().name]",
                    "AutomationAccountName": "[parameters('accountName')]",
                    "NewModuleName": "AzureRM.Insights"
                }
            }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('ReRegisterSchedulesRunbookName'))]",
          "type": "Microsoft.Automation/automationAccounts/runbooks",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "dependsOn": [
          ],
          "location": "[parameters('regionId')]",
          "tags": { },
          "properties": {
            "runbookType": "PowerShell",
            "logProgress": "true",
            "logVerbose": "true",
            "description": "[variables('ReRegisterSchedulesRunbookDescription')]",
            "publishContentLink": {
              "uri": "[variables('ReRegisterSchedulesRunbookUri')]",
              "version": "1.0.0.0"
            }
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', parameters('reRegisterSchedulesGuid'))]",
          "type": "Microsoft.Automation/automationAccounts/jobs",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "location": "[parameters('regionId')]",
          "dependsOn": [
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('ReRegisterSchedulesRunbookName'))]"
          ],
          "tags": { },
          "properties": {
            "runbook": {
              "name": "[variables('ReRegisterSchedulesRunbookName')]"
            },
            "parameters": {
              "targetSubscriptionId": "[subscription().subscriptionId]",
              "resourcegroup": "[resourceGroup().name]",
              "automationaccount": "[parameters('accountName')]"
            }
        }
    ]


## <a name="next-steps"></a>Étapes suivantes

- [Ajouter une vue à votre solution](operations-management-suite-solutions-resources-views.md) pour visualiser les données collectées.