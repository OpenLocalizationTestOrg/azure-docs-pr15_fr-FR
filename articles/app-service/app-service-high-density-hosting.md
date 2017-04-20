<properties
    pageTitle="Haute densité d’hébergement service d’application Azure | Microsoft Azure"
    description="Haute densité d’hébergement service d’application Azure"
    authors="btardif"
    manager="wpickett"
    editor=""
    services="app-service\web"
    documentationCenter=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="10/24/2016"
    ms.author="byvinyal"/>

# <a name="high-density-hosting-on-azure-app-service"></a>Haute densité d’hébergement service d’application Azure

Lorsque vous utilisez le Service d’application, votre application est dissociée de la capacité attribuée par deux concepts :

- **L’Application :** Représente l’application et sa configuration pour l’exécution. Par exemple, il inclut la version de .NET que le runtime doit charger, les paramètres de l’application, etc..

- **Le Plan de services d’application :** Définit les caractéristiques de la capacité, ensemble des fonctionnalités disponibles et de localisation de l’application. Par exemple, caractéristiques peuvent être volumineux machine (quatre cœurs), quatre instances, fonctionnalités Premium d’Extrême-Orient aux États-Unis.

Une application est toujours liée à un plan de services d’application, mais un plan de services d’application peut fournir la capacité à une ou plusieurs applications.

Par conséquent, la plateforme souplesse pour isoler une application unique ou avoir plusieurs applications de partager des ressources en partageant un plan de services d’application.

Toutefois, lorsque plusieurs applications partagent un plan de services d’application, une instance de cette application s’exécute sur toutes les instances de ce plan de services d’application.

## <a name="per-app-scaling"></a>Par mise à l’échelle de l’application
*Par mise à l’échelle de l’application* est une fonctionnalité qui peut être activée au niveau du plan de Service d’application et puis utilisée par l’application.

Par application mise à l’échelle nuances une application sur le plan de services d’application qui héberge séparément. De cette façon, un plan de services d’application peut être configuré pour fournir des 10 instances, mais une application peut être définie pour s’adapter aux seules 5.

Le modèle de gestionnaire de ressources Azure suivant crée un plan de Service d’application qui est répartie à 10 instances et une application qui est configurée pour être utilisées par la mise à l’échelle de l’application et mettre à l’échelle à seulement 5 instances.

Le plan de services d’application est définissant la propriété **mise à l’échelle par site** vrai ( `"perSiteScaling": true`). L’application consiste à définir le **nombre de collaborateurs** à utiliser pour 5 (`"properties": { "numberOfWorkers": "5" }`).

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters":{
            "appServicePlanName": { "type": "string" },
            "appName": { "type": "string" }
         },
        "resources": [
        {
            "comments": "App Service Plan with per site perSiteScaling = true",
            "type": "Microsoft.Web/serverFarms",
            "sku": {
                "name": "S1",
                "tier": "Standard",
                "size": "S1",
                "family": "S",
                "capacity": 10
                },
            "name": "[parameters('appServicePlanName')]",
            "apiVersion": "2015-08-01",
            "location": "West US",
            "properties": {
                "name": "[parameters('appServicePlanName')]",
                "perSiteScaling": true
            }
        },
        {
            "type": "Microsoft.Web/sites",
            "name": "[parameters('appName')]",
            "apiVersion": "2015-08-01-preview",
            "location": "West US",
            "dependsOn": [ "[resourceId('Microsoft.Web/serverFarms', parameters('appServicePlanName'))]" ],
            "properties": { "serverFarmId": "[resourceId('Microsoft.Web/serverFarms', parameters('appServicePlanName'))]" },
            "resources": [ {
                    "comments": "",
                    "type": "config",
                    "name": "web",
                    "apiVersion": "2015-08-01",
                    "location": "West US",
                    "dependsOn": [ "[resourceId('Microsoft.Web/Sites', parameters('appName'))]" ],
                    "properties": { "numberOfWorkers": "5" }
             } ]
         }]
    }


## <a name="recommended-configuration-for-high-density-hosting"></a>Configuration recommandée pour l’hébergement de haute densité

Par mise à l’échelle de l’application est une fonctionnalité qui est activée dans les régions Azure publiques et environnements de Service d’application. Toutefois, la stratégie recommandée consiste à utiliser les environnements de Service d’application pour tirer parti de leurs fonctionnalités avancées et les plus grandes pools de capacité.  

Suivez ces étapes pour configurer haute densité d’hébergement pour vos applications :

1. Configurer l’environnement de Service d’application et choisissez un pool de travail s’efforce du scénario d’hébergement haute densité.

1. Créer un plan de Service d’application unique et échelle de manière à utiliser la capacité disponible sur le pool de travail.

1. Définir l’indicateur de mise à l’échelle par site true sur le plan de services d’application.

1. Nouveaux sites sont créées et affectées à cette offre de Service d’application avec la propriété **numberOfWorkers** la valeur **1**. À l’aide de cette configuration donne la plus grande densité possible sur ce pool de travail.

1. Le nombre de travailleurs peut être configuré indépendamment par site octroyer à des ressources supplémentaires selon vos besoins. Par exemple, un site forte utilisation ait défini une **numberOfWorkers** à **3** pour ont plus capacité de traitement de cette application, tandis que les sites de faible utilisation définissez **numberOfWorkers** **1**.
