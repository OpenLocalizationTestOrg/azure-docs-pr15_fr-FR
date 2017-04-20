<properties
    pageTitle="Créer une capture instantanée en lecture seule d’un objet blob | Microsoft Azure"
    description="Découvrez comment créer un instantané d’un objet blob pour sauvegarder des données blob à un moment donné dans le temps. Comprendre comment instantanés sont facturés et comment les utiliser pour réduire les frais de capacité."
    services="storage"
    documentationCenter=""
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>

# <a name="create-a-blob-snapshot"></a>Créer une capture instantanée blob

## <a name="overview"></a>Vue d’ensemble

Un instantané est une version en lecture seule d’un blob qui est considérée à un point dans le temps. Instantanés sont utiles pour la sauvegarde des objets BLOB. Après avoir créé un instantané, vous pouvez lire, copier ou supprimer, mais vous ne pouvez pas le modifier.

Un instantané d’un objet blob est identique à son blob de base, sauf que le blob URI comporte une valeur de **date/heure** ajoutée au blob URI pour indiquer l’heure à laquelle l’instantané. Par exemple, si une page blob URI est `http://storagesample.core.blob.windows.net/mydrives/myvhd`, l’instantané URI ressemble à `http://storagesample.core.blob.windows.net/mydrives/myvhd?snapshot=2011-03-09T01:42:34.9360000Z`. 

> [AZURE.NOTE] Tous les instantanés partagent URI du blob base. La seule distinction entre le blob de base et l’instantané est la valeur de **date/heure** ajoutée.

Un blob peut avoir n’importe quel nombre d’instantanés. Instantanés sont conservés jusqu'à ce qu’ils sont supprimés de manière explicite. Un instantané ne peut pas survivent son blob de base. Vous pouvez énumérer les instantanés associées à la base blob pour effectuer le suivi de vos instantanés en cours.

Lorsque vous créez un instantané d’un objet blob, propriétés du système de blob sont copiées dans l’instantané avec les mêmes valeurs. Métadonnées de blob base sont également copiée à l’instantané, sauf si vous spécifiez métadonnées distinctes pour l’instantané lors de sa création.

N’importe quel location associée à la base blob ne concernent pas l’instantané. Vous ne pouvez pas acquérir une location sur une capture instantanée.

## <a name="create-a-snapshot"></a>Créer une capture instantanée

L’exemple suivant montre comment créer un instantané dans .NET. Cet exemple spécifie les métadonnées distinctes pour l’instantané lors de sa création.

    private static async Task CreateBlockBlobSnapshot(CloudBlobContainer container)
    {
        // Create a new block blob in the container.
        CloudBlockBlob baseBlob = container.GetBlockBlobReference("sample-base-blob.txt");

        // Add blob metadata.
        baseBlob.Metadata.Add("ApproxBlobCreatedDate", DateTime.UtcNow.ToString());

        try
        {
            // Upload the blob to create it, with its metadata.
            await baseBlob.UploadTextAsync(string.Format("Base blob: {0}", baseBlob.Uri.ToString()));

            // Sleep 5 seconds.
            System.Threading.Thread.Sleep(5000);

            // Create a snapshot of the base blob.
            // Specify metadata at the time that the snapshot is created to specify unique metadata for the snapshot.
            // If no metadata is specified when the snapshot is created, the base blob's metadata is copied to the snapshot.
            Dictionary<string, string> metadata = new Dictionary<string, string>();
            metadata.Add("ApproxSnapshotCreatedDate", DateTime.UtcNow.ToString());
            await baseBlob.CreateSnapshotAsync(metadata, null, null, null);
        }
        catch (StorageException e)
        {
            Console.WriteLine(e.Message);
            Console.ReadLine();
            throw;
        }
    }
 

## <a name="copy-snapshots"></a>Copier des instantanés

Opérations de copie impliquant des objets BLOB et des instantanés suivent ces règles :

- Vous pouvez copier un instantané via son blob de base. Par la promotion d’un instantané vers la position de l’objet blob de base, vous pouvez restaurer une version antérieure d’un objet blob. Le reste instantané, mais la base de blob est remplacée par une copie de l’instantané.

- Vous pouvez copier un instantané dans un blob destination sous un autre nom. Le blob de destination qui en résulte est un blob accessible en écriture et pas un instantané.

- Lorsqu’un blob source est copié, n’importe quel instantanés du blob source ne sont pas copiés vers la destination. Lorsqu’un blob de destination est remplacé par une copie, n’importe quel clichés associés à la blob de destination d’origine restent intactes.

