<properties 
    pageTitle="Copier une base de données SQL Azure à l’aide de Transact-SQL | Microsoft Azure" 
    description="Créer une copie d’une base de données SQL Azure à l’aide de Transact-SQL" 
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


# <a name="copy-an-azure-sql-database-using-transact-sql"></a>Copier une base de données SQL Azure à l’aide de Transact-SQL


> [AZURE.SELECTOR]
- [Vue d’ensemble](sql-database-copy.md)
- [Portail Azure](sql-database-copy-portal.md)
- [PowerShell](sql-database-copy-powershell.md)
- [T-SQL](sql-database-copy-transact-sql.md)


La procédure décrite ci-dessous vous montrer comment copier une base de données SQL avec Transact-SQL sur le même serveur ou un autre serveur. L’opération de copie de base de données utilise l’instruction [CREATE DATABASE](https://msdn.microsoft.com/library/ms176061.aspx) .

Pour effectuer les étapes décrites dans cet article vous besoin des éléments suivants :

- Un abonnement Azure. Si vous avez besoin d’un abonnement Azure simplement cliquez sur **Version d’évaluation gratuite** en haut de cette page et puis revenez à la fin de cet article.
- Une base de données SQL Azure. Si vous ne disposez pas d’une base de données SQL, créez-en un en suivant les étapes décrites dans cet article : [créer votre première base de données SQL Azure](sql-database-get-started.md).
- [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/ms174173.aspx). Si vous n’avez pas SSMS, ou si fonctionnalités décrites dans cet article ne sont pas disponibles, [Téléchargez la dernière version](https://msdn.microsoft.com/library/mt238290.aspx).


## <a name="copy-your-sql-database"></a>Copiez votre base de données SQL

Connectez-vous à la base de données principale à l’aide de la connexion d’accès au niveau du serveur principale ou de la connexion qui a créé la base de données que vous souhaitez copier. Connexions qui ne sont pas l’entité de sécurité au niveau du serveur doivent être membres du rôle dbmanager afin de copier des bases de données. Pour plus d’informations sur les connexions et la connexion au serveur, voir [Gérer les connexions](sql-database-manage-logins.md).

Commencer la copie de la base de données source avec l’instruction [CREATE DATABASE](https://msdn.microsoft.com/library/ms176061.aspx) . Cette instruction lance le processus de copie de base de données. Copie d’une base de données étant un processus asynchrone, l’instruction CREATE DATABASE renvoie avant la fin de la base de données copie.


### <a name="copy-a-sql-database-to-the-same-server"></a>Copier une base de données SQL sur le même serveur

Connectez-vous à la base de données principale à l’aide de la connexion d’accès au niveau du serveur principale ou de la connexion qui a créé la base de données que vous souhaitez copier. Connexions qui ne sont pas l’entité de sécurité au niveau du serveur doivent être membres du rôle dbmanager afin de copier des bases de données.

Cette copie de commande Database1 à une base de données nommée Database2 sur le même serveur. Selon la taille de votre base de données l’opération de copie peut prendre un certain temps pour terminer.

    -- Execute on the master database.
    -- Start copying.
    CREATE DATABASE Database1_copy AS COPY OF Database1;

### <a name="copy-a-sql-database-to-a-different-server"></a>Copier une base de données SQL vers un autre serveur

Connectez-vous à la base de données principale du serveur de destination, le serveur de base de données SQL Azure où la nouvelle base de données doit être créé. Utiliser une connexion qui a le même nom et mot de passe en tant que la base de données propriétaire de la base de données source sur le serveur de base de données SQL Azure source. La connexion au serveur de destination doit également être membre du rôle dbmanager ou le nom de connexion au niveau du serveur principal.

Cette commande copie Database1 sur server1 - dans une base de données nommée Database2 sur Serveur2. Selon la taille de votre base de données l’opération de copie peut prendre un certain temps pour terminer.


    -- Execute on the master database of the target server (server2)
    -- Start copying from Server1 to Server2
    CREATE DATABASE Database1_copy AS COPY OF server1.Database1;
    

## <a name="monitor-the-progress-of-the-copy-operation"></a>Surveiller l’avancement de l’opération de copie

Surveiller le processus de copie par interroger les vues sys.databases et sys.dm_database_copies. Alors que la copie est en cours, la colonne state_desc de la vue sys.databases pour la nouvelle base de données est définie sur la copie.


- Si la copie échoue, la colonne state_desc de la vue sys.databases pour la nouvelle base de données est définie sur SUSPECT. Dans ce cas, exécutez l’instruction DROP sur la nouvelle base de données et réessayez plus tard.
- Si la copie réussit, la colonne state_desc de la vue sys.databases pour la nouvelle base de données est définie sur en ligne. Dans ce cas, la copie terminée et la nouvelle base de données est une base de données régulière, peut être modifié indépendamment de la base de données source.

> [AZURE.NOTE] -Si vous décidez d’annuler la copie lorsqu’il est en cours, exécutez l’instruction [DROP DATABASE](https://msdn.microsoft.com/library/ms178613.aspx) sur la nouvelle base de données. Par ailleurs, l’exécution de l’instruction DROP DATABASE sur la base de données source annule également le processus de copie.


## <a name="resolve-logins-after-the-copy-operation-completes"></a>Résolution de connexions une fois l’opération de copie terminée

Une fois que la nouvelle base de données est en ligne sur le serveur de destination, utilisez l’instruction [ALTER USER](https://msdn.microsoft.com/library/ms176060.aspx) remapper les utilisateurs à partir de la nouvelle base de données aux connexions sur le serveur de destination. Pour résoudre les problèmes d’utilisateurs orphelins, voir [Résoudre les problèmes d’utilisateurs orphelins](https://msdn.microsoft.com/library/ms175475.aspx). Voir aussi [comment gérer la sécurité de base de données SQL Azure après sinistre](sql-database-geo-replication-security-config.md).

Tous les utilisateurs de la nouvelle base de données mettre à jour les autorisations qu’il avait dans la base de données source. L’utilisateur à l’origine de la copie de la base de données devient le propriétaire de base de données de la nouvelle base de données et est affectée à un nouvel identificateur de sécurité (identificateur de sécurité). Une fois la copie établie et avant d’autres utilisateurs sont remappés, uniquement la connexion qui a démarré la copie, la base de données propriétaire, pouvez vous connecter à la nouvelle base de données.


## <a name="next-steps"></a>Étapes suivantes

- Pour une vue d’ensemble de la copie d’une base de données SQL Azure, voir [Copier une base de données SQL Azure](sql-database-copy.md) .
- Voir [Copier une base de données SQL Azure à l’aide du portail Azure](sql-database-copy-portal.md) pour copier une base de données à l’aide du portail Azure.
- Voir [Copier une base de données SQL Azure à l’aide de PowerShell](sql-database-copy-powershell.md) pour copier une base de données à l’aide de PowerShell.
- Découvrez [comment gérer la sécurité de base de données SQL Azure après sinistre](sql-database-geo-replication-security-config.md) pour en savoir plus sur la gestion des utilisateurs et les sessions lors de la copie d’une base de données vers un autre serveur logique.



## <a name="additional-resources"></a>Ressources supplémentaires

- [Gérer les connexions](sql-database-manage-logins.md)
- [Se connecter à la base de données SQL avec SQL Server Management Studio et effectuer un exemple de requête T-SQL](sql-database-connect-query-ssms.md)
- [Exporter la base de données à un DOS](sql-database-export.md)
- [Vue d’ensemble de la continuité de gestion](sql-database-business-continuity.md)
- [Documentation de base de données SQL](https://azure.microsoft.com/documentation/services/sql-database/)


