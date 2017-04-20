<properties
    pageTitle="Gérer le stockage Azure à l’aide de Automation Azure"
    description="Découvrez comment le service Azure Automation peut être utilisé pour gérer le stockage Azure à l’échelle."
    services="storage, automation"
    documentationCenter=""
    authors="jodoglevy"
    manager="eamono"
    editor=""/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/23/2016"
    ms.author="jolevy"/>



#<a name="managing-azure-storage-using-azure-automation"></a>Gestion du stockage Azure à l’aide de Automation Azure

Ce guide vous présenter au service Azure Automation et la manière dont il peut être utilisé pour simplifier la gestion de vos objets BLOB Azure stockage, les tables et les files d’attente.


## <a name="what-is-azure-automation"></a>Qu’est Automation Azure ?

[Azure Automation](https://azure.microsoft.com/services/automation/) est un service d’Azure pour simplifier la gestion de cloud via automatisation des processus. Utiliser Automation Azure, longue, manuels, des erreurs et si vous effectuez souvent des tâches peuvent être automatisées pour accroître l’efficacité et la fiabilité et réduisent le temps de valeur pour votre organisation.

Automatisation Azure fournit un moteur d’exécution du flux de travail très fiable et hautement disponible qui s’adapte à vos besoins fur et à votre organisation. Dans Azure Automation, processus est à l’abri manuellement, en systèmes 3ème tiers, ou à intervalles réguliers afin que les tâches se produisent exactement quand nécessaire.

Réduire les frais opérationnelles et libérer de l’informatique / DevOps personnel pour vous concentrer sur travail qui ajoute entreprise valeur en déplaçant les tâches d’administration nuage pour être exécuté automatiquement par Automation Azure.


## <a name="how-can-azure-automation-help-manage-azure-storage"></a>Comment Automation Azure peut-il m’aider à gérer le stockage Azure ?

Stockage Azure peut être géré dans Azure Automation en utilisant les applets de commande PowerShell disponibles dans [Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx). Automatisation Azure comporte ces applets de commande PowerShell de stockage disponibles prêts à l’emploi, afin que vous puissiez effectuer tous vos objets blob, table et tâches de gestion de file d’attente au sein du service. Vous pouvez également associer ces applets de commande dans Azure Automation avec les applets de commande pour les autres services Azure, pour automatiser les tâches complexes au sein des services Azure et 3e partie systèmes.

La [Galerie de runbook Azure Automation](https://azure.microsoft.com/blog/2014/10/07/introducing-the-azure-automation-runbook-gallery/) contient une variété de procédures opérationnelles Communauté et de l’équipe de produit pour commencer à automatisation de la gestion du stockage Azure, les autres services Azure et 3e systèmes tiers. Procédures opérationnelles galerie sont les suivantes :

 * [Supprimer des objets BLOB de stockage Azure sont certains jours ancien à l’aide de Automation Services.](https://gallery.technet.microsoft.com/scriptcenter/Remove-Storage-Blobs-that-aae4b761)
 * [Télécharger un Blob à partir du stockage Azure](https://gallery.technet.microsoft.com/scriptcenter/a-Blob-from-Azure-Storage-6bc13745)
 * [Sauvegardez tous les disques pour une machine virtuelle Azure unique ou pour tous les ordinateurs virtuels dans un Service Cloud](https://gallery.technet.microsoft.com/scriptcenter/Backup-all-disks-for-a-ede940d5)


## <a name="next-steps"></a>Étapes suivantes

À présent que vous avez appris les notions de base d’Azure Automation et comment elle peut être utilisée pour gérer des objets BLOB Azure stockage, des tableaux et des files d’attente, suivez ces liens pour en savoir plus sur automatisation Azure.

Consultez le didacticiel Azure Automation [Création ou en important un runbook dans Azure Automation](../automation/automation-creating-importing-runbook.md).
 
