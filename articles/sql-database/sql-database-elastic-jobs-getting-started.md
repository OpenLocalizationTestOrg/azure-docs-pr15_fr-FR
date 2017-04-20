<properties
    pageTitle="Mise en route des tâches de base de données élastique"
    description="comment utiliser les tâches de base de données élastique"
    services="sql-database"
    documentationCenter=""  
    manager="jhubbard"
    authors="ddove"/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="ddove" />

# <a name="getting-started-with-elastic-database-jobs"></a>Mise en route des tâches de base de données élastique

Tâches de base de données élastiques (preview) pour la base de données SQL Azure permet de vous permettent de fiabilité exécutez des scripts T-SQL qui s’étalent sur plusieurs bases de données lors de la nouvelle tentative et garanties achèvement éventuel automatiquement. Pour plus d’informations sur la fonctionnalité de travail élastique de base de données, voir la [page Vue d’ensemble de fonctionnalité](sql-database-elastic-jobs-overview.md).

Cette rubrique étend l’échantillon figurant dans [prise en main des outils de base de données élastique](sql-database-elastic-scale-get-started.md). Une fois terminé, vous allez : Découvrez comment créer et gérer des travaux qui gérer un groupe de bases de données liées. Il n’est pas nécessaire d’utiliser les outils d’échelle élastique pour tirer parti des avantages des travaux élastique.

## <a name="prerequisites"></a>Conditions préalables

Téléchargez et exécutez la [mise en route d’exemples d’outils de base de données élastique](sql-database-elastic-scale-get-started.md).

## <a name="create-a-shard-map-manager-using-the-sample-app"></a>Créer un partagé à l’aide de l’application exemple le Gestionnaire de carte

Ici que vous allez créer un mappage d’éclater manager avec plusieurs milieu des fragments, suivi d’insertion de données en au milieu des fragments. Si vous avez déjà configuré avec des données sharded qu’ils milieu des fragments, vous pouvez ignorer les étapes suivantes et passer à la section suivante.

