<properties
    pageTitle="Gérer les bases de données SQL Azure à l’aide de Automation Azure | Microsoft Azure"
    description="Découvrez comment le service Azure Automation peut être utilisé pour gérer les bases de données SQL Azure à l’échelle."
    services="sql-database, automation"
    documentationCenter=""
    authors="jodoglevy"
    manager="jhubbard"
    editor="monicar"/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/26/2016"
    ms.author="jolevy"/>



#<a name="managing-azure-sql-databases-using-azure-automation"></a>Gestion des bases de données SQL Azure à l’aide de Automation Azure

Ce guide vous présenter au service Azure Automation et la manière dont il peut être utilisé pour simplifier la gestion de vos bases de données SQL Azure.


## <a name="what-is-azure-automation"></a>Qu’est Automation Azure ?

[Azure Automation](https://azure.microsoft.com/services/automation/) est un service d’Azure pour simplifier la gestion de cloud via automatisation des processus. Utiliser l’Automation Azure, longue, manuels, des erreurs et si vous effectuez souvent des tâches peuvent être automatisées pour augmenter la fiabilité, l’efficacité et la valeur de durée pour votre organisation.

Automatisation Azure fournit un moteur d’exécution du flux de travail très fiable et hautement disponible qui s’adapte à vos besoins fur et à votre organisation. Dans Azure Automation, processus est à l’abri manuellement, en systèmes 3ème tiers, ou à intervalles réguliers afin que les tâches se produisent exactement quand nécessaire.

Réduire les frais opérationnelles et libérer de l’informatique / DevOps personnel pour vous concentrer sur travail qui ajoute entreprise valeur en déplaçant les tâches d’administration nuage pour être exécuté automatiquement par Automation Azure.


## <a name="how-can-azure-automation-help-manage-azure-sql-databases"></a>Comment Automation Azure peut-il m’aider à gérer les bases de données SQL Azure ?

Base de données SQL Azure peuvent être géré dans Azure Automation en utilisant les [applets de commande PowerShell de base de données SQL Azure](https://msdn.microsoft.com/library/dn546723.aspx) qui sont disponibles dans les [Outils de PowerShell Azure](https://msdn.microsoft.com/library/azure/jj156055.aspx). Automatisation Azure comporte ces applets de commande PowerShell de base de données SQL Azure disponibles prêts à l’emploi, afin que vous puissiez effectuer de toutes vos tâches de gestion de base de données SQL au sein du service. Vous pouvez également associer ces applets de commande dans Azure Automation avec les applets de commande pour les autres services Azure, pour automatiser les tâches complexes au sein des services Azure et 3e partie systèmes.

Automatisation Azure a également la possibilité de communiquer avec les serveurs SQL directement à l’aide de commandes SQL à l’aide de PowerShell.

La [Galerie de runbook Azure Automation](https://azure.microsoft.com/blog/2014/10/07/introducing-the-azure-automation-runbook-gallery/) contient une variété de procédures opérationnelles Communauté et de l’équipe de produit pour commencer à automatisation de gestion des bases de données SQL Azure, les autres services Azure et 3e systèmes tiers. Procédures opérationnelles galerie sont les suivantes :

 * [Exécuter des requêtes SQL sur une base de données SQL Server](https://gallery.technet.microsoft.com/scriptcenter/How-to-use-a-SQL-Command-be77f9d2)
 * [Évolution verticale (vers le haut ou vers le bas) une base de données SQL Azure sur un planning](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-Database-e957354f)
 * [Tronquer une table SQL si sa base de données est proche de sa taille maximale](https://gallery.technet.microsoft.com/scriptcenter/Azure-Automation-Your-SQL-30f8736b)
 * [Indexer des tables dans une base de données SQL Azure si elles sont très fragmentés](https://gallery.technet.microsoft.com/scriptcenter/Indexes-tables-in-an-Azure-73a2a8ea)

## <a name="next-steps"></a>Étapes suivantes

À présent que vous avez appris les notions de base d’Azure Automation et comment elle peut être utilisée pour gérer les bases de données SQL Azure, suivez ces liens pour en savoir plus sur automatisation Azure.

- [Vue d’ensemble de Automation Azure](../automation/automation-intro.md)
- [Mon runbook première](../automation/automation-first-runbook-graphical.md)
- [Carte d’apprentissage Automation Azure](https://azure.microsoft.com/documentation/learning-paths/automation/)
- [Automatisation Azure : Votre Agent SQL dans le Cloud](https://azure.microsoft.com/blog/2014/06/26/azure-automation-your-sql-agent-in-the-cloud/) 
 