- Lorsque vous créez un instantané d’un objet blob bloc, liste de blocs validée de blob est également copiée à l’instantané. Tous les blocs non validées ne sont pas copiés.

## <a name="specify-an-access-condition"></a>Spécifier une condition d’accès

Vous pouvez spécifier une condition d’accès afin que l’instantané est créé uniquement si une condition est remplie. Pour spécifier une condition d’accès, utilisez la propriété **AccessCondition** . Si la condition spécifiée n’est pas remplie, l’instantané n’a pas été créée et le service Blob renvoie le code d’état HTTPStatusCode.PreconditionFailed.

## <a name="delete-snapshots"></a>Supprimer des instantanés

Vous ne pouvez pas supprimer un blob avec des instantanés, à moins que les instantanés sont également supprimés. Vous pouvez supprimer une capture instantanée individuellement, ou spécifier que tous les instantanés supprimée lorsque le blob source est supprimé. Si vous tentez de supprimer un blob qui comporte toujours des instantanés, une erreur se produit.

L’exemple suivant montre comment supprimer un blob et ses instantanés dans .NET, où `blockBlob` est une variable de type **CloudBlockBlob**:

    await blockBlob.DeleteIfExistsAsync(DeleteSnapshotsOption.IncludeSnapshots, null, null, null);

## <a name="snapshots-with-azure-premium-storage"></a>Instantanés avec stockage Azure Premium

Utilisation des instantanés avec suivi de stockage Premium ces règles :

- Le nombre maximal d’instantanés par blob de page dans un compte de stockage premium est 100. Si cette limite est dépassée, l’opération d’objets Blob instantané renvoie le code d’erreur 409 (**SnapshotCountExceeded**).

- Vous pouvez prendre un instantané d’un objet blob de page dans un compte de stockage premium toutes les 10 minutes. Si ce taux est dépassé, l’opération d’objets Blob instantané renvoie le code d’erreur 409 (**SnaphotOperationRateExceeded**).

- Vous ne pouvez pas appeler obtenir Blob pour lire un instantané d’un objet blob de page dans un compte de stockage premium. Blob obtenir l’appel sur un instantané dans un compte de stockage premium renvoie le code d’erreur 400 (**InvalidOperation**). Toutefois, vous pouvez appeler obtenir les propriétés Blob et obtenir des métadonnées Blob par rapport à un instantané dans un compte de stockage premium.

- Pour lire un instantané, vous pouvez utiliser l’opération d’objets Blob copier pour copier un instantané vers un autre blob de page dans le compte. Le blob de destination pour l’opération de copie ne doit pas avoir de n’importe quel instantanés existants. Si le blob de destination comporte des instantanés, l’opération copier Blob renvoie le code d’erreur 409 (**SnapshotsPresent**).

## <a name="return-the-absolute-uri-to-a-snapshot"></a>Retourne l’URI absolu à un instantané

Cet exemple de code c# crée un instantané et écrit l’URI absolu à l’emplacement principal.

    //Create the blob service client object.
    const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    //Get a reference to a container.
    CloudBlobContainer container = blobClient.GetContainerReference("sample-container");
    container.CreateIfNotExists();

    //Get a reference to a blob.
    CloudBlockBlob blob = container.GetBlockBlobReference("sampleblob.txt");
    blob.UploadText("This is a blob.");

    //Create a snapshot of the blob and write out its primary URI.
    CloudBlockBlob blobSnapshot = blob.CreateSnapshot();
    Console.WriteLine(blobSnapshot.SnapshotQualifiedStorageUri.PrimaryUri);

## <a name="understand-how-snapshots-accrue-charges"></a>Comprendre la méthode instantanés d'allocation frais

Création d’un instantané, qui est une copie en lecture seule d’un objet blob, peut entraîner des frais de stockage des données supplémentaires à votre compte. Lorsque vous créez votre application, il est important de savoir comment ces frais peuvent accumuler afin que vous pouvez réduire les coûts inutiles.

### <a name="important-billing-considerations"></a>Considérations relatives à la facturation importantes

La liste suivante répertorie les principaux points à prendre en considération lors de la création d’un instantané.

