<properties
    pageTitle="Utiliser PowerShell pour sauvegarder et restaurer des applications de Service d’application"
    description="Découvrez comment utiliser PowerShell pour sauvegarder et restaurer une application dans le Service d’application Azure"
    services="app-service"
    documentationCenter=""
    authors="NKing92"
    manager="wpickett"
    editor="" />

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/10/2016"
    ms.author="nicking"/>
# <a name="use-powershell-to-back-up-and-restore-app-service-apps"></a>Utiliser PowerShell pour sauvegarder et restaurer des applications de Service d’application

> [AZURE.SELECTOR]
- [PowerShell](app-service-powershell-backup.md)
- [API REST](../app-service-web/websites-csm-backup.md)

Découvrez comment utiliser PowerShell Azure pour sauvegarder et restaurer des [applications de Service d’application](https://azure.microsoft.com/services/app-service/web/). Pour plus d’informations sur les sauvegardes application web, y compris les exigences et les restrictions, voir [sauvegarder une application web dans le Service d’application Azure](../app-service-web/web-sites-backup.md).

## <a name="prerequisites"></a>Conditions préalables
Pour utiliser PowerShell pour gérer vos sauvegardes application, vous devez les éléments suivants :

- **A sa URL** qui autorise l’accès en lecture et écriture dans un conteneur de stockage Azure. Pour obtenir une explication d’URL associations de sécurité, voir [Présentation du modèle associations de sécurité](../storage/storage-dotnet-shared-access-signature-part-1.md) . Pour obtenir des exemples de gestion du stockage Azure à l’aide de PowerShell, voir [Utilisation de PowerShell Azure avec stockage Azure](../storage/storage-powershell-guide-full.md) .
- **Chaîne de connexion de base de données A** si vous souhaitez sauvegarder une base de données en même temps que votre application web.

### <a name="how-to-generate-a-sas-url-to-use-with-the-web-app-backup-cmdlets"></a>Comment faire pour générer une URL associations de sécurité à utiliser avec les applets de commande web application sauvegarde
Une URL de sa peut être générée avec PowerShell. Voici un exemple de la façon d’en générer un qui peuvent être utilisés avec les applets de commande mentionnés dans cet article.

        $storageAccountName = "<your storage account's name>"
        $storageAccountRg = "<your storage account's resource group>"

        # This returns an array of keys for your storage account. Be sure to select the appropriate key. Here we select the first key as a default.
        $storageAccountKey = Get-AzureRmStorageAccountKey -ResourceGroupName $storageAccountRg -Name $storageAccountName
        $context = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey[0].Value

        $blobContainerName = "<name of blob container for app backups>"
        $sasUrl = New-AzureStorageContainerSASToken -Name $blobContainerName -Permission rwdl -Context $context -ExpiryTime (Get-Date).AddMonths(1) -FullUri

## <a name="install-azure-powershell-132-or-greater"></a>Installer PowerShell Azure 1.3.2 ou version ultérieure

Pour obtenir des instructions sur l’installation et l’utilisation de PowerShell Azure, voir [Utilisation de PowerShell Azure avec le Gestionnaire de ressources Azure](../powershell-install-configure.md) .

## <a name="create-a-backup"></a>Créer une sauvegarde

Utiliser l’applet de commande New-AzureRmWebAppBackup pour créer une sauvegarde d’une application web.

        $sasUrl = "<your SAS URL>"
        $resourceGroupName = "Default-Web-WestUS"
        $appName = "ContosoApp"

        $backup = New-AzureRmWebAppBackup -ResourceGroupName $resourceGroupName -Name $appName -StorageAccountUrl $sasUrl

Cela crée une sauvegarde avec un nom généré automatiquement. Si vous voulez fournir un nom pour votre sauvegarde, utilisez le paramètre facultatif BackupName.

        $backup = New-AzureRmWebAppBackup -ResourceGroupName $resourceGroupName -Name $appName -StorageAccountUrl $sasUrl -BackupName MyBackup

Pour inclure une base de données dans la sauvegarde, créez un paramètre de sauvegarde de base de données à l’aide de l’applet de commande New-AzureRmWebAppDatabaseBackupSetting, puis fournir ce paramètre dans le paramètre de bases de données de l’applet de commande New-AzureRmWebAppBackup. Le paramètre de bases de données accepte un tableau de paramètres de base de données, ce qui vous permet de sauvegarder plusieurs bases de données.

        $dbSetting1 = New-AzureRmWebAppDatabaseBackupSetting -Name DB1 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
        $dbSetting2 = New-AzureRmWebAppDatabaseBackupSetting -Name DB2 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
        $dbBackup = New-AzureRmWebAppBackup -ResourceGroupName $resourceGroupName -Name $appName -BackupName MyBackup -StorageAccountUrl $sasUrl -Databases $dbSetting1,$dbSetting2

## <a name="get-backups"></a>Obtenir des sauvegardes

L’applet de commande Get-AzureRmWebAppBackupList renvoie une matrice de toutes les sauvegardes pour une application web. Vous devez fournir le nom de l’application web et son groupe de ressources.

        $resourceGroupName = "Default-Web-WestUS"
        $appName = "ContosoApp"
        $backups = Get-AzureRmWebAppBackupList -Name $appName -ResourceGroupName $resourceGroupName

Pour obtenir une sauvegarde spécifique, utilisez l’applet de commande Get-AzureRmWebAppBackup.

        $backup = Get-AzureRmWebAppBackup -Name $appName -ResourceGroupName $resourceGroupName -BackupId 10102

Vous pouvez également insérer un objet de l’application web dans n’importe lequel des applets de commande de gestion des sauvegardes pour faciliter la tâche.

        $app = Get-AzureRmWebApp -Name ContosoApp -ResourceGroupName Default-Web-WestUS
        $backupList = $app | Get-AzureRmWebAppBackupList
        $backup = $app | Get-AzureRmWebAppBackup -BackupId 10102

## <a name="schedule-automatic-backups"></a>Planifier des sauvegardes automatiques

Vous pouvez planifier des sauvegardes s’exécutent automatiquement à intervalles réguliers. Pour configurer un planning de sauvegarde, utilisez l’applet de commande Modifier AzureRmWebAppBackupConfiguration. Cette applet de commande prend plusieurs paramètres :

- **Nom** : le nom de l’application web.
- **ResourceGroupName** - le nom du groupe de ressources contenant l’application web.
- **Emplacement** : facultatif. Le nom de l’emplacement de l’application web.
- **StorageAccountUrl** - URL pour le conteneur de stockage Azure permet de stocker les sauvegardes associations de sécurité.
- **FrequencyInterval** - valeur numérique pour la fréquence à laquelle les sauvegardes doivent être effectuées. Doit être un nombre entier positif.
- **FrequencyUnit** - unité de temps pour la fréquence à laquelle les sauvegardes doivent être effectuées. Les options sont heure et chaque jour.
- **RetentionPeriodInDays** - le nombre de jours les sauvegardes automatiques doivent être enregistrés avant d’être supprimées automatiquement.
- **Heure de début** : facultatif. Heure lorsque les sauvegardes automatiques doivent commencer. Sauvegardes commencent immédiatement s’il est null. Doit être une valeur DateTime.
- **Bases de données** - facultatif. Tableau de DatabaseBackupSettings pour les bases de données de sauvegarde.
- **KeepAtLeastOneBackup** - facultatif changé paramètre. Fournissez cette option si une sauvegarde doit toujours être conservée dans le compte de stockage, quelle que soit l’âge.

Voici un exemple de l’utilisation de cette applet de commande.

        $resourceGroupName = "Default-Web-WestUS"
        $appName = "ContosoApp"
        $slotName = "StagingSlot"
        $dbSetting1 = New-AzureRmWebAppDatabaseBackupSetting -Name DB1 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
        $dbSetting2 = New-AzureRmWebAppDatabaseBackupSetting -Name DB2 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
        Edit-AzureRmWebAppBackupConfiguration -Name $appName -ResourceGroupName $resourceGroupName -Slot $slotName `
          -StorageAccountUrl "<your SAS URL>" -FrequencyInterval 6 -FrequencyUnit Hour -Databases $dbSetting1,$dbSetting2 `
          -KeepAtLeastOneBackup -StartTime (Get-Date).AddHours(1)

Pour obtenir le planning de sauvegarde actuel, utilisez l’applet de commande Get-AzureRmWebAppBackupConfiguration. Cela peut être utile de la modification d’un calendrier qui a déjà été configuré.

        $configuration = Get-AzureRmWebAppBackupConfiguration -Name $appName -ResourceGroupName $resourceGroupName

        # Modify the configuration slightly
        $configuration.FrequencyInterval = 2
        $configuration.FrequencyUnit = "Day"

        # Apply the new configuration by piping it into the Edit-AzureRmWebAppBackupConfiguration cmdlet
        $configuration | Edit-AzureRmWebAppBackupConfiguration

## <a name="restore-a-web-app-from-a-backup"></a>Restaurer une application web à partir d’une sauvegarde

Pour restaurer une application web à partir d’une sauvegarde, utilisez l’applet de commande Restaurer AzureRmWebAppBackup. Pour utiliser cette applet de commande le plus simple consiste à barre verticale dans un objet sauvegarde récupérées à partir de l’applet de commande Get-AzureRmWebAppBackup ou l’applet de commande Get-AzureRmWebAppBackupList.

Une fois que vous avez un objet de sauvegarde, vous pouvez l’insérer dans l’applet de commande Restaurer AzureRmWebAppBackup. Spécifier le paramètre de commutateur remplacer pour indiquer que vous souhaitez remplacer le contenu de votre application web avec le contenu de la sauvegarde. Si la sauvegarde contient des bases de données, les bases de données sont restaurés également.

        $backup | Restore-AzureRmWebAppBackup -Overwrite

Voici un exemple de l’utilisation de la restauration-AzureRmWebAppBackup en spécifiant tous les paramètres.

        $resourceGroupName = "Default-Web-WestUS"
        $appName = "ContosoApp"
        $slotName = "StagingSlot"
        $blobName = "ContosoBackup.zip"
        $dbSetting1 = New-AzureRmWebAppDatabaseBackupSetting -Name DB1 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
        $dbSetting2 = New-AzureRmWebAppDatabaseBackupSetting -Name DB2 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
        Restore-AzureRmWebAppBackup -ResourceGroupName $resourceGroupName -Name $appName -Slot $slotName -StorageAccountUrl "<your SAS URL>" -BlobName $blobName -Databases $dbSetting1,$dbSetting2 -Overwrite

## <a name="delete-a-backup"></a>Supprimer une sauvegarde

Pour supprimer une sauvegarde, utilisez l’applet de commande Supprimer AzureRmWebAppBackup. Cela supprime la sauvegarde de votre compte de stockage. Spécifier l’ID de la sauvegarde que vous voulez supprimer votre nom de l’application et son groupe de ressources.

        $resourceGroupName = "Default-Web-WestUS"
        $appName = "ContosoApp"
        Remove-AzureRmWebAppBackup -ResourceGroupName $resourceGroupName -Name $appName -BackupId 10102

Vous pouvez également insérer un objet de sauvegarde dans l’applet de commande Supprimer AzureRmWebAppBackup pour la supprimer.

        $backup = Get-AzureRmWebAppBackup -Name $appName -ResourceGroupName $resourceGroupName -BackupId 10102
        $backup | Remove-AzureRmWebAppBackup -Overwrite
