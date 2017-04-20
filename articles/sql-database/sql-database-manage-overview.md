<properties
    pageTitle="Vue d’ensemble : outils de gestion pour la base de données SQL | Microsoft Azure"
    description="Compare les outils et les options de gestion de base de données SQL Azure"
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/24/2016"
    ms.author="sstein"/>

# <a name="overview-management-tools-for-sql-database"></a>Vue d’ensemble : outils de gestion pour la base de données SQL

Cette rubrique présente et compare les outils et les options de gestion des bases de données SQL Azure de sorte que vous pouvez choisir l’outil approprié pour la tâche, votre entreprise et vous. Choix de l’outil approprié dépend de bases de données combien vous gérez, la tâche et la fréquence à laquelle une tâche est effectuée.

## <a name="azure-portal"></a>Portail Azure

Le [portail Azure](https://portal.azure.com) est une application basée sur le web dans laquelle vous pouvez créer, mettre à jour et supprimer des bases de données et des serveurs logiques et contrôler l’activité de base de données. Cet outil est correctement si vous venez juste de commencer avec Azure, gestion de plusieurs bases de données, ou que vous devez effectuer une opération rapidement.

Pour plus d’informations sur l’utilisation du portail, voir [Gérer les bases de données SQL à l’aide du portail Azure](sql-database-manage-portal.md).

## <a name="sql-server-management-studio-and-sql-server-data-tools-in-visual-studio"></a>SQL Server Management Studio et SQL Server Data Tools dans Visual Studio

SQL Server Management Studio (SSMS) et SQL Server Data Tools (SSDT) sont les outils clients qui s’exécutent sur votre ordinateur pour gérer et développer votre base de données dans le cloud. Si vous êtes un développeur d’applications familiarisé avec Visual Studio ou d’autres environnements de développement intégré (IDE), [essayez d’utiliser SSDT dans Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx). De nombreux administrateurs de base de données vous connaissez SSMS, qui peut être utilisé avec des bases de données SQL Azure. [Téléchargez la dernière version de SSMS](https://msdn.microsoft.com/library/mt238290) et toujours utiliser la dernière version lorsque vous travaillez avec la base de données SQL Azure. Pour plus d’informations sur la gestion de vos bases de données SQL Azure avec SSMS, voir [Gérer les bases de données SQL à l’aide de SSMS](sql-database-manage-azure-ssms.md).

> [AZURE.IMPORTANT] Toujours utiliser la dernière version de [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290) et [SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) pour rester synchronisé avec les mises à jour Microsoft Azure et base de données SQL.


## <a name="powershell"></a>PowerShell

Vous pouvez utiliser PowerShell pour gérer les bases de données et des pools élastique de base de données et pour automatiser les déploiements ressource Azure. Microsoft recommande cet outil permettant de gérer un grand nombre de bases de données et d’automatisation du déploiement et modifications de ressources dans un environnement de production.

Pour plus d’informations, voir [Gérer la base de données SQL avec PowerShell](sql-database-manage-powershell.md)

## <a name="elastic-database-tools"></a>Outils de base de données élastiques
Utilisez les outils de base de données élastique pour effectuer des actions telles que 

* Exécuter un script T-SQL par rapport à un ensemble de bases de données à l’aide d’une [tâche élastique](sql-database-elastic-jobs-overview.md)
* Déplacement des bases de données model client multiples à un modèle unique client avec l' [outil de fusion de fractionnement](sql-database-elastic-scale-overview-split-and-merge.md)
* Gestion des bases de données dans un modèle unique client ou client multiples à l’aide de la [bibliothèque cliente échelle élastique](sql-database-elastic-database-client-library.md).
 

## <a name="additional-resources"></a>Ressources supplémentaires

- [Gestionnaire de ressources Azure](https://azure.microsoft.com/features/resource-manager/)
- [Automatisation Azure](https://azure.microsoft.com/documentation/services/automation/)
- [Planificateur Azure](https://azure.microsoft.com/documentation/services/scheduler/)