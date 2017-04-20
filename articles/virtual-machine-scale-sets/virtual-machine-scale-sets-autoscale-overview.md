<properties
    pageTitle="Mise à l’échelle automatique et la machine virtuelle échelle jeux | Microsoft Azure"
    description="Découvrez comment utiliser de diagnostics et de ressources échelle ajuster automatiquement machines virtuelles dans un jeu d’échelle."
    services="virtual-machine-scale-sets"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machine-scale-sets"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="davidmu"/>

# <a name="automatic-scaling-and-virtual-machine-scale-sets"></a>Mise à l’échelle automatique et la machine virtuelle échelle jeux

Mise à l’échelle automatique des machines virtuelles dans un jeu d’échelle est créé ou supprimé des ordinateurs de la définir en tant que nécessaire pour correspondre à des exigences de performances. Au fur et à mesure du volume de travail, une application peut nécessiter des ressources supplémentaires pour lui permettre d’effectuer des tâches.

Mise à l’échelle automatique est un processus automatisé facilite des frais de gestion. En réduisant aérienne, vous n’avez pas besoin surveiller les performances du système en continu ou décider comment gérer les ressources. Mise à l’échelle est un processus élastique. Ressources complémentaires peuvent être ajoutés en tant que la charge augmente, mais en tant que demande diminue ressources peuvent être supprimés pour réduire les frais et mettre à jour les niveaux de performance.

Configurer la mise à l’échelle automatiques sur une échelle définie à l’aide d’un modèle de gestionnaire de ressources Azure, Azure PowerShell, Azure infrastructure du langage commun ou le portail Azure.

## <a name="set-up-scaling-by-using-resource-manager-templates"></a>Configurer la mise à l’échelle à l’aide de modèles de gestionnaire de ressources

Plutôt que de déploiement et la gestion de chaque ressource de votre application séparément, utiliser un modèle qui déploie toutes les ressources dans une seule opération coordonnée. Dans le modèle, les ressources d’application sont définies et les paramètres de déploiement sont spécifiés pour les différents environnements. Le modèle est constitué de JSON et expressions que vous pouvez utiliser pour créer des valeurs pour votre déploiement. Pour plus d’informations, consultez [Gestionnaire de ressources Azure de création de modèles](../resource-group-authoring-templates.md).

Dans le modèle, vous spécifiez l’élément capacité :

    "sku": {
      "name": "Standard_A0",
      "tier": "Standard",
      "capacity": 3
    },

Capacité identifie le nombre de machines virtuelles dans l’ensemble. Vous pouvez modifier manuellement la capacité en déployant un modèle avec une valeur différente. Si vous déployez un modèle pour modifier la capacité, vous pouvez inclure uniquement l’élément de référence (SKU) avec la capacité de mise à jour.

Modifier automatiquement la capacité de votre échelle définie à l’aide de la combinaison de la ressource autoscaleSettings et l’extension diagnostics.

### <a name="configure-the-azure-diagnostics-extension"></a>Configurer l’extension Diagnostics Azure

Mise à l’échelle automatique ne peut être effectué si la collecte de métriques réussit sur chaque machine virtuelle dans l’ensemble d’échelle. L’Extension de Diagnostics Azure fournit les fonctionnalités de surveillance et de diagnostic qui répond aux besoins de collection de sites métriques de la ressource échelle automatique. Vous pouvez installer l’extension dans le cadre du modèle de gestionnaire de ressources.

Cet exemple montre les variables qui sont utilisées dans le modèle pour configurer l’extension de diagnostics :

    "diagnosticsStorageAccountName": "[concat(parameters('resourcePrefix'), 'saa')]",
    "accountid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/', resourceGroup().name,'/providers/', 'Microsoft.Storage/storageAccounts/', variables('diagnosticsStorageAccountName'))]",
    "wadlogs": "<WadCfg> <DiagnosticMonitorConfiguration overallQuotaInMB=\"4096\" xmlns=\"http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration\"> <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter=\"Error\"/> <WindowsEventLog scheduledTransferPeriod=\"PT1M\" > <DataSource name=\"Application!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"Security!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"System!*[System[(Level = 1 or Level = 2)]]\" /></WindowsEventLog>",
    "wadperfcounter": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\"><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\Thread Count\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Thread Count\" locale=\"en-us\"/></PerformanceCounterConfiguration></PerformanceCounters>",
    "wadcfgxstart": "[concat(variables('wadlogs'),variables('wadperfcounter'),'<Metrics resourceId=\"')]",
    "wadmetricsresourceid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name ,'/providers/','Microsoft.Compute/virtualMachineScaleSets/',parameters('vmssName'))]",
    "wadcfgxend": "[concat('\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/><MetricAggregation scheduledTransferPeriod=\"PT1M\"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>')]"

