<properties
   pageTitle="Affichages dans des solutions de gestion Suite de gestion des opérations (OMS) | Microsoft Azure"
   description="Solutions de gestion dans Suite de gestion des opérations (OMS) inclura généralement un ou plusieurs affichages pour visualiser des données.  Cet article décrit comment exporter une vue créée par le Concepteur de vue et l’inclure dans une solution de gestion. "
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
   ms.date="10/17/2016"
   ms.author="bwren" />

# <a name="views-in-operations-management-suite-oms-management-solutions-preview"></a>Affichages dans des solutions de gestion Suite de gestion des opérations (OMS) (Preview)

>[AZURE.NOTE]Il s’agit d’une documentation préliminaire pour créer des solutions de gestion dans OMS qui sont actuellement en mode Aperçu avant. N’importe quel schéma décrit ci-dessous peut être modifiée.    

[Solutions de gestion dans Suite de gestion des opérations (OMS)](operations-management-suite-solutions.md) inclura généralement un ou plusieurs affichages pour visualiser des données.  Cet article décrit comment exporter une vue créée par le [Concepteur de vue](../log-analytics/log-analytics-view-designer.md) et l’inclure dans une solution de gestion.  

>[AZURE.NOTE]Les exemples de cet article utilisent les paramètres et les variables obligatoire ou communes aux solutions de gestion et décrites dans les [solutions de gestion de création dans Suite de gestion des opérations (OMS)](operations-management-suite-solutions-creating.md) 


## <a name="prerequisites"></a>Conditions préalables
Cet article suppose que vous connaissez déjà comment [créer une solution de gestion](operations-management-suite-solutions-creating.md) et la structure d’un fichier solution.


## <a name="overview"></a>Vue d’ensemble

Pour inclure un affichage dans une solution de gestion, vous créez une **ressource** pour qu’elle dans le [fichier de la solution](operations-management-suite-solutions-creating.md).  JSON qui décrit la configuration détaillée de la vue est généralement complexe bien qu’et pas quelque chose qu’un auteur de solution classique pourront créer manuellement.  La méthode la plus courante consiste à créer l’affichage à l’aide du [Concepteur de vue](../log-analytics/log-analytics-view-designer.md), exporter, puis ajoutez sa configuration détaillée à la solution. 

Voici les étapes de base pour ajouter une vue à une solution.  Chaque étape est décrit plus en détail dans les sections ci-dessous.

1. Exporter l’affichage vers un fichier.
2. Créer la ressource de l’affichage de la solution.
3. Ajoutez les détails de la vue.

## <a name="export-the-view-to-a-file"></a>Exporter l’affichage vers un fichier
Suivez les instructions au [Concepteur de vue Analytique journal](../log-analytics/log-analytics-view-designer.md) pour exporter un affichage dans un fichier.  Le fichier exporté sera au format JSON avec les mêmes [éléments que le fichier solution](operations-management-suite-solutions-creating.md#management-solution-files).  

L’élément de **ressources** du fichier affichage aura une ressource à un type de **Microsoft.OperationalInsights/workspaces** qui représente l’espace de travail OMS.  Cet élément a un sous-élément à un type d' **affichages** qui représente l’affichage et contient sa configuration détaillée.  Copiez les détails de cet élément et puis copiez-le dans votre solution.


## <a name="create-the-view-resource-in-the-solution"></a>Créer la ressource de l’affichage de la solution
Ajouter la ressource suivante de la vue à l’élément de **ressources** de votre fichier de solution.  Cet exemple utilise les variables qui sont décrites ci-dessous que vous devez également ajouter.  Notez que les propriétés du **tableau de bord** et **OverviewTile** sont des espaces réservés que vous allez remplacer avec les propriétés correspondantes à partir du fichier exporté affichage.
 
    {
        "apiVersion": "[variables('LogAnalyticsApiVersion')]",
        "name": "[concat(parameters('workspaceName'), '/', variables('ViewName'))]",
        "type": "Microsoft.OperationalInsights/workspaces/views",
        "location": "[parameters('workspaceregionId')]",
        "id": "[Concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'),'/views/', variables('ViewName'))]",
        dependson": [
            ],
        "properties": {
            "Id": "[variables('ViewName')]",
            "Name": "[variables('ViewName')]",
            "DisplayName": "[variables('ViewName')]",
            "Description": "",
            "Author": "[variables('ViewAuthor')]",
            "Source": "Local",
            "Dashboard": ,
            "OverviewTile": 
        }
    }

