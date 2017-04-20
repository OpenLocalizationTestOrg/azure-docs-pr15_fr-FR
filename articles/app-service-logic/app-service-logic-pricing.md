<properties 
    pageTitle="Applications logique tarification | Microsoft Azure" 
    description="Détails sur le fonctionnement de tarifs dans les applications de logique" 
    authors="kevinlam1" 
    manager="dwrede" 
    editor="" 
    services="logic-apps" 
    documentationCenter=""/>

<tags
    ms.service="logic-apps"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article" 
    ms.date="10/12/2016"
    ms.author="klam"/>

# <a name="logic-apps-pricing-model"></a>Applications logique tarification

Azure applications logique vous permet de mettre à l’échelle et d’exécuter des flux de travail de l’intégration dans le cloud.  Vous trouverez ci-dessous plus d’informations sur la facturation et les prix des offres pour les applications logique.

## <a name="consumption-pricing"></a>Consommation tarifs

Nouvellement créé applications logique d’utiliser un plan de consommation. Avec le modèle de tarification consommation logique applications, vous payez uniquement ce que vous utilisez.  Applications logique ne sont pas limitées lorsque vous utilisez un plan de consommation.
Toutes les actions exécutées lors de l’exécution d’une instance de l’application logique sont limitées.

### <a name="what-are-action-executions"></a>Que sont les exécutions action ?

Chaque étape dans une définition de l’application logique est une action.  Cela inclut les déclencheurs, les étapes du flux de contrôle comme les conditions, étendues, pour chaque boucles, jusqu'à ce que boucles, appels aux liens et les appels aux actions natives.
Déclencheurs sont des actions simplement spéciales qui ont été conçues d’instanciation d’une nouvelle instance d’une application logique lorsqu’un événement particulier se produit.  Il existe un certain nombre de comportements différents pour les déclencheurs qui peuvent affecter la façon dont l’application logique est limitée.

-   **L’interrogation déclencheur** – ce déclencheur continuellement consulte un point de terminaison jusqu'à la réception d’un message répondant aux critères pour la création d’une nouvelle instance d’une application logique.  La fréquence d’interrogation peut être configuré dans le déclencheur dans le Concepteur d’applications logique.  Chaque demande d’interrogation, même si elle ne créer une nouvelle instance d’une application logique, comptera comme l’exécution d’une action.

-   **Webhook déclencheur** – ce déclencheur attend un client envoyer une demande sur un point de terminaison particulier.  Chaque requête envoyée au point de terminaison webhook est considérée comme l’exécution d’une action. La demande et le déclencheur HTTP Webhook sont les deux déclencheurs webhook.

-   **Déclencheur périodicité** – ce déclencheur crée une nouvelle instance de l’application logique en fonction de l’intervalle de périodicité configuré dans le déclencheur.  Par exemple, un déclencheur périodicité peut être configuré pour exécuter tous les 3 jours ou même toutes les minutes.

Exécutions du déclencheur peuvent être vues dans la carte de ressources logique applications dans la partie de l’historique de déclencheur.

Toutes les actions exécutées, il s’agissait de réussies ou sont limitées en tant que l’exécution d’une action.  Les actions qui ont été ignorées en raison d’une condition ne pas respectée ou qui n’a pas été s’exécuter, car l’application logique a été interrompu avant la saisie semi-automatique ne sont pas considérées comme exécutions action.

Les actions exécutées dans des boucles sont prises en compte par itération de la boucle.  Par exemple, une action unique dans une pour chaque itération de boucle dans une liste de 10 articles à compter en tant que le nombre d’éléments dans la liste (10) multipliée par le nombre d’actions dans des échanges (1) plus une pour l’ouverture de la boucle qui, dans cet exemple, serait (10 * 1) + 1 = 11 exécutions action.

Logique applications qui sont désactivés ne peut pas contenir des instances instanciés et par conséquent au moment où ils sont désactivés n'êtes pas obtenir facturée.  Être conscients qu’après avoir désactivé une application logique peut prendre un peu de temps pour les instances pour mettre en veille avant d’être complètement désactivée.

## <a name="app-service-plans"></a>Plans de services d’application

Application Service Plans sont n’est plus nécessaire pour créer une application logique.  Vous pouvez également faire référence un Plan de Service de l’application avec une application logique existant.  Applications logique précédemment créées avec un Plan de services d’application continuent à se comporte comme avant où, selon le plan choisie, sera prise limité après qu’un nombre d’exécutions quotidiennes est dépassé et ne sera pas facturé à l’aide de la jauge d’exécution d’action.

Plans de Service d’application et leurs exécutions action autorisée quotidienne :

| |Libérer/partagés/Basic|Standard|Premium|
|---|---|---|---|
|Exécutions action par jour| 200|10 000|50 000|

### <a name="convert-from-consumption-to-app-service-plan-pricing"></a>Convertir le Plan de Service d’application tarifs de consommation

Pour faire référence à un Plan de Service de l’application pour une application d’une logique de consommation, vous pouvez simplement [exécuter le script PowerShell ci-dessous](https://github.com/logicappsio/ConsumptionToAppServicePlan).  Vérifiez que vous devez commencer par les [Outils de PowerShell Azure](https://github.com/Azure/azure-powershell) sont installés.

``` powershell
Param(
    [string] $AppService_RG = '<app-service-resource-group>',
    [string] $AppService_Name = '<app-service-name>',
    [string] $LogicApp_RG = '<logic-app-resource-group>',
    [string] $LogicApp_Name = '<logic-app-name>',
    [string] $subscriptionId = '<azure-subscription-id>'
)

Login-AzureRmAccount 
$subscription = Get-AzureRmSubscription -SubscriptionId $subscriptionId
$appserviceplan = Get-AzureRmResource -ResourceType "Microsoft.Web/serverFarms" -ResourceGroupName $AppService_RG -ResourceName $AppService_Name
$logicapp = Get-AzureRmResource -ResourceType "Microsoft.Logic/workflows" -ResourceGroupName $LogicApp_RG -ResourceName $LogicApp_Name

$sku = @{
    "name" = $appservicePlan.Sku.tier;
    "plan" = @{
      "id" = $appserviceplan.ResourceId;
      "type" = "Microsoft.Web/ServerFarms";
      "name" = $appserviceplan.Name  
    }
}

$updatedProperties = $logicapp.Properties | Add-Member @{sku = $sku;} -PassThru

$updatedLA = Set-AzureRmResource -ResourceId $logicapp.ResourceId -Properties $updatedProperties -ApiVersion 2015-08-01-preview
```

### <a name="convert-from-app-service-plan-pricing-to-consumption"></a>Convertir à partir de l’application Service planifier tarification pour la consommation

Pour modifier une application logique qui contient un Service d’application Plan associé à un modèle de consommation supprimez la référence au Plan du Service application dans la définition de l’application logique.  Cela peut être exécuté avec un appel à une applet de commande PowerShell :

`Set-AzureRmLogicApp -ResourceGroupName ‘rgname’ -Name ‘wfname’ –UseConsumptionModel -Force`

## <a name="pricing"></a>Tarifs

Pour connaître les prix voir [Logique applications tarifs](https://azure.microsoft.com/pricing/details/logic-apps/).

## <a name="next-steps"></a>Étapes suivantes

- [Une vue d’ensemble des applications de logique][whatis]
- [Créer votre première application logique][create]

[pricing]: https://azure.microsoft.com/pricing/details/logic-apps/
[whatis]: app-service-logic-what-are-logic-apps.md
[create]: app-service-logic-create-a-logic-app.md