Paramètres sont fournis lorsque le modèle est déployé. Dans cet exemple, le nom du compte de stockage de l’emplacement de stockage des données et le nom de l’ensemble d’échelle à partir de laquelle les données sont collectées sont fournis. Également dans cet exemple de Windows Server, uniquement le compteur de performance nombre de threads est collecté. Tous les compteurs de performance disponibles dans Windows ou Linux peuvent être utilisés pour collecter les informations de diagnostic et peuvent être incluses dans la configuration de l’extension.

Cet exemple illustre la définition de l’extension dans le modèle :

    "extensionProfile": {
      "extensions": [
        {
          "name": "Microsoft.Insights.VMDiagnosticsSettings",
          "properties": {
            "publisher": "Microsoft.Azure.Diagnostics",
            "type": "IaaSDiagnostics",
            "typeHandlerVersion": "1.5",
            "autoUpgradeMinorVersion": true,
            "settings": {
              "xmlCfg": "[base64(concat(variables('wadcfgxstart'),variables('wadmetricsresourceid'),variables('wadcfgxend')))]",
              "storageAccount": "[variables('diagnosticsStorageAccountName')]"
            },
            "protectedSettings": {
              "storageAccountName": "[variables('diagnosticsStorageAccountName')]",
              "storageAccountKey": "[listkeys(variables('accountid'), variables('apiVersion')).key1]",
              "storageAccountEndPoint": "https://core.windows.net"
            }
          }
        }
      ]
    }

Lorsque l’extension diagnostics s’exécute, les données sont collectées dans une table qui se trouve dans le compte de stockage que vous spécifiez. Dans la table WADPerformanceCounters, vous pouvez trouver les informations collectées :

![](./media/virtual-machine-scale-sets-autoscale-overview/ThreadCountBefore2.png)

### <a name="configure-the-autoscalesettings-resource"></a>Configurez la ressource autoScaleSettings

La ressource autoscaleSettings utilise ces informations à partir de l’extension diagnostics pour décider s’il faut augmenter ou diminuer le nombre de machines virtuelles dans l’ensemble d’échelle.

Cet exemple illustre la configuration de la ressource dans le modèle :

    {
      "type": "Microsoft.Insights/autoscaleSettings",
      "apiVersion": "2015-04-01",
      "name": "[concat(parameters('resourcePrefix'),'as1')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachineScaleSets/',parameters('vmSSName'))]"
      ],
      "properties": {
        "enabled": true,
        "name": "[concat(parameters('resourcePrefix'),'as1')]",
        "profiles": [
          {
            "name": "Profile1",
            "capacity": {
              "minimum": "1",
              "maximum": "10",
              "default": "1"
            },
            "rules": [
              {
                "metricTrigger": {
                  "metricName": "\\Process(_Total)\\Thread Count",
                  "metricNamespace": "",
                  "metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]",
                  "timeGrain": "PT1M",
                  "statistic": "Average",
                  "timeWindow": "PT5M",
                  "timeAggregation": "Average",
                  "operator": "GreaterThan",
                  "threshold": 650
                },
                "scaleAction": {
                  "direction": "Increase",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              },
              {
                "metricTrigger": {
                  "metricName": "\\Process(_Total)\\Thread Count",
                  "metricNamespace": "",
                  "metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]",
                  "timeGrain": "PT1M",
                  "statistic": "Average",
                  "timeWindow": "PT5M",
                  "timeAggregation": "Average",
                  "operator": "LessThan",
                  "threshold": 550
                },
                "scaleAction": {
                  "direction": "Decrease",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              }
            ]
          }
        ],
        "targetResourceUri": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]"
      }
    }

