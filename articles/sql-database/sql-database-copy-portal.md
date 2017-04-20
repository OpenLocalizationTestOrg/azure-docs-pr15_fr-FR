<properties
    pageTitle="Copier une base de données SQL Azure à l’aide du portail Azure | Microsoft Azure"
    description="Créer une copie d’une base de données SQL Azure"
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="09/19/2016"
    ms.author="sstein"
    ms.workload="data-management"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>



# <a name="copy-an-azure-sql-database-using-the-azure-portal"></a>Copier une base de données SQL Azure à l’aide du portail Azure

> [AZURE.SELECTOR]
- [Vue d’ensemble](sql-database-copy.md)
- [Portail Azure](sql-database-copy-portal.md)
- [PowerShell](sql-database-copy-powershell.md)
- [T-SQL](sql-database-copy-transact-sql.md)

Les étapes suivantes vous montrent comment copier une base de données SQL grâce au [portail Azure](https://portal.azure.com) dans le même serveur ou un autre serveur.

Pour copier une base de données SQL, vous devez les éléments suivants :

- Un abonnement Azure. Si vous avez besoin d’un abonnement Azure simplement cliquez sur **Version d’évaluation gratuite** en haut de cette page et puis revenez à la fin de cet article.
- Une base de données SQL à copier. Si vous ne disposez pas d’une base de données SQL, créez-en un en suivant les étapes décrites dans cet article : [créer votre première base de données SQL Azure](sql-database-get-started.md).


## <a name="copy-your-sql-database"></a>Copiez votre base de données SQL

Ouvrez la page de base de données SQL pour la base de données que vous voulez copier :

1.  Accédez au [portail Azure](https://portal.azure.com).
2.  Cliquez sur **plusieurs services de** > **bases de données SQL**, puis cliquez sur la base de données souhaité.
3.  Dans la page de base de données SQL, cliquez sur **Copier**:

    ![Base de données SQL](./media/sql-database-copy-portal/sql-database-copy.png)

1.  Dans la page **Copier** , un nom de base de données par défaut est fourni. Tapez un nom différent si vous souhaitez (toutes les bases de données sur un serveur doivent avoir des noms uniques).
2.  Sélectionnez un **serveur cible**. Le serveur cible est l’endroit où la copie de la base de données est créée. Vous pouvez copier la base de données dans le même serveur ou un autre serveur. Vous pouvez créer un serveur ou sélectionnez un serveur existant dans la liste. 
3.  Après avoir sélectionné le **serveur cible**, le **pool de base de données élastique**et **tarification niveau** options sont activées. Si le serveur dispose d’un pool, vous pouvez copier la base de données dans celle-ci.
3.  Cliquez sur **OK** pour démarrer le processus de copie.

    ![Base de données SQL](./media/sql-database-copy-portal/copy-page.png)


## <a name="monitor-the-progress-of-the-copy-operation"></a>Surveiller l’avancement de l’opération de copie

- Après avoir démarré la copie, cliquez sur la notification du portail pour plus d’informations.

    ![notification][3]
 
    ![Moniteur][4]


## <a name="verify-the-database-is-live-on-the-server"></a>Vérifiez que la base de données est en ligne sur le serveur

- Cliquez sur **plusieurs services de** > **bases de données SQL** et vérifiez que la nouvelle base de données est **en ligne**.


## <a name="resolve-logins"></a>Résolution de connexions

Pour résoudre les connexions une fois l’opération de copie terminée, voir [résolution de connexions](sql-database-copy-transact-sql.md#resolve-logins-after-the-copy-operation-completes)


## <a name="next-steps"></a>Étapes suivantes

- Pour une vue d’ensemble de la copie d’une base de données SQL Azure, voir [Copier une base de données SQL Azure](sql-database-copy.md) .
- Voir [Copier une base de données SQL Azure à l’aide de PowerShell](sql-database-copy-powershell.md) pour copier une base de données à l’aide de PowerShell.
- Voir [Copier une base de données SQL Azure à l’aide de T-SQL](sql-database-copy-transact-sql.md) pour copier une base de données à l’aide de Transact-SQL.
- Découvrez [comment gérer la sécurité de base de données SQL Azure après sinistre](sql-database-geo-replication-security-config.md) pour en savoir plus sur la gestion des utilisateurs et les sessions lors de la copie d’une base de données vers un autre serveur logique.



## <a name="additional-resources"></a>Ressources supplémentaires

- [Gérer les connexions](sql-database-manage-logins.md)
- [Se connecter à la base de données SQL avec SQL Server Management Studio et effectuer un exemple de requête T-SQL](sql-database-connect-query-ssms.md)
- [Exporter la base de données à un DOS](sql-database-export.md)
- [Vue d’ensemble de la continuité de gestion](sql-database-business-continuity.md)
- [Documentation de base de données SQL](https://azure.microsoft.com/documentation/services/sql-database/)




<!--Image references-->
[1]: ./media/sql-database-copy-portal/copy.png
[2]: ./media/sql-database-copy-portal/copy-ok.png
[3]: ./media/sql-database-copy-portal/copy-notification.png
[4]: ./media/sql-database-copy-portal/monitor-copy.png

