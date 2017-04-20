<properties
   pageTitle="Créer un Data Warehouse SQL dans le portail Azure | Microsoft Azure"
   description="Découvrez comment créer un magasin de données SQL Azure dans le portail Azure"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="jhubbard"
   editor=""
   tags="azure-sql-data-warehouse"/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/25/2016"
   ms.author="barbkess;lodipalm;sonyama"/>

# <a name="create-an-azure-sql-data-warehouse"></a>Créer un magasin de données SQL Azure

> [AZURE.SELECTOR]
- [Portail Azure](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

Ce didacticiel utilise le portail Azure pour créer un Data Warehouse SQL qui contient une base de données exemple AdventureWorksDW.


## <a name="prerequisites"></a>Conditions préalables

Pour commencer, vous devez :

- **Compte Azure**: visite [Azure la version d’évaluation gratuite][] ou [MSDN Azure crédits][] pour créer un compte.
- **Azure SQL server**: pour plus d’informations, consultez [créer un serveur logique de base de données SQL Azure avec le portail Azure][] .

> [AZURE.NOTE] Création d’un Data Warehouse SQL peut entraîner un nouveau service facturable.  Pour plus d’informations, voir [SQL Data Warehouse tarifs][] .

## <a name="create-a-sql-data-warehouse"></a>Créer un Data Warehouse SQL

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Cliquez sur **+ nouvelle** > **données + stockage** > **Data Warehouse SQL**.

    ![Créer](./media/sql-data-warehouse-get-started-provision/create-sample.gif)

3. Dans la carte **Data Warehouse SQL** , renseignez les informations nécessaires, puis appuyez sur « Create » à créer.

    ![Créer la base de données](./media/sql-data-warehouse-get-started-provision/create-database.png)

    - **Serveur**: nous vous recommandons de sélectionner tout d’abord votre serveur.  

    - **Nom de la base de données**: le nom qui est utilisé pour référencer le Data Warehouse SQL.  Il doit être unique sur le serveur.
    
    - **Performances**: nous vous recommandons de commençant par 400 [DWUs][DWU]. Vous pouvez déplacer le curseur vers la gauche ou la droite pour régler les performances de votre data warehouse ou mettre à l’échelle vers le haut ou vers le bas après sa création.  Pour en savoir plus sur DWUs, consultez notre documentation sur [mise à l’échelle](./sql-data-warehouse-manage-compute-overview.md) ou nos [tarifs page][tarifs Data Warehouse SQL]. 

    - **Abonnement**: sélectionnez l' [abonnement] auquel ce Data Warehouse SQL sera facturation.

    - **Groupe de ressources**: [groupes de ressources] [ Resource group] sont des conteneurs conçus pour vous aider à gérer une collection de ressources Azure. En savoir plus sur les [groupes de ressources](../azure-resource-manager/resource-group-overview.md).

    - **Sélectionnez source**: cliquez sur **Sélectionner source** > **exemple**. Azure remplit automatiquement l’option **Sélectionner les exemples** avec AdventureWorksDW.

> [AZURE.NOTE] Le classement par défaut pour un Data Warehouse SQL est SQL_Latin1_General_CP1_CI_AS. Si un autre classement est nécessaire, [T-SQL][] pouvant être utilisées pour créer la base de données avec un classement différent.

4. Cliquez sur **créer** pour créer votre Data Warehouse SQL.

5. Patientez quelques minutes. Lorsque votre data warehouse est prête, vous devez être retournée au [portail Azure](https://portal.azure.com). Vous pouvez trouver votre Data Warehouse SQL dans votre tableau de bord, répertorié sous vos bases de données SQL, ou dans le groupe de ressources que vous avez utilisé pour créer votre document. 

    ![vue du portail](./media/sql-data-warehouse-get-started-provision/database-portal-view.png)

[AZURE.INCLUDE [SQL Database create server](../../includes/sql-database-create-new-server-firewall-portal.md)] 

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez créé un Data Warehouse SQL, vous êtes prêt à [se connecter](./sql-data-warehouse-connect-overview.md) et procéder à l’interrogation.

Pour charger les données dans Data Warehouse SQL, consultez la [vue d’ensemble de chargement](./sql-data-warehouse-overview-load.md).

Si vous essayez de migrer une base de données existante vers SQL Data Warehouse, reportez-vous à la [vue d’ensemble de la Migration](./sql-data-warehouse-overview-migrate.md) , ou utilisez [L’utilitaire de Migration](./sql-data-warehouse-migrate-migration-utility.md).

Règles de pare-feu peuvent également être configurés à l’aide de Transact-SQL. Pour plus d’informations, voir [sp_set_firewall_rule][] et [sp_set_database_firewall_rule][].

Il est également une excellente idée pour examiner les [meilleures pratiques][].

<!--Article references-->
[Création d’un serveur logique de base de données SQL Azure avec le portail Azure]: ../sql-database/sql-database-get-started.md#create-an-azure-sql-database-logical-server
[Create an Azure SQL Database logical server with PowerShell]: ../sql-database/sql-database-get-started-powershell.md#database-setup-create-a-resource-group-server-and-firewall-rule
[resource groups]: ../resource-group-template-deploy-portal.md
[Meilleures pratiques]: sql-data-warehouse-best-practices.md
[DWU]: sql-data-warehouse-overview-what-is.md#data-warehouse-units
[abonnement]: ../azure-glossary-cloud-terminology.md#subscription
[resource group]: ../azure-glossary-cloud-terminology.md#resource-group
[T-SQL]: ./sql-data-warehouse-get-started-create-database-tsql.md
 
<!--MSDN references-->
[sp_set_firewall_rule]: https://msdn.microsoft.com/library/dn270017.aspx
[sp_set_database_firewall_rule]: https://msdn.microsoft.com/library/dn270010.aspx

<!--Other Web references-->
[Tarifs Data Warehouse SQL]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Version d’évaluation gratuite Azure]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[MSDN crédits Azure]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F

