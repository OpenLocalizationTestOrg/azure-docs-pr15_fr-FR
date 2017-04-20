<properties
    pageTitle="Gérer les Services de Cloud Azure à l’aide de Automation Azure | Microsoft Azure"
    description="Découvrez comment le service Azure Automation peut être utilisé pour gérer les services de cloud Azure à l’échelle."
    services="cloud-services, automation"
    documentationCenter=""
    authors="jodoglevy"
    manager="timlt"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/20/2016"
    ms.author="jolevy"/>



#<a name="managing-azure-cloud-services-using-azure-automation"></a>Gestion des Services de Cloud Azure à l’aide de Automation Azure

Ce guide vous présenter au service Azure Automation et la manière dont il peut être utilisé pour simplifier la gestion de vos services cloud Azure.

## <a name="what-is-azure-automation"></a>Qu’est Automation Azure ?

[Azure Automation](https://azure.microsoft.com/services/automation/) est un service d’Azure pour simplifier la gestion de cloud via automatisation des processus. Utiliser l’Automation Azure, longue, manuels, des erreurs et si vous effectuez souvent des tâches peuvent être automatisées pour augmenter la fiabilité, l’efficacité et la valeur de durée pour votre organisation.

Automatisation Azure fournit un moteur d’exécution du flux de travail très fiable et hautement disponible qui s’adapte à vos besoins fur et à votre organisation. Dans Azure Automation, processus pouvant déconnectés manuellement, 3e partie systèmes ou à intervalles réguliers afin que les tâches se produisent exactement quand nécessaire.

Réduire les frais opérationnelles et libérer de l’informatique / DevOps personnel pour vous concentrer sur travail qui ajoute entreprise valeur en déplaçant les tâches d’administration nuage pour être exécuté automatiquement par Automation Azure.


## <a name="how-can-azure-automation-help-manage-azure-cloud-services"></a>Comment Automation Azure peut-il m’aider à gérer les services cloud Azure ?

Services de cloud Azure peuvent être gérés dans Azure Automation en utilisant les applets de commande PowerShell disponibles dans les [Outils de PowerShell Azure](https://msdn.microsoft.com/library/azure/jj156055.aspx). Automatisation Azure comporte ces cloud service applets de commande PowerShell disponibles prêts à l’emploi, afin que vous puissiez effectuer de toutes vos tâches de gestion du service cloud au sein du service. Vous pouvez également associer ces applets de commande dans Azure Automation avec les applets de commande pour d’autres services Azure, pour automatiser les tâches complexes dans plusieurs services Azure et 3ème systèmes tiers.

Quelques exemples d’utilisation de l’automatisation Azure pour gérer les Services de Cloud Azure sont les suivantes :

- [Déploiement continue d’un Service Cloud si cscfg ou cspkg est mis à jour dans le stockage Blob Azure](https://gallery.technet.microsoft.com/scriptcenter/Continuous-Deployment-of-A-eeebf3a6)
- [Redémarrage d’instances de Service Cloud en parallèle, un domaine de mise à niveau à la fois](https://gallery.technet.microsoft.com/scriptcenter/Reboot-Cloud-Service-PaaS-b337a06d)

## <a name="next-steps"></a>Étapes suivantes

À présent que vous avez appris les notions de base d’Azure Automation et comment elle peut être utilisée pour gérer les services de cloud Azure, suivez ces liens pour en savoir plus sur automatisation Azure.

- [Vue d’ensemble de Automation Azure](../automation/automation-intro.md)
- [Mon runbook première](../automation/automation-first-runbook-graphical.md)
- [Carte d’apprentissage Automation Azure](https://azure.microsoft.com/documentation/learning-paths/automation/)
 
