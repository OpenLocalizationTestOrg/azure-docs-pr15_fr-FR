<properties
   pageTitle="Gérer l’alimentation cluster dans SQL Azure Data Warehouse (PowerShell) | Microsoft Azure"
   description="Tâches de PowerShell pour gérer calculent power. Échelle de ressources de calcul en ajustant DWUs. Ou, interrompre et reprendre cluster ressources pour réduire les coûts."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/13/2016"
   ms.author="barbkess;sonyama"/>

# <a name="manage-compute-power-in-azure-sql-data-warehouse-powershell"></a>Gérer l’alimentation cluster dans SQL Azure Data Warehouse (PowerShell)

> [AZURE.SELECTOR]
- [Vue d’ensemble](sql-data-warehouse-manage-compute-overview.md)
- [Portail](sql-data-warehouse-manage-compute-portal.md)
- [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
- [RESTE](sql-data-warehouse-manage-compute-rest-api.md)
- [TSQL](sql-data-warehouse-manage-compute-tsql.md)


Adapter les performances en faisant évoluer calculent des ressources et la mémoire pour répondre à la demande de modification de votre charge de travail. Réduire les coûts par des ressources précédent mise à l’échelle au cours des heures non pointe ou cluster suspension complètement. 

Cet ensemble de tâches utilise le portail Azure pour :

- Échelle cluster
- Cluster pause
- CV cluster

Pour plus d’informations, voir [vue d’ensemble de calcul gérer][].


## <a name="before-you-begin"></a>Avant de commencer

### <a name="install-the-latest-version-of-azure-powershell"></a>Installer la dernière version de PowerShell Azure

> [AZURE.NOTE]  Pour utiliser PowerShell Azure avec SQL Data Warehouse, vous devez Azure PowerShell version 1.0.3 ou version ultérieure.  Pour vérifier votre version actuelle, exécutée la commande **Get-Module - ListAvailable-nom Azure**. Vous pouvez installer la version la plus récente à partir de [Microsoft Web Platform Installer][].  Pour plus d’informations, voir [comment installer et configurer Azure PowerShell][].

### <a name="get-started-with-azure-powershell-cmdlets"></a>Prise en main applets de commande PowerShell Azure

Pour commencer :

1. Ouvrez la session PowerShell Azure. 
2. À l’invite PowerShell, exécutez ces commandes pour se connecter au Gestionnaire de ressources Azure et sélectionnez votre abonnement.

    ```PowerShell
    Login-AzureRmAccount
    Get-AzureRmSubscription
    Select-AzureRmSubscription -SubscriptionName "MySubscription"
    ```

<a name="scale-performance-bk"></a>
<a name="scale-compute-bk"></a>

## <a name="scale-compute-power"></a>Échelle cluster power

[AZURE.INCLUDE [SQL Data Warehouse scale DWUs description](../../includes/sql-data-warehouse-scale-dwus-description.md)]

Pour modifier les DWUs, utilisez l’applet de commande PowerShell [Set-AzureRmSqlDatabase][] . L’exemple suivant définit l’objectif de niveau de service à DW1000 pour la base de données MySQLDW qui est hébergé sur le serveur MyServer. 

```Powershell
Set-AzureRmSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000"
```

<a name="pause-compute-bk"></a>

## <a name="pause-compute"></a>Cluster pause

[AZURE.INCLUDE [SQL Data Warehouse pause description](../../includes/sql-data-warehouse-pause-description.md)]

Pour suspendre une base de données, utilisez l’applet de commande de [Mise en veille AzureRmSqlDatabase][] . L’exemple suivant s’interrompt une base de données nommée base de données02 hébergé sur un serveur nommé serveur01. Le serveur est dans un groupe de ressources Azure nommé ResourceGroup1. 

> [AZURE.NOTE] Notez que si votre serveur est foo.database.windows.net, utilisez « foo » comme le nom du serveur - dans les applets de commande PowerShell.

```Powershell
Suspend-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
```
Une variation de l’exemple suivant extrait la base de données dans l’objet $database. Il injecte ensuite l’objet à la [Mise en veille AzureRmSqlDatabase][]. Les résultats sont stockés dans l’objet resultDatabase. La commande finale affiche les résultats.

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzureRmSqlDatabase
$resultDatabase
```

<a name="resume-compute-bk"></a>

## <a name="resume-compute"></a>CV cluster

[AZURE.INCLUDE [SQL Data Warehouse resume description](../../includes/sql-data-warehouse-resume-description.md)]

Pour démarrer une base de données, utilisez l’applet de commande [Resume AzureRmSqlDatabase][] . L’exemple suivant démarre une base de données nommée base de données02 hébergé sur un serveur nommé serveur01. Le serveur est dans un groupe de ressources Azure nommé ResourceGroup1. 

```Powershell
Resume-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" -DatabaseName "Database02"
```

Une variation de l’exemple suivant extrait la base de données dans l’objet $database. Il injecte l’objet à [Reprendre AzureRmSqlDatabase][] et stocke les résultats dans $resultDatabase. La commande finale affiche les résultats.

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzureRmSqlDatabase
$resultDatabase
```

<a name="next-steps-bk"></a>

## <a name="next-steps"></a>Étapes suivantes

Pour d’autres tâches de gestion, voir [Présentation de la gestion][].

<!--Image references-->

<!--Article references-->
[Service capacity limits]: ./sql-data-warehouse-service-capacity-limits.md
[Présentation de la gestion]: ./sql-data-warehouse-overview-manage.md
[Comment installer et configurer PowerShell Azure]: ./powershell-install-configure.md
[Gérer la vue d’ensemble de calcul]: ./sql-data-warehouse-manage-compute-overview.md

<!--MSDN references-->
[CV AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619347.aspx
[AzureRmSqlDatabase suspendre]: https://msdn.microsoft.com/library/mt619337.aspx
[Jeu de AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619433.aspx

<!--Other Web references-->
[Programme d’installation de la plateforme Microsoft Web]: https://aka.ms/webpi-azps
[Azure portal]: http://portal.azure.com/
