<properties 
    pageTitle="Créer et gérer des tâches de base de données élastique à l’aide de PowerShell" 
    description="PowerShell utilisée pour gérer des pools de base de données SQL Azure" 
    services="sql-database" documentationCenter=""  
    manager="jhubbard" 
    authors="ddove"/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/27/2016" 
    ms.author="ddove" />

# <a name="create-and-manage-a-sql-database-elastic-database-jobs-using-powershell-preview"></a>Créer et gérer un travaux élastique de base de données SQL de base de données à l’aide de PowerShell (preview)

> [AZURE.SELECTOR]
- [Portail Azure](sql-database-elastic-jobs-create-and-manage.md)
- [PowerShell](sql-database-elastic-jobs-powershell.md)



Les APIs PowerShell pour les **tâches de base de données élastique** (dans l’aperçu), vous permettent de définir un groupe de bases de données par rapport à laquelle les scripts seront exécutera. Cet article vous explique comment créer et gérer les **tâches de base de données élastique** à l’aide des applets de commande PowerShell. Voir [vue d’ensemble des travaux élastique](sql-database-elastic-jobs-overview.md). 

## <a name="prerequisites"></a>Conditions préalables
* Un abonnement Azure. Pour une version d’évaluation gratuite, voir [version d’évaluation gratuite d’un mois](https://azure.microsoft.com/pricing/free-trial/).
* Un ensemble de bases de données créées avec les outils de base de données élastique. Voir [prise en main des outils de base de données élastique](sql-database-elastic-scale-get-started.md).
* PowerShell Azure. Pour plus d’informations, voir [comment installer et configurer Azure PowerShell](../powershell-install-configure.md).
* **Tâches de base de données élastique** Package PowerShell : afficher les [tâches de l’installation d’une base de données élastique](sql-database-elastic-jobs-service-installation.md)

### <a name="select-your-azure-subscription"></a>Sélectionnez votre abonnement Azure

Pour sélectionner l’abonnement que vous avez besoin de votre Id (**-SubscriptionId**) de l’abonnement ou d’abonnement nommez (**-SubscriptionName**). Si vous avez plusieurs abonnements, vous pouvez exécuter l’applet de commande **Get-AzureRmSubscription** et copiez les informations d’abonnement souhaité de l’ensemble de résultats. Une fois que vos informations d’abonnement, exécutez l’applet de commande suivante pour définir cet abonnement par défaut, à savoir la cible pour la création et gestion des travaux :

    Select-AzureRmSubscription -SubscriptionId {SubscriptionID}

L' [PowerShell ISE](https://technet.microsoft.com/library/dd315244.aspx) est recommandée pour l’utilisation de développer et exécuter des scripts PowerShell contre les tâches de base de données élastique.

## <a name="elastic-database-jobs-objects"></a>Objets de tâches de base de données élastiques

Le tableau suivant répertorie tous les types d’objet de **tâches de base de données élastique** ainsi que sa description et pertinents PowerShell APIs.

<table style="width:100%">
  <tr>
    <th>Type d’objet</th>
    <th>Description</th>
    <th>API PowerShell connexes</th>
  </tr>
  <tr>
    <td>Informations d’identification</td>
    <td>Nom d’utilisateur et mot de passe à utiliser lors de la connexion aux bases de données pour l’exécution de scripts ou l’application de DACPACs. <p>Le mot de passe est chiffré avant d’envoyer à et le stockage de la base de données élastique tâches de base de données.  Le mot de passe est déchiffré par le service élastique tâches de base de données via les informations d’identification créé et téléchargés à partir du script d’installation.</td>
    <td><p>Get-AzureSqlJobCredential</p>
    <p>Nouvelle AzureSqlJobCredential</p><p>Jeu de AzureSqlJobCredential</p></td></td>
  </tr>

  <tr>
    <td>Script</td>
    <td>Script Transact-SQL à utiliser pour l’exécution sur bases de données.  Le script doit être créé pour être équipotentes étant donné que le service tentera de l’exécution du script lors des échecs.
    </td>
    <td>
    <p>Get-AzureSqlJobContent</p>
    <p>Get-AzureSqlJobContentDefinition</p>
    <p>Nouvelle AzureSqlJobContent</p>
    <p>Jeu de AzureSqlJobContentDefinition</p>
    </td>
  </tr>

  <tr>
    <td>DACPAC</td>
    <td><a href="https://msdn.microsoft.com/library/ee210546.aspx">Application de couche données </a> package pouvant être appliqués entre bases de données.

    </td>
    <td>
    <p>Get-AzureSqlJobContent</p>
    <p>Nouvelle AzureSqlJobContent</p>
    <p>Jeu de AzureSqlJobContentDefinition</p>
    </td>
  </tr>
  <tr>
    <td>Base de données cible</td>
    <td>Nom de base de données et serveur pointant vers une base de données SQL Azure.

    </td>
    <td>
    <p>Get-AzureSqlJobTarget</p>
    <p>Nouvelle AzureSqlJobTarget</p>
    </td>
  </tr>
  <tr>
    <td>Éclater carte cible</td>
    <td>Combinaison d’une cible de la base de données et les informations d’identification à utiliser pour déterminer les informations stockées dans un mappage éclater élastique de base de données.
    </td>
    <td>
    <p>Get-AzureSqlJobTarget</p>
    <p>Nouvelle AzureSqlJobTarget</p>
    <p>Jeu de AzureSqlJobTarget</p>
    </td>
  </tr>
<tr>
    <td>Cible de la collection de sites personnalisée</td>
    <td>Groupe défini des bases de données à utiliser collectivement pour l’exécution.</td>
    <td>
    <p>Get-AzureSqlJobTarget</p>
    <p>Nouvelle AzureSqlJobTarget</p>
    </td>
  </tr>
<tr>
    <td>Collection de sites personnalisée enfant cible</td>
    <td>Cible de base de données qui est référencée à partir d’une collection de sites personnalisée.</td>
    <td>
    <p>AzureSqlJobChildTarget ajouter</p>
    <p>Supprimer AzureSqlJobChildTarget</p>
    </td>
  </tr>

<tr>
    <td>Tâche</td>
    <td>
    <p>Définition des paramètres d’une tâche peut être utilisé pour déclencher l’exécution ou pour répondre à un planning.</p>
    </td>
    <td>
    <p>Get-AzureSqlJob</p>
    <p>Nouvelle AzureSqlJob</p>
    <p>Jeu de AzureSqlJob</p>
    </td>
  </tr>

<tr>
    <td>Exécution du travail</td>
    <td>
    <p>Conteneur de tâches permettant de répondre soit exécuter un script ou en appliquant un DACPAC à une cible à l’aide des informations d’identification pour les connexions de base de données avec des erreurs gérées conformément à une stratégie d’exécution.</p>
    </td>
    <td>
    <p>Get-AzureSqlJobExecution</p>
    <p>Démarrage AzureSqlJobExecution</p>
    <p>Stop AzureSqlJobExecution</p>
    <p>Attente AzureSqlJobExecution</p>
  </tr>

<tr>
    <td>Exécution de tâches de projet</td>
    <td>
    <p>Unité de travail unique pour répondre à une tâche.</p>
    <p>Si une tâche ne peut pas correctement exécuter, le message d’exception qui en résulte est enregistré et une nouvelle tâche de projet correspondant est créée et exécutée conformément à la stratégie d’exécution spécifiée.</p></p>
    </td>
    <td>
    <p>Get-AzureSqlJobExecution</p>
    <p>Démarrage AzureSqlJobExecution</p>
    <p>Stop AzureSqlJobExecution</p>
    <p>Attente AzureSqlJobExecution</p>
  </tr>

<tr>
    <td>Stratégie d’exécution de tâche</td>
    <td>
    <p>Contrôles du travail délais d’exécution, nombre maximal de tentatives et des intervalles entre les tentatives.</p>
    <p>Tâches de base de données élastiques inclut une stratégie d’exécution de travail par défaut qui provoquer essentiellement infinies tentatives tâche des échecs de travail avec exponentielle intervalle d’intervalles entre chaque tentative de.</p>
    </td>
    <td>
    <p>Get-AzureSqlJobExecutionPolicy</p>
    <p>Nouvelle AzureSqlJobExecutionPolicy</p>
    <p>Jeu de AzureSqlJobExecutionPolicy</p>
    </td>
  </tr>

<tr>
    <td>Planification</td>
    <td>
    <p>Heure en fonction de spécification pour l’exécution d’avoir lieu sur un intervalle récurrents ou en une seule fois.</p>
    </td>
    <td>
    <p>Get-AzureSqlJobSchedule</p>
    <p>Nouvelle AzureSqlJobSchedule</p>
    <p>Jeu de AzureSqlJobSchedule</p>
    </td>
  </tr>

<tr>
    <td>Déclencheurs de travail</td>
    <td>
    <p>Un mappage entre une tâche et une planification pour déclencher l’exécution du travail en fonction de l’échéancier.</p>
    </td>
    <td>
    <p>Nouvelle AzureSqlJobTrigger</p>
    <p>Supprimer AzureSqlJobTrigger</p>
    </td>
  </tr>
</table>

## <a name="supported-elastic-database-jobs-group-types"></a>Types de groupe travaux élastique de base de données pris en charge
Le travail s’exécute des scripts Transact-SQL (T-SQL) ou l’application de DACPACs sur un groupe de bases de données. Lorsqu’une tâche est soumise à être exécuté sur un groupe de bases de données, la tâche « développe » l’en tâches enfant où chacun effectue l’exécution demandée par rapport à une seule base de données dans le groupe. 
 
Il existe deux types de groupes que vous pouvez créer : 

* Groupe [Carte éclater](sql-database-elastic-scale-shard-map-management.md) : lorsqu’une tâche est soumise pour cibler un mappage partagé, le travail le mappage partagé afin de déterminer son ensemble actuel de milieu des fragments des requêtes et crée puis des tâches pour chaque éclater enfant dans la carte partagé.
* Groupe de collection de sites personnalisée : un ensemble de bases de données personnalisées. Lorsqu’une tâche cible une collection de sites personnalisée, qu’il crée enfant travaux pour chaque base de données actuellement dans la collection de sites personnalisée.

## <a name="to-set-the-elastic-database-jobs-connection"></a>Pour définir la base de données élastique tâches de connexion

Une connexion doit être définie à la *base de données de contrôle* de travaux avant de procéder à l’aide de l’API de travaux. Cette applet de commande en cours d’exécution déclenche une fenêtre d’informations d’identification contextuel demandant le nom d’utilisateur et mot de passe créé lors de l’installation de tâches de base de données élastique. Tous les exemples présentés dans cette rubrique part du principe que cette première étape a déjà été effectuée.

Ouvrir une connexion à des tâches de base de données élastique :

    Use-AzureSqlJobConnection -CurrentAzureSubscription 

## <a name="encrypted-credentials-within-the-elastic-database-jobs"></a>Informations d’identification chiffrées dans les tâches de base de données élastique

Informations d’identification de la base de données peuvent être insérées dans la *base de données de contrôle* de travaux avec son mot de passe chiffré. Il est nécessaire stocker les informations d’identification pour activer les travaux qui doivent être exécutés ultérieurement, (en utilisant les planifications de travaux).
 
Chiffrement fonctionne via un certificat créé dans le cadre du script d’installation. Le script d’installation crée et télécharge le certificat dans le Service en nuage Azure pour le déchiffrement des mots de passe chiffrés stockés. Le Service de nuage Azure stocke ultérieurement la clé publique travaux *base de données de contrôle* qui permet à l’interface API de PowerShell ou Azure classique portail chiffrer un mot de passe fourni sans avoir besoin du certificat doit être installé localement.
 
Les mots de passe d’informations d’identification sont chiffrées et protégées contre les utilisateurs avec accès en lecture seule aux objets de tâches de base de données élastique. Mais il est possible pour un utilisateur malveillant avec l’accès en lecture / écriture aux objets élastique tâches de base de données extraire un mot de passe. Informations d’identification sont conçues pour les réutiliser dans leur exécution. Informations d’identification sont passées aux bases de données cible lors de l’établissement de connexions. Il n’existe actuellement aucune restriction sur les bases de données cible utilisés pour chaque information d’identification, utilisateur malveillant ajoute une cible de la base de données pour une base de données sous contrôle de l’utilisateur malveillant. L’utilisateur peut démarrer par la suite d’un travail ciblage cette base de données pour obtenir le mot de passe de l’information d’identification.

Meilleures pratiques de sécurité pour les tâches de base de données élastique sont les suivantes :

* Limiter l’utilisation de l’API aux personnes de confiance.
* Informations d’identification doivent avoir les privilèges minimum nécessaires pour effectuer la tâche.  Plus d’informations puisse être vu dans cet article de SQL Server MSDN [autorisations](https://msdn.microsoft.com/library/bb669084.aspx) .

### <a name="to-create-an-encrypted-credential-for-job-execution-across-databases"></a>Pour créer des informations d’identification chiffrées pour l’exécution du travail sur bases de données

Pour créer une nouvelle information d’identification chiffrée, l' [**applet de commande Get-Credential**](https://technet.microsoft.com/library/hh849815.aspx) invite à entrer un nom d’utilisateur et mot de passe qui peut être passé à l' [**applet de commande New-AzureSqlJobCredential**](https://msdn.microsoft.com/library/mt346063.aspx).

    $credentialName = "{Credential Name}"
    $databaseCredential = Get-Credential
    $credential = New-AzureSqlJobCredential -Credential $databaseCredential -CredentialName $credentialName
    Write-Output $credential

### <a name="to-update-credentials"></a>Pour mettre à jour les informations d’identification

Lorsque les mots de passe changent, utilisez l' [**applet de commande Set-AzureSqlJobCredential**](https://msdn.microsoft.com/library/mt346062.aspx) et définissez le paramètre **CredentialName** .

    $credentialName = "{Credential Name}"
    Set-AzureSqlJobCredential -CredentialName $credentialName -Credential $credential 

## <a name="to-define-an-elastic-database-shard-map-target"></a>Pour définir une destination de carte éclater élastique de base de données

Pour exécuter une tâche par rapport à toutes les bases de données dans un jeu partagé (créé à l’aide de la [bibliothèque de client de base de données élastique](sql-database-elastic-database-client-library.md)), utilisez un mappage partagé comme cible de la base de données. Cet exemple requiert une application sharded créée à l’aide de la bibliothèque de client élastique de base de données. Voir [mise en route d’exemples d’outils de base de données élastique](sql-database-elastic-scale-get-started.md).

La base de données Gestionnaire de carte éclater doit être définie comme cible de base de données et puis la carte éclater spécifique doit être spécifiée en tant que cible.

    $shardMapCredentialName = "{Credential Name}"
    $shardMapDatabaseName = "{ShardMapDatabaseName}" #example: ElasticScaleStarterKit_ShardMapManagerDb
    $shardMapDatabaseServerName = "{ShardMapServerName}"
    $shardMapName = "{MyShardMap}" #example: CustomerIDShardMap
    $shardMapDatabaseTarget = New-AzureSqlJobTarget -DatabaseName $shardMapDatabaseName -ServerName $shardMapDatabaseServerName
    $shardMapTarget = New-AzureSqlJobTarget -ShardMapManagerCredentialName $shardMapCredentialName -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapDatabaseServerName -ShardMapName $shardMapName
    Write-Output $shardMapTarget

## <a name="create-a-t-sql-script-for-execution-across-databases"></a>Créer un Script T-SQL pour une exécution sur bases de données

Lorsque vous créez des scripts T-SQL pour l’exécution, il est hautement recommandée pour générer les pour être [équipotentes](https://en.wikipedia.org/wiki/Idempotence) et résistant contre les défaillances. Tâches de base de données élastiques tentera l’exécution d’un script chaque fois que l’exécution rencontre une défaillance, quelle que soit la classification de l’échec.

Utiliser l' [**applet de commande New-AzureSqlJobContent**](https://msdn.microsoft.com/library/mt346085.aspx) pour créer et enregistrer un script pour l’exécution et définissez les paramètres **ContentName présent -** et **- CommandText** .

    $scriptName = "Create a TestTable"

    $scriptCommandText = "
    IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'TestTable')
    BEGIN
        CREATE TABLE TestTable(
            TestTableId INT PRIMARY KEY IDENTITY,
            InsertionTime DATETIME2
        );
    END
    GO
    INSERT INTO TestTable(InsertionTime) VALUES (sysutcdatetime());
    GO"

    $script = New-AzureSqlJobContent -ContentName $scriptName -CommandText $scriptCommandText
    Write-Output $script

### <a name="create-a-new-script-from-a-file"></a>Créer un nouveau script à partir d’un fichier
Si le script T-SQL est défini dans un fichier, utilisez-le pour importer le script :

    $scriptName = "My Script Imported from a File"
    $scriptPath = "{Path to SQL File}"
    $scriptCommandText = Get-Content -Path $scriptPath
    $script = New-AzureSqlJobContent -ContentName $scriptName -CommandText $scriptCommandText
    Write-Output $script

### <a name="to-update-a-t-sql-script-for-execution-across-databases"></a>Pour mettre à jour un script T-SQL pour une exécution sur bases de données  

Ce script PowerShell met à jour le texte de la commande T-SQL pour un script existant.

Définir les variables suivantes pour refléter la définition de script désiré à définir :

    $scriptName = "Create a TestTable"
    $scriptUpdateComment = "Adding AdditionalInformation column to TestTable"
    $scriptCommandText = "
    IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'TestTable')
    BEGIN
    CREATE TABLE TestTable(
        TestTableId INT PRIMARY KEY IDENTITY,
        InsertionTime DATETIME2
    );
    END
    GO

    IF NOT EXISTS (SELECT columns.name FROM sys.columns INNER JOIN sys.tables on columns.object_id = tables.object_id WHERE tables.name = 'TestTable' AND columns.name = 'AdditionalInformation')
    BEGIN
    ALTER TABLE TestTable
    ADD AdditionalInformation NVARCHAR(400);
    END
    GO

    INSERT INTO TestTable(InsertionTime, AdditionalInformation) VALUES (sysutcdatetime(), 'test');
    GO"

### <a name="to-update-the-definition-to-an-existing-script"></a>Pour mettre à jour la définition d’un script existant

    Set-AzureSqlJobContentDefinition -ContentName $scriptName -CommandText $scriptCommandText -Comment $scriptUpdateComment 

## <a name="to-create-a-job-to-execute-a-script-across-a-shard-map"></a>Pour créer une tâche pour exécuter un script sur une carte partagé

Ce script PowerShell démarre une tâche pour l’exécution d’un script entre chaque partagé dans un mappage éclater élastique échelle.

Définir les variables suivantes pour refléter le script désiré et cible :

    $jobName = "{Job Name}"
    $scriptName = "{Script Name}"
    $shardMapServerName = "{Shard Map Server Name}"
    $shardMapDatabaseName = "{Shard Map Database Name}"
    $shardMapName = "{Shard Map Name}"
    $credentialName = "{Credential Name}"
    $shardMapTarget = Get-AzureSqlJobTarget -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapServerName -ShardMapName $shardMapName 
    $job = New-AzureSqlJob -ContentName $scriptName -CredentialName $credentialName -JobName $jobName -TargetId $shardMapTarget.TargetId
    Write-Output $job

## <a name="to-execute-a-job"></a>Pour exécuter une tâche 

Ce script PowerShell s’exécute une tâche existante :

Mettre à jour la variable suivante pour refléter le nom du travail souhaité à exécuter :

    $jobName = "{Job Name}"
    $jobExecution = Start-AzureSqlJobExecution -JobName $jobName 
    Write-Output $jobExecution
 
## <a name="to-retrieve-the-state-of-a-single-job-execution"></a>Pour récupérer l’état de l’exécution d’une seule tâche

Utiliser l' [**applet de commande Get-AzureSqlJobExecution**](https://msdn.microsoft.com/library/mt346058.aspx) et définissez le paramètre **JobExecutionId** pour afficher l’état de l’exécution du travail.

    $jobExecutionId = "{Job Execution Id}"
    $jobExecution = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId
    Write-Output $jobExecution

Utiliser l’applet de commande **Get-AzureSqlJobExecution** même avec le paramètre **IncludeChildren** pour afficher l’état d’exécution des travaux enfant, à savoir l’état pour chaque exécution du travail sur chaque base de données ciblée par la tâche spécifique.

    $jobExecutionId = "{Job Execution Id}"
    $jobExecutions = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId -IncludeChildren
    Write-Output $jobExecutions 

## <a name="to-view-the-state-across-multiple-job-executions"></a>Pour afficher l’état au sein de plusieurs exécutions de travail

L' [**applet de commande Get-AzureSqlJobExecution**](https://msdn.microsoft.com/library/mt346058.aspx) contient plusieurs paramètres facultatifs qui peuvent être utilisés pour afficher plusieurs exécutions de travail, filtrées par le biais paramètres fournis. La section suivante montre quelques façons d’utiliser Get-AzureSqlJobExecution possibles :

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

## <a name="to-retrieve-failures-within-job-task-executions"></a>Pour extraire les échecs dans les exécutions de tâche de projet

L' **objet JobTaskExecution** inclut une propriété pour le cycle de vie de la tâche ainsi que d’une propriété de message. Si une tâche l’exécution du travail a échoué, la propriété du cycle de vie est fixée à *Échec* et la propriété message est fixée au message d’exception qui en résulte et sa pile. Si une tâche n’a pas réussi, il est important afficher les détails des tâches de projet n’a pas réussi pour une tâche donnée.

    $jobExecutionId = "{Job Execution Id}"
    $jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
    Foreach($jobTaskExecution in $jobTaskExecutions) 
        {
        if($jobTaskExecution.Lifecycle -ne 'Succeeded')
            {
            Write-Output $jobTaskExecution
            }
        }

## <a name="to-wait-for-a-job-execution-to-complete"></a>Patienter pour l’exécution d’une tâche Terminer

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
    $executionPolicy = New-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval 
    -RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
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

1. Annuler les tâches en cours d’exécution : si une annulation est détectée pendant une tâche est en cours d’exécution, une annulation sera retentée au sein de l’aspect de la tâche en cours d’exécution.  Par exemple : s’il existe une requête longue en cours d’exécution lors de la tentative d’une annulation, il y ait une tentative d’annulation de la requête.
2. Annulation de tentatives de tâche : si une annulation est détectée par le thread de contrôle avant de lancer une tâche pour l’exécution, le thread de contrôle sera Évitez lancement de la tâche et déclarer la demande comme étant annulée.

Si une tâche demande d’annulation pour une tâche parent, la demande d’annulation est respectée pour le travail parent et pour l’ensemble de ses tâches enfants.
 
Pour envoyer une demande d’annulation, utilisez l' [**applet de commande Stop AzureSqlJobExecution**](https://msdn.microsoft.com/library/mt346053.aspx) et définissez le paramètre **JobExecutionId** .

    $jobExecutionId = "{Job Execution Id}"
    Stop-AzureSqlJobExecution -JobExecutionId $jobExecutionId

## <a name="to-delete-a-job-and-job-history-asynchronously"></a>Pour supprimer une tâche et l’historique des travaux asynchrone

Tâches de base de données élastiques prend en charge asynchrone suppression des travaux. Une tâche peut être marquée pour la suppression et le système supprime la tâche et son historique de travail après que l’exécution de toutes les tâches terminées pour la tâche. Le système annule pas automatiquement les exécutions de travail actif.  

Appeler [**Arrêter AzureSqlJobExecution**](https://msdn.microsoft.com/library/mt346053.aspx) pour annuler l’exécution des travaux active.

Pour déclencher la suppression de la tâche, utilisez l' [**applet de commande Supprimer AzureSqlJob**](https://msdn.microsoft.com/library/mt346083.aspx) et définissez le paramètre **JobName** .

    $jobName = "{Job Name}"
    Remove-AzureSqlJob -JobName $jobName
 
## <a name="to-create-a-custom-database-target"></a>Pour créer une cible de la base de données personnalisée

Vous pouvez définir des cibles de base de données personnalisée pour exécution directe ou à inclure dans un groupe de base de données personnalisée. Par exemple, **des pools élastique de base de données** ne étant pas encore directement pris en charge à l’aide de PowerShell APIs, vous pouvez créer une base de données personnalisée cibles et base de données personnalisée collection de sites qui englobe tous les bases de données dans le pool.

Définir les variables suivantes pour refléter les informations de base de données souhaitée :

    $databaseName = "{Database Name}"
    $databaseServerName = "{Server Name}"
    New-AzureSqlJobDatabaseTarget -DatabaseName $databaseName -ServerName $databaseServerName 

## <a name="to-create-a-custom-database-collection-target"></a>Pour créer une cible de collection de sites de base de données personnalisée

Utiliser l’applet de commande [**New-AzureSqlJobTarget**](https://msdn.microsoft.com/library/mt346077.aspx) pour définir une cible de collection de sites de base de données personnalisée pour permettre l’exécution sur plusieurs cibles de base de données défini. Après avoir créé un groupe de base de données, les bases de données peuvent être associés à la cible de la collection de sites personnalisée.

Définir les variables suivantes pour refléter la configuration cible souhaité collection personnalisée :

    $customCollectionName = "{Custom Database Collection Name}"
    New-AzureSqlJobTarget -CustomCollectionName $customCollectionName 

### <a name="to-add-databases-to-a-custom-database-collection-target"></a>Pour ajouter des bases de données à une cible de collection de sites de base de données personnalisée

Pour ajouter une base de données à une collection spécifique personnalisée utiliser l’applet de commande [**Add-AzureSqlJobChildTarget**](https://msdn.microsoft.comlibrary/mt346064.aspx) .

    $databaseServerName = "{Database Server Name}"
    $databaseName = "{Database Name}"
    $customCollectionName = "{Custom Database Collection Name}"
    Add-AzureSqlJobChildTarget -CustomCollectionName $customCollectionName -DatabaseName $databaseName -ServerName $databaseServerName 

#### <a name="review-the-databases-within-a-custom-database-collection-target"></a>Passez en revue les bases de données dans une cible de collection de sites de base de données personnalisée

Utiliser l’applet de commande [**Get-AzureSqlJobTarget**](https://msdn.microsoft.com/library/mt346077.aspx) pour récupérer les bases de données enfant dans une cible de collection de sites de base de données personnalisée. 
 
    $customCollectionName = "{Custom Database Collection Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $childTargets = Get-AzureSqlJobTarget -ParentTargetId $target.TargetId
    Write-Output $childTargets

### <a name="create-a-job-to-execute-a-script-across-a-custom-database-collection-target"></a>Créer une tâche pour exécuter un script sur une cible de collection de sites de base de données personnalisée

Utiliser l’applet de commande [**New-AzureSqlJob**](https://msdn.microsoft.com/library/mt346078.aspx) pour créer une tâche par rapport à un groupe de bases de données défini par une cible de collection de sites de base de données personnalisée. Élastiques tâches de base de données seront développer la tâche en plusieurs tâches enfant correspondant chacune à une base de données associé à la cible de collection de sites de base de données personnalisée et vérifiez que le script est exécuté sur chaque base de données. Là encore, il est important que les scripts sont idempotents être résistant aux nouvelles tentatives.

    $jobName = "{Job Name}"
    $scriptName = "{Script Name}"
    $customCollectionName = "{Custom Collection Name}"
    $credentialName = "{Credential Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $scriptName -TargetId $target.TargetId
    Write-Output $job

## <a name="data-collection-across-databases"></a>Collecte de données entre bases de données

Vous pouvez utiliser une tâche pour exécuter une requête sur un groupe de bases de données et renvoyer les résultats à une table spécifique. Le tableau peut être interrogé après le fait pour afficher les résultats de la requête à partir de chaque base de données. Il fournit une méthode asynchrone pour exécuter une requête sur plusieurs bases de données. Échecs sont traitées automatiquement via tentatives.

La table de destination spécifié est automatiquement créée s’il n’existe pas encore. La nouvelle table correspondre au schéma du jeu de résultats. Si un script retourne plusieurs jeux de résultats, tâches de base de données élastique vous envoie uniquement la première table de destination.

Le script PowerShell suivant exécute un script et collecte ses résultats dans une table spécifiée. Ce script suppose qu’un script T-SQL qui retourne un jeu de résultats unique a été créé et qu’une cible de collection de sites de base de données personnalisée a été créée.

Ce script utilise l’applet de commande [**Get-AzureSqlJobTarget**](https://msdn.microsoft.com/library/mt346077.aspx) . Définir les paramètres de script, les informations d’identification et cible d’exécution :

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

### <a name="to-create-and-start-a-job-for-data-collection-scenarios"></a>Pour créer et démarrer une tâche pour des scénarios de collecte de données

Ce script utilise l’applet de commande [**Démarrer AzureSqlJobExecution**](https://msdn.microsoft.com/library/mt346055.aspx) .
 
    $job = New-AzureSqlJob -JobName $jobName 
    -CredentialName $executionCredentialName 
    -ContentName $scriptName 
    -ResultSetDestinationServerName $destinationServerName 
    -ResultSetDestinationDatabaseName $destinationDatabaseName 
    -ResultSetDestinationSchemaName $destinationSchemaName 
    -ResultSetDestinationTableName $destinationTableName 
    -ResultSetDestinationCredentialName $destinationCredentialName 
    -TargetId $target.TargetId
    Write-Output $job
    $jobExecution = Start-AzureSqlJobExecution -JobName $jobName
    Write-Output $jobExecution

## <a name="to-schedule-a-job-execution-trigger"></a>Planification d’un déclencheur d’exécution de tâche

Le script PowerShell suivant pouvant être utilisé pour créer un calendrier récurrent. Ce script utilise un intervalle minute, mais [**Nouveau AzureSqlJobSchedule**](https://msdn.microsoft.com/library/mt346068.aspx) prend également en charge les paramètres - DayInterval, - HourInterval, - MonthInterval et - WeekInterval. Tableaux qui s’exécutent qu’une seule fois peuvent être créés en transmettant - unique.

Créer un nouvel horaire :

    $scheduleName = "Every one minute"
    $minuteInterval = 1
    $startTime = (Get-Date).ToUniversalTime()
    $schedule = New-AzureSqlJobSchedule 
    -MinuteInterval $minuteInterval 
    -ScheduleName $scheduleName 
    -StartTime $startTime 
    Write-Output $schedule

### <a name="to-trigger-a-job-executed-on-a-time-schedule"></a>Déclencher une tâche s’exécutée sur un calendrier

Un déclencheur de travail peut être défini pour une tâche exécutée en fonction d’un calendrier. Le script PowerShell suivant pouvant être utilisé pour créer un déclencheur de travail.

Utilisez [New-AzureSqlJobTrigger](https://msdn.microsoft.com/library/mt346069.aspx) et définir les variables suivantes pour correspondre à la tâche souhaitée et le calendrier :

    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    $jobTrigger = New-AzureSqlJobTrigger
    -ScheduleName $scheduleName
    –JobName $jobName
    Write-Output $jobTrigger

### <a name="to-remove-a-scheduled-association-to-stop-job-from-executing-on-schedule"></a>Pour supprimer une association planifiée pour arrêter la tâche à partir de l’exécution de la planification

Pour arrêter l’exécution du travail récurrents via un déclencheur de travail, le déclencheur de travail peut être supprimé. Supprimer un déclencheur de tâche pour arrêter une tâche à partir d’en cours d’exécution en fonction d’une planification à l’aide de l' [**applet de commande Supprimer AzureSqlJobTrigger**](https://msdn.microsoft.com/library/mt346070.aspx).

    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    Remove-AzureSqlJobTrigger 
    -ScheduleName $scheduleName 
    -JobName $jobName

### <a name="retrieve-job-triggers-bound-to-a-time-schedule"></a>Récupérer des déclencheurs travail liés à un calendrier

Le script PowerShell suivant peut être utilisé pour obtenir et afficher les déclencheurs travail enregistrés à un calendrier spécifique.

    $scheduleName = "{Schedule Name}"
    $jobTriggers = Get-AzureSqlJobTrigger -ScheduleName $scheduleName
    Write-Output $jobTriggers

### <a name="to-retrieve-job-triggers-bound-to-a-job"></a>Pour récupérer des déclencheurs travail lié à une tâche 

[Get-AzureSqlJobTrigger](https://msdn.microsoft.com/library/mt346067.aspx) permet d’obtenir et afficher les tableaux contenant une tâche enregistrée.

    $jobName = "{Job Name}"
    $jobTriggers = Get-AzureSqlJobTrigger -JobName $jobName
    Write-Output $jobTriggers

## <a name="to-create-a-data-tier-application-dacpac-for-execution-across-databases"></a>Pour créer une application de couche données (DACPAC) pour l’exécution sur bases de données

Pour créer un DACPAC, voir [les applications couche de données](https://msdn.microsoft.com/library/ee210546.aspx). Pour déployer un DACPAC, utilisez l' [applet de commande New-AzureSqlJobContent](https://msdn.microsoft.com/library/mt346085.aspx). Le DACPAC doit être accessible au service. Il est recommandé de télécharger un DACPAC créé au stockage Azure et créer une [Signature de Access partagés](../storage/storage-dotnet-shared-access-signature-part-1.md) pour la DACPAC.

    $dacpacUri = "{Uri}"
    $dacpacName = "{Dacpac Name}"
    $dacpac = New-AzureSqlJobContent -DacpacUri $dacpacUri -ContentName $dacpacName 
    Write-Output $dacpac

### <a name="to-update-a-data-tier-application-dacpac-for-execution-across-databases"></a>Pour mettre à jour une application de couche données (DACPAC) pour l’exécution sur bases de données

DACPACs existants enregistrées dans les tâches de base de données élastique peuvent être mis à jour pour faire pointer vers le nouveau URI. Utiliser l' [**applet de commande Set-AzureSqlJobContentDefinition**](https://msdn.microsoft.com/library/mt346074.aspx) pour mettre à jour l’URI DACPAC sur un DACPAC enregistré existant :

    $dacpacName = "{Dacpac Name}"
    $newDacpacUri = "{Uri}"
    $updatedDacpac = Set-AzureSqlJobDacpacDefinition -ContentName $dacpacName -DacpacUri $newDacpacUri
    Write-Output $updatedDacpac

## <a name="to-create-a-job-to-apply-a-data-tier-application-dacpac-across-databases"></a>Pour créer une tâche pour appliquer une application de couche données (DACPAC) entre bases de données

Une fois un DACPAC a été créé dans élastique tâches de base de données, une tâche peut créée pour appliquer la DACPAC dans un groupe de bases de données. Le script PowerShell suivant pouvant être utilisé pour créer une tâche DACPAC au sein d’une collection personnalisée des bases de données :

    $jobName = "{Job Name}"
    $dacpacName = "{Dacpac Name}"
    $customCollectionName = "{Custom Collection Name}"
    $credentialName = "{Credential Name}"
    $target = Get-AzureSqlJobTarget 
    -CustomCollectionName $customCollectionName
    $job = New-AzureSqlJob 
    -JobName $jobName 
    -CredentialName $credentialName 
    -ContentName $dacpacName -TargetId $target.TargetId
    Write-Output $job 

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-powershell/cmd-prompt.png
[2]: ./media/sql-database-elastic-jobs-powershell/portal.png
<!--anchors-->