- Compte de stockage vous entraîne des frais de blocs uniques ou des pages, qu’ils soient dans le blob ou dans l’instantané. Votre compte n’entraîne pas de frais supplémentaires pour clichés associés à un objet blob jusqu'à ce que vous mettez à jour le blob sur lesquelles ils sont basés. Une fois que vous mettez à jour le blob de base, il diverge de ses instantanés. Dans ce cas, vous êtes chargé pour les blocs uniques ou les pages dans chaque blob ou un instantané.

- Lorsque vous remplacez un bloc au sein d’un blob bloc, qui bloquent est facturée par la suite comme un bloc unique. C’est vrai même si le bloc de porte le même ID de bloc et les mêmes données que dans l’instantané. Une fois que le bloc validé, il diffère communs dans n’importe quel instantané et vous seront facturés pour ses données. La même chose pour une page dans un blob de page est mis à jour avec des données identiques.

- Remplacer un blob bloc en appelant la méthode **UploadFile**, **UploadText**, **UploadStream**ou **UploadByteArray** remplace tous les blocs dans le blob. Si vous disposez d’un instantané associé à ce blob, tous les blocs dans le blob de base et l’instantané divergent maintenant, et vous devrez payer pour tous les blocs dans les deux objets BLOB. C’est vrai même si les données dans le blob de base et l’instantané restent identiques.

- Le service d’objets Blob Azure n’a pas un moyen de déterminer si deux blocs contiennent des données identiques. Chaque bloc est téléchargée et validée est considéré comme unique, même si elle a les mêmes données et l’ID de bloc même. Étant donné que le cumul des frais de blocs uniques, il est important à prendre en considération qu’à jour un blob qui comporte des résultats un instantané blocs uniques supplémentaires et les frais supplémentaires.

> [AZURE.NOTE] Meilleures pratiques indiquent que vous gérez des instantanés avec soin pour éviter des frais supplémentaires. Nous vous recommandons de gérer des instantanés de la manière suivante :

> - Supprimer et recréer clichés associés à un objet blob lorsque vous mettez à jour le blob, même si vous mettez à jour avec des données identiques, à moins que votre conception d’application nécessite que vous tenir à jour des instantanés. En supprimant et en créant des instantanés de blob de nouveau, vous pouvez vous assurer que les objets blob et des instantanés ne diffèrent pas.

> - Si vous mettez à jour des instantanés pour un blob, évitez d’appeler **UploadFile**, **UploadText**, **UploadStream**ou **UploadByteArray** pour mettre à jour le blob. Ces méthodes remplacent toutes les blocs dans le blob, afin que vos objets blob de base et des instantanés divergent déclarer. Mettre à jour à la place, le plus petit nombre possible de blocs en utilisant les méthodes **PutBlock** et **PutBlockList** .


### <a name="snapshot-billing-scenarios"></a>Instantané facturation scénarios


Les scénarios suivants montrent la méthode d’allocation pour un blob bloc et ses instantanés frais.

Dans le scénario 1, le blob base n'a pas été mis à jour une fois l’instantané, afin de frais sont encourus uniquement pour les blocs uniques 1, 2 et 3.

![Ressources de stockage Azure](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-1.png)

Dans le scénario 2, le blob base a été mis à jour, mais l’instantané n’a pas. Bloc 3 a été mis à jour, et même si elle contient les mêmes données et l’ID de même, il n’est pas le même que celui bloquer 3 dans l’instantané. Par conséquent, le compte est facturé pour les blocs de quatre.

![Ressources de stockage Azure](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-2.png)

Dans le scénario 3, le blob base a été mis à jour, mais l’instantané n’a pas. Bloc 3 a été remplacé par bloc 4 dans le blob de base, mais l’instantané reflète toujours un bloc 3. Par conséquent, le compte est facturé pour les blocs de quatre.

![Ressources de stockage Azure](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-3.png)

Dans le scénario 4, la base blob a été mis à jour complètement et ne contient aucun de ses blocs d’origine. Par conséquent, le compte est facturé pour tous les huit blocs uniques. Ce scénario peut se produire si vous utilisez une méthode de mise à jour comme **UploadFile**, **UploadText**, **UploadFromStream**ou **UploadByteArray**, car ces méthodes remplacent tout le contenu d’un objet blob.

![Ressources de stockage Azure](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-4.png)

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir des exemples supplémentaires à l’aide de stockage d’objets Blob, voir [Les exemples de Code Azure](https://azure.microsoft.com/documentation/samples/?service=storage&term=blob). Vous pouvez télécharger un exemple d’application et exécutez-le ou recherchez le code de GitHub. 