Dans l’exemple ci-dessus, deux règles sont créées pour la définition des mise à l’échelle des actions automatiques. La première règle définit l’action horizontale et la deuxième règle définit l’action d’échelle. Ces valeurs sont fournies dans les règles :

- **metricName** - cette valeur est différente de celle du compteur de performance que vous avez défini dans la variable wadperfcounter pour l’extension diagnostics. Dans l’exemple ci-dessus, le nombre de threads compteur est utilisé.  
- **metricResourceUri** - cette valeur est l’identificateur de ressources de l’ensemble d’échelle machine virtuelle. Cet identifiant contient le nom du groupe de ressources, le nom du fournisseur de ressources et le nom de l’échelle de la valeur à l’échelle.
- **timeGrain** – cette valeur est la précision des mesures qui sont collectées. Dans l’exemple précédent, les données sont collectées sur un intervalle d’une minute. Cette valeur est utilisée avec la durée.
- **statistique** – cette valeur détermine la façon dont les mesures sont combinées afin de s’adapter à l’action de mise à l’échelle automatique. Les valeurs possibles sont : moyenne, Min, Max.
- **durée** – cette valeur est l’intervalle de temps dans lequel les données de l’instance sont collectées. Il doit être comprise entre 5 minutes et 12 heures.
- **timeAggregation** – cette valeur détermine la façon dont les données sont collectées doivent être combinées au fil du temps. La valeur par défaut est moyenne. Les valeurs possibles sont : moyenne, Minimum, Maximum, dernier, Total, de nombre.
- l' **opérateur** – cette valeur est l’opérateur qui est utilisé pour comparer les données métriques et le seuil. Les valeurs possibles sont : est égal à, NotEquals, supérieur, GreaterThanOrEqual, LessThan, LessThanOrEqual.
- **seuil** – cette valeur est la valeur déclenchant l’action d’échelle. Veillez à fournir une différence suffisante entre le seuil de l’action horizontale et le seuil de l’action d’échelle. Si vous définissez les valeurs pour les mêmes, le système prévoit changements constants, qui empêche l’implémentation d’une action mise à l’échelle. Par exemple, les deux, à 600 threads définition dans l’exemple précédent ne fonctionne pas.
- **direction** – cette valeur détermine l’action qui est exécutée lorsque la valeur de seuil est atteint. Les valeurs possibles sont augmenter ou diminuer.
- **type** – cette valeur est le type d’action qui doit avoir lieu et doit être définie sur ChangeCount.
- **valeur** – cette valeur est le nombre de machines virtuelles qui sont ajoutés ou supprimés à partir du jeu d’échelle. Cette valeur doit être 1 ou supérieure.
- **ralentissement** – cette valeur est la quantité de délai d’attente depuis la dernière action mise à l’échelle avant la prochaine action se produit. Cette valeur doit être comprise entre une minute et une semaine.

Selon le compteur de performance que vous utilisez, certains des éléments dans la configuration du modèle sont utilisés différemment. Dans l’exemple précédent, le compteur de performance est le nombre de threads, la valeur de seuil est 650 pour une action horizontale et la valeur de seuil est 550 pour l’action d’échelle. Si vous utilisez un compteur, tels que % temps processeur, la valeur de seuil est définie pour le pourcentage de l’UC qui détermine une action de mise à l’échelle.

Lorsqu’un chargement est créé sur les ordinateurs virtuels déclenchant une action de mise à l’échelle, la capacité de l’ensemble est augmentée en fonction de la valeur dans le modèle. Par exemple, dans une échelle de jeu dont la capacité est définie sur 3 et la valeur d’action échelle est défini sur 1 :

![](./media/virtual-machine-scale-sets-autoscale-overview/ResourceExplorerBefore.png)

Lorsque le chargement est créé entraînant le nombre de threads moyenne accéder le seuil de 650 :

![](./media/virtual-machine-scale-sets-autoscale-overview/ThreadCountAfter.png)

