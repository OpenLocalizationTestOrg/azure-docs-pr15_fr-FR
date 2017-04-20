<properties
    pageTitle="Importer un fichier à DOS pour créer une base de données SQL Azure à l’aide de PowerShell | Microsoft Azure"
    description="Importer un fichier à DOS pour créer une base de données SQL Azure à l’aide de PowerShell"
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management"
    ms.date="08/31/2016"
    ms.author="sstein"/>

# <a name="import-a-bacpac-file-to-create-an-azure-sql-database-by-using-powershell"></a>Importer un fichier à DOS pour créer une base de données SQL Azure à l’aide de PowerShell

**Base de données unique**

> [AZURE.SELECTOR]
- [Portail Azure](sql-database-import.md)
- [PowerShell](sql-database-import-powershell.md)
- [SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)

Cet article explique comment créer une base de données SQL Azure en important un fichier [à DOS](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) avec PowerShell.

La base de données est créé à partir d’un fichier à DOS (.bacpac) importé à partir d’un conteneur d’objets blob Azure stockage. Si vous n’avez pas un fichier à DOS dans le stockage Azure, voir [Archiver une base de données SQL Azure vers un fichier à DOS à l’aide de PowerShell](sql-database-export-powershell.md). Si vous disposez déjà d’un fichier à DOS qui n’est pas dans le stockage Azure, [Utilisez AzCopy pour le télécharger facilement à votre compte de stockage Azure](../storage/storage-use-azcopy.md#blob-upload).

> [AZURE.NOTE] Base de données SQL Azure crée automatiquement et conserve les sauvegardes pour chaque base de données utilisateur que vous pouvez restaurer. Pour plus d’informations, voir [sauvegardes automatisées de base de données SQL](sql-database-automated-backups.md).


Pour importer une base de données SQL, vous devez les éléments suivants :

- Un abonnement Azure. Si vous avez besoin d’un abonnement Azure simplement cliquez sur **Version d’évaluation gratuite** en haut de cette page et puis revenez à la fin de cet article.
- Un fichier à dos de la base de données que vous souhaitez importer. À la DOS doit se trouver dans un conteneur blob [Azure stockage compte](../storage/storage-create-storage-account.md) .



[AZURE.INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]



## <a name="set-up-the-variables-for-your-environment"></a>Configurer les variables pour votre environnement

Il existe quelques variables où vous devez remplacer les valeurs d’exemple avec les valeurs spécifiques pour votre base de données et de votre compte de stockage.

Le nom du serveur doit être un serveur qui existe dans l’abonnement sélectionné à l’étape précédente. Il doit être le serveur souhaité soit créé dans la base de données. Importer une base de données directement dans un pool élastique n’est pas pris en charge. Mais vous pouvez tout d’abord importer dans une seule base de données et puis déplacez la base de données dans un pool.

Le nom de la base de données est le nom souhaité pour la nouvelle base de données.

    $ResourceGroupName = "resource group name"
    $ServerName = "server name"
    $DatabaseName = "database name"


Les variables suivantes sont à partir du compte de stockage dans lequel se trouve votre à DOS. Dans le [portail Azure](https://portal.azure.com), accédez à votre compte de stockage pour obtenir ces valeurs. Vous pouvez trouver la clé primaire access en cliquant sur **tous les paramètres** , puis sur **touches** à partir de la carte de votre compte de stockage.

Le nom d’objets blob est le nom d’un fichier à DOS existant que vous souhaitez créer la base de données à partir de. Vous avez besoin d’inclure l’extension .bacpac.

    $StorageName = "storageaccountname"
    $StorageKeyType = "StorageAccessKey"
    $StorageUri = "http://$StorageName.blob.core.windows.net/containerName/filename.bacpac"
    $StorageKey = "primaryaccesskey"


En cours d’exécution la [Get-Credential] (https://msdn.microsoft.com/library/azure/hh849815(v=azure.300\).aspx) applet de commande ouvre une fenêtre de demande de votre nom d’utilisateur et mot de passe. Entrez l’ouverture de session admin et le mot de passe pour le serveur de base de données SQL ($ServerName à partir du haut) et non le nom d’utilisateur et mot de passe de votre compte Azure.

    $credential = Get-Credential


## <a name="import-the-database"></a>Importer la base de données

Cette commande envoie une demande d’importation de base de données au service. Selon la taille de votre base de données, l’opération d’importation peut prendre un certain temps pour terminer.

    $importRequest = New-AzureRmSqlDatabaseImport –ResourceGroupName $ResourceGroupName –ServerName $ServerName –DatabaseName $DatabaseName –StorageKeytype $StorageKeyType –StorageKey $StorageKey -StorageUri $StorageUri –AdministratorLogin $credential.UserName –AdministratorLoginPassword $credential.Password –Edition Standard –ServiceObjectiveName S0 -DatabaseMaxSizeBytes 50000


## <a name="monitor-the-progress-of-the-operation"></a>Surveiller l’avancement de l’opération

Après avoir exécuté [New-AzureRmSqlDatabaseImport] (https://msdn.microsoft.com/library/azure/mt707793(v=azure.300\).aspx), vous pouvez vérifier l’état de la requête en exécutant la [Get-AzureRmSqlDatabaseImportExportStatus] (https://msdn.microsoft.com/library/azure/mt707794(v=azure.300\).aspx).

    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink



## <a name="sql-database-powershell-import-script"></a>Importer le script PowerShell de base de données SQL


    $ResourceGroupName = "resourceGroupName"
    $ServerName = "servername"
    $DatabaseName = "databasename"

    $StorageName = "storageaccountname"
    $StorageKeyType = "StorageAccessKey"
    $StorageUri = "http://$StorageName.blob.core.windows.net/containerName/filename.bacpac"
    $StorageKey = "primaryaccesskey"

    $credential = Get-Credential

    $importRequest = New-AzureRmSqlDatabaseImport –ResourceGroupName $ResourceGroupName –ServerName $ServerName –DatabaseName $DatabaseName –StorageKeytype $StorageKeyType –StorageKey $StorageKey -StorageUri $StorageUri –AdministratorLogin $credential.UserName –AdministratorLoginPassword $credential.Password –Edition Standard –ServiceObjectiveName S0 -DatabaseMaxSizeBytes 50000

    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink



## <a name="next-steps"></a>Étapes suivantes

- Pour découvrir comment vous connecter à et interroger une base de données SQL importées, voir [se connecter à la base de données SQL avec SQL Server Management Studio et effectuer un exemple de requête T-SQL](sql-database-connect-query-ssms.md)
