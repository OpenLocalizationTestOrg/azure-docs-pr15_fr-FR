<properties 
    pageTitle="Code XEvent événement fichier de base de données SQL | Microsoft Azure" 
    description="Fournit des PowerShell et Transact-SQL pour un échantillon de code en deux phases qui montre la cible du fichier de l’événement dans un événement étendu sur base de données SQL Azure. Stockage Azure est une partie obligatoire de ce scénario." 
    services="sql-database" 
    documentationCenter="" 
    authors="MightyPen" 
    manager="jhubbard" 
    editor="" 
    tags=""/>


<tags 
    ms.service="sql-database" 
    ms.workload="data-management" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/23/2016" 
    ms.author="genemi"/>


# <a name="event-file-target-code-for-extended-events-in-sql-database"></a>Code de cible fichier événement pour les événements étendus dans la base de données SQL

[AZURE.INCLUDE [sql-database-xevents-selectors-1-include](../../includes/sql-database-xevents-selectors-1-include.md)]

Vous souhaitez un exemple de code complet pour un excellent moyen des États et capture des données pour un événement étendu.


Dans Microsoft SQL Server, le [fichier d’événements cible](http://msdn.microsoft.com/library/ff878115.aspx) permet de stocker des sorties événement dans un fichier de disque dur local. Mais ces fichiers ne sont pas disponibles pour la base de données SQL Azure. Nous utilisons à la place le service de stockage Azure pour prendre en charge la cible du fichier de l’événement.


Cette rubrique présente un exemple de code en deux phases :


- PowerShell, pour créer un conteneur de stockage Azure dans le cloud.

- Transact-SQL :
 - Pour affecter le conteneur de stockage Azure à une cible de fichier de l’événement.
 - Comment créer et démarrer la session d’événements et ainsi de suite.


## <a name="prerequisites"></a>Conditions préalables


- Un compte Azure et abonnement. Vous pouvez vous inscrire à une [version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/).


- Les bases de données que vous pouvez créer un tableau dans.
 - Vous pouvez également vous pouvez [créer une base de données de la démonstration **AdventureWorksLT** ](sql-database-get-started.md) en minutes.


- SQL Server Management Studio (ssms.exe), dans l’idéal sa dernière version mise à jour mensuelles. Vous pouvez télécharger la dernière ssms.exe à partir de :
 - Rubrique [Télécharger SQL Server Management Studio](http://msdn.microsoft.com/library/mt238290.aspx).
 - [Un lien direct vers le téléchargement.](http://go.microsoft.com/fwlink/?linkid=616025)


- Vous devez avoir les [modules PowerShell Azure](http://go.microsoft.com/?linkid=9811175) installé.
 - Les modules fournissent des commandes telles que - **Nouveau AzureStorageAccount**.


## <a name="phase-1-powershell-code-for-azure-storage-container"></a>Phase 1 : Code de PowerShell pour conteneur de stockage Azure


Cette PowerShell est phase 1 de l’exemple de code en deux phases.

Le script commence par commandes pour nettoyer après une éventuelle précédente s’exécute et rerunnable.



1. Collez le script PowerShell dans un éditeur de texte simple, par exemple Notepad.exe et enregistrez le script dans un fichier avec l' extension **.ps1**.

2. Démarrer PowerShell ISE en tant qu’administrateur.

3. À l’invite, tapez<br/>`Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser`<br/>et appuyez sur ENTRÉE.

4. Dans PowerShell ISE, ouvrez votre fichier **.ps1** . Exécuter le script.

5. Tout d’abord, le script démarre une nouvelle fenêtre dans laquelle vous vous connectez à Azure.
 - Si vous exécutez à nouveau le script sans interrompre votre session, vous avez la possibilité pratique des commentaires dans la commande **Ajouter AzureAccount** .


![PowerShell ISE, avec le module Azure installé, vous êtes prêt à exécuter un script.][30_powershell_ise]


&nbsp;


```
## TODO: Before running, find all 'TODO' and make each edit!!

#--------------- 1 -----------------------


# You can comment out or skip this Add-AzureAccount
# command after the first run.
# Current PowerShell environment retains the successful outcome.

'Expect a pop-up window in which you log in to Azure.'


Add-AzureAccount

#-------------- 2 ------------------------


'
TODO: Edit the values assigned to these variables, especially the first few!
'

# Ensure the current date is between
# the Expiry and Start time values that you edit here.

$subscriptionName    = 'YOUR_SUBSCRIPTION_NAME'
$policySasExpiryTime = '2016-01-28T23:44:56Z'
$policySasStartTime  = '2015-08-01'


$storageAccountName     = 'gmstorageaccountxevent'
$storageAccountLocation = 'West US'
$contextName            = 'gmcontext'
$containerName          = 'gmcontainerxevent'
$policySasToken         = 'gmpolicysastoken'


# Leave this value alone, as 'rwl'.
$policySasPermission = 'rwl'

#--------------- 3 -----------------------


# The ending display lists your Azure subscriptions.
# One should match the $subscriptionName value you assigned
#   earlier in this PowerShell script. 

'Choose an existing subscription for the current PowerShell environment.'


Select-AzureSubscription -SubscriptionName $subscriptionName


#-------------- 4 ------------------------


'
Clean up the old Azure Storage Account after any previous run, 
before continuing this new run.'


If ($storageAccountName)
{
    Remove-AzureStorageAccount -StorageAccountName $storageAccountName
}

#--------------- 5 -----------------------

[System.DateTime]::Now.ToString()

'
Create a storage account. 
This might take several minutes, will beep when ready.
  ...PLEASE WAIT...'

New-AzureStorageAccount `
    -StorageAccountName $storageAccountName `
    -Location           $storageAccountLocation

[System.DateTime]::Now.ToString()

[System.Media.SystemSounds]::Beep.Play()


'
Get the primary access key for your storage account.
'


$primaryAccessKey_ForStorageAccount = `
    (Get-AzureStorageKey `
        -StorageAccountName $storageAccountName).Primary

"`$primaryAccessKey_ForStorageAccount = $primaryAccessKey_ForStorageAccount"

'Azure Storage Account cmdlet completed.
Remainder of PowerShell .ps1 script continues.
'

#--------------- 6 -----------------------


# The context will be needed to create a container within the storage account.

'Create a context object from the storage account and its primary access key.
'

$context = New-AzureStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey  $primaryAccessKey_ForStorageAccount


'Create a container within the storage account.
'


$containerObjectInStorageAccount = New-AzureStorageContainer `
    -Name    $containerName `
    -Context $context


'Create a security policy to be applied to the SAS token.
'

New-AzureStorageContainerStoredAccessPolicy `
    -Container  $containerName `
    -Context    $context `
    -Policy     $policySasToken `
    -Permission $policySasPermission `
    -ExpiryTime $policySasExpiryTime `
    -StartTime  $policySasStartTime 

'
Generate a SAS token for the container.
'
Try
{
    $sasTokenWithPolicy = New-AzureStorageContainerSASToken `
        -Name    $containerName `
        -Context $context `
        -Policy  $policySasToken
}
Catch 
{
    $Error[0].Exception.ToString()
}

#-------------- 7 ------------------------


'Display the values that YOU must edit into the Transact-SQL script next!:
'

"storageAccountName: $storageAccountName"
"containerName:      $containerName"
"sasTokenWithPolicy: $sasTokenWithPolicy"

'
REMINDER: sasTokenWithPolicy here might start with "?" character, which you must exclude from Transact-SQL.
'

'
(Later, return here to delete your Azure Storage account. See the preceding - Remove-AzureStorageAccount -StorageAccountName $storageAccountName)'

'
Now shift to the Transact-SQL portion of the two-part code sample!'

# EOFile
```


&nbsp;


Notez les premières valeurs nommées qui permet d’imprimer le script PowerShell lorsqu’elle se termine. Vous devez modifier ces valeurs dans le script Transact-SQL qui suit en tant que phase 2.


## <a name="phase-2-transact-sql-code-that-uses-azure-storage-container"></a>Phase 2 : Code Transact-SQL qui utilise le stockage Azure conteneur


- Dans la phase 1 de cet exemple de code, vous avez exécuté un script PowerShell pour créer un conteneur de stockage Azure.
- Suivant dans la phase 2, le script Transact-SQL suivant doit utiliser le conteneur.


Le script commence par commandes pour nettoyer après une éventuelle précédente s’exécute et rerunnable.


Le script PowerShell imprimées quelques valeurs nommées lorsqu’il terminée. Vous devez modifier le script Transact-SQL pour utiliser ces valeurs. Vous trouverez **TODO** dans le script Transact-SQL pour identifier les points d’édition.


1. Ouvrez SQL Server Management Studio (ssms.exe).

2. Se connecter à votre base de données de base de données SQL Azure.

3. Cliquez pour ouvrir un nouveau volet requête.

4. Collez le script Transact-SQL suivant dans le volet requête.

5. Recherchez chaque **TODO** dans le script, puis apportez les modifications appropriées.

6. Enregistrez et exécutez le script.


&nbsp;


> [AZURE.WARNING] La valeur de clé sa générée par le script PowerShell précédent peut commencer par un « ? » (point d’interrogation). Lorsque vous utilisez la touche associations de sécurité dans le script T-SQL suivant, vous devez *Supprimer l’interligne '?'*. Dans le cas contraire vos efforts peuvent être bloqués par la sécurité.


&nbsp;


```
---- TODO: First, run the PowerShell portion of this two-part code sample.
---- TODO: Second, find every 'TODO' in this Transact-SQL file, and edit each.

---- Transact-SQL code for Event File target on Azure SQL Database.


SET NOCOUNT ON;
GO


----  Step 1.  Establish one little table, and  ---------
----  insert one row of data.


IF EXISTS
    (SELECT * FROM sys.objects
        WHERE type = 'U' and name = 'gmTabEmployee')
BEGIN
    DROP TABLE gmTabEmployee;
END
GO


CREATE TABLE gmTabEmployee
(
    EmployeeGuid         uniqueIdentifier   not null  default newid()  primary key,
    EmployeeId           int                not null  identity(1,1),
    EmployeeKudosCount   int                not null  default 0,
    EmployeeDescr        nvarchar(256)          null
);
GO


INSERT INTO gmTabEmployee ( EmployeeDescr )
    VALUES ( 'Jane Doe' );
GO


------  Step 2.  Create key, and  ------------
------  Create credential (your Azure Storage container must already exist).


IF NOT EXISTS
    (SELECT * FROM sys.symmetric_keys
        WHERE symmetric_key_id = 101)
BEGIN
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '0C34C960-6621-4682-A123-C7EA08E3FC46' -- Or any newid().
END
GO


IF EXISTS
    (SELECT * FROM sys.database_scoped_credentials
        -- TODO: Assign AzureStorageAccount name, and the associated Container name.
        WHERE name = 'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent')
BEGIN
    DROP DATABASE SCOPED CREDENTIAL
        -- TODO: Assign AzureStorageAccount name, and the associated Container name.
        [https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent] ;
END
GO


CREATE
    DATABASE SCOPED
    CREDENTIAL
        -- use '.blob.',   and not '.queue.' or '.table.' etc.
        -- TODO: Assign AzureStorageAccount name, and the associated Container name.
        [https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent]
    WITH
        IDENTITY = 'SHARED ACCESS SIGNATURE',  -- "SAS" token.
        -- TODO: Paste in the long SasToken string here for Secret, but exclude any leading '?'.
        SECRET = 'sv=2014-02-14&sr=c&si=gmpolicysastoken&sig=EjAqjo6Nu5xMLEZEkMkLbeF7TD9v1J8DNB2t8gOKTts%3D'
    ;
GO


------  Step 3.  Create (define) an event session.  --------
------  The event session has an event with an action,
------  and a has a target.

IF EXISTS
    (SELECT * from sys.database_event_sessions
        WHERE name = 'gmeventsessionname240b')
BEGIN
    DROP
        EVENT SESSION
            gmeventsessionname240b
        ON DATABASE;
END
GO


CREATE
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE

    ADD EVENT
        sqlserver.sql_statement_starting
            (
            ACTION (sqlserver.sql_text)
            WHERE statement LIKE 'UPDATE gmTabEmployee%'
            )
    ADD TARGET
        package0.event_file
            (
            -- TODO: Assign AzureStorageAccount name, and the associated Container name.
            -- Also, tweak the .xel file name at end, if you like.
            SET filename =
                'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent/anyfilenamexel242b.xel'
            )
    WITH
        (MAX_MEMORY = 10 MB,
        MAX_DISPATCH_LATENCY = 3 SECONDS)
    ;
GO


------  Step 4.  Start the event session.  ----------------
------  Issue the SQL Update statements that will be traced.
------  Then stop the session.

------  Note: If the target fails to attach,
------  the session must be stopped and restarted.

ALTER
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE
    STATE = START;
GO


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM gmTabEmployee;

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 2
    WHERE EmployeeDescr = 'Jane Doe';

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 13
    WHERE EmployeeDescr = 'Jane Doe';

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM gmTabEmployee;
GO


ALTER
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE
    STATE = STOP;
GO


-------------- Step 5.  Select the results. ----------

SELECT
        *, 'CLICK_NEXT_CELL_TO_BROWSE_ITS_RESULTS!' as [CLICK_NEXT_CELL_TO_BROWSE_ITS_RESULTS],
        CAST(event_data AS XML) AS [event_data_XML]  -- TODO: In ssms.exe results grid, double-click this cell!
    FROM
        sys.fn_xe_file_target_read_file
            (
                -- TODO: Fill in Storage Account name, and the associated Container name.
                'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent/anyfilenamexel242b',
                null, null, null
            );
GO


-------------- Step 6.  Clean up. ----------

DROP
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE;
GO

DROP DATABASE SCOPED CREDENTIAL
    -- TODO: Assign AzureStorageAccount name, and the associated Container name.
    [https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent]
    ;
GO

DROP TABLE gmTabEmployee;
GO

PRINT 'Use PowerShell Remove-AzureStorageAccount to delete your Azure Storage account!';
GO
```


&nbsp;


Si la cible ne parvient pas à joindre lorsque vous exécutez, vous devez arrêter et redémarrer la session d’événements :


```
ALTER EVENT SESSION ... STATE = STOP;
GO
ALTER EVENT SESSION ... STATE = START;
GO
```


&nbsp;


## <a name="output"></a>Sortie


Lorsque le script Transact-SQL est terminée, cliquez sur une cellule sous l’en-tête de colonne **event_data_XML** . Un seul **<event>** élément s’affiche qui montre une instruction UPDATE.

Voici une **<event>** élément a été généré pendant le test :


&nbsp;


```
<event name="sql_statement_starting" package="sqlserver" timestamp="2015-09-22T19:18:45.420Z">
  <data name="state">
    <value>0</value>
    <text>Normal</text>
  </data>
  <data name="line_number">
    <value>5</value>
  </data>
  <data name="offset">
    <value>148</value>
  </data>
  <data name="offset_end">
    <value>368</value>
  </data>
  <data name="statement">
    <value>UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 2
    WHERE EmployeeDescr = 'Jane Doe'</value>
  </data>
  <action name="sql_text" package="sqlserver">
    <value>

SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM gmTabEmployee;

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 2
    WHERE EmployeeDescr = 'Jane Doe';

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 13
    WHERE EmployeeDescr = 'Jane Doe';

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM gmTabEmployee;
</value>
  </action>
</event>
```

&nbsp;


Le script Transact-SQL précédent utilisé la fonction système suivante pour lire l’event_file :

- [Sys.fn_xe_file_target_read_file (Transact-SQL)](http://msdn.microsoft.com/library/cc280743.aspx)


Explication des options avancées pour l’affichage de données à partir d’événements étendus est disponible sur :

- [Fonctions avancées de consultation de données cible à partir d’événements étendus](http://msdn.microsoft.com/library/mt752502.aspx)

&nbsp;


## <a name="converting-the-code-sample-to-run-on-sql-server"></a>Conversion de l’exemple de code pour s’exécuter sur SQL Server


Supposons que vous voulez exécuter l’exemple précédent Transact-SQL sur Microsoft SQL Server.


- Pour simplifier, vous pouvez remplacer complètement utilisation du conteneur de stockage Azure avec un fichier simple tel que **C:\myeventdata.xel**. Le fichier est écrit sur le disque dur de l’ordinateur qui héberge SQL Server.


- Vous n’avez pas tout type d’instructions Transact-SQL pour **créer la clé principale** et **Créer les informations d’identification**.


- Dans l’instruction **CREATE EVENT SESSION** , dans sa clause **Ajouter une cible** , vous devez remplacer la valeur Http est affectée à **filename =** avec une chaîne de chemin d’accès complet comme **C:\myfile.xel**.
 - Aucun compte Azure stockage ne doit être impliqué.


## <a name="more-information"></a>Plus d’informations


Pour plus d’informations sur les comptes et conteneurs dans le service de stockage Azure, voir :

- [L’utilisation de stockage d’objets Blob à partir de .NET](../storage/storage-dotnet-how-to-use-blobs.md)
- [Nommer et faisant référence à des conteneurs et des objets BLOB des métadonnées](http://msdn.microsoft.com/library/azure/dd135715.aspx)
- [Travailler avec le conteneur racine](http://msdn.microsoft.com/library/azure/ee395424.aspx)
- [Leçon 1 : Créer une stratégie d’accès stockée et une signature d’un accès partagé sur un conteneur Azure](http://msdn.microsoft.com/library/dn466430.aspx)
    - [Leçon 2 : Créer des informations d’identification SQL Server à l’aide d’une signature accès partagé](http://msdn.microsoft.com/library/dn466435.aspx)




<!--
Image references.
-->

[30_powershell_ise]: ./media/sql-database-xevent-code-event-file/event-file-powershell-ise-b30.png

