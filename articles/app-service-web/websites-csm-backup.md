<properties
    pageTitle="Utilisez reste pour sauvegarder et restaurer des applications de Service d’application"
    description="Apprenez à utiliser des appels API RESTful pour sauvegarder et restaurer une application dans le Service d’application Azure"
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
# <a name="use-rest-to-back-up-and-restore-app-service-apps"></a>Utilisez reste pour sauvegarder et restaurer des applications de Service d’application

> [AZURE.SELECTOR]
- [PowerShell](../app-service/app-service-powershell-backup.md)
- [API REST](websites-csm-backup.md)

[Applications de Service d’application](https://azure.microsoft.com/services/app-service/web/) peuvent être sauvegardés comme des objets BLOB Azure stockage. La sauvegarde peut également contenir des bases de données de l’application. Si l’application est jamais accidentellement supprimée, ou si l’application doit être rétablie à une version précédente, il peut être restauré à partir d’une sauvegarde précédente. Des sauvegardes peuvent être effectuées à tout moment à la demande, ou des sauvegardes peuvent être planifiées à intervalles appropriés.

Cet article explique comment faire pour sauvegarder et restaurer une application ayant des demandes d’API RESTful. Si vous voulez créer et gérer des sauvegardes application graphiquement via le portail d’Azure, consultez [sauvegarder une application web dans le Service d’application Azure](web-sites-backup.md)

<a name="gettingstarted"></a>
## <a name="getting-started"></a>Prise en main
Pour envoyer les demandes REST, vous devez connaître votre application **nom**, **groupe de ressources**et **l’id de l’abonnement**. Ces informations sont accessibles en cliquant sur votre application dans la carte de **Service d’application** du [portail Azure](https://portal.azure.com). Pour les exemples de cet article, nous allons configurer le site Web **backuprestoreapiexamples.azurewebsites.net**. Il est stocké dans le groupe de ressources par défaut-Web-WestUS et s’exécute sur un abonnement avec l’ID 00001111-2222-3333-4444-555566667777.

![Informations de l’exemple de site Web][SampleWebsiteInformation]

<a name="backup-restore-rest-api"></a>
## <a name="backup-and-restore-rest-api"></a>Sauvegarder et restaurer des API REST
Nous allons maintenant plusieurs exemples d’utilisation de l’API REST pour sauvegarder et restaurer une application. Chaque exemple inclut un corps de la requête URL et HTTP. L’URL de l’exemple contient des espaces réservés de sport avec des accolades, par exemple {-id de l’abonnement}. Remplacer les espaces réservés par les informations correspondant à votre application. Pour référence, voici une explication de chaque espace réservé qui s’affiche dans les exemples d’URL.

* id d’abonnement – ID de l’abonnement Azure contenant l’application
* groupe-nom de la ressource – nom du groupe de ressources contenant l’application
* nom : nom de l’application
* id de sauvegarde – ID de la sauvegarde d’application

Pour la documentation complète de l’API, y compris plusieurs paramètres facultatifs pouvant être inclus dans la demande HTTP, voir l' [Explorateur de ressources Azure](https://resources.azure.com/).

<a name="backup-on-demand"></a>
## <a name="backup-an-app-on-demand"></a>Sauvegarder une application à la demande
Pour sauvegarder une application immédiatement, envoyez une demande de **publication** pour **https://management.azure.com/subscriptions/ {subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{name}/backup/**.

Voici à quoi ressemble l’URL à l’aide de notre exemple de site Web. **https://Management.Azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/Providers/Microsoft.Web/sites/backuprestoreapiexamples/Backup/**

Fournissez un objet JSON dans le corps de votre requête pour spécifier le compte de stockage à utiliser pour stocker la sauvegarde. L’objet JSON doit avoir une propriété nommée **storageAccountUrl**, qui contient une [URL de sa](../storage/storage-dotnet-shared-access-signature-part-1.md) l’attribution de l’accès en écriture au conteneur de stockage Azure qui contient le blob de sauvegarde. Si vous souhaitez sauvegarder vos bases de données, vous devez également fournir une liste contenant les noms, les types et les chaînes de connexion à des bases de données à sauvegarder.

```
{
    "properties":
    {
        "storageAccountUrl": “https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl”,
        “databases”: [
            {
                “databaseType”: “SqlAzure”,
                “name”: “MyDatabase1”,
                "connectionString": "<your database connection string>"
            }
        ]
    }
}
```

Une sauvegarde de l’application commence immédiatement lors de la réception de la demande. Le processus de sauvegarde peut prendre beaucoup de temps. La réponse HTTP contient un ID que vous pouvez utiliser dans une autre requête pour afficher l’état de la sauvegarde. Voici un exemple du corps de la réponse HTTP à notre demande de sauvegarde.

```
{
    "id": "/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples",
    "name": " backuprestoreapiexamples ",
    "type": "Microsoft.Web/sites",
    "location": "WestUS",
    "properties":    {
        "id": 1,
        "storageAccountUrl": “https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl”,
        "blobName": “backup_201509291825.zip”,
        "name": "backup_201509291825",
        "status": 4,
        "sizeInBytes": 0,
        "created": "2015-09-29T18:25:26.3992707Z",
        "log": null,
        "databases": [
            {
                "databaseType": "SqlAzure",
                "name": "MyDatabase1",
                "connectionString": "<your database connection string>"
            }
        ],
        "scheduled": false,
        "correlationId": "ea730f4d-dd06-4f7f-a090-9aa09k662h36",
    }
}
```

>[AZURE.NOTE] Vous trouverez des messages d’erreur dans la propriété journal de la réponse HTTP.

<a name="schedule-automatic-backups"></a>
## <a name="schedule-automatic-backups"></a>Planifier des sauvegardes automatiques
En plus de sauvegarder une application à la demande, vous pouvez également planifier une sauvegarde se produise automatiquement.

### <a name="set-up-a-new-automatic-backup-schedule"></a>Configurer un planning de sauvegarde automatique
Pour configurer un planning de sauvegarde, envoyez une demande de **placer** pour **https://management.azure.com/subscriptions/ {subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{name}/config/backup**.

Voici à quoi ressemble l’URL pour notre exemple de site Web. **https://Management.Azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/Providers/Microsoft.Web/sites/backuprestoreapiexamples/config/Backup**

Le corps de la requête doit avoir un objet JSON qui spécifie la configuration de sauvegarde. Voici un exemple avec tous les paramètres requis.

```
{
    "location": "WestUS",
    "properties": // Represents an app restore request
    {
        "backupSchedule": { // Required for automatically scheduled backups
            "frequencyInterval": "7",
            "frequencyUnit": "Day",
            "keepAtLeastOneBackup": "True",
            "retentionPeriodInDays": "10",
        },
        "enabled": "True", // Must be set to true to enable automatic backups
        "name": “mysitebackup”,
        "storageAccountUrl": "https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl"
    }
}
```

Cet exemple configure l’application à sauvegarder automatiquement tous les sept jours. Les paramètres **frequencyInterval** et **frequencyUnit** gagnante déterminent la fréquence à laquelle les sauvegardes se produisent. Valeurs valides pour **frequencyUnit** sont **heure** et chaque **jour**. Par exemple, pour une application sauvegarder toutes les 12 heures, affectez la valeur à 12 et frequencyUnit Hour.

Anciennes sauvegardes sont automatiquement supprimés du compte de stockage. Vous pouvez contrôler l’ancienneté les sauvegardes peuvent être en attribuant au paramètre **retentionPeriodInDays** . Si vous voulez toujours disposer d’au moins une sauvegarde enregistrée, quelle que soit l’ancienneté il, **keepAtLeastOneBackup** sur true.

### <a name="get-the-automatic-backup-schedule"></a>Obtenir le planning de sauvegarde automatique
Pour obtenir la configuration de la sauvegarde d’une application, envoyer une demande de **publication** à l’URL **https://management.azure.com/subscriptions/ {subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{name}/config/backup/list**.

L’URL de notre exemple de site est **https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/config/backup/list**.

<a name="get-backup-status"></a>
## <a name="get-the-status-of-a-backup"></a>Obtenir l’état d’une sauvegarde
Selon la taille est de l’application, une sauvegarde peut prendre un certain temps. Des sauvegardes peuvent également échouer, le délai d’expiration, ou réussir partiellement. Pour afficher l’état des sauvegardes de tous les une application, envoyer une requête **GET** à l’URL **https://management.azure.com/subscriptions/ {subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{name}/backups**.

Pour afficher l’état d’une sauvegarde spécifique, envoyer une demande GET à l' URL **https://management.azure.com/subscriptions/ {subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{name}/backups/{backup-id}**.

Voici à quoi ressemble l’URL pour notre exemple de site Web. **https://Management.Azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/Providers/Microsoft.Web/sites/backuprestoreapiexamples/Backups/1**

Le corps de réponse contient un objet JSON similaire à cet exemple.

```
{
    "properties":  {
        "id": 1,
        "storageAccountUrl": "https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl",
        "blobName": "backup_201509291734.zip",
        "name": "backup_201509291734",
        "status": 2,
        "sizeInBytes": 151973,
        "created": "2015-09-29T17:34:57.2091605",
        "scheduled": false,
        "lastRestoreTimeStamp": null,
        "finishedTimeStamp": "2015-09-29T17:35:11.3293602",
        "correlationId": "2379fc13-418a-4b9c-920d-d06e73c5028d",
        "websiteSizeInBytes": 209920
    }
}
```

L’état d’une sauvegarde est un type énuméré. Voici chaque état possible.

* 0 – InProgress : la sauvegarde a été démarrée mais n’est pas encore terminé.
* 1 – a échoué : La sauvegarde a échoué.
* 2 – a réussi : La sauvegarde s’est terminée correctement.
* 3-avec délai dépassé : La sauvegarde s’est pas terminée dans le temps et a été annulée.
* 4-créé : La requête de sauvegarde est mise en attente, mais n’a pas été démarrée.
* 5 – ignorées : La sauvegarde n’est pas allé en raison d’un planning de déclenchement des sauvegardes trop.
* 6 – PartiallySucceeded : La sauvegarde réussie, mais certains fichiers n’étaient pas sauvegardées, car ils ne peuvent pas être lus. Généralement, cela se produit, car un accès exclusif a été placé sur les fichiers.
* 7-DeleteInProgress : La sauvegarde a demandé à être supprimé, mais n’a ne pas encore été supprimé.
* 8 – Échec de la suppression : La sauvegarde n’a pas pu être supprimée. Cela peut se produire parce que l’URL de sa ayant servi à créer la sauvegarde a expiré.
* 9 – supprimé : La sauvegarde a été supprimée.

<a name="restore-app"></a>
## <a name="restore-an-app-from-a-backup"></a>Restaurer une application à partir d’une sauvegarde
Si votre application a été supprimée ou si vous voulez rétablir votre application à une version précédente, vous pouvez restaurer l’application à partir d’une sauvegarde. Pour appeler une restauration, envoyer une demande de **publication** à l’URL **https://management.azure.com/subscriptions/ {subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{name}/backups/{backup-id}/restore**.

Voici à quoi ressemble l’URL pour notre exemple de site Web. **https://Management.Azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/Providers/Microsoft.Web/sites/backuprestoreapiexamples/Backups/1/Restore**

Dans le corps de la requête, envoyer un objet JSON qui contient les propriétés de l’opération de restauration. Voici un exemple contenant toutes les propriétés requises :

```
{
    "location": "WestUS",
    "properties":
    {
        "blobName": "backup_201509280431.zip",
        "databases": [ // Not required unless you’re restoring databases
            {
                “databaseType”: “SqlAzure”,
                “name”: “MyDatabase1”
        }],
        "overwrite": "true",
        "storageAccountUrl": "https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl" // SAS URL to storage container containing your website backup
    }
}
```

### <a name="restore-to-a-new-app"></a>Restaurer vers une nouvelle application
Parfois, vous souhaiterez peut-être créer une nouvelle application lorsque vous restaurez une sauvegarde, au lieu de remplacer une application existante. Pour ce faire, modifiez l’URL de demande pour pointer vers la nouvelle application que vous voulez créer et modifier la propriété **Remplacer** dans le JSON sur **false**.

<a name="delete-app-backup"></a>
## <a name="delete-an-app-backup"></a>Supprimer une sauvegarde d’application
Si vous souhaitez supprimer une sauvegarde, envoyer une demande de **Supprimer** à l' URL **https://management.azure.com/subscriptions/ {subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{name}/backups/{backup-id}**.

Voici à quoi ressemble l’URL pour notre exemple de site Web. **https://Management.Azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/Providers/Microsoft.Web/sites/backuprestoreapiexamples/Backups/1**

<a name="manage-sas-url"></a>
## <a name="manage-a-backups-sas-url"></a>Gérer les URL des associations de sécurité d’une sauvegarde
Azure Application Service tentera de supprimer votre sauvegarde du stockage Azure à l’aide de l’URL de sa qui était fourni lorsque la sauvegarde a été créée. Si cette URL sa n’est plus valide, la sauvegarde ne peut pas être supprimée via l’API REST. Toutefois, vous pouvez mettre à jour l’URL de sa associée à une sauvegarde en envoyant une demande **POST** à l’URL **https://management.azure.com/subscriptions/ {subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{name}/backups/{backup-id}/list**.

Voici à quoi ressemble l’URL pour notre exemple de site Web. **https://Management.Azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/Providers/Microsoft.Web/sites/backuprestoreapiexamples/Backups/1/List**

Dans le corps de la requête, envoyer un objet JSON qui contient la nouvelle URL associations de sécurité. Voici un exemple.

```
{
    "properties":
    {
        "storageAccountUrl": "https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl"
    }
}
```

>[AZURE.NOTE] Pour des raisons de sécurité, l’URL de sa associée à une sauvegarde n’est pas renvoyé lors de l’envoi d’une requête GET pour une sauvegarde spécifique. Si vous souhaitez afficher l’URL de sa associée à une sauvegarde, envoyez une demande de publication à la même URL ci-dessus. Inclure un objet JSON vide dans le corps de la requête. La réponse à partir du serveur contient toutes les informations de sauvegarde, y compris son URL associations de sécurité.

<!-- IMAGES -->
[SampleWebsiteInformation]: ./media/websites-csm-backup/01siteconfig.png