Une action horizontale se déclenche entraînant la capacité de l’ensemble d’augmente d’un numéro :

    "sku": {
      "name": "Standard_A0",
      "tier": "Standard",
      "capacity": 4
    },

Et une machine virtuelle est ajoutée à l’ensemble d’échelle :

![](./media/virtual-machine-scale-sets-autoscale-overview/ResourceExplorerAfter.png)

Après une période de ralentissement de cinq minutes, si le nombre moyen de threads sur les ordinateurs reste plus de 600, un autre ordinateur est ajouté à l’ensemble. Si le nombre de threads moyenne demeure en dessous de 550, la capacité de l’ensemble d’échelle est réduite d’une unité et un ordinateur est supprimé de l’ensemble.

## <a name="set-up-scaling-using-azure-powershell"></a>Configurer la mise à l’échelle à l’aide de PowerShell Azure

Pour voir des exemples d’utilisation de PowerShell pour configurer autoscaling, recherchez-le sur [Azure moniteur PowerShell rapide démarrer exemples](../monitoring-and-diagnostics/insights-powershell-samples.md).

## <a name="set-up-scaling-using-azure-cli"></a>Configurer la mise à l’échelle à l’aide d’infrastructure du langage commun Azure

Pour voir des exemples d’utilisation Azure infrastructure du langage commun pour configurer autoscaling, recherchez-le sur [Azure moniteur disponibilité sur plusieurs plateformes infrastructure du langage commun rapide démarrer exemples](../monitoring-and-diagnostics/insights-cli-samples.md).

## <a name="set-up-scaling-using-the-azure-portal"></a>Configurer la mise à l’échelle à l’aide du portail Azure

Pour visualiser un exemple de l’aide du portail Azure configurer autoscaling, consultez [créer un ensemble d’échelle de Machine virtuelle à l’aide du portail Azure](virtual-machine-scale-sets-portal-create.md).

## <a name="investigate-scaling-actions"></a>Examinez les actions de mise à l’échelle

- [Portail azure]() - accessible actuellement limitée des informations à l’aide du portail.
- [Explorateur de Ressources azure]() - cet outil est la meilleure pour l’exploration de l’état actuel de votre jeu d’échelle. Suivez ce chemin d’accès et vous devriez voir la vue d’instance de l’ensemble d’échelle que vous avez créé : abonnements > {votre abonnement} > resourceGroups > {votre groupe de ressources} > fournisseurs > Microsoft.Compute > virtualMachineScaleSets > {votre jeu échelle} > desmachines virtuelles
- Azure PowerShell - Utilisez cette commande pour obtenir des informations :

        Get-AzureRmResource -name vmsstest1 -ResourceGroupName vmsstestrg1 -ResourceType Microsoft.Compute/virtualMachineScaleSets -ApiVersion 2015-06-15
        Get-Autoscalesetting -ResourceGroup rainvmss -DetailedOutput

- Se connecter à la machine virtuelle jumpbox comme vous le feriez pour toute autre machine et vous pouvez ensuite accéder à distance les machines virtuelles dans l’échelle défini pour contrôler les processus individuels.

## <a name="next-steps"></a>Étapes suivantes

- Consultez [mettre automatiquement à l’échelle machines dans un jeu de Machine virtuelle échelle](virtual-machine-scale-sets-windows-autoscale.md) pour voir un exemple montrant comment créer une échelle définie avec la mise à l’échelle automatique configuré.
- Rechercher des exemples d’Azure moniteur fonctionnalités d’analyse dans [Azure moniteur PowerShell rapide démarrer exemples](../monitoring-and-diagnostics/insights-powershell-samples.md)
- Obtenir des informations sur les fonctionnalités de notification [d’utiliser les actions d’échelle pour envoyer des messages électroniques et webhook notifications d’alerte dans le moniteur Azure](../monitoring-and-diagnostics/insights-autoscale-to-webhook-email.md).
- Découvrez comment [utiliser audit enregistre pour envoyer des messages électroniques et webhook notifications alertes moniteur d’Azure](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md)
- Découvrez les [scénarios échelle automatique avancées](./virtual-machine-scale-sets-advanced-autoscale.md).
