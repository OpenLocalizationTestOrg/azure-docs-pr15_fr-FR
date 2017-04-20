<properties
    pageTitle="Mise à niveau vers V12 de base de données SQL Azure à l’aide de PowerShell | Microsoft Azure"
    description="Explique comment mettre à niveau vers V12 de base de données SQL Azure, notamment comment mettre à niveau des bases de données Web et entreprise et comment mettre à niveau un serveur 11 migration ses bases de données directement dans un pool élastique de base de données à l’aide de PowerShell."
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
    ms.date="09/19/2016"
    ms.author="sstein"/>

# <a name="upgrade-to-azure-sql-database-v12-using-powershell"></a>Mise à niveau vers V12 de base de données SQL Azure à l’aide de PowerShell


> [AZURE.SELECTOR]
- [Portail Azure](sql-database-upgrade-server-portal.md)
- [PowerShell](sql-database-upgrade-server-powershell.md)


V12 de base de données SQL étant la dernière version mise à niveau vers V12 de base de données SQL est conseillé.
V12 de base de données SQL contient de nombreux [avantages par rapport à la version précédente](sql-database-v12-whats-new.md) , y compris :

- Augmenter la compatibilité avec SQL Server.
- Performances améliorées premium et nouveaux niveaux de performances.
- [Des pools élastique de base de données](sql-database-elastic-pool.md).

Cet article fournit des instructions relatives à la mise à niveau des bases de données et les serveurs 11 de base de données SQL existants vers V12 de base de données SQL.

Pendant le processus de mise à niveau vers V12, vous mettez à niveau des bases de données Web et entreprise vers un nouveau niveau de service afin que les indications correspondant à la mise à niveau des bases de données Web et d’entreprise sont incluses.

En outre, la migration vers un [pool de base de données élastique](sql-database-elastic-pool.md) peut être plus efficace que la mise à niveau des niveaux de performance individuels (tarification niveaux) pour les bases de données unique. Pools simplifient également la gestion de base de données, car vous ne devez gérer les paramètres de performance pour le pool plutôt que séparément gérer les niveaux de performances des bases de données. Si vous avez des bases de données sur plusieurs serveurs, envisagez de les déplacer dans le même serveur et tirer parti en les mettant dans un pool.

Vous pouvez suivre les étapes décrites dans cet article pour migrer facilement des bases de données à partir de serveurs 11 directement dans des pools élastique de base de données.

Notez que vos bases de données seront restent en ligne et continuer à travailler dans l’ensemble de l’opération de mise à niveau. Au moment de la transition réelle vers le nouveau niveau de performance temporaire de suppression des connexions à la base de données peut se produire pendant une durée très petite est généralement environ 90 secondes, mais peut être autant que 5 minutes. Si votre application a [traitement d’inattendues de connexion de l’erreur transitoire](sql-database-connectivity-issues.md) , il est suffisant pour protéger contre les pertes de connexion à la fin de la mise à niveau.

Mise à niveau vers V12 de base de données SQL ne peut pas être annulée. Après une mise à niveau le serveur ne peut pas être annulée en 11.

Après mise à niveau vers V12, [recommandations de niveau de service](sql-database-service-tier-advisor.md) et de [recommandations relatives aux pools élastique](sql-database-elastic-pool-create-portal.md) immédiatement sera pas disponibles jusqu'à ce que le service a temps d’évaluer vos charges de travail sur le nouveau serveur. Historique des recommandations de serveur 11 ne s’applique pas aux serveurs V12 afin qu’il n’est pas conservé.  

## <a name="prepare-to-upgrade"></a>Préparer la mise à niveau

