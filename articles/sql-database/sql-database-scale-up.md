<properties
    pageTitle="Modifier le niveau de performances et de niveau de service d’une base de données SQL Azure | Microsoft Azure"
    description="Modifier le niveau de service et niveau de performance d’une base de données SQL Azure montre comment mettre à l’échelle de votre base de données SQL vers le haut ou vers le bas. Modifier le niveau de tarification d’une base de données SQL Azure."
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="10/12/2016"
    ms.author="sstein"
    ms.workload="data-management"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>


# <a name="change-the-service-tier-and-performance-level-pricing-tier-of-a-sql-database-using-the-azure-portal"></a>Modifier le niveau et les performances niveau de service (tarification couche) d’une base de données SQL à l’aide du portail Azure


> [AZURE.SELECTOR]
- [**Portail Azure**](sql-database-scale-up.md)
- [PowerShell](sql-database-scale-up-powershell.md)


Niveaux de service et de performances décrivent les fonctionnalités et ressources disponibles pour votre base de données SQL et peuvent être mis à jour selon les besoins de votre application. Pour plus d’informations, voir [Niveaux de Service](sql-database-service-tiers.md).

Remarquez que la modification du niveau de service et/ou niveau de performances d’une base de données crée une copie de la base de données d’origine dans le nouveau niveau de performance et avant de passer les connexions au réplica. Aucune donnée n’est perdue au cours du processus mais pendant la brève intervention quand nous basculer vers le réplica, les connexions à la base de données sont désactivées, certaines transactions en vol peuvent être annulées. Cette fenêtre varie, mais est en moyenne moins de 4 secondes et dans plus de 99 % des cas est inférieur à 30 secondes. Très rarement, en particulier si grande numéros de transactions dans vol aux connexions échantillonnage sont désactivés, cette fenêtre peut être plus longue.  

La durée de l’ensemble du processus à distance échelle dépend de la couche service et taille de la base de données avant et après la modification. Par exemple, une base de données de 250 Go change vers, d’ou au sein d’un niveau de service Standard, doit se terminer dans les 6 heures. Pour une base de données de la même taille que la modification de niveaux de performances dans le niveau de service Premium est, elle doit se terminer au sein de 3 heures.


Utilisez les informations dans [les niveaux de performances et les niveaux de Service de base de données SQL Azure](sql-database-service-tiers.md) pour déterminer le niveau de performances et de niveau de service approprié pour votre base de données SQL Azure.

- Pour mettre à niveau une base de données, la base de données doit être inférieure à la taille maximale autorisée du niveau de service cible. 
- Lors de la mise à niveau d’une base de données avec [La réplication Geo](sql-database-geo-replication-overview.md) est activée, vous devez d’abord passer ses bases de données secondaires vers le niveau de performance souhaité avant la mise à niveau de la base de données principale.
- Lors de la mise à niveau un niveau de service, vous devez tout d’abord terminer toutes les relations Geo réplication. 
- Les offres de service restaurer sont différentes pour les différents niveaux de service. Si vous êtes mise à niveau vous risquez de perdre la possibilité de restaurer à un point dans le temps, ou dispose d’un délai de conservation des sauvegardes inférieur. Pour plus d’informations, voir [sauvegarde de base de données SQL Azure et de restauration](sql-database-business-continuity.md).
- Modification de votre base de données tarifs couche ne change pas la taille maximale de la base de données. Pour modifier votre base de données taille maximale utilisez [Transact-SQL (T-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) ou [PowerShell](https://msdn.microsoft.com/library/mt619433.aspx).
- Les nouvelles propriétés de la base de données ne sont pas appliquées jusqu'à ce que les modifications sont terminées.



**Pour terminer cet article vous besoin des éléments suivants :**

- Une base de données SQL Azure. Si vous ne disposez pas d’une base de données SQL, créez-en un en suivant les étapes décrites dans cet article : [créer votre première base de données SQL Azure](sql-database-get-started.md).


## <a name="change-the-service-tier-and-performance-level-of-your-database"></a>Modifier le niveau de service niveau et les performances de votre base de données


Ouvrez la carte de base de données SQL pour la base de données que vous voulez mettre à l’échelle vers le haut ou vers le bas :

1.  Dans le [portail Azure](https://portal.azure.com), cliquez sur **plusieurs services de** > **bases de données SQL**.
2.  Cliquez sur la base de données que vous souhaitez modifier.
3.  Dans la carte de **base de données SQL** cliquez sur **couche de tarification (échelle DTUs)**:

    ![niveau de tarification][1]

1.  Choisissez un nouveau niveau, puis cliquez sur **Sélectionner**:

    Cliquer sur **Sélectionner** envoie une demande d’échelle pour modifier le niveau de tarification. Selon la taille de votre base de données mise à l’échelle peut prendre un certain temps à complète (voir les informations dans la partie supérieure de cet article).

    > [AZURE.NOTE] Modification de votre base de données tarifs couche ne change pas la taille maximale de la base de données. Pour modifier votre base de données taille maximale utilisez [Transact-SQL (T-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) ou [PowerShell](https://msdn.microsoft.com/library/mt619433.aspx).

    ![Sélectionnez le niveau de tarification][2]

3.  Cliquez sur l’icône de notification (bell), dans le coin supérieur droit :

    ![notifications][3]

    Cliquez sur le texte de notification pour ouvrir le volet de détails où vous pouvez consulter l’état de la demande.




## <a name="next-steps"></a>Étapes suivantes

- Modifier votre taille maximale de base de données à l’aide de [PowerShell](https://msdn.microsoft.com/library/mt619433.aspx)ou [Transact-SQL (T-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) .
- [Mettre à l’échelle, puis dans](sql-database-elastic-scale-get-started.md)
- [Se connecter et une base de données SQL SSMS de requête](sql-database-connect-query-ssms.md)
- [Exporter une base de données SQL Azure](sql-database-export.md)

## <a name="additional-resources"></a>Ressources supplémentaires

- [Vue d’ensemble de la continuité de gestion](sql-database-business-continuity.md)
- [Documentation de base de données SQL](https://azure.microsoft.com/documentation/services/sql-database/)


<!--Image references-->
[1]: ./media/sql-database-scale-up/new-tier.png
[2]: ./media/sql-database-scale-up/choose-tier.png
[3]: ./media/sql-database-scale-up/scale-notification.png
[4]: ./media/sql-database-scale-up/new-tier.png
