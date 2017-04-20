<properties
    pageTitle="Exemples de démarrage rapide de PowerShell moniteur Azure. | Microsoft Azure"
    description="Utiliser PowerShell pour accéder aux fonctionnalités Azure moniteur tels que l’échelle, les alertes, webhooks et recherche des journaux d’activité."
    authors="kamathashwin"
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
    ms.date="10/26/2016"
    ms.author="ashwink"/>

# <a name="azure-monitor-powershell-quick-start-samples"></a>Exemples de démarrage rapide de PowerShell moniteur Azure

Cet article explique les exemples de commandes PowerShell pour vous aider à accéder aux fonctionnalités moniteur Azure. Azure moniteur permet de Services de Cloud échelle, Machines virtuelles et applications Web et envoie des notifications ou appel URL web en fonction des valeurs de données de télémétrie configuré.

>[AZURE.NOTE] Moniteur Azure est le nouveau nom de ce qui était appelé « Perspectives Azure » jusqu'à 25 septembre 2016. Toutefois, les espaces de noms et donc les commandes ci-dessous contient toujours les « perspectives ».

## <a name="set-up-powershell"></a>Configurer la PowerShell
Si vous n’avez pas déjà, configurez PowerShell s’exécuter sur votre ordinateur. Pour plus d’informations, voir [comment installer et configurer PowerShell](../powershell-install-configure.md) .

## <a name="examples-in-this-article"></a>Exemples de cet article