- **Mettre à niveau toutes les bases de données Web et entreprise**: utiliser le portail ou utiliser [PowerShell pour mettre à niveau des bases de données et le serveur](sql-database-upgrade-server-powershell.md).
- **Révision et suspendre la réplication de Geo**: Si votre base de données SQL Azure est configuré pour la réplication de Geo vous devez documenter sa configuration en cours et [Arrêter Geo réplication](sql-database-geo-replication-portal.md#remove-secondary-database). Une fois terminée la mise à niveau reconfigurer votre base de données pour la réplication de Geo.
- **Ouvrez les ports si vous disposez de clients sur un ordinateur virtuel Azure**: Si votre programme client se connecte à V12 de base de données SQL pendant l’exécution de votre client sur une Azure machine virtuelle (), vous devez ouvrir plages de port 11000 11999 et 14000-14999 sur l’ordinateur virtuel. Pour plus d’informations, voir [Ports pour V12 de base de données SQL](sql-database-develop-direct-route-ports-adonet-v12.md).


## <a name="prerequisites"></a>Conditions préalables

Pour mettre à niveau un serveur V12 avec PowerShell, vous devez disposer de la dernière PowerShell Azure installé et en cours d’exécution. Pour plus d’informations, voir [comment installer et configurer Azure PowerShell](../powershell-install-configure.md).


## <a name="configure-your-credentials-and-select-your-subscription"></a>Configurer vos informations d’identification, puis sélectionnez votre abonnement

Pour exécuter les applets de commande PowerShell par rapport à votre abonnement Azure, vous devez tout d’abord établir l’accès à votre compte Azure. Exécutez la commande suivante et s’affiche avec un écran de connexion à entrer vos informations d’identification. Utiliser la même adresse de messagerie et mot de passe que vous utilisez pour vous connecter au portail Azure.

    Add-AzureRmAccount

Après la connexion avec succès, vous devriez voir certaines informations sur l’écran qui inclut l’Id vous connecté à l’aide et les abonnements Azure que vous avez accès.

Pour sélectionner l’abonnement que vous voulez travailler avec vous devez votre Id (**-SubscriptionId**) de l’abonnement ou d’abonnement nommez (**-SubscriptionName**). Vous pouvez la copier à partir de l’étape précédente, ou si vous avez plusieurs abonnements, vous pouvez exécuter l’applet de commande **Get-AzureRmSubscription** et copiez les informations d’abonnement de votre choix dans le jeu de résultats.

Avec vos informations d’abonnement pour définir votre abonnement actuel, exécutez l’applet de commande suivante :

    Set-AzureRmContext -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

Les commandes suivantes seront exécutées sur l’abonnement que vous venez de sélectionner ci-dessus.

## <a name="get-recommendations"></a>Afficher des recommandations

Pour obtenir la recommandation pour le serveur de mise à niveau exécutez l’applet de commande suivante :

    $hint = Get-AzureRmSqlServerUpgradeHint -ResourceGroupName “resourcegroup1” -ServerName “server1”

Pour plus d’informations, voir [créer un pool de base de données élastique](sql-database-elastic-pool-create-portal.md) et [base de données SQL Azure tarifs recommandations niveau](sql-database-service-tier-advisor.md).



## <a name="start-the-upgrade"></a>Démarrer la mise à niveau

Pour commencer la mise à niveau du serveur, exécutez l’applet de commande suivante :

    Start-AzureRmSqlServerUpgrade -ResourceGroupName “resourcegroup1” -ServerName “server1” -ServerVersion 12.0 -DatabaseCollection $hint.Databases -ElasticPoolCollection $hint.ElasticPools  


Lorsque vous exécutez le processus de mise à niveau de cette commande commencera. Vous pouvez personnaliser la sortie de la recommandation et fournir la recommandation modifiée à cette applet de commande.


## <a name="upgrade-a-server"></a>Mise à niveau un serveur


    # Adding the account
    #
    Add-AzureRmAccount

    # Setting the variables
    #
    $SubscriptionName = 'YOUR_SUBSCRIPTION'
    $ResourceGroupName = 'YOUR_RESOURCE_GROUP'
    $ServerName = 'YOUR_SERVER'

    # Selecting the right subscription
    #
    Set-AzureRmContext -SubscriptionName $SubscriptionName

    # Getting the upgrade recommendations
    #
    $hint = Get-AzureRmSqlServerUpgradeHint -ResourceGroupName $ResourceGroupName -ServerName $ServerName

    # Starting the upgrade process
    #
    Start-AzureRmSqlServerUpgrade -ResourceGroupName $ResourceGroupName -ServerName $ServerName -ServerVersion 12.0 -DatabaseCollection $hint.Databases -ElasticPoolCollection $hint.ElasticPools  


## <a name="custom-upgrade-mapping"></a>Mise à niveau personnalisée mappage

Si les recommandations ne sont pas adaptées à votre serveur et étude de cas, vous pouvez choisir comment vos bases de données sont mis à niveau et pouvant établir une correspondance entre les bases de données unique ou élastiques.

ElasticPoolCollection et sur DatabaseCollection sont facultatives :

    # Creating elastic pool mapping
    #
    $elasticPool = New-Object -TypeName Microsoft.Azure.Management.Sql.Models.UpgradeRecommendedElasticPoolProperties
    $elasticPool.DatabaseDtuMax = 100
    $elasticPool.DatabaseDtuMin = 0
    $elasticPool.Dtu = 800
    $elasticPool.Edition = "Standard"
    $elasticPool.DatabaseCollection = ("DB1", “DB2”, “DB3”, “DB4”)
    $elasticPool.Name = "elasticpool_1"
    $elasticPool.StorageMb = 800

    # Creating single database mapping for 2 databases. DBMain1 mapped to S0 and DBMain2 mapped to S2
    #
    $databaseMap1 = New-Object -TypeName Microsoft.Azure.Management.Sql.Models.UpgradeDatabaseProperties
    $databaseMap1.Name = "DBMain1"
    $databaseMap1.TargetEdition = "Standard"
    $databaseMap1.TargetServiceLevelObjective = "S0"

    $databaseMap2 = New-Object -TypeName Microsoft.Azure.Management.Sql.Models.UpgradeDatabaseProperties
    $databaseMap2.Name = "DBMain2"
    $databaseMap2.TargetEdition = "Standard"
    $databaseMap2.TargetServiceLevelObjective = "S2"

    # Starting the upgrade
    #
    Start-AzureRmSqlServerUpgrade –ResourceGroupName resourcegroup1 –ServerName server1 -ServerVersion 12.0 -DatabaseCollection @($databaseMap1, $databaseMap2) -ElasticPoolCollection @($elasticPool)



## <a name="monitor-databases-after-upgrading-to-sql-database-v12"></a>Surveiller les bases de données après la mise à niveau vers V12 de base de données SQL


Après la mise à niveau, il est recommandé pour contrôler la base de données active pour vous assurer que les applications en cours d’exécution lors de l’exécution de votre choix et optimiser l’utilisation de selon vos besoins.

En outre, à l’analyse des bases de données vous pouvez surveiller des pools élastique de base de données [à l’aide du portail](sql-database-elastic-pool-manage-portal.md) ou avec [PowerShell](sql-database-elastic-pool-manage-powershell.md)


**Données de consommation de ressources :** Pour les bases de données basique, Standard et Premium données de consommation de ressource sont disponibles via le [sys.dm_ db_ resource_stats](http://msdn.microsoft.com/library/azure/dn800981.aspx) vue dans la base de données utilisateur. Cette vue offre près des informations de consommation des ressources en temps réel à précision deuxième 15 pour l’heure précédente d’opération. La consommation de pourcentage DTU pour un intervalle est calculée comme étant la consommation pourcentage maximal des dimensions processeur, IO et journal. Voici une requête pour calculer la consommation de pourcentage moyenne DTU sur la dernière heure :

    SELECT end_time
         , (SELECT Max(v)
             FROM (VALUES (avg_cpu_percent)
                         , (avg_data_io_percent)
                         , (avg_log_write_percent)
           ) AS value(v)) AS [avg_DTU_percent]
    FROM sys.dm_db_resource_stats
    ORDER BY end_time DESC;

Informations supplémentaires :

- [Conseils pour les performances de base de données SQL azure des bases de données unique](http://msdn.microsoft.com/library/azure/dn369873.aspx).
- [Considérations prix et de performances d’un pool élastique de base de données](sql-database-elastic-pool-guidance.md).
- [Base de données SQL Azure à l’aide des vues de gestion dynamiques de surveillance](sql-database-monitoring-with-dmvs.md)



**Alertes :** Configurer des « Alertes » dans le portail Azure afin de vous avertir lorsque la consommation DTU pour une base de données mis à niveau approche certaine niveau élevé. Alertes de base de données peuvent être configurés dans le portail Azure pour différents indicateurs de performance comme DTU, processeur, IO et journal. Accédez à votre base de données et sélectionnez **règles d’alerte** dans la carte de **paramètres** .

Par exemple, vous pouvez configurer un message d’alerte sur « Pourcentage DTU » si la valeur de pourcentage moyenne DTU est supérieure à 75 % sur les cinq minutes. Pour plus d’informations sur la façon de configurer les notifications d’alerte, reportez-vous à [recevoir des notifications d’alerte](../monitoring-and-diagnostics/insights-receive-alert-notifications.md) .



## <a name="next-steps"></a>Étapes suivantes

- [Créer un pool de base de données élastique](sql-database-elastic-pool-create-portal.md) et ajouter tout ou partie des bases de données dans le pool.
- [Modifier le niveau de service niveau et les performances de votre base de données](sql-database-scale-up.md).



## <a name="related-information"></a>Informations connexes

- [Get-AzureRmSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603582.aspx)
- [Démarrage AzureRmSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt619403.aspx)
- [Stop AzureRmSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603589.aspx)
