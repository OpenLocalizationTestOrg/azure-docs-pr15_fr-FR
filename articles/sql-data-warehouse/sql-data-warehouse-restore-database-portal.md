<properties
   pageTitle="Restaurer un SQL Azure Data Warehouse (portail) | Microsoft Azure"
   description="Tâches du portail Azure pour restaurer un magasin de données SQL Azure."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="Lakshmi1812"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/21/2016"
   ms.author="lakshmir;barbkess;sonyama"/>

# <a name="restore-an-azure-sql-data-warehouse-portal"></a>Restaurer un SQL Azure Data Warehouse (portail)

> [AZURE.SELECTOR]
- [Vue d’ensemble][]
- [Portail][]
- [PowerShell][]
- [RESTE][]

Dans cet article, vous allez apprendre à restaurer un SQL Azure Data Warehouse à l’aide du portail Azure.

## <a name="before-you-begin"></a>Avant de commencer

**Vérifiez votre capacité DTU.** Chaque Data Warehouse SQL est hébergé par un serveur SQL (par exemple, myserver.database.windows.net) qui dispose d’un quota DTU par défaut.  Avant de pouvoir restaurer un Data Warehouse SQL, vérifiez que la votre serveur SQL server est suffisamment quota DTU restant pour la base de données en cours de restauration. Pour apprendre à calculer DTU nécessaire ou pour demander DTU plus, voir [demander une modification de quota DTU][].


## <a name="restore-an-active-or-paused-database"></a>Restaurer une base de données active ou suspendu

Pour restaurer une base de données :

1. Connectez-vous au [portail Azure][]
2. Sur le côté gauche de l’écran, sélectionnez **Parcourir** , puis sélectionnez **serveurs SQL**
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-browse-for-sql-server.png)
    
3. Accédez à votre serveur et sélectionnez-le
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-select-server.png)

4. Trouver le Data Warehouse SQL que vous souhaitez restaurer à partir d’et le sélectionner
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-select-active-dw.png)
5. Dans la partie supérieure de la cuillère Data Warehouse, cliquez sur **restaurer**
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-select-restore-from-active.png)

6. Spécifiez un nouveau **nom de la base de données**
7. Sélectionnez le dernier **Point de restauration**
    1. Assurez-vous que vous choisissez le dernier point de restauration.  Dans la mesure où les points de restauration sont affichés dans UTC, parfois l’option par défaut indiquée n’est pas le dernier point de restauration.
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-restore-blade-from-active.png)

8. Cliquez sur **OK**
9. Le processus de restauration de base de données commence et peut être contrôlé à l’aide des **NOTIFICATIONS**

>[AZURE.NOTE] Une fois la restauration terminée, vous pouvez configurer votre base de données récupéré en suivant [configurer votre base de données après restauration][].


## <a name="restore-a-deleted-database"></a>Restaurer une base de données supprimé

Pour restaurer une base de données supprimée :

1. Connectez-vous au [portail Azure][]
2. Sur le côté gauche de l’écran, sélectionnez **Parcourir** , puis sélectionnez **serveurs SQL**
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-browse-for-sql-server.png)

3. Accédez à votre serveur et sélectionnez-le
    
    ![](./media/sql-data-warehouse-restore-database-portal/02-select-server.png)

4. Faites défiler jusqu'à la section opérations sur carte de votre serveur
5. Cliquez sur la vignette de **Bases de données supprimées**
    
    ![](./media/sql-data-warehouse-restore-database-portal/02-select-deleted-dws.png)

6. Sélectionnez la base de données supprimé que vous souhaitez restaurer
    
    ![](./media/sql-data-warehouse-restore-database-portal/02-select-deleted-dw.png)

7. Spécifiez un nouveau **nom de la base de données**
    
    ![](./media/sql-data-warehouse-restore-database-portal/02-restore-blade-from-deleted.png)
    
8. Cliquez sur **OK**
9. Le processus de restauration de base de données commence et peut être contrôlé à l’aide des **NOTIFICATIONS**

>[AZURE.NOTE] Pour configurer votre base de données une fois la restauration terminée, voir [configurer votre base de données après restauration][]. 

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur les fonctionnalités continuité des éditions de base de données SQL Azure, veuillez lire la [vue d’ensemble de la continuité de gestion de base de données SQL Azure][].

<!--Image references-->

<!--Article references-->
[Vue d’ensemble du continuité de gestion de la base de données SQL Azure]: ./sql-database-business-continuity.md
[Vue d’ensemble]: ./sql-data-warehouse-restore-database-overview.md
[Portail]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[RESTE]: ./sql-data-warehouse-restore-database-rest-api.md
[Configurer votre base de données après restauration]: ./sql-database-disaster-recovery.md#configure-your-database-after-recovery
[Demander une modification de quota DTU]: ./sql-data-warehouse-get-started-create-support-ticket.md#request-quota-change

<!--MSDN references-->

<!--Blog references-->

<!--Other Web references-->
[Portail Azure]: https://portal.azure.com/
