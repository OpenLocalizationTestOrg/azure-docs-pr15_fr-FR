<properties
    pageTitle="Activer les diagnostics de Services de Cloud Azure à l’aide de PowerShell | Microsoft Azure"
    description="Apprenez à activer des diagnostics pour les services de cloud à l’aide de PowerShell"
    services="cloud-services"
    documentationCenter=".net"
    authors="Thraka"
    manager="timlt"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="adegeo"/>


# <a name="enable-diagnostics-in-azure-cloud-services-using-powershell"></a>Activer les diagnostics de Services de Cloud Azure à l’aide de PowerShell

Vous pouvez collecter des données diagnostics tels que des journaux d’applications, compteur de performance etc. à partir d’un Service Cloud à l’aide de l’extension Azure Diagnostics. Cet article décrit comment activer l’extension Azure Diagnostics pour un Service Cloud à l’aide de PowerShell.  Découvrez [comment installer et configurer Azure PowerShell](../powershell-install-configure.md) pour la configuration requise pour cet article.

## <a name="enable-diagnostics-extension-as-part-of-deploying-a-cloud-service"></a>Activer l’extension diagnostics dans le cadre du déploiement d’un Service Cloud

Cette approche de bonne pour le type d’intégration continue de scénarios où l’extension diagnostics peut être activée dans le cadre du déploiement du service cloud. Lorsque vous créez un nouveau déploiement Service Cloud, vous pouvez activer l’extension diagnostics en passant le paramètre *ExtensionConfiguration* à l’applet de commande [New-AzureDeployment](https://msdn.microsoft.com/library/azure/mt589089.aspx) . Le paramètre *ExtensionConfiguration* prend un tableau de configurations diagnostics pouvant être créées à l’aide de l’applet de commande [New-AzureServiceDiagnosticsExtensionConfig](https://msdn.microsoft.com/library/azure/mt589168.aspx) .

L’exemple suivant montre comment vous pouvez activer des diagnostics pour un service cloud avec une WebRole et WorkerRole chacun possédant une configuration diagnostics différents.

    $service_name = "MyService"
    $service_package = "CloudService.cspkg"
    $service_config = "ServiceConfiguration.Cloud.cscfg"
    $webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml"
    $workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

    $webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath
    $workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath

    New-AzureDeployment -ServiceName $service_name -Slot Production -Package $service_package -Configuration $service_config -ExtensionConfiguration @($webrole_diagconfig,$workerrole_diagconfig)

Si le fichier de configuration diagnostics spécifie un élément StorageAccount avec un nom de compte de stockage, l’applet de commande New-AzureServiceDiagnosticsExtensionConfig utilise automatiquement ce compte de stockage. Pour ce faire, le compte de stockage doit être placé dans le même abonnement en tant que le Service en nuage déployée.

À partir d’Azure SDK 2.6 à publier les fichiers de configuration d’extension générés par le MSBuild sortie cible inclura le nom de compte de stockage en fonction de la chaîne de configuration diagnostics spécifiée dans le fichier de configuration de service (.cscfg). Le script ci-dessous montre comment analyser les fichiers de configuration d’Extension de la sortie cible publier et lorsque vous déployez le service cloud, configurer extension diagnostics pour chaque rôle.

    $service_name = "MyService"
    $service_package = "C:\build\output\CloudService.cspkg"
    $service_config = "C:\build\output\ServiceConfiguration.Cloud.cscfg"

    #Find the Extensions path based on service configuration file
    $extensionsSearchPath = Join-Path -Path (Split-Path -Parent $service_config) -ChildPath "Extensions"

    $diagnosticsExtensions = Get-ChildItem -Path $extensionsSearchPath -Filter "PaaSDiagnostics.*.PubConfig.xml"
    $diagnosticsConfigurations = @()
    foreach ($extPath in $diagnosticsExtensions)
    {
    #Find the RoleName based on file naming convention PaaSDiagnostics.<RoleName>.PubConfig.xml
    $roleName = ""
    $roles = $extPath -split ".",0,"simplematch"
    if ($roles -is [system.array] -and $roles.Length -gt 1)
        {
        $roleName = $roles[1]
        $x = 2
        while ($x -le $roles.Length)
            {
               if ($roles[$x] -ne "PubConfig")
                {
                    $roleName = $roleName + "." + $roles[$x]
                }
                else
                {
                    break
                }
                $x++
            }
        $fullExtPath = Join-Path -path $extensionsSearchPath -ChildPath $extPath
        $diagnosticsconfig = New-AzureServiceDiagnosticsExtensionConfig -Role $roleName -DiagnosticsConfigurationPath $fullExtPath
        $diagnosticsConfigurations += $diagnosticsconfig
        }
    }
    New-AzureDeployment -ServiceName $service_name -Slot Production -Package $service_package -Configuration $service_config -ExtensionConfiguration $diagnosticsConfigurations

Visual Studio en ligne utilise une approche similaire pour automatisée deploymnts des Services en nuage avec l’extension diagnostics. Pour obtenir un exemple complet, consultez [Publier AzureCloudDeployment.ps1](https://github.com/Microsoft/vso-agent-tasks/blob/master/Tasks/AzureCloudPowerShellDeployment/Publish-AzureCloudDeployment.ps1) .

Si aucune StorageAccount a été spécifié dans la configuration des diagnostics, vous devez passer le paramètre StorageAccountName à l’applet de commande. Si le paramètre StorageAccountName est spécifié, l’applet de commande utilisera toujours le compte de stockage spécifié dans le paramètre et non celle qui est spécifiée dans le fichier de configuration de diagnostics.

Si le compte de stockage diagnostics se trouve dans un autre abonnement du service Cloud, vous devez passer explicitement dans les paramètres StorageAccountName et StorageAccountKey à l’applet de commande. Le paramètre StorageAccountKey n’est pas nécessaire lors du compte de stockage diagnostics se trouve dans le même abonnement, comme l’applet de commande peut automatiquement la requête et définir la valeur de clé lors de l’activation de l’extension de diagnostics. Toutefois, si le compte de stockage diagnostics se trouve dans un autre abonnement, l’applet de commande peut ne pas être en mesure d’obtenir la clé automatiquement et vous devez spécifier explicitement la clé via le paramètre StorageAccountKey.

    $webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
    $workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key


## <a name="enable-diagnostics-extension-on-an-existing-cloud-service"></a>Activer l’extension de diagnostic sur un Service Cloud existant

Vous pouvez utiliser l’applet de commande [Set-AzureServiceDiagnosticsExtension](https://msdn.microsoft.com/library/azure/mt589140.aspx) pour activer ou mettre à jour de la configuration des diagnostics sur un Service Cloud en cours d’exécution.


    $service_name = "MyService"
    $webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml"
    $workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

    $webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath
    $workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath

    Set-AzureServiceDiagnosticsExtension -DiagnosticsConfiguration @($webrole_diagconfig,$workerrole_diagconfig) -ServiceName $service_name


## <a name="get-current-diagnostics-extension-configuration"></a>Obtenir la configuration d’extension diagnostics en cours
Utiliser l’applet de commande [Get-AzureServiceDiagnosticsExtension](https://msdn.microsoft.com/library/azure/mt589204.aspx) pour obtenir la configuration d’un service cloud diagnostics.

    Get-AzureServiceDiagnosticsExtension -ServiceName "MyService"

## <a name="remove-diagnostics-extension"></a>Supprimer l’extension diagnostics
Pour désactiver les tests de diagnostic sur un service cloud, vous pouvez utiliser l’applet de commande [Supprimer AzureServiceDiagnosticsExtension](https://msdn.microsoft.com/library/azure/mt589183.aspx) .

    Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService"

Si vous avez activé l’extension diagnostics à l’aide de *Jeu AzureServiceDiagnosticsExtension* ou *Nouveau AzureServiceDiagnosticsExtensionConfig* sans le paramètre de *rôle* vous pouvez supprimer l’extension à l’aide de *Supprimer AzureServiceDiagnosticsExtension* sans le paramètre de *rôle* . Si le paramètre de *rôle* a été utilisé lors de l’activation de l’extension, il doit également être utilisé lors de la suppression de l’extension.

Pour supprimer l’extension diagnostics de chaque rôle individuel :

    Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService" -Role "WebRole"


## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir des conseils supplémentaires sur l’utilisation des diagnostics de Windows Azure et d’autres techniques pour résoudre les problèmes, voir [Activation des Diagnostics dans Azure Cloud Services et Machines virtuelles](cloud-services-dotnet-diagnostics.md).
- Le [Schéma de Configuration Diagnostics](https://msdn.microsoft.com/library/azure/dn782207.aspx) explique les différentes options de configurations xml pour l’extension diagnostics.
- Pour savoir comment activer l’extension de diagnostic pour les Machines virtuelles, voir [créer une machine virtuelle Windows avec la surveillance et les diagnostics à l’aide du modèle de gestionnaire de ressources Azure](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md)  
