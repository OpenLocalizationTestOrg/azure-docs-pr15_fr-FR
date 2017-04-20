<properties
   pageTitle="Créer des solutions de gestion dans Suite de gestion des opérations (OMS) | Microsoft Azure"
   description="Solutions de gestion étendent les fonctionnalités de Suite de gestion des opérations (OMS) en fournissant des scénarios de package de gestion des clients peuvent ajouter à son espace de travail OMS.  Cet article fournit des détails sur comment vous pouvez créer des solutions de gestion à utiliser dans votre propre environnement ou mis à disposition à vos clients."
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
   ms.date="10/27/2016"
   ms.author="bwren" />

# <a name="creating-management-solutions-in-operations-management-suite-oms-preview"></a>Créer des solutions de gestion des opérations gestion Suite (OMS) (Preview)

>[AZURE.NOTE]Il s’agit d’une documentation préliminaire pour créer des solutions de gestion dans OMS qui sont actuellement en mode Aperçu avant. N’importe quel schéma décrit ci-dessous peut être modifiée.  

Solutions de gestion étendent les fonctionnalités de Suite de gestion des opérations (OMS) en fournissant des scénarios de package de gestion des clients peuvent ajouter à son espace de travail OMS.  Cet article fournit des détails sur la création de vos propres solutions de gestion que vous pouvez utiliser dans votre propre environnement ou rendre disponibles aux clients par la Communauté.

## <a name="planning-your-management-solution"></a>Planification de votre solution de gestion
Solutions de gestion OMS incluent plusieurs ressources prenant en charge un scénario de gestion particulier.  Lorsque vous planifiez votre solution, vous devez vous concentrer sur le scénario que vous essayez d’atteindre et toutes les ressources nécessaires pour prendre en charge.  Chaque solution doit être autonome et définissez chaque ressource dont il a besoin, même si une ou plusieurs ressources sont également définies par d’autres solutions.  Après avoir installé une solution de gestion, chaque ressource est créée, sauf si elle existe déjà, et vous pouvez définir ce qui se passe aux ressources lorsqu’une solution est supprimée.  

Par exemple, une solution de gestion peut-être inclure une [procédure opérationnelle Automation Azure](../automation/automation-intro.md) qui collecte des données dans le référentiel de journal Analytique à l’aide d’un [Échéancier](../automation/automation-schedules.md) et une [vue](../log-analytics/log-analytics-view-designer.md) qui fournit différentes visualisations de données collectées.  La même planification peut être utilisée par une autre solution.  En tant que l’auteur de solution de gestion, vous souhaitez définir toutes les trois ressources mais spécifier que les runbook et la vue doivent être supprimés automatiquement lorsque la solution est supprimée.    Vous le feriez également définir la planification mais spécifier qu’elle doit rester en place si la solution ont été supprimée au cas où il a été en cours d’utilisation par l’autre solution.

## <a name="management-solution-files"></a>Fichiers de solution de gestion
Gestion des solutions sont implémentées en tant que [modèles de gestion des ressources](../resource-manager-template-walkthrough.md).  L’apprentissage de la tâche principale à apprendre à créer des solutions de gestion de [l’auteur d’un modèle](../resource-group-authoring-templates.md).  Cet article fournit des détails uniques des modèles utilisés pour les solutions et la définition des ressources de la solution classique.

