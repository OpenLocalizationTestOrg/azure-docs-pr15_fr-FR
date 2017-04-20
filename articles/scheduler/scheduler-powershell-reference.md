<properties
 pageTitle="Référence sur les applets de commande PowerShell Scheduler"
 description="Référence sur les applets de commande PowerShell Scheduler"
 services="scheduler"
 documentationCenter=".NET"
 authors="derek1ee"
 manager="kevinlam1"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.date="08/18/2016"
 ms.author="deli"/>

# <a name="scheduler-powershell-cmdlets-reference"></a>Référence sur les applets de commande PowerShell Scheduler

Le tableau suivant décrit et des liens vers la page de référence de chacun des applets de commande principales dans le planificateur Azure.

Pour installer PowerShell Azure et associer à votre abonnement Azure, Découvrez [comment installer et configurer Azure PowerShell](../powershell-install-configure.md). 

Pour plus d’informations sur les [applets de commande Gestionnaire de ressources Azure](https://msdn.microsoft.com/library/mt125356\(v=azure.200\).aspx), voir [Utilisation de PowerShell Azure avec le Gestionnaire de ressources Azure](../powershell-azure-resource-manager.md).

|Applet de commande|Description de l’applet de commande|
|---|---|
[Désactiver AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490133\(v=azure.200\).aspx) |Désactive une collection de sites de projet. 
[Activer AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490135\(v=azure.200\).aspx) |Permet à une collection de travail.
[Get-AzureRmSchedulerJob](https://msdn.microsoft.com/library/mt490125\(v=azure.200\).aspx) |Obtient les travaux du planificateur.
[Get-AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490132\(v=azure.200\).aspx) |Obtient la tâche collections de sites.
[Get-AzureRmSchedulerJobHistory](https://msdn.microsoft.com/library/mt490126\(v=azure.200\).aspx) |Obtient l’historique des travaux.
[Nouvelle AzureRmSchedulerHttpJob](https://msdn.microsoft.com/library/mt490136\(v=azure.200\).aspx) |Crée une tâche HTTP.
[Nouvelle AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490141\(v=azure.200\).aspx) |Crée une collection de travail.
[Nouvelle AzureRmSchedulerServiceBusQueueJob](https://msdn.microsoft.com/library/mt490134\(v=azure.200\).aspx) |Crée une tâche service bus file d’attente.
[Nouvelle AzureRmSchedulerServiceBusTopicJob](https://msdn.microsoft.com/library/mt490142\(v=azure.200\).aspx) |Crée une tâche service bus rubrique.
[Nouvelle AzureRmSchedulerStorageQueueJob](https://msdn.microsoft.com/library/mt490127\(v=azure.200\).aspx) |Crée une tâche de file d’attente de stockage. 
[Supprimer AzureRmSchedulerJob](https://msdn.microsoft.com/library/mt490140\(v=azure.200\).aspx) |Supprime un travail de planificateur.  
[Supprimer AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490131\(v=azure.200\).aspx) |Permet de supprimer une collection de sites de projet. 
[Jeu de AzureRmSchedulerHttpJob](https://msdn.microsoft.com/library/mt490130\(v=azure.200\).aspx) |Modifie une tâche de planificateur HTTP.
[Jeu de AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490129\(v=azure.200\).aspx) |Modifie une collection de sites de projet. 
[Jeu de AzureRmSchedulerServiceBusQueueJob](https://msdn.microsoft.com/library/mt490143\(v=azure.200\).aspx) |Modifie une tâche de file d’attente de bus de service.  
[Jeu de AzureRmSchedulerServiceBusTopicJob](https://msdn.microsoft.com/library/mt490137\(v=azure.200\).aspx) |Modifie un travail rubrique bus du service. 
[Jeu de AzureRmSchedulerStorageQueueJob](https://msdn.microsoft.com/library/mt490128\(v=azure.200\).aspx) |Modifie une opération de file d’attente de stockage.   

Pour plus d’informations, vous pouvez exécuter les applets de commande suivantes : 

```
Get-Help <cmdlet name> -Detailed
```
```
Get-Help <cmdlet name> -Examples
```
```
Get-Help <cmdlet name> -Full
```

## <a name="see-also"></a>Voir aussi


 [Quel est le planificateur ?](scheduler-intro.md)

 [Azure Scheduler concepts, terminologie et hiérarchie d’entités](scheduler-concepts-terms.md)

 [Commencer à utiliser le planificateur dans le portail Azure](scheduler-get-started-portal.md)

 [Les offres et facturation dans le planificateur d’Azure](scheduler-plans-billing.md)

 [Référence de l’API REST Scheduler Azure](https://msdn.microsoft.com/library/mt629143)

 [Azure Scheduler disponibilité et fiabilité](scheduler-high-availability-reliability.md)

 [Limites de planificateur Azure, les valeurs par défaut et codes d’erreur](scheduler-limits-defaults-errors.md)

 [Authentification sortante Scheduler Azure](scheduler-outbound-authentication.md)