Ajoutez les variables suivantes à l’élément de [variables](operations-management-suite-solutions-creating.md#variables) du fichier solution et remplacer les valeurs à celles associées à votre solution.

    "LogAnalyticsApiVersion": "2015-11-01-preview",
    "ViewAuthor": "Your name."
    "ViewDescription": "Optional description of the view."
    "ViewName": "Provide a name for the view here."


Notez que vous pouvez copier la ressource de l’intégralité de l’affichage de votre fichier exporté vue, mais vous devez apporter les modifications suivantes pour qu’il fonctionne dans votre solution.  

- Le **type** de la ressource de la vue doit être changé de **vues** **Microsoft.OperationalInsights/workspaces**.
- La propriété **name** pour afficher la ressource doit être modifiée pour inclure le nom de l’espace de travail.
- La dépendance par rapport à l’espace de travail doit être supprimé dans la mesure où la ressource de l’espace de travail n’est pas définie dans la solution.
- Propriété **DisplayName** doit être ajoutée à l’affichage.  Tous les **Id**, **nom**et **DisplayName** doivent correspondre.
- Les noms de paramètres doivent être modifiées pour correspondre à l’ensemble des paramètres requis.
- Variables doivent être définis dans la solution et utilisées dans les propriétés appropriées.

## <a name="add-the-view-details"></a>Ajoutez les détails de la vue
La ressource d’affichage dans le fichier exporté affichage contiendra deux éléments dans l’élément de **Propriétés** nommé **tableau de bord** et **OverviewTile** qui contiennent la configuration de la vue détaillée.  Copiez ces deux éléments et leur contenu dans l’élément de **Propriétés** de la ressource d’affichage dans votre fichier solution. 

## <a name="example"></a>Exemple
Par exemple, l’exemple suivant montre un fichier de solution simple avec une vue.  Points de suspension (...) sont affichées pour le contenu de **tableau de bord** et **OverviewTile** pour des raisons de l’espace.


    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "workspaceName": {
                "type": "string"
            },
            "accountName": {
                "type": "string"
            },
            "workspaceRegionId": {
                "type": "string"
            },
            "regionId": {
                "type": "string"
            },
            "pricingTier": {
                "type": "string"
            }
        },
        "variables": {
            "SolutionVersion": "1.1",
            "SolutionPublisher": "Contoso",
            "SolutionName": "Contoso Solution",
            "LogAnalyticsApiVersion": "2015-11-01-preview",
            "ViewAuthor":  "user@contoso.com",
            "ViewDescription":  "This is a sample view.",
            "ViewName":  "Contoso View"
        },
        "resources": [
            {
                "name": "[concat(variables('SolutionName'), '(' ,parameters('workspacename'), ')')]",
                "location": "[parameters('workspaceRegionId')]",
                "tags": { },
                "type": "Microsoft.OperationsManagement/solutions",
                "apiVersion": "[variables('LogAnalyticsApiVersion')]",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspacename'), '/views/', variables('ViewName'))]"
                ],
                "properties": {
                    "workspaceResourceId": "[concat(resourceGroup().id, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspacename'))]",
                    "referencedResources": [
                    ],
                    "containedResources": [
                        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/views/', variables('ViewName'))]"
                    ]
                },
                "plan": {
                    "name": "[concat(variables('SolutionName'), '(' ,parameters('workspaceName'), ')')]",
                    "Version": "[variables('SolutionVersion')]",
                    "product": "ContosoSolution",
                    "publisher": "[variables('SolutionPublisher')]",
                    "promotionCode": ""
                }
            },
            {
                "apiVersion": "[variables('LogAnalyticsApiVersion')]",
                "name": "[concat(parameters('workspaceName'), '/', variables('ViewName'))]",
                "type": "Microsoft.OperationalInsights/workspaces/views",
                "location": "[parameters('workspaceregionId')]",
                "id": "[Concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'),'/views/', variables('ViewName'))]",
                "dependson": [
                ],
                "properties": {
                    "Id": "[variables('ViewName')]",
                    "Name": "[variables('ViewName')]",
                    "DisplayName": "[variables('ViewName')]",
                    "Description": "[variables('ViewDescription')]",
                    "Author": "[variables('ViewAuthor')]",
                    "Source": "Local",
                    "Dashboard": ...,
                    "OverviewTile": ...
                }
            }
        ]
    }




## <a name="next-steps"></a>Étapes suivantes

- Découvrez les détails complets de créer des [solutions de gestion](operations-management-suite-solutions-creating.md).
- Inclure des [procédures opérationnelles Automation dans votre solution de gestion](operations-management-suite-solutions-resources-automation.md).