<properties 
    pageTitle="Script PowerShell pour créer une ressource d’Application perspectives" 
    description="Automatiser la création de ressources de l’analyse de l’Application." 
    services="application-insights" 
    documentationCenter="windows"
    authors="alancameronwills" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="02/19/2016" 
    ms.author="awills"/>

#  <a name="powershell-script-to-create-an-application-insights-resource"></a>Script PowerShell pour créer une ressource d’Application perspectives

*Analyse de l’application est en mode Aperçu.*

Lorsque vous voulez surveiller une nouvelle application - ou une nouvelle version d’une application - avec [Visual Studio Application perspectives](https://azure.microsoft.com/services/application-insights/), vous configurez une nouvelle ressource dans Microsoft Azure. Cette ressource est l’endroit où les données de télémétrie provenant de votre application sont analysées et affichées. 

Vous pouvez automatiser la création d’une nouvelle ressource à l’aide de PowerShell.

Par exemple, si vous développez une application d’appareil mobile, il est probable que, à tout moment, il sera plusieurs versions publiées de votre application en cours d’utilisation par vos clients. Vous ne voulez pas obtenir les résultats de télémétrie de différentes versions mixtes vers le haut. Vous disposez ainsi votre processus de génération pour créer une nouvelle ressource pour chaque build.

## <a name="script-to-create-an-application-insights-resource"></a>Script pour créer une ressource d’Application perspectives

Consultez les spécifications applet de commande pertinents :

* [Nouvelle AzureRmResource](https://msdn.microsoft.com/library/mt652510.aspx)
* [Nouvelle AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt678995.aspx)


*Script PowerShell*  

```PowerShell


###########################################
# Set Values
###########################################

# If running manually, uncomment before the first 
# execution to login to the Azure Portal:

# Add-AzureRmAccount

# Set the name of the Application Insights Resource

$appInsightsName = "TestApp"

# Set the application name used for the value of the Tag "AppInsightsApp" 
# - http://azure.microsoft.com/documentation/articles/azure-preview-portal-using-tags/
$applicationTagName = "MyApp"

# Set the name of the Resource Group to use.  
# Default is the application name.
$resourceGroupName = "MyAppResourceGroup"

###################################################
# Create the Resource and Output the name and iKey
###################################################

#Select the azure subscription
Select-AzureSubscription -SubscriptionName "MySubscription"

# Create the App Insights Resource

$resource = New-AzureRmResource `
  -ResourceName $appInsightsName `
  -ResourceGroupName $resourceGroupName `
  -Tag @{ Name = "AppInsightsApp"; Value = $applicationTagName} `
  -ResourceType "Microsoft.Insights/Components" `
  -Location "Central US" `
  -PropertyObject @{"Type"="ASP.NET"} `
  -Force

# Give owner access to the team

New-AzureRmRoleAssignment `
  -SignInName "myteam@fabrikam.com" `
  -RoleDefinitionName Owner `
  -Scope $resource.ResourceId 


#Display iKey
Write-Host "App Insights Name = " $resource.Name
Write-Host "IKey = " $resource.Properties.InstrumentationKey

```

## <a name="what-to-do-with-the-ikey"></a>Que faire avec l’iKey

Chaque ressource est identifié par sa clé instrumentation (iKey). L’iKey est une sortie du script de création d’une ressource. Votre script de compilation doit fournir que le iKey au SDK Insights Application incorporé dans votre application.

Il existe deux façons de rendre l’iKey disponibles pour le Kit de développement :
  
* Dans [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md): 
 * `<instrumentationkey>`*iKey*`</instrumentationkey>`
* Ou dans le [code de l’initialisation](app-insights-api-custom-events-metrics.md): 
 * `Microsoft.ApplicationInsights.Extensibility.
    TelemetryConfiguration.Active.InstrumentationKey = "`*iKey*`";`



## <a name="see-also"></a>Voir aussi

* [Créer des ressources de test web et analyse des applications à partir de modèles](app-insights-powershell.md)
* [Configurer la surveillance des diagnostics de Windows Azure avec PowerShell](app-insights-powershell-azure-diagnostics.md) 
* [Définir des alertes à l’aide de PowerShell](app-insights-powershell-alerts.md)

 