1. Créer et exécuter l’application exemple **prise en main des outils de base de données élastique** . Suivez les étapes avant l’étape 7 dans la section [télécharger et exécute l’exemple d’application](sql-database-elastic-scale-get-started.md#Getting-started-with-elastic-database-tools). À la fin de l’étape 7, vous verrez l’invite de commande suivante :

    ![invite de commandes][1]

2.  Dans la fenêtre de commande, tapez « 1 », puis appuyez sur **entrée**. Cela crée l’éclater Gestionnaire de carte et ajoute deux milieu des fragments sur le serveur. Tapez « 3 », puis appuyez sur **entrée**. Répétez cette action quatre fois. Cela permet d’insérer des lignes de données exemple dans votre milieu des fragments.

3.  Le [Portail Azure](https://portal.azure.com) doit afficher trois nouvelles bases de données de votre serveur v12 :

    ![Confirmation de Visual Studio][2]

    À ce stade, nous allons créer une collection de base de données personnalisée qui reflète toutes les bases de données dans la carte partagé. Cela permet de créer et exécuter une tâche qui ajouter une nouvelle table sur milieu des fragments.

Ici nous serait généralement créer une cible de carte partagé, à l’aide de l’applet de commande **New-AzureSqlJobTarget** . La base de données Gestionnaire de carte éclater doit être définie comme cible de base de données et puis la carte éclater spécifique est indiquée comme cible. À la place, nous allons répertorier toutes les bases de données sur le serveur et ajouter les bases de données à la nouvelle collection personnalisée à l’exception de base de données principale.

##<a name="creates-a-custom-collection-and-adds-all-databases-in-the-server-to-the-custom-collection-target-with-the-exception-of-master"></a>Crée une collection de sites personnalisée et ajoute toutes les bases de données sur le serveur à la cible de collection de sites personnalisée à l’exception de masque.


    $customCollectionName = "dbs_in_server"
    New-AzureSqlJobTarget -CustomCollectionName $customCollectionName 
    $ResourceGroupName = "ddove_samples"
    $ServerName = "samples"
    $dbsinserver = Get-AzureRMSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName 
    $dbsinserver | %{
    $currentdb = $_.DatabaseName 
    $ErrorActionPreference = "Stop"
    Write-Output ""

    Try
    {
       New-AzureSqlJobTarget -ServerName $ServerName -DatabaseName $currentdb | Write-Output
    }
    Catch 
    {
        $ErrorMessage = $_.Exception.Message
        $ErrorCategory = $_.CategoryInfo.Reason
                
        if ($ErrorCategory -eq 'UniqueConstraintViolatedException')
        {
             Write-Host $currentdb "is already a database target." 
        }

        else
        {
            throw $_
        }
    
    }

    Try
    {
        if ($currentdb -eq "master")
        {
            Write-Host $currentdb "will not be added custom collection target" $CustomCollectionName "."
        }

        else
        {
            Add-AzureSqlJobChildTarget -CustomCollectionName $CustomCollectionName -ServerName $ServerName -DatabaseName $currentdb
            Write-Host $currentdb "was added to" $CustomCollectionName "."
        }

    }
    Catch 
    {
        $ErrorMessage = $_.Exception.Message
        $ErrorCategory = $_.CategoryInfo.Reason

        if ($ErrorCategory -eq 'UniqueConstraintViolatedException')
        {
             Write-Host $currentdb "is already in the custom collection target" $CustomCollectionName"."
        }

        else
        {
            throw $_
        }
    }
    $ErrorActionPreference = "Continue"
}
    
## <a name="create-a-t-sql-script-for-execution-across-databases"></a>Créer un Script T-SQL pour une exécution sur bases de données

    $scriptName = "NewTable"
    $scriptCommandText = "
    IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'Test')
    BEGIN
        CREATE TABLE Test(
            TestId INT PRIMARY KEY IDENTITY,
            InsertionTime DATETIME2
        );
    END
    GO
    INSERT INTO Test(InsertionTime) VALUES (sysutcdatetime());
    GO"

    $script = New-AzureSqlJobContent -ContentName $scriptName -CommandText $scriptCommandText
    Write-Output $script

##<a name="create-the-job-to-execute-a-script-across-the-custom-group-of-databases"></a>Créer la tâche pour exécuter un script dans le groupe personnalisé des bases de données

    $jobName = "create on server dbs"
    $scriptName = "NewTable"
    $customCollectionName = "dbs_in_server"
    $credentialName = "ddove66"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $scriptName -TargetId $target.TargetId
    Write-Output $job


##<a name="execute-the-job"></a>Exécuter la tâche 

Le script PowerShell suivant peut être utilisé pour exécuter une tâche existante :

Mettre à jour la variable suivante pour refléter le nom du travail souhaité à exécuter :

    $jobName = "create on server dbs"
    $jobExecution = Start-AzureSqlJobExecution -JobName $jobName 
    Write-Output $jobExecution
 
## <a name="retrieve-the-state-of-a-single-job-execution"></a>Récupérer l’état de l’exécution d’une seule tâche

Utiliser l’applet de commande **Get-AzureSqlJobExecution** même avec le paramètre **IncludeChildren** pour afficher l’état d’exécution des travaux enfant, à savoir l’état pour chaque exécution du travail sur chaque base de données ciblée par la tâche spécifique.

    $jobExecutionId = "{Job Execution Id}"
    $jobExecutions = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId -IncludeChildren
    Write-Output $jobExecutions 

## <a name="view-the-state-across-multiple-job-executions"></a>Afficher l’état au sein de plusieurs exécutions de travail

L’applet de commande **Get-AzureSqlJobExecution** comporte plusieurs paramètres facultatifs qui peuvent être utilisés pour afficher plusieurs exécutions de travail, filtrées par le biais paramètres fournis. La section suivante montre quelques façons d’utiliser Get-AzureSqlJobExecution possibles :

Récupérer toutes les exécutions de travail actif de niveau supérieur :

    Get-AzureSqlJobExecution

Récupérer toutes les exécutions de travail de niveau supérieur, y compris les exécutions d’inactivité de la tâche :

    Get-AzureSqlJobExecution -IncludeInactive

Récupérer toutes les exécutions de travail enfant d’un ID de l’exécution de tâche fourni, y compris les exécutions d’inactivité de la tâche :

    $parentJobExecutionId = "{Job Execution Id}"
    Get-AzureSqlJobExecution -AzureSqlJobExecution -JobExecutionId $parentJobExecutionId –IncludeInactive -IncludeChildren

Récupérer toutes les exécutions de travail créées à l’aide d’un échéancier / combinaison, y compris les travaux inactifs du travail :

    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    Get-AzureSqlJobExecution -JobName $jobName -ScheduleName $scheduleName -IncludeInactive

Récupérer une carte éclater spécifiée, y compris les travaux inactifs de ciblage de toutes les tâches :

    $shardMapServerName = "{Shard Map Server Name}"
    $shardMapDatabaseName = "{Shard Map Database Name}"
    $shardMapName = "{Shard Map Name}"
    $target = Get-AzureSqlJobTarget -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapServerName -ShardMapName $shardMapName
    Get-AzureSqlJobExecution -TargetId $target.TargetId –IncludeInactive

Récupérer une collection personnalisée spécifiée, y compris les travaux inactifs de ciblage de toutes les tâches :

    $customCollectionName = "{Custom Collection Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    Get-AzureSqlJobExecution -TargetId $target.TargetId –IncludeInactive
 
Récupérer la liste des exécutions de tâches de projet au sein de l’exécution d’une tâche spécifique :

    $jobExecutionId = "{Job Execution Id}"
    $jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
    Write-Output $jobTaskExecutions 

Récupérer les détails de l’exécution de tâches de projet :

Le script PowerShell suivant peut être utilisé pour afficher les détails d’une tâche l’exécution des tâches, qui est particulièrement utile lors du débogage des erreurs d’exécution.

    $jobTaskExecutionId = "{Job Task Execution Id}"
    $jobTaskExecution = Get-AzureSqlJobTaskExecution -JobTaskExecutionId $jobTaskExecutionId
    Write-Output $jobTaskExecution

## <a name="retrieve-failures-within-job-task-executions"></a>Récupérer des échecs dans les exécutions de tâche de projet

L’objet JobTaskExecution inclut une propriété pour le cycle de vie de la tâche ainsi que d’une propriété de Message. Si une tâche l’exécution du travail a échoué, la propriété du cycle de vie est fixée à *Échec* et la propriété Message est fixée au message d’exception qui en résulte et sa pile. Si une tâche n’a pas réussi, il est important afficher les détails des tâches de projet n’a pas réussi pour une tâche donnée.

    $jobExecutionId = "{Job Execution Id}"
    $jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
    Foreach($jobTaskExecution in $jobTaskExecutions) 
        {
        if($jobTaskExecution.Lifecycle -ne 'Succeeded')
            {
            Write-Output $jobTaskExecution
            }
        }

## <a name="waiting-for-a-job-execution-to-complete"></a>En attente de l’exécution d’une tâche Terminer

Le script PowerShell suivant peut servir à attendre une tâche à effectuer :

    $jobExecutionId = "{Job Execution Id}"
    Wait-AzureSqlJobExecution -JobExecutionId $jobExecutionId 

## <a name="create-a-custom-execution-policy"></a>Créer une stratégie d’exécution personnalisés

Élastiques tâches de base de données prend en charge la création de stratégies d’exécution personnalisés qui peuvent être appliqués au démarrage de tâches.
  
Stratégies d’exécution autoriser actuellement pour la définition de :

* Nom : L’identificateur de la stratégie d’exécution.
* Délai d’attente : Durée totale avant un travail sera annulé par élastique tâches de base de données.
* Intervalle initial : Délai au terme duquel première nouvelle tentative.
* Intervalle maximal : Extrémité d’intervalles à utiliser.
* Réessayer intervalle intervalle Coefficient : Coefficient permet de calculer l’intervalle entre les essais suivant.  La formule suivante est utilisée : (Initial temporisation) * Math.pow (intervalle intervalle Coefficient (), (nombre de tentatives de) - 2). 
* Nombre maximal de tentatives : Le nombre maximal de nouvelles tentatives tente d’effectuer au sein d’une tâche.

La stratégie d’exécution par défaut utilise les valeurs suivantes :

* Name : Stratégie d’exécution par défaut
* Délai d’expiration de la tâche : 1 semaine
* Intervalle initial : 100 millisecondes
* Intervalle maximal : 30 minutes
* Réessayer Coefficient intervalle : 2
* Nombre maximal de tentatives : 2 147 483 647

Créer la stratégie d’exécution de votre choix :

    $executionPolicyName = "{Execution Policy Name}"
    $initialRetryInterval = New-TimeSpan -Seconds 10
    $jobTimeout = New-TimeSpan -Minutes 30
    $maximumAttempts = 999999
    $maximumRetryInterval = New-TimeSpan -Minutes 1
    $retryIntervalBackoffCoefficient = 1.5
    $executionPolicy = New-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval -RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
    Write-Output $executionPolicy

### <a name="update-a-custom-execution-policy"></a>Mettre à jour une stratégie d’exécution personnalisés

Mettre à jour la stratégie d’exécution de votre choix pour mettre à jour :

    $executionPolicyName = "{Execution Policy Name}"
    $initialRetryInterval = New-TimeSpan -Seconds 15
    $jobTimeout = New-TimeSpan -Minutes 30
    $maximumAttempts = 999999
    $maximumRetryInterval = New-TimeSpan -Minutes 1
    $retryIntervalBackoffCoefficient = 1.5
    $updatedExecutionPolicy = Set-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval -RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
    Write-Output $updatedExecutionPolicy
 
## <a name="cancel-a-job"></a>Annulation d’une tâche

Élastique tâches de base de données prend en charge les demandes d’annulation de tâches.  Si les tâches de base de données élastique détecte une demande d’annulation pour une tâche en cours d’exécution, il tentera arrêter le travail.

Il existe deux façons différentes qu’élastique tâches de base de données peut effectuer une annulation :

1. Annulation en cours d’exécution des tâches : si une annulation est détectée pendant une tâche est en cours d’exécution, une annulation sera retentée au sein de l’aspect de la tâche en cours d’exécution.  Par exemple : s’il existe une requête longue en cours d’exécution lors de la tentative d’une annulation, il y ait une tentative d’annulation de la requête.
2. Annulation tâche tentatives : Si une annulation est détectée par le thread de contrôle avant de lancer une tâche pour l’exécution, le thread de contrôle sera Évitez lancement de la tâche et déclarer la demande comme étant annulée.

Si une tâche demande d’annulation pour une tâche parent, la demande d’annulation est respectée pour le travail parent et pour l’ensemble de ses tâches enfants.
 
Pour envoyer une demande d’annulation, utilisez l’applet de commande **Stop AzureSqlJobExecution** et définissez le paramètre **JobExecutionId** .

    $jobExecutionId = "{Job Execution Id}"
    Stop-AzureSqlJobExecution -JobExecutionId $jobExecutionId

## <a name="delete-a-job-by-name-and-the-jobs-history"></a>Suppression d’une tâche par nom et l’historique du travail

Tâches de base de données élastiques prend en charge asynchrone suppression des travaux. Une tâche peut être marquée pour la suppression et le système supprime la tâche et son historique de travail après que l’exécution de toutes les tâches terminées pour la tâche. Le système annule pas automatiquement les exécutions de travail actif.  

En revanche, arrêter AzureSqlJobExecution doit être appelée pour annuler l’exécution des travaux active.

Pour déclencher la suppression de la tâche, utilisez l’applet de commande **Supprimer AzureSqlJob** et définissez le paramètre **JobName** .

    $jobName = "{Job Name}"
    Remove-AzureSqlJob -JobName $jobName
 
## <a name="create-a-custom-database-target"></a>Créer une cible de la base de données personnalisée
Cibles de base de données personnalisée peuvent être définis dans les tâches de base de données élastique qui peuvent être utilisés pour l’exécution directement ou à inclure dans un groupe de base de données personnalisée. Dans la mesure où **de base de données élastique pools** ne sont pas encore directement pris en charge via les APIs PowerShell, vous créez simplement une base de données personnalisée cibles et base de données personnalisée collection de sites qui englobe tous les bases de données dans le pool.

Définir les variables suivantes pour refléter les informations de base de données souhaitée :

    $databaseName = "{Database Name}"
    $databaseServerName = "{Server Name}"
    New-AzureSqlJobDatabaseTarget -DatabaseName $databaseName -ServerName $databaseServerName 

## <a name="create-a-custom-database-collection-target"></a>Créer une cible de collection de sites de base de données personnalisée
Une cible de collection de sites de base de données personnalisée peut être définie pour activer l’exécution sur plusieurs cibles de base de données défini. Après la création d’un groupe de base de données, les bases de données peuvent être associés à la cible de la collection de sites personnalisée.

Définir les variables suivantes pour refléter la configuration cible souhaité collection personnalisée :

    $customCollectionName = "{Custom Database Collection Name}"
    New-AzureSqlJobTarget -CustomCollectionName $customCollectionName 

### <a name="add-databases-to-a-custom-database-collection-target"></a>Ajouter des bases de données à une cible de collection de sites de base de données personnalisée

Cibles de base de données peuvent être associés cibles de collection de sites de base de données personnalisée pour créer un groupe de bases de données. Chaque fois qu’une tâche est créée qui cible une cible de collection de sites de base de données personnalisée, elle est développée pour cibler les bases de données associées au groupe au moment de l’exécution.

Ajouter la base de données souhaité pour une collection de sites personnalisée spécifique :

    $serverName = "{Database Server Name}"
    $databaseName = "{Database Name}"
    $customCollectionName = "{Custom Database Collection Name}"
    Add-AzureSqlJobChildTarget -CustomCollectionName $customCollectionName -DatabaseName $databaseName -ServerName $databaseServerName 

#### <a name="review-the-databases-within-a-custom-database-collection-target"></a>Passez en revue les bases de données dans une cible de collection de sites de base de données personnalisée

Utiliser l’applet de commande **Get-AzureSqlJobTarget** pour récupérer les bases de données enfant dans une cible de collection de sites de base de données personnalisée. 
 
    $customCollectionName = "{Custom Database Collection Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $childTargets = Get-AzureSqlJobTarget -ParentTargetId $target.TargetId
    Write-Output $childTargets

### <a name="create-a-job-to-execute-a-script-across-a-custom-database-collection-target"></a>Créer une tâche pour exécuter un script sur une cible de collection de sites de base de données personnalisée

Utiliser l’applet de commande **New-AzureSqlJob** pour créer une tâche par rapport à un groupe de bases de données défini par une cible de collection de sites de base de données personnalisée. Élastiques tâches de base de données seront développer la tâche en plusieurs tâches enfant correspondant chacune à une base de données associé à la cible de collection de sites de base de données personnalisée et vérifiez que le script est exécuté sur chaque base de données. Là encore, il est important que les scripts sont idempotents être résistant aux nouvelles tentatives.

    $jobName = "{Job Name}"
    $scriptName = "{Script Name}"
    $customCollectionName = "{Custom Collection Name}"
    $credentialName = "{Credential Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $scriptName -TargetId $target.TargetId
    Write-Output $job

## <a name="data-collection-across-databases"></a>Collecte de données entre bases de données

**Tâches de base de données élastique** prend en charge l’exécution d’une requête sur un groupe de bases de données et envoie les résultats au tableau de base de données spécifiée. Le tableau peut être interrogé après le fait pour afficher les résultats de la requête à partir de chaque base de données. Fournit un mécanisme asynchrone pour exécuter une requête sur plusieurs bases de données. Cas d’échec comme une base de données est temporairement indisponible sont traitées automatiquement via tentatives.

La table de destination spécifié est automatiquement créée si elle n’existe pas encore, qui correspondent au schéma du jeu de résultats. Si l’exécution d’une script retourne plusieurs jeux de résultats, tâches de base de données élastique vous envoie uniquement la première personne à la table de destination fourni.

Le script PowerShell suivant peut être utilisé pour exécuter un script collecte ses résultats dans une table spécifiée. Ce script suppose qu’un script T-SQL a été créé qui retourne un jeu de résultats unique et une cible de collection de sites de base de données personnalisée a été créée.

Définir les options suivantes pour refléter le script désiré, informations d’identification et cible d’exécution :

    $jobName = "{Job Name}"
    $scriptName = "{Script Name}"
    $executionCredentialName = "{Execution Credential Name}"
    $customCollectionName = "{Custom Collection Name}"
    $destinationCredentialName = "{Destination Credential Name}"
    $destinationServerName = "{Destination Server Name}"
    $destinationDatabaseName = "{Destination Database Name}"
    $destinationSchemaName = "{Destination Schema Name}"
    $destinationTableName = "{Destination Table Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName

### <a name="create-and-start-a-job-for-data-collection-scenarios"></a>Créer et commencer à une tâche pour des scénarios de collecte de données
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $executionCredentialName -ContentName $scriptName -ResultSetDestinationServerName $destinationServerName -ResultSetDestinationDatabaseName $destinationDatabaseName -ResultSetDestinationSchemaName $destinationSchemaName -ResultSetDestinationTableName $destinationTableName -ResultSetDestinationCredentialName $destinationCredentialName -TargetId $target.TargetId
    Write-Output $job
    $jobExecution = Start-AzureSqlJobExecution -JobName $jobName
    Write-Output $jobExecution

## <a name="create-a-schedule-for-job-execution-using-a-job-trigger"></a>Créer une planification pour l’exécution du travail à l’aide d’un déclencheur de tâche

Le script PowerShell suivant pouvant être utilisé pour créer un calendrier récurrents. Ce script utilise un intervalle d’une minute, mais nouveau AzureSqlJobSchedule prend également en charge les paramètres - DayInterval, - HourInterval, - MonthInterval et - WeekInterval. Tableaux qui s’exécutent qu’une seule fois peuvent être créés en transmettant - unique.

Créer un nouvel horaire :

    $scheduleName = "Every one minute"
    $minuteInterval = 1
    $startTime = (Get-Date).ToUniversalTime()
    $schedule = New-AzureSqlJobSchedule -MinuteInterval $minuteInterval -ScheduleName $scheduleName -StartTime $startTime 
    Write-Output $schedule

### <a name="create-a-job-trigger-to-have-a-job-executed-on-a-time-schedule"></a>Créer un déclencheur de tâche pour une tâche s’exécute sur un calendrier

Un déclencheur de travail peut être défini pour une tâche exécutée en fonction d’un calendrier. Le script PowerShell suivant pouvant être utilisé pour créer un déclencheur de travail.

Définir les variables suivantes pour correspondre à la tâche souhaitée et le calendrier :

    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    $jobTrigger = New-AzureSqlJobTrigger -ScheduleName $scheduleName –JobName $jobName
    Write-Output $jobTrigger

### <a name="remove-a-scheduled-association-to-stop-job-from-executing-on-schedule"></a>Supprimer une association planifiée pour arrêter la tâche à partir de l’exécution de la planification

Pour arrêter l’exécution du travail récurrents via un déclencheur de travail, le déclencheur de travail peut être supprimé. Supprimer un déclencheur de tâche pour arrêter une tâche à partir d’en cours d’exécution en fonction d’une planification à l’aide de l’applet de commande **Supprimer AzureSqlJobTrigger** .

    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    Remove-AzureSqlJobTrigger -ScheduleName $scheduleName -JobName $jobName





## <a name="import-elastic-database-query-results-to-excel"></a>Importer des résultats de la requête de base de données élastique dans Excel

 Vous pouvez importer les résultats à partir d’une requête vers un fichier Excel.

1. Lancez Excel 2013.
2.  Accédez au ruban **données** .
3.  Cliquez sur **à partir d’autres Sources** , puis cliquez sur **à partir de SQL Server**.

    ![Importation d’Excel à partir d’autres sources][5]
4.  Dans l' **Assistant connexion de données** , tapez les informations d’identification serveur nom et la connexion. Cliquez sur **suivant**.
5.  Dans la boîte de dialogue **Sélectionner la base de données qui contient les données souhaitées**, sélectionnez la base de données **ElasticDBQuery** .
6.  Sélectionnez la table **Customers** dans la liste et cliquez sur **suivant**. Puis cliquez sur **Terminer**.
7.  Dans l’écran **Importer des données** , sous **Sélectionner la manière dont vous souhaitez afficher ces données dans votre classeur**, sélectionnez la **Table** , puis cliquez sur **OK**.

Toutes les lignes de la table **clients** , stockées dans différents milieu des fragments remplissent la feuille de calcul Excel.

## <a name="next-steps"></a>Étapes suivantes
Vous pouvez désormais utiliser les fonctions de données d’Excel. Utiliser la chaîne de connexion avec votre nom de serveur, nom de la base de données et les informations d’identification pour vous connecter vos outils d’intégration BI et les données à la base de données de requête élastique. Assurez-vous que SQL Server est pris en charge en tant que source de données pour votre outil. Reportez-vous à la base de données de la requête élastique et des tables externes comme n’importe quel autre base de données SQL Server et SQL Server que vous voulez vous connecter à avec votre outil.

### <a name="cost"></a>Coût
Il est sans frais supplémentaires pour l’utilisation de la fonctionnalité de requête de base de données élastique. Toutefois, pour le moment cette fonctionnalité est disponible uniquement sur les bases de données premium comme point de fin, mais au milieu des fragments peuvent être de n’importe quel niveau de service.

Informations de tarification voir [Détails sur tarification pour la base de données SQL](https://azure.microsoft.com/pricing/details/sql-database/).


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-query-getting-started/cmd-prompt.png
[2]: ./media/sql-database-elastic-query-getting-started/portal.png
[3]: ./media/sql-database-elastic-query-getting-started/tiers.png
[4]: ./media/sql-database-elastic-query-getting-started/details.png
[5]: ./media/sql-database-elastic-query-getting-started/exel-sources.png
<!--anchors-->
