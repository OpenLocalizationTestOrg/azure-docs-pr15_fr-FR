<properties
    pageTitle="Copier une base de données SQL Azure | Microsoft Azure"
    description="Créer une copie d’une base de données SQL Azure"
    services="sql-database"
    documentationCenter=""
    authors="anosov1960"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="10/24/2016"
    ms.author="sstein; sashan"
    ms.workload="data-management"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>



# <a name="copy-an-azure-sql-database"></a>Copier une base de données SQL Azure

> [AZURE.SELECTOR]
- [Vue d’ensemble](sql-database-copy.md)
- [Portail Azure](sql-database-copy-portal.md)
- [PowerShell](sql-database-copy-powershell.md)
- [T-SQL](sql-database-copy-transact-sql.md)

Vous pouvez utiliser Azure [sauvegardes automatisées de base de données SQL](sql-database-automated-backups.md) pour créer une copie de votre base de données SQL. La copie de la base de données utilise la même technologie en tant que la fonctionnalité de réplication de geo. Mais contrairement à geo réplication elle termine le lien de réplication comme une fois la phase d’amorçage est terminée. Par conséquent, la base de données de copie est un instantané de la base de données source au moment de la demande de copie.  
Vous pouvez créer la copie de la base de données sur le même serveur ou un autre serveur. Le niveau et les performances niveau de service (niveau de tarification) de la copie de la base de données sont les mêmes que la base de données source par défaut. Lorsque vous utilisez l’API, vous pouvez sélectionner un niveau de performance différents dans le même niveau de service (édition). Une fois la copie terminée, la copie devient une base de données entièrement fonctionnelle, indépendamment. À ce stade, vous pouvez mettre à niveau ou mettre à niveau vers une version. Les connexions, les utilisateurs et les autorisations pouvant être gérées séparément.  

Lorsque vous copiez une base de données sur le même serveur logique, les connexions mêmes peuvent être utilisées dans les deux bases de données. L’entité que vous permet de copier la base de données de sécurité devient la base de données propriétaire sur la nouvelle base de données. Tous les utilisateurs de base de données et leurs sécurité identificateurs de leurs autorisations sont copiées dans la copie de la base de données.  

Lorsque vous copiez une base de données vers un autre serveur logique, la sécurité principale sur le nouveau serveur devient le propriétaire de base de données sur la nouvelle base de données. Si vous utilisez [les utilisateurs de base de données de contenu](sql-database-manage-logins.md) pour l’accès aux données garantit bases de données principales et secondaires ont toujours les mêmes informations d’identification utilisateur pour une fois la copie terminée, vous pouvez l’accéder immédiatement avec les mêmes informations d’identification. Si vous utilisez [Azure Active Directory](../active-directory/active-directory-whatis.md), vous pouvez éliminer complètement qu’il soit nécessaire pour la gestion des informations d’identification dans la copie. Toutefois, lorsque vous copiez la base de données vers un nouveau serveur, l’accès en général ne fonctionnera pas, car les connexions n’existent pas sur le nouveau serveur. Découvrez [comment gérer la sécurité de base de données SQL Azure après sinistre](sql-database-geo-replication-security-config.md) pour en savoir plus sur la gestion des connexions lors de la copie d’une base de données vers un autre serveur logique. 

Pour copier une base de données SQL, vous devez les éléments suivants :

- Un abonnement Azure. Si vous avez besoin d’un abonnement Azure simplement cliquez sur **Version d’évaluation gratuite** en haut de cette page et puis revenez à la fin de cet article.
- Une base de données SQL à copier. Si vous ne disposez pas d’une base de données SQL, créez-en un en suivant les étapes décrites dans cet article : [créer votre première base de données SQL Azure](sql-database-get-started.md).

## <a name="next-steps"></a>Étapes suivantes

- Voir [Copier une base de données SQL Azure à l’aide du portail Azure](sql-database-copy-portal.md) pour copier une base de données à l’aide du portail Azure.
- Voir [Copier une base de données SQL Azure à l’aide de PowerShell](sql-database-copy-powershell.md) pour copier une base de données à l’aide de PowerShell.
- Voir [Copier une base de données SQL Azure à l’aide de T-SQL](sql-database-copy-transact-sql.md) pour copier une base de données à l’aide de Transact-SQL.
- Découvrez [comment gérer la sécurité de base de données SQL Azure après sinistre](sql-database-geo-replication-security-config.md) pour en savoir plus sur la gestion des utilisateurs et les sessions lors de la copie d’une base de données vers un autre serveur logique.



## <a name="additional-resources"></a>Ressources supplémentaires

- [Gérer les connexions](sql-database-manage-logins.md)
- [Se connecter à la base de données SQL avec SQL Server Management Studio et effectuer un exemple de requête T-SQL](sql-database-connect-query-ssms.md)
- [Exporter la base de données à un DOS](sql-database-export.md)
- [Vue d’ensemble de la continuité de gestion](sql-database-business-continuity.md)
- [Documentation de base de données SQL](https://azure.microsoft.com/documentation/services/sql-database/)