La structure de base d’un fichier de solution de gestion des est identique à un [Modèle de gestionnaire de ressources](resource-group-authoring-templates.md#template-format) qui se présente comme suit.  Chacune des sections suivantes décrit les éléments de niveau supérieur et et leur contenu dans une solution.  

    {
       "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "",
       "parameters": {  },
       "variables": {  },
       "resources": [  ],
       "outputs": {  }
    }

## <a name="parameters"></a>Paramètres

[Paramètres](../resource-group-authoring-templates.md#parameters) sont des valeurs dont vous avez besoin de l’utilisateur lors de l’installation de la solution de gestion.  Il existe des paramètres standards ayant à toutes les solutions, et vous pouvez ajouter des paramètres supplémentaires selon les besoins de votre solution particulière.  Comment les utilisateurs constituent les valeurs de paramètre lors de l’installation de votre solution varient selon le paramètre spécifique et la manière dont la solution est en cours d’installation.


Lorsqu’un utilisateur installe votre solution de gestion via le [Azure Marketplace](operations-management-suite-solutions.md#finding-and-installing-solutions) ou des [modèles de démarrage rapide Azure](operations-management-suite-solutions.md#finding-and-installing-solutions) que vous y êtes invités à sélectionner un [espace de travail OMS et Automation compte](operations-management-suite-solutions-creating.md#oms-workspace-and-automation-account).  Celles-ci sont utilisées pour remplir les valeurs de chacun des paramètres standards.  L’utilisateur n’est pas invité à fournir directement des valeurs pour les paramètres standard, mais il est invité à fournir des valeurs pour les paramètres supplémentaires.

Lorsque l’utilisateur installe votre solution [une autre méthode](operations-management-suite-solutions.md#finding-and-installing-solutions), ils doivent fournir une valeur pour tous les paramètres standards et tous les paramètres supplémentaires.

Voici un exemple de paramètre.

    "Daily Start Time": {
        "type": "string",
        "metadata": {
            "description": "Enter time for starting VMs by resource group.",
            "control": "datetime",
            "category": "Schedule"
        }

Le tableau suivant décrit les attributs d’un paramètre.

| Attribut | Description |
|:--|:--|
| type        | Type de données pour le paramètre. Le contrôle d’entrée affiché pour l’utilisateur varie selon le type de données.<br><br>bool - zone de liste déroulante<br>chaîne - zone de texte<br>int - zone de texte<br>SecureString - champ mot de passe<br> |
| catégorie    | Category facultatif pour le paramètre.  Paramètres de la même catégorie sont regroupés. |
| contrôle     | Fonctionnalités supplémentaires pour les paramètres de la chaîne.<br><br>date/heure - contrôle Datetime s’affiche.<br>GUID - valeur Guid est généré automatiquement, et le paramètre n’est pas affiché. |
| Description | Description facultative pour le paramètre.  Affichés dans une info-bulle informations en regard du paramètre. |


### <a name="standard-parameters"></a>Paramètres standard
Le tableau suivant répertorie les paramètres standard pour toutes les solutions de gestion.  Les valeurs suivantes sont remplies pour l’utilisateur au lieu de lui demandant lorsque votre solution est installée depuis les modèles de démarrage rapide ou Azure Marketplace.  L’utilisateur doit fournir des valeurs si la solution est installée avec une autre méthode.

>[AZURE.NOTE]L’interface utilisateur dans les modèles de démarrage rapide et Azure Marketplace attend les noms de paramètres dans le tableau.  Si vous utilisez des noms de paramètres différents puis l’utilisateur sera invité pour eux et ils ne seront pas automatiquement remplies.


| Paramètre | Type | Description |
|:--|:--|:--|
| nom de compte       | chaîne |  Nom du compte Azure Automation. |
| pricingTier       | chaîne | Niveau de tarification d’espace de travail journal Analytique et compte Azure Automation. |
| regionId          | chaîne | Région du compte Azure Automation. |
| NomSolution      | chaîne | Nom de la solution. |
| workspaceName     | chaîne | Nom de journal Analytique espace de travail. |
| workspaceRegionId | chaîne | Zone de l’espace de travail journal Analytique. |





### <a name="sample"></a>Exemple
Voici une entité de paramètre exemple pour une solution.  Cela inclut tous les paramètres standard et deux paramètres supplémentaires dans la même catégorie.

    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
                "description": "A valid Log Analytics workspace name"
            }
        },
        "accountName": {
            "type": "string",
            "metadata": {
                "description": "A valid Azure Automation account name"
            }
        },
        "workspaceRegionId": {
            "type": "string",
            "metadata": {
                "description": "Region of the Log Analytics workspace"
            }
        },
        "regionId": {
            "type": "string",
            "metadata": {
                "description": "Region of the Azure Automation account"
            }
        },
        "pricingTier": {
            "type": "string",
            "metadata": {
                "description": "Pricing tier of both Log Analytics workspace and Azure Automation account"
            }
        },
        "jobIdGuid": {
        "type": "string",
            "metadata": {
                "description": "GUID for a runbook job",
                "control": "guid",
                "category": "Schedule"
            }
        },
        "startTime": {
            "type": "string",
            "metadata": {
                "description": "Time for starting the runbook.",
                "control": "datetime",
                "category": "Schedule"
            }
        }


Vous y faites référence aux valeurs de paramètre dans d’autres éléments de la solution avec la syntaxe de la **Paramètres ('nom paramètre')**.  Par exemple, pour accéder au nom de l’espace de travail, vous utiliseriez **parameters('workspaceName')** 

## <a name="variables"></a>Variables

L’élément **Variables** inclut les valeurs que vous souhaitez utiliser dans le reste de la solution de gestion.  Ces valeurs ne sont pas exposées à l’utilisateur de l’installation de la solution.  Ils sont conçus pour fournir l’auteur avec un seul emplacement dans lequel ils peuvent gérer des valeurs qui peuvent être utilisés plusieurs fois dans l’ensemble de la solution. Vous devez placer toutes les valeurs spécifiques à votre solution dans les variables par opposition à coder les dans l’élément de **ressources** .  Cela rend le code plus lisible et vous pouvez facilement modifier ces valeurs dans les versions ultérieures.

Voici un exemple d’un élément de **variables** avec des paramètres standard utilisés dans les solutions.

    "variables": { 
        "SolutionVersion": "1.1", 
        "SolutionPublisher": "Contoso", 
        "SolutionName": "My Solution",
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31"
    },

Vous y faites référence à des valeurs de variables via la solution avec la syntaxe de la **variables (nom variable)**.  Par exemple, pour accéder à la variable NomSolution, vous utiliseriez **variables('solutionName')** 


## <a name="resources"></a>Ressources

L’élément de **ressources** définit les différentes ressources incluses dans votre solution de gestion.  Il s’agit de la partie plus grande et les plus complexe du modèle.  Ressources sont définies avec la structure suivante.  

    "resources": [
        {
            "name": "<name-of-the-resource>",           
            "apiVersion": "<api-version-of-resource>",
            "type": "<resource-provider-namespace/resource-type-name>",     
            "location": "<location-of-resource>",
            "tags": "<name-value-pairs-for-resource-tagging>",
            "comments": "<your-reference-notes>",
            "dependsOn": [
                "<array-of-related-resource-names>"
            ],
            "properties": "<unique-settings-for-the-resource>",
            "resources": [
                "<array-of-child-resources>"
            ]
        }
    ]

### <a name="dependencies"></a>Dépendances
Les éléments **dependsOn** spécifie une [dépendance](../resource-group-define-dependencies.md) avec une autre ressource.  Après avoir installé la solution, une ressource n’est pas créée jusqu'à ce que toutes ses dépendances ont été créés.  Par exemple, votre solution peut [Démarrer un runbook](operations-management-suite-solutions-resources-automation.md#runbooks) lorsqu’il est installé à l’aide d’une [ressource de travail](operations-management-suite-solutions-resources-automation.md#automation-jobs).  La ressource de travail serait dépendante de la ressource runbook pour vous assurer que le runbook est créée avant que le travail est créé.

### <a name="oms-workspace-and-automation-account"></a>Espace de travail OMS et compte Automation
Gestion des solutions nécessitent un [espace de travail OMS](../log-analytics/log-analytics-manage-access.md) pour contenir des vues et un [compte Automation](../automation/automation-security-overview.md#automation-account-overview) contenir des procédures opérationnelles et les ressources associées.  Ils doivent être disponibles avant que les ressources de la solution sont créées et ne doivent pas être définis dans la solution elle-même.  L’utilisateur sera [spécifier un espace de travail et un compte](operations-management-suite-solutions.md#oms-workspace-and-automation-account) lorsqu’ils déploient votre solution, mais l’auteur vous devez prendre en compte les points suivants.


## <a name="solution-resource"></a>Ressources de solution
Chaque solution nécessite une entrée de la ressource dans l’élément de **ressources** qui définit la solution elle-même.  Cela aura un type de **Microsoft.OperationsManagement/solutions**.  Voici un exemple d’une ressource de la solution.  Ses différents éléments sont décrites dans les sections ci-dessous.

    "name": "[concat(variables('SolutionName'), '[ ' ,parameters('workspacename'), ' ]')]",
    "location": "[parameters('workspaceRegionId')]",
    "tags": { },
    "type": "Microsoft.OperationsManagement/solutions",
    "apiVersion": "[variables('LogAnalyticsApiVersion')]",
    "dependsOn": [
        "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('RunbookName'))]",
        "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/schedules/', variables('ScheduleName'))]",
        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/views/', variables('ViewName'))]"
    ]
    "properties": {
        "workspaceResourceId": "[concat(resourceGroup().id, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]",
        "referencedResources": [
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/schedules/', variables('ScheduleName'))]"
        ],
        "containedResources": [
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('RunbookName'))]",
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/views/', variables('ViewName'))]"
        ]
    },
    "plan": {
        "name": "[concat(variables('SolutionName'), '[' ,parameters('workspacename'), ']')]",
        "Version": "[variables('SolutionVersion')]",
        "product": "AzureSQLAnalyticSolution",
        "publisher": "[variables('SolutionPublisher')]",
        "promotionCode": ""
    }

### <a name="solution-name"></a>Nom de la solution
Le nom de la solution doit être unique dans votre abonnement Azure. La valeur recommandée à utiliser est la suivante.  Ceci utilise une variable appelée **NomSolution** pour le nom de base et le paramètre **workspaceName** pour vous assurer que le nom est unique.

    [concat(variables('SolutionName'), ' [' ,parameters('workspaceName'), ']')]

Cela serait résoudre à un nom semblable à celui-ci.

    My Solution Name [MyWorkspace]
 

### <a name="dependencies"></a>Dépendances
La ressource solution doit avoir une [dépendance](../resource-group-define-dependencies.md) sur toutes les autres ressources de la solution dans la mesure où ils doivent exister avant de la solution peut être créée.  Pour cela, en ajoutant une entrée pour chaque ressource dans l’élément **dependsOn** .


### <a name="properties"></a>Propriétés
La ressource solution possède les propriétés dans le tableau suivant.  Cela inclut les ressources référencées par et contenu la solution qui définit la gestion de la ressource après l’installation de la solution.  Chaque ressource dans la solution doit être répertorié dans la **referencedResources** ou la propriété **containedResources** .

| Propriété | Description |
|:--|:--|
| workspaceResourceId | ID de l’espace de travail OMS sous la forme * <Resource Group ID>/providers/Microsoft.OperationalInsights/workspaces/\<nom de l’espace de travail\>*. |
| referencedResources   | Liste des ressources de la solution qui ne doit pas être supprimé lorsque la solution est supprimée. |
| containedResources   | Liste des ressources de la solution qui doivent être supprimées lors de la solution est supprimée. |

L’exemple ci-dessus est pour une solution avec un runbook, une planification et la vue.  La planification et runbook sont *référencées* dans l’élément **Propriétés** afin qu’ils ne sont pas supprimées lorsque la solution est supprimée.  La vue est *contenue* afin qu’il est supprimé lorsque la solution est supprimée.


### <a name="plan"></a>Plan
L’entité de **plan** de la ressource solution possède les propriétés dans le tableau suivant. 

| Propriété | Description |
|:--|:--|
| nom          | Nom de la solution. |
| Version       | Version de la solution définie par l’auteur. |
| produit       | Chaîne unique pour identifier la solution. |
| Publisher     | Éditeur de la solution. |


## <a name="other-resources"></a>Autres ressources
Vous pouvez obtenir les détails et les exemples de ressources qui sont communes aux solutions de gestion dans les articles suivants.

- [Affichages et les tableaux de bord](operations-management-suite-solutions-resources-views.md)
- [Ressources Automation](operations-management-suite-solutions-resources-automation.md)

## <a name="testing-a-management-solution"></a>Test d’une solution de gestion
Avant de déployer votre solution de gestion, il est recommandé de tester à l’aide de [Test AzureRmResourceGroupDeployment](../resource-group-template-deploy.md#deploy-with-powershell).  Cela valide votre fichier solution et vous aider à qu'identifier les problèmes avant d’essayer de déployer.



## <a name="next-steps"></a>Étapes suivantes

- Découvrez les détails du [Gestionnaire de ressources Azure de création de modèles](../resource-group-authoring-templates.md).
- Rechercher des [Modèles de démarrage rapide d’Azure](https://azure.microsoft.com/documentation/templates) des exemples de différents modèles de gestionnaire de ressources.
- Afficher les détails pour [l’Ajout des affichages pour une solution de gestion](operations-management-suite-solutions-resources-views.md).
- Afficher les détails [d’automatisation de ressources supplémentaires pour une solution de gestion](operations-management-suite-solutions-resources-automation.md).
 