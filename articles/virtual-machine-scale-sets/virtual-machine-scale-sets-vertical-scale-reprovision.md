<properties
    pageTitle="Évolution verticale de jeux d’échelle Azure machine virtuelle | Microsoft Azure"
    description="Comment mettre à l’échelle verticalement une Machine virtuelle en réponse aux alertes avec Azure Automation de surveillance"
    services="virtual-machine-scale-sets"
    documentationCenter=""
    authors="gbowerman"
    manager="madhana"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machine-scale-sets"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-multiple"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/03/2016"
    ms.author="guybo"/>

# <a name="vertical-autoscale-with-virtual-machine-scale-sets"></a>Jeux d’échelle verticale avec une échelle de Machine virtuelle

Cet article décrit comment mettre à l’échelle verticalement Azure [Machine virtuelle échelle jeux](https://azure.microsoft.com/services/virtual-machine-scale-sets/) avec ou sans le réapprovisionnement. Pour échelle verticale de machines virtuelles qui ne sont pas dans les jeux d’échelle, reportez-vous à [l’échelle verticalement Azure machine virtuelle avec Azure Automation](../virtual-machines/virtual-machines-windows-vertical-scaling-automation.md).

Échelle verticale, également appelés _évoluer_ et _mettre à l’échelle vers le bas_, signifie croissantes ou décroissantes tailles machine virtuelle () en réponse à une charge de travail. Faites la comparaison avec [échelle horizontale](./virtual-machine-scale-sets-autoscale-overview.md), également appelées _horizontale_ et _échelle dans_, où le nombre de machines virtuelles est modifié en fonction de la charge de travail.

Effectuer un redéploiement signifie suppression d’un ordinateur virtuel existant et le remplacer par un autre. Lorsque vous augmentez ou diminuez la taille de machines virtuelles dans un jeu d’échelle de machine virtuelle, dans certains cas, vous souhaitez redimensionner machines virtuelles existantes et conserver vos données, tandis que dans d’autres cas, vous devez déployer des nouvelles machines virtuelles de la nouvelle taille. Ce document traite les deux cas.

Échelle verticale peut être utile lorsque :

- Un service basé sur des machines virtuelles est utilisé sous (par exemple en fin de semaine). Réduire la taille de la mémoire virtuelle peut réduire les coûts mensuels.
- Augmentation de la taille machine virtuelle résistant supérieure à la demande sans créer de machines virtuelles supplémentaires.

Vous pouvez configurer échelle verticale être déclenchées en fonction des alertes de base métriques à partir de votre machine virtuelle échelle définie. L’alerte est activé se déclenche un webhook déclenchant un runbook qui peut s’ajuster votre échelle définie vers le haut ou vers le bas. Échelle verticale peut être configurée en procédant comme suit :

1. Créez un compte Azure Automation avec exécuter en tant que fonctionnalité.
2. Importer des procédures opérationnelles échelle verticale Azure Automation pour les jeux d’échelle machine virtuelle dans votre abonnement.
3. Ajouter un webhook à votre runbook.
4. Ajouter une alerte à votre échelle machine virtuelle définie à l’aide d’une notification webhook.

> [AZURE.NOTE] Autoscaling vertical ne peut avoir lieu dans certaines séries de tailles de mémoire virtuelle. Comparer les spécifications de chaque taille avant de décider à l’échelle de l’un à l’autre (nombre supérieur n’indique pas toujours plus grande taille de la mémoire virtuelle). Vous pouvez choisir de mettre à l’échelle entre les paires de tailles suivantes :

>| Machine virtuelle tailles paire mise à l’échelle |   |
|---|---|
|  Standard_A0 | Standard_A11 |
|  Standard_D1 |  Standard_D14 |
|  Standard_DS1 |  Standard_DS14 |
|  Standard_D1v2 |  Standard_D15v2 |
|  Standard_G1 |  Standard_G5 |
|  Standard_GS1 |  Standard_GS5 |

## <a name="create-an-azure-automation-account-with-run-as-capability"></a>Créer un compte de Automation Azure avec exécuter en tant que fonctionnalité

La première chose à faire est de créer un compte Azure Automation hébergeant les procédures opérationnelles utilisée pour redéfinir les instances machine virtuelle échelle définie. Récemment [Azure Automation](https://azure.microsoft.com/services/automation/) ainsi que la fonctionnalité « Exécuter en tant que compte » qui facilite la configuration de l’entité de Service permettant d’exécuter les procédures opérationnelles en nom d’un utilisateur très simple. Vous pouvez en savoir plus sur ce dans l’article ci-dessous :

* [Authentifier procédures opérationnelles avec Azure exécuter en tant que compte](../automation/automation-sec-configure-azure-runas-account.md)

## <a name="import-azure-automation-vertical-scale-runbooks-into-your-subscription"></a>Importer des procédures opérationnelles Azure Automation échelle verticale dans votre abonnement

Les procédures opérationnelles nécessaires à l’échelle verticalement vos ensembles d’échelle machine virtuelle sont déjà publiées dans la galerie Azure Automation Runbook. Pour les importer dans votre abonnement suivre les étapes décrites dans cet article :

* [Galeries Runbook et module Azure Automation](../automation/automation-runbook-gallery.md)

Choisissez l’option Parcourir la galerie dans le menu de procédures opérationnelles :

![Procédures opérationnelles à importer][runbooks]

Les procédures opérationnelles qui doivent être importées sont affichées. Sélectionnez la procédure opérationnelle en fonction selon que vous voulez vertical mise à l’échelle avec ou sans le réapprovisionnement :

![Galerie de procédures opérationnelles][gallery]

## <a name="add-a-webhook-to-your-runbook"></a>Ajouter un webhook à votre runbook

Une fois que vous avez importé les procédures opérationnelles que vous devez ajouter un webhook à la runbook afin qu’il peut être déclenchée par une alerte à partir d’un ensemble d’échelle de machine virtuelle. Détails de la création d’un webhook pour votre Runbook sont décrites dans cet article :

* [Azure Automation webhooks](../automation/automation-webhooks.md)

> [AZURE.NOTE] Vérifiez que vous copiez la webhook URI avant de fermer la boîte de dialogue webhook comme vous en avez besoin dans la section suivante.

## <a name="add-an-alert-to-your-vm-scale-set"></a>Ajouter une alerte à votre machine virtuelle échelle définie

Vous trouverez ci-dessous un script PowerShell qui montre comment ajouter une alerte pour une machine virtuelle échelle définie. Consultez l’article suivant pour obtenir le nom de la mesure de déclencher l’alerte sur : [Moniteur Azure autoscaling courantes indicateurs](../monitoring-and-diagnostics/insights-autoscale-common-metrics.md).

```
$actionEmail = New-AzureRmAlertRuleEmail -CustomEmail user@contoso.com
$actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri <uri-of-the-webhook>
$threshold = <value-of-the-threshold>
$rg = <resource-group-name>
$id = <resource-id-to-add-the-alert-to>
$location = <location-of-the-resource>
$alertName = <name-of-the-resource>
$metricName = <metric-to-fire-the-alert-on>
$timeWindow = <time-window-in-hh:mm:ss-format>
$condition = <condition-for-the-threshold> # Other valid values are LessThanOrEqual, GreaterThan, GreaterThanOrEqual
$description = <description-for-the-alert>

Add-AzureRmMetricAlertRule  -Name  $alertName `
                            -Location  $location `
                            -ResourceGroup $rg `
                            -TargetResourceId $id `
                            -MetricName $metricName `
                            -Operator  $condition `
                            -Threshold $threshold `
                            -WindowSize  $timeWindow `
                            -TimeAggregationOperator Average `
                            -Actions $actionEmail, $actionWebhook `
                            -Description $description
```

> [AZURE.NOTE] Il est recommandé de configurer une fenêtre délai raisonnablement pour l’alerte afin d’éviter le déclenchement de la mise à l’échelle verticale et toute interruption de service associé, trop souvent. Envisagez d’une fenêtre d’au moins 20 à 30 minutes ou plus. Vous pouvez horizontal mise à l’échelle si vous avez besoin éviter toute interruption.

Pour plus d’informations sur la façon de créer des alertes, reportez-vous aux articles suivants :

* [Exemples de démarrage rapide de PowerShell moniteur Azure](../monitoring-and-diagnostics/insights-powershell-samples.md)
* [Exemples de démarrage rapide Azure infrastructure du langage commun disponibilité sur plusieurs plateformes moniteur](../monitoring-and-diagnostics/insights-cli-samples.md)

## <a name="summary"></a>Résumé

Cet article a montré des exemples de redimensionnement verticales simples. Avec ces blocs de construction - compte automatisation, procédures opérationnelles, webhooks, alertes - vous pouvez vous connecter une vaste gamme d’événements avec un ensemble d’actions personnalisée.

[runbooks]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks.png
[gallery]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks-gallery.png
