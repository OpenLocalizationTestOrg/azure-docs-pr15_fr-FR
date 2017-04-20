<properties
   pageTitle="Créer SQL Data Warehouse à l’aide de PowerShell | Microsoft Azure"
   description="Créer SQL Data Warehouse à l’aide de PowerShell"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/25/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# <a name="create-sql-data-warehouse-using-powershell"></a>Créer SQL Data Warehouse à l’aide de PowerShell

> [AZURE.SELECTOR]
- [Portail Azure](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

Cet article vous explique comment créer un SQL Data Warehouse à l’aide de PowerShell.

## <a name="prerequisites"></a>Conditions préalables

Pour commencer, vous devez :

- **Compte Azure**: visite [Azure la version d’évaluation gratuite][] ou [MSDN Azure crédits][] pour créer un compte.
- **Azure SQL server**: pour plus d’informations, consultez [créer un serveur logique de base de données SQL Azure avec le portail Azure][] ou [créer un serveur logique de base de données SQL Azure avec PowerShell][] .
- **Groupe de ressources**: utilisez le même groupe de ressources que votre serveur SQL Azure ou Découvrez [comment créer un groupe de ressources][].
- **PowerShell version 1.0.3 ou supérieur**: vous pouvez identifier votre version en exécutant **Get-Module - ListAvailable-nom Azure**.  La version la plus récente peut être installée à partir de [Microsoft Web Platform Installer][].  Pour plus d’informations sur l’installation de la dernière version, voir [comment installer et configurer Azure PowerShell][].

> [AZURE.NOTE] Création d’un Data Warehouse SQL peut entraîner un nouveau service facturable.  Pour plus d’informations sur les tarifs, consultez [Data Warehouse SQL tarification][] .

## <a name="create-a-sql-data-warehouse"></a>Créer un Data Warehouse SQL

1. Ouvrez Windows PowerShell.
2. Exécuter cette applet de commande pour vous connecter au Gestionnaire de ressources Azure.

    ```Powershell
    Login-AzureRmAccount
    ```
    
3. Sélectionnez l’abonnement que vous voulez utiliser pour votre session actuelle.

    ```Powershell
    Get-AzureRmSubscription -SubscriptionName "MySubscription" | Select-AzureRmSubscription
    ```

4.  Créer la base de données. Cet exemple crée une base de données nommée « mynewsqldw » avec niveau objectif de service « DW400 », sur le serveur nommé « sqldwserver1 », qui se trouve dans le groupe de ressources nommé « mywesteuroperesgp1 ».

    ```Powershell
    New-AzureRmSqlDatabase -RequestedServiceObjectiveName "DW400" -DatabaseName "mynewsqldw" -ServerName "sqldwserver1" -ResourceGroupName "mywesteuroperesgp1" -Edition "DataWarehouse" -CollationName "SQL_Latin1_General_CP1_CI_AS" -MaxSizeBytes 10995116277760
    ```

Paramètres requis sont :

- **RequestedServiceObjectiveName**: la quantité d' [DWU][] vous demandez.  Valeurs prises en charge sont : DW100, DW200, DW300, DW400, DW500, DW600, DW1000, DW1200, DW1500, DW2000, DW3000 et DW6000.
- **DatabaseName**: le nom du SQL Data Warehouse que vous créez.
- **Nom du serveur**: le nom du serveur que vous utilisez pour la création de (doit être V12).
- **ResourceGroupName**: groupe de ressources que vous utilisez.  Pour rechercher des ressources disponibles groupes dans votre abonnement utilisent Get-AzureResource.
- **Édition**: doit être « Data Warehouse » pour créer un Data Warehouse SQL.

Paramètres facultatifs sont :

- **CollationName**: le classement par défaut sauf indication contraire est SQL_Latin1_General_CP1_CI_AS.  Classement ne peuvent pas être modifié dans une base de données.
- **MaxSizeBytes**: la taille maximale par défaut d’une base de données est de 10 Go.


Pour plus d’informations sur les options de paramètres, voir [AzureRmSqlDatabase de nouveau][] et [Créer une base de données (SQL Azure Data Warehouse)][].

## <a name="next-steps"></a>Étapes suivantes

Une fois votre Data Warehouse SQL a terminé la mise en service vous souhaitiez essayez de [charger les exemples de données][] ou extraire comment [développer][], [charger][]ou [migrer][].

Si vous êtes intéressé plus d’informations sur la gestion de Data Warehouse SQL par programme, consultez notre article sur l’utilisation des [applets de commande PowerShell et API REST][].

<!--Image references-->

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[migrer]: ./sql-data-warehouse-overview-migrate.md
[développer]: ./sql-data-warehouse-overview-develop.md
[charger]: ./sql-data-warehouse-load-with-bcp.md
[chargement des exemples de données]: ./sql-data-warehouse-load-sample-databases.md
[Applets de commande PowerShell et API REST]: ./sql-data-warehouse-reference-powershell-cmdlets.md
[firewall rules]: ../sql-database-configure-firewall-settings.md

[Comment installer et configurer PowerShell Azure]: ../powershell/powershell-install-configure.md
[how to create a SQL Data Warehouse from the Azure Portal]: ./sql-data-warehouse-get-started-provision.md
[Création d’un serveur logique de base de données SQL Azure avec le portail Azure]: ../sql-database/sql-database-get-started.md#create-an-azure-sql-database-logical-server
[Création d’un serveur logique de base de données SQL Azure avec PowerShell]: ../sql-database/sql-database-get-started-powershell.md#database-setup-create-a-resource-group-server-and-firewall-rule
[comment créer un groupe de ressources]: ../resource-group-template-deploy-portal.md#create-resource-group

<!--MSDN references--> 
[MSDN]: https://msdn.microsoft.com/library/azure/dn546722.aspx
[Nouvelle AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619339.aspx
[Créer la base de données (SQL Azure Data Warehouse)]: https://msdn.microsoft.com/library/mt204021.aspx

<!--Other Web references-->
[Programme d’installation de la plateforme Microsoft Web]: https://aka.ms/webpi-azps
[Tarifs Data Warehouse SQL]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Version d’évaluation gratuite Azure]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[MSDN crédits Azure]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F
