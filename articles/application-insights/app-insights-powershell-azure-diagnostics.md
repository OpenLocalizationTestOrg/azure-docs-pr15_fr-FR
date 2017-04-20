<properties
    pageTitle="Utilisation de PowerShell pour la mise en Application Insights dans un Azure | Microsoft Azure"
    description="Automatiser la configuration des Diagnostics Azure au canal analyse de l’Application."
    services="application-insights"
    documentationCenter=".net"
    authors="sbtron"
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="11/17/2015"
    ms.author="awills"/>

# <a name="using-powershell-to-set-up-application-insights-for-an-azure-web-app"></a>Utilisation de PowerShell pour configurer Application perspectives pour une application web Azure

[Microsoft Azure](https://azure.com) peut être [configuré pour envoyer des Diagnostics Azure](app-insights-azure-diagnostics.md) [Visual Studio Application](app-insights-overview.md)analyse. Les diagnostics sont liés aux Services en nuage Azure et machines virtuelles Azure. Ils complément la télémétrie que vous envoyez à partir de l’application à l’aide de l’Application Insights SDK. Dans le cadre d’automatisation du processus de création de nouvelles ressources dans Azure, vous pouvez configurer des diagnostics à l’aide de PowerShell.

## <a name="azure-template"></a>Modèle Azure

Si l’application web est dans Azure et créez vos ressources à l’aide d’un modèle de gestionnaire de ressources Azure, vous pouvez configurer Application perspectives en ajoutant Ceci vers le nœud de ressources :

    {
      resources: [
        /* Create Application Insights resource */
        {
          "apiVersion": "2015-05-01",
          "type": "microsoft.insights/components",
          "name": "nameOfAIAppResource",
          "location": "centralus",
          "kind": "web",
          "properties": { "ApplicationId": "nameOfAIAppResource" },
          "dependsOn": [
            "[concat('Microsoft.Web/sites/', myWebAppName)]"
          ]
        }
       ]
     } 

* `nameOfAIAppResource`-un nom pour la ressource Application perspectives
* `myWebAppName`-l’id de l’application web


## <a name="enable-diagnostics-extension-as-part-of-deploying-a-cloud-service"></a>Activer l’extension diagnostics dans le cadre du déploiement d’un Service Cloud

La `New-AzureDeployment` applet de commande a un paramètre `ExtensionConfiguration`, qui prend un tableau de configurations diagnostics. Il peuvent être créés à l’aide de la `New-AzureServiceDiagnosticsExtensionConfig` applet de commande. Par exemple :

```ps

    $service_package = "CloudService.cspkg"
    $service_config = "ServiceConfiguration.Cloud.cscfg"
    $diagnostics_storagename = "myservicediagnostics"
    $webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml" 
    $workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

    $primary_storagekey = (Get-AzureStorageKey `
     -StorageAccountName "$diagnostics_storagename").Primary
    $storage_context = New-AzureStorageContext `
       -StorageAccountName $diagnostics_storagename `
       -StorageAccountKey $primary_storagekey

    $webrole_diagconfig = `
     New-AzureServiceDiagnosticsExtensionConfig `
      -Role "WebRole" -Storage_context $storageContext `
      -DiagnosticsConfigurationPath $webrole_diagconfigpath
    $workerrole_diagconfig = `
     New-AzureServiceDiagnosticsExtensionConfig `
      -Role "WorkerRole" `
      -StorageContext $storage_context `
      -DiagnosticsConfigurationPath $workerrole_diagconfigpath

    New-AzureDeployment `
      -ServiceName $service_name `
      -Slot Production `
      -Package $service_package `
      -Configuration $service_config `
      -ExtensionConfiguration @($webrole_diagconfig,$workerrole_diagconfig)

``` 

## <a name="enable-diagnostics-extension-on-an-existing-cloud-service"></a>Activer l’extension de diagnostic sur un Service Cloud existant

Sur un service existant, utilisez `Set-AzureServiceDiagnosticsExtension`.

```ps
 
    $service_name = "MyService"
    $diagnostics_storagename = "myservicediagnostics"
    $webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml" 
    $workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"
    $primary_storagekey = (Get-AzureStorageKey `
         -StorageAccountName "$diagnostics_storagename").Primary
    $storage_context = New-AzureStorageContext `
        -StorageAccountName $diagnostics_storagename `
        -StorageAccountKey $primary_storagekey

    Set-AzureServiceDiagnosticsExtension `
        -StorageContext $storage_context `
        -DiagnosticsConfigurationPath $webrole_diagconfigpath `
        -ServiceName $service_name `
        -Slot Production `
        -Role "WebRole" 
    Set-AzureServiceDiagnosticsExtension `
        -StorageContext $storage_context `
        -DiagnosticsConfigurationPath $workerrole_diagconfigpath `
        -ServiceName $service_name `
        -Slot Production `
        -Role "WorkerRole"
```

## <a name="get-current-diagnostics-extension-configuration"></a>Obtenir la configuration d’extension diagnostics en cours

```ps

    Get-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```


## <a name="remove-diagnostics-extension"></a>Supprimer l’extension diagnostics

```ps

    Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

Si vous avez activé l’extension diagnostics à l’aide de des options `Set-AzureServiceDiagnosticsExtension` ou `New-AzureServiceDiagnosticsExtensionConfig` sans le paramètre de rôle, vous pouvez supprimer l’extension à l’aide `Remove-AzureServiceDiagnosticsExtension` sans le paramètre de rôle. Si le paramètre de rôle a été utilisé lors de l’activation de l’extension, il doit également être utilisé lors de la suppression de l’extension.

Pour supprimer l’extension diagnostics de chaque rôle individuel :

```ps

    Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService" -Role "WebRole"
```


## <a name="see-also"></a>Voir aussi

* [Surveiller Azure Cloud Services applications avec des aperçus de l’Application](app-insights-cloudservices.md)
* [Envoyer les Diagnostics de Windows Azure Application analyse](app-insights-azure-diagnostics.md)
* [Automatiser les alertes de configuration](app-insights-powershell-alerts.md)

