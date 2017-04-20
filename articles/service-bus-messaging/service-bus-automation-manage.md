<properties
    pageTitle="Gérer les Bus des services Azure à l’aide de Automation Azure | Microsoft Azure"
    description="Découvrez comment utiliser le service Azure Automation pour gérer Bus des services Azure."
    services="service-bus, automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/29/2016"
    ms.author="magoedte;csand"/>

# <a name="managing-azure-service-bus-using-azure-automation"></a>Gestion des Bus des services Azure à l’aide de Automation Azure

Ce guide vous présenter au service Azure Automation et la manière dont il peut être utilisé pour simplifier la gestion de Bus des services Azure.

## <a name="what-is-azure-automation"></a>Qu’est Automation Azure ?

[Azure Automation](../automation/automation-intro.md) est un service d’Azure pour simplifier la gestion de cloud à l’aide automatisation des processus de configuration de l’état de votre choix. À l’aide de Automation Azure, manuel, répétée, longue et erreurs tâches peuvent être automatisées pour augmenter la fiabilité, l’efficacité et la valeur de durée pour votre organisation.

Automatisation Azure fournit un moteur d’exécution du flux de travail très fiable et hautement disponibles qui s’adapte à vos besoins. Dans Azure Automation, processus est à l’abri manuellement, en systèmes 3ème tiers, ou à intervalles réguliers afin que les tâches se produisent exactement quand nécessaire.

Réduire la surcharge opérationnelle et libérer de l’informatique et le personnel DevOps pour vous concentrer sur travail valeur ajoutée, en déplaçant les tâches d’administration nuage pour être exécuté automatiquement par Automation Azure.

## <a name="how-can-azure-automation-help-manage-azure-service-bus"></a>Comment Automation Azure peut-il m’aider à gérer Bus des services Azure ?

Vous pouvez gérer Bus des services avec Azure Automation à l’aide de l' [API REST de Bus Service](https://msdn.microsoft.com/library/azure/mt639375.aspx). Au sein d’automatisation Azure, vous pouvez exécuter des scripts PowerShell pour effectuer un grand nombre de vos tâches Bus des services à l’aide de l’API REST. Vous pouvez également associer ces appels API REST dans Azure Automation avec les applets de commande pour les autres services Azure, pour automatiser les tâches complexes au sein des services Azure et 3e partie systèmes.

Voici quelques exemples d’utilisation de PowerShell pour gérer Bus des services Azure :

* [Applets de commande PowerShell personnalisées pour gérer les files d’attente Bus des services Azure](https://blogs.technet.microsoft.com/uktechnet/2014/12/04/sample-of-custom-powershell-cmdlets-to-manage-azure-servicebus-queues)
* [Comment créer des files d’attente, les rubriques et les abonnements à l’aide d’un script PowerShell Bus des services](http://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
* [Créer des espaces de noms Bus de Service Azure à l’aide de PowerShell](http://buildazure.com/2015/09/24/create-azure-service-bus-namespaces-using-powershell-and-x-plat-cli/)

Le module PowerShell pour l’utilisation de bus des services Azure dans Automation procédures opérationnelles peut être téléchargé à partir de la [Galerie de PowerShell](https://www.powershellgallery.com/packages/AzureServiceBusCreation/1.0).


## <a name="next-steps"></a>Étapes suivantes

À présent que vous avez appris les notions de base d’Azure Automation et comment elle peut être utilisée pour gérer les Bus des services Azure, suivez ces liens pour en savoir plus sur automatisation Azure.

* Voir l’Automation Azure [didacticiels de mise en route](../automation/automation-first-runbook-graphical.md)
* Voir comment [gérer le Service Bus avec PowerShell](service-bus-powershell-how-to-provision.md)
