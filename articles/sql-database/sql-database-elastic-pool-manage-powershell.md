<properties 
    pageTitle="Gérer un pool de base de données élastique (PowerShell) | Microsoft Azure" 
    description="Découvrez comment utiliser PowerShell pour gérer un pool élastique de base de données."  
    services="sql-database" 
    documentationCenter="" 
    authors="srinia" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management" 
    ms.date="06/22/2016"
    ms.author="srinia"/>

# <a name="monitor-and-manage-an-elastic-database-pool-with-powershell"></a>Surveiller et gérer un pool de base de données élastique avec PowerShell 

> [AZURE.SELECTOR]
- [Portail Azure](sql-database-elastic-pool-manage-portal.md)
- [PowerShell](sql-database-elastic-pool-manage-powershell.md)
- [C#](sql-database-elastic-pool-manage-csharp.md)
- [T-SQL](sql-database-elastic-pool-manage-tsql.md)

Gérer un [pool élastique de base de données](sql-database-elastic-pool.md) à l’aide des applets de commande PowerShell. 

Pour les codes d’erreur courants, voir [codes d’erreur SQL pour les applications clientes de base de données SQL : erreur de connexion et d’autres problèmes de base de données](sql-database-develop-error-messages.md).

Vous trouverez des valeurs pour des pools dans [les limites de stockage et eDTU](sql-database-elastic-pool.md#eDTU-and-storage-limits-for-elastic-pools-and-elastic-databases). 

## <a name="prerequisites"></a>Conditions préalables

* Azure PowerShell 1.0 ou version ultérieure. Pour plus d’informations, voir [comment installer et configurer Azure PowerShell](../powershell-install-configure.md).
* Des pools élastique de base de données sont uniquement disponibles avec les serveurs V12 de base de données SQL. Si vous avez un serveur SQL de base de données 11, [utiliser PowerShell pour mettre à niveau vers V12 et créer un pool](sql-database-upgrade-server-portal.md) en une seule étape.


## <a name="move-a-database-into-an-elastic-pool"></a>Déplacer une base de données dans un pool élastique

Vous pouvez déplacer une base de données ou disparition un pool avec [Jeu de AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433.aspx). 

    Set-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

## <a name="change-performance-settings-of-a-pool"></a>Modifier les paramètres de performances d’un pool

Lorsque les performances sont affectées, vous pouvez modifier les paramètres du pool pour suivre la croissance. Utiliser l’applet de commande [Set-AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt603511.aspx) . Définissez le paramètre - Dtu à l’eDTUs par pool. Voir [les limites de stockage et eDTU](sql-database-elastic-pool.md#eDTU-and-storage-limits-for-elastic-pools-and-elastic-databases) pour les valeurs possibles.  

    Set-AzureRmSqlElasticPool –ResourceGroupName “resourcegroup1” –ServerName “server1” –ElasticPoolName “elasticpool1” –Dtu 1200 –DatabaseDtuMax 100 –DatabaseDtuMin 50 


## <a name="get-the-status-of-pool-operations"></a>Obtenir le statut d’opérations sur les listes

Création d’un pool peut prendre de temps. Pour suivre l’état d’opérations sur les listes, y compris la création et mises à jour, utilisez l’applet de commande [Get-AzureRmSqlElasticPoolActivity](https://msdn.microsoft.com/library/azure/mt603812.aspx) .

    Get-AzureRmSqlElasticPoolActivity –ResourceGroupName “resourcegroup1” –ServerName “server1” –ElasticPoolName “elasticpool1” 


## <a name="get-the-status-of-moving-an-elastic-database-into-and-out-of-a-pool"></a>Obtenir le statut de déplacement d’une base de données élastique dans et se déconnecter d’un pool

Déplacement d’une base de données peut prendre un temps. Effectuer le suivi d’un état de déplacement à l’aide de l’applet de commande [Get-AzureRmSqlDatabaseActivity](https://msdn.microsoft.com/library/azure/mt603687.aspx) .

    Get-AzureRmSqlDatabaseActivity -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

## <a name="get-resource-usage-data-for-a-pool"></a>Obtenir des données d’utilisation des ressources pour un pool

Indicateurs qui peuvent être récupérés sous forme de pourcentage de la limite du pool ressource :   


| Métrique nom | Description |
| :-- | :-- |
| processeur\_pour cent | Taux d’utilisation moyenne cluster sous forme de pourcentage de la limite du pool. |
| physique\_données\_lire\_pour cent | Utilisation d’e/s moyenne sous forme de pourcentage en fonction de la limite du pool. |
| journal\_écrire\_pour cent | L’utilisation des ressources d’écriture moyenne sous forme de pourcentage de la limite du pool. | 
| DTU\_consommation\_pour cent | Utilisation d’eDTU moyenne en pourcentage de la limite d’eDTU pour le pool | 
| stockage\_pour cent | Moyenne de l’utilisation du stockage sous forme de pourcentage de la limite de stockage du pool. |  
| travailleurs\_pour cent | Travailleurs simultanées maximales (requêtes) sous forme de pourcentage en fonction de la limite du pool. |  
| sessions\_pour cent | Nombre maximal de sessions simultané sous forme de pourcentage en fonction de la limite du pool. | 
| eDTU_limit | Max pool élastique DTU paramètre actuel pour ce pool élastique pendant cet intervalle. |
| stockage\_limite | Limite de stockage maximale du pool élastique en cours définie pour ce pool élastique en mégaoctets pendant cet intervalle. |
| eDTU\_utilisé | Moyenne eDTUs utilisé par le pool dans cet intervalle. |
| stockage\_utilisé | Moyens de stockage utilisé par le pool dans cet intervalle en octets |

**Périodes de précision/rétention indicateurs :**

* Données seront retournées en précision de 5 minutes.  
* Conservation des données est 35 jours.  

Cette applet de commande et API limite le nombre de lignes peuvent être récupérés dans un appel à 1000 lignes (environ 3 jours de données au niveau de précision de 5 minutes). Mais cette commande peut être appelée plusieurs fois avec des intervalles de temps différente début/fin pour récupérer des données plus 

Pour récupérer les mesures :

    $metrics = (Get-AzureRmMetric -ResourceId /subscriptions/<subscriptionId>/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015")  


## <a name="get-resource-usage-data-for-an-elastic-database"></a>Obtenir des données d’utilisation des ressources pour une base de données élastique

Ces API est les mêmes que l’API actuelles (V12) utilisés pour surveiller l’utilisation des ressources d’une base de données autonome, à l’exception de la différence sémantique suivante. 

Pour cette API métriques récupérées sont exprimés sous forme de pourcentage de la par eDTUs max (ou lettrine équivalent pour la mesure sous-jacent comme processeur, etc. IO) définie pour ce pool. Par exemple, 50 % d’utilisation d’un de ces indicateurs indique que la consommation de ressources spécifique est à 50 % de la limite de lettrine de base de données pour cette ressource dans la liste parent par. 

Pour récupérer les mesures :

    $metrics = (Get-AzureRmMetric -ResourceId /subscriptions/<subscriptionId>/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015") 

## <a name="add-an-alert-to-a-pool-resource"></a>Ajouter une alerte pour une ressource pool

Vous pouvez ajouter des règles d’alerte à des ressources pour envoyer des notifications par courrier électronique ou chaînes d’alerte aux [points de terminaison URL](https://msdn.microsoft.com/library/mt718036.aspx) lors de la ressource atteint un seuil de l’utilisation que vous avez configurées. Utiliser l’applet de commande Add-AzureRmMetricAlertRule. 

> [AZURE.IMPORTANT]L’utilisation des ressources pour des Pools élastique de surveillance a un retard d’au moins 20 minutes. Définir des alertes de moins de 30 minutes pour des Pools élastique n’est pas actuellement pris en charge. Toutes les alertes définir pour des Pools élastique par un point (paramètre appelé «-WindowSize » dans l’API PowerShell) de moins de 30 minutes ne peut pas être déclenchée. Assurez-vous que toutes les alertes que vous définissez pour des Pools élastique utilisent un point (WindowSize) de 30 minutes ou plus.

Cet exemple montre comment ajouter une alerte pour recevoir une notification lorsque la consommation d’un pool eDTU dépasse certain seuil.

    # Set up your resource ID configurations
    $subscriptionId = '<Azure subscription id>'      # Azure subscription ID
    $location =  '<location'                         # Azure region
    $resourceGroupName = '<resource group name>'     # Resource Group
    $serverName = '<server name>'                    # server name
    $poolName = '<elastic pool name>'                # pool name 

    #$Target Resource ID
    $ResourceID = '/subscriptions/' + $subscriptionId + '/resourceGroups/' +$resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/elasticpools/' + $poolName

    # Create an email action
    $actionEmail = New-AzureRmAlertRuleEmail -SendToServiceOwners -CustomEmail JohnDoe@contoso.com

    # create a unique rule name
    $alertName = $poolName + "- DTU consumption rule"

    # Create an alert rule for DTU_consumption_percent
    Add-AzureRMMetricAlertRule -Name $alertName -Location $location -ResourceGroup $resourceGroupName -TargetResourceId $ResourceID -MetricName "DTU_consumption_percent"  -Operator GreaterThan -Threshold 80 -TimeAggregationOperator Average -WindowSize 00:60:00 -Actions $actionEmail 

## <a name="add-alerts-to-all-databases-in-a-pool"></a>Ajouter des alertes à toutes les bases de données dans une liste

Vous pouvez ajouter des règles d’alerte pour une base de données dans un pool élastique pour envoyer des notifications par courrier électronique ou chaînes d’alerte pour les [points de terminaison URL](https://msdn.microsoft.com/library/mt718036.aspx) lorsqu’une ressource atteint un seuil d’utilisation défini par l’alerte. 

> [AZURE.IMPORTANT] L’utilisation des ressources pour des Pools élastique de surveillance a un retard d’au moins 20 minutes. Définir des alertes de moins de 30 minutes pour des Pools élastique n’est pas actuellement pris en charge. Toutes les alertes définir pour des Pools élastique par un point (paramètre appelé «-WindowSize » dans l’API PowerShell) de moins de 30 minutes ne peut pas être déclenchée. Assurez-vous que toutes les alertes que vous définissez pour des Pools élastique utilisent un point (WindowSize) de 30 minutes ou plus.

Cet exemple ajoute une alerte à chacun des bases de données dans un pool pour recevoir une notification lorsque la consommation de la base DTU dépasse certain seuil.

    # Set up your resource ID configurations
    $subscriptionId = '<Azure subscription id>'      # Azure subscription ID
    $location = '<location'                          # Azure region
    $resourceGroupName = '<resource group name>'     # Resource Group
    $serverName = '<server name>'                    # server name
    $poolName = '<elastic pool name>'                # pool name 

    # Get the list of databases in this pool.
    $dbList = Get-AzureRmSqlElasticPoolDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -ElasticPoolName $poolName

    # Create an email action
    $actionEmail = New-AzureRmAlertRuleEmail -SendToServiceOwners -CustomEmail JohnDoe@contoso.com

    # Get resource usage metrics for a database in an elastic database for the specified time interval.
    foreach ($db in $dbList)
    {
    $dbResourceId = '/subscriptions/' + $subscriptionId + '/resourceGroups/' + $resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/databases/' + $db.DatabaseName

    # create a unique rule name
    $alertName = $db.DatabaseName + "- DTU consumption rule"

    # Create an alert rule for DTU_consumption_percent
    Add-AzureRMMetricAlertRule -Name $alertName  -Location $location -ResourceGroup $resourceGroupName -TargetResourceId $dbResourceId -MetricName "dtu_consumption_percent"  -Operator GreaterThan -Threshold 80 -TimeAggregationOperator Average -WindowSize 00:60:00 -Actions $actionEmail

    # drop the alert rule
    #Remove-AzureRmAlertRule -ResourceGroup $resourceGroupName -Name $alertName
    } 



## <a name="collect-and-monitor-resource-usage-data-across-multiple-pools-in-a-subscription"></a>Collecter et analyser des données d’utilisation des ressources dans l’ensemble des pools multiples dans un abonnement

Lorsque vous avez un grand nombre de bases de données dans un abonnement, il est difficile à surveiller chaque pool élastique séparément. En revanche, les applets de commande PowerShell de base de données SQL et requêtes T-SQL peuvent être combinées pour collecter les données d’utilisation des ressources de plusieurs pools et leurs bases de données de surveillance et d’analyse de l’utilisation des ressources. Un [exemple d’implémentation](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools) d’un tel jeu des scripts powershell sont accessibles dans le référentiel exemples GitHub SQL Server ainsi que de la documentation relative à ce qu’il fait et comment l’utiliser.

Pour utiliser cet exemple d’implémentation suivez ces étapes présentées ci-après.


1. Télécharger les [scripts et à la documentation](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools):
2. Modifier les scripts pour votre environnement. Spécifiez un ou plusieurs des serveurs hébergés sur lequel pools élastiques.
3. Spécifier une base de données de télémétrie où les mesures collectés doivent être stockés. 
4. Personnaliser le script pour spécifier la durée d’exécution des scripts.

À un niveau élevé, les scripts procédez comme suit :

*   Énumère tous les serveurs d’un abonnement Azure donné (ou une liste spécifiée des serveurs).
*   S’exécute une tâche en arrière-plan pour chaque serveur. Le travail s’exécute dans une boucle à intervalles réguliers et collecte des données de télémétrie pour tous les pools sur le serveur. Il charge ensuite les informations collectées dans la base de données de télémétrie spécifié.
*   Affiche une liste des bases de données dans chaque pool pour collecter les données de l’utilisation de ressources de base de données. Il charge ensuite les informations collectées dans la base de données de télémétrie.

Les mesures collectés dans la base de données de télémétrie peuvent être analysées pour surveiller l’intégrité des pools élastiques et les bases de données qu’il contient. Le script installe également une fonction de valeur de la Table prédéfinie (table) dans la base de données de télémétrie pour vous aider agrégat les mesures pour une période spécifiée. Par exemple, les résultats de la fonction table peuvent être utilisés pour afficher « supérieure N élastiques pools avec le taux d’utilisation maximal eDTU dans une période donnée. » Vous pouvez également utiliser des outils analytiques comme Excel ou Power BI pour interroger et analyser les données collectées.

## <a name="example-retrieve-resource-consumption-metrics-for-a-pool-and-its-databases"></a>Exemple : récupèrent métriques de consommation de ressources pour un pool et ses bases de données

Cet exemple extrait la métrique de consommation d’un pool élastique donné et toutes ses bases de données. Les données collectées sont mis en forme et écrites dans un fichier au format .csv. Le fichier peut être parcouru avec Excel.

    $subscriptionId = '<Azure subscription id>'       # Azure subscription ID
    $resourceGroupName = '<resource group name>'             # Resource Group
    $serverName = <server name>                              # server name
    $poolName = <elastic pool name>                          # pool name
        
    # Login to Azure account and select the subscription.
    Login-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $subscriptionId
    
    # Get resource usage metrics for an elastic pool for the specified time interval.
    $startTime = '4/27/2016 00:00:00'  # start time in UTC
    $endTime = '4/27/2016 01:00:00'    # end time in UTC
    
    # Construct the pool resource ID and retrive pool metrics at 5 minute granularity.
    $poolResourceId = '/subscriptions/' + $subscriptionId + '/resourceGroups/' + $resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/elasticPools/' + $poolName
    $poolMetrics = (Get-AzureRmMetric -ResourceId $poolResourceId -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime $startTime -EndTime $endTime) 
    
    # Get the list of databases in this pool.
    $dbList = Get-AzureRmSqlElasticPoolDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -ElasticPoolName $poolName
    
    # Get resource usage metrics for a database in an elastic database for the specified time interval.
    $dbMetrics = @()
    foreach ($db in $dbList)
    {
        $dbResourceId = '/subscriptions/' + $subscriptionId + '/resourceGroups/' + $resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/databases/' + $db.DatabaseName
        $dbMetrics = $dbMetrics + (Get-AzureRmMetric -ResourceId $dbResourceId -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime $startTime -EndTime $endTime)
    }
    
    #Optionally you can format the metrics and output as .csv file using the following script block.
    $command = {
    param($metricList, $outputFile)

    # Format metrics into a table.
    $table = @()
    foreach($metric in $metricList) { 
      foreach($metricValue in $metric.MetricValues) {
        $sx = New-Object PSObject -Property @{
            Timestamp = $metricValue.Timestamp.ToString()
            MetricName = $metric.Name; 
            Average = $metricValue.Average;
            ResourceID = $metric.ResourceId 
          }
          $table = $table += $sx
      }
    }
    
    # Output the metrics into a .csv file.
    write-output $table | Export-csv -Path $outputFile -Append -NoTypeInformation
    }
    
    # Format and output pool metrics
    Invoke-Command -ScriptBlock $command -ArgumentList $poolMetrics,c:\temp\poolmetrics.csv
    
    # Format and output database metrics
    Invoke-Command -ScriptBlock $command -ArgumentList $dbMetrics,c:\temp\dbmetrics.csv



## <a name="latency-of-elastic-pool-operations"></a>Latence des opérations sur les listes élastique

- Modification de l’eDTUs min par base de données ou max eDTUs par base de données en général se termine en 5 minutes ou moins.
- Modification de l’eDTUs par pool dépend le montant total d’espace utilisé par toutes les bases de données dans le pool. Modifications moyenne 90 minutes ou moins par 100 Go. Par exemple, si l’espace total utilisé par toutes les bases de données dans le pool est 200 Go, puis le temps de latence escompté permettant de modifier l’eDTU pool par pool est de 3 heures inférieur ou égal à.

## <a name="migrate-from-v11-to-v12-servers"></a>Migrer à partir de 11 vers les serveurs V12

Applets de commande PowerShell sont disponibles pour démarrer, arrêter ou contrôler une mise à niveau vers V12 de base de données SQL Azure dans 11 ou tout autre version de pré-V12.

- [Mise à niveau vers V12 de base de données SQL à l’aide de PowerShell](sql-database-upgrade-server-powershell.md)

Pour des informations de référence sur ces applets de commande PowerShell, voir :


- [Get-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603582.aspx)
- [Démarrage AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt619403.aspx)
- [Stop AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603589.aspx)


L’applet de commande Stop signifie Annuler, placez le curseur pas. Il n’existe aucun moyen à reprendre une mise à niveau, différent de redémarrer à partir du début. L’applet de commande Stop efface et libère toutes les ressources appropriées.

## <a name="next-steps"></a>Étapes suivantes

- [Créer des travaux élastiques](sql-database-elastic-jobs-overview.md) Tâches élastiques vous permettent d’exécuter des scripts T-SQL par rapport à un nombre quelconque de bases de données dans le pool.
- Voir [mise à l’échelle avec la base de données SQL Azure](sql-database-elastic-scale-introduction.md): outils de base de données élastique permet de horizontale, de déplacer des données, de la requête, ou créer des transactions.