Les exemples dans l’article illustrent comment vous pouvez utiliser les applets de commande Azure moniteur. Vous pouvez également consulter la liste complète des applets de commande PowerShell de moniteur Azure aux [Cmdlets Azure moniteur (perspectives)](https://msdn.microsoft.com/library/azure/mt282452#40v=azure.200#41.aspx).


## <a name="sign-in-and-use-subscriptions"></a>Se connecter et utiliser des abonnements

Tout d’abord, connectez-vous à votre abonnement Azure.

```PowerShell
Login-AzureRmAccount
```

Cette fonctionnalité nécessite de vous connecter. Une fois que vous effectuez, votre compte, TenantId et par défaut Id de l’abonnement s’affichent. Toutes les applets de commande Azure fonctionnent dans le cadre de votre abonnement par défaut. Pour afficher la liste des abonnements que vous avez accès, utilisez la commande suivante.

```PowerShell
Get-AzureRmSubscription
```

Pour modifier le contexte de travail dans un autre abonnement, utilisez la commande suivante.

```PowerShell
Set-AzureRmContext -SubscriptionId <subscriptionid>
```


## <a name="retrieve-audit-logs-for-a-subscription"></a>Récupérer les journaux d’Audit pour un abonnement
Utiliser la `Get-AzureRmLog` applet de commande.  Voici quelques exemples courants.

Obtenir les entrées du journal à partir de cette date et heure pour présenter :

```PowerShell
Get-AzureRmLog -StartTime 2016-03-01T10:30
```

Obtenir des entrées de journal entre une plage de date et heure :

```PowerShell
Get-AzureRmLog -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Obtenir les entrées du journal d’un groupe de ressources spécifique :

```PowerShell
Get-AzureRmLog -ResourceGroup 'myrg1'
```

Obtenir les entrées du journal à partir d’un fournisseur de ressources spécifique entre une plage de date et heure :

```PowerShell
Get-AzureRmLog -ResourceProvider 'Microsoft.Web' -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Obtenir toutes les entrées de journal à un appelant spécifique :

```PowerShell
Get-AzureRmLog -Caller 'myname@company.com'
```

La commande suivante extrait les 1000 derniers événements du journal d’audit :

```PowerShell
Get-AzureRmLog -MaxEvents 1000
```

`Get-AzureRmLog`prend en charge de nombreux autres paramètres. Voir la `Get-AzureRmLog` référence pour plus d’informations.

>[AZURE.NOTE] `Get-AzureRmLog`fournit uniquement des 15 jours d’historique. En utilisant le paramètre **- MaxEvents** permet des événements N dernières, au-delà de 15 jours de la requête. Événements access antérieurs à 15 jours, utilisez l’API REST ou kit de développement logiciel (exemple c# à l’aide du Kit de développement). Si vous n’incluez pas **l’heure de début**, la valeur par défaut est **l’heure de fin** moins une heure. Si vous n’incluez pas **l’heure de fin**, la valeur par défaut est heure actuelle. Toutes les heures sont au format UTC.

## <a name="retrieve-alerts-history"></a>Récupérer l’historique des alertes
Pour afficher tous les événements d’alerte, vous pouvez interroger les journaux Azure le Gestionnaire de ressources à l’aide de la procédure ci-après.

```PowerShell
Get-AzureRmLog -Caller "Microsoft.Insights/alertRules" -DetailedOutput -StartTime 2015-03-01
```

Pour afficher l’historique d’une règle d’alerte spécifique, vous pouvez utiliser la `Get-AzureRmAlertHistory` applet de commande, en passant l’ID de la ressource de la règle d’alerte.

```PowerShell
Get-AzureRmAlertHistory -ResourceId /subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/alertrules/myalert -StartTime 2016-03-1 -Status Activated
```

La `Get-AzureRmAlertHistory` applet de commande prend en charge les différents paramètres. Plus d’informations, voir [Get-AlertHistory](https://msdn.microsoft.com/library/mt282453.aspx).


## <a name="retrieve-information-on-alert-rules"></a>Récupérer des informations sur les règles d’alerte
Toutes les commandes suivantes s’appliquent à un groupe de ressources nommé « montest ».

Afficher toutes les propriétés de la règle d’alerte :

```PowerShell
Get-AzureRmAlertRule -Name simpletestCPU -ResourceGroup montest -DetailedOutput
```

Récupérer toutes les alertes sur un groupe de ressources :

```PowerShell
Get-AzureRmAlertRule -ResourceGroup montest
```

Récupérer toutes les règles d’alerte défini pour une ressource cible. Par exemple, toutes les règles d’alerte définie sur un ordinateur virtuel.

```PowerShell
Get-AzureRmAlertRule -ResourceGroup montest -TargetResourceId /subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig
```

`Get-AzureRmAlertRule`prend en charge des autres paramètres. Pour plus d’informations, voir [Get-AlertRule](https://msdn.microsoft.com/library/mt282459.aspx) .

## <a name="create-alert-rules"></a>Créer des règles d’alerte
Vous pouvez utiliser la `Add-AlertRule` applet de commande pour créer, mettre à jour ou désactiver une règle d’alerte.

Vous pouvez créer des propriétés de messagerie et webhook à l’aide de `New-AzureRmAlertRuleEmail` et `New-AzureRmAlertRuleWebhook`, respectivement. Dans l’applet de commande règle d’alerte, attribuer comme des actions à la propriété **Actions** de la règle d’alerte.

La section suivante contient un exemple qui vous montre comment créer une règle d’alerte avec des paramètres différents.

### <a name="alert-rule-on-a-metric"></a>Règle d’alerte sur une métrique
Le tableau suivant décrit les paramètres et les valeurs utilisées pour créer une alerte à l’aide d’une métrique.


|paramètre|valeur|
|---|---|
|Nom|  simpletestdiskwrite|
|Emplacement de cette règle d’alerte|   États-Unis Extrême-Orient|
|ResourceGroup| MonTest|
|TargetResourceId|  /Subscriptions/s1/resourceGroups/MonTest/Providers/Microsoft.Compute/virtualMachines/testconfig|
|MetricName de l’alerte est créé|   \PhysicalDisk (_Total) \Disk écritures/s. Voir la `Get-MetricDefinitions` applet de commande ci-dessous comment faire récupérer les noms de mesure exactes|
|opérateur|  Supérieur|
|Valeur de seuil (Nb/sec dans pour cette mesure)|    1|
|WindowSize (format hh : mm :)|  05:00:00|
|agrégation (statistique de la métrique, qui utilise le nombre de moyenne, dans ce cas)|  Moyenne|
|messages électroniques personnalisés (tableau de chaînes)|'foo@example.com','bar@example.com'|
|envoyer des messages électroniques pour les propriétaires, les collaborateurs et les lecteurs|    -SendToServiceOwners|

Créer une action de messagerie

```PowerShell
$actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
```

Créer une action Webhook

```PowerShell
$actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://example.com?token=mytoken
```

Créer la règle d’alerte sur le métrique % processeur sur un ordinateur virtuel classique

```PowerShell
Add-AzureRmMetricAlertRule -Name vmcpu_gt_1 -Location "East US" -ResourceGroup myrg1 -TargetResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.ClassicCompute/virtualMachines/my_vm1 -MetricName "Percentage CPU" -Operator GreaterThan -Threshold 1 -WindowSize 00:05:00 -TimeAggregationOperator Average -Actions $actionEmail, $actionWebhook -Description "alert on CPU > 1%"
```

Récupérer la règle d’alerte

```PowerShell
Get-AzureRmAlertRule -Name vmcpu_gt_1 -ResourceGroup myrg1 -DetailedOutput
```

L’applet de commande alerte ajouter met également à jour la règle s’il existe déjà une règle d’alerte pour les propriétés spécifiées. Pour désactiver une règle d’alerte, incluez le paramètre **- DisableRule**.

### <a name="alert-on-audit-log-event"></a>Avertir à propos de l’événement de journal d’audit

>[AZURE.NOTE] Cette fonctionnalité est toujours en mode Aperçu avant.

Dans ce scénario, vous allez envoyer messagerie lorsqu’un site Web est démarré dans mon abonnement dans groupe de ressources *abhingrgtest123*.

Configurer une règle de messagerie

```PowerShell
$actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
```

Configurer une règle webhook

```PowerShell
$actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://example.com?token=mytoken
```

Créer la règle sur l’événement

```PowerShell
Add-AzureRmLogAlertRule -Name superalert1 -Location "East US" -ResourceGroup myrg1 -OperationName microsoft.web/sites/start/action -Status Succeeded -TargetResourceGroup abhingrgtest123 -Actions $actionEmail, $actionWebhook
```

Récupérer la règle d’alerte

```PowerShell
Get-AzureRmAlertRule -Name superalert1 -ResourceGroup myrg1 -DetailedOutput
```

La `Add-AlertRule` applet de commande permet de divers autres paramètres. Plus d’informations, voir [Ajouter AlertRule](https://msdn.microsoft.com/library/mt282468.aspx).

## <a name="get-a-list-of-available-metrics-for-alerts"></a>Obtenir une liste des statistiques disponibles pour les alertes
Vous pouvez utiliser la `Get-AzureRmMetricDefinition` applet de commande pour afficher la liste de toutes les mesures pour une ressource spécifique.

```PowerShell
Get-AzureRmMetricDefinition -ResourceId <resource_id>
```

L’exemple suivant génère une table avec la métrique nom et l’unité de celui-ci.

```PowerShell
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Une liste complète des options disponibles pour `Get-AzureRmMetricDefinition` est disponible en [Get-MetricDefinitions](https://msdn.microsoft.com/library/mt282458.aspx).


## <a name="create-and-manage-autoscale-settings"></a>Créer et gérer les paramètres d’échelle
Une ressource, comme une application Web, machine virtuelle, Service Cloud ou machine virtuelle échelle définie peut avoir qu’un seul paramètre échelle configuré pour cela.
Toutefois, chaque paramètre échelle peut avoir plusieurs profils. Par exemple, un profil échelle en fonction des performances et une deuxième pour une planification en fonction de profil. Chaque profil peut avoir plusieurs règles configurés dessus. Pour plus d’informations sur échelle, voir [comment échelle une Application](../cloud-services/cloud-services-how-to-scale.md).

Voici les étapes que nous utiliserons :

1. Créer une ou plusieurs règles.
2. Créer des profils de mappage les règles que vous avez créée précédemment en utilisant les profils.
3. Facultatif : Créer des notifications pour échelle en configurant les propriétés webhook et la messagerie électronique.
4. Créer un paramètre d’échelle avec un nom de la ressource cible en mappant les profils et les notifications que vous avez créé lors des étapes précédentes.

Les exemples suivants montrent comment vous pouvez créer un paramètre d’échelle pour une échelle machine virtuelle définie pour un système d’exploitation Windows basé à l’aide de la mesure de l’utilisation du processeur.

Tout d’abord, créez une règle horizontale, avec une augmentation des Nb instance.

```PowerShell
$rule1 = New-AzureRmAutoscaleRule -MetricName "\Processor(_Total)\% Processor Time" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 0.01 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Increase -ScaleActionScaleType ChangeCount -ScaleActionValue 1
```     

Ensuite, créez une règle à échelle-in, avec une diminution de Nb instance.

```PowerShell
$rule2 = New-AzureRmAutoscaleRule -MetricName "\Processor(_Total)\% Processor Time" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 2 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Decrease -ScaleActionScaleType ChangeCount -ScaleActionValue 1
```

Ensuite, créez un profil pour les règles.

```PowerShell
$profile1 = New-AzureRmAutoscaleProfile -DefaultCapacity 2 -MaximumCapacity 10 -MinimumCapacity 2 -Rules $rule1,$rule2 -Name "My_Profile"
```

Créer une propriété webhook.

```PowerShell
$webhook_scale = New-AzureRmAutoscaleWebhook -ServiceUri "https://example.com?mytoken=mytokenvalue"
```

Créer la propriété de notification pour le paramètre échelle, y compris messagerie et le webhook que vous avez créé précédemment.

```PowerShell
$notification1= New-AzureRmAutoscaleNotification -CustomEmails ashwink@microsoft.com -SendEmailToSubscriptionAdministrators SendEmailToSubscriptionCoAdministrators -Webhooks $webhook_scale
```

Enfin, créez le paramètre échelle automatique pour ajouter le profil que vous avez créé précédemment.

```PowerShell
Add-AzureRmAutoscaleSetting -Location "East US" -Name "MyScaleVMSSSetting" -ResourceGroup big2 -TargetResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -AutoscaleProfiles $profile1 -Notifications $notification1
```

Pour plus d’informations sur la gestion des paramètres d’échelle, voir [Get-AutoscaleSetting](https://msdn.microsoft.com/library/mt282461.aspx).

## <a name="autoscale-history"></a>Historique de l’échelle
L’exemple suivant vous montre comment vous pouvez afficher échelle récente et événements d’alerte. Utilisez la recherche du journal d’audit pour afficher l’historique d’échelle.

```PowerShell
Get-AzureRmLog -Caller "Microsoft.Insights/autoscaleSettings" -DetailedOutput -StartTime 2015-03-01
```

Vous pouvez utiliser la `Get-AzureRmAutoScaleHistory` applet de commande pour récupérer l’historique d’échelle.

```PowerShell
Get-AzureRmAutoScaleHistory -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/microsoft.insights/autoscalesettings/myScaleSetting -StartTime 2016-03-15 -DetailedOutput
```

Pour plus d’informations, voir [Get-AutoscaleHistory](https://msdn.microsoft.com/library/mt282464.aspx).

### <a name="view-details-for-an-autoscale-setting"></a>Afficher les détails d’un paramètre d’échelle
Vous pouvez utiliser la `Get-Autoscalesetting` applet de commande pour récupérer des informations sur le paramètre d’échelle.

L’exemple suivant montre des détails sur tous les paramètres d’échelle dans le groupe de ressources « myrg1 ».

```PowerShell
Get-AzureRmAutoscalesetting -ResourceGroup myrg1 -DetailedOutput
```

L’exemple suivant montre des détails sur tous les paramètres d’échelle dans le groupe de ressources « myrg1 » et spécifiquement le paramètre échelle nommé « MyScaleVMSSSetting ».

```PowerShell
Get-AzureRmAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting -DetailedOutput
```

### <a name="remove-an-autoscale-setting"></a>Supprimer un paramètre d’échelle
Vous pouvez utiliser la `Remove-Autoscalesetting` applet de commande pour supprimer un paramètre d’échelle.

```PowerShell
Remove-AzureRmAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting
```

## <a name="manage-log-profiles-for-audit-logs"></a>Gérer les profils de journal pour les journaux d’audit

Vous pouvez créer un *profil de journal* et exporter des données à partir de vos journaux d’audit à un compte de stockage et vous pouvez configurer la rétention des données de celui-ci. Si vous le souhaitez, vous pouvez également flux de données à votre plateforme d’événement. Notez que cette fonctionnalité est actuellement en aperçu et vous ne pouvez créer un profil de journal par abonnement. Vous pouvez utiliser les applets de commande avec votre abonnement actuel pour créer et gérer les profils de journal. Vous pouvez également choisir un abonnement spécifique. Bien que PowerShell paramètres par défaut à l’abonnement actuel, vous pouvez toujours modifier que l’utilisation `Set-AzureRmContext`. Vous pouvez configurer les journaux d’audit pour acheminer des données sur un compte de stockage ou un événement concentrateur de cet abonnement. Les données sont écrites en tant que fichiers blob au format JSON.

### <a name="get-a-log-profile"></a>Obtenir un profil de journal
Pour récupérer vos profils journal existant, utilisez la `Get-AzureRmLogProfile` applet de commande.

### <a name="add-a-log-profile-without-data-retention"></a>Ajouter un profil de journal sans conservation des données

```PowerShell
Add-AzureRmLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia
```

### <a name="remove-a-log-profile"></a>Supprimer un profil de journal

```PowerShell
Remove-AzureRmLogProfile -name my_log_profile_s1
```

### <a name="add-a-log-profile-with-data-retention"></a>Ajouter un profil de journal avec conservation des données

Vous pouvez spécifier la propriété **- RetentionInDays** avec le nombre de jours, sous la forme d’un nombre entier positif, où les données sont conservées.

```PowerShell
Add-AzureRmLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

### <a name="add-log-profile-with-retention-and-eventhub"></a>Ajouter un profil de journal avec rétention et EventHub
Outre le routage de vos données au compte de stockage, vous pouvez également le flux à un concentrateur de l’événement. Notez que dans cette version préliminaire et le stockage de configuration du compte est obligatoire mais configuration événement Hub est facultative.

```PowerShell
Add-AzureRmLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

## <a name="configure-diagnostics-logs"></a>Configurer les journaux des diagnostics
De nombreux services Azure fournissent des journaux supplémentaires et télémétrie, y compris les groupes de sécurité Azure réseau, programmes d’équilibrage de charge logiciel, l’archivage sécurisé clé, Azure des Services de recherche, et applications logique et ils peuvent être configurés pour enregistrer les données dans votre compte de stockage Azure. Cette opération ne peut être effectuée à un niveau de ressources et du compte de stockage doit être présent dans la même région en tant que la ressource cible où le paramètre diagnostics est configuré.

### <a name="get-diagnostic-setting"></a>Obtenir les paramètres de diagnostic

```PowerShell
Get-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp
```

Désactiver le paramètre des Diagnostics

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $false
```

Activer le paramètre de diagnostic sans rétention

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true
```

Activer le paramètre de diagnostic avec rétention

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

Activer le paramètre de diagnostic avec rétention pour une catégorie de journal spécifique

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/sakteststorage -Categories NetworkSecurityGroupEvent -Enable $true -RetentionEnabled $true -RetentionInDays 90
```